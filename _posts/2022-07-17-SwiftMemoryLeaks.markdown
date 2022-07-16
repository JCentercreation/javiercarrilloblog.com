---
layout: post
title: "Memory leakages and how to avoid them"
date:   2022-07-17 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
When our code instantiates an entitie that entitie is saved into a memory space until all the references inside the code to that entitie are removed. But sometimes the references to the entitie are destroyed prior the Swift's memory manager (ARC) is able to remove the entitie from memory. So at the end of the day, **a memory leak is memory that was allocated at some point but never released, and since there is no reference to it there is no way to free it so it can be used in the future.**

Memory leaks not only increase our app memory footprint, something that our app and the device is running on could cope with despite the fact is not optimum, but they also could lead to crashes, which is critical. And, despite the fact that our code could lead into a memory leakage thanks to third party framework which has not been optimized, it is very likely that the memory leaks appear thank to the programmer and the implementation of retain cycles. In order to understand what a retain cycle is and how to avoid them, is mandatory to know how the Swift's memory manager works.

<br>
<h3 style="color: #403F3F">Understanding ARC</h3>

ARC stands for *Automatic Reference Counting*. As you may imagine, *Reference* reffers to reference types like instances of classes, so value types like structures and enumerations are out of ARC purposes. Its main duty is to ensure that all the classes that are still in use by the app are still in memory and not to deallocate an instance of a class that already is reference by another property, variablen or constant. You can imagine what would happen if the instance of class that is being targeted by, for instance, a variable, is not in memory. Yes, you are right, the app will crash. This sort of reference between the property, variable or constant and the class instance is called **strong reference**. So ARC does not deallocate a class instance while there's sill a strong reference to it.

Detecting an strong reference is very straightforward, we now know that this is a property, variable or constarnt instancing a class. But sometimes inside a class there's a strong reference to another class and also the latter has a strong reference to the former. This is called a **strong reference cycle**. Let's have a look to this example:

<style>.hljs-built_in{color: #B281EB;}.hljs-deletion{color:#DABAFF;}.hljs-comment{color:#7F8C98;}.hljs-symbol{color:#FF8170;}.hljs-function{color:#6BDFFF;}.hljs-selector-tag{color:#FF7AB2;}.hljs-template-variable{color:#DABAFF;}.hljs-string{color:#FF8170;}.hljs-tag{color:#DABAFF;}.hljs-bullet{color:#FF8170;}.hljs-type{color:#ACF2E4;}.hljs-emphasis{font-style:italic;}.hljs-selector-class{color:#DABAFF;}.hljs{display:block;padding:0.5em;color:#E0E0E0;}.hljs-params{color:#ACF2E4;}.hljs-variable{color:#DABAFF;}.hljs-selector-id{color:#DABAFF;}.hljs-name{color:#DABAFF;}.hljs-attribute{color:#DABAFF;}.hljs-section{color:#6BDFFF;}.hljs-strong{font-weight:bold;}.hljs-number{color: #D9C97C;}.hljs-addition{color:#FF8170;}.hljs-quote{color:#7F8C98;}.hljs-literal{color: #B281EB;}.hljs-builtin-name{color: #B281EB;}.hljs-link{color:#DABAFF;}.hljs-class{color:#6BDFFF;}.hljs-keyword{color:#FF7AB2;}.hljs-regexp{color:#DABAFF;}.hljs-meta{color:#B281EB;}.hljs-title{color:#6BDFFF;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Person</span> </span>{
    <span class="hljs-keyword">var</span> name: <span class="hljs-type">String</span>
    <span class="hljs-keyword">var</span> pet: <span class="hljs-type">Pet?</span>
    
   <span class="hljs-attribute"> init</span>(name: <span class="hljs-type">String</span>) {
        <span class="hljs-keyword">self</span>.<span class="hljs-attribute">name</span> = name
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Person class initialized"</span>)
    }
    
    <span class="hljs-keyword">deinit</span> {
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Person class deinitialized"</span>)
    }
}

<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Pet</span> </span>{
    <span class="hljs-keyword">var</span> petName: <span class="hljs-type">String</span>
    <span class="hljs-keyword">var</span> owner: <span class="hljs-type">Person?</span>
    
   <span class="hljs-attribute"> init</span>(petName: <span class="hljs-type">String</span>) {
        <span class="hljs-keyword">self</span>.<span class="hljs-attribute">petName</span> = petName
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Pet class initialized"</span>)
    }
    
    <span class="hljs-keyword">deinit</span> {
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Pet class deinitialized"</span>)
    }
}

<span class="hljs-keyword">var</span> javi: <span class="hljs-type">Person?</span>
<span class="hljs-keyword">var</span> tobby: <span class="hljs-type">Pet?</span>

javi =<span class="hljs-attribute"> Person</span>(name: <span class="hljs-string">"Javi"</span>)
tobby =<span class="hljs-attribute"> Pet</span>(petName: <span class="hljs-string">"Tobby"</span>)

javi!.<span class="hljs-attribute">pet</span> = tobby
tobby!.<span class="hljs-attribute">owner</span> = javi

javi = <span class="hljs-literal">nil</span>
tobby = <span class="hljs-literal">nil</span>

</code></pre>

This is what we will get in the logcat:

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 8px" src="/assets/img/PersonPetOutput.png"></h1>

This means that *Person* and *Pet* classes has not being deinitialized. Why? Because there is a stronge reference cyclo between these two classes. In the next chapter we will go throught the two main solutions that Swift programming language offers to save this situation.

<br>
<h3 style="color: #403F3F">Avoiding memory leaks</h3>

Once we understand what's a memory leak and how they can be generated, now is time to fix them. And the best wayy to fix them is too avoid them by making no strong references between classes. For this purpose Swift brings the possibility to built a `weak` and `unowned` references. As official Swift documentation suggest, it is highly recommended to use `weak` when the reference has a shorter lifetime and `unowned` when the reference has the same lifetime or longer. In our example it is much likely that a Person may not have a Pet so we would use here a `weak` reference, while it is unlikely that a Pet has no owner so we would use `unowned`.

Let's see how `weak` reference works in our example:

<style>.hljs-comment{color:#7F8C98;}.hljs-string{color:#FF8170;}.hljs-class{color:#6BDFFF;}.hljs-number{color: #D9C97C;}.hljs-template-variable{color:#DABAFF;}.hljs-attribute{color:#DABAFF;}.hljs-title{color:#6BDFFF;}.hljs-function{color:#6BDFFF;}.hljs-name{color:#DABAFF;}.hljs-section{color:#6BDFFF;}.hljs-params{color:#ACF2E4;}.hljs-selector-tag{color:#FF7AB2;}.hljs-builtin-name{color: #B281EB;}.hljs-regexp{color:#DABAFF;}.hljs-emphasis{font-style:italic;}.hljs-keyword{color:#FF7AB2;}.hljs-variable{color:#DABAFF;}.hljs-tag{color:#DABAFF;}.hljs-bullet{color:#FF8170;}.hljs-addition{color:#FF8170;}.hljs-built_in{color: #B281EB;}.hljs-symbol{color:#FF8170;}.hljs{padding:0.5em;display:block;color:#E0E0E0;}.hljs-meta{color:#B281EB;}.hljs-strong{font-weight:bold;}.hljs-selector-id{color:#DABAFF;}.hljs-type{color:#ACF2E4;}.hljs-deletion{color:#DABAFF;}.hljs-literal{color: #B281EB;}.hljs-link{color:#DABAFF;}.hljs-quote{color:#7F8C98;}.hljs-selector-class{color:#DABAFF;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Person</span> </span>{
    <span class="hljs-keyword">var</span> name: <span class="hljs-type">String</span>
    <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> pet: <span class="hljs-type">Pet?</span>
    
   <span class="hljs-attribute"> init</span>(name: <span class="hljs-type">String</span>) {
        <span class="hljs-keyword">self</span>.<span class="hljs-attribute">name</span> = name
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Person class initialized"</span>)
    }
    
    <span class="hljs-keyword">deinit</span> {
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Person class deinitialized"</span>)
    }
}

<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Pet</span> </span>{
    <span class="hljs-keyword">var</span> petName: <span class="hljs-type">String</span>
    <span class="hljs-keyword">var</span> owner: <span class="hljs-type">Person?</span>
    
   <span class="hljs-attribute"> init</span>(petName: <span class="hljs-type">String</span>) {
        <span class="hljs-keyword">self</span>.<span class="hljs-attribute">petName</span> = petName
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Pet class initialized"</span>)
    }
    
    <span class="hljs-keyword">deinit</span> {
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Pet class deinitialized"</span>)
    }
}

<span class="hljs-keyword">var</span> javi: <span class="hljs-type">Person?</span>
<span class="hljs-keyword">var</span> tobby: <span class="hljs-type">Pet?</span>

javi =<span class="hljs-attribute"> Person</span>(name: <span class="hljs-string">"Javi"</span>)
tobby =<span class="hljs-attribute"> Pet</span>(petName: <span class="hljs-string">"Tobby"</span>)

javi!.<span class="hljs-attribute">pet</span> = tobby
tobby!.<span class="hljs-attribute">owner</span> = javi

tobby = <span class="hljs-literal">nil</span></code></pre>

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 8px" src="/assets/img/PersonPetOutputWeak.png"></h1>

Now the Pet class has been deinitialized properly, because we have set a `weak` reference in the variable. Please, bear in mind that **`weak` references are always declared as variables, because they need their value to be changed to `nil`**.

We can also use the `unowned` reference approach, but unlike a `weak` reference, the `unowned` does not turn a variable into an optional one, so it expects the variable to have a value. It is very important also **to use an `unowned` variable when the reference always refers to an instance that has not been deallocated previously and it is already allocated** or you will wet a runtime error. You can also use `unowned` with optional variables, and it works as a `weak` reference does:

<style>.hljs-number{color: #D9C97C;}.hljs-section{color:#6BDFFF;}.hljs-symbol{color:#FF8170;}.hljs-meta{color:#B281EB;}.hljs-params{color:#ACF2E4;}.hljs-variable{color:#DABAFF;}.hljs-title{color:#6BDFFF;}.hljs-keyword{color:#FF7AB2;}.hljs{color:#E0E0E0;display:block;padding:0.5em;}.hljs-class{color:#6BDFFF;}.hljs-addition{color:#FF8170;}.hljs-strong{font-weight:bold;}.hljs-regexp{color:#DABAFF;}.hljs-selector-tag{color:#FF7AB2;}.hljs-selector-class{color:#DABAFF;}.hljs-quote{color:#7F8C98;}.hljs-selector-id{color:#DABAFF;}.hljs-bullet{color:#FF8170;}.hljs-tag{color:#DABAFF;}.hljs-attribute{color:#DABAFF;}.hljs-name{color:#DABAFF;}.hljs-deletion{color:#DABAFF;}.hljs-literal{color: #B281EB;}.hljs-emphasis{font-style:italic;}.hljs-function{color:#6BDFFF;}.hljs-built_in{color: #B281EB;}.hljs-template-variable{color:#DABAFF;}.hljs-type{color:#ACF2E4;}.hljs-link{color:#DABAFF;}.hljs-string{color:#FF8170;}.hljs-comment{color:#7F8C98;}.hljs-builtin-name{color: #B281EB;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Person</span> </span>{
    <span class="hljs-keyword">var</span> name: <span class="hljs-type">String</span>
    <span class="hljs-keyword">var</span> pet: <span class="hljs-type">Pet?</span>
    
   <span class="hljs-attribute"> init</span>(name: <span class="hljs-type">String</span>) {
        <span class="hljs-keyword">self</span>.<span class="hljs-attribute">name</span> = name
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Person class initialized"</span>)
    }
    
    <span class="hljs-keyword">deinit</span> {
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Person class deinitialized"</span>)
    }
}

<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Pet</span> </span>{
    <span class="hljs-keyword">var</span> petName: <span class="hljs-type">String</span>
    <span class="hljs-keyword">unowned</span> <span class="hljs-keyword">var</span> owner: <span class="hljs-type">Person?</span>
    
   <span class="hljs-attribute"> init</span>(petName: <span class="hljs-type">String</span>) {
        <span class="hljs-keyword">self</span>.<span class="hljs-attribute">petName</span> = petName
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Pet class initialized"</span>)
    }
    
    <span class="hljs-keyword">deinit</span> {
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Pet class deinitialized"</span>)
    }
}

<span class="hljs-keyword">var</span> javi: <span class="hljs-type">Person?</span>
<span class="hljs-keyword">var</span> tobby: <span class="hljs-type">Pet?</span>

javi =<span class="hljs-attribute"> Person</span>(name: <span class="hljs-string">"Javi"</span>)
tobby =<span class="hljs-attribute"> Pet</span>(petName: <span class="hljs-string">"Tobby"</span>)

javi!.<span class="hljs-attribute">pet</span> = tobby
tobby!.<span class="hljs-attribute">owner</span> = javi

javi = <span class="hljs-literal">nil</span></code></pre>

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 8px" src="/assets/img/PersonPetOutputUnowned.png"></h1>

<br>
<h3 style="color: #403F3F">Retain cycles in closures</h3>

Now that we know what a retain cycle is and how to avoid them, is time to know a very common memory leak case that occurs within a closure. I will take for granted that you already know what a closure is in terms of syntax and asynchrony, if not please head up to <a href="https://www.javiercarrilloblog.com/coding/17/05/2021/SwiftClosures.html">**How Swift Closure Works**</a> where you can find a brief explanation about closures behaviour. I also recommend have a look to <a href="https://www.javiercarrilloblog.com/coding/15/04/2022/SwiftAsyncAwait.html">**Swift Concurrency I: Async/Await**</a> for a further understanding about concurrency.

Let's have a look to this class whith a closure reference to a lazy variable:
<style>.hljs-number{color: #D9C97C;}.hljs-type{color:#ACF2E4;}.hljs-selector-id{color:#DABAFF;}.hljs-symbol{color:#FF8170;}.hljs-keyword{color:#FF7AB2;}.hljs-comment{color:#7F8C98;}.hljs-section{color:#6BDFFF;}.hljs-builtin-name{color: #B281EB;}.hljs-title{color:#6BDFFF;}.hljs-meta{color:#B281EB;}.hljs-name{color:#DABAFF;}.hljs-attribute{color:#DABAFF;}.hljs-literal{color: #B281EB;}.hljs-built_in{color: #B281EB;}.hljs-deletion{color:#DABAFF;}.hljs-selector-tag{color:#FF7AB2;}.hljs-strong{font-weight:bold;}.hljs-function{color:#6BDFFF;}.hljs-addition{color:#FF8170;}.hljs-link{color:#DABAFF;}.hljs-params{color:#ACF2E4;}.hljs{display:block;color:#E0E0E0;padding:0.5em;}.hljs-bullet{color:#FF8170;}.hljs-regexp{color:#DABAFF;}.hljs-string{color:#FF8170;}.hljs-emphasis{font-style:italic;}.hljs-template-variable{color:#DABAFF;}.hljs-quote{color:#7F8C98;}.hljs-selector-class{color:#DABAFF;}.hljs-tag{color:#DABAFF;}.hljs-class{color:#6BDFFF;}.hljs-variable{color:#DABAFF;}</style>

<pre style="background-color: #292A30; border-radius:8px; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid #DDDDDD"><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Text</span> </span>{
    
    <span class="hljs-keyword">var</span> title: <span class="hljs-type">String</span>
    <span class="hljs-keyword">var</span> content: <span class="hljs-type">String?</span>
    
    <span class="hljs-built_in">lazy</span> <span class="hljs-keyword">var</span> formated: () -&gt; <span class="hljs-type">String</span> = {
        <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> content = <span class="hljs-keyword">self</span>.<span class="hljs-attribute">content</span> {
            <span class="hljs-keyword">return</span> <span class="hljs-string">"Title: \(self.title), Content: \(content)"</span>
        } <span class="hljs-keyword">else</span> {
            <span class="hljs-keyword">return</span> <span class="hljs-string">"Title: \(self.title)"</span>
        }
    }
    
   <span class="hljs-attribute"> init</span>(title: <span class="hljs-type">String</span>, content: <span class="hljs-type">String?</span> = <span class="hljs-literal">nil</span>){
        <span class="hljs-keyword">self</span>.<span class="hljs-attribute">title</span> = title
        <span class="hljs-keyword">self</span>.<span class="hljs-attribute">content</span> = content
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Text class initialized"</span>)
    }
    
    <span class="hljs-keyword">deinit</span> {
       <span class="hljs-attribute"> print</span>(<span class="hljs-string">"Text class deinitialized"</span>)
    }
    
}


<span class="hljs-keyword">var</span> class1: <span class="hljs-type">Text?</span> =<span class="hljs-attribute"> Text</span>(title: <span class="hljs-string">"Chapter 1"</span>)
class1!.<span class="hljs-attribute">content</span> = <span class="hljs-string">"Alexander Hamilton: The founding father"</span><span class="hljs-attribute">
print</span>(class1!.<span class="hljs-attribute">formated</span>())
class1!.<span class="hljs-attribute">content</span> = <span class="hljs-literal">nil</span><span class="hljs-attribute">
print</span>(class1!.<span class="hljs-attribute">formated</span>())
class1 = <span class="hljs-literal">nil</span>
</code></pre>

We could expect that when we set "class1" as nil, the class is deinitialized, but... there's a `self` strong reference inside the closure! So ARC will not deinitilize the class:
<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 8px" src="/assets/img/TextOutput.png"></h1>


<br>
<h3 style="color: #403F3F">Summing Up</h3>

- A memory leak is memory that was allocated at some point but never released, and since there is no reference to it there is no way to free it so it can be used in the future.
- We can prevent memory leaks by using `weak` and `unowned` references instead of strong references bewteen classes. `weak` references are suitable when the variable refers to a shorter lifetime instance and `unowned` for equal or longer lifetime one.


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




