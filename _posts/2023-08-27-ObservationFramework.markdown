---
layout: post
title: "Observation Framework"
date:   2023-08-27 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
<br>
<h3 style="color: #403F3F">Background</h3>

One of the main advantages that SwiftUI brought since it showed up for the first time is that data is a **source of truth**. This means SwuiftUI would adapt to data changes automatically, following a customized **oberver design pattern**. For that purpose it was necessary to use `ObservableObject` protocol, `@Published`, `@StateObject` or `@EnvironmentObject`.

<h3 style="color: #403F3F">Observation</h3>

But now it is much easier to define and implement the observer design pattern objects with new ***Observation framework*** for iOS 17.

With this new framework we just need to define the model with the new `@Observable` macro:
<style>.hljs-selector-id{color:#DABAFF;}.hljs-regexp{color:#DABAFF;}.hljs-params{color:#ACF2E4;}.hljs-selector-tag{color:#FF7AB2;}.hljs-function{color:#6BDFFF;}.hljs-bullet{color:#FF8170;}.hljs-link{color:#DABAFF;}.hljs-title{color:#6BDFFF;}.hljs-deletion{color:#DABAFF;}.hljs-class{color:#6BDFFF;}.hljs-builtin-name{color: #B281EB;}.hljs-keyword{color:#FF7AB2;}.hljs-built_in{color: #B281EB;}.hljs{display:block;color:#E0E0E0;padding:0.5em;}.hljs-tag{color:#DABAFF;}.hljs-name{color:#DABAFF;}.hljs-section{color:#6BDFFF;}.hljs-addition{color:#FF8170;}.hljs-selector-class{color:#DABAFF;}.hljs-number{color: #D9C97C;}.hljs-type{color:#ACF2E4;}.hljs-string{color:#FF8170;}.hljs-meta{color:#B281EB;}.hljs-literal{color: #B281EB;}.hljs-attribute{color:#DABAFF;}.hljs-strong{font-weight:bold;}.hljs-comment{color:#7F8C98;}.hljs-variable{color:#DABAFF;}.hljs-emphasis{font-style:italic;}.hljs-template-variable{color:#DABAFF;}.hljs-symbol{color:#FF8170;}.hljs-quote{color:#7F8C98;}</style>
<pre><code class="hljs" style="background:#292A30;border-radius:8px">@<span class="hljs-type">Observable</span>
<span class="hljs-keyword">final</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">User</span> </span>{
    <span class="hljs-keyword">var</span> name: <span class="hljs-type">String</span>
    <span class="hljs-keyword">var</span> age: <span class="hljs-type">Int</span>
    
   <span class="hljs-attribute"> init</span>(name: <span class="hljs-type">String</span>, age: <span class="hljs-type">Int</span>) {
        <span class="hljs-keyword">self</span>.<span class="hljs-attribute">name</span> = name
        <span class="hljs-keyword">self</span>.<span class="hljs-attribute">age</span> = age
    }
}</code></pre>

Now that the observable object is defined we could implement it by two different means.

<h4 style="color: #403F3F">State property</h4>

In case the observed object os only used in just one view we could instance it as an `@State` property of the view:
<pre><code class="hljs" style="background:#292A30;border-radius:8px">@main
<span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">ObservationFrameworkApp</span>: <span class="hljs-title">App</span> </span>{
    
    @<span class="hljs-type">State</span> <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> user =<span class="hljs-attribute"> User</span>(name: <span class="hljs-string">"Pepe"</span>, age: <span class="hljs-number">29</span>)
    
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">Scene</span> {
        <span class="hljs-type">WindowGroup</span> {
           <span class="hljs-attribute"> ContentView</span>(user: user)
        }
    }
}</code></pre>

<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">ContentView</span>: <span class="hljs-title">View</span> </span>{
    
    <span class="hljs-keyword">var</span> user: <span class="hljs-type">User</span>
    
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">VStack</span> {
            readStack
            updateStack
        }
    }
    
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> readStack: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">VStack</span> {
           <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"\(user.name)"</span>)
           <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"\(user.age)"</span>)
        }
        .<span class="hljs-literal">padding</span>()
    }
    
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> updateStack: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">HStack</span> {
           <span class="hljs-attribute"> Button</span>(action: {
                user.<span class="hljs-attribute">age</span> += <span class="hljs-number">1</span>
            }, label: {
               <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"+"</span>)
            }).<span class="hljs-attribute">padding</span>(.<span class="hljs-attribute">horizontal</span>)
           <span class="hljs-attribute"> Button</span>(action: {
                user.<span class="hljs-attribute">age</span> -= <span class="hljs-number">1</span>
            }, label: {
               <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"-"</span>)
            }).<span class="hljs-attribute">padding</span>(.<span class="hljs-attribute">horizontal</span>)
        }.<span class="hljs-attribute">font</span>(.<span class="hljs-attribute">title</span>)
    }
}</code></pre>

<h4 style="color: #403F3F">Environment property</h4>

But oftentimes or apps view hierarchy is much more complicated that a single view, so it is much more suitable to deal with the observable object as an environment object for the app:

<pre><code class="hljs" style="background:#292A30;border-radius:8px">@main
<span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">ObservationFrameworkApp</span>: <span class="hljs-title">App</span> </span>{
    
    @<span class="hljs-type">State</span> <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> user =<span class="hljs-attribute"> User</span>(name: <span class="hljs-string">"Pepe"</span>, age: <span class="hljs-number">29</span>)
    
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">Scene</span> {
        <span class="hljs-type">WindowGroup</span> {
           <span class="hljs-attribute"> ContentView</span>()
                .<span class="hljs-literal">environment</span>(user)
        }
    }
}</code></pre>

<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">ContentView</span>: <span class="hljs-title">View</span> </span>{
    
    @<span class="hljs-type">Environment</span>(<span class="hljs-type">User</span>.<span class="hljs-attribute">self</span>) <span class="hljs-keyword">var</span> user: <span class="hljs-type">User</span>
    
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">VStack</span> {
            readStack
            updateStack
        }
    }
    
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> readStack: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">VStack</span> {
           <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"\(user.name)"</span>)
           <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"\(user.age)"</span>)
        }
        .<span class="hljs-literal">padding</span>()
    }
    
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> updateStack: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">HStack</span> {
           <span class="hljs-attribute"> Button</span>(action: {
                user.<span class="hljs-attribute">age</span> += <span class="hljs-number">1</span>
            }, label: {
               <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"+"</span>)
            }).<span class="hljs-attribute">padding</span>(.<span class="hljs-attribute">horizontal</span>)
           <span class="hljs-attribute"> Button</span>(action: {
                user.<span class="hljs-attribute">age</span> -= <span class="hljs-number">1</span>
            }, label: {
               <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"-"</span>)
            }).<span class="hljs-attribute">padding</span>(.<span class="hljs-attribute">horizontal</span>)
        }.<span class="hljs-attribute">font</span>(.<span class="hljs-attribute">title</span>)
    }
}</code></pre>

<h3 style="color: #403F3F">Bonus</h3>

Setting an environment property by instancing it directly in the view is very straightforward but maybe is not the smartest way to do it. You may have already used some `EnvironmentKey` objects like `\.dismiss` or `\.colorScheme` so... why not creating a customized one for our user environment object?
<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">CustomUserKey</span>: <span class="hljs-title">EnvironmentKey</span> </span>{
    <span class="hljs-keyword">static</span> <span class="hljs-keyword">var</span> defaultValue =<span class="hljs-attribute"> User</span>(name: <span class="hljs-string">""</span>, age: <span class="hljs-number">0</span>)
}

<span class="hljs-class"><span class="hljs-keyword">extension</span> <span class="hljs-title">EnvironmentValues</span> </span>{
    <span class="hljs-keyword">var</span> user: <span class="hljs-type">User</span> {
        <span class="hljs-keyword">get</span> { <span class="hljs-keyword">self</span>[<span class="hljs-type">CustomUserKey</span>.<span class="hljs-attribute">self</span>] }
        <span class="hljs-keyword">set</span> { <span class="hljs-keyword">self</span>[<span class="hljs-type">CustomUserKey</span>.<span class="hljs-attribute">self</span>] = newValue }
    }
}</code></pre>

<pre><code class="hljs" style="background:#292A30;border-radius:8px">@main
<span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">ObservationFrameworkApp</span>: <span class="hljs-title">App</span> </span>{
    
    @<span class="hljs-type">State</span> <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> user =<span class="hljs-attribute"> User</span>(name: <span class="hljs-string">"Pepe"</span>, age: <span class="hljs-number">29</span>)
    
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">Scene</span> {
        <span class="hljs-type">WindowGroup</span> {
           <span class="hljs-attribute"> ContentView</span>()
                .<span class="hljs-literal">environment</span>(\.<span class="hljs-attribute">user</span> ,user)
        }
    }
}</code></pre>

<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">ContentView</span>: <span class="hljs-title">View</span> </span>{
    
    @<span class="hljs-type">Environment</span>(\.<span class="hljs-attribute">user</span>) <span class="hljs-keyword">var</span> user: <span class="hljs-type">User</span>
    
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">NavigationStack</span> {
            readStack
            updateStack
            <span class="hljs-type">NavigationLink</span> {
               <span class="hljs-attribute"> SecondView</span>(user: user)
            } label: {
               <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"Go to Second View"</span>)
            }

        }
    }
    
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> readStack: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">VStack</span> {
           <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"\(user.name)"</span>)
           <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"\(user.age)"</span>)
        }
        .<span class="hljs-literal">padding</span>()
    }
    
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> updateStack: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">HStack</span> {
           <span class="hljs-attribute"> Button</span>(action: {
                user.<span class="hljs-attribute">age</span> += <span class="hljs-number">1</span>
            }, label: {
               <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"+"</span>)
            }).<span class="hljs-attribute">padding</span>(.<span class="hljs-attribute">horizontal</span>)
           <span class="hljs-attribute"> Button</span>(action: {
                user.<span class="hljs-attribute">age</span> -= <span class="hljs-number">1</span>
            }, label: {
               <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"-"</span>)
            }).<span class="hljs-attribute">padding</span>(.<span class="hljs-attribute">horizontal</span>)
        }.<span class="hljs-attribute">font</span>(.<span class="hljs-attribute">title</span>)
    }
}</code></pre>

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




