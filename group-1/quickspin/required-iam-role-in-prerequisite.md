---
description: This page contain the list of the setup required from the regards of IAM role
---

# Required IAM Role in prerequisite

The initial step for setting up the <mark style="background-color:yellow;">QuickSpin</mark> project, for a new user, will be to create the [**OIDC connection**](../../group-2/aws/how-to-setup-oidc-connection-with-github-repository.md) of AWS Account and the **forked Github Repository**

Once the [OIDC connection](../../group-2/aws/how-to-setup-oidc-connection-with-github-repository.md) is established, the second step will be to create the required IAM role, and attach the policy to it, so that the Github repository runner can use that IAM role in the OIDC connection between AWS and Github, and deploy the required resources.

## Step to create the IAM Role

1. Goto the IAM console and select role from left navigation bar
2. Click on **Create Role**
3. In the opened window, select **Web Identity**
4. In the drop down menu, select `tokens.actions.githubusercontent.com`
5. In the **Audience**, enter _`sts.amazonaws.com`_
6. In the **Github Organisation**, enter your organisation ( generally your Github username )
7. In repository name, enter <mark style="background-color:yellow;">QuickSpin</mark>&#x20;
8. In Github Branch, enter `main`
9. On the next window, attach below permissions:
   1. [AmazonEC2FullAccess](https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1#/policies/details/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FAmazonEC2FullAccess)
   2. [AmazonS3FullAccess](https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1#/policies/details/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FAmazonS3FullAccess)
   3. [IAMFullAccess](https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1#/policies/details/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FIAMFullAccess)

This will create the IAM role, which will be used by <mark style="background-color:yellow;">QuickSpin</mark> to deploy services in your AWS Account. Note the **ARN** for this IAM Role.

## Create the secret in the Github Repository

In your Github Repository for <mark style="background-color:yellow;">QuickSpin,</mark> you will need to setup the secret. You can follow this link for a guide on [How to Setup the Secret](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets#creating-secrets-for-a-repository)&#x20;

To create the secret, follow the below steps:

1. Open the Repository, Goto **Settings**
2. In the left menu, scroll at bottom to **Secrets and Variables**
3. Click on **Actions**
4. Click on New Repository Secret
5. Create secret with  name **`AWS_ROLE_ARN` .** The value for this secret will be the ARN of the IAM role that you copied in Step 9.
