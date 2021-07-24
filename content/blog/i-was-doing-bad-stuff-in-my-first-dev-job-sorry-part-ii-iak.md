---
stackbit_url_path: posts/i-was-doing-bad-stuff-in-my-first-dev-job-sorry-part-ii-iak
title: I was doing bad stuff in my first dev job. Sorry! (Part II)
date: 2020-09-24T12:22:49.643Z
excerpt: >-
  Glad to see you back. It means that you liked the previous part of this
  article. Or if you haven't se...
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--LMMaUFJ0--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/i/j52oybcvdycfmk193hs0.png
comments_count: 0
positive_reactions_count: 3
tags:
  - beginners
  - career
  - productivity
canonical_url: >-
  https://dev.to/rubenwap/i-was-doing-bad-stuff-in-my-first-dev-job-sorry-part-ii-iak
template: post
---
Glad to see you back. It means that you liked the previous part of this article. Or if you haven't seen it yet, [please check it out](https://dev.to/rubenwap/i-was-doing-bad-stuff-in-my-first-dev-job-sorry-part-i-58k0). 

I would like to continue the series by explaining about some more dubious techniques I was using during my first dev job. I hope you can find some advice and good tips from reading it!

## Bad dependencies

So in that first job, we were very keen on researching new cool tools. I specially remember a case where we had to use a famous library for Python, but for some reason I stumbled upon a another library that was internally using the previous library to accomplish the very same thing, but with a couple of nice improvements on top.  

The outcome of the story? I lost quite a long time because shortly after implementing it in my project, things started to fail and initially it wasn't obvious that it was because of the library (if you remember the first part of the article, I wasn't fond of logging important stuff back then). When I finally realized that this was the cause, it also took quite a bit of time to find out about the specific reason that caused the problem. After spending all that time, we realized the wrapper was not even maintained very frequently, so a pull request that someone had done with that fix just stayed in the PR limbo lingering forever. 

<img src="https://dev-to-uploads.s3.amazonaws.com/i/genff3rzr7banaoyxiql.jpg" alt="dependencies" width="300"/>

Choosing dependencies is not a trivial task. Is not enough with finding something that does what you want. You also have to consider whether it will be maintained or perhaps it will be abandoned and issues forgotten forever. It's true that open source software has tools to fix that (you can usually fork and fix the stuff yourself if nobody is caring about the main repo), but you have to analyze your cost/benefit if you are doing this. 

Also, in some cases you have to consider about whether you really need a dependency. Have you read about the [Left pad debacle?](https://www.davidhaney.io/npm-left-pad-have-we-forgotten-how-to-program/) Please go ahead and read it, it's very interesting.

## Structure and discipline

So, consider this, you work in a team of 3 people and you all work in the same language (let's say Python). Jim likes to bootstrap his projects with [Pipenv](https://pipenv.pypa.io/en/latest/). Mike uses [Poetry](https://python-poetry.org/) and Scott just uses the Python global environment. Mike adds a [setup.py](https://stackoverflow.com/questions/1471994/what-is-setup-py), Jim likes to keep all the files in the root folder and Scott puts as much code as possible inside a single file, so it's easier to find later. Mike builds his Docker based projects with the official Python full image. Jim uses Python slim and Scott uses the Ubuntu image. 

<img src="https://dev-to-uploads.s3.amazonaws.com/i/t3w0vot2x39be73oyflo.jpg" alt="works-on-my-machine" width="300"/>

Now, the manager has notified the guys that Brandon will join the team next Monday and would like the team to give him an overview on how to build Python projects. What's the next step here?

The existing members of the team have to agree on a unified way to work. And the way they need to do it is ensuring that Brandon can navigate the projects easily and intuitively from day one. You need a project structure in which placements of the parts are obvious. You shouldn't need people to tell you where things are.

If you cannot agree, you can use well established public layouts such as:

* [Golang](https://github.com/golang-standards/project-layout)
* [Python](https://docs.python-guide.org/writing/structure/)

## GitHub is not a storage room

By the time I discovered about version control, I ran into the usual issues that beginners encounter when starting to use it. 

* Not using branches: I realized quickly that juggling different attempts for features plus fixes for the current version inside the master branch was going to develop into a tragic mess of 
`revert`
 commands. Please use branches. 
* Not learning beyond the basics: So your muscle memory can already do 
`git status`
 
`git add -A`
 
`git commit -m "Changes"`
 and 
`git push origin master`
. Nice, but that is the happy path. I advise to learn about how to react when problems arise (merge conflicts) so you are used to work with things like 
`git mergetool`
 and understanding how can you revert to previous commits. 

<img src="https://dev-to-uploads.s3.amazonaws.com/i/4okln3i3k79xyk56f08q.jpg" alt="git" width="300"/>

However, the most striking error when using Git and GitHub, is the fact that some beginner developers would treat it as the final storage area where you stick your finished projects. So then your code is not in GitHub because "is not ready". Well, that's the whole point of GitHub. When you bake a cake you put it in the fridge when you are finished, but your code is not a cake and GitHub is not a fridge. In fact we could say that GitHub is the kitchen itself. Please, commit early and commit often. 

## Don't learn this

Surprisingly I found out that knowing how to do something is not really a very interesting skill. Let's say you work with Python. Well, knowing how to open a CSV file is not interesting. Anyone can Google how to do that. Knowing how to connect to a database is not interesting. Same as before, all those are just skills taken from the instructions manual of certain libraries. Can you create a dataframe out of json data? Very few people will care. Specific skills (how to use this or that library to accomplish this or that task) are not very valuable. If you don't know them today and you need them, just read the documentation.

However, the really powerful thing is to take all what you have read in the docs and put it together in a way that accomplishes what I describe in this series of articles. That will indeed leave a good footprint and make you a well rounded developer. To be able to "use Python" is not as amazing as the fact of being able to use it well and efficiently. It will really make a difference. 




*[This post is also available on DEV.](https://dev.to/rubenwap/i-was-doing-bad-stuff-in-my-first-dev-job-sorry-part-ii-iak)*


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
