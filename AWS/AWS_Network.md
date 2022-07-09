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

## S3 Encryptions

1. SSE-S3(server side encryption) encryption: encryption using keys handled & managed by Amazon S3.
2. SSE-KMS(key management service): encryption using keys handled & manged by KMS
3. SSE-C: server-side encryption using data keys fully managed by the customer outside of AWS (HTTPS is mandatory for SSE-C)
4. Client Side Encryption: client library such as the Amazon S3 Encryption Client.

With SSE-KMS, the encryption happens in AWS, and the encryption keys are managed by AWS but you have full control over the rotation policy of the encryption key. Encryption keys stored in AWS.  
With Client-Side Encryption, you have to do the encryption yourself and you have full control over the encryption keys. You perform the encryption yourself and send the encrypted data to AWS. AWS does not know your encryption keys and cannot decrypt your data.

### S3 security

1. User based: IAM policies - which API calls should be allowed for a specific user from IAM console.

Multi-Part Upload is recommended as soon as the file is over 100 MB；  
You have enabled versioning in your S3 bucket which already contains a lot of files. Which version will the existing files have? null；

### CORS - Cross-Origin Resouce Sharing

Cross-Origin Resource Sharing (CORS) defines a way for client web applications that are loaded in one domain to interact with resources in a different domain. To learn more about CORS, go here: https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html
