---
title: Environment Variables
type: docs
next: onboarding/zap/6-testing
prev: onboarding/zap/4-structure
weight: 5
---

Setting up environment variables are a little bit more complicated in Chalice. However, it is crucial to understand and know how to implement them as we work with sensitive credentials from other external services.

Currently, our local backend is configured to utilize AWS services on a development environment/service. This means local environment variables are technically hosted on the cloud as well.

## Creating an Environment Variable

First, navigate to **AWS Systems Manager**. Then, navigate to **Parameter Store** on the sidebar under Application Management:

<img src="/images/zap/paramstore_sidebar.png" width="300">

Then, click **Create parameter** to create an environment variable:

<img src="/images/zap/paramstore_create.png" width="500">

Fill out all the necessary details for your environment variable (try your best to keep the name formatting relevant and consistent):

<img src="/images/zap/paramstore_details.png" width="500">

Click **Create parameter**.

## Provision Access

To use AWS services on chalice, the necessary permissions need to be provided or else AWS denies the specific resource when requested. Param Store environment variables are considered as a resource and we need to specify that our application should have access to request it.

On Zap's directory, navigate to `.chalice`. Inside it, you will see both `policy-dev.json` and `policy-prod.json`. Inside both json files, navigate to `"Resource"` and add your new parameter to the respective files. Your Chalice application should now have access to request the new environment variables you created.
