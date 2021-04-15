---
layout: post
title: Formula 1 Web Scraping
date:   2021-04-18 12:02:05 +0100
categories: books
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: tech
---
Hi guys welcome to the channel. If you are new here my name is Javier Carrillo, I'm a Powertrain Development Engineer based in Spain. Today I want to show you how to build an easy routine to automate your tasks related to web scrapping so you could save much time. And we are going to apply those concepts to a real case.

Well basically what we are gonna do today is building a small program which is going to send an email to our inbox with the latest results of Formula 1, which is my favourite sport. So for that purpose we are will divide that final goal into four small routines:

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0d9beb89-bb82-473b-b133-5924e59cea2f/ProgramExplanation.001.jpeg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0d9beb89-bb82-473b-b133-5924e59cea2f/ProgramExplanation.001.jpeg)

The programming tools we will need are:

- [ ]  A recent Python version. I'm currently using Python 3.9.3
- [ ]  Following Python modules:
    - [ ]  "requests" module: in order to get the URL.
    - [ ]  Beautiful Soup module: which will allow us to perform the webscrapping
    - [ ]  "smtplib" module: to work with mailing stuff

I'm not gonna spend any time explaining how to install those modules cause I guess you all ready know how to do it. If you are a very beginner with Python no worries, just make a quick search in the internet about how to use PIP, the Python Package Installer manager. It's very straight forward.

Ok, lets dive into it:

- WebAvailability.py

    Basically inside this module we will define the URL we want the program take the information from. This variable obviously is vital. In this case I just made a quick if else rule to define it based on how the webpages are routed inside the official Formula 1 website and taking into account which is the real date the program is been used so the email contains the latest race results. But if you just want to make it easy at the first attempt in order to test your code quickly this will be enough:

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6b1cc296-2c06-4787-9db8-d4f5cce286b8/Captura_de_pantalla_2021-04-15_a_las_22.20.11.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6b1cc296-2c06-4787-9db8-d4f5cce286b8/Captura_de_pantalla_2021-04-15_a_las_22.20.11.png)

    Inside this module the URL availability is also checked so we could skip the program if the requested URL is not available yet for whatever reason. This is our first web scrapping activity so we ask Beautiful Soup to do it. It will return a true/false state.

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/866a6b0c-5a7e-44c1-ad3c-f4fa03b404c5/Captura_de_pantalla_2021-04-15_a_las_22.18.23.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/866a6b0c-5a7e-44c1-ad3c-f4fa03b404c5/Captura_de_pantalla_2021-04-15_a_las_22.18.23.png)

- WebScraping.py

    All right, so this is the module where we are going to properly get the information from the webpage. And Then we are gonna write that information into a .txt file. Take a look to the whole code:

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0300693a-480c-4e66-8af9-dcb29ef578d6/Captura_de_pantalla_2021-04-15_a_las_22.28.14.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0300693a-480c-4e66-8af9-dcb29ef578d6/Captura_de_pantalla_2021-04-15_a_las_22.28.14.png)

- EmailDelivery.py

    Inside this module we are going to built the connection with the mailing server, create the email and to send it.

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9ab02c71-3c12-4f49-b645-65662cb6c8d9/Captura_de_pantalla_2021-04-15_a_las_22.53.58.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9ab02c71-3c12-4f49-b645-65662cb6c8d9/Captura_de_pantalla_2021-04-15_a_las_22.53.58.png)

- Main.py

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d6b213c7-56c0-46aa-bae3-843e79f55760/Captura_de_pantalla_2021-04-15_a_las_23.09.16.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d6b213c7-56c0-46aa-bae3-843e79f55760/Captura_de_pantalla_2021-04-15_a_las_23.09.16.png)