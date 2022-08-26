---
layout: post
title: "Unit testing I: the Basics"
date:   2022-08-15 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding
---
> # Throughout this post you will learn:
>
> - What a Unit Test is and why it is so important
> - Basic implementation with Swift native test framework

<br>
<h3 style="color: #403F3F">What's a Unit Test?</h3>

Basically a Unit Test is a piece of code that given an input and an expected output returns wether the real output matches the expected one or not. And what's the thing that turns the input into an output? Well, the entity that is been tested.

You may be wondering why doing this? What's the point of unit testing? The answers to those questions are difficult to see at the first stages of your career as a developer, never mind if more experiencied colleagues warn you about the importance of Unit Testing, you always dismiss them. But let me tell you that if you want to become a developer or software engineer who values gode code and want to gain piece of mind, unit testing is a must.

Testing the code also helps you follow the developments and keep up with new features and funcionalities that are implemented by team mates for instances, so Unit Testing not only serves to the piece of software that is being developed, it also helps you as a developer on a daily basis.

<br>
<h3 style="color: #403F3F">Let's get started!</h3>

The first thing we need to do is to add a Unit Test target to our Xcode project. When we set up a project from scratch there's an option called "Include Tests" that is marked by default. But in case you do not find any target related with tests you can create it by going to Test Navigation Tab inside the Xcode Project Navigator and press the plus button down below.

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 8px" src="/assets/img/XcodeNavigator.png"></h1>

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 8px" src="/assets/img/AddUnitTest.png"></h1>

Once you have followed the instructions you will realice that a new target has been added to the project and that a new class, that inherits from `XCTestCase`, has been created. `XCTest` is the Swift native framework for testing purposes, and `XCTestCase` is it main class. Two methods are also created by default: `setUpWithError()` which is called before each test function is called in order to set the prerquisities, and `tearDownWithError()` which is called after each test function is performed in order to clean any debris that could be originated when running the test.

You can create as many test functions as you want, but bear in mind that XCode only will detect a new function as a proper test if you use the prefix **test** in the function name. Let's see an example:

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 8px" src="/assets/img/TestNoTest.png"></h1>

As you can see when Xcode detects a new test function it places a diamond figure on the left. You can launch each test function by clicking that diamond. But if you want to launch all the test I recommend using the shortcut ⌘ + U.

Distribution and naming of unit test are also important. Basically Apple suggest to create as `XCTesCase` class per each functionality you want to test, and within the class to name each test function with a clear description of what is being tested with that function.

Basically a test compares a expected result with the real result the test function returns. If both results match then the test <span style="color: green">**"Succeeded"**</span>, if not the test <span style="color: red">**"Failed"**</span>. The way to tell the test function which is the expected result is by means of `Assertions`. `XCTest` brings a bunch of assertions types, but the most commony used ones are `XCTAssertTrue`, `XCTAssertFalse`, `XCTAssertEqual`, `XCTAssertNotEqual`, `XCTAssertNil` and `XCTAssertNotNil`. As I mentioned `XCTest` comes with lot of assertions but many developers also prefer to use third party expectations, like the ones provided by [Nimble](https://github.com/Quick/Nimble).


<br>
<h3 style="color: #403F3F">Summing Up</h3>

- Unit Testing not only is a good way to build good code but also a great tool to remember what's going with the code and to keep up with the developments.
- Swift native Unit Test framework is called `XCTest` and to build a new test method the function name must have the prefix **test**.
- A Unit Test is like a function with two inputs: the object to be tested and the expected result of the test (`XCTAssert`). If the real result matches the expected one then the test <span style="color: green">**"Succeeded"**</span>, if not the test <span style="color: red">**"Failed"**</span>.
- The expected result of a test function is defined by means of assertions like `XCTAssertTrue`, `XCTAssertFalse`, `XCTAssertEqual`, `XCTAssertNotEqual` ... or the expectations provided by third party libraries like [Nimble](https://github.com/Quick/Nimble).


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
            <p style="font-size: 18px">In the past I developed car's powertrain control software. Now I develope software for Apple  platforms.</p>
        </td>
    </tr>
</table>




