---
title: CI/CD
date: 2016-05-19 20:04:35
tags: [Agile, DevOps]

---
# Agile Development

Before we talk about CI and CD， I think we should figure out why we need them, so we have to talk about Agile Development first.
Agile Development is a set of principles for software development. It was develop to response to changing customer's requirement. As we all know, managers generally desirable to quantify the progress of the development, but quantify of the coding is not easy, the quantify we can only control is the process of the requirment. So we can process a requirement implemented as an iteration cycle of software development.
For this purpose, they created with CI and CD as the core of agile development.
<!-- more -->
## Continuous Integration(CI)

Continuous Integration means you need to integration at least daily, It's a development practice that requires developers to integrate code into a shared repository several times a day."Shared"" means everyone can see what others working on, Frequently compile, test, commit means every times we commit, the code could be running, ideally, every integration should be automated, the unit-test allowing teams to detect problems early. By integration regularly, you can detect errors quickly, and locate them more easily.  
there are many advantages for Continuous Integration, one of which is the continuous delivery.

## Continuous Delivery(CD)

Continuous Delivery is customer requirement oriented, It's like Continuous Integration but more than it, Continuous Delivery is the natural extension of Continuous Integration, Continuous Delivery makes releases boring, so we can deliver frequently and get fast feedback on what users care about.

## Continuous Deployment

Continuous Deployment is more than Continuous Delivery, seams like this:
 ![88cd2b608bfb92ca5487a3e41694e9ccf6b.jpg](https://d1089v03p3mzyq.cloudfront.net/assets/website/continuous-integration-essentials/continuous-delivery-vs-continuous-deployment-b371cf5be55b1c52635058af7b70188cd2b608bfb92ca5487a3e41694e9ccf6b.jpg)
It is the practice of keeping your codebase deployable at any point. Beyond making sure your application passes automated tests it has to have all the configuration necessary to push it into production. Many teams then do push changes that pass the automated tests into a test or production environment immediately to ensure a fast development loop.
A simplified continuous deployment flow can look like this:
![New-Page.png](https://risingstack-blog.s3-eu-west-1.amazonaws.com/2014/Sep/Continuous-deployment---New-Page.png)


## Development Operations(DevOps)

Development Operations is bigger than Continuous Deployment, It aims at establishing a culture and environment where building testing, and releasing software, can happen rapidly, and more reliably.
In my words, DevOps is a practical form of agile.

## Tools for CI and CD

[Jenkins](https://jenkins.io/)

[GoCD](https://www.go.cd/download/)

[TeamCity](https://www.jetbrains.com/teamcity/)

[Travis CI](https://travis-ci.org/)