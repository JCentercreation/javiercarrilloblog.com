---
layout: post
title: Managing MySQL database with Python
date:   2021-02-15 12:02:05 +0100
categories: coding
author: Javier Carrillo
permalink: /:categories/:day/:month/:year/:title.html
published: true
tags: coding tech
---
Hey guys, today I wanna show you how to link your mySQL server with your Python application. This is very convenient when it comes to handle with a great amount of data that needs to be stored. As you may know mySQL is one of the most widely used structured database in the world (in deed now it is P2 in the <a href="https://db-engines.com/en/ranking">ranking</a>.

MySQL is not a pure coding language but we could consider it as that. Basically a database is composed of a server where the info is stored, so to access the info, read it or update it we do that by means of queries (SQL stands for Structured Query Language). There are many external SQL management tools to perform that queries (I usually use PoPSQL) but in the most of the cases a database is just a part of a bigger application or system so linking the database with our api is much more handy than doing it aside. And here it is where Python plays his part, so lets go for it.

Please take into account that in order to perform the following steps you will need to still running a local mysql server on your computer.

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 10px" src="https://jcentercreation.github.io/JekyllPersonalWeb/assets/img/Captura de pantalla 2021-04-21 a las 16.42.21.png"></h1>


The programming tools we need are:

- A recent Python version. I’m currently using Python 3.9.3.
- Database driver → This will allow us to connect the database system with the application. Each programming language has its own database driver, so we are gonna use **MySQL Connector/Python**.

### Connecting with MySQL server

Let's imagine that we want to manage a database already created. Well, basically there are four parameters we must pass to the database driver in order to perform the connection with the server:

- Server host
- Username
- Password
- Database id

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 10px" src="https://jcentercreation.github.io/JekyllPersonalWeb/assets/img/Captura de pantalla 2021-04-21 a las 16.44.06.png"></h1>

*Connect()* method will transform the connection into an object.

### Reading from the database

In order to reed from the database first we need to set the mySQL query into a python variable. Then we set a cursor method and execute it passing the query variable. Finally we need to store the result (info inside the database server) into a variable and print it.


<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 10px" src="https://jcentercreation.github.io/JekyllPersonalWeb/assets/img/Captura de pantalla 2021-04-21 a las 16.43.37.png"></h1>


### Updating the database

Obviously it is also possible to modify the database. Lets imagine that we want to update one of the database tables. In this case we use *commit* method instead of *fetch* *all* one.

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 10px" src="https://jcentercreation.github.io/JekyllPersonalWeb/assets/img/Captura de pantalla 2021-04-21 a las 16.44.31.png"></h1>

Finally this is what we will get into the terminal:

<h1><img style="display: block; margin-left: auto; margin-right: auto; width: 100%; border-radius: 10px" src="https://jcentercreation.github.io/JekyllPersonalWeb/assets/img/Captura de pantalla 2021-04-21 a las 18.50.48.PNG"></h1>


Take a look to my GitHub <a href="https://github.com/JCentercreation/MySQLPython/blob/main/mySQLConnection.py">repository</a> to see full code.