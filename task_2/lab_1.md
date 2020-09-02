# Google Cloud Fundamentals: Getting Started with Compute Engine
Overview
In this lab, you will create virtual machines (VMs) and connect to them. You will also create connections between the instances.

### Objectives
In this lab, you will learn how to perform the following tasks:

* Create a Compute Engine virtual machine using the Google Cloud Platform (GCP) Console.

* Create a Compute Engine virtual machine using the gcloud command-line interface.

* Connect between the two instances.

### Task 2: Create a virtual machine using the GCP Console
Creating a VM instance
`gcloud compute instances create my-vm-1 --image-project "debian-cloud" --image "debian-9-stretch-v20190213"`

### Task 3: Create a virtual machine using the gcloud command line
```
gcloud compute zones list | grep us-central1
gcloud config set compute/zone us-central1-b
gcloud compute instances create "my-vm-2" \
--machine-type "n1-standard-1" \
--image-project "debian-cloud" \
--image "debian-9-stretch-v20190213" \
--subnet "default"
```
### Task 4: Connect between VM instances
'ping my-vm-1'
