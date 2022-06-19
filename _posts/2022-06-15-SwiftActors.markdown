---
layout: post
title: "Swift Concurrency II: Actors"
date:   2022-06-15 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
If you are new with Swift concurrency I strongly recommend you reading <a href="https://www.javiercarrilloblog.com/coding/15/04/2022/SwiftAsyncAwait.html">Swift Concurrency I: async/await</a> prior to this post. There you will find the basic concepts of swift concurrency and how to implement `async/await` so it will be much easier for you to understand more complex stuff like swift actors.

<br>
<h3 style="color: #403F3F">What's a Data Race?</h3>
Well a data race occurs when at least two executions (two separate threads) tries to access to the same data an at letas one of those executions is a "write" one. Let's see a quick example about how a data race could happen.

<style>.hljs-tag{color:#DABAFF;}.hljs-keyword{color:#FF7AB2;}.hljs-template-variable{color:#DABAFF;}.hljs-meta{color:#B281EB;}.hljs-link{color:#DABAFF;}.hljs-selector-class{color:#DABAFF;}.hljs-attribute{color:#DABAFF;}.hljs-regexp{color:#DABAFF;}.hljs-emphasis{font-style:italic;}.hljs-literal{color: #B281EB;}.hljs{padding:0.5em;display:block;color:#E0E0E0;}.hljs-variable{color:#DABAFF;}.hljs-string{color:#FF8170;}.hljs-selector-tag{color:#FF7AB2;}.hljs-symbol{color:#FF8170;}.hljs-name{color:#DABAFF;}.hljs-deletion{color:#DABAFF;}.hljs-built_in{color: #B281EB;}.hljs-class{color:#6BDFFF;}.hljs-title{color:#6BDFFF;}.hljs-number{color: #D9C97C;}.hljs-section{color:#6BDFFF;}.hljs-bullet{color:#FF8170;}.hljs-type{color:#ACF2E4;}.hljs-addition{color:#FF8170;}.hljs-builtin-name{color: #B281EB;}.hljs-quote{color:#7F8C98;}.hljs-strong{font-weight:bold;}.hljs-params{color:#ACF2E4;}.hljs-selector-id{color:#DABAFF;}.hljs-comment{color:#7F8C98;}.hljs-function{color:#6BDFFF;}</style>

<pre style="background-color: #FDFDFD; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Counter</span> </span>{
    
    <span class="hljs-keyword">var</span> value: <span class="hljs-type">Int</span> = <span class="hljs-number">0</span>
    
    <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">increment</span><span class="hljs-params">()</span></span> -&gt; <span class="hljs-type">Int</span> {
        value = value + <span class="hljs-number">1</span>
        <span class="hljs-keyword">return</span> value
    }
}

<span class="hljs-keyword">let</span> counter =<span class="hljs-attribute"> Counter</span>()

<span class="hljs-type">Task</span>.<span class="hljs-attribute">detached</span> { <span class="hljs-comment">// Task 1</span>
   <span class="hljs-attribute"> print</span>(counter.<span class="hljs-attribute">increment</span>())
}

<span class="hljs-type">Task</span>.<span class="hljs-attribute">detached</span> { <span class="hljs-comment">// Task 2</span>
   <span class="hljs-attribute"> print</span>(counter.<span class="hljs-attribute">increment</span>())
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




