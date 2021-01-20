---
title:  "Leaking AWS S3 Credentials"
date:   2020-11-18 00:00:00 +0000
categories: azure aws s3 security
tags:
    - aws
    - s3
    - security
classes: wide
toc: true
header: 
    overlay_image: "https://www.ans.co.uk/app/uploads/2019/08/Home-protection.jpg"
    overlay_filter: rgba(0, 0, 0, 0.4)
published: true


---

# Leaking AWS S3 Credentials: An Experiment

I have been working with AWS on an off for just over a year, enough to navigate round the console/SDKs, deploy resources via the portal or IaC and troubleshoot issues.

One thing that has been in the [news](https://www.computerweekly.com/news/252491842/Leaky-AWS-S3-bucket-once-again-at-centre-of-data-breach) a lot over the past couple years is the number of compromised S3 (Simple storage service) buckets due to incorrectly configured access.

This got me thinking (inspired by a medium article), what if I purposefully leaked the credentials to an S3 bucket? What would happen? How long would it take before someone tried to login? 



## The Approach

The simplest way I thought to “leak” the credentials would be to “accidentally” push them to a public [git repository](https://www.geeksforgeeks.org/what-is-a-git-repository/) (almost everyone who just read that will have cringed at the idea of commiting secrets to git) 

Unlike the medium article which had a genuine use for a read-only public S3 bucket, I didn’t so I had to come up with a name and codebase. 

So, I built a simple containerised app using python to download blobs from the bucket which arn't prefixed with blob_ and reupload them with correct prefix. 

To make it more realistic that I “forgot” to gitignore the file, I stored the access id and key in a file called .env and used environment variables inside the script instead of hard-coding the variables

 ```
--- .env ---
AWS_ACCESS_KEY_ID=MX4E7UBE57F6PQ9ARFD
AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYdefMwPpaqwv
AWS_DEFAULT_REGION="eu-west-1"
SRC_BUCKET="s3-corp-file_share291"
DEST_BUCKET="s3-corp-file_share291"
FILE_PREFIX="blob_"
 ```

>  Note: On *nix based systems "dotfiles" are usually hidden by deafult



## Preparing AWS

AWS has an access control framework called IAM (Integral Access Control), not to be confused with AMI (Amazon Machine Image), 

Off topic rant…

> Why can’t Amazon just have a simple naming convention like Azure. 
>
> Most people would be able to jump into Azure and spin up a Web App just by searching for Web App in the search bar, but AWS like to go the extra mile with their services, so they called their Serverless WebApp service, Elastic Beanstalk. What was wrong with calling it Web Apps like Azure 

Right, back to the task at hand 

IAM allows you to configure policies and roles written in JSON (JavaScript Object Notation) to control how users/api accounts interact with your AWS account.

In this test I wrote a policy that gave an API user list buckets, get objects (files), put objects and delete objects. 

IAM policies can quickly become hard to maintain due to the schema. AWS recommends having multiple smaller polices/permission sets in conjunction with [principal of least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege)

![IAM Role](https://i.imgur.com/lHQPhoD.png)

I also configured an AWS Budget alert, should this every start to cost some real money due to people uploading, downloading and deleting objects, I could login and delete the bucket.

My current monthly costs in this AWS account are almost £2 as most of the resources I use take advantage of the free tier.

Finally, I created the IAM API user for the fictitious app. This user has a single policy attached to it (shown above). I then took the access key and id, update the .env file and checked those into git  😬

After completing my pull request from dev to main for my new python app, I got an email from AWS. It would appear they have some form of bot or scraper configured to roam github and possibly the wider internet for strings that looks like AWS access keys. Most likely designed to help developers who accidentally committing the wrong file without noticing.

## AWS save my bacon

>  Dear AWS customer, 
>
>  We have become aware that the AWS Access Key MX4E7UBE57F6PQ9ARFD, belonging to IAM User python-app-rw, along with the corresponding Secret Key is publicly available online at https://github.com/dewhurstwill2/corp-file-share-python/blob/<blob hash removed>/.env

> Your Security is important to us and this exposure of your account’s IAM credentials poses a security risk to your AWS account, could lead to excessive charges from unauthorised activity, and violates the AWS Customer Agreement or other agreement with us governing your use of our Services.

> To protect your account from excessive charges and unauthorized activity, we have applied the “AWSCompromisedKeyQuarantine” AWS Managed Policy (“Quarantine Policy”) to the IAM User listed above. The Quarantine Policy applied to the User protects your account by limiting permissions for high risk AWS services. You can view the policy by going here: <Link to IAM> For your security, DO NOT remove the Quarantine Policy before following the instructions below. In cases where the Quarantine Policy is causing production issues you may detach the policy from the user. NOTE: Only users with admin privileges or with access to iam:DetachUserPolicy may remove the policy. For instructions on how to remove managed policies go here: <Link to IAM>. In the event of the unauthorized use of your AWS account, we may, at our sole discretion, provide you with concessions. However, a failure to follow the instructions below may jeopardize your ability to receive a concession.

> If you believe you’ve received this note in error, please contact us immediately via the support case.PLEASE FOLLOW THE INSTRUCTIONS BELOW TO SECURE YOUR ACCOUNT: <Steps to secure account> WE RECOMMEND THAT YOU ENABLE AMAZON GUARDDUTY: Amazon GuardDuty is an AWS threat detection service that helps you continuously monitor and protect your AWS accounts and workloads. Enabling Amazon GuardDuty on your accounts gives you further visibility into malicious or unauthorized activity, alerting you to take action in order to reduce the risk of harm. To learn more, visit: <Link to Guard Duty>. If you have any questions, you can contact us by accessing the newly created Support Case in your account’s Support Center. If you do not see a new case, you can create a case from the Support Center here: <Link to Suport Center> Thank you for your immediate attention to this matter.

I also got emails from a couple of third-party services however they weren’t as on the ball as AWS

## What about other platforms?

You may be thinking, if AWS do this surly Azure does.... Well you would be wrong.

I created a a similar version of the python app using the azure SDK and another environment variables file

```
--- .env ---
AZURE_STORAGE_CONNECTION_STRING="DefaultEndpointsProtocol=https;AccountName=testcorp1234567;AccountKey=xvGqM8eNlKD0JgdeAilcqIEmoXORqCcAETaOrvUqOWlFI/P4Usmha/CfPIe5YNTZHPMz+yWSG/wk9kXjyuPrcQ==;EndpointSuffix=core.windows.net"
SRC_CONTAINER="blobs"
DEST_CONTAINER="blobs"
FILE_PREFIX="blob_"
```

Although I didnt get an email from azure to say I had uploaded a connection string by accident, one of my collegues did upload a file using the connection string secret and the azure storage explorer (only know it was one of my collegues as they told me)

![](https://i.imgur.com/cqQCgyw.png)

Looking further into Microsoft documentation this is not a best practice way of accessing a storage account instead they recommend creating an app registration with limited permissions on the storage account/container from which you can generate should lived access tokens with just the right ammount of permission

## To conclude

Leaking secrets always doesn’t end well however it would appear due to the number of high-profile news stories regarding S3, Amazon has invested heavily to ensure administrators are notified before the credentials can be used maliciously

What if they were not AWS credentials where they have invested heavily in security, what if it was an API key to a custom service or you accidentilly commited a connection string to your database?

This would be where you would turn to an alternative for non-AWS credentials such Git Guardian (Not Sponsored) which you can read more about [here](https://www.gitguardian.com/)

Moral of the story, ensure your .gitignore is configured before pushing to GitHub

