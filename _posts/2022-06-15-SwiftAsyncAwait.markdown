---
layout: post
title: "Swift Concurrency: async/await"
date:   2022-05-13 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
As you may know, concurrency is a very interesting tool when it comes, for instante, to network calls. In that case we do not want to pause all the tasks that our app is running just because networks functions are running, mainly because that behaviour would freeze the user interface tasks that are updating the views and letting the user to keep flowing throught the app. So thats why it is better to perform all network functions as an asynchronous ones.

When it comes to Concurrency in Swift we are in reallity talking about asynchronous and parallel tasks. Asynchronous tasks are those that can be suspended and resumed when needed in order to let other tasks with a higher level of priority to be runned, and parallel tasks are those that are able to be executed ate the same time in through a multicore processor.

The main example of an asynchronous function is a closure one (callback). These sort of functions are really useful, but sometimes, when we use a closure inside another closure inside another closure inside... it turns out that it is very difficult to follow the code and to figure out what the heck is the real sequence of the process. Let's see a quick example of this:

<style>.hljs-link{color:#DABAFF;}.hljs-name{color:#DABAFF;}.hljs-selector-class{color:#DABAFF;}.hljs{display:block;padding:0.5em;color:#E0E0E0;}.hljs-selector-tag{color:#FF7AB2;}.hljs-meta{color:#B281EB;}.hljs-string{color:#FF8170;}.hljs-attribute{color:#DABAFF;}.hljs-symbol{color:#FF8170;}.hljs-emphasis{font-style:italic;}.hljs-class{color:#6BDFFF;}.hljs-addition{color:#FF8170;}.hljs-regexp{color:#DABAFF;}.hljs-params{color:#ACF2E4;}.hljs-built_in{color: #B281EB;}.hljs-deletion{color:#DABAFF;}.hljs-function{color:#6BDFFF;}.hljs-template-variable{color:#DABAFF;}.hljs-builtin-name{color: #B281EB;}.hljs-number{color: #D9C97C;}.hljs-section{color:#6BDFFF;}.hljs-variable{color:#DABAFF;}.hljs-literal{color: #B281EB;}.hljs-type{color:#ACF2E4;}.hljs-bullet{color:#FF8170;}.hljs-keyword{color:#FF7AB2;}.hljs-comment{color:#7F8C98;}.hljs-tag{color:#DABAFF;}.hljs-strong{font-weight:bold;}.hljs-selector-id{color:#DABAFF;}.hljs-quote{color:#7F8C98;}.hljs-title{color:#6BDFFF;}</style>

<pre style="background-color: #FDFDFD; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-keyword">import</span> UIKit

<span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">imageURLRequest</span><span class="hljs-params">(<span class="hljs-keyword">for</span> id: String)</span></span> -&gt; <span class="hljs-type">URLRequest</span> {
    <span class="hljs-keyword">let</span> url =<span class="hljs-attribute"> URL</span>(string: <span class="hljs-string">"url/"</span> + id)!
    <span class="hljs-keyword">let</span> request =<span class="hljs-attribute"> URLRequest</span>(url: url)
    <span class="hljs-comment">//URLRequest configuration</span>
    <span class="hljs-keyword">return</span> request
}

<span class="hljs-class"><span class="hljs-keyword">extension</span> <span class="hljs-title">UIImage</span> </span>{
    <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">prepareImage</span><span class="hljs-params">(of size: CGSize, completion: @escaping <span class="hljs-params">(UIImage?)</span></span></span> -&gt; <span class="hljs-type">Void</span>) {
        
    }
}

<span class="hljs-class"><span class="hljs-keyword">enum</span> <span class="hljs-title">FetchImageError</span>: <span class="hljs-title">Error</span> </span>{
    <span class="hljs-keyword">case</span> badID
    <span class="hljs-keyword">case</span> badImage
}

<span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">fetchImage</span><span class="hljs-params">(<span class="hljs-keyword">for</span> id: String, completion: @escaping <span class="hljs-params">(UIImage?, Error?)</span></span></span> -&gt; <span class="hljs-type">Void</span>) {
    <span class="hljs-keyword">let</span> request =<span class="hljs-attribute"> imageURLRequest</span>(<span class="hljs-keyword">for</span>: id)
    <span class="hljs-keyword">let</span> task = <span class="hljs-type">URLSession</span>.<span class="hljs-attribute">shared</span>.<span class="hljs-attribute">dataTask</span>(with: request) { data, response, error <span class="hljs-keyword">in</span>
        <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> error = error {
           <span class="hljs-attribute"> completion</span>(<span class="hljs-literal">nil</span>, error)
        } <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (response <span class="hljs-keyword">as</span>? <span class="hljs-type">HTTPURLResponse</span>)?.<span class="hljs-attribute">statusCode</span> != <span class="hljs-number">200</span> {
           <span class="hljs-attribute"> completion</span>(<span class="hljs-literal">nil</span>, <span class="hljs-type">FetchImageError</span>.<span class="hljs-attribute">badID</span>)
            <span class="hljs-keyword">return</span>
        } <span class="hljs-keyword">else</span> {
            <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> image =<span class="hljs-attribute"> UIImage</span>(data: data!) <span class="hljs-keyword">else</span> {
               <span class="hljs-attribute"> completion</span>(<span class="hljs-literal">nil</span>, <span class="hljs-type">FetchImageError</span>.<span class="hljs-attribute">badImage</span>)
                <span class="hljs-keyword">return</span>
            }
            image.<span class="hljs-attribute">prepareImage</span>(of:<span class="hljs-attribute"> CGSize</span>(width: <span class="hljs-number">100</span>, height: <span class="hljs-number">100</span>)) { image <span class="hljs-keyword">in</span>
                <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> image = image <span class="hljs-keyword">else</span> {
                   <span class="hljs-attribute"> completion</span>(<span class="hljs-literal">nil</span>, <span class="hljs-type">FetchImageError</span>.<span class="hljs-attribute">badImage</span>)
                    <span class="hljs-keyword">return</span>
                }
               <span class="hljs-attribute"> completion</span>(image, <span class="hljs-literal">nil</span>)
            }
        }
    }
    task.<span class="hljs-attribute">resume</span>()
}
</code></pre>


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




