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
When you decide to build an App or any other kind of software development it is very likely that you will manage sensitive information that you want to protect from others, above all when the app is linked to a network. No good programmer would ever code an App that sends to the net the username or password in a raw format without been encrypted previously.

Bear in mind that building your own encryption algorithms is a very tough and risky task unless you are a cryptography expert, and fortunately nowadays there are many libraries that could help you with this issue. As you may now if you are an iOS developer, Apple provides a library called CryptoKit for your encryption purposes. If it is the first time for you hearing about this don´t worry, in this article we will go through CryptoKit basics so at the end you could start working with cryptography within your development.
<br>
<br>
<h2 style="color: #403F3F">Hashing Data</h2>
A Hash is a function that returns a unique identifier for the input data. The returned value is called "digest". Let's see an example with CryptoKit:

```swift
import Foundation
import CryptoKit

let chain = "This is a string" //This is the information we want to hash
let chainData = cadena.data(using: .utf8) //Turning the string type into data type

let chainDigest = SHA256.hash(data: chainData!) //Getting the hash digest
```
The hash digest returned for this is example is:
> 4e9518575422c9087396887ce20477ab5f550a4aa3d161c5c22a996b0abb8b35

Are we sure that a hash function will return a unique output for a unique input? Well, the answer to these questions is Yes but No. It depends on which hash function you are using. Notice that for this example we have used the SHA256 which is a 256 bits output function and there is not any collision case reported so far. Collision resistance of a hash function is the likelihood that for two different input values the function returns exactly the same digest value. If you are curios about how many hash functions are available as an standard, collision reports, performance and more I strongly recommend you heading up to <a href="https://www.nist.gov">NIST website</a> where you can find detailed information about this.
<br>
<br>
<h2 style="color: #403F3F">Authenticating Data with HMAC</h2>
HMAC is hash fucntion that requires a key to build the digest. We could consider it as a certified hash that inform to the recipient that digest comes from someone and that the information has not been corrupted. HMAC stands for <i>Hash Message Authentication Code</i>.

```swift
import Foundation
import CryptoKit

let chain = "This is a string" //This is the information we want to hash
let chainData = cadena.data(using: .utf8) //Turning the string type into data type

let key = SymmetricKey(size: .bits256) //Building a key

let HMAC = HMAC<SHA256>.authenticationCode(for: chainData!, using: key) //Creating the HMAC by means of the key

//Lets validate the HMAC by means of the key.
HMAC<SHA256>.isValidAuthenticationCode(HMAC, authenticating: chainData!, using: key) //It will return a true because it takes the key used for creating the HMAC

//Creating a different HMAC by means of a different key
let key2 = SymmetricKey(size: .bits256)//Building a second key
let HMAC2 = HMAC<SHA256>.authenticationCode(for: chainData!, using: key2)//Creating a new HMAC with the second key

//Validating the new HMAC BUT with the first key
HMAC<SHA256>.isValidAuthenticationCode(HMAC2, authenticating: chainData!, using: key)//It will return false because is using the first key to validate a HMAC created by means of key2
```


<br>
<h2 style="color: #403F3F">Encrypting Data</h2>
A hash function does not encrypt data. A hash function only returns an identifier of the data. A hash fucntion is valid to authenticate the content but nor for knowing the content itself. CryptoKit offers two main symetric encryption methods: ChaChaPoly and AES-GCM. What's de difference between this two methods? Well the first one is that ChaChaPoly (ChaCha20-Poly1305) is only defined at 256 bits security level whereas AES-GCM is able to target 128-bit, 192-bit and 256-bit security levels. Another difference is that ChaChaPoly20 is faster in the most of the cases than AES-GCM, unless you are using hardware acceleration.

What CryptoKit offers in terms of data encryption is a securized container (based on ChaChaPoly or AES-GCM) with the encrypted information. The container is composed by three elements:
- A cipher: A version of the encrypted data
- An authentication tag
- A NONCE (Number that can only be used ONCE): The NONCE is unique for each encryption process. That means that if we encrypt exactly the same information twice the NONCE of the first and the second encryption process wil be difference. The NONCE is used as a root vector for the block encryption process.

Now worries if you have not understand all at the first time. Let's see an example about this.

```swift
import Foundation
import CryptoKit

let chain = "This is a chain that must be encrypted" //This is the information we want to encrypt
let chainData = chain.data(using: .utf8) //Turning the string into a Data type

let key = SymmetricKey(size: .bits256) //Generating a key

let encryptedChain = try! ChaChaPoly.seal(chainaData!, using: key).combined //We use the key to generate the three elements that must have the container

let container = try! ChaChaPoly.SealedBox(combined: encryptedChain) //Generating the container with the three elements

let decryptedChain = try! ChaChaPoly.open(container, using: key) //We coul only open/decrypt the container with the exact same key that was used to encrypt the information
print(String(data: decryptedChain, encoding: .utf8)!)
```

> This is a chain that must be encrypted

It is also possible to acces the three elements of the container:

```swift
let nonce = container.nonce 

let tag = container.tag

let ciphertext = container.ciphertext 
```
These are just the basics to stast using CryptoKit. For further information I strongly recommend reading <a href="">Protect the secrets</a> which explains with more detail how to encrypt data, store it and send it through a network communication.
Thanks for reading :)

<table style="border-right: 0px solid gray; border-left: 0px solid gray; width: 100%">
    <tr style="border-right: 0px solid gray; border-left: 0px solid gray">
        <td style="border-top: 2px solid #E8E8E8; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid gray; width: 70%; color: #E8E8E8; font-size: 20px; text-align: left; vertical-align: top; padding: 0px">
            <p><b>Written by</b></p>
        </td>
        <td style="border-top: 2px solid #E8E8E8; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 0px solid gray; width: 70%; text-align: left; vertical-align: top; padding: 0px">
        </td>
    </tr>
    <tr style="border-right: 0px solid gray; border-left: 0px solid gray">
        <td style="width: 30%; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 2px solid gray; color: gray; font-size: 20px; background-color: #FDFDFD; text-align: left; vertical-align: top; padding: 0px">
        </td>
        <td style="width: 30%; border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 2px solid gray; background-color: #FDFDFD; text-align: left; vertical-align: top; padding: 0px">
            <p style="font-size: 20px;"><b>Javi Carrillo</b></p>
            <p>In the past I developed car's powertrain control software. Now I develope mobile apps for many purposes.</p>
        </td>
    </tr>
</table>




