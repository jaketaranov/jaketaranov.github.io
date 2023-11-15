---
layout: page
title: SafetyNet
description: An application to help users return home safely 
img: assets/img/safetynet_pic.png
importance: 3
category: work
related_publications: 
---
[GitHub Repo](https://github.com/rayquaza7/SafetyNet)

As part of one of Western Canada’s largest annual hackathons hosted by UBC, my two teammates (Arthur Lee, Sarhaan Gulat) and myself conceived, prototyped, developed, and tested our project SafetyNet in 24 hours. The app was targeted towards individuals who may be entering a potentially risky situation, like going on a first date, hiking, walking late at night, etc. Users simply enter where they are going, where they will return to, and what time they will return. They then enter three contacts to notify if they do not return home by the specified time. In the event that they do not return home in time, the app will automatically send out texts to all their emergency contacts and let them know where they were planning on going,what time they planned on being back, along with their last known location which we store.

Our submission was awarded 2nd place in the hackathon.

<div align="center"><iframe id="player" width="720" height="360" src="https://www.youtube.com/embed/EYhCXC9rJ6E" frameborder="0" allowfullscreen></iframe></div>

### How we Developed the Application

Prior to writing the first lines of code, we as a team spent approximately two hours brainstorming ideas, drawing up prototypes, and providing feedback and critiques, until we decided that we wanted to commit to the idea of SafetyNet. We felt that it was a great chance to learn some new technologies while creating an app that has a real world practical use.

The application was developed in Swift. Considering we had zero experience with swift prior to this hackathon, it was a unique learning experience to say the least. Coming from a familiar language such as Python to Swift proved to have some difficulties. Amongst swift we conducted all our programming, editing, version controls, etc. in Xcode, which is the native iOS software development environment.

To achieve the results we wanted, we needed to employ the use of some API’s. This included the GoogleMaps API for autocomplete search box, location storing and tracking. Twilio SMS API for sending texts to the emergency contacts. And Alamofire for making HTTP requests in Swift. 

My responsibilities for the app were to develop the underlying logic in the application. This meant I wrote the code for

<ul>
    <li>obtaining and storing the user's location via GoogleMaps API</li>
    <li>pinging the user's location every x minutes to keep a crumb trail in case of search</li>
    <li>keeping track of the current time and the time the user is supposed to be back. i.e if the user is supposed to be back at time <i>x</i> in location <i>y</i> and we check at time <i>x</i> and see the user is in location <i>z</i>, we must send out a text to the emergency contacts</li>
    <li>Alamofire integration and network requests</li>
    <li>curating and sending texts via Twilio</li>

</ul>