---
layout: post
title: "Decoding Swift generics power"
date:   2022-07-31 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
> # Throughout this post you will learn:
>
> - The generic code potential
> - Generic types
> - Generic types constraints

<br>
<h3 style="color: #403F3F">The generic code potential</h3>

Basically a *generic* code is one that has the potential to be used and implemented across many cases withouth the need to be refactored or extended in order to cover a new use case that we stumble across when developing our app.
For instance, lets imaging that we need a function that prints some information. Whithout generic coding we could perform something like this:

<style>.hljs-quote{color:#7F8C98;}.hljs-addition{color:#FF8170;}.hljs-symbol{color:#FF8170;}.hljs-bullet{color:#FF8170;}.hljs-link{color:#DABAFF;}.hljs-meta{color:#B281EB;}.hljs-strong{font-weight:bold;}.hljs-variable{color:#DABAFF;}.hljs-string{color:#FF8170;}.hljs-attribute{color:#DABAFF;}.hljs-function{color:#6BDFFF;}.hljs-selector-tag{color:#FF7AB2;}.hljs-deletion{color:#DABAFF;}.hljs-type{color:#ACF2E4;}.hljs-comment{color:#7F8C98;}.hljs-builtin-name{color: #B281EB;}.hljs-class{color:#6BDFFF;}.hljs-regexp{color:#DABAFF;}.hljs-number{color: #D9C97C;}.hljs-params{color:#ACF2E4;}.hljs-name{color:#DABAFF;}.hljs-section{color:#6BDFFF;}.hljs-template-variable{color:#DABAFF;}.hljs-emphasis{font-style:italic;}.hljs-title{color:#6BDFFF;}.hljs{display:block;color:#E0E0E0;padding:0.5em;}.hljs-built_in{color: #B281EB;}.hljs-literal{color: #B281EB;}.hljs-selector-class{color:#DABAFF;}.hljs-selector-id{color:#DABAFF;}.hljs-tag{color:#DABAFF;}.hljs-keyword{color:#FF7AB2;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">display</span><span class="hljs-params">(data: Int)</span></span> {
   <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Data:"</span>, data)
}
<span class="hljs-attribute">
display</span>(data: <span class="hljs-number">4</span>)</code></pre>

But, what would happen if we want to display an `string` rather than an `Int?`` We would be tempted to write another function called something like `displayString` or the like. Are we keeping up with this strategy along all of the code in order to cover all the types implemented? Fortunately Swift brings the generics as a solution for this challenge, and the proper solution is to write a generic function like this:

<style>.hljs-link{color:#DABAFF;}.hljs-number{color: #D9C97C;}.hljs-symbol{color:#FF8170;}.hljs-strong{font-weight:bold;}.hljs-literal{color: #B281EB;}.hljs-title{color:#6BDFFF;}.hljs-comment{color:#7F8C98;}.hljs-built_in{color: #B281EB;}.hljs-emphasis{font-style:italic;}.hljs-attribute{color:#DABAFF;}.hljs-function{color:#6BDFFF;}.hljs-meta{color:#B281EB;}.hljs-bullet{color:#FF8170;}.hljs{display:block;padding:0.5em;color:#E0E0E0;}.hljs-builtin-name{color: #B281EB;}.hljs-keyword{color:#FF7AB2;}.hljs-template-variable{color:#DABAFF;}.hljs-string{color:#FF8170;}.hljs-name{color:#DABAFF;}.hljs-section{color:#6BDFFF;}.hljs-addition{color:#FF8170;}.hljs-quote{color:#7F8C98;}.hljs-regexp{color:#DABAFF;}.hljs-class{color:#6BDFFF;}.hljs-params{color:#ACF2E4;}.hljs-selector-id{color:#DABAFF;}.hljs-type{color:#ACF2E4;}.hljs-selector-class{color:#DABAFF;}.hljs-variable{color:#DABAFF;}.hljs-tag{color:#DABAFF;}.hljs-deletion{color:#DABAFF;}.hljs-selector-tag{color:#FF7AB2;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">display</span>&lt;T&gt;<span class="hljs-params">(data: T)</span></span> {
   <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Data:"</span>, data)
}
<span class="hljs-attribute">
display</span>(data: <span class="hljs-number">4</span>)<span class="hljs-attribute">
display</span>(data: <span class="hljs-string">"Javier"</span>)<span class="hljs-attribute">
display</span>(data: <span class="hljs-literal">true</span>)</code></pre>

As you can see the `T` is a placeholder type value, and when the function is called it is replaced by the parameter's type that is being used. We could have used other name for the type value rather than `T`, for instance `Element` if we want to refer an `Array<Element>`, but when there is no relationship between the parameter and the function it is very suitable to use capital letters like `T`, `U` or `V`.

But, does Swift only allows us to create generic fucntions? It is also possible to create generics like classes or structs that could work with any type?

<br>
<h3 style="color: #403F3F">Generic Types</h3>

Let's imagine that we would like to have just a single struct that could represent a bunch of different object types. Let's have a looj to the following example:

<style>.hljs-template-variable{color:#DABAFF;}.hljs-params{color:#ACF2E4;}.hljs-function{color:#6BDFFF;}.hljs-keyword{color:#FF7AB2;}.hljs-comment{color:#7F8C98;}.hljs-title{color:#6BDFFF;}.hljs-emphasis{font-style:italic;}.hljs-meta{color:#B281EB;}.hljs-selector-tag{color:#FF7AB2;}.hljs-builtin-name{color: #B281EB;}.hljs-selector-id{color:#DABAFF;}.hljs-number{color: #D9C97C;}.hljs-regexp{color:#DABAFF;}.hljs-strong{font-weight:bold;}.hljs-string{color:#FF8170;}.hljs-symbol{color:#FF8170;}.hljs-type{color:#ACF2E4;}.hljs-name{color:#DABAFF;}.hljs-link{color:#DABAFF;}.hljs-deletion{color:#DABAFF;}.hljs-section{color:#6BDFFF;}.hljs{padding:0.5em;color:#E0E0E0;display:block;}.hljs-literal{color: #B281EB;}.hljs-tag{color:#DABAFF;}.hljs-quote{color:#7F8C98;}.hljs-variable{color:#DABAFF;}.hljs-built_in{color: #B281EB;}.hljs-bullet{color:#FF8170;}.hljs-class{color:#6BDFFF;}.hljs-addition{color:#FF8170;}.hljs-attribute{color:#DABAFF;}.hljs-selector-class{color:#DABAFF;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">Container</span> </span>{
    <span class="hljs-keyword">var</span> container: [<span class="hljs-type">Int</span>] = []
    
    <span class="hljs-keyword">mutating</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">addNew</span><span class="hljs-params">(newItem: Int)</span></span> {
        container.<span class="hljs-attribute">append</span>(newItem)
    }
    
    <span class="hljs-keyword">mutating</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">removeLast</span><span class="hljs-params">()</span></span> {
        container.<span class="hljs-attribute">removeLast</span>()
    }
}</code></pre>

The `container` struct above is not very reusable, we can only use it with `Int` arrays. Swift let us refactor that struct as a generic one so it could be used with other types:

<style>.hljs-builtin-name{color: #B281EB;}.hljs-keyword{color:#FF7AB2;}.hljs-deletion{color:#DABAFF;}.hljs-selector-class{color:#DABAFF;}.hljs-attribute{color:#DABAFF;}.hljs-bullet{color:#FF8170;}.hljs-regexp{color:#DABAFF;}.hljs-strong{font-weight:bold;}.hljs-addition{color:#FF8170;}.hljs-comment{color:#7F8C98;}.hljs-class{color:#6BDFFF;}.hljs-tag{color:#DABAFF;}.hljs-params{color:#ACF2E4;}.hljs-quote{color:#7F8C98;}.hljs-name{color:#DABAFF;}.hljs-link{color:#DABAFF;}.hljs-selector-id{color:#DABAFF;}.hljs-emphasis{font-style:italic;}.hljs-built_in{color: #B281EB;}.hljs-literal{color: #B281EB;}.hljs-type{color:#ACF2E4;}.hljs-variable{color:#DABAFF;}.hljs-title{color:#6BDFFF;}.hljs-meta{color:#B281EB;}.hljs{color:#E0E0E0;display:block;padding:0.5em;}.hljs-template-variable{color:#DABAFF;}.hljs-string{color:#FF8170;}.hljs-number{color: #D9C97C;}.hljs-function{color:#6BDFFF;}.hljs-section{color:#6BDFFF;}.hljs-symbol{color:#FF8170;}.hljs-selector-tag{color:#FF7AB2;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">Containers</span>&lt;<span class="hljs-title">T</span>&gt; </span>{
    <span class="hljs-keyword">var</span> continer: [<span class="hljs-type">T</span>] = []
    
    <span class="hljs-keyword">mutating</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">addNew</span><span class="hljs-params">(newItem: T)</span></span> {
        continer.<span class="hljs-attribute">append</span>(newItem)
    }
    
    <span class="hljs-keyword">mutating</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">removeLast</span><span class="hljs-params">()</span></span> {
        continer.<span class="hljs-attribute">removeLast</span>()
    }
}</code></pre>

We have to bear in mind that when it comes to extend a generic type you don't define a parameter list in the extension definition. Let's make an extension of former example in order to clear this concept up:

<style>.hljs{color:#E0E0E0;display:block;padding:0.5em;}.hljs-string{color:#FF8170;}.hljs-comment{color:#7F8C98;}.hljs-variable{color:#DABAFF;}.hljs-class{color:#6BDFFF;}.hljs-bullet{color:#FF8170;}.hljs-number{color: #D9C97C;}.hljs-template-variable{color:#DABAFF;}.hljs-name{color:#DABAFF;}.hljs-title{color:#6BDFFF;}.hljs-symbol{color:#FF8170;}.hljs-literal{color: #B281EB;}.hljs-quote{color:#7F8C98;}.hljs-params{color:#ACF2E4;}.hljs-deletion{color:#DABAFF;}.hljs-link{color:#DABAFF;}.hljs-tag{color:#DABAFF;}.hljs-selector-class{color:#DABAFF;}.hljs-regexp{color:#DABAFF;}.hljs-meta{color:#B281EB;}.hljs-selector-id{color:#DABAFF;}.hljs-section{color:#6BDFFF;}.hljs-strong{font-weight:bold;}.hljs-attribute{color:#DABAFF;}.hljs-addition{color:#FF8170;}.hljs-type{color:#ACF2E4;}.hljs-function{color:#6BDFFF;}.hljs-builtin-name{color: #B281EB;}.hljs-built_in{color: #B281EB;}.hljs-emphasis{font-style:italic;}.hljs-keyword{color:#FF7AB2;}.hljs-selector-tag{color:#FF7AB2;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">extension</span> <span class="hljs-title">Container</span> </span>{
    <span class="hljs-keyword">mutating</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">search</span><span class="hljs-params">(itemToSearch: T)</span></span> -&gt; <span class="hljs-type">Bool</span> {
        <span class="hljs-keyword">let</span> found = continer.<span class="hljs-attribute">first</span> { element <span class="hljs-keyword">in</span>
            element == itemToSearch
        }
        <span class="hljs-keyword">if</span> found != <span class="hljs-literal">nil</span> {
            <span class="hljs-keyword">return</span> <span class="hljs-literal">true</span>
        } <span class="hljs-keyword">else</span> {
            <span class="hljs-keyword">return</span> <span class="hljs-literal">false</span>
        }
    }
}</code></pre>

<br>
<h3 style="color: #403F3F">Type constraints</h3>

As I have previously said, the generic code potential lies in the fact that it can be used all across the code allowing the programmer to use it without the need of defining new functions and classes, but sometimes we may want to restrict that reusability and limit it to just the types that inherit from an specific class or conform to any particular protocol.

For instance let's imagine that we want to definde a container just for *Equatable* types. Basically we just need to define the protocol or class been followed by the generic definition:

<style>.hljs-meta{color:#B281EB;}.hljs-symbol{color:#FF8170;}.hljs-tag{color:#DABAFF;}.hljs-selector-class{color:#DABAFF;}.hljs-built_in{color: #B281EB;}.hljs-name{color:#DABAFF;}.hljs-link{color:#DABAFF;}.hljs-class{color:#6BDFFF;}.hljs-bullet{color:#FF8170;}.hljs-strong{font-weight:bold;}.hljs-attribute{color:#DABAFF;}.hljs-template-variable{color:#DABAFF;}.hljs-literal{color: #B281EB;}.hljs-quote{color:#7F8C98;}.hljs{padding:0.5em;color:#E0E0E0;display:block;}.hljs-selector-tag{color:#FF7AB2;}.hljs-title{color:#6BDFFF;}.hljs-selector-id{color:#DABAFF;}.hljs-keyword{color:#FF7AB2;}.hljs-regexp{color:#DABAFF;}.hljs-number{color: #D9C97C;}.hljs-builtin-name{color: #B281EB;}.hljs-params{color:#ACF2E4;}.hljs-section{color:#6BDFFF;}.hljs-string{color:#FF8170;}.hljs-deletion{color:#DABAFF;}.hljs-type{color:#ACF2E4;}.hljs-function{color:#6BDFFF;}.hljs-addition{color:#FF8170;}.hljs-comment{color:#7F8C98;}.hljs-emphasis{font-style:italic;}.hljs-variable{color:#DABAFF;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">Container</span>&lt;<span class="hljs-title">T</span>: <span class="hljs-title">Equatable</span>&gt; </span>{
    <span class="hljs-keyword">var</span> continer: [<span class="hljs-type">T</span>] = []
    
    <span class="hljs-keyword">mutating</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">addNew</span><span class="hljs-params">(newItem: T)</span></span> {
        continer.<span class="hljs-attribute">append</span>(newItem)
    }
    
    <span class="hljs-keyword">mutating</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">removeLast</span><span class="hljs-params">()</span></span> {
        continer.<span class="hljs-attribute">removeLast</span>()
    }
}</code></pre>


<br>
<h3 style="color: #403F3F">Summing Up</h3>

- Swift generics code allows to build functions suitable for different sort of value types.
- With swift generic code not only you can customize functions that are able to work with all parameter types, but also allows you to create your own generic types like classes, structs and enums that could work with any type.
- When it comes to extend a generic type the parameter list should not appear in the extension definition.
- It is possible to restrict the generics just to types that inherit from an sepecific class or conform to any particular protocol.

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
            <p style="font-size: 18px">In the past I developed car's powertrain control software. Now I develope mobile apps for many purposes.</p>
        </td>
    </tr>
</table>




