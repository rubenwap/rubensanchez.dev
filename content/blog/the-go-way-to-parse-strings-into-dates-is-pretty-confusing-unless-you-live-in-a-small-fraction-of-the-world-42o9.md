---
stackbit_url_path: >-
  posts/the-go-way-to-parse-strings-into-dates-is-pretty-confusing-unless-you-live-in-a-small-fraction-of-the-world-42o9
title: >-
  The Go way to parse strings into dates is pretty confusing unless you live in
  a small fraction of the world
date: '2020-05-18T16:22:29.633Z'
excerpt: >-
  When it comes to date formats, there is about 640 million people who prefer to
  order using MDY, and m...
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--2dro9QON--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/i/8ajwokz03ll8o9s9nrcx.png
comments_count: 2
positive_reactions_count: 7
tags:
  - go
canonical_url: >-
  https://dev.to/rubenwap/the-go-way-to-parse-strings-into-dates-is-pretty-confusing-unless-you-live-in-a-small-fraction-of-the-world-42o9
template: post
---
When it comes to date formats, there is about 640 million people who prefer to order using 
`MDY`
, and more than 5000 million that are doing other ordering systems: 

https://en.wikipedia.org/wiki/Date_format_by_country

This shouldn't need to be an issue, really. Let's take Python as an example and do a string to date operation both for our 
`MDY`
 friends and for the rest of the world too:


```python
    from datetime import datetime

    datetime_mdy = datetime.strptime('11/22/1980', '%m/%d/%Y')
    datetime_dmy = datetime.strptime('22/11/1980', '%d/%m/%Y')
    datetime_ymd = datetime.strptime('1980/11/22', '%Y/%m/%d')
```


No big issue. All operations output the exact same date. The formatting template is easy to check in https://strftime.org/ and multiple other resources. 

Enter Golang: 


```go
func main() {
	dateMdy, err := time.Parse("01/02/2006", "11/22/1980")
	if err != nil {
		panic(err)
	}
	dateDmy, err := time.Parse("02/01/2006", "22/11/1980")
	if err != nil {
		panic(err)
	}


	fmt.Println(dateMdy)
	fmt.Println(dateDmy)
}

```

As you see, the parsing introduces a "magic date" which you need to use to refer to the part of the date you need. That magic date is

    Mon Jan 2 15:04:05 -0700 MST 2006

You are supposed to remember this, because if you live in the minority of the world that does 
`MDY`
, then Jan=1, so we can say something like:

    01/02 03:04:05 PM '06 -0700

Do you think this is easier? I think it might be for people used to think in 
`MDY`
, but there is some confusing parts. For instance, 
`Monday`
 is not part of the magic number (
`1 2 3...`
) so it's hard to remember how to fit it in. Also, non 
`MDY`
 folks will trip often in the Day vs Month. 

Additionally I think this makes the code way less readable. I specially think here it's pretty bad:

```go
    time.Parse("01/02/2006", "11/22/1980")
```

Unless you know Go, you cannot know what is this parsing function doing and how. 

[This](https://www.pauladamsmith.com/blog/2011/05/go_time.html) blog post states that this is done like this to avoid context switching, but I have to disagree. I know we also have the format constants, but I don't think is that much more intuitive to discern whether you need 
`RFC850`
, perhaps 
`RFC1123`
 or maybe you would be better off using 
`RFC822`
. 

My conclusion is that there are many Go programmers that have created libraries to parse dates in the "old style". That could speak about the usefulness of that system and how people were disappointed with the current official way. 

https://github.com/vigneshuvi/GoDateFormat
https://github.com/bykof/gostradamus
https://github.com/takmongwai/go-strftime

Last, this post wouldn't be complete without concluding with this classic meme:

![DD/MM/YYYY is the best date format!](https://dev-to-uploads.s3.amazonaws.com/i/6gnzyh285b31v6n202fg.png)


*[This post is also available on DEV.](https://dev.to/rubenwap/the-go-way-to-parse-strings-into-dates-is-pretty-confusing-unless-you-live-in-a-small-fraction-of-the-world-42o9)*


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
