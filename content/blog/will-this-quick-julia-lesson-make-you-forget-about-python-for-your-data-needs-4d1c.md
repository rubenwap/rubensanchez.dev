---
stackbit_url_path: >-
  posts/will-this-quick-julia-lesson-make-you-forget-about-python-for-your-data-needs-4d1c
title: >-
  Will this quick Julia lesson make you forget about Python for your data
  needs? 
date: '2020-10-02T17:52:24.272Z'
excerpt: >-
  I have been a Python heavy user for some time, so when I learned about Julia I
  thought that the prosp...
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--err143ep--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/i/6vp8zlm20fe2beo8lb0o.png
comments_count: 0
positive_reactions_count: 14
tags:
  - datascience
  - julia
  - python
  - learning
canonical_url: >-
  https://dev.to/rubenwap/will-this-quick-julia-lesson-make-you-forget-about-python-for-your-data-needs-4d1c
template: post
---
I have been a Python heavy user for some time, so when I learned about [Julia](https://juliacomputing.com/) I thought that the prospect it was introducing was very interesting. Simplicity in the syntax but execution power closer to C, but maybe the obstacle to beat was too high. Python already has massive adoption, there is a huge ecosystem with modules for any task you can think about and even people from the R world seem to be migrating to it. Also, Matlab seems to keep having strong presence in Academia. So, what is the incentive to change? Will Julia arise and become a main actor in the scientific computing community? 

Indeed, it is already quite relevant if you [look at the case studies](https://juliacomputing.com/case-studies/) you will see how companies such as NASA, CISCO or PFIZER are already using it in serious projects.

While I don't suggest to throw all your Python project out the window and rewrite everything in Julia, I think the language is interesting enough to merit a quick look at its functionality. So if you have heard comments on unstability or packages not mature enough, you can draw your own conclusions after giving it a try. 

![julia-indexes-start-at-1-mm-regular-programmers-indexes-start-57672418](https://dev-to-uploads.s3.amazonaws.com/i/54kv17cv7ebyk5097qx8.png)
 

## Installation

After you install [Julia](https://juliacomputing.com/) from their site (or if you are on mac, doing 
`brew cask install julia`
), I am going to assume that you also want a notebook environment. For that you have two options. You can install the desktop app [Nteract](https://nteract.io/), or if you are so inclined, stick with the classic Jupyter Notebook (or Jupyter lab) for which you need to have Python installed, so you can do 
`pip3 install jupyterlab`
. 

When you have your notebook application, you still need the actual Julia notebook kernel, so go to your terminal and type 
`julia`
. You should see this:


```
               _
   _       _ _(_)_     |  Documentation: https://docs.julialang.org
  (_)     | (_) (_)    |
   _ _   _| |_  __ _   |  Type "?" for help, "]?" for Pkg help.
  | | | | | | |/ _  |
  | | |_| | | | (_| |  |  Version 1.5.1 (2020-08-25)
 _/ |\__'_|_|_|\__'_|  |  Official https://julialang.org/ release
|__/                   |

julia>

```

Now type the `
]
` symbol, and the prompt will change to this:

```

(@v1.5) pkg>


```

So you can type

```

(@v1.5) pkg> add IJulia


```

press enter, and observe how the kernel package gets installed. If you had already opened your notebook before, it's pretty likely that you need to close it and open it again so it detects the new Julia package. 


## Getting started with the language

The basic things are what you might expect:

```
julia

# Printing
println("Hello")

# Variables
my_name = "Ruben"

typeof(my_name) # String

# Arithmetic
sum = 3 + 7
difference = 10 - 3
product = 5 * 5
quotient = 100 / 10
power = 10 ^ 2
modulus = 101 % 2

# String interpolation
interp = "My name is $my_name"

```

Some interesting quirks:

```
julia
# Note that single quote denote a character
typeof('a') # char
typeof("a") # string

# Concatenations can force transformations
# Like in this case, from int to string

string(10, " can also be converted")
# outputs: "10 can also be converted"

```

Some nice structures:

```
julia
# Dictionary
phonebook = Dict("Ruben" => 555234898, "Santa Claus" => 00358787876)

# Tuple
animals = ("dog", "donkey")

animals[1] # Outputs "dog" because Julia is 1 indexed!!!!

# Mix types
farm2 = ["chicken", "hen", "horse", "cow", 56]


```

## Control structures and functions

```
julia
# classic while. What you would expect
n = 0
while n < 10
    n += 1
    println(n)
end

# the for is quite pythonic
for n in 1:10
    println(n)
end

```

Now something new. Look at this way to create vectors, either populated or empty. We can do it either with random data or specific data

```
julia
# a 5 rows 3 cols vector with random data
r, c = 5,3
A = rand(r, c)

# same but with zeroes
Z = zeros(r, c)

```
Because of this:
```
julia
# We can have a for loop with two conditions in that vector
for i in 1:m, j in 1:n
    A[i,j] = i + j
end

# Or something looking like a Python list comprehension
C = [i + j for i in 1:m, j in 1:n]

```
Finally, functions can be defined in different ways
```
julia
function hi(name)
    println("Hi $name")
end

hi2(name) = println("Hi $name")

hi3 = name -> println("Hi $name")

# and you would call them as you expect
hi("world")
hi2("world")
hi3("world")

```

## What about dataframes

If you are going to work with dataframes, you are going to need to know how to install (and import package). We have already seen one way to install (when we added IJulia earlier on)

Another way is from within your code:

```
julia
using Pkg
Pkg.add("CSV")
Pkg.add("DataFrames")

```

Although you might want to do that with the other option (via terminal) to don't execute this every time you run your code. Normally when you ship a Julia project you would have a `
.toml
` file with all the dependencies, so you can grab them at once by issuing the `
] instantiate
` instruction. 

Once you have installed the DataFrames module (and CSV if you need it), we can create Dataframes from files or from our own data constructions. For the example, I am using [this file](https://github.com/noahg/sp500csv/blob/master/data/sp500.csv)

```
julia
df = DataFrame(CSV.File("sp500.csv"))

```

You can use pretty familiar functions to get the heads and lasts:

```
julia
# 3 is how many rows you want to display
head(df, 3)
first(df, 3)
last(df, 3)

```
You can get the column names
```
julia
names(df)

```
And select specific columns
```
julia
df["column_name"]

```
Selecting rows uses the array index for what you need. For example, this is the first 5 lines and the first 3 columns
```
julia
df[1:5, 1:3]

```
Or you could get very specific searching. Just show me the rows where the symbol is either `
MMM
` or `
ABT
`
```
julia
filter(row -> row.symbol in ["MMM", "ABT"], df)

```


## To summarize

Although by this article you could think that the biggest plus of Julia is that it has a very nice syntax, the truth is that I am only scratching the surface, since its real benefit is way deeper than that. It's not only a matter of speed (but also)

Take a look at the article on [*Why We Created Julia*](https://julialang.org/blog/2012/02/why-we-created-julia/) to have a clearer view. 

Julia seems a good solution for the two languages problem with Python (if you want speed you need to revert to C extensions), it has modules for most tasks you might need, specially in the scientific computing area (including ML) and it feels stable and well maintained. 

If you are a happy Python/R/Matlab user, you might not switch overnight, but I still think it's a very fresh and interesting proposition. 



*[This post is also available on DEV.](https://dev.to/rubenwap/will-this-quick-julia-lesson-make-you-forget-about-python-for-your-data-needs-4d1c)*


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
