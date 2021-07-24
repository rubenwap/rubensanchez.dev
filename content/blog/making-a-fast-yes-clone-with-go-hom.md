---
stackbit_url_path: posts/making-a-fast-yes-clone-with-go-hom
title: Making a fast "yes" clone with Go
date: '2020-10-18T12:59:33.593Z'
excerpt: >-
  Following the series on creating Go based clones of the GNU coreutils, today I
  wanted to do a speed e...
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--f_dmyf0W--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/i/8fesk0hidxy21admkr7r.png
comments_count: 3
positive_reactions_count: 5
tags:
  - go
  - performance
  - gnu
  - tutorial
canonical_url: 'https://dev.to/rubenwap/making-a-fast-yes-clone-with-go-hom'
template: post
---
Following the series on creating Go based clones of the GNU coreutils, today I wanted to do a speed experiment using 
`yes`


## What is YES?

If you are using mac or linux, go to your terminal and type 
`yes`
. You should get an infinite stream of 
`y`
. Or you can also run it as 
`yes yes`
 and you will get an infinite stream of 
`yes`
. Or 
`yes no`
. 

Graphical representation of this tool:

![a2pXn6d](https://dev-to-uploads.s3.amazonaws.com/i/x8d76wj4mvf793tey72f.gif)
 
## What is the point of this? 

Imagine that you have a long installation script that you want to run unattended. If the script has a flag to force positive answers for every question, then no issue. But what if the script really forces you to wait for the questions and answer 
`y`
 or 
`n`
 every time? What if you don't care because you know you will answer 
`y`
 to every question? Then you can do:

    $ yes | myInstallScript.sh

So a constant stream of 
`y`
 will be piped into your install script, so whenever you are asked about something, the pipe will answer for you. 

Another use is if you need to produce dummy data, you can pipe 
`yes`
 to a file for as long as you need. 

## So... while true, print yes?

Kind of, but not entirely, because we will focus on performance. If we didn't care, we could do:


```go
expletive := os.Args[1]
if expletive == "" {
    expletive = "y"
}
for {
    fmt.Println(expletive)
}
```


And that would be the end of the story. 

However, let's measure how fast this implementation is. Please go ahead and install 
`pv`
 if you are using mac, via 
`brew install pv`
. On linux you probably already have it. On Windows you probably need to use Cygwin. Pv is a tool to measure the data through your pipe. 

## Implementation 1: Infinite loop print

    go build yes.go
    ./yes | pv > /dev/null

This prints an average of [1.31MiB/s]

Considering that the real 
`yes`
 installed on a mac (the BSD implementation) does around [23.4MiB/s], the conclusion is that our first implementation is not very good. 

## Implementation 2: Why print just one when you can print 100 million

So far we have been doing


```go
for {
    fmt.Println(expletive)
}
```

which doesn't seem very efficient because it's just printing the loop one by one. Let's spice it up by printing huge blocks of the repeated string:


```go
fmt.Println(strings.Repeat(expletive+"\n", 100))
```


That gives us [89.8MiB/s]. Much better! We have already passed the benchmark of the original 
`yes`
 installed on macs. 

Why stop at 100?


```go
fmt.Println(strings.Repeat(expletive+"\n", 100000000))
```


That gives [1.37GiB/s] We seem to be onto something here. Beyond 100 million repeats, at least on my computer two things can happen: Either there is no difference, or it simply doesn't work and writes don't begin.

## Implementation 3: Goroutines assemble! 

One would think that goroutines can help here. If 
`yes`
 is being thrown at high speed by multiple goroutines, won't that give high throughput? I don't know! We will discover! 

The problem is that if we do simply like this:

```go
yesChan = make(chan string)
go func() {
    yesChan <- strings.Repeat(expletive+"\n", 100000000)
}()
go func() {
    fmt.Println(<-yesChan)
}()
```

We will run out into an interesting problem (after a few seconds running):

    panic: too many concurrent operations on a single file or socket

Which is a self-explanatory issue. The solution for that is to use a semaphore. However, I am worried that the code is getting way too complicated for the simple goal that we want to achieve:


```go
maxWorkers = runtime.GOMAXPROCS(0)
sem = semaphore.NewWeighted(int64(maxWorkers))

ctx := context.TODO()

for {
    if err := sem.Acquire(ctx, 4); err != nil {
    log.Printf("Failed to acquire semaphore: %v", err)
    break
    }

    go func() {
        defer sem.Release(2)
        yesChan <- bytes.Repeat([]byte(expletive), 100000000)
    }()

    go func() {
    defer sem.Release(2)
    os.Stdout.Write(<-yesChan)
    }()
}
```


So, this must be the bomb, right? Let's see how super fast it goes....

(...suspense...)

[1.42GiB/s]

WHY? Why is it practically the same as in the previous serial operation with 100 million writes? 

Well, the answer is simple: The improvement is barely noticeable, because even if coroutines are parallel operations, I/O operations are not. You cannot have two writes to the same output at the very same time. This must be a serial operation, that's why the overcomplicated goroutines code is not helping here. 

## Implementation 4: Bytes are our last hope

Notice that so far we have been using strings. Let's change it to bytes


```go
yesChan = make(chan []byte)
go func() {
    defer sem.Release(2)
    yesChan <- bytes.Repeat([]byte(expletive), 100000000)
}()
go func() {
    defer sem.Release(2)
    os.Stdout.Write(<-yesChan)
}()
```

Do we have any improvement? Yes! (but not a lot) [1.63GiB/s] So, working with bytes instead of strings really helped. 

## Implementation 5: Back to the basics

We have agreed before that we don't need the goroutines/semaphore mess, so we can bring this script back to basics:


```go
for {
    os.Stdout.Write(bytes.Repeat([]byte(expletive), 100000000))
}
```


That's all we need to achieve 1.63GiB/s! 

Also, consider that your mileage might vary, because the kind of computer you use to run this also plays a role. I have tried to run other people's code reported to give about 4GiB/s and I have been getting barely the half of it, so think about that if you want to run this on your machine.

## Implementation 6: Will a bytes buffer help? 

Short answer: At least with my medium knowledge of it, it doesn't seem to. 


```go
for {
    b.Write(bytes.Repeat([]byte(expletive), 100000000))
    b.WriteTo(os.Stdout)
}
```


That gives [1.28GiB/s]

## Implementation 7: What about a proper bufio buffer? 

Yes! This is the fastest I can reach with the processor and memory that I use:


```go
for {
    f := bufio.NewWriterSize(os.Stdout, 8192)
    f.Write(bytes.Repeat([]byte(expletive+"\n"), 100000000))
}
```


This gives [1.73GiB/s] I have been unable to achieve more so far, if you have a hint to make this better, let me know. 

## Full version of implementation 7 (the fastest)

This is the full code. Most of it is boilerplate related to the command line flags, the meaty part is very small. 


```go
package main

import (
	"bytes"
	"github.com/urfave/cli"
	"os"
	"bufio"
)

func yes() *cli.App {
	app := cli.NewApp()
	app.Name = "yes"
	app.Usage = "yes outputs expletive, or, by default, ``
y'', forever"
	app.Action = func(c *cli.Context) error {
    expletive := c.Args().Get(0)
		if expletive == "" {
			expletive = "y"
		}

		for {
			f := bufio.NewWriterSize(os.Stdout, 8192)
			f.Write(bytes.Repeat([]byte(expletive+"\n"), 100000000))
		}
	}
	return app
}

func main() {
	app := yes()
	app.Run(os.Args)
}


```


## Further reading

* BSD Yes source: https://github.com/openbsd/src/blob/master/usr.bin/yes/yes.c
* GNU Yes source: https://github.com/coreutils/coreutils/blob/master/src/yes.c
* Reddit - How is GNU yes so fast: https://www.reddit.com/r/unix/comments/6gxduc/how_is_gnu_yes_so_fast/


*[This post is also available on DEV.](https://dev.to/rubenwap/making-a-fast-yes-clone-with-go-hom)*


<script>
const parent = document.getElementsByTagName('head')[0];
const script = document.createElement('script');
script.type = 'text/javascript';
script.src = 'https://cdnjs.cloudflare.com/ajax/libs/iframe-resizer/4.1.1/iframeResizer.min.js';
script.charset = 'utf-8';
script.onload = function() {
    window.iFrameResize({}, '.liquidTag');
};
parent.appendChild(script);
</script>    
