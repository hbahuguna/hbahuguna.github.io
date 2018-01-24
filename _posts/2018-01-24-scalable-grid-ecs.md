---
layout: post
title: "Scalable Selenium Grid in ECS"
date: 2018-01-24
---

Selenium grid now provides an endpoint api/hub which tells us how many browser slots are available on a hub to run a test. We can leverage this information to scale up and down the number of selenium grid containers in an AWS ECS setup, based on the test requests. ECS Cloudformation template is an easy way to define your grid setup in AWS ECS. Once the template is ready, we can use AWS cli to actually create the grid setup on ECS. This particular template contains specific Roles, Tasks and Services for Selenium hub and node containers. In ECS we first define a task definition for a container that needs to be invoked, later we can use this task in a service to scale up and down the number of containers. We will need to define a task and service each for hub and node containers in our Cloudformation template. In our setup we will create one cluster each for hub and node containers. We will also need to build firefox and chrome docker images locally and then push them to a container registry, so that our cloudformation stack can be built using the loaction of that image on container registry.  

I found a git repo which has the required code and instructions on how to create and use selenium grid on ECS. The cloudformation template for ECS  and Docker files can be found on this <a href="https://github.com/RetailMeNotSandbox/ecs-selenium">repo</a>. I was able to create the stack using the instructions from this repo. Then I created a workstation node on same VPC as grid. I used this workstation node to invoke webdriver tests to use the grid created using the cloudformation template. When the tests run in parallel, the node containers are scaled up based on the test requirements. And there is a cron job on node host machine, that is run periodically to scale down if any node is not being used. 

