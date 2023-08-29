---
layout: post
title: "@ViewBuilder"
date:   2023-08-28 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
<br>
<h3 style="color: #403F3F">Background</h3>

`@ViewBuilder` is one of the ***Result Builders*** available to be used within SwiftUI. A Result Builder collects different parts that get combined and offer a final result, and it's like an embedded DSL (Domain Specific Language). For instante, `VStack` is a Result Builder, it combines many `View` instances into a single one.

Indeed a `View` uses a Result Builder `@ViewBuilder` attribute: 

<style>.hljs-builtin-name{color: #B281EB;}.hljs-symbol{color:#FF8170;}.hljs-selector-class{color:#DABAFF;}.hljs-emphasis{font-style:italic;}.hljs-name{color:#DABAFF;}.hljs-strong{font-weight:bold;}.hljs-comment{color:#7F8C98;}.hljs-string{color:#FF8170;}.hljs{color:#E0E0E0;display:block;padding:0.5em;}.hljs-selector-tag{color:#FF7AB2;}.hljs-meta{color:#B281EB;}.hljs-title{color:#6BDFFF;}.hljs-built_in{color: #B281EB;}.hljs-literal{color: #B281EB;}.hljs-regexp{color:#DABAFF;}.hljs-function{color:#6BDFFF;}.hljs-variable{color:#DABAFF;}.hljs-type{color:#ACF2E4;}.hljs-link{color:#DABAFF;}.hljs-attribute{color:#DABAFF;}.hljs-class{color:#6BDFFF;}.hljs-template-variable{color:#DABAFF;}.hljs-addition{color:#FF8170;}.hljs-tag{color:#DABAFF;}.hljs-params{color:#ACF2E4;}.hljs-section{color:#6BDFFF;}.hljs-deletion{color:#DABAFF;}.hljs-bullet{color:#FF8170;}.hljs-selector-id{color:#DABAFF;}.hljs-number{color: #D9C97C;}.hljs-quote{color:#7F8C98;}.hljs-keyword{color:#FF7AB2;}</style>
<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">protocol</span> <span class="hljs-title">View</span> </span>{

    <span class="hljs-keyword">associatedtype</span> <span class="hljs-type">Body</span> : <span class="hljs-type">View</span>

    @<span class="hljs-type">ViewBuilder</span> @<span class="hljs-type">MainActor</span> <span class="hljs-keyword">var</span> body: <span class="hljs-type">Self</span>.<span class="hljs-attribute">Body</span> { <span class="hljs-keyword">get</span> }
}</code></pre>

So we can declare a `View` with more views inside without explicit return:

<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">ContentView</span>: <span class="hljs-title">View</span> </span>{
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">VStack</span> {
           <span class="hljs-attribute"> Image</span>(systemName: <span class="hljs-string">"globe"</span>)
                .<span class="hljs-literal">imageScale</span>(.<span class="hljs-attribute">large</span>)
                .<span class="hljs-literal">foregroundColor</span>(.<span class="hljs-attribute">accentColor</span>)
           <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"Hello, world!"</span>)
        }
        .<span class="hljs-literal">padding</span>()
    }
}</code></pre>

Let's imagine that `View` protocol is not suppported by a `@ViewBuilder` like this one I figured out:

<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">protocol</span> <span class="hljs-title">NonViewBuilderView</span> </span>{
    <span class="hljs-keyword">associatedtype</span> <span class="hljs-type">Body</span> : <span class="hljs-type">View</span>
    <span class="hljs-keyword">var</span> body: <span class="hljs-type">Self</span>.<span class="hljs-attribute">Body</span> { <span class="hljs-keyword">get</span> }
}</code></pre>

And we implement it like this:

<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">ContentView</span>: <span class="hljs-title">NonViewBuilderView</span> </span>{
    
    <span class="hljs-keyword">var</span> isLogged: <span class="hljs-type">Bool</span>
    
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-keyword">if</span> isLogged {
           <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"You are Logged"</span>)
        } <span class="hljs-keyword">else</span> {
           <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"Please log in"</span>)
        }
    }
}</code></pre>

We are going to get an error like this:

![NonViewBuilderViewImplementation](/assets/img/NonViewBuilderViewImplementation.png)

Because there's no returning element.

We could solve this by defining the returning type:

<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">ContentView</span>: <span class="hljs-title">NonViewBuilderView</span> </span>{
    
    <span class="hljs-keyword">var</span> isLogged: <span class="hljs-type">Bool</span>
    
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-keyword">if</span> isLogged {
            <span class="hljs-keyword">return</span><span class="hljs-attribute"> Text</span>(<span class="hljs-string">"You are Logged"</span>)
        } <span class="hljs-keyword">else</span> {
            <span class="hljs-keyword">return</span><span class="hljs-attribute"> Text</span>(<span class="hljs-string">"Please log in"</span>)
        }
    }
}</code></pre>

But doing this not very handy because we will need to define a return type each time its required. It's much better to use the `@ViewBuilder` :

<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">ContentView</span>: <span class="hljs-title">NonViewBuilderView</span> </span>{
    
    <span class="hljs-keyword">var</span> isLogged: <span class="hljs-type">Bool</span>
    
    @<span class="hljs-type">ViewBuilder</span>
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-keyword">if</span> isLogged {
           <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"You are Logged"</span>)
        } <span class="hljs-keyword">else</span> {
           <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"Please log in"</span>)
        }
    }
}</code></pre>

<br>
<h3 style="color: #403F3F">Use Case</h3>

<h4 style="color: #403F3F">Embedded View</h4>

When it comes to develope an app it is very suitable to have predefined elements that are used all across the app. Let's imagine that we want to create a view with a "card" style and with an embedded `View` :

<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">CardView</span>&lt;<span class="hljs-title">Content</span>: <span class="hljs-title">View</span>&gt;: <span class="hljs-title">View</span> </span>{
    
    <span class="hljs-keyword">let</span> embeddedView: <span class="hljs-type">Content</span>
    
   <span class="hljs-attribute"> init</span>(@<span class="hljs-type">ViewBuilder</span> embeddedView: () -&gt; <span class="hljs-type">Content</span>) {
        <span class="hljs-keyword">self</span>.<span class="hljs-attribute">embeddedView</span> =<span class="hljs-attribute"> embeddedView</span>()
    }
    
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">VStack</span> {
            embeddedView
                .<span class="hljs-literal">padding</span>(.<span class="hljs-attribute">vertical</span>)
        }
        .<span class="hljs-literal">frame</span>(maxWidth: .<span class="hljs-literal">infinity</span>)
        .<span class="hljs-literal">background</span>(<span class="hljs-type">Material</span>.<span class="hljs-attribute">ultraThin</span>)
        .<span class="hljs-literal">cornerRadius</span>(<span class="hljs-number">20</span>)
        .<span class="hljs-literal">padding</span>(.<span class="hljs-attribute">horizontal</span>)
    }
}</code></pre>

Now we can use that `CardView` as a container to show any other `View` inside.

<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-type">CardView</span> {
           <span class="hljs-attribute"> ContentView</span>(isLogged: <span class="hljs-literal">false</span>)
        }</code></pre>

<br>

![@ViewBuilder simulation](/assets/img/@ViewBuilder_simulation.png)

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




