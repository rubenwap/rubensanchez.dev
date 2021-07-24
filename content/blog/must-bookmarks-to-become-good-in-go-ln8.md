---
stackbit_url_path: posts/must-bookmarks-to-become-good-in-go-ln8
title: Must-Bookmarks to become good in Go
date: 2021-04-01T09:14:10.641Z
excerpt: >-
  I am having some days off, so I have compiled a list of Golang resources to go
  through during this ti...
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--s2E6CEUL--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/43t60x5ut3jayos9fn8u.png
comments_count: 0
positive_reactions_count: 52
tags:
  - go
  - learning
canonical_url: 'https://dev.to/rubenwap/must-bookmarks-to-become-good-in-go-ln8'
template: post
---
I am having some days off, so I have compiled a list of Golang resources to go through during this time, aimed to become a better Go developer. I am hoping that this can also be helpful to you. By the time we all reach the end of the list, our level of language familiarity should have increased a lot! 

![study](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/56rpi2x2a7i7kqwyaq5b.gif)
 

## Fundamentals

* **How to write Go code**

Quick initial refresher in case you need it! 
https://golang.org/doc/code


* **Effective Go**

I think it's a very good idea to revisit the "Effective Go" document once in a while with different skill level. In my case, the understanding I get from it and the profitability is nothing compared to the understanding I got a while ago when I read it for the first time. The more your mind has been exposed to different Go codebases, the more insight you can find when reading this. 
https://golang.org/doc/effective_go.html


* **Code Review comments**

I discovered this document recently and as the description says, it's a nice complement for the Effective Go write up. It's full of interesting comments that you should take into account, useful when doing code reviews (or writing your own code!)
https://github.com/golang/go/wiki/CodeReviewComments


* **Standard library**

Not being an extremely large standard library, getting to know it quite well is quite advisable. Having a pretty good knowledge will save you from the error of reaching for a third party library to accomplish something that can be easily done remaining within the stdlib. 
https://golang.org/pkg/# stdlib

Also, remember that the language specification is here to help 
https://golang.org/ref/spec



## Tricky parts


* **Pointers**

Getting more into action, I got to admit pointers were hard to grasp at first. If you want an explanation which is comprehensive and very easy to follow, check Junmin Lee's fantastic video
https://www.youtube.com/watch?v=sTFJtxJXkaY


* **Benchmarking and profiling**

I am almost sure that if you are doing Go, one of the main reasons is because you want peformant code. So well, things don't always go as expected, and when issues arise, knowing how to benchmark and profile will give you a big advantage. This comprehensive workshop contains a lot of interesting information about this matter. 
https://dave.cheney.net/high-performance-go-workshop/gophercon-2019.html


## Tips, tricks and common gotchas

Even though Go has a pretty short language specification that doesn't mean it's an easy language. I think Go is in fact difficult... or at least difficult to get right. It's really easy to get "whatever" up and running and I think that explains its popularity, but if your goal is not just to have "whatever" but idiomatic code which is precise, clean and simple to understand then you better be ready to face a pretty nice collection of quirks that you should know in order to write the best possible code. Here are two good articles on this topic.


* **The darker corners of go**
https://rytisbiel.com/2021/03/06/darker-corners-of-go/


* **50 shades of go**
http://devs.cloudimmunity.com/gotchas-and-common-mistakes-in-go-golang/


## Project based learning

Sometimes you want to get a good understanding of many crucial parts (mocks, interfaces, etc...) without doing toy exercises or easy tutorials. In this case the most enjoyable option is to do a long project tutorial like the following:


* **Http servers with ScyllaDB and Kafka**
https://www.youtube.com/watch?v=IS9Erqkx-ks&list=PLGRs9mrW62D1I4SF1p03zNn0x2WSSFhOA

You can also find a collection of guided long-ish tutorials here:


* **Gophexercises**
https://gophercises.com/


## Golang University

Almost ending the list, here is a triad of playlists put together by John Arundel from [Bitfield Consulting](https://bitfieldconsulting.com/) which can serve as a good general resource, available in three different skill levels.

[BASIC](https://www.youtube.com/watch?v=rFejpH_tAHM&list=PLEcwzBXTPUE9V1o8mZdC9tNnRZaTgI-1P)
[MEDIUM](https://www.youtube.com/watch?v=yeetIgNeIkc&list=PLEcwzBXTPUE_5m_JaMXmGEFgduH8EsuTs)
[ADVANCED](https://www.youtube.com/watch?v=YHRO5WQGh0k&list=PLEcwzBXTPUE8KvXRFmmfPEUmKoy9LfmAf)

## Books

Ending the list, it's time for books. I like books because the signal to noise ratio is so much better than many online tutorials. Unfortunately in some cases they might get outdated very easily, but if you pick books with the right content, many times the learnings you get from them should be timeless. For instance, the original "The Go Programming Language" book might not have the latest and greatest information about modules, but that is something that you can research on your own and doesn't make the book less valid as the best starting point for the language. 

Having said that, a couple of titles that have grabbed my attention


* **The interpreter/compiler book**

The Interpreter Book and its sequel, The Compiler Book are two resources to accomplish writing an interpreter (and a compiler) for a made up language. Really interesting proposition and definitely on my "to do" list. 
https://interpreterbook.com/
https://compilerbook.com/


* **Practical Go Lessons**

This excellent resource almost replaces entirely the rest of the list. A mamooth-sized book with everything you need to know about writing Go. The HTML version of the book is free, but you can support the author buying paper and digital copies. A must-bookmark! 
https://www.practical-go-lessons.com

![studying](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rd7s6wb2unhhe9rk7dcv.gif)
 





*[This post is also available on DEV.](https://dev.to/rubenwap/must-bookmarks-to-become-good-in-go-ln8)*


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
