---
stackbit_url_path: posts/lua-and-love-are-your-gateway-drug-to-videogame-making-53ag
title: Lua and Löve are your gateway drug to videogame making
date: '2020-03-23T19:09:18.069Z'
excerpt: >-
  So I admit it, I always wanted to create a game, but I have been too easily
  scared by the amount of w...
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--CWCnMEGl--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/i/tmydjr132wb5pj7u8xsj.png
comments_count: 2
positive_reactions_count: 18
tags:
  - lua
  - love2d
  - gamedev
canonical_url: >-
  https://dev.to/rubenwap/lua-and-love-are-your-gateway-drug-to-videogame-making-53ag
template: post
---
So I admit it, I always wanted to create a game, but I have been too easily scared by the amount of work that seemed to be required to delve into something like Unity. I did some easy attempts at HTML canvas based Javascript games, but nothing impressive enough. Although if you are so inclined, do check the great [PhysicsJS](http://wellcaffeinated.net/PhysicsJS/) library and you might end up creating something cool.

Then my quest had a clear goal: Be able to make a cool game with the most minimal amount of effort possible. That's where Lua and Löve come in. 

![Love](https://dev-to-uploads.s3.amazonaws.com/i/ap46ifd0u9w3sdq47va8.png)

[Lua](https://www.lua.org/) is a very lightweight language which is used often to be embedded and extend other programs, but it's also absolutely standalone in its own right. In the scenario that we are seeing today, it will be complemented by the [Löve 2D engine](https://love2d.org/) for a perfect combination of easy to learn and great results. 

*Note:* This post is intended as a super lightweight introduction to create games this way, so you won't end up with a complete example, but you will learn about some basic techniques that can get you started. 

## Requirements:

* Lua installed in your system. I am using macOS, so I can skip this step because it already comes installed by default. If you are using Windows, you probably want [this](http://luadist.org/)
* Löve installed in your system. Download it from [their site](https://love2d.org/) picking the package that fits your operative system.

## Get something up and running

### Create files:

There is not a very strict requirement on what your folder structure should look like. Let's create a folder called 
`love-tutorial`
 and inside, a file called 
`main.lua`


If you open your terminal in that folder, in mac you can do:

    $ open -n -a love .

Which means "run 
`love`
 with all contents of the current folder. Since you need to give Löve a folder rather than a single file. 

### The code

Roughly speaking, Löve is based on three big callback functions. There are many more, but the following three are crucial:

* LOAD

This function is called at the start and it's supposed to load all your data and variables. For instance, you could initialize a table (similar to what would be a Python dict or a JS object) with the data about a character:


```lua
function love.load()

    hero = {}
    hero.name = "Ruben"
    hero.health = 100
    hero.alive = true

    score = 0

end
```


* UPDATE

This function is running repeatedly once and again during the duration of your session. 
`dt`
 is the time passed since the last run. You can use this to update your data.


```lua
function love.update(dt)

    if hero.health < 1 then
        hero.alive = false
    end

    if enemy.dead then
        score = score + 1
    end

end
```


* DRAW

Like 
`update`
, this function is also called continuously, but rather than calculate the values, its mission is to draw on screen all the game elements. For instance:


```lua
function love.draw()
    
    love.graphics.print(score, 400, 300)   

end
```


So you can see how the value being printed 
`score`
 would constantly be redrawn, but since we are also updating the value of it in the 
`update`
 function, on every redraw we would get the most updated value. 

### Beyond the super basics

Ok, you have seen the principle of how a Love game works, but this is not even the beginning of the beginning. There is a lot of ground to cover. For instance, movements:

 
```lua
if love.keyboard.isDown("right") then
    hero.x = hero.x + hero.speed * dt
end

 ```


 Or actually drawing elements:

 
```lua
love.graphics.rectangle("line", 400, 300, 100, 100)
 ```


 Which could become an image with a small change:

  
```lua
love.graphics.draw("path to img", 400, 300, 100, 100)
 ```


## Conclusion

Rather than a game making tutorial, this article was geared towards encouraging you to explore Lua and Love, since the learning curve is relatively small (compared to other alternatives) and can get you enjoying your own games in very little time. 


*[This post is also available on DEV.](https://dev.to/rubenwap/lua-and-love-are-your-gateway-drug-to-videogame-making-53ag)*


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
