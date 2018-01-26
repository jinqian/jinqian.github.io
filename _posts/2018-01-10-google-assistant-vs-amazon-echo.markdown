---
layout: post
title: Google Assistant v.s. Amazon Alexa
comments: true
categories: tech
date: 2018-01-26 19:00:00
---

I've recently animated a [Hands-On](https://github.com/xebia-france/hands-on-assistant) 
with my coworker on voice assistant platforms. We chose [Google Assistant](https://assistant.google.com/) and [Amazon Alexa](https://developer.amazon.com/alexa), which represents the state of art in the consumer market. This article provides comparisons of these 2 platforms and insights in term of core concepts, design tools, SDK usage and the integration.

# Overview

## Google Assistant

When talking about Google Assistant, developers tend to mix up different concepts:

- [**The Google Assistant**](https://assistant.google.com/): A conversation between you and Google that helps you get more done in your world.
- [**Google Home**](https://store.google.com/product/google_home): an assistant-enabled smart speaker. Android phones also have the Google Assistant enabled.
- [**Actions On Google**](https://developers.google.com/actions/): The platform that enables developers to build apps for the Google Assistant. Currently, you can use [Dialogflow](https://dialogflow.com/), [Actions SDK](https://developers.google.com/actions/sdk/) & [Converse.ai](http://www.converse.ai/) to create custom app.

The entry point for developers to create applications for Google Assistant is the platform **Actions on Google**. In this article, we'll focus on how to build an app with DialogFlow, which is the preferred choice of building an Assistant app. It handles much of the heavy lifting of understanding natural language and parsing the data one need from user input.

## Amazon Alexa 

Alexa is Amazon’s cloud-based voice service available on tens of millions of devices from Amazon and third-party device manufacturers. Amazon currently owns a line of Echo products:
![amazon-echo-family](/assets/image/20180110/amazon-echo-family.png)

- Echo 1st & 2nd generation
- Echo Dot
- Echo Plus, Echo with the built-in smart home hub.
- Echo Spot, compact Echo with a screen
- Echo Show, smart speaker with touchscreen
- Echo Look, hands-free camera & style assistant
- Echo Tap, Alexa-Enabled Portable Bluetooth Speaker

[The Alexa developer portal](https://developer.amazon.com/alexa) is your entry point to create an Alexa skill, you will probably need an [AWS](https://aws.amazon.com/) account to go through the build process.

# Build / Design Tools

## DialogFlow v.s. Interaction Model Builder

DialogFlow (formerly api.ai) is a speech interaction builder, the preferred choice for building an Assistant app:

![dialogflow](/assets/image/20180110/dialogflow.png)

Alexa currently has a beta version of Skill Builder for designing the interaction model (Intents, Slots, and Dialogs) for a Skill:

![alexa-skill-builder](/assets/image/20180110/alexa-skill-builder.png)

We can see much resemblance between two tools.

# Core Concepts

Two platforms use different technical terminologies to address their core concepts.

## App v.s. Skill

Google didn't bother and went with the word **App**. Amazon, on the other hand, decides to call the application of Alexa as **Skill**. 

## Intent

The essential part of designing a conversational experience is to identify user's intentions. Both platforms emphasize the importance of **Intent** design. Amazon refers frequently what user says as "utterance" in their documentation. 

For both platforms, there are well defined built-in intents such as greeting intent or intents to control music play. 

Design guides and best practices are also provided by both platforms:

- Actions on Google: [The Conversational UI and Why It Matters](https://developers.google.com/actions/design/)
- Alexa: [Voice Design Guide](https://developer.amazon.com/designing-for-voice/)

## Entity v.s. Slot

The variable in what user says is defined as an **Entity** in DiaglogFlow and a **Slot** in Alexa Skill's vocabulary. You can use either built-in types (e.g. city,  date, color, address) or customize your own types on both platforms. 

For example in a sentence like this:
```
I want to book a flight from Paris to Rome for next Friday.
```

You would identify **Paris** and **New Yorks** as `@sys.geo-city` in DialogFlow and `AMAZON.EUROPE_CITY` in Alexa.

# Fulfill user's intention

You can call it your application's business logic, or your skill's backend logic. The ultimate objective is to give what user want from the application, either it's booking a train ticket or getting to know the match result of their favorite football team.

- For Google Assistant, if you are using DialogFlow, you don't have the obligation to deploy a backend and provide a webhook endpoint. You can simply rely on the tool's built-in features to create something quite satisfying.
- As for Alexa, you have to implement a web service that accepts requests from and sends responses to the Alexa service in the cloud for a custom skill.

## DialogFlow Node.js example

Here is a simple Hello World example using [DialogFlow's Node.js SDK](https://developers.google.com/actions/reference/nodejs/DialogflowApp), with firebase 
function:

```javascript
const { DialogflowApp } = require('actions-on-google');
const functions = require('firebase-functions');

const helloWorld = app => {
  const foo = app.getArgument(Parameters.FOO);
  app.tell("Hello you! Did you just say:" + foo)
}

const Actions = {
  HELLO_WORLD: 'hello.world'
}

const Parameters = {
  FOO: 'foo'
};

const actionMap = new Map();
actionMap.set(Actions.HELLO_WORLD, helloWorld);

// HTTP Cloud Function for Firebase handler
exports.recipeassistant = functions.https.onRequest(
  (request, response) => new DialogflowApp({request, response}).handleRequest(actionMap)
);
```

## Alexa Node.js SDK example

Alexa has similar API in term of handling intents & emitting responses. Here is a simple snippet on how to give a random cat fact:

```javascript
const Alexa = require('alexa-sdk');

exports.handler = function(event, context, callback) {
    var alexa = Alexa.handler(event, context);
    alexa.appId = "APP_ID";
    alexa.registerHandlers(handlers);
    alexa.execute();
};

const handlers = {
    'LaunchRequest': function () {
        this.emit('GetNewCatFactIntent');
    },
    'GetNewCatFactIntent': function () {
        this.response.speak("Here's your fact: The average cat sleeps 16-18 hours per day.");
        this.emit(':responseReady');
    }
}
```

Apart from Node.js client, both platforms also have python, java and many more clients in different programming languages. You can surely find the one you are familiar with and start building your solution easily.

# Serverless Integration

It's not surprising that each platform favors their own serverless solution for the integration choice. 

- For DialogFlow, the integration with [Firebase solutions](https://firebase.google.com/) (Cloud Functions, Real-time Database, etc...) are almost seamless thanks to Firebase command line tools. There is also an inline editor powered by Firebase Cloud Functions which allows you to start building right away.
- For Alexa, if you are familiar with [AWS Lambda](https://aws.amazon.com/lambda/), 
the whole integration process would just a piece of cake. You just have to past the AWS Lambda ARN in your Alexa skill, then the endpoint is all set.

# Conclusion

This article is not able to cover all the aspects of both platforms but it gives brief ideas on how things are done till this point. Both platforms have reached a certain level of maturity but there is still a long way to go on popularizing the design and development of application for conversational experience.

Give the [Hands-on](https://github.com/xebia-france/hands-on-assistant) a try or find some codelabs or example to play with. These should get you started on creating some cool vocal assistant use cases.
