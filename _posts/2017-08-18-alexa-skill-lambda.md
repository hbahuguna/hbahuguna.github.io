---
layout: post
title: "Simple Amazon Alexa Skill Using AWS Lambda"
date: 2017-08-18
---
To create an Alexa skill, you will need an Amazon Developer Account and an AWS account. AWS lambda supports a veriety of languages and there are libraries available for those languages, for the purposes of this post, we will be using Node.js as it has the good lambda support compared to other languages.

We will need Alexa SDK for Node.js in order to start creating a skill. On your terminal run the following command to install alexa-sdk:

npm install alexa-sdk --save

We will create a home directory for our Alexa skill. Let us call our home directory simple_alexa_skill:

mkdir simple_alexa_skill

Now inside simple_alexa_skill we will create a src directory where we will keep our index.js and facts.js files.

index.js is where we handle user request and alexa response. In our facts.js we will keep a simple database of AI facts that we want to tell when a user asks for.
Below is an example of facts array in facts.js file :

module.exports = {
    "FACTS_EN": [
        "The field of AI is considered to have its origin in 1950, with publication of British mathematician Alan Turing's paper, Computing Machinery and Intelligence.",
        "The term, Artificial Intelligence, was coined in 1956 by mathematician and computer scientist John McCarthy, at Dartmouth College, in New Hampshire.",
        "In 1997, a chess-playing program named Deep Blue, developed by IBM, beat the reigning world chess champion.",
        "A so-called AI Winter occurred in 1974, when funding was cut after Speech Understanding research did not live up to its promise.",
        "A driverless robotic car named Stanley, engineered by Sebastian Thrun's Stanford Racing team, sped through the Mojave desert at 22 miles per hour to win the 2005 Darpa Grand Challenge.",
	"Between 1980 and 1987, a form of AI program called expert systems was adopted by corporations around the world and knowledge became the focus of mainstream AI research",
	"Around 1982,  David Rumelhart popularized a new method for training neural networks called backpropagation, which revived the field of connectionism",
	"In February 2011, in a Jeopardy! quiz show exhibition match, IBM's question answering system, Watson, defeated the two greatest Jeopardy! champions, Brad Rutter and Ken Jennings, by a significant margin",
	"New York Times reported in 2005: Computer scientists and software engineers avoided the term artificial intelligence for fear of being viewed as wild-eyed dreamers",
	"In 2016, Google's Deepmind AlphaGo AI defeated 18-time world Go champion Lee Sidol by 4-1",
    ]
}


Now we will take a look at index.js where the core logic resides. Open index.js and copy the following code :

'use strict';
var Alexa = require('alexa-sdk');
var APP_ID = 'APP_ID';  // can be replaced with your app ID if publishing
var facts = require('./facts');
var GET_FACT_MSG_EN = [
    "Here's your fact: ",
    "Following is your fact: ",
    "Here's an interesting fact: ",
    "Following is an interesting fact for you: ",
    "Got an interesting fact for you: "
]

var languageStrings = {
    "en": {
        "translation": {
            "FACTS": facts.FACTS_EN,
            "SKILL_NAME": "My History Facts",  // OPTIONAL change this to a more descriptive name
            "GET_FACT_MESSAGE": GET_FACT_MSG_EN,
            "HELP_MESSAGE": "You can say tell me a fact, or, you can say exit... What can I help you with?",
            "HELP_REPROMPT": "What can I help you with?",
            "STOP_MESSAGE": "Goodbye!"
        }
    }
};

exports.handler = function (event, context, callback) {
    var alexa = Alexa.handler(event, context);
    alexa.APP_ID = APP_ID;
    // set a test appId if running the mocha local tests
    if (event.session.application.applicationId == "mochatest") {
        alexa.appId = APP_ID_TEST
    }
    // To enable string internationalization (i18n) features, set a resources object.
    alexa.resources = languageStrings;
    alexa.registerHandlers(handlers);
    alexa.execute();
};

var handlers = {
    'LaunchRequest': function () {
        this.emit('GetFact');
    },
    'GetNewFactIntent': function () {
        var factArr = this.t('FACTS');
        var randomFact = randomPhrase(factArr);

        // Create speech output
        var speechOutput = randomPhrase(this.t("GET_FACT_MESSAGE")) + randomFact;
        this.emit(':askWithCard', speechOutput, this.t("HELP_REPROMPT"), "Here's an Interesting AI Fact:", randomFact)
    },
    'GetFact': function () {
        // Get a random fact from the facts list
        // Use this.t() to get corresponding language data
        var factArr = this.t('FACTS');
        var randomFact = randomPhrase(factArr);

        // Create speech output
        var speechOutput = randomPhrase(this.t("GET_FACT_MESSAGE")) + randomFact;
        this.emit(':tellWithCard', speechOutput, this.t("SKILL_NAME"), randomFact)
    },
    'GetNewYearFactIntent': function () {
        //TODO your code here
        if (this.event.request.intent.slots && this.event.request.intent.slots.FACT_YEAR && this.event.request.intent.slots.FACT_YEAR.value) {
            console.log("Found " + this.event.request.intent.slots.FACT_YEAR.value);
            var year = this.event.request.intent.slots.FACT_YEAR.value;
            var yearFact = GetYearFact(this.t('FACTS'),year);
            var speechOutput = randomPhrase(this.t("GET_FACT_MESSAGE")) + yearFact;
            this.emit(':askWithCard', speechOutput, this.t("HELP_REPROMPT"), "Here's an Interesting AI Fact About " + year + ":", yearFact)
        }
    },
    'AMAZON.HelpIntent': function () {
        var speechOutput = this.t("HELP_MESSAGE");
        var reprompt = this.t("HELP_MESSAGE");
        this.emit(':ask', speechOutput, reprompt);
    },
    'AMAZON.CancelIntent': function () {
        this.emit(':tell', this.t("STOP_MESSAGE"));
    },
    'AMAZON.StopIntent': function () {
        this.emit(':tell', this.t("STOP_MESSAGE"));
    }
};

function GetYearFact(phraseArr,year){
    for(var i=0;i<phraseArr.length;i++){
        var phrase = phraseArr[i];
        if(phrase.includes(year)){
            return phrase;
        }       
    }
    var alternativeFact = "fact for year " + year + " is not available at this time. Here is an alternative fact: ";
    return alternativeFact + randomPhrase(phraseArr);   
}

function randomPhrase(phraseArr) {
    // returns a random phrase
    // where phraseArr is an array of string phrases
    var i = 0;
    i = Math.floor(Math.random() * phraseArr.length);
    return (phraseArr[i]);
};

