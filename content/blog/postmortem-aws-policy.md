---
title: Postmortem - AWS Policy Management
date: 2024-05-11
authors:
  - name: Jin Young Bang
    link: https://github.com/jinyoungbang
    image: https://github.com/jinyoungbang.png
tags:
  - Postmortem
  - Admin
  - AWS
excludeSearch: true
---

Around late January this year, all API requests were functioning correctly when tested locally. However, upon deployment to both staging and production environments, none of the requests or responses were being processed as expected.
<!--more-->

## What happened?

In late January, a critical issue arose within our system, affecting the processing of Update and Delete requests in both staging (dev cloud API) and production environments. Despite successful execution of these requests during local testing, they consistently failed to proceed as expected in live environments.

During testing in the staging environment, an unexpected discrepancy was encountered where the HTTP response consistently returned a status code of 200. However, upon inspecting the response body, it was evident that the actual result response was a 500 status code. Upon encountering this discrepancy, an investigation was initiated to identify the root cause. Initial findings suggested a potential issue with the formatting of the response object, possibly due to improper JSON parsing.

To address this suspicion, corrective measures were promptly implemented. Specifically, steps were taken to ensure proper JSON parsing to rectify any potential issues with response object formatting. While the corrective action aimed to resolve the discrepancy, it was discovered that the underlying issue persisted despite the implemented fix. This indicated that the problem extended beyond the initially suspected cause, necessitating further investigation.

Despite implementing the fix, the issue persisted. I attempted to add logging to both the response body and CloudWatch for further insight. However, CloudWatch did not generate any logs. Examination of the response body revealed an error indicating that our development account lacked permissions to execute an `UpdateOperation` on DynamoDB. Consequently, when our API was invoked, the associated Lambda function attempted to update the database without the necessary permissions.

## The Fix

Prior to resolving this issue, it was identified that the application's permissions within AWS Chalice relied on the `policy.json` file located within the `.chalice` directory. However, it was discovered that our Chalice application lacked the essential permissions required to perform DynamoDB operations. To address this, the necessary policies were added to both the development and production `policy.json` files. This fix was implemented and can be referenced in detail in the following pull request: https://github.com/whyphi/zap/pull/27

A similar occurrence was observed with our AWS SES Email Sending Service. It was crucial to ensure that the resource being utilized was properly configured within the policy. To address this, adjustments were made to the policy settings to include the necessary resources. Detailed information regarding this fix can be found in the following pull request: https://github.com/whyphi/zap/pull/30


## How can we prevent this from happening again?

- Regular Review of IAM Policies before Important Events: Before deploying new features, regular reviews of IAM policies should be done to ensure that they align with the evolving requirements of the system. This includes verifying that all necessary permissions are granted for each environment and service utilized within the application.
- Enhanced Logging and Debugging: Improve logging mechanisms to capture detailed information about API requests, responses, and errors. Ensure that logs are readily accessible and include relevant contextual data to facilitate efficient debugging and troubleshooting.
- and finally, understanding how to actually use the AWS ecosystem.