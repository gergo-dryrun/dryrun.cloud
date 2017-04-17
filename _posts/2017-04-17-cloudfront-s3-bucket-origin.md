---
title: "CloudFront distribution with S3 bucket origin and Origin Access Identity protection"
date: 2017-04-17
tags: [cloudfront, cloudformation, lambda]
categories: [cloudformation]
description: "Cloudformation template containing custom resource to create CloudFront Origin Access Identity and sample stack for creating a CloudFront distribution using Origin Access Identity."
---

For the [static site infrastructure quickstart](https://github.com/gergo-dryrun/staticsite-infra) I Initially wanted to use Origin Access Identity to protect the s3 bucket from direct access and only allow CloudFront to serve the content, but as [I found out](https://github.com/gergo-dryrun/staticsite-infra/issues/1) CloudFront errs when tasked to serve content from subdirectories.

I already had the custom resource for creating/deleting Origin Access Identity and instead of letting it go to waste, figured I'd re-purpose it into a standalone module. Plus it was a great opportunity to play around with `aws cloudformation package` and `aws cloudformation deploy`.

The [github repo](https://github.com/gergo-dryrun/cloudfront-origin-access-identity) includes a `template/cloudfront-oai.template` which creates an Origin Access Identity that can be used to protect s3 buckets from direct public access so that requests only go through CloudFront.
You can embed it in any of your stacks and then just reference the output values as:

```
Fn::GetAtt: [CloudFrontOAI, Outputs.OriginAccessId]
Fn::GetAtt: [CloudFrontOAI, Outputs.S3CanonicalUserId]
```

The `template/demo-stack.template` stack creates a CloudFront distribution with S3 bucket (also creates the s3 bucket) as origin based off 3 simple parameters as provided in `parameters/demo-stack.json`

```javascript
[
  {
    "ParameterKey": "CloudFrontAccessLogBucket",
    "ParameterValue": "example-com-infra-logs"
  },
  {
    "ParameterKey": "OriginBucketName",
    "ParameterValue": "cdn.example.com"
  },
  {
    "ParameterKey": "CloudFrontAliases",
    "ParameterValue": "cdn.example.com"
  }
]
```

Please keep in mind that the templates contain references to local paths so they have to be used with `aws cloudformation package/deploy`

Attached you have a Makefile that can do all that for you, just make sure that the `BUCKET_NAME` you specify exists so that the resources can be uploaded.


```bash
make BUCKET_NAME=cloudformation-resources STACK_NAME=demo-stack deploy
```

#### Prerequisites

* [jq](https://stedolan.github.io/jq/) version >=1.4
* [awscli](http://docs.aws.amazon.com/cli/latest/userguide/installing.html) version >=1.11.36