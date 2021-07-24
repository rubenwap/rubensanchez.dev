---
stackbit_url_path: posts/i-was-doing-bad-stuff-in-my-first-dev-job-sorry-part-i-58k0
title: I was doing bad stuff in my first dev job. Sorry! (Part I)
date: 2020-09-24T11:30:17.649Z
excerpt: >-
  I got my first developer job almost by accident. I was not looking for a dev
  job back then, but while...
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--vCYtRnli--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/i/ltbf053pu2nhsrcldfy8.png
comments_count: 0
positive_reactions_count: 3
tags:
  - beginners
  - career
  - productivity
canonical_url: >-
  https://dev.to/rubenwap/i-was-doing-bad-stuff-in-my-first-dev-job-sorry-part-i-58k0
template: post
---

I got my first developer job almost by accident. I was not looking for a dev job back then, but while I was working doing something else, some tools I had developed as work side project got some traction and I was able to get an offer from the same company to move into a position to develop full time. 

That was pretty cool! But looking back, I did things that would send shivers down the spines of security professionals, devops guys and other kinds of reasonable people. Luckily it served to teach me a valuable lesson, so let me share some of what I did, in case you find it useful. 

<img src="https://dev-to-uploads.s3.amazonaws.com/i/6rhynjcjplzquh7v4xem.jpg" alt="programmer" width="500"/>

### FIRST PART
* Testing (or lack thereof)
* Deploy like it's 1997
* Monitoring what matters

## Testing (or lack thereof)

When I started programming I was into Chrome Extensions. I loved the simplicity of sticking bits of [DOM](https://www.w3schools.com/js/js_htmldom.asp) manipulation into a package and see them coming to life upon page load. The first extension I did professionally  was interacting with an internal tool of the company and it got pretty popular, so the team using it started to request a lot of very diverse functionality. 

For me, every new feature was as simple as building the correct javascript using the browser's developer console, sticking the finished bit somewhere inside the extension script and call it a day. You can imagine that after many feature requests the extension grew to be a messy monster. Calling it spaghetti code doesn't do it justice. Copy-Paste-Oriented-Development could be an alternative too.

<img src="https://dev-to-uploads.s3.amazonaws.com/i/l168jgswm0h4q3kap4gi.jpg" alt="spaghetti-driven-development" width="300"/>

After I left the team, the new maintainers struggled with this project. What earlier was managed fully inside my brain -not the best scenario-, was now a meaningless bunch of files with lots of undocumented functions with names that kinda made sense only for some people. Because of that, new feature requests on that extension are now on hold. 

The fact that you know 100% that it works is not enough, because even in the most solitary project you can do, some day you will leave the company and the new people won't have a clue about why it should or shouldn't work. Besides that, can you guarantee that your brain will process all the possible scenarios for the feature to go through? 

A test won't slow you down. What will slow you down is to waste one day of work to find out why your new feature is breaking the previous feature. Testing is not some super special technique you must learn separately from coding. Testing IS part of coding. 

### Helpful links:

- **For Javascript**: I don't do a lot of JS recently, so my recommendation might be outdated, but last time I tried, I was having an easy time with [Mocha](https://mochajs.org/)

- **For Python**: Even though Python has 
`unittest`
 bundled in, I like to use [Pytest](https://docs.pytest.org/en/latest/)

- **For Golang**: For unit testing, you are more than covered with the standard Go testing library. For cases when you want [BDD](https://en.wikipedia.org/wiki/Behavior-driven_development) testing, you can look into [Ginkgo](https://onsi.github.io/ginkgo/)

(If I haven't convinced you and you are still not testing your code, at the very least write detailed documentation!)

## Deploy like it's 1997

Apparently nowadays is very cool to hit a button and engage a complex pipeline that will test your code, package it and deploy it. Even your infrastructure [lives in a piece of code](https://en.wikipedia.org/wiki/Infrastructure_as_code) so it can be replicated. 

Back when I started working with this it wasn't 1997, but in my mind it was, because my deployments consisted on connecting to a remote server and copy paste some files from my local computer. I know that FTP'ing stuff was the norm a while ago, but on top of that I used to edit the remote copy to add bits of code (which I had not saved in my local...), had credentials files laying around everywhere, and during a long time (luckily fixed after a while) the whole Python ecosystem on that machine ran off a Global Python environment without virtual envs, so services needing different dependency versions were out of luck. There was a bit of everything, a couple of web apps, some services, etc... 

<img src="https://dev-to-uploads.s3.amazonaws.com/i/hit9w223h1nlb6mcfivr.png" alt="work-in-production" width="300"/>

If you code is good enough to be shipped, ship it and don't touch it. Docker images are ideal to preserve an untouched environment. If you want to tinker with something because you forgot to fix a typo, release it again. Ideally your release should be automated so it's repeatable, but I am going to say that at the very least, release it from your local, don't do remote edits. Editing your production copy of something is a really bad idea, not because your typo fixing is dangerous in itself, but because it sets a precedent that can escalate to worse cases.

## Monitoring what matters

So when you start having lots of processes up and running, some are bound to fail. What will you do when something goes wrong? So ideally you would have a nice setup to monitor what matters, like I describe in [this article](https://dev.to/rubenwap/monitor-the-behavior-of-your-python-app-by-learning-influxdb-grafana-and-telegraf-3ehg). Depending on where are you deploying your tools, you could pair it with a nice logging tool. I like [Google Operations](https://cloud.google.com/products/operations) (formerly known as Stackdriver). Every cloud has its own and there are some other cloud agnostic tools out there.

So what did I do when my processes started to fail for the first time? Well, I had a genius technique which consisted on re-running the failed step and stare at the console to observe the crash. Naturally nothing came up because I was not logging anything. I couldn't help but starting with 
`print(suspiciousVariable)`
. It would be some time until I upgraded to use a logging library. 

You need to do that. You will be in control of a more flexible logging format and will be able to pick multiple outputs if needed. Please, do not print stuff, log it instead. And most importantly, log the relevant tracebacks and items that will help you diagnose a problem. Don't be me that time when after a failure I just logged the text "it failed". Very helpful. We could also talk about using a debugger, but that's for a future article.

<img src="https://dev-to-uploads.s3.amazonaws.com/i/tgguslw4byvtujdve1ez.png" alt="useless-logs" width="300"/>

If you found this interesting, come back for the [second part](https://dev.to/rubenwap/i-was-doing-bad-stuff-in-my-first-dev-job-sorry-part-ii-iak), where I will talk about:

* **Bad dependencies**
* **GitHub is not a storage room**
* **Project structure**

And will also advise on something you definitely don't have to learn. 

*[This post is also available on DEV.](https://dev.to/rubenwap/i-was-doing-bad-stuff-in-my-first-dev-job-sorry-part-i-58k0)*


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
