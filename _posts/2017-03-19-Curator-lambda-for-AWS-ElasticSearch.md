---
title: "Curator lambda for AWS ElasticSearch"
date: 2017-03-19
tags: [cloudformation, elasticsearch, lambda, curator]
categories: [lambda]
description: "Cloudformation stack to deploy curator lambda."
---

Inspired by [the post from elastic](https://www.elastic.co/blog/serverless-elasticsearch-curator-on-aws-lambda)

Deploys a lambda function to run curator on a scheduled basis.

###### Curator config

Create a configuration file for your ElasticSearch domains and uploaded it to s3.

Sample curator.yaml

```yaml
---
- name: example logging cluster
  endpoint: search-domain-logstash-vnojmawsc.us-west-1.es.amazonaws.com
  indices:
    - prefix: logstash-
      days: 365

- name: example metrics cluster
  endpoint: search-domain-metrics-vnojmawsc.us-west-1.es.amazonaws.com
  indices:
    - prefix: metricbeat-
      days: 14
    - prefix: packetbeat-
      days: 14
```

Launch stack

[<img src="{{"images/cloudformation-launch-stack.png" | prepend: site.baseurl}}">](https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=elasticsearch-curator&templateURL=https://s3-eu-west-1.amazonaws.com/dryrun.cloud-resources/2017-03-19-Curator-lambda-for-ElasticSearch-51/curator_lambda.template)

{% lightbox images/19-cloudformation-parameters.png  --data="appfoundry_image_set" --title="CloudFormation Parameters" --alt="CloudFormation Parameters" --img-style="max-width:100%;" --class="yourclass" %}


You can specify the CronSchedule either via a [lambda cron expression or rate expression.][1]

[1]: http://docs.aws.amazon.com/lambda/latest/dg/tutorial-scheduled-events-schedule-expressions.html

The lambda function uses Signature Version 4 Signing Process to authenticate against the ElasticSearch domains.

Tested and validated against ElasticSearch version 5.1

You can find the project on [github](https://github.com/gergo-dryrun/curator-lambda-aws-es/)


Enjoy!
---