---
layout: post
title: How Swift Closure Works
date:   2021-05-17 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: tech
---
<p style = "color: gray">Basically a closure is a block of code that can be passed around and through the code. Their function is pretty similar to blocks in ObjectiveC and C. You already know that we can pass a variable as a parameter to a function, but what you may not know is that it is also possible to pass a function as a parameter to a function. That's how closures works, as functions embedded in other functions and it is very suitable for instance  when we want to execute code for instance when we want to execute a block of code once another block of code is already executed and is ready to pass data to the second block of code.</p>

Lets see the concept with an example. Imagine that we want to build a tiny program that works out the sum of first 1000 numbers and print the result through the terminal. We could do that easily with just one function but in this case we are using two functions or two blocks of code. The first block of code is the closure, which is going to handle the result printing. And the second block of code will handle the calculations and the handler which will pass the result to the closure.

So the first block, the closure, will take an Int type as a parameter and will return a Void, cause it just print a message through the terminal:

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 10px" src="https://jcentercreation.github.io/JekyllPersonalWeb/assets/img/closure1.png"></h1>

As you can see we have define a constant that gets an Int and returns a Void. This is a closure. Inside the closure Int type is named as "total" and is print inside a string.

Now we are gonna code the second block. This function will give a value to that Int type that will be passed along to the closure. We have to define as argument of the function another function usually called "completion handler", that will be instanced once the code inside the function is done. That means that the completion handler function will be executed once the prior code has finished. As you could imagine that completion handler function will be the closure we have built prior.

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 10px" src="https://jcentercreation.github.io/JekyllPersonalWeb/assets/img/closure2.png"></h1>

Finally we instance the function passing the closure as the completion handler:

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 10px" src="https://jcentercreation.github.io/JekyllPersonalWeb/assets/img/closure3.png"></h1>

As a final comment, and if you are following this short explanation writing the code in your own computer, you probably have noticed that Swift gives you the chance to work with the input argument of the closure embedded as a completion handler when instancing the fucntion:

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 10px" src="https://jcentercreation.github.io/JekyllPersonalWeb/assets/img/closure4.png"></h1>

The result is:

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 10px" src="https://jcentercreation.github.io/JekyllPersonalWeb/assets/img/closure5.png"></h1>
