---
layout: post
title: CryptoKit Basics
date:   2021-06-15 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---

When you decide to build an App or any other kind of software development it is very likely that you will manage sensitive information that you want to protect from others, above all when the app is linked to a network. No good programmer would ever code an App that sends to the net the username or password in a raw format without been encrypted previously. Bear in mind that building your own encryption algorithms is a very tough and risky task unless you are cryptography expert, and fortunately nowadays there are many libraries that could help you with this issue. As you may now if you are an iOS developer, Apple provides a library called CryptoKit for your encryption purposes. If it is the first time for you hearing about this don´t worry, in this article we will go through CryptoKit basics so at the end you could start working with cryptography within your development.

<h2 style="color: #403F3F">Hashing Data</h2>
A Hash is a function that returns a unique identifier for the input data. The returned value is called "digest". Lets see an example with CryptoKit:

```swift
import Foundation
import CryptoKit

let chain = "This is a string" //This is the information we want to hash.
let chainData = cadena.data(using: .utf8) //Turning the string type into data type.

let chainDigest = SHA256.hash(data: chainData!) //Getting the hash digest
```
The hash digest returned for this is example is:
> 4e9518575422c9087396887ce20477ab5f550a4aa3d161c5c22a996b0abb8b35

If this method valid to encrypt data? Are we sure that a hash function will return a unique output for a unique input? Well, the answer to these questions is Yes but No. It depends on which hash function you are using. Notice that for this example we have used the SHA256 which is a 256 bits output function and so far there has not been any collision case reported. Collision resistance of a hash function is the likelihood that for two different input values the function returns exactly the same digest value. If you are curios about how many hash functions are available as an standard, collision reports, performance and more I strongly recommend you heading up to <a href="https://www.nist.gov">NIST website</a> where you can find detailed information about this.