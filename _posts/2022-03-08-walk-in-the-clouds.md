---
layout: post
title: "A Walk in the Clouds"
description: "Migrate your services to the cloud"
category: tech
tags: [cloud, gcp, aws, Kubernetes, Raspberry Pi]
---
{% include JB/setup %}
### Push a button and get everything in the cloud? <img src="/assets/imgs/cloud.jpeg"  alt="Various Edna Mode Styles" width="40%"/>

Recently I joined [Coursera's GCP courses](https://www.coursera.org/learn/gcp-fundamentals) for helping the migration of some services to the GCP cloud.  Because of past experience I had some concept of AWS, however GCP world is still pretty new for me.  Thus I create a note here for myself to learn [AWS](https://explore.skillbuilder.aws/), [Azure](https://docs.microsoft.com/en-us/learn/azure/) and [GCP](https://cloud.google.com/training/learning-path-offer).

<hr/>

|   | [AWS](https://aws.amazon.com/)|[Azure](https://azure.microsoft.com/)|[GCP](https://cloud.google.com/)|
| ----------------------- | --------------- | -------------- | -------------- |
|IAAS | --------------- | -------------- | -------------- |
| Server  | EC2  | Azure Virtual Machine | Compute Engine|
| OS images | AWS Marketplace | Azure Marketplace | Marketplace |
| Object storage | S3 | Blob Storage | Cloud Storage|
| Relational Database | RDS| Azure Database | Cloud SQL/spanner |
| DNS | Route 53 | Azure DNS | Cloud DNS |
| Serverless | Lambda | Azure Functions | Cloud Functions |
|NoSQL database | Document DB, Dynamo DB, Simpler DB | Table Storage, CosmosDB| Cloud Bigtable, DataStore |
|Queue| MSK | Kafka on HDInsight | Apache Kafka, Google pub/sub |
| permission | IAM <br>(by account) | Azure Active Directory <br> (by organization) | Cloud IAM <br> (by project) |
|monitoring| Cloudwatch|Azure Monitor|Stackdriver|
|PAAS|--------------- | -------------- | -------------- |
|platform | Elastic Beanstalk | App Services | App Engine |
|Container Orchestration | EKS | AKS | GKE |
|Multi Cloud | EKS Anywhere | Azure Migrate | Anthos |
{:style="width: 100%;"}
<hr/>

The major goal of my GCP course study is Kubernetes, K8. The concept of k8 can be simplified by the following drawing:
<img src="/assets/imgs/k8.jpg"  alt="objects in Kubernetes" width="100%"/>

However, deploying K8 requires some hosts.  If you'd like to play your own K8 for free, other than signing up free trial AWS, one way is to [enable K8 in docker desktop](https://andrewlock.net/running-kubernetes-and-the-dashboard-with-docker-desktop/) and install Dashboard.  Or you can setup [K3s (a lightweight K8 replacement, armv7 binaries available)](https://k3s.io/) dashboard on Raspberry Pi.  Here are steps based on [Anthony's step](https://anthonynsimon.com/blog/kubernetes-cluster-raspberry-pi/) on Debian 10:

```
$ sudo apt upgrade -y
$ sudo apt install -y docker.io
$ sudo docker info
$ sudo sed -i \
'$ s/$/ cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1 swapaccount=1/' \
/boot/cmdline.txt
$ sudo reboot

$ curl -sfL https://get.k3s.io | sh -

# Create a namespace and and switch to it
$ kubectl create namespace kubernetes-dashboard
$ kubens kubernetes-dashboard

# Deploy from the official source
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml
$ kubectl patch deployment kubernetes-dashboard -n kubernetes-dashboard --type 'json' -p '[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value": "--enable-skip-login"}]'
$ kubectl proxy
```
K3s dashboard, [http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/overview?namespace=_all](visit http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/overview?namespace=_all), on my local Raspberry Pi 3B+ is very slow, but it's surely fun!
