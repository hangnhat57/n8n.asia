+++
title = "Google Cloud Series: Compute Engine In Action"
description = ""
tags = [
    "cloud",
    "google",
    "gcp",
]
date = "2014-04-02"
categories = [
    "Cloud",
    "Google Cloud",
]
menu = "main"
+++



=============================================

**Objectives:**

_Deploy a highly scalable nodejs app on GCE._

**Prerequisites**:

Folk and clone this repo: [https://github.com/hangnhat57/compute-engine-101/](https://github.com/hangnhat57/compute-engine-101/)  
Create a service account which have Roles : Compute Instance Admin (v1) and Source Repo Writer

![](http://n8n.asia/wp-content/uploads/2018/09/Screen-Shot-2018-09-06-at-11.56.12-AM.png)

Create a service account 

**Deployment Strategy:**

1\. Create a startup script which will automatically get code, install dependencies then start web server right after instances start.  
2\. Create an instance template with startup script.  
3\. Create an instance managed group and enable autoscaling.  
4\. Create a load balancing to route traffic between instances of group.

**Actions:**

1.Open Google Web Console, then go to Source Repositories.  

2.Enable API for project, this could take a while

![](http://n8n.asia/wp-content/uploads/2018/09/Screen-Shot-2018-09-06-at-12.56.23-PM.png)

 Enable Source API for project  

3.Create a repository in CSR. It will give you 3 options how you save your code. For me, I mirror this repository with my github project.

![](http://n8n.asia/wp-content/uploads/2018/09/Screen-Shot-2018-09-06-at-12.59.09-PM.png)

Select connect to Github and select project for mirroring

Hold on, why we have to mirror Github repo to Google? Why don’t we directly use Github?  
In case your repository is private, when an instance start, it would require your credential to get code from repo. Because we use startup start, cloning source code to instance must be automatically with out any human interactive. This means you’ll have to store your ssh key or user/password in instance metadata / environment variable, and of course it’s not a good way. So we mirror to GSR then grant access permission for a service account which will be assigned to instance template.

4.On your local machine, open terminal then cd to project folder that you’ve cloned. 

5.Open startup.sh file, change <repo name> to your google repository name then save it.

![](http://n8n.asia/wp-content/uploads/2018/09/carbon-1.png)

6.Open create-ins-template.sh, change <project-id> to your project id. <service-account-name> to the service account that you’ve created in prerequisites step. Then execute it.

![](http://n8n.asia/wp-content/uploads/2018/09/carbon.png)

You can go to web console -> Compute Engine -> Instance templates section to create manually by Web UI also.

![](http://n8n.asia/wp-content/uploads/2018/09/Screen-Shot-2018-09-06-at-2.16.59-PM.png)

7.Next, we create a managed instances group. Open create-ins-group.sh and execute first script block.

![](http://n8n.asia/wp-content/uploads/2018/09/carbon-2.png)

Then active auto scaling for that group 

![](http://n8n.asia/wp-content/uploads/2018/09/carbon-3.png)

Now we have an instance group that have minimum 2 instances in region, will add or remove VMs to group if CPU usage reach 70%. Maximum instances number is 4.

8.Execute script in create-health-check.sh to create a http health check for Load Balancer 

![](http://n8n.asia/wp-content/uploads/2018/09/carbon-4.png)

9.Now on Web Console, go to Network services > Load balancing and click on Create load balancer. Select Config HTTP(s) load balancer.

![](http://n8n.asia/wp-content/uploads/2018/09/Screen-Shot-2018-09-06-at-2.51.19-PM.png)

10\. Create a new backend service with the instance group and health check we’ve just created.

![](http://n8n.asia/wp-content/uploads/2018/09/Screen-Shot-2018-09-06-at-3.07.47-PM.png)

Leave Host and Path rules, Frontend config by default, then Create Load balancer 

![](http://n8n.asia/wp-content/uploads/2018/09/Screen-Shot-2018-09-06-at-3.12.07-PM.png)

Load balancer would take a moment to set things up, then you can access your web app through load balancer external ip 

![](http://n8n.asia/wp-content/uploads/2018/09/Screen-Shot-2018-09-06-at-3.20.35-PM-2.png)

_And that’s it. You’ve deployed successfully a web server which always ready to scale up when needed. Autoscaler is turned on in the Instance Group governing the two instances. If there is an increase in usage (CPU in our example), the Autoscaler will spawn a new instance to handle the increase in traffic._

Posted on [6th September 20187th September 2018](http://n8n.asia/google-cloud-series-compute-engine-in-action/)Author [阮 日](http://n8n.asia/author/hangnhat57/)Categories [Cloud](http://n8n.asia/category/cloud/), [Development](http://n8n.asia/category/dev/), [DevOps](http://n8n.asia/category/devops/), [English](http://n8n.asia/category/en/)Tags [action](http://n8n.asia/tag/action/), [architect](http://n8n.asia/tag/architect/), [cloud](http://n8n.asia/tag/cloud/), [google](http://n8n.asia/tag/google/), [practice](http://n8n.asia/tag/practice/)