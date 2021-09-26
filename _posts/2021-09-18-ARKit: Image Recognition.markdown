---
layout: post
title: "ARKit: Image Recognition"
date:   2021-09-18 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
The invention of computers was the first big leap in the tech industry and the world wide deployment of the internet was the second one, and now it seems that is time for the Augment Reality. Apple is very aware about this and rumours say that the bitten apple company is almost ready to launch a new sort of glasses or helmet with advanced AR capabilities. I know that rumours are just rumours but what is completely sure is that Apple has been preparing the path for AR deployment during the last five years, that´s why frameworks like ARKit or RealityKit were released.

For those who do not know what Augmented Reality is, basically it is the way that 2D and 3D computer generated items are visualized into the real world thanks to a camera and a screen or visor, and ARKit is a Swift framework developed for that purpose. ARKit takes over device motion traking, camera scene capture, advanced scene processing and a bunch of different processes that take part into the AR experience. Combined with ARkit there are other frameworks really useful for AR purposes, like SceneKit or RealityKit.

Today we are gonna build an Image Recognition App by means of AR capabilities, so when the camera of our device detects a known image the App will display a computer generated item. In this example we are gonna use a MVC arquitecture for building the App but as you may know it is possible to integrate it into SwiftUI thanks to `UIViewRepresentableContainer`.

Let´s imagine that we are building an App for a museum, and we want the user to have a great inmersive experience when looking at the pictures. For instance, when the vistor points with the device to a picture the title and the name of the artist who created that picture will show up in the visor. Let me show you the final result so you can get an idea about what we are talking about:
<img style="display: block; margin-left: auto; margin-right: auto; width: 100%; object-fit: contain" src="/assets/img/IMG_0513.PNG">
<br>
<h2 style="color: #403F3F">Project Configuration</h2>
The first thing we need to do is to set a new XCode project configured for Augmented Reality purposes:
<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 80%; border-radius: 10px; box-shadow: 0px 0px 20px grey" src="/assets/img/ARproject.png"></h1>
<br>
Also, we have to select the AR technology we want to use so we are going to choose `SceneKit`.
<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 80%; border-radius: 10px; box-shadow: 0px 0px 20px grey" src="/assets/img/sceneKit.png"></h1>
<br>

Now is time to add the pictures we want the App to recognize to the project. So what we need to do is to create, inside the Assets folder, a new `AR Resource Group`. Inside this new group we are gonna drop the images of the pictures, but we also have to define the dimensions of the picture in the real world.
<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 80%; border-radius: 10px; box-shadow: 0px 0px 20px grey" src="/assets/img/ARresources.png"></h1>
<br>

<h2 style="color: #403F3F">AR Session Configuration</h2>
Once the SceneKit project is created, you may realized that a initial configuration is already loaded and it is very likely that this initial configuration does not fit to your App purpose, as happen in this case. So the first thing we are gonna do is to configure properly the AR Session we want to set in the App.

```swift
class ViewController: UIViewController, ARSCNViewDelegate {

    @IBOutlet var sceneView: ARSCNView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // Set the view's delegate
        sceneView.delegate = self
        
    }
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        
        // Create a session configuration. In this case we're gonna use the Image Tracking Configuration.
        let configuration = ARImageTrackingConfiguration()
        
        // Define where are the images to be tracked. In this case our images are in a folder called "AR Resources"
        guard let trackingImages = ARReferenceImage.referenceImages(inGroupNamed: "AR Resources", bundle: nil) else {
            fatalError("Could not load tracking images")
        }
        
        configuration.trackingImages = trackingImages

        // Run the view's session
        sceneView.session.run(configuration)
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        
        // Pause the view's session
        sceneView.session.pause()
    }
}
```

Bear in mind that the device is not gonna able to detect a huge amount of pictures at the same time. Apple has not given any accurate number about this but from my experience I do not recommend trying to recognize more that 20 pictures at the same time. I guess this number depends on the device capabilities so later iPhones or iPads are able to recognize more images than the older versions.

<h2 style="color: #403F3F">Adding AR Items</h2>
Each time the App detects an image already stored in the AR Resources folder, internally is gonna place an anchor linked to the image. So thanks to that anchor we can also link to it a Scene node `SCNNode`, which is very useful to place the AR items. But firstly we want the App user to be aware of the App has recognized a picture, so for that we are gonna place a colored plane as an AR item. We are gonna do all this by means of a `renderer`function:

```swift
func renderer(_ renderer: SCNSceneRenderer, nodeFor anchor: ARAnchor) -> SCNNode? {
        guard let imageAnchor = anchor as? ARImageAnchor else { return nil } //Define the anchor as an ARImageAnchor.
        
        let plane = SCNPlane(width: imageAnchor.referenceImage.physicalSize.width, height: imageAnchor.referenceImage.physicalSize.height) //Define a SCNPlane with the dimensions of the detected ARImageAnchor.
        
        plane.firstMaterial?.diffuse.contents = UIColor.red //Define a color for the SCNPlane.
        
        let planeNode = SCNNode(geometry: plane) //Define a node for the plane, so the plane is fixed in this space and the user could move the device or the anchor could change its position in the room.
        planeNode.eulerAngles.x = -.pi / 2 //We need to rotate the planeNode to be parallel to the recognized picture.
        
        let node = SCNNode() //Create a new node
        node.addChildNode(planeNode) //Add the planeNode as a child of the new node so the rotation could be visualized.
        
        return node
    }
```

<h1><video src="/assets/img/out.MOV" controls="controls" style="max-width: 730px;"></video>
</h1>
<br>

<h2 style="color: #403F3F">Loading and parsing Pictures information</h2>
The information we want to be displayed as AR Items when a picture is recognized by the App is stored in a JSON file.
<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 80%; border-radius: 10px; box-shadow: 0px 0px 20px grey" src="/assets/img/picturesJSON.png"></h1>
<br>
We need to load the JSON file and to decode it into a data type, so first we need to create a struct that defines the data type:

```swift
import Foundation

struct Pictures: Decodable {
    let name: String
    let artist: String
    let date: String
    let description: String
}
```

And then we can decode the JSON information into our data type (function inside the `ViewController`):

```swift
func loadData(){
        guard let url = Bundle.main.url(forResource: "pictures", withExtension: "json") else {
            fatalError("Unable to find JSON")
        }
        
        guard let data = try? Data(contentsOf: url) else {
            fatalError("Unable to load JSON data")
        }
        
        let decoder = JSONDecoder()
        guard let loadPictures = try? decoder.decode([String: Pictures].self, from: data) else {
            fatalError("Unable to decode JSON data")
        }
        
        pictures = loadPictures
    }
```

Then we can find the proper information for the recognized picture into the JSON (function inside the `ViewController`):

```swift
func renderer(_ renderer: SCNSceneRenderer, nodeFor anchor: ARAnchor) -> SCNNode? {
        guard let imageAnchor = anchor as? ARImageAnchor else { return nil } //Define the anchor as an ARImageAnchor.
        guard let name = imageAnchor.referenceImage.name else { return nil } //Storing the recognized image name into a constant.
        guard let picture = pictures[name] else { return nil } //Accesing the recognized picture information stored in the JSON file
        
        let plane = SCNPlane(width: imageAnchor.referenceImage.physicalSize.width, height: imageAnchor.referenceImage.physicalSize.height) //Define a SCNPlane with the dimensions of the detected ARImageAnchor.
        
        plane.firstMaterial?.diffuse.contents = UIColor.red //Define a color for the SCNPlane.
        
        let planeNode = SCNNode(geometry: plane) //Define a node for the plane, so the plane is fixed in this space and the user could move the device or the anchor could change its position in the room.
        planeNode.eulerAngles.x = -.pi / 2 //We need to rotate the planeNode to be parallel to the recognized picture.
        
        let node = SCNNode() //Create a new node
        node.addChildNode(planeNode) //Add the planeNode as a child of the new node so the rotation could be visualized.
        
        return node
    }
```
<br>
<h2 style="color: #403F3F">Placing AR items</h2>
The App is able to recognize the picture and the search the information for that picture into the JSON file, so far. Now it's time to place that information into the scene as AR items. But before doing that I stringly recommend adding some extensions to the code that will be very useful in following steps.

The first extension is related to how SceneKit positions AR items. SceneKit (and SpriteKit), by default, positions the (0, 0, 0) of each item in the center of the item, which is very convinient for many purposes but not for our App, so let's make some changes in the SCNNode properties and adding also some new methods:

```swift
extension SCNNode {
    
    var width: Float {
        return (boundingBox.max.x - boundingBox.min.x) * scale.x //By multiplying for scale.x we ensure the dimensions are related to measures in the real world
    }
    
    var height: Float {
        return (boundingBox.max.y - boundingBox.min.y) * scale.y
    }
    
    func pivotOnTopLeft() { //Positions the center in the top left corner of the item
        let (min, max) = boundingBox
        
        pivot = SCNMatrix4MakeTranslation(min.x, (max.y - min.y) + min.y, 0)
    }
    
    func pivotOnTopCenter() { //Positions the center in the top center of the item
        let (min, max) = boundingBox
        
        pivot = SCNMatrix4MakeTranslation((max.x - min.x) / 2 + min.x, (max.y - min.y) + min.y, 0)
    }
}
```

Now we have to create the ARText we want to be displayed as AR items, and for that we are gonna build another function inside the `ViewController`:

```swift
func textNode(string: String, font: UIFont, maxWidth: Int? = nil) -> SCNNode {
        let text = SCNText(string: string, extrusionDepth: 0)
        text.flatness = 0.1 //Corners very smooth.
        text.font = font
        
        if let maxWidth = maxWidth {
            text.containerFrame = CGRect(origin: .zero, size: CGSize(width: maxWidth, height: 500)) //Text wrapped into a maxWidth rectangle.
            text.isWrapped = true
        }
        
        let textNode = SCNNode(geometry: text)
        textNode.scale = SCNVector3(0.002, 0.002, 0.002) //We need to reescale the textNode cause SceneKit uses meters by default so for instance a font 10 will be displayed as a 10 meters tall.
        
        return textNode
    }
```

Now it's time to implement the new funtion:

```swift
func renderer(_ renderer: SCNSceneRenderer, nodeFor anchor: ARAnchor) -> SCNNode? {
        guard let imageAnchor = anchor as? ARImageAnchor else { return nil } //Define the anchor as an ARImageAnchor.
        guard let name = imageAnchor.referenceImage.name else { return nil } //Storing the recognized image name into a constant.
        guard let picture = pictures[name] else { return nil } //Accesing the recognized picture information stored in the JSON file
        
        let plane = SCNPlane(width: imageAnchor.referenceImage.physicalSize.width, height: imageAnchor.referenceImage.physicalSize.height) //Define a SCNPlane with the dimensions of the detected ARImageAnchor.
        
        plane.firstMaterial?.diffuse.contents = UIColor.clear //Define a color for the SCNPlane.
        
        let planeNode = SCNNode(geometry: plane) //Define a node for the plane, so the plane is fixed in this space and the user could move the device or the anchor could change its position in the room.
        planeNode.eulerAngles.x = -.pi / 2 //We need to rotate the planeNode to be parallel to the recognized picture.
        
        let node = SCNNode() //Create a new node
        node.addChildNode(planeNode) //Add the planeNode as a child of the new node so the rotation could be visualized.
        
        let spacing: Float = 0.005
        
        let titleNode = textNode(string: picture.name, font: UIFont.boldSystemFont(ofSize: 10)) //Defining the Title Node
        titleNode.pivotOnTopLeft() //Pivoting the zero of the titleNode
        
        titleNode.position.x += Float(plane.width / 2) + spacing //Replacing in x the titleNode
        titleNode.position.y += Float(plane.height / 2) //Replacing in y the titleNode
        
        planeNode.addChildNode(titleNode) //Adding the titleNode as child of the planeNode
        
        return node
    }
```

<h1><video src="/assets/img/out 2.MOV" controls="controls" style="max-width: 730px;"></video>
</h1>
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
        <img style="display: block; margin-left: auto; margin-right: auto; width: 100%; object-fit: contain" src="/assets/img/yo2.png">
        </td>
        <td style="border-top: 0px solid gray; border-left: 0px solid gray; border-right: 0px solid gray; border-bottom: 2px solid #DDDDDD; background-color: #FDFDFD; text-align: left; vertical-align: center; padding: 10px">
            <p style="font-size: 26px; margin: 0px 0px"><b>Javi Carrillo</b></p>
            <p style="font-size: 18px">In the past I developed car's powertrain control software. Now I develope mobile apps for many purposes.</p>
        </td>
    </tr>
</table>




