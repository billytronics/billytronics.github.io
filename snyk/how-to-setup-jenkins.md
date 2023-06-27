---
layout: snyk
title: How to setup Snyk in a Jenkins Pipeline
---

# Introduction

This is a step by step guide to setting up Snyk to run security tests in a Jenkins Pipeline.
The intention is to show how to get started, what's possible and it is up to you to customize it to your needs.
In this walkthrough, we will add Snyk Code (SAST) and Snyk Open Source (SCA) testing to a dummy Jenkins job.

The Jenkins used in this example is a local install on v2.379. Note that some of the steps may differ if you are using a different version of Jenkins.

## Prerequisites

Before getting started, we will need to install the following plugins, by going to:
> Manage Jenkins > Plugin Manager > Search Available Plugins

- [Pipeline](https://plugins.jenkins.io/workflow-aggregator/)
  - For more info, see: [Pipeline as Code with Jenkins](https://www.jenkins.io/solutions/pipeline/)
 
- [Warnings Next Gen](https://plugins.jenkins.io/warnings-ng/)
  - This plugin allows Jenkins to process SARIF outputs natively in the results
 
- [HTML Publisher](https://plugins.jenkins.io/htmlpublisher/)
  - This plugin is required to publish the HTML reports directly inside the Jenkins job
 
- [NodeJS](https://plugins.jenkins.io/nodejs/)
  - NodeJS is needed to install the Snyk CLI to run the tests
  - You need to define the version and name of the NodeJS installation, do this at:
  > Manage Jenkins > Global Tool Configuration
  
![Jenkins NodeJS Plugin](/assets/images/snyk/how-to-setup-jenkins-1.png)

## Setup Snyk

If you don't already have a Snyk account, you can get one here: [Getting Started](https://docs.snyk.io/getting-started/quickstart/create-a-snyk-account)

1. First, we need to add the Snyk Token to the Jenkins Credential Store so we can authenticate the Snyk CLI:
> Manage Jenkins > Manage Credentials > System > Global credentials > Add Credentials

- Add a ```Secret text```, name it ```SNYK_TOKEN``` in the ID field and enter the token into the Secret field
  - You get the token from the Snyk UI, this is what your Jenkins CLI will be using.
    - I recommend creating a service account for this, under Settings (*requires a Snyk Enterprise License*)
    - Alternatively, you can use your account's API token, under your Account Settings

2. Next, we need to enable Snyk Code for your Snyk Organization if not already enabled:
> Settings > Snyk Code > Enable Snyk Code

Note, this is not enabled by default because Snyk will be accessing your 1st-party code here in order to scan it for security vulnerabilities

## Setup Jenkins Job

In this example, we are going to use a ```Pipeline script from SCM```, by defining the script in a Jenkinsfile which will be pulled by the Jenkins job.
This way, your changes are version controlled and allows for better collaboration.
Alternatively, you can simply put your script inside the ```Pipeline script``` inside the Jenkins job configurations.

I recommend creating a dummy job to test this out before adding Snyk tests to any of your existing jobs.
Follow the steps below to give this a go:

1. Create a new Jenkins job:
> New Item > Enter a name + Select Pipeline type > Proceed  

2. Define Jenkins job configurations:
- Select ```Pipeline script```
  - Paste your pipeline script code inside the Script section as such:
    
![Jenkins Pipeline Script](/assets/images/snyk/how-to-setup-jenkins-2.png)

- OR Select ```Pipeline script from SCM```
  - Create a Jenkinsfile at the root of your repo and enter ```Jenkinsfile``` as the Script Path field:
    
![Jenkins Pipeline Script from SCM](/assets/images/snyk/how-to-setup-jenkins-3.png)

Refer to this example Jenkinsfile to create your own definition:
https://github.com/billytronics/goof/blob/jenkins/Jenkinsfile

## Run Jenkins job & Verify HTML Reports

If your job runs successfully and is able to produce the below reports then congrats, you have made it!
Verify the HTML reports look like the below:

![Snyk HTML Report 1](/assets/images/snyk/how-to-setup-jenkins-4.png)

![Snyk HTML Report 2](/assets/images/snyk/how-to-setup-jenkins-5.png)

If your reports do not look like this, then your Jenkins is preventing the CSS from loading properly.
This is due to the elevated security settings in Jenkins that is blocking such processing.

To fix this, we need to relax the security settings. However, if the below command is ran once, then it will revert to the previous setting after Jenkins restart.
In order to ensure the changes are persisted, we will need the ```Startup Trigger``` plugin.
Note - if your Jenkins instance is managed by a separate team at your organization, you should work with them on this issue.

1. Install the following plugin: [Startup Trigger](https://plugins.jenkins.io/startup-trigger-plugin/)
2. Create a new Jenkins job and call it something like: ```Jenkins Startup Triggers```
- Configure the ```Build Triggers``` and select ```Build when job nodes start```
  
![Jenkins Build Triggers 1](/assets/images/snyk/how-to-setup-jenkins-6.png)

- Select ```Execute system Groovy script``` and paste the following code into the script section:
``` groovy
System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "default-src 'self' 'unsafe-inline' 'unsafe-eval'; img-src 'self' 'unsafe-inline' data:;")
```
  - *Note: Make sure you select the system groovy script and not the generic one as the groovy plugin is not available at startup*

![Jenkins Build Triggers 2](/assets/images/snyk/how-to-setup-jenkins-7.png)

## Analyze the Results

By now, you should have a fully working Jenkins job running SAST & SCA security testing on your source code.
Below I will summarize the findings with a little bit of context:

TBD
