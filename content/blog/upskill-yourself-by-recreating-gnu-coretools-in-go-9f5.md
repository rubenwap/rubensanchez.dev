---
stackbit_url_path: posts/upskill-yourself-by-recreating-gnu-coretools-in-go-9f5
title: Upskill yourself by recreating GNU Coreutils in Go
date: '2020-09-26T21:10:52.275Z'
excerpt: >-
  Reinventing the wheel for fun   One of the most popular advice to gain skills
  programming is...
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--4kTWkpex--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/i/qcaxuobyjq50ohn4h5fw.png
comments_count: 0
positive_reactions_count: 7
tags:
  - go
  - tutorial
canonical_url: 'https://dev.to/rubenwap/upskill-yourself-by-recreating-gnu-coretools-in-go-9f5'
template: post
---
## Reinventing the wheel for fun

One of the most popular advice to gain skills programming is to reimplement tools that already exist. For instance, if you wanted to learn how to do a CRUD API, I am pretty sure you probably practiced with a "To do" app. The point of this is not to create something new and groundbreaking, but to use well known ideas to improve what you know. Other examples you might have seen are things such as implementing a chat server to learn about websockets, a weather app, a Hacker News reader...

Today I am talking about something I consider more interesting: Reimplementing the GNU Coreutils utilities. I am not the first person doing this, but I am convinced that you are going to come out of this exercise with way better skills in your language of choice.

So what are the Coreutils? If you use Linux or Mac, you have probably used a bunch of them. Perhaps on Windows too in some cases. [Take a look at this link for more information](https://www.gnu.org/software/coreutils/coreutils.html). 

![GNU](https://dev-to-uploads.s3.amazonaws.com/i/h2nzpbs2zdpb1dxsqltr.png)
 
You can approach this project in different ways. In my case, I know that the point of coreutils is that they are really small and really simple, they do just one task but they do it well. In the original repo in C, most of the tools are just one single file. I am going to start with the same idea. 

* Understand the functionality reading the man page. Requirements.
* Implementing that functionality freely with no guidelines other than my own criteria. I do the project plan! 
* Comparing the output of my tool against the output of the real coreutil tool. This is the QA part.

As you see, this is more like a very free exercise rather than a serious structured practice. I am also very flexible in terms of whether or not to use extra libraries. For example, I realized that the 
`flag`
 package in Go is a little bit tedious to set up. Other flag packages are much nicer. Also, I need to find one that let concatenate arguments such as 
`-wl`
 instead of 
`-w -l`
, [to get as close as possible to the real Coreutils functionality](http://www.gnu.org/software/libc/manual/html_node/Argument-Syntax.html). 

## WC 

The first tool I picked was the [word counter WC](https://www.gnu.org/software/coreutils/manual/html_node/wc-invocation.html). Not the most difficult, not the easiest. For me, the requirements looked like this:

* Individual functions for every kind of count
* Flag parsing
* Main function that would get the input from Stdin and files passed as argument
* Depending on the passed flags, the counting functions would run, using the text from the previous step as argument
* Results are printed on screen 

So my rough schema:

![Untitled Diagram](https://dev-to-uploads.s3.amazonaws.com/i/vb6jsf72omi9i90156xm.png)
 
And here is the code. As mentioned before, the only external dependency is for my own personal taste, but could be easily avoided (specially when I recently [spoke about unnecessary dependencies here](https://dev.to/rubenwap/i-was-doing-bad-stuff-in-my-first-dev-job-sorry-part-ii-iak)!) But if we take this as an initial exercise, I think the result is not bad. 

I have commented the code so you can follow along


```go
package main

import (
	"bufio"
	"bytes"
	"fmt"
	"github.com/urfave/cli"
	"io/ioutil"
	"log"
	"os"
	"strings"
	"unicode/utf8"
)

/*
We define the four basic counting functions. 
The interesting thing with Go is that unlike 
languages like Python, len(string) will give you 
the byte count, not the character count. 
For characters, you need to count runes. 
*/

func byteCounts(text string) int {
	return len(text)
}

func lineCounts(text string) int {
	return len(strings.Split(text, "\n"))
}

func characterCounts(text string) int {
	return utf8.RuneCountInString(text)
}

func wordCounts(text string) int {
	return len(strings.Fields(text))
}

/* 
We initialize the CLI App with the definition 
of the flags. I think that the way this library 
handles flags is pretty clean and elegant. 
But feel free to use the `
flag
` package too. 
*/

func wc() *cli.App {
	app := cli.NewApp()
	app.Name = "WC"
	app.Usage = "The wc utility displays the number of lines, words, and bytes contained in each input file"
	app.Flags = []cli.Flag{
		cli.BoolFlag{
			Name:  "c",
			Usage: "The number of bytes in each input file is written to the standard output",
		},
		cli.BoolFlag{
			Name:  "l",
			Usage: "The number of lines in each input file is written to the standard output",
		},
		cli.BoolFlag{
			Name:  "m",
			Usage: "The number of characters in each input file is written to the standard output",
		},
		cli.BoolFlag{
			Name:  "w",
			Usage: "The number of words in each input file is written to the standard output",
		},
	}

// We define what actually happens when running

	app.Action = func(c *cli.Context) error {

		var buf bytes.Buffer
		var m map[string]int
		m = make(map[string]int)

/* 
We check for text passed to Stdin, just so we 
can invoke the app by doing `
echo "hello" | wc -m
` 
*/

		scanner := bufio.NewScanner(os.Stdin)
		for scanner.Scan() {
			buf.WriteString(scanner.Text())
		}

		if err := scanner.Err(); err != nil {
			log.Println(err)
		}

/* 
Maybe some filenames have been passed as argument. 
We have to retrieve the text from those and 
store it in a buffer 
*/

		for i := range c.Args() {
			fmt.Print(i)
			content, err := ioutil.ReadFile(c.Args().Get(i))
			if err != nil {
				log.Fatal(err)
			}
			buf.WriteString(string(content))
		}

// Now, depending on passed flags, we run the counting

		if c.Bool("c") {
			m["clen"] = byteCounts(buf.String())
		}

		if c.Bool("l") {
			m["llen"] = lineCounts(buf.String())
		}

		if c.Bool("m") {
			m["mlen"] = characterCounts(buf.String())
		}

		if c.Bool("w") {
			m["wlen"] = wordCounts(buf.String())
		}

		if c.Bool("c") && c.Bool("m") {
			m["mlen"] = 0
		}

// Printing the final values

		for _, value := range m {
			if value != 0 {
				fmt.Print("\t", value)
			}
		}
		fmt.Println("")

		return nil
	}
	return app
}

func main() {
	app := wc()
	app.Run(os.Args)
}

```


Is not perfect, and the functionality is not 100% on par with the real tool, but I learned a couple of things while doing this. That was the goal! 

Just for fun, here is the goroutines/channels enabled version. Pick your choice! 


```go
package main

import (
	"bufio"
	"bytes"
	"fmt"
	"io/ioutil"
	"log"
	"os"
	"strings"
	"sync"
	"unicode/utf8"

	"github.com/urfave/cli"
)

var wg sync.WaitGroup

func byteCounts(text string) int {
	return len(text)
}

func lineCounts(text string) int {
	return len(strings.Split(text, "\n"))
}

func characterCounts(text string) int {
	return utf8.RuneCountInString(text)
}

func wordCounts(text string) int {
	return len(strings.Fields(text))
}

func wc() *cli.App {
	app := cli.NewApp()
	app.Name = "WC"
	app.Usage = "The wc utility displays the number of lines, words, and bytes contained in each input file"
	app.Flags = []cli.Flag{
		cli.BoolFlag{
			Name:  "c",
			Usage: "The number of bytes in each input file is written to the standard output",
		},
		cli.BoolFlag{
			Name:  "l",
			Usage: "The number of lines in each input file is written to the standard output",
		},
		cli.BoolFlag{
			Name:  "m",
			Usage: "The number of characters in each input file is written to the standard output",
		},
		cli.BoolFlag{
			Name:  "w",
			Usage: "The number of words in each input file is written to the standard output",
		},
	}
	app.Action = func(c *cli.Context) error {

		var buf bytes.Buffer
		var m map[string]int
		m = make(map[string]int)

		scanner := bufio.NewScanner(os.Stdin)
		for scanner.Scan() {
			buf.WriteString(scanner.Text())
		}

		if err := scanner.Err(); err != nil {
			log.Println(err)
		}

		for i := range c.Args() {
			fmt.Print(i)
			content, err := ioutil.ReadFile(c.Args().Get(i))
			if err != nil {
				log.Fatal(err)
			}
			buf.WriteString(string(content))
		}

		bytesChan := make(chan int)
		linesChan := make(chan int)
		charactersChan := make(chan int)
		wordChan := make(chan int)

		wg.Add(4)
		go func(text string) {
			bytesChan <- byteCounts(text)
			wg.Done()
		}(buf.String())
		go func(text string) {
			linesChan <- lineCounts(text)
			wg.Done()
		}(buf.String())
		go func(text string) {
			charactersChan <- characterCounts(text)
			wg.Done()
		}(buf.String())
		go func(text string) {
			wordChan <- wordCounts(text)
			wg.Done()
		}(buf.String())

		for i := 0; i < 4; i++ {
			select {
			case msg1 := <-bytesChan:
				m["c"] = msg1
				m["m"] = 0
			case msg2 := <-linesChan:
				m["l"] = msg2
			case msg3 := <-charactersChan:
				m["m"] = msg3
				m["c"] = 0
			case msg4 := <-wordChan:
				m["w"] = msg4
			}
		}

		wg.Wait()
		for key, value := range m {
			if c.Bool(key) && value != 0 {
				fmt.Print("\t", key, ": ", value)
			}
		}
		fmt.Println("")

		return nil
	}
		
	return app
}

func main() {
	app := wc()
	app.Run(os.Args)
}

```


## Running the final version

We can build the executable:

    go build -o gwc wc/wc.go

Where 
`gwc`
 is my desired executable filename (to don't mix it up with the real 
`wc`
) and 
`wc/wc.go`
 is the path where I keep the script. On mac and linux you will get the 
`gwc`
 file you need to call with 
`./gwc`
 unless you add it to your path. On Windows you should get 
`gwc.exe`
.

And we can run it:

    echo "hello dev.to" | ./gwc -c 

outputs 
`12`


If you are on Windows, I am not sure how passing stdin text works in the command line, so you could try this alternate example:

Create a file with some text, and do:

    gwc.exe -c myfile.txt

So now, time to implement the other 97 remaining coreutils! 


*[This post is also available on DEV.](https://dev.to/rubenwap/upskill-yourself-by-recreating-gnu-coretools-in-go-9f5)*


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
