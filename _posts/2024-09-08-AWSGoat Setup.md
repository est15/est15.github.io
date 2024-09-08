---
layout: post
date: 2024-09-08
title: "Damn Vulnerable AWS (AWSGoat) Setup"
categories: [Setup]
tags: [Cloud,AWS]
img_path: /assets/AWSGoat-Setup/
render_with_liquid: false
---

## Damn Vulnerable AWS (aka AWSGoat) Setup
[Damn Vulnerable AWS](https://github.com/ine-labs/AWSGoat/tree/master) aka AWSGoat contains two (2) vulnerable by-design AWS infrastructures. This blog post is intended to part one of a two-part series. This post covers the installation and setup, and the second part is a walkthrough for the exploitation pathways for both modules 1 and 2.
![AWSGoat Logo](AWSGoat-Logo.png)
_AWSGoat Logo_

### Prerequisites
- GitHub Account
- AWS Account (free trial used for this blog)
- AWS Access Key Creation

## Installation 
I chose to go with the easy method of setup which requires forking the AWSGoat repository. The following walks through the necessary steps to install AWGoat. 

### 1. Fork AWSGoat
If you do not already have a GitHub account create one, then navigate to the [AWSGoat repository](https://github.com/ine-labs/AWSGoat/tree/master). Towards the top of the page select the "Fork" option.  
![AWSGoat Repo Fork](Forking Repo.png)
_GitHub: Forking AWSGoat Reposiotry_

Give the fork an appropriate name designation (it can be something entirely custom) and leave the "Copy the `master` branch only" option selected. Then continue by choosing the "Create fork" option in the bottom right corner. We should now have the entire AWSGoat main branch within our repository:
![My Own AWSGoat Repo](Personal AWSGoat.png)
_AWSGoat Forked Repo_

### 2. Create an AWS Account
For the initial setup with AWSGoat, I am going to be using AWS's free trial period, which as AWSGoat states `The resources created with the deployment of AWSGoat will not incur any charges if the AWS account is under the free tier/trial period.` This is perfect for experimenting with this instance and AWS in general. Navigate to the AWS "[Create an AWS Account](https://signin.aws.amazon.com/signup?request_type=register)" page and register your account. 
> **NOTE:** If you have already used your AWS free trial period then reference [AWSGoat Pricing](https://github.com/est15/est15-AWSGoat#pricing) which outlines the deployment costs.
{: .prompt-tip }

### 3. Generate AWS Keys
Once we have an AWS account registered the next step is to create an `AWS_ACCESS_KEY` and `AWS_SECRET_ACCESS_KEY` as these are crucial for the setup process.
- `AWS_ACCESS_KEY` = AWS access key associated with an IAM user or role, which is required to connect to Amazon Keyspaces programmatically. 
- `AWS_SECRET_ACCESS_KEY` = The Secret key associated with the AWS access key, which essentially acts as the password for the access key. This is also required to connect to Amazon Keyspaces Programmatically. 

Within the AWS Console in the top-right corner select *your account name* -> *security credentials* -> *Access Keys* -> *Create Access Key*:

![AWS Key Creation](Create Access key.png)
_AWS Access Key Creation_
> **NOTE:** When attempting to create an access key for the root user you will receive an alternatives to root user access key notification. Select the "I understand" checkbox and continue with the "Create access key" option. 
{: .prompt-info }

The reason we are ignoring this is because the AWSGoat project uses GitHub Actions, which for each terraform project GitHub assumes a role that gets only the permissions that particular project needs to create or maintain the infrastructure it manages[^1]. Copy the Access Key and Secret Access Key to a secure location. We will now see the newly created access key populated within the Security Credentials page. 
![Access Keys Generated](Access key Generated.png)
_Successful AWS Key Generation_

### 4. Set GitHub Action Secrets
Copy the two (2) AWS access keys we generated, and navigate to our forked AWSGoat repo. To add the Secrets navigate to *Settings* -> *Security* -> *Actions* -> *Repository Secrets* -> *New Repository Secret*. Create two (2) repository secrets, one for your `AWS_ACCESS_KEY` and one for your `AWS_SECRET_ACCESS_KEY`.
![Adding Repository Secrets](repository secrets.png)
_Adding Repository Secrets_
![AWS Key Repo Secrets](Action Secrets Created.png)
_AWS Keys Repository Secrets_

### 5. Run Terraform Apply Workflow
Once all of the above steps have been completed then we can run the `Terraform Apply` workflow. Within the forked AWSGoat repo navigate to *Actions* -> Terraform Apply -> *Run Workflow*. 
![Terraform Apply](Terraform Apply Workflow.png)
_Launching Terraform Apply Workflow_

The workflow gives the option to deploy either Module 1 or Module 2 which AWSGoat provides. Reference the [Modules](https://github.com/ine-labs/AWSGoat#modules) section to see a description for each of the two (2) modules. In this case, I opted to deploy Module 1. Once the workflow has been completed select it and navigate to Terraform completed Jobs and under the "Application URL" you will find the generated AWS URL for the AWSGoat application.
![AWSGoat Generated URL](AWSGoat URL.png)
_AWSGoat Instance URL_

The Damn Vulnerable AWS Infrastructure is now ready to be tested and exploited.
![AWSGoat Deployed](AWSGoat Deployed.png)
_AWSGoat Serverless Blog Application_

## Removing Deployed Module Infrastructure
AWSGoat also provides a `Terraform Destroy` workflow which will get rid of the previously deployed module. Within the forked AWSGoat repo navigate to *Actions* -> Terraform Destroy -> *Run Workflow* and choose the module that was previously deployed. Once the workflow completes the instance will no longer be accessible / deployed.   

---
## References
[^1]: dijitalmunky ~ Reddit - [How do you handle AWS permissions for terraform user?](https://www.reddit.com/r/Terraform/comments/wc5hsd/how_do_you_handle_aws_permissions_for_terraform/)
