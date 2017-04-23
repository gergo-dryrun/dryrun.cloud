---
title: "Quickstart Hosting Static Site on AWS Part 1: Infrastructure"
date: 2017-04-16
tags: [cloudfront, cloudformation, waf, staticsite]
categories: [cloudformation]
description: "Cloudformation stack suite to deploy static site behind cloudfront with optional test environment and WAF protection."
---

I don't know about you, but when I get bored I like to read [AWS whitepapers](https://aws.amazon.com/whitepapers/), it's a very relaxing Sunday teatime activity.

One of the more recently read ones was the whitepaper on [hosting static websites on aws](https://d0.awsstatic.com/whitepapers/Building%20Static%20Websites%20on%20AWS.pdf) and it was the inspiration for this article.

dryrun.cloud is a static site generated using [Jekyll](https://jekyllrb.com/) and hosted on AWS. Up until now the setup was fairly basic and manual using `jekyll build` and `aws s3 sync`, but that's not the way things should be in 2017. Cloudformation to the rescue!

This first part of the article will cover creating the infrastructure backbone, and Part 2 will be about CI/CD.


The [github repo](https://github.com/gergo-dryrun/staticsite-infra) includes a master CloudFormation template that bundles up independent stacks:

 * CloudFront distribution with s3 bucket as origin
 * [Optional] WebACL with a suite of security automations
 * [Optional] IP restricted staging environment

Additionally includes separate template for:

 * ACM SSL certificate


#### Prerequisites

* Route53 hosted zone for the domain you want your static site to be hosted on. [1]
* Means to verify e-mail sent out by ACM. [2]

#### Setup
Deploy the `acm-certificate.template` stack first, make sure you do it in US East (N. Virginia).

[<img src="{{"images/cloudformation-launch-stack.png" | prepend: site.baseurl}}">](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=acm-certificate&templateURL=https://s3-eu-west-1.amazonaws.com/dryrun.cloud-resources/2017-04-09-getting-started-static-sites/template/acm-certificate.template)

{% lightbox images/acm-certificate.png  --data="appfoundry_image_set" --title="Certificate parameters" --alt="Certificate parameters" --img-style="max-width:100%;" --class="yourclass" %}


This will issue a certificate for both `www.{domainName}` and `{domainName}`

Grab the CertificateArn from Outputs tab and save it for the next step.

Now onto deploying the `master.template`

[<img src="{{"images/cloudformation-launch-stack.png" | prepend: site.baseurl}}">](https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=staticsite-infra&templateURL=https://s3-eu-west-1.amazonaws.com/dryrun.cloud-resources/2017-04-09-getting-started-static-sites/template/master.template)


Quite a few parameters are needed, the defaults should be fine for most of them (if you want the full setup with WAF included)

{% lightbox images/master.png  --data="appfoundry_image_set" --title="Master parameters" --alt="Master parameters" --img-style="max-width:100%;" --class="yourclass" %}

Use the `ACM Certificate ARN` obtained from previous step.

If you don't want to use WAF then set `Configure WAF` to `no` and keep in mind that the `CloudFront Access Log Bucket` has to exist before deploying this stack.

If you already have a WebACL with security automations configured, set `Provision Security Atuomations` to `no` and use the id of the WebACL that you want to re-use.

To read more about the WAF Security Automations I recommend giving [aws security automation quickstart](https://aws.amazon.com/answers/security/aws-waf-security-automations/) a read through.

Once you launch the stack it can take up to 40 minutes for all the resources to create.

Depending whether you selected to also create a staging environment you will end up with 2 buckets:

* live.{DomainName}
* staging.{DomainName}

Both of them have been configured with default IndexDocument: `index.html`

If you want to be more autonomous I recommend forking this repo and adjusting the parameter files under `/parameters/` folder.

Then you can just use the Makefile to deploy/update the stack:

```bash
# To create stack
make STACK_NAME=staticsite-demo STACK=master PARAM_PATH=`pwd`/parameters REGION=us-west-1 create
# To poll for events
make STACK_NAME=staticsite-demo STACK=master REGION=us-east-1 watch
# To see the stack outputs
make STACK_NAME=staticsite-demo STACK=master REGION=us-east-1 output
# To update the stack
make STACK_NAME=staticsite-demo STACK=master PARAM_PATH=`pwd`/parameters REGION=us-east-1 update
# To delete the stack
make STACK_NAME=staticsite-demo STACK=master PARAM_PATH=`pwd`/parameters REGION=us-east-1 delete
```

This works with any template that has an associated parameter file. Alternatively, you can keep the parameters in a completely separate repository and just point PARAM_PATH to the right place.


#### Tips & gotchas
If you visit the live site before the DNS has fully propagated within AWS CloudFront, you might get a 307 Temporary Redirect, so I recommend waiting 20-30 minutes before visiting the site after the stack has been deployed.

If you do end up with 307 and don't want to wait 24h for cloudfront cache to clear you will have to Create a `CloudFront invalidation`  for `*` to forceclear the DNS cache.

WAF can be a bit expensive if all you really host is a static site. Please make sure you check out the [Pricing model](https://aws.amazon.com/waf/pricing/) before deploying anything.

***

 [1] <http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/creating-migrating.html>

 [2] <http://docs.aws.amazon.com/acm/latest/userguide/setup-email.html>