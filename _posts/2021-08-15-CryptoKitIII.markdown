---
layout: post
title: "CryptoKit III: Sign It"
date:   2021-08-15 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
In previous articles we saw basics how concepts about encryption using CrypyoKit for Swift and also how to create public keys to encrypt/decrypt data. If you are new in this CryptoKit series I strongly recommend heading to the firs article <a href="https://www.javiercarrilloblog.com/coding/15/06/2021/CryptoKit.html">CryptoKit I: The Basics"</a> so you can follow this series properly. In this article we will go throught how to create and verify signatures in order to ensure authenticity and integrity of the data we encrypt.

In previous articles we use P256 NIST´s algorithm to generate the encryption key, and we could use it also now for building the signature but I really would like to introduce you the Curve225519. This curve was developed by Daniel J. Berstain and became very popular since 2013, when Edward Snowden leaked that the NSA was able to introduce a back door in the curve-based random generator of the P256...that may mean that that the NSA is able to jump over P256 encryption.

```swift
import Foundation
import CryptoKit

let signatureKey = Curve25519.Signing.PrivateKey() //Creating the private key
let signatureKeyPublic = signatureKey.publicKey //Creating the public key
let signatureKeyPublicData = signatureKeyPublic.rawRepresentation //Data type for public key

let initSignatureKeyPublic = try! Curve25519.Signing.PublicKey(rawRepresentation: signatureKeyPublicData) //Initializing the public key

let info = "This is the confidential information" //Info to sign
let infoData = info.data(using: .utf8) //Data type for info to sign
let signature = try! signatureKey.signature(for: infoData!) //Signing the info

if initSignatureKeyPublic.isValidSignature(signature, for: infoData!) {
    print("The signature is Ok")
} //Using the public key to check the signature
```
> The signature is OK

We already know how to sign the info so could apply that to our encryption purposes:

```swift
import Foundation
import CryptoKit

let protocolSalt = "Javier is cool".data(using: .utf8)!

let javierPrivateKey = P256.KeyAgreement.PrivateKey() 
let javierPublicKey = javierPrivateKey.publicKey 

let pepePrivateKey = P256.KeyAgreement.PrivateKey() 
let pepePublicKey = pepePrivateKey.publicKey 

let javierSharedSecret = try! javierPrivateKey.sharedSecretFromKeyAgreement(with: pepePublicKey) PepePublicKey
let javierSymmetricKey = javierSharedSecret.hkdfDerivedSymmetricKey(using: SHA256.self, salt: protocolSalt, sharedInfo: Data(), outputByteCount: 32) 

let pepeSharedSecret = try! pepePrivateKey.sharedSecretFromKeyAgreement(with: javierPublicKey) 
let pepeSymmetricKey = pepeSharedSecret.hkdfDerivedSymmetricKey(using: SHA256.self, salt: protocolSalt, sharedInfo: Data(), outputByteCount: 32)

//Javier encrypts data
let chain = "This is a super confidential information" 
let chainData = chain.data(using: .utf8)!
let key = javierSymmetricKey 
let chainEncrypted = try! ChaChaPoly.seal(chainData, using: key).combined

//Javier signs the encrypted data
let signatureKey = Curve25519.Signing.PrivateKey()
let signatureKeyPublic = signatureKey.publicKey
let signatureKeyPublicData = signatureKeyPublic.rawRepresentation
let signature = try! signatureKey.signature(for: chainEncrypted)

//Pepe verifies the signature
let initSignatureKeyPublic = try! Curve25519.Signing.PublicKey(rawRepresentation: signatureKeyPublicData)
if initSignatureKeyPublic.isValidSignature(signature, for: chainData!) {
    //Pepe decrypts data
        let container = try! ChaChaPoly.SealedBox(combined: chainEncrypted)
        let containerDecrypted = try! ChaChaPoly.open(container, using: pepeSymmetricKey) //Pepe is using his own symmetric key (which is the same as Javier's one)
        let chainDecrypted = String(data: containerDecrypted, encoding: .utf8)
        print(chainDecrypted!)
}
```

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
        <img style="display: block; margin-left: auto; margin-right: auto; width: 100%; object-fit: contain" src="../assets/img/yo2.png">
        </td>
        <td style="border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 2px solid #DDDDDD; background-color: #FDFDFD; text-align: left; vertical-align: center; padding: 10px">
            <p style="font-size: 26px; margin: 0px 0px"><b>Javi Carrillo</b></p>
            <p style="font-size: 18px">In the past I developed car's powertrain control software. Now I develope mobile apps for many purposes.</p>
        </td>
    </tr>
</table>




