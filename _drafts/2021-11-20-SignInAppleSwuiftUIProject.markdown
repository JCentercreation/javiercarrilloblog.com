---
layout: post
title: "Sign-In with Apple in a SwiftUI personal project using Firebase"
date:   2021-11-20 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
Hi guys! In todays article I will explain how to implement sign-in with apple feature in your SwiftUI project by using Google Firebase capabilities. Based on my own user experience when using an App or surfing in the internet, it is very handy to have a proper method to sign-in with your most used credentials instead of forcing the user to enter by hand the email and password. Moreover, Sign-In with Apple offers another layer of security and privacy.

As I mentioned we are going to use the cloud computing tool that Google has embeded in his Firebase service. Firebase is a great tool for developers and it offers a wide range of capabalities for your app, from data storage to crash reporting or A/B testing. In the future I may talk you about these other features but now lets stick to the sign-in with Apple one.

<br>
<h3 style="color: #403F3F">Adding Firebase SDK into your SwiftUI project</h3>

It is possible to install de Firebase dependencies through many packages manager spftware like CocoaPods or Carthage but I really like to use the XCode native Swift Package Manager, so head up to 'File/Add Packages' and search for "Firebase" in the following window, press 'Add Package' and there you go.

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 80%; border-radius: 10px; box-shadow: 0px 0px 20px grey" src="/assets/img/FirebaseSPM.png"></h1>
<br>



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




