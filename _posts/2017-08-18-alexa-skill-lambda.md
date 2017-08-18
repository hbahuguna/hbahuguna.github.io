---
layout: post
title: "Simple Alexa Skill Using AWS Lambda"
date: 2017-08-18
---
To create an Alexa skill, you will need an Amazon Developer Account and an AWS account. AWS lambda supports a veriety of languages and there are libraries available for those languages, for the purposes of this post, we will be using Node.js as it has the good lambda support compared to other languages.

We will need Alexa SDK for<a href="https://nodejs.org/en/">Node.js</a> in order to start creating a skill. On your terminal run the following command to install alexa-sdk:

<code>npm install alexa-sdk --save</code>

We will create a home directory for our Alexa skill. Let us call our home directory simple_alexa_skill:

<code>mkdir simple_alexa_skill</code>

Now inside simple_alexa_skill we will create a src directory where we will keep our index.js and facts.js files. Navigate to src directory and create a file called <a href="https://github.com/hbahuguna/AlexaAIHistorySkill/blob/master/src/package.json">package.json</a> and paste the linked code and save. Now, you will need to run <code>npm install</code> command. This should create a <code>node_modules</code> directory inside src directory. This is how node.js attaches libraries to code.

index.js is where we handle user request and alexa response. In our facts.js we will keep a simple database of AI facts that we want to tell when a user asks for.
Linked is an example of facts array in <a href="https://github.com/hbahuguna/AlexaAIHistorySkill/blob/master/src/facts.js">facts.js</a> file.

Now we will take a look at <a href="https://github.com/hbahuguna/AlexaAIHistorySkill/blob/master/src/index.js">index.js</a> where the core logic resides. Open index.js and paste the code from the link.

We need to require 'alexa-sdk' and 'facts'. You can also add your app id. Next we create an array containing some utterances for Alexa to respond with for your skill. 

Most important part of index.js is handlers, where we write code to handle different intents. 
<pre class="highlight"><code>
var handlers = {
    'LaunchRequest': function () {
        this.emit('GetFact');
    },
    'GetNewFactIntent': function () {
        var factArr = this.t('FACTS');
        var randomFact = randomPhrase(factArr);
        var speechOutput = randomPhrase(this.t("GET_FACT_MESSAGE")) + randomFact;
        this.emit(':askWithCard', speechOutput, this.t("HELP_REPROMPT"), "Here's an Interesting AI Fact:", randomFact)
    },
    'GetFact': function () {
        var factArr = this.t('FACTS');
        var randomFact = randomPhrase(factArr);
        var speechOutput = randomPhrase(this.t("GET_FACT_MESSAGE")) + randomFact;
        this.emit(':tellWithCard', speechOutput, this.t("SKILL_NAME"), randomFact)
    },
    'GetNewYearFactIntent': function () {
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
</code></pre>
When a skill is first invoked, we need to handle the <code>LaunchRequest</code> intent. In our implementation we are simply calling the handler for <code>GetFact</code>, which will get us a random fact from facts.js. We should always add handlers for <code>AMAZON.HelpIntent</code>, <code>AMAZON.CancelIntent</code> and <code>AMAZON.StopIntent</code>. These intents are invoked when a user wants help for your skill, cancel the current operation and stop the skill respectively. Now, we also have some handlers for custom intents such as <code>GetNewFactIntent</code> and <code>GetNewYearFactIntent</code>. <code>GetNewFactIntent</code> is a handler for a case when user wants to keep the conversation open, so we use <code>:askWithCard</code> and <code>HELP_REPROMPT</code>. <code>GetNewYearFactIntent</code> is handler for an event when user asks a fact for a certain year. This is acheived using a slot. A slot is a variable which we need to define in the model for our Alexa skill in Amazon Developer Console. 

So it seems like we need to define a model for our skill. We will need to login to <a href="https://developer.amazon.com">Amazon Developer Console</a> and then navigate to Alexa tab and Alexa Skills Kit. Then we can Add a new skill. You will need to choose an appropriate name and invocation name. Keep everything else default. Click Save and click Next to navigate to Interaction Model step. In our example we will not use Skill Builder. Instead, we will just paste code from <a href="https://github.com/hbahuguna/AlexaAIHistorySkill/blob/master/speechAssets/IntentSchema.json">Intent Schema</a> in the Intent Schema text box. You will notice that we used a <code>slots</code> array for <code>GetNewYearFact</code> intent. So we will also need to add a slot named <code>FACT_YEAR</code> which has a built in type <AMAZON.FOUR_DIGIT_NUMBER></code>. You can add the following values for the <code>FACT_YEAR</code> slot:
<ul>nineteen hundred and fifty</ul>
<ul>nineteen hundred and fifty six</ul>
<ul>nineteen hundred and seventy four</ul>
<ul>nineteen hundred and ninety seven</ul>
<ul>two thousand and five</ul>
<ul>nineteen hundred and eighty</ul>
<ul>nineteen hundred and eighty seven</ul>
<ul>nineteen hundred and eighty two</ul>
<ul>two thousand and eleven</ul>
<ul>two thousand and sixteen</ul>

Now, you will also need to add some <a href="https://github.com/hbahuguna/AlexaAIHistorySkill/blob/master/speechAssets/SampleUtterances_en_US.txt">Sample Utterances</a>  for people to interact with skill. So please write your own utterances or copy from the link above. You will notice that utterances for <code>GetNewYearFactIntent</code> intent also contain <code>{FACT_YEAR}</code>. This tells the intent that the value at this particular place should be replaced for <code>FACT_YEAR</code>. Now, our Interaction Model is complete. We can Save and click Next to navigate to Configuration step.

We will get back to Amazon Developer Console so we will keep this open in a browser tab while we setup our Lambda function in the few next steps.

From the src directory of simple_alexa_skill directory we will zip index.js, facts.js and the node_modules directory together. This zip file will be used to upload to Lambda.

We will use an AWS Lambda ARN as service endpoint. In order to create a lambda we will need to login to <a href="https://console.aws.amazon.com">AWS console</a> and navigate to Lambda and click Create Function button. From the available <code>Blueprints</code> we will select <code>alexa-skill-kit-sdk-factskill</code> blueprint. In the Add Trigger box click the gray box to slect <code>Alexa Skills Kit</code> and click Next button. On the Configuration page you should provide a name and description for your skill. Scroll all the way down to <code>Lambda function handler and role</code> and select Create a custom role. And then use the instructions mentioned <a href="https://github.com/alexa/skill-sample-nodejs-fact/blob/master/lambda-role.md">here</a> to create a new custom role. Once the role is created we can use it and navigate to Review page by clicking Next button. On the Review page click Create Function button. Navigate to Code tab of the newly created Lambda function and from the Code Entry Type drop down, select Upload a .ZIP file option and upload the zip file we created in the previous step. We are now ready to use <code>ARN</code> on the top right from our Lambda function to Configuration page of Amazon Alexa Skill setup.

We can now navigate back to the browser tab where we had Configuration page of Alexa Skill open. We will select <code>AWS Lambda ARN (Amazon Resource Name)</code> and corresponding geographical region. In the given text box we will paste the ARN that we acquired from the Lambda we created in the previous step. We can keep all other fields as default on this page and click Next button to navigate to Test page. In the <code>Service Simulator</code> section we will enter a sample utterance containing a slot. Paste <code>tell me something interesting about two thousand and sixteen</code> in the Enter Utterance text box and click Ask button, and it should show request and response for the lambda function. Because we used <code>:askWithCard</code> in our handler, it should also show us a card for the response. At this point we have created and tested a simple conversational Alexa skill. We can go ahead and publish it by filling Publishing Information and Privacy and Compliance Sections.
