---
stackbit_url_path: >-
  posts/use-python-in-your-browser-client-code-together-with-js-no-server-required-thanks-to-web-assembly-2be2
title: >-
  Use Python in your browser client code together with JS. No server required,
  thanks to Web Assembly
date: 2020-05-21T10:57:53.375Z
excerpt: >-
  Use case: Let't say that I love Python Pandas for data manipulation, but at
  the same time I need to u...
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--rALsVPXN--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/i/lwon21ugw8hnbzqoiuh3.png
comments_count: 3
positive_reactions_count: 81
tags:
  - python
  - javascript
  - webassembly
  - tutorial
canonical_url: >-
  https://dev.to/rubenwap/use-python-in-your-browser-client-code-together-with-js-no-server-required-thanks-to-web-assembly-2be2
template: post
---
*Use case: Let't say that I love Python Pandas for data manipulation, but at the same time I need to use the manipulation results in a regular client-side webpage. The classic method I would do is to have a Python backend, do my calculations in there and then send the results to the front, via an API, server rendered templates or whatever.* 

**I am going to present you an alternative based on Web Assembly and the Pyodide project.** 

Web Assembly is a standard (supported in all major browsers) that roughly speaking says "This is how executable binaries should look like and here is how they should interact with the host" (the host in this case being the browser). That opens the door to client side applications written in a wide variety of languages. Take a look at this repo with the list of available languages: 

https://github.com/appcypher/awesome-wasm-langs

One of the fruits of this project has been 
`Pyodide`
, which allows you to execute real Python code in the browser. Do not mistake this with other projects that allow you to write Python and then transpile it into Javascript so it can run in the browser. Here what is running is the actual Python code. 

We can do a quick demo and then I walk you through what just happened:


```html
<!DOCTYPE html>
<html>
<head>
    <script type="text/javascript">
        window.languagePluginUrl = 'https://pyodide-cdn2.iodide.io/v0.15.0/full/';
    </script>
    <script src="https://pyodide-cdn2.iodide.io/v0.15.0/full/pyodide.js"></script>
</head>
<body>
    Pass JS data to Python pandas (fully in-browser) and returns results back to JS
    <script type="text/javascript">
        const data = JSON.stringify([{ "name": "Ruben", "surname": "Sanchez" }, 
        { "name": "Michael", "surname": "Johnson" }, 
        { "name": "John", "surname": "Smith" }])
        languagePluginLoader.then(function () {
            pyodide.loadPackage(['pandas']).then(() => {
                pyodide.runPython(`

                import pandas as pd
                df = pd.DataFrame(${data})
                count = df["name"].count()
               
`)
                console.log(pyodide.pyimport('count'))
            });
        });
    </script>
</body>
```


Save that snippet in a new html file or run it from this CodePen https://codepen.io/rubenwap/pen/yLYZORW Note that the results you are looking for will be in the 
`console`
 tab! last 
`console.log`
 in the code should print 
`3`
, and that is the value we have taken from Python. 

Overall, We have done three nice things:


###  1) We have defined JS data to be used inside our Python script

Your starting point for this mix'n'match session can be regular Javascript data that you have obtained in any way you like:


```js
 const data = JSON.stringify([{ "name": "Ruben", "surname": "Sanchez" }, 
        { "name": "Michael", "surname": "Johnson" }, 
        { "name": "John", "surname": "Smith" }])
```



### 2) We have executed actual Python code in the browser, including Pandas!

Don't be fooled by the ugly way in which you need to specify your Python code (as string inside the 
`runPython`
 method). You could write an actual nice 
`.py`
 file and then import the text contents via Ajax, so it would look nicer. 

Note that here, 
`loadPackage`
 is acting somehow like an installation of that package. If you try to import pandas without having loaded it first, it won't work. 


```js

 pyodide.loadPackage(['pandas']).then(() => {
                pyodide.runPython(`

                import pandas as pd
                df = pd.DataFrame(${data})
                count = df["name"].count()
               
`)
```



### 3) We have taken the results of our Python scripts and sent them back to Javascript


```js
    console.log(pyodide.pyimport('count'))
```


I think this is pretty cool, the fact that you can run something in a totally different language and have it available in Javascript without having to go back and forth with API requests. 

If you found this interesting, don't forget to explore their docs:
https://pyodide.readthedocs.io/en/latest/# 

And generally, WASM docs, specially if you code in any of the other compatible languages:
https://webassembly.org/getting-started/developers-guide/

*[This post is also available on DEV.](https://dev.to/rubenwap/use-python-in-your-browser-client-code-together-with-js-no-server-required-thanks-to-web-assembly-2be2)*


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
