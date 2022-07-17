---
layout: post
title: "Memory leaks and how to avoid them"
date:   2022-07-17 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
In previous posts we have gone throught basics concepts about asynchrony and concurrency using Async/Await. If you are new with this topic please head up to <a href="https://www.javiercarrilloblog.com/coding/15/04/2022/SwiftAsyncAwait.html">**Swift Concurrency I: Async/Await**</a> and <a href="https://www.javiercarrilloblog.com/coding/15/06/2022/SwiftActors.html">**Swift Concurrency II: Actors**</a> so you can have the basic knowledge prior to reading this article.

<br>
<h3 style="color: #403F3F">Calling Async methods from a function that does not support concurrency</h3>
As we saw an saynchronous function that is supended it also suspends its caller too, that is why the callen must be asynchronous as well.

<img style="display: block; margin-left: auto; margin-right: auto; width: 100%; object-fit: contain; border-radius: 8px" src="/assets/img/asyncError.png">

We could solve this issue by making the caller an asynchronous one, but probably we can not keep doing this forever along our code so at the end of the day we will need to call an asynchronous function from a non asynchronous one. Here is where `Tasks` appear.

Basically a `Task` is a piece of code that can be run asynchronously. It can start running inmediatly without starting or scheduling it. Nevertheless you can use its reference to pause or cancel it, in case you want to reference it.

Let's see how we can take advantages of task in oreder to run an asynchronous code inside a non asynchronous function:

<style>.hljs-class{color:#6BDFFF;}.hljs-link{color:#DABAFF;}.hljs-keyword{color:#FF7AB2;}.hljs{display:block;color:#E0E0E0;padding:0.5em;}.hljs-number{color: #D9C97C;}.hljs-attribute{color:#DABAFF;}.hljs-emphasis{font-style:italic;}.hljs-name{color:#DABAFF;}.hljs-selector-id{color:#DABAFF;}.hljs-selector-class{color:#DABAFF;}.hljs-literal{color: #B281EB;}.hljs-comment{color:#7F8C98;}.hljs-selector-tag{color:#FF7AB2;}.hljs-variable{color:#DABAFF;}.hljs-template-variable{color:#DABAFF;}.hljs-builtin-name{color: #B281EB;}.hljs-string{color:#FF8170;}.hljs-addition{color:#FF8170;}.hljs-function{color:#6BDFFF;}.hljs-section{color:#6BDFFF;}.hljs-quote{color:#7F8C98;}.hljs-regexp{color:#DABAFF;}.hljs-params{color:#ACF2E4;}.hljs-tag{color:#DABAFF;}.hljs-deletion{color:#DABAFF;}.hljs-title{color:#6BDFFF;}.hljs-symbol{color:#FF8170;}.hljs-type{color:#ACF2E4;}.hljs-bullet{color:#FF8170;}.hljs-built_in{color: #B281EB;}.hljs-meta{color:#B281EB;}.hljs-strong{font-weight:bold;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">fetchImageAsyncAwait</span><span class="hljs-params">(<span class="hljs-keyword">for</span> id: String)</span></span> <span class="hljs-keyword">async</span> <span class="hljs-keyword">throws</span> -&gt; <span class="hljs-type">UIImage</span> {
    <span class="hljs-keyword">let</span> request =<span class="hljs-attribute"> imageURLRequest</span>(<span class="hljs-keyword">for</span>: id)
    <span class="hljs-keyword">let</span> (data, response) = <span class="hljs-keyword">try</span> <span class="hljs-keyword">await</span> <span class="hljs-type">URLSession</span>.<span class="hljs-attribute">shared</span>.<span class="hljs-attribute">data</span>(<span class="hljs-keyword">for</span>: request)
    <span class="hljs-keyword">guard</span> (response <span class="hljs-keyword">as</span>? <span class="hljs-type">HTTPURLResponse</span>)?.<span class="hljs-attribute">statusCode</span> == <span class="hljs-number">200</span> <span class="hljs-keyword">else</span> { <span class="hljs-keyword">throw</span> <span class="hljs-type">FetchImageError</span>.<span class="hljs-attribute">badID</span> }
    <span class="hljs-keyword">let</span> maybeImage =<span class="hljs-attribute"> UIImage</span>(data: data)
    <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> image = <span class="hljs-keyword">await</span> maybeImage?.<span class="hljs-attribute">prepareImageAsyncAwait</span>() <span class="hljs-keyword">else</span> { <span class="hljs-keyword">throw</span> <span class="hljs-type">FetchImageError</span>.<span class="hljs-attribute">badImage</span> }
    <span class="hljs-keyword">return</span> image
}

<span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">fetchImages</span><span class="hljs-params">()</span></span> {
    <span class="hljs-type">Task</span>.<span class="hljs-attribute">init</span> {
        <span class="hljs-keyword">do</span> {
            <span class="hljs-keyword">try</span> <span class="hljs-keyword">await</span><span class="hljs-attribute"> fetchImageAsyncAwait</span>(<span class="hljs-keyword">for</span>: <span class="hljs-string">"test"</span>)
        } <span class="hljs-keyword">catch</span> {
           <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Error: \(error)"</span>)
        }
    }
}</code></pre>

`Tasks` are a really powerful tool that you can manage in different ways, you can cancel it, put it to sleep during nanoseconds, accessing the result value, set a priority for it, etc.

Thanks for reading :)

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




