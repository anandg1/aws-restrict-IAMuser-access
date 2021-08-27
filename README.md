# Restricting the access of IAM users to specific Amazon EC2 resources.


[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)]()

## DESCRIPTION:

Let us see how to restrict an AWS Identity and Access Management (IAM) user or group’s access to one particular Amazon Elastic Compute Cloud (Amazon EC2) resource or group of Amazon EC2 resources for multiple groups of resources on the same AWS account.

## PRE-REQUISITES:

1) AWS account with root access.
2) Creating an EC2 instance.
2) Creating IAM user and assigning an IAM policy.

## STEPS:

##### 1) Creating a custom IAM policy

First we need to create a custom IAM policy. For this,  open the IAM console and create an policy similar to the following:

```sh
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":"ec2:Describe*",
         "Resource":"*"
      },
      {
         "Effect":"Allow",
         "Action":[
            "ec2:StartInstances",
            "ec2:StopInstances",
            "ec2:RebootInstances"
         ],
         "Resource":[
            "arn:aws:ec2:ap-south-1:xxxxxxxxxxxx:instance/*"
         ],
         "Condition":{
            "StringEquals":{
               "ec2:ResourceTag/yyyy":"zzzz"
            }
         }
      }
   ]
}
```

Here, 
- replace 'xxxxxxxxxxxx' with your [account id](https://docs.aws.amazon.com/general/latest/gr/acct-identifiers.html).
- replace 'yyyy' with the tag key you have attached to the EC2 
- replace 'zzzz' with the tag value you have attached to the EC2
- 'Action' specifies the only actions which could be performed by the user with this policy attached.

> Here I'm taking the values
> yyyy = Name
> zzzz = Anand


This example policy thus restricts an IAM user or group access to only Start/Stop/Reboot EC2 instances in the Asia Pacafic (Mumbai) [ap-south-1] Region that have a tag key of 'Name' with a tag value of 'Anand'.


##### 2) Creating an IAM user and attaching the policy

Create a new user from the IAM console and attach our custom IAM policy.



Now, this user will be able to start/stop/reboot any EC2 instance in the specified region(here ap-south-1) with the a tag key 'Name' having value 'Anand'. It can't alter the state of any other EC2s

## RESULT:

Inorder to test and verify the policy is working properly, I have created 2 EC2 instances Anand and Alex with both having their tag key 'Name' and tag value 'Anand' and 'Alex' respectively.



Now, log in as the IAM user, you will be able to see both the EC2 listed in the region. Try Stopping, starting or rebooting them.


We could verify that only the EC2 with Tag key 'Name' and value 'Anand' could be stopped, started or rebooted.
