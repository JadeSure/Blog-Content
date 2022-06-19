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

## AWS Regions

Regions all around the world. eg. us-east-1, eu-west-3...  
A region is a cluster of data centers

#### How to choose an AWS Region?

1. compliance with data governace and legal requirements: data never leaves a region without your explicit permission.
2. Proximity to customers: reduce latency.
3. Available services: not all services available in every region.
4. Pricing: pricing varies region to region and is transparent in the service pricing page.

#### AWS Availability Zones

1. Each region has many availability zone(AZ). (2 ~ 6) eg. ap-southeast-2a, ap-southeast-2b, ap-southeast-2c(they are separate with each other(isolated from disasters), but connected with high bandwidth, utlra-low latency networking.)
2. Each AZ is one or more discrete data centers with redundant power, networking and connectivity.

#### AWS global services

IAM, Route53(DNS service), CloudFront(Content Delivery Network), WAF(Web Application Firewall)

#### AWS services region-scoped

EC2(infrastructure as a service), Elastic Beanstalk(platform as a service), Lambda(function as a service), Rekognition???(software as a service)

1 region -> many AZ
1 AZ -> many discrete data centers

## IAM: Identity and Access Management

1 user => many gourps  
1 gourp => many users  
1 user cannot have a group  
1 group cannot contain another group  
In AWS, you apply the least privilege principle; don't give more permissions than a user needs.

## IAM Policies in heritance

inline policy: only assign policies to a single person rather by a group.
!(IAM_Policies_Structure)[]

## IAM Password Policies

1. strong Password with rules setting
2. Multi Factor Authentication - MFA = password you know + security device you own (for root account)  
   eg. Virtual MFA device: Google Authenticator, Authy; and Universal 2nd Factor (U2F) Security Key
