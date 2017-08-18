---
layout: post
title: "Simple Alexa Skill Using AWS Lambda"
date: 2017-08-18
---
To create an Alexa skill, you will need an Amazon Developer Account and an AWS account. AWS lambda supports a veriety of languages and there are libraries available for those languages, for the purposes of this post, we will be using Node.js as it has the good lambda support compared to other languages.

We will need Alexa SDK for Node.js in order to start creating a skill. On your terminal run the following command to install alexa-sdk:

npm install alexa-sdk --save

We will create a home directory for our Alexa skill. Let us call our home directory simple_alexa_skill:

mkdir simple_alexa_skill

Now inside simple_alexa_skill we will create a src directory where we will keep our index.js and facts.js files.

index.js is where we handle user request and alexa response. In our facts.js we will keep a simple database of AI facts that we want to tell when a user asks for.
Linked is an example of facts array in <a href="https://github.com/hbahuguna/AlexaAIHistorySkill/blob/master/src/facts.js">facts.js</a> file.

Now we will take a look at <a href="https://github.com/hbahuguna/AlexaAIHistorySkill/blob/master/src/index.js">index.js</a> where the core logic resides. Open index.js and copy the code from the link.

We need to require 'alexa-sdk' and 'facts'. You can also add your app id. Next we create an array containing some utterances for Alexa to respond with for your skill. 


