History:
google develop an internal system called 'Borg' (later named as omega) to deploy and manage
thousands of google application and services on their cluster

in 2014, google introduce k8s as an open source platform written in Golang and later donated
to CNCF (cloud native computing foundation)

• Kubernetes playground
• play with k8s
• GKS google
• AKS azure
• EKS amazon
Problem with scaling up the container:
• can't communicate with each other
• autoscaling
• load balancing
• container had to be manage carefully
Terms to know:
• monolithic application: single stone application, every
• Microservice: each task is deploy in diff-2 services, connect with each other via API
• Orchestration tool = container management tool
Kubernetes = k8s
k8s is an open source container management tool which automates container deployment ,
container scaling, load balancing

it schedule, run, manage isolated containers which are running on virtual/physical/cloud
machine

• all top cloud provider support k8s
feature of k8s:
• Orchestration (clustering of any no of cluster running on different n/w)
• autoscaling
• load balancing
• platform independent (cloud / virtual / physical)
• fault tolerance (node / pod failure)


Refer PDF for more details


[Kubernetes Basic to Advance.pdf](https://github.com/user-attachments/files/21765354/Kubernetes.Basic.to.Advance.pdf)


• rollback
• health monitoring of pod
• batch execution
