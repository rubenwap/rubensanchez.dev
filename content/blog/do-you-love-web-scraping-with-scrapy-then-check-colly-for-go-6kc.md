---
stackbit_url_path: posts/do-you-love-web-scraping-with-scrapy-then-check-colly-for-go-6kc
title: Do you love web scraping with Scrapy? Then check Colly for Go
date: '2020-03-05T09:39:29.137Z'
excerpt: >-
  Scrapy is the star of the web scraping world in Python. There are indeed other
  beautiful libraries ou...
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--c6DgwgDQ--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/i/16lfjkroz8y7vgf2zs3c.png
comments_count: 1
positive_reactions_count: 24
tags:
  - scrapy
  - go
  - scraping
  - colly
canonical_url: >-
  https://dev.to/rubenwap/do-you-love-web-scraping-with-scrapy-then-check-colly-for-go-6kc
template: post
---
[Scrapy](https://scrapy.org/) is the star of the web scraping world in Python. There are indeed other beautiful libraries out there, but the simplicity and power that Scrapy gives you has captured the hearts of many Python developers who now have this full suite of excellent tools to perform the type of tasks that used to need more tinkering and patching. Scrapy alone will give you the actual scraping running on the [Twisted](https://twistedmatrix.com/trac/) engine, a shell to test your xpaths and css paths, an elegant way to specify your middleware, build a pipeline of actions easily and much more. 

Now, if you like Python and want to stick with it, then fine, use Scrapy. But are you by any chance using [Go](https://golang.org/) or considering to give it a try? Go seems such a popular language for previous Python developers to try out. 

![Gopher at work](https://dev-to-uploads.s3.amazonaws.com/i/9ey7jw45jwy5xkao65pm.jpg)


If that is the case, do check [Colly](http://go-colly.org/). Definitely. It captures the elegancy of Scrapy, it also captures its performance. And I am sure that given enough time and resources it will also end up capturing all its toolset. If you are just getting started with Go, it's also easy to learn and easy to use (as the language itself). We can take a look at an example so you can decide for yourself.

## Scrape the list of URLs for Company Registries worldwide

The UK government maintains a list of URLs for Company Registration sites (https://www.gov.uk/government/publications/overseas-registries/overseas-registries). These sites allow you to search for legal entities in each given country, so each individual URL we scrape can be explored further and additional scrapers for each country can be created, so perhaps you will build the next [OpenCorporates](https://opencorporates.com/)! But let's start from the beginning and get this initial list using Colly. 

## The Scrape

1) Initialize your project if you wish. The modules command arrived to Golang in the version 1.11, so if you are using an earlier version I guess it means you have been using Go for a while and know how to set up a new project. 

    go mod init github.com/rubenwap/colly-world-registers

2) Create the necessary files

    touch scrape.go

3) Install the latest Colly

    go get -u github.com/gocolly/colly/v2/...

4) Let's edit that scrape.go file

This is the basic scaffolding of our program. It initializes the Colly Collector, which is the object that will control all the other callback methods that we will use.



```golang
package main

import "github.com/gocolly/colly"

func main() {
  c := colly.NewCollector()
}
```


Check [this page](http://go-colly.org/docs/introduction/start/) for a quick start, and [this one](https://godoc.org/github.com/gocolly/colly# Collector.OnError) for a complete list of possible callbacks you can use. For this exercise, we are going to use a few of those:

Let's add a struct, in order to store each item that we retrieve:


```golang
type Registry struct {
	Country string
	URL     string
}
```


So your code becomes


```golang
package main

import "github.com/gocolly/colly"

type Registry struct {
	Country string
	URL     string
}

func main() {
  c := colly.NewCollector()
}
```


Now we could use the callback 
`Visit`
 to make the collector actually visit our target website


```golang
func main() {
  c := colly.NewCollector()
  c.Visit("https://www.gov.uk/government/publications/overseas-registries/overseas-registries")
}
```


However, apparently nothing would happen if you do 
`go run scrape.go`


If you want to really see something happening, you can tell Colly what to do 
`onRequest`
. Note that if you print, you will need to add 
`"fmt"`
 to your imports


```golang
func main() {
  c := colly.NewCollector()

	c.OnRequest(func(r *colly.Request) {
		fmt.Println("Visiting", r.URL)
	})
	c.Visit("https://www.gov.uk/government/publications/overseas-registries/overseas-registries")
}
```


Ok, so we know now that Colly is indeed visiting that URL. How do we get the HTML? 

If we visit the page in our browser and open the console on the developer tools, we notice that all we need to retrieve our data is grabbing the 
`.govuk-link`
 classes that are under the 
`.govspeak`
 class. So for instance, in the console you would issue this:

    document.querySelector(".govspeak").querySelectorAll(".govuk-link").forEach((link)=>console.log(link))

In our Go code, we will use the callback 
`onHTML`
, which will act on each of the elements that it finds following the pattern we specify. So for instance (remember that 
`Registry`
 was the struct we initialized a while ago):


```golang
c.OnHTML(".govspeak .govuk-link", func(e *colly.HTMLElement) {
		reg := Registry{}
		reg.Country = e.Text
		reg.URL = e.Attr("href")
	})
```


This means that on HTML reception, it shall grab the elements conforming the pattern 
`".govspeak .govuk-link"`
 and then for each of those elements, do as the function says. In our case we populate the struct. By the way, if the pattern of the selectors look familiar, it's because it's using [GoQuery](https://github.com/PuerkitoBio/goquery), which aims to replicate the classic jQuery selectors for Go. 

We can also intialize a slice of structs to save the full collection 
`registries := []Registry{}`
 and append each struct as we receive it. 

Finally we need to take into account that after the data has been scraped, we probably want to do something else with it. For this, we have the callback 
`onScraped`
, which will run after 
`onHTML`
 finishes. You could observe how it works by doing this. Of course, you could add your own steps. Here it's just printing the slice of structs:


```golang
	c.OnScraped(func(r *colly.Response) {
		fmt.Println("Finished. Here is your data:", registries)
	})
```


Prefer to output JSON? No problem, if you import 
`"encoding/json"`
, that module has you covered. 


```golang
	c.OnScraped(func(r *colly.Response) { 
		data, err := json.Marshal(registries)
		if err != nil {
			fmt.Println(err)
		} else {
			fmt.Println("Finished. Here is your data:", string(data))
		}
		
	})
```


The final code would look like this


```golang
package main

import (
	"fmt"
	"github.com/gocolly/colly"
	"encoding/json"
)

// Registry will store the Country registration URL items
type Registry struct {
	Country string
	URL     string
}

func main() {
	registries := []Registry{}
	c := colly.NewCollector()

	c.OnRequest(func(r *colly.Request) {
		fmt.Println("Visiting", r.URL)
	})

	c.OnHTML(".govspeak .govuk-link", func(e *colly.HTMLElement) {
		reg := Registry{}
		reg.Country = e.Text
		reg.URL = e.Attr("href")
		registries = append(registries, reg)
	})

	c.OnScraped(func(r *colly.Response) { 
		data, err := json.Marshal(registries)
		if err != nil {
			fmt.Println(err)
		} else {
			fmt.Println("Finished. Here is your data:", string(data))
		}
		
	})

	c.Visit("https://www.gov.uk/government/publications/overseas-registries/overseas-registries")
}

```


Give it a try! build it with 
`go build scrape.go`
 or run it with 
`go run scrape.go`
. Then go ahead and explore the docs further. They are pretty easy and you will surely get great ideas!

*[This post is also available on DEV.](https://dev.to/rubenwap/do-you-love-web-scraping-with-scrapy-then-check-colly-for-go-6kc)*


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
