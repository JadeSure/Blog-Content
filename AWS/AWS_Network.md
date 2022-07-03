---
layout: post
title: Install TensorFlow-GPU by Anaconda (conda install tensorflow-gpu)
subtitle: It might be the simplest way to install Tensorflow or Tensorflow-GPU by conda install in the conda environment
date: 2019-03-29
author: Wang Pei
header-img: img/machine-learning.jpg
catalog: true
tags:
  - ubuntu
  - TensorFlow
  - deep learning
  - CUDA
---

### Check ip address for a domain Name

for Windows: nslookup  
for Mac: dig

### TTL: time to live

how long will client cache the result for the TTL of the record and during this period, client will not call amazon 53 again.

### CNAME vs Alias

AWS resources (load balancer, cloud front..) expose an AWS hostname -> map from myapp.mydomain.com to 123.us-east-2

1. CNAME: points a hostname to any other hostname (app.mydomain.com => XXX.anything.com) => ONLY FOR NON ROOT DOMAIN
2. ALIAS: points a hostname to an AWS resource => Works for ROOT DOMAIN and NON ROOT DOMAIN(type -> A, AAAAA); You cannot set an ALIAS record for an EC2 DNS name

## Routing Policies

Define how route 53 responds to DNS queries

1. simple: can return many ones and client will pick one randomly.
2. weighted: 10%, 20%, 70% to relocate the burden
3. failover: restart secondary diaster recovery EC2 immediately
4. latency based: will pick the closer one to connect the sever based on local server location.
5. geolocation: based on location to separate client to visit different server
6. multi-value answer(client side load balancing): up to 8 healthy records are returned for each mulit-value query, which is not suitable for ELB; also, this one can be associated with Health Checks (return only values for healthy resources)
7. geoproximity (using route 53 traffic flow feature: use bias to split users), which is really helpful when you need to shift traffic from one region to another by increasing the bias

## Health Checks

### Monitor an Endpoint

1. only be available for public resources
2. automated DNS failover
   1. monitor an endpoint
   2. monitor other health checks(Calculated Health Checks)
   3. monitor CloudWatch Alarms (full control!!) - e.g. throttles of DynamoDB, alarms on RDS, custom metrics
      all of them are integrated with Could Watch metrics
      if > 18% of health checkers report the endpoint is healthy, Route 53 considers it Healthy.

### Calculated health Checks

Combine the results of multiple health checks into a single health check  
perform maintenance to your website without causing all health checks to fail

### How to check private hosted zones?

create a CloudWatch Metric and associate a CloudWatch Alarm, then create a health check that checks the alarm itself.

### You have purchased a domain on GoDaddy and would like to use Route 53 as the DNS Service Provider. What should you do to make this work?

Public Hosted Zones are meant to be used for people requesting your website through the Internet. Finally, NS records must be updated on the 3rd party Registrar.
