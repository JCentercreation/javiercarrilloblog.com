---
layout: post
title: "SwiftData: Introduction"
date:   2023-08-19 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
> # Throughout this post you will learn:
>
> - What SwiftData is
> - How SwiftData works

<br>
<h3 style="color: #403F3F">A new persistence framework</h3>
SwiftData is a new Swift framework for persistence purposes. It was introduced during Apple WWDC23 and althouth its aimed to substitute CoreData, it leans on CoreData's persistency technology and Swift's modern concurrency capabilities.
<br>
<br>
Although SwiftData is (at the time of this post publication) very new and it's on beta indeed, it is very suitable to start getting in touch with it cause it brings some advantages over CoreData above all when it comes to implement it with SwiftUI.
<br>
<br>
Here some SwiftData features that outstand CoreData:

- Declarative data modeling
- Automatic persistence
- Efficient Data Access
- Integration with SwiftUI

<br>
<h3 style="color: #403F3F">How SwiftData works</h3>

SwiftData implementation is based on a new Swift features also introduced during Apple WWDC23: Swift Macros. In a future post I'll explain how these Swift Macros works, but for now let's say that basically a Swift Macro injects code within a class during compile-time and also makes the class comply with a protocol if necessary. In that regard the most common macro applied while using SwiftData is `@Model`, and this macro builds a persistence scheme within the class that defines the model of the object we want to apply persistence on.

Let's image we want to develope an app to track our notes. Then we will need to define a model of a note object like this:
<style>.hljs-quote{color:#7F8C98;}.hljs-symbol{color:#FF8170;}.hljs{display:block;color:#E0E0E0;padding:0.5em;}.hljs-built_in{color: #B281EB;}.hljs-type{color:#ACF2E4;}.hljs-number{color: #D9C97C;}.hljs-tag{color:#DABAFF;}.hljs-emphasis{font-style:italic;}.hljs-class{color:#6BDFFF;}.hljs-selector-tag{color:#FF7AB2;}.hljs-template-variable{color:#DABAFF;}.hljs-params{color:#ACF2E4;}.hljs-meta{color:#B281EB;}.hljs-comment{color:#7F8C98;}.hljs-deletion{color:#DABAFF;}.hljs-string{color:#FF8170;}.hljs-keyword{color:#FF7AB2;}.hljs-attribute{color:#DABAFF;}.hljs-section{color:#6BDFFF;}.hljs-strong{font-weight:bold;}.hljs-builtin-name{color: #B281EB;}.hljs-variable{color:#DABAFF;}.hljs-name{color:#DABAFF;}.hljs-title{color:#6BDFFF;}.hljs-bullet{color:#FF8170;}.hljs-function{color:#6BDFFF;}.hljs-link{color:#DABAFF;}.hljs-regexp{color:#DABAFF;}.hljs-addition{color:#FF8170;}.hljs-literal{color: #B281EB;}.hljs-selector-class{color:#DABAFF;}.hljs-selector-id{color:#DABAFF;}</style><pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-keyword">import</span> Foundation
<span class="hljs-keyword">import</span> SwiftData

<span class="hljs-type">@Model</span>
<span class="hljs-keyword">final</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">NoteModel</span>: <span class="hljs-title">Identifiable</span> </span>{
    <span class="hljs-type"> @Attribute</span>(.<span class="hljs-attribute">unique</span>) <span class="hljs-keyword">var</span> id: <span class="hljs-type">UUID</span>
    <span class="hljs-keyword"> var</span> title: <span class="hljs-type">String</span>
    <span class="hljs-keyword"> var</span> content: <span class="hljs-type">String</span>
    
   <span class="hljs-attribute"> init</span>(id: <span class="hljs-type">UUID</span> =<span class="hljs-attribute"> UUID</span>(), title: <span class="hljs-type">String</span>, content: <span class="hljs-type">String</span>) {
        <span class="hljs-keyword">    self</span>.<span class="hljs-attribute">id</span> = id
        <span class="hljs-keyword">    self</span>.<span class="hljs-attribute">title</span> = title
        <span class="hljs-keyword">    self</span>.<span class="hljs-attribute">content</span> = content
    }
}</code></pre>
As you may notice it is possible to use metadata within the SwiftData model like property attributes (`@Attribute`) or relationships (`@Relationship`). By making the property `id` as `.unique` each persistence instance of the object will have a unique value.

When working with SwiftData we have to deal with two main objects:

- `ModelContainer` works as the persistence backend for the `@Model` types. It can be used with defaults settings or customized configurations and migrations like proper URL or CloudKit implementation.
- `ModelContext` is an object that tracks changes and updates within the persistence models and provides actions to operate on them.

Let's see how to deal with this objects when building a SwiftUI app like the Notes App we code the "NoteModel" class for. Our Notes App needs a NoteView that not only is used to read the note but also to update its content:
<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-keyword">import</span> SwiftUI
<span class="hljs-keyword">import</span> SwiftData

<span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">NoteView</span>: <span class="hljs-title">View</span> </span>{
    
    <span class="hljs-keyword">var</span> note: <span class="hljs-type">NoteModel</span>
    
    <span class="hljs-type">@Environment</span>(\.<span class="hljs-attribute">modelContext</span>) <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> persistenceModelContext
    <span class="hljs-type">@Environment</span>(\.<span class="hljs-attribute">dismiss</span>) <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> dismiss
    
    <span class="hljs-type">@State</span> <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> title: <span class="hljs-type">String</span> = <span class="hljs-string">""</span>
    <span class="hljs-type">@State</span> <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> content: <span class="hljs-type">String</span> = <span class="hljs-string">""</span>
    <span class="hljs-type">@State</span> <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> isUpdating: <span class="hljs-type">Bool</span> = <span class="hljs-literal">false</span>
    
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">VStack</span> {
            <span class="hljs-keyword">if</span> isUpdating || note.<span class="hljs-attribute">title</span>.<span class="hljs-attribute">isEmpty</span> {
                updatableNoteView
            } <span class="hljs-keyword">else</span> {
                nonUpdatableNoteView
            }
        }
    }
    
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> nonUpdatableNoteView: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">VStack</span> {
           <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"\(note.title)"</span>)
                .<span class="hljs-literal">font</span>(.<span class="hljs-attribute">title2</span>)
           <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"\(note.content)"</span>)
                .<span class="hljs-literal">font</span>(.<span class="hljs-attribute">title3</span>)
           <span class="hljs-attribute"> Button</span>(action: {
                isUpdating.<span class="hljs-attribute">toggle</span>()
            }, label: {
               <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"Update"</span>)
            })
        }
    }
    
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> updatableNoteView: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">VStack</span> {
           <span class="hljs-attribute"> TextField</span>(note.<span class="hljs-attribute">title</span>.<span class="hljs-attribute">isEmpty</span> ? <span class="hljs-string">"Insert Title"</span> : <span class="hljs-string">"\(note.title)"</span>, text: $title)
                .<span class="hljs-literal">font</span>(.<span class="hljs-attribute">title2</span>)
           <span class="hljs-attribute"> TextField</span>(note.<span class="hljs-attribute">title</span>.<span class="hljs-attribute">isEmpty</span> ? <span class="hljs-string">"Insert Content"</span> : <span class="hljs-string">"\(note.content)"</span>, text: $content)
                .<span class="hljs-literal">font</span>(.<span class="hljs-attribute">title3</span>)
           <span class="hljs-attribute"> Button</span>(action: {
               <span class="hljs-attribute"> saveNote</span>()
                isUpdating.<span class="hljs-attribute">toggle</span>()
               <span class="hljs-attribute"> dismiss</span>()
            }, label: {
               <span class="hljs-attribute"> Text</span>(<span class="hljs-string">"Done"</span>)
            })
        }
    }
    
    <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">saveNote</span><span class="hljs-params">()</span></span> {
        <span class="hljs-keyword">do</span> {
            note.<span class="hljs-attribute">title</span> = title
            note.<span class="hljs-attribute">content</span> = content
            persistenceModelContext.<span class="hljs-attribute">insert</span>(note)
            <span class="hljs-keyword">try</span> persistenceModelContext.<span class="hljs-attribute">save</span>()
        } <span class="hljs-keyword">catch</span> {
           <span class="hljs-attribute"> print</span>(error.<span class="hljs-attribute">localizedDescription</span>)
        }
    }
    
}

<span class="hljs-type">#Preview</span> {
   <span class="hljs-attribute"> NoteView</span>(note:<span class="hljs-attribute"> NoteModel</span>(title: <span class="hljs-string">"19/8/2023"</span>, content: <span class="hljs-string">"This is my first note"</span>))
        .<span class="hljs-literal">modelContainer</span>(<span class="hljs-keyword">for</span>: <span class="hljs-type">NoteModel</span>.<span class="hljs-attribute">self</span>)
}
</code></pre>
With the `ModelContext` environment property we insert a new object into the persistent model and save it.

Now we need a NotesListView to list all the saved Notes:
<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-keyword">import</span> SwiftUI
<span class="hljs-keyword">import</span> SwiftData

<span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">NotesListView</span>: <span class="hljs-title">View</span> </span>{
    
    <span class="hljs-type">@Query</span>(<span class="hljs-built_in">sort</span>: \<span class="hljs-type">NoteModel</span>.<span class="hljs-attribute">id</span>, order: .<span class="hljs-literal">reverse</span>) <span class="hljs-keyword">var</span> notes: [<span class="hljs-type">NoteModel</span>]
    <span class="hljs-type">@Environment</span>(\.<span class="hljs-attribute">modelContext</span>) <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> persitenceModelContext
    
    <span class="hljs-type">@State</span> <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> showNoteView: <span class="hljs-type">Bool</span> = <span class="hljs-literal">false</span>
    
    
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">View</span> {
        <span class="hljs-type">NavigationStack</span> {
            <span class="hljs-type">List</span> {
               <span class="hljs-attribute"> ForEach</span>(notes, id: \.<span class="hljs-attribute">id</span>) { note <span class="hljs-keyword">in</span>
                    <span class="hljs-type">NavigationLink</span> {
                       <span class="hljs-attribute"> NoteView</span>(note: note)
                    } label: {
                       <span class="hljs-attribute"> NoteListItemView</span>(note: note)
                    }
                }.<span class="hljs-attribute">onDelete</span>(perform: { indexSet <span class="hljs-keyword">in</span>
                   <span class="hljs-attribute"> deleteNote</span>(indexSet: indexSet)
                })
            }
            .<span class="hljs-literal">navigationTitle</span>(<span class="hljs-string">"Notes App"</span>)
            .<span class="hljs-literal">toolbar</span>(content: {
               <span class="hljs-attribute"> ToolbarItem</span>(placement: .<span class="hljs-literal">topBarTrailing</span>) {
                   <span class="hljs-attribute"> Button</span>(action: {
                        showNoteView = <span class="hljs-literal">true</span>
                    }, label: {
                       <span class="hljs-attribute"> Image</span>(systemName: <span class="hljs-string">"plus.circle"</span>)
                    })
                }
            })
        }.<span class="hljs-attribute">sheet</span>(isPresented: $showNoteView, content: {
            <span class="hljs-keyword">let</span> newNote =<span class="hljs-attribute"> NoteModel</span>(title: <span class="hljs-string">""</span>, content: <span class="hljs-string">""</span>)
           <span class="hljs-attribute"> NoteView</span>(note: newNote)
        })
    }
    
    <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">deleteNote</span><span class="hljs-params">(indexSet: IndexSet)</span></span> {
        indexSet.<span class="hljs-attribute">forEach</span> { index <span class="hljs-keyword">in</span>
            <span class="hljs-keyword">let</span> note = notes[index]
            persitenceModelContext.<span class="hljs-attribute">delete</span>(note)
            <span class="hljs-keyword">do</span> {
                <span class="hljs-keyword">try</span> persitenceModelContext.<span class="hljs-attribute">save</span>()
            } <span class="hljs-keyword">catch</span> {
               <span class="hljs-attribute"> print</span>(error.<span class="hljs-attribute">localizedDescription</span>)
            }
        }
    }
    
}

<span class="hljs-type">#Preview</span> {
   <span class="hljs-attribute"> NotesListView</span>()
        .<span class="hljs-literal">modelContainer</span>(<span class="hljs-keyword">for</span>: <span class="hljs-type">NoteModel</span>.<span class="hljs-attribute">self</span>)
}
</code></pre>
The List needs to query the notes stored in the model container with ` @Query(sort: , order: )`, and the modal container must be implemented with `modalContainer(for: )` in order to be accesible:
<pre><code class="hljs" style="background:#292A30;border-radius:8px"><span class="hljs-keyword">import</span> SwiftUI

@main
<span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">NotesAppApp</span>: <span class="hljs-title">App</span> </span>{
    <span class="hljs-keyword">var</span> body: <span class="hljs-keyword">some</span> <span class="hljs-type">Scene</span> {
        <span class="hljs-type">WindowGroup</span> {
           <span class="hljs-attribute"> NotesListView</span>()
        }
        .<span class="hljs-literal">modelContainer</span>(<span class="hljs-keyword">for</span>: <span class="hljs-type">NoteModel</span>.<span class="hljs-attribute">self</span>)
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




