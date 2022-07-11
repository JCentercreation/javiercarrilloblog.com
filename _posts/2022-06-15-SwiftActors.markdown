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
If you are new with Swift concurrency I strongly recommend you reading <a href="https://www.javiercarrilloblog.com/coding/15/04/2022/SwiftAsyncAwait.html">**Swift Concurrency I: Async/Await**</a> prior to this post. There you will find the basic concepts of swift concurrency and how to implement `async/await` so it will be much easier for you to understand more complex stuff like swift actors.

<br>
<h3 style="color: #403F3F">What's a Data Race?</h3>
Well a data race occurs when at least two executions (two separate threads) tries to access to the same data an at letas one of those executions is a "write" one. Let's see a quick example about how a data race could happen.

<style>.hljs-tag{color:#DABAFF;}.hljs-keyword{color:#FF7AB2;}.hljs-template-variable{color:#DABAFF;}.hljs-meta{color:#B281EB;}.hljs-link{color:#DABAFF;}.hljs-selector-class{color:#DABAFF;}.hljs-attribute{color:#DABAFF;}.hljs-regexp{color:#DABAFF;}.hljs-emphasis{font-style:italic;}.hljs-literal{color: #B281EB;}.hljs{padding:0.5em;display:block;color:#E0E0E0;}.hljs-variable{color:#DABAFF;}.hljs-string{color:#FF8170;}.hljs-selector-tag{color:#FF7AB2;}.hljs-symbol{color:#FF8170;}.hljs-name{color:#DABAFF;}.hljs-deletion{color:#DABAFF;}.hljs-built_in{color: #B281EB;}.hljs-class{color:#6BDFFF;}.hljs-title{color:#6BDFFF;}.hljs-number{color: #D9C97C;}.hljs-section{color:#6BDFFF;}.hljs-bullet{color:#FF8170;}.hljs-type{color:#ACF2E4;}.hljs-addition{color:#FF8170;}.hljs-builtin-name{color: #B281EB;}.hljs-quote{color:#7F8C98;}.hljs-strong{font-weight:bold;}.hljs-params{color:#ACF2E4;}.hljs-selector-id{color:#DABAFF;}.hljs-comment{color:#7F8C98;}.hljs-function{color:#6BDFFF;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Counter</span> </span>{
    
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

You might thing that Task1 will return and 1 and Task2 will return a 2, but it really depends on the execution timing, which we are not able to figure it out because the scheduler change the order of concurrent tasks each time we run the program. It is likely that Task1 would return a 2 and Task would return a 1, or even both task returns a 1 or a 2. This is a data race.

<br>
<h3 style="color: #403F3F">How to avoid data races</h3>
The first way to solve the data race issue we could come up with is the use of value semantics. We know that classes are reference types while structs are value types, so lets take advantages of value types.

<style>.hljs-built_in{color: #B281EB;}.hljs-tag{color:#DABAFF;}.hljs-params{color:#ACF2E4;}.hljs-emphasis{font-style:italic;}.hljs-deletion{color:#DABAFF;}.hljs-variable{color:#DABAFF;}.hljs-number{color: #D9C97C;}.hljs-literal{color: #B281EB;}.hljs-bullet{color:#FF8170;}.hljs-regexp{color:#DABAFF;}.hljs-symbol{color:#FF8170;}.hljs-addition{color:#FF8170;}.hljs{padding:0.5em;color:#E0E0E0;display:block;}.hljs-title{color:#6BDFFF;}.hljs-section{color:#6BDFFF;}.hljs-selector-class{color:#DABAFF;}.hljs-name{color:#DABAFF;}.hljs-selector-tag{color:#FF7AB2;}.hljs-selector-id{color:#DABAFF;}.hljs-function{color:#6BDFFF;}.hljs-strong{font-weight:bold;}.hljs-type{color:#ACF2E4;}.hljs-template-variable{color:#DABAFF;}.hljs-quote{color:#7F8C98;}.hljs-class{color:#6BDFFF;}.hljs-meta{color:#B281EB;}.hljs-attribute{color:#DABAFF;}.hljs-comment{color:#7F8C98;}.hljs-link{color:#DABAFF;}.hljs-keyword{color:#FF7AB2;}.hljs-string{color:#FF8170;}.hljs-builtin-name{color: #B281EB;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">Counter2</span> </span>{
    <span class="hljs-keyword">var</span> value: <span class="hljs-type">Int</span> = <span class="hljs-number">0</span>
    
    <span class="hljs-keyword">mutating</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">increment</span><span class="hljs-params">()</span></span> -&gt; <span class="hljs-type">Int</span> {
        value = value + <span class="hljs-number">1</span>
        <span class="hljs-keyword">return</span> value
    }
}

<span class="hljs-keyword">let</span> counter2 =<span class="hljs-attribute"> Counter2</span>()
<span class="hljs-type">Task</span>.<span class="hljs-attribute">detached</span> { <span class="hljs-comment">// Task 1</span>
    <span class="hljs-keyword">var</span> counter = counter2
   <span class="hljs-attribute"> print</span>(counter.<span class="hljs-attribute">increment</span>())
}

<span class="hljs-type">Task</span>.<span class="hljs-attribute">detached</span> { <span class="hljs-comment">// Task 2</span>
    <span class="hljs-keyword">var</span> counter = counter2
   <span class="hljs-attribute"> print</span>(counter.<span class="hljs-attribute">increment</span>())
}
</code></pre>

The code above solves our problem, but what if we need to share the mutable state between different executions? Definitely we need a tool that allows us to synchronize those mutable states. This is when I really would like to introduce Swift Actors to you.

<br>
<h3 style="color: #403F3F">Actors</h3>
Before Actores show up in Swift, there were primitive tools for sharing mutable states, like Atomics, Locks and Serial Dispatch Queues, but they all have the same problem: they hard to implement without not making any syntax mistake that leads to a data race.

Actors are much easier to implement that primitives tools, having the following main characteristis:
- Actors provide synchronization for shared mutable states.
- Actors isolate their state from the rest of the program:
    - All access to the state goes through the actor.
    - The actor ensures mutually-exclusive access to its state. This means that no other execution can access the actor state.

But what really is an Actor? Well is another type in swift, and brings all the capabilities as all of the named types, having methods, properties, initializers, the can conform to protocols and can be augmented with extensions. **Actors are reference types**, like classes. What an actor does is first to isolate the data from the rest of the program and then ensure synchronized access to that data. Lets see how to implement actors in our example:

<style>.hljs-built_in{color: #B281EB;}.hljs-tag{color:#DABAFF;}.hljs-selector-class{color:#DABAFF;}.hljs-variable{color:#DABAFF;}.hljs-builtin-name{color: #B281EB;}.hljs-function{color:#6BDFFF;}.hljs-emphasis{font-style:italic;}.hljs-literal{color: #B281EB;}.hljs-class{color:#6BDFFF;}.hljs-title{color:#6BDFFF;}.hljs-deletion{color:#DABAFF;}.hljs-number{color: #D9C97C;}.hljs-selector-id{color:#DABAFF;}.hljs-selector-tag{color:#FF7AB2;}.hljs-type{color:#ACF2E4;}.hljs-template-variable{color:#DABAFF;}.hljs-quote{color:#7F8C98;}.hljs-meta{color:#B281EB;}.hljs-params{color:#ACF2E4;}.hljs-strong{font-weight:bold;}.hljs-symbol{color:#FF8170;}.hljs-attribute{color:#DABAFF;}.hljs-regexp{color:#DABAFF;}.hljs{padding:0.5em;display:block;color:#E0E0E0;}.hljs-addition{color:#FF8170;}.hljs-comment{color:#7F8C98;}.hljs-name{color:#DABAFF;}.hljs-bullet{color:#FF8170;}.hljs-link{color:#DABAFF;}.hljs-keyword{color:#FF7AB2;}.hljs-string{color:#FF8170;}.hljs-section{color:#6BDFFF;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px">actor <span class="hljs-type">Counter</span> {
    <span class="hljs-keyword">var</span> value = <span class="hljs-number">0</span>
    
    <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">increment</span><span class="hljs-params">()</span></span> -&gt; <span class="hljs-type">Int</span> {
        value = value + <span class="hljs-number">1</span>
        <span class="hljs-keyword">return</span> value
    }
    
}

<span class="hljs-keyword">let</span> counter =<span class="hljs-attribute"> Counter</span>()
<span class="hljs-type">Task</span>.<span class="hljs-attribute">detached</span> { <span class="hljs-comment">// Task 1</span>
   <span class="hljs-attribute"> print</span>(<span class="hljs-keyword">await</span> counter.<span class="hljs-attribute">increment</span>())
}

<span class="hljs-type">Task</span>.<span class="hljs-attribute">detached</span> { <span class="hljs-comment">// Task 2</span>
   <span class="hljs-attribute"> print</span>(<span class="hljs-keyword">await</span> counter.<span class="hljs-attribute">increment</span>())
}</code></pre>

The `await` keyword indicates that the asynchronous call to the actor might involve a suspension. This is really important to bear in mind, because despite the fact that by using actors our code will not be involving a data race, it could potentially have a behaviour bug if we do not take care about what is after the `await`.

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




