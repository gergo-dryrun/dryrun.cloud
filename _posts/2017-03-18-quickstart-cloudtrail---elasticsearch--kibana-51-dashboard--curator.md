---
title: "Quickstart CloudTrail to ElasticSearch 5.1"
date: 2017-03-18
tags: [cloudformation, elasticsearch, lambda, cloudtrail, cloudwatch, curator, kibana, dashboard]
categories: [cloudformation]
description: "Cloudformation stack to deploy CloudTrail to ElasticSearch 5.1 + Kibana 5.1 Dashboard + Curator"
---


This project includes a master CloudFormation template that bundles up independent stacks for:

 * Setting up an AWS managed ElasticSearch 5.1 Domain
 * Enabling CloudTrail, configure CloudWatch LogGroup, lambda to stream API activity to ElasticSearch Domain
 * Custom lambda to import a CloudTrail Kibana 5.1 dashboard
 * ElasticSearch Curator lambda



If you already have an ElasticSearch domain and you don't want a dedicated one for CloudTrail, feel free to deploy the sub-stacks independently.


If you want the full setup you can launch the stack and get going.

[<img src="{{"images/cloudformation-launch-stack.png" | prepend: site.baseurl}}">](https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=quickstart-ct-to-es&templateURL=https://s3-eu-west-1.amazonaws.com/quickstart-cloudtrail-to-elasticsearch/template/quickstart-cloudtrail-to-elasticsearch.template)


{% lightbox images/cloudformation-parameters.png  --data="appfoundry_image_set" --title="CloudFormation Parameters" --alt="CloudFormation Parameters" --img-style="max-width:100%;" --class="yourclass" %}

If you are planning to use the ElasticSearch domain just for CloudTrail logs, I would recommend to not use dedicated master nodes.

Depending on domain configuration it can take up to 30 minutes for the stack to finish.


{% lightbox images/cloudformation-stack-output.png  --data="appfoundry_image_set" --title="CloudFormation Output" --alt="CloudFormation Output" --img-style="max-width:100%;" --class="yourclass" %}

If it's a new ElasticSearch domain, when you first time visit Kibana you will be asked to select a default index pattern.
You can use the cwl- one.

From Dashboard you can load the CloudTrail Dashboard

{% lightbox images/cloudtrail-dashboard.png  --data="appfoundry_image_set" --title="CloudTrail Dashboard" --alt="CloudTrail Dashboard" --img-style="max-width:100%;" --class="yourclass" %}

So there you have it, a dedicated ElasticSearch domain to track all the API activities and curator lambda function to automatically deletes old indices.

You can find the project on [github](https://github.com/gergo-dryrun/quickstart-cloudtrail-to-elasticsearch/)

## Disclaimer

 Currently the whole setup works on the premise that the index pattern for cloudtrail logs will be cwl-

---