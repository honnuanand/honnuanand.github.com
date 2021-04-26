---
layout: post
title: 
date: 2021-04-25 16:26
category: 
author: 
tags: []
summary: The DevSecOps Flow in the Cloud Native World
---
<img src="/images/sec-into-devops.jpg" width="350" height="175" />
Containers and specifically Kubernetes is abuzz in the world of cloud technology. This has led to a tremendous rush to adopt kubernetes and rightfully so. Having said that, much too often you are in a state where one can ask you, you just installed/provisioned Kubernetes - "Now what ?? ". The Challenge of operationalizing kubernetes is not small. There is a lot that goes into making kubernetes useful for developers and operators alike.

`DevSecOps`, We have heard this phrase quite a bit lately. I will attempt to explain what that means to me and kick off a series of blog posts to walk through the workflow. Lets try to paint a picture of the current software development and deployment. To develop, build , deploy and manage software that has multiple constraints, there has been a constant evolution. The DevOps model or the flow has been adopted by the cloud native software industry. The key tenets of devops has been the automation of the key tasks of the work flow that the use of a CI/CD process. The distributed and the exploded scale has brought a big emphasis on security to be a core part of this DevOps flow that brings us to the new mantra in the cloud native world called the Dev-Sec-Ops Flow.

<img src="/images/ThedevsecOpsAsk.jpg" width="250" height="500" />

The Development teams and Designers in the current cloud native and mostly distributed world are imposed with multiple implicit asks for any new software thats being developed. 
Build software that is 
1. Secure by default
1. Can scale at ease to the needs of the distributed user base
1. Allows users to use it consistently
1. Highly available

Lets run through a common set of pit stops that a DevSecOps flow or the journey takes for an Idea to reach the hands of a user. 

1. **The Ideation and Development**
<img src="/images/development.jpg" width="300" height="500" />

    This is the phase in which the developers and the product designers join hands and come up with the product design. This will focus on the user requirements augmented with the 4 key constraints added. The constraints are added almost like a guard rail to the design process. 
    
    The Shelf life of modern software is highly reduced by fast paced change in customer or user needs that evolve over days, weeks or months as compared longer time in the years past. This is the phase where the ideas are converted into code. To enable developers to high productivity, they will need a modern development framework. A framework that allows for rapid development and abundant reusability with libraries that can help take care of common needs. One can think of modern frameworks like the Spring Framework for Java,  .Net Framework and or modern languages like Go, all of which have some common traits that lead to rapid development of software. Once the Code is developed,  we will need to provide the developers a place to store the code.  This has to provide key features for managing code being developed by multiple developers in a team or some times even multiple teams that may be spread across different geographies and timezones. This is where the need for a very good modern source control system comes into picture. Git made famous by github has become one of the most commonly used source control system. 
        
    This phase also may bring the need for a very short and swift feedback loop. Think about the looping through this multiple times in a single day. We will need to provide developers with development environments that allow for these fast paced loops along with the potential of distributed development teams. The environments may need to have the data services and other middleware needed by the application being built. We can almost envision a small dedicated or multi-tenant Kubernetes cluster that can be short living. 

1. **Continuous Integration**

    This can be seen as the phase where multiple individual components are running in an integrated model for the first time. We will need a capability to ensure the functionality when working together by running some automated tests as part of a continuous Integration pipeline. Teams and companies typically use Jenkins, Tekton, Concourse or other similar a CI tool-chain to perform these set of tasks. 

1. **Source to Image packaging**
<img src="/images/src-to-image.png" width="350" height="175" />

    This is the phase when we need to find an efficient process which are bound by the key guard rails from above. Being able to build a process that can build an image which is secure and the images produced are reproducible are top needs here. The latest OCI image formats allow for a lot of efficiencies when comes to building of the images. With the layered model where is layer can be a reference to a separate repository, changes can now be granular. What that means is that now,  we can just change the one layer than has changed and not have to touch any other layers.  The systems can cache the previous layers and reuse them making the image build process extremely fast. The Cloud Native Buildpacks system developed by VMware in partnership with Heroku under the Cloud Native Computing Foundation (CNCF) is an image build process that allows you to build an application's docker image with the developer bringing just the source code. This was the technology that was originally built by the Cloud Foundry PaaS system. The kpack project has all the scaffolding needed to automate this the build process using the buildpacks being triggered by changes in the source code or the other key components needed for the image. These other components include the buildpacks, the base images used for run and build. The images built out of this system are consistent as in reproducible,  secure to the extent that the layers brought in by the platform are updated. This allows for most of the key layers coming from the same set or a small set of images. 

    This allows updates to the images to be very efficient and fast. The process to identify the impacted application images when you have to change some thing like a base image or the buildpack image is very easy as it can be traced back to a small or a single set. This also allows for a very efficient and simpler inventory or Bill of materials management. 


1. **The Image Registry**
<img src="/images/registry.png" width="350" height="175" />

    The docker or oci image registry is a repository for storing OCI images. The registries have become increasingly sophisticated as they build towards scale, security, consistency and enterprise readiness. One such registry is Harbor which is an open source project under CNCF as well. Harbor has a few very strong enterprise ready features like automatic image scanning for vulnerabilities and control of consumption of images from clusters based on the level and number of vulnerabilities found by the scans. Harbor also allows for image signing to help avoid man in the middle attacks. Harbor has a good RBAC system to allow for user access controls along with support for a Helm repository. Having said that,  docker hub ,  GCR, ACR  and ECR are also very popular docker registries. 

1. Third party and OSS apps/services 
<img src="/images/Catalog.png" width="350" height="175" />

    There are not a lot of real applications that do not need databases and/or other middleware like queuing systems. With the every evolving world of databases there is a big concern on the source of the images for those services. Common questions around security and consistency is a major concern for security and compliances teams at enterprises. The needs to be able to provide a process to have the same OSS apps or third party apps packaged on top of a known secure base image is a big need for enterprises. They have to create processes that manage constant checking for vulnerabilities and updates when patches are ready. VMware has build an entire product under the Tanzu portfolio that just does that. The Tanzu Application Catalog allows enterprises to pick a secure, curated and hardened base image on which their choice of the OSS or third party applications can be packaged. This product also recreates the images when any patches for the underlying base image or the software is available. This elevates the security posture of an enterprise in this area significantly. 

1. **The Continuous Deployment**

    Now that we have figured how to build a custom application and also bring in a OSS/Third party app and store them in a trusted secure registry. We need to have a automated continuous deployment pipeline that gets triggered by any change in the images that needs deployment into the clusters its managing. Enterprises use tools like Jenkins and concourse for their automated deployment needs. Argo CD is an up and coming open source deployment tool. 

1. **Network and routing integration ,  Service mesh ? **
<img src="/images/Networking.png" width="500" height="200" />

    Once the Applications are deployed into the clusters one of the major needs for the applications to run in conjunction with tother apps and services that may be running on other namespaces and clusters is their connectivity to those names spaces in other clusters. Connectivity comes with other challenges. How are we going to make sure the connections are encrypted and how we will be able to negotiate the potentially unknown number of disparate apps and networks. This is where a need for a consolidated and unified network comes into picture. Service meshes play a big role in solving this puzzle.  One example is VMware's Tanzu service mesh. TSM allows you to create an overall mesh connecting multiple istio installed k8s clusters. Its agents allow for a encrypted pipe to be created across all connected clusters and provides a unified view to the apps running in the connected names spaces via a Global Namespace. This helps realize a major requirement when the workloads for a given application may be spread across multiple clusters which may in turn be spread across multiple Geographies. A service mesh like this can enable multiple other features. Example: the Tanzu service mesh has all the traffic related information allowing it to use its agents to trigger app autoscaling using traffic based metrics. 

1. **Cluster and application management at Scale** 
<img src="/images/Management.png" width="400" height="200" />

    Another major change in the paradigm when it comes to managing Kubernetes clusters is that gone are those days when we just had a handful or more clusters. In todays world,  we see a lot more scenarios and use-cases where the usage is leaning more towards many small size clusters than a smaller set of large scale clusters. This puts a lot of pressure on Operations teams that are managing these clusters. We have no choice but to start seeing clusters are cattle than pets. Treating them as cattle is easy when you have centralized management of these clusters using one or a small set of policies that are applied on all the clusters. A good example of a tool or a product that helps manage a fleet of clusters is VMware's Tanzu mission control. Mission control caters to both the Cluster operator and Application operator persona. TMC seems to have a lot of features for cluster management. It has built in support for Data protection at multiple levels of Granularity. It also brings some virtual constructs like Cluster groups and Workspaces that are more of a group of name spaces that may span multiple clusters. This allows management of applications that may be spread across multiple namespaces that may be in disparate and distributed clusters to be very easy. 

1. **Monitoring and Observability**
<img src="/images/Observability.png" width="400" height="200" />
 
   Monitoring and Observability is a big ask when it comes to building applications and clusters. In the current cloud native world,  there is a lot of the management and monitoring that is based on the SRE principles that uses a lot of the SLO / SLA based management. Having a pure cloud native observability tool like Tanzu observability helps have a good view into your entire set workloads. 

### Putting it all together

If I ask you, which part of the above key steps is it ok to skip ? My assumption is that you will say none. very step is important to make sure the key asks of security, speed, consistency and scale are taken care on every step on the workflow to fully operationalize a container platform like kubernetes. I will double click on these steps and talk about the details on each of those on the subsequent posts on this series. 

<img src="/images/Fullpicture.png" width="800" height="600" />
