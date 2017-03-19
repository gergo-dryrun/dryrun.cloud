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



If you already have an ElasticSearch domain and you don't want a dedicated one for CloudTrail, feel free to use only the sub-stacks.


You can Launch the master stack

TODO: make stack public / launchable

{Launch Stack icon}



Depending on domain configuration it can take up to 30 minutes for the stack to finish.


{% lightbox images/cloudformation-stack-output.png  --data="appfoundry_image_set" --title="CloudFormation Output" --alt="CloudFormation Output" --img-style="max-width:100%;" --class="yourclass" %}


Browsing to Kibana 5.1 and loading in the CloudTrail Dashboard you get:

{% lightbox images/cloudtrail-dashboard.png  --data="appfoundry_image_set" --title="CloudTrail Dashboard" --alt="CloudTrail Dashboard" --img-style="max-width:100%;" --class="yourclass" %}


## Disclaimer

 Currently the whole setup works on the premise that the index pattern for cloudtrai logs will be cwl-

---