---
layout: post
title: "Data Structures in Swift"
date:   2022-08-28 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
> # Throughout this post you will learn:
>
> - What a Data Structure is
> - Different data structure types that Swift language offers

<br>
<h3 style="color: #403F3F">What's a Data Structure?</h3>

A Data Structure is a way to organize the data in order to make it easier and more efficient when dealing with it. Depending on the sort of problem your code is trying to solve or the flow and arquitecture your application follows you will use an specific data structure or other, because each data structure is suitable for a particular case.

Basically there are two types of data strcutures depending on how you store and access the data: static and dynamic. You can also classify the data structures by the way you go throught them: lineal and non-lineal. But this is just a theorical way to differentiate them.

I'm pretty sure that you have already use a data structure throught your code. And if you are doubting about that sentence...have you ever use an array? Yes, an array is a data structure, so you are not new with data structures it is just that you didn´t realice about that (me too not too long ago 😉). The Swift standard library offers three data structures: `Aray`, `Dictionary` and `Set`.

In the next sections we will go throught a description of a widely used data structures in Swift. We will also clarify what is each data structure suitable for and when we should choose it. Let's get started!

<br>
<h3 style="color: #403F3F">Array</h3>

Arrays are very used mainly because they are very predictable. Arrays keep elements in sequence so it's easy to go throught all the elements in the proper order. Let's have a look to the following example:

<style>.hljs-function{color:#6BDFFF;}.hljs-selector-class{color:#DABAFF;}.hljs-name{color:#DABAFF;}.hljs-literal{color: #B281EB;}.hljs-regexp{color:#DABAFF;}.hljs-selector-tag{color:#FF7AB2;}.hljs-params{color:#ACF2E4;}.hljs-section{color:#6BDFFF;}.hljs-type{color:#ACF2E4;}.hljs-string{color:#FF8170;}.hljs-built_in{color: #B281EB;}.hljs-selector-id{color:#DABAFF;}.hljs-title{color:#6BDFFF;}.hljs-class{color:#6BDFFF;}.hljs-symbol{color:#FF8170;}.hljs-builtin-name{color: #B281EB;}.hljs-meta{color:#B281EB;}.hljs-bullet{color:#FF8170;}.hljs-link{color:#DABAFF;}.hljs-strong{font-weight:bold;}.hljs-quote{color:#7F8C98;}.hljs-template-variable{color:#DABAFF;}.hljs-variable{color:#DABAFF;}.hljs-deletion{color:#DABAFF;}.hljs-keyword{color:#FF7AB2;}.hljs-addition{color:#FF8170;}.hljs-emphasis{font-style:italic;}.hljs{padding:0.5em;color:#E0E0E0;display:block;}.hljs-comment{color:#7F8C98;}.hljs-number{color: #D9C97C;}.hljs-attribute{color:#DABAFF;}.hljs-tag{color:#DABAFF;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">Event</span> </span>{
    <span class="hljs-keyword">var</span> attendees: [<span class="hljs-type">Person</span>]
    
    <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">printAttendees</span><span class="hljs-params">()</span></span> {
        attendees.<span class="hljs-attribute">forEach</span> { attendee <span class="hljs-keyword">in</span>
           <span class="hljs-attribute"> print</span>(attendee)
        }
    }
}

<span class="hljs-keyword">var</span> person1 =<span class="hljs-attribute"> Person</span>(name: <span class="hljs-string">"Javier"</span>, email: <span class="hljs-string">"javier@email.com"</span>)
<span class="hljs-keyword">var</span> person2 =<span class="hljs-attribute"> Person</span>(name: <span class="hljs-string">"Maria"</span>, email: <span class="hljs-string">"maria@email.com"</span>)

<span class="hljs-keyword">var</span> event =<span class="hljs-attribute"> Event</span>(attendees: [person1, person2])
event.<span class="hljs-attribute">printAttendees</span>()</code></pre>

As you can see going throught the array elements is very straight forward and it can be very useful for some cases, but for other purposes that could be a drawback. Let's imagine now that we want to remove an element of the array. The first thing we will need to do is to get the index of the element and then remove it like this:

<style>.hljs-regexp{color:#DABAFF;}.hljs-number{color: #D9C97C;}.hljs-selector-class{color:#DABAFF;}.hljs-bullet{color:#FF8170;}.hljs-template-variable{color:#DABAFF;}.hljs-section{color:#6BDFFF;}.hljs-function{color:#6BDFFF;}.hljs-symbol{color:#FF8170;}.hljs-name{color:#DABAFF;}.hljs-title{color:#6BDFFF;}.hljs-tag{color:#DABAFF;}.hljs-built_in{color: #B281EB;}.hljs-class{color:#6BDFFF;}.hljs-addition{color:#FF8170;}.hljs-link{color:#DABAFF;}.hljs-comment{color:#7F8C98;}.hljs-variable{color:#DABAFF;}.hljs-emphasis{font-style:italic;}.hljs{padding:0.5em;color:#E0E0E0;display:block;}.hljs-meta{color:#B281EB;}.hljs-literal{color: #B281EB;}.hljs-strong{font-weight:bold;}.hljs-type{color:#ACF2E4;}.hljs-quote{color:#7F8C98;}.hljs-selector-id{color:#DABAFF;}.hljs-params{color:#ACF2E4;}.hljs-selector-tag{color:#FF7AB2;}.hljs-attribute{color:#DABAFF;}.hljs-builtin-name{color: #B281EB;}.hljs-deletion{color:#DABAFF;}.hljs-keyword{color:#FF7AB2;}.hljs-string{color:#FF8170;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">extension</span> <span class="hljs-title">Event</span> </span>{
    
    <span class="hljs-keyword">mutating</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">removeAttendee</span><span class="hljs-params">(attendee: Person)</span></span> {
        <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> index = attendees.<span class="hljs-attribute">firstIndex</span>(of: attendee) <span class="hljs-keyword">else</span> {
            <span class="hljs-keyword">return</span>
        }
        attendees.<span class="hljs-attribute">remove</span>(at: index)
    }
}


event.<span class="hljs-attribute">removeAttendee</span>(attendee:<span class="hljs-attribute"> Person</span>(name: <span class="hljs-string">"Javier"</span>, email: <span class="hljs-string">"javier@email.com"</span>))</code></pre>

In this example the array only contains two elements, but what if the array contains hundreds or thousands of elements? In that case geting the index of the element we want to remove will take time.

<br>
<h3 style="color: #403F3F">Sets</h3>



<br>
<h3 style="color: #403F3F">Summing Up</h3>

- A Data Structure is a way to store and organize the data so it is easier and more efficient to manage it.


<br>
Thanks for reading :)

<br>
<table style="width: 100%; overflow: scroll; border-right: 0px solid gray; border-left: 0px solid gray">
    <tr style="border-right: 0px solid gray; border-left: 0px solid gray">
        <td style="width: 20%; border-top: 2px solid #DDDDDD; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid gray; text-align: center; vertical-align: center; padding: 0px">
            <p style="color: #A8A8A8; font-size: 20px; margin: 0px 0px"><b>Written By</b></p>
        </td>
        <td style="border-top: 2px solid #DDDDDD; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid gray; text-align: center; vertical-align: center; padding: 0px">
            <p style="color: #A8A8A8; font-size: 20px"><b></b></p>
        </td>
    </tr>
    <tr style="border-right: 0px solid gray; border-left: 0px solid gray">
        <td style="border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 2px solid #DDDDDD; color: gray; font-size: 20px; background-color: #FDFDFD; text-align: center; vertical-align: center; horizontal-align: center; padding: 5px">
        <img style="display: block; margin-left: auto; margin-right: auto; width: 100%; object-fit: contain" src="/assets/img/yo.png">
        </td>
        <td style="border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 2px solid #DDDDDD; background-color: #FDFDFD; text-align: left; vertical-align: center; padding: 10px">
            <p style="font-size: 26px; margin: 0px 0px"><b>Javi Carrillo</b></p>
            <p style="font-size: 18px">In the past I developed car's powertrain control software. Now I develope software for  Apple platforms.</p>
        </td>
    </tr>
</table>




