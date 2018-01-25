---
layout: post
title: "Chromeless on AWS Lambda"
date: 2018-01-25
---


Chromeless is a project that leverages headless chrome and works with serverless to run tests on AWS Lambda. Major benefit of this approach is that you can invoke a lambda on demand when a test is run. 

<a href="https://github.com/graphcool/chromeless">Chromeless</a> requires Node version 8.2 . You can clone <a href="https://github.com/graphcool/chromeless">Chromeless</a> from their git repo, cd into chromeless directory and run npm install.  Also, if NODE_PATH variable is not set,  you will need to set NODE_PATH variable to npm home directory using the following cmd.

<pre class="highlight"><code>
export NODE_PATH=`which npm|sed -e "s/bin\/npm/lib\/node_modules/g"`
</code></pre>

After setting NODE_PATH variable node should be able to find chromeless. 

Next thing, you will need to set CHROMELESS_ENDPOINT_API_KEY and CHROMELESS_ENDPOINT_URL environment variables in order to run test on AWS lambda.

<pre class="highlight"><code>
export CHROMELESS_ENDPOINT_API_KEY=your_api-key
export CHROMELESS_ENDPOINT_URL=endpoint_url
</code></pre>

Once these variables are set you can run the exapmle test in chromeless repo using the following cmd:

<pre class="highlight"><code>
node examples/extract-google-results.js
</code></pre>

This should result a list of google search results and corresponding hrefs. If you get the results back it means you are now set to run chromeless tests remotely on AWS lambda. Voila! 
 
For next step you can execute lambda tests in paralllel by using concurrently.

<pre class="highlight"><code>
npm install concurrently -g
concurrently "node examples/extract-google-results.js" "node example/twitter.js"
</code></pre>
