---
layout: post
title: "CryptoKit II: Don't share your secret"
date:   2021-07-16 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
If you are here it is probably because you have already read <a href="https://www.javiercarrilloblog.com/coding/15/06/2021/CryptoKit.html">CryptoKit I: The Basics</a>, the first article I wrote about how CryptoKit helps you encrypt the data you are managing throught your Swift development. There you discovered the basics about CryptoKit: what is a hash function and its elements, how to authenticate a hash digest and how to encrypt data inside a container. Now, in this article, you will learn how to share encrypted data with a trusted person by means of a "secret". Let's dive into it.
<br>
<br>
<h2 style="color: #403F3F">Key Agreement</h2>
Before going deeply about this topic it is neccessary to understand a few concepts about cryptography. What we allready know is that in order to encrypt any information we need to generate a key previously. Then we use that key to encrypt or decrypt the information. In <a href="https://www.javiercarrilloblog.com/coding/15/06/2021/CryptoKit.html">CryptoKit I: The Basics</a> we used a symmetric key for that purpose, but this simmetric key must be shared "out of band" with the trusted person we want to decrypt the information, otherwise a third person could sniff the key and use it. So how can we encrypt data and let other person decrypt it without sending the key that could decrypt the data? The answer is <b>Public-Key Cryptography</b>.

The idea is to let both the sender and the recipient generate a key to decrypt the data by their own by means of a public key that its send throught the net. How is this possible? Thanks to a mathematical elliptic curve. And how this works? Well basically each member (the sender who encrypts the data and the recipient who decrypts the data) is gonna create by their own a private and a public key. Then they are gonna share their own public key and use it to built a "secret" which, thanks to elliptic curve properties, will be the same for both members and its used to encrypt/decrypt the data. Let's see all this with an example:

```swift
import Foundation
import CryptoKit

//The first thing we do is generate a salt value. A salt value is an optional value that is used as an input (aport from the information we want to hash) of a hash function in order to create a unique hash. What? Let me explain. Let´s imagine that two different users of a service (gmail for instance) set up the same password. It is totally possible, so the hash digest of both passwords will be exactly the same. But if we use a salt value for instance based on the username or the thelephone number of each user then the digest will be different
let protocolSalt = "Javier is cool".data(using: .utf8)!

let javierPrivateKey = P256.KeyAgreement.PrivateKey() //Javier's Private Key. Here I have choosen a P256 NIST elliptic curve
let javierPublicKey = javierPrivateKey.publicKey //Javier`s Public Key created by means of the private key

let pepePrivateKey = P256.KeyAgreement.PrivateKey() //Pepe's Private Key
let pepePublicKey = pepePrivateKey.publicKey //Pepe's Public Key

let javierSharedSecret = try! javierPrivateKey.sharedSecretFromKeyAgreement(with: pepePublicKey) //Javier's Secret by means of PepePublicKey
let javierSymmetricKey = javierSharedSecret.hkdfDerivedSymmetricKey(using: SHA256.self, salt: protocolSalt, sharedInfo: Data(), outputByteCount: 32) //Javier's Symmetric Key. We use the salt value.

let pepeSharedSecret = try! pepePrivateKey.sharedSecretFromKeyAgreement(with: javierPublicKey) //Pepe's Secret by means of JavierPublicKey
let pepeSymmetricKey = pepeSharedSecret.hkdfDerivedSymmetricKey(using: SHA256.self, salt: protocolSalt, sharedInfo: Data(), outputByteCount: 32) //Pepe's Symmetric Key

if javierSharedSecret == pepeSharedSecret {
    print("The Secret is OK")
} else {
    print("The Secret is not OK")
}
````
> The Secret is OK

As we can see the Javier's Secret and Pepe's Secret are exactly the same, so that means that Javier's and Pepe's Symmetric keys are exactly the same. Now we are gonna use that symmetric key to encrypt/decrypt data.

```swift
//Javier encrypts data
let chain = "This is a super confidential information" //Chain we want to encrypt
let chainData = chain.data(using: .utf8)!
let key = javierSymmetricKey //Key used to encrypt data
let chainEncrypted = try! ChaChaPoly.seal(chainData, using: key).combined //Encrypting data

//Pepe decrypts data
let container = try! ChaChaPoly.SealedBox(combined: chainEncrypted)
let containerDecrypted = try! ChaChaPoly.open(container, using: pepeSymmetricKey) //Pepe is using his own symmetric key (which is the same as Javier's one)
let chainDecrypted = String(data: containerDecrypted, encoding: .utf8)
print(chainDecrypted!)
```
> This is a super confidential information

Now you know how to implement public key encryption in your projects. In following articles we will go throught how to sign the information.

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




