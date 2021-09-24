---
layout: post
title: "ARKit: Image Recognition"
date:   2021-09-24 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
The invention of computers was the first big leap in the tech industry and the world wide deployment of the internet was the second one, and now it seems that is time for the Augment Reality. Apple is very aware about this and rumours say that the bitten apple company is almost ready to launch a new sort of glasses or helmet with advanced AR capabilities. I know that rumours are just rumours but what is completely sure is that Apple has been preparing the path for AR deployment during the last five years, that´s why frameworks like ARKit or RealityKit were released.

For those who do not know what Augmented Reality is, basically it is the way that 2D and 3D computer generated items are visualized into the real world thanks to a camera and a screen or visor, and ARKit is a Swift framework developed for that purpose. ARKit takes over device motion traking, camera scene capture, advanced scene processing and a bunch of different processes that take part into the AR experience. Combined with ARkit there are other frameworks really useful for AR purposes, like SceneKit or RealityKit.

Today we are gonna build an Image Recognition App by means of AR capabilities, so when the camera of our device detects a known image the App will display a computer generated item. In this example we are gonna use a MVC arquitecture for building the App but as you may know it is possible to integrate it into SwiftUI thanks to `UIViewRepresentableContainer`.

The first thing we need to do is to set a new XCode project configured for Augmented Reality purposes:
<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 80%; border-radius: 10px; box-shadow: 0px 0px 20px grey" src="/assets/img/ARproject.png"></h1>
Also, we have to select the AR technology we want to use so we are going to choose `SceneKit`.
<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 80%; border-radius: 10px; box-shadow: 0px 0px 20px grey" src="/assets/img/sceneKit.png"></h1>

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
        <img style="display: block; margin-left: auto; margin-right: auto; width: 100%; object-fit: contain" src="/assets/img/yo2.png">
        </td>
        <td style="border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 2px solid #DDDDDD; background-color: #FDFDFD; text-align: left; vertical-align: center; padding: 10px">
            <p style="font-size: 26px; margin: 0px 0px"><b>Javi Carrillo</b></p>
            <p style="font-size: 18px">In the past I developed car's powertrain control software. Now I develope mobile apps for many purposes.</p>
        </td>
    </tr>
</table>




