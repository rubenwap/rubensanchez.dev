---
stackbit_url_path: posts/should-you-learn-functional-programming-3flp
title: Should you learn Functional Programming?
date: 2020-04-10T22:06:45.251Z
excerpt: >-
  Have you started learning to program recently and heard that all the cool
  people are doing functional...
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--0AXTi3dX--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/i/cnz2zop2b3tahkozkdtb.png
comments_count: 3
positive_reactions_count: 7
tags:
  - functional
  - codenewbie
  - beginners
canonical_url: 'https://dev.to/rubenwap/should-you-learn-functional-programming-3flp'
template: post
---
Have you started learning to program recently and heard that all the cool people are doing functional programming? Unsure about whether giving it a go or not? I used my mad design skills to put together a handy chart. 

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/0sjkay0qnq1kz3j2gd2u.png)

Let me add some extra glossary that might help you in your quest:

*Programming paradigm:* 
This means how we classify programming languages according to their features. If you started learning recently, it's very likely that you are using a multiparadigm language such as Javascript or Python. It means that the language accepts multiple paradigms and doesn't force you to stick with just one.  

*Functions as first class citizens:* 
This refers to the fact that functions can have the role of values, being used as parameters in functions (a function that takes another function as parameter) or being used as a return value (a function that returns another function). That has a name: Higher order functions. 

*About mutating data:*
One of the things that Functional Programming advocates not to do. Being able to use setters and mutate states, can easily develop into a cause for bugs and great distress. Rather than mutate, we should ideally return a copy of our target with the new value. This can become confusing in languages that don't enforce this, but for example if you use Javascript, there is a library called [Immutable.js](https://immutable-js.github.io/immutable-js/) which can help structuring your code this way. For Python you could use something like [Pysistence](https://pythonhosted.org/pysistence/)

*Function Currying:* 
So no, the name is not about the delicious oriental food (haha, I am surely the first person doing this joke!), but more about the mathematical technique on doing the following. What currying does it to split a function with various parameters into a number of function with one parameters. Why would you want to do this? Well, it can help you to transform very generic functions into really specific ones. To make things clear, let me show you this quick Python example.


```python
def menu(a):
    def today(b):
        return f"{a} with {b}"
    return today

# So now we could call this function like:
menu("rice")("chicken")

# and this returns:
# "rice with chicken"

# But things get more interesting if we assign it to a variable
rice_adder = menu("rice")

# So when calling it like below, you can observe 
# how we have transformed a function with a very generic 
# purpose into a really specific one:

rice_adder("chicken")
# returns "rice with chicken" 
```


*Test easily (no side effects!):*

Ok, so if you have a function called 
`transform_to_uppercase`
 we should expect that trasnform to uppercase is the only thing the function does. Not saving the text to your database or any other task that deviates from the main purpose of the function. So:


```python
def menu_to_upper(menu):
    return menu.upper()
```


can be tested with a simple 
`menu_to_upper("chicken with rice") == "CHICKEN WITH RICE"`
, but how do you really test this?


```python
def menu_to_upper(menu):
    db = requests.get("localhost/api/db_name")
    menu_upper = menu.upper()
    db.save("daily_menus", menu_upper)
```

Not so easy, right? Here you see in action the benefit of getting functions to do just one thing, although we could say that the single responsability principle is something you should try to use regardless of the paradigm you are using. 

# Conclusion

Functional Programming is definitely more difficult to get right than imperative, but at the same time it can be more succint. You are actually spending longer time in order to write less. Rather than shoot code with a machine gun and hope for the best, FP forces you to make big efforts to produce something, but after you have done it, you can be sure that it will probably be more stable and less error prone. 

Give it a try! 



*[This post is also available on DEV.](https://dev.to/rubenwap/should-you-learn-functional-programming-3flp)*


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
