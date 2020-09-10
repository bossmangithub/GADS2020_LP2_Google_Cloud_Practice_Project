# VPC Networking
### Objectives
- Explore the default VPC network
- Create an auto mode network with firewall rules
- Convert an auto mode network to a custom mode network
- Create custom mode VPC networks with firewall rules
- Create VM instances using Compute Engine
- Explore the connectivity for VM instances across VPC networks
## Task 1. Explore the default network
#### View the subnets
Use this command to view the subnets in the default network
```
gcloud compute networks subnets list | grep default
```
 
#### View the routes
Use this command to view the routes in the VPC
```
gcloud compute routes list
```
#### View the firewall rules
Use this command to view the firewall rules in the project
```
gcloud compute firewall-rules list
```
#### Delete the Firewall rules
Use this command to delete the firewall rules in the project
```
gcloud compute firewall-rules /*/
```
 
#### Delete the default network
Use this command to delete the default network.
```
gcloud compute networks delete default
```
 
#### Try to create a VM instance
If you try to create a VM, the command will fail because you do not have a VPC in the current project.
```
gcloud compute instances create my-vm --zone us-central1-b
```
 
## Task 2. Create an auto mode network
#### Create an auto mode VPC network with firewall rules
```
gcloud compute networks create mynetwork --subnet-mode=auto


gcloud compute firewall-rules create allow-icmp --network=mynetwork --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=icmp

gcloud compute firewall-rules create allow-internal --network=mynetwork --direction=INGRESS --priority=65534 --source-ranges=10.128.0.0/9 --action=ALLOW --rules=all

gcloud compute firewall-rules create allow-rdp --network=mynetwork --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:3389

gcloud compute firewall-rules create allow-ssh --network=mynetwork --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:22
```
 
#### Create a VM instance in us-central1
Use this command to create a VM instance in us-central1-c of machine type n1-standard-1
```
gcloud compute instances create mynet-us-vm --network=mynetwork --zone=us-central1-c --machine-type=n1-standard-1
```

To verify that the Internal IP for the new instance was assigned from the IP address range for the subnet in us-central1 (10.128.0.0/20), run
```
gcloud compute instances list | grep mynet-us-vm
```
 
#### Create a VM instance in europe-west1
Use this command to create a VM instance in europe-west1-c of machine type n1-standard-1
```
gcloud compute instances create mynet-eu-vm --network=mynetwork --zone=europe-west1-c --machine-type=n1-standard-1
```

To verify that the Internal IP for the new instance was assigned from the IP address range for the subnet in europe-west1 (10.132.0.0/20), run
```
gcloud compute instances list | grep mynet-eu-vm
```

#### Verify connectivity for the VM instances
SSH into mynet-us-vm
```
gcloud compute ssh mynet-us-vm --zone=us-central1-c
```

To test connectivity to mynet-eu-vm's internal IP, run the following command, replacing mynet-eu-vm's internal IP
```
ping -c 3 
```
Repeat the same test by running the following:
```
ping -c 3 mynet-eu-vm
```

To test connectivity to mynet-eu-vm's external IP, run the following command, replacing MYNET-EU-VM-EXTERNAL-IP with mynet-eu-vm's external IP
```
ping -c 3 MYNET-EU-VM-EXTERNAL-IP
```

#### Convert the network to a custom mode network
To convert the network from an auto mode network to a custom mode network, run the following command
```
gcloud compute networks update mynetwork --switch-to-custom-subnet-mode
```


## Task 3. Create custom mode networks
#### Create the managementnet network
The following command creates a VPC network named managementnet
```
gcloud compute networks create managementnet --subnet-mode=custom
```

The following command creates a subnet named managementsubnet-us in the managementnet network
```
gcloud compute networks subnets create managementsubnet-us --range=10.130.0.0/20 --network=managementnet --region=us-central1
```

#### Create the privatenet network
The following command creates a VPC network named privatenet
```
gcloud compute networks create privatenet --subnet-mode=custom
```

The following command creates  two subnets in the privatenet network
```
gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24
```

```
gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/20
```

This command lists the networks in the current project.
```
gcloud compute networks list
```

This command lists all the subnets in the current project and sorts them by network.
```
gcloud compute networks subnets list --sort-by=NETWORK
```

#### Create the firewall rules for managementnet
This command creates a firewall rule to allow SSH, ICMP, and RDP ingress traffic to VM instances on the managementnet network.
```
gcloud compute firewall-rules create managementnet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=managementnet --action=ALLOW --rules=tcp:22,tcp:3389,icmp --source-ranges=0.0.0.0/0
```


#### Create the firewall rules for privatenet
This command creates a firewall rule to allow SSH, ICMP, and RDP ingress traffic to VM instances on the privatenet network.
```
gcloud compute firewall-rules create privatenet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=privatenet --action=ALLOW --rules=icmp,tcp:22,tcp:3389 --source-ranges=0.0.0.0/0
```

To list all the firewall rules (sorted by VPC network), run the following command:
```
gcloud compute firewall-rules list --sort-by=NETWORK
```

To see a more comprehensive list, you can add the tag  ```--format json```

#### Create the managementnet-us-vm instance
To create a VM instance in the managementsubnet-us subnet, run
```
gcloud compute instances create managementnet-us-vm --zone us-central1-c --machine-type=f1-micro --subnet=managementsubnet-us
```

#### Create the privatenet-us-vm instance
To create a VM instance in the privatesubnet-us subnet, run
```
gcloud compute instances create privatenet-us-vm --zone=us-central1-c --machine-type=f1-micro --subnet=privatesubnet-us
```

To list all the VM instances (sorted by zone), run the following command:
```
gcloud compute instances list --sort-by=ZONE
```

## Task 4. Explore the connectivity across networks
To get the internal and external IP addresses of mynet-eu-vm, managementnet-us-vm, and privatenet-us-vm, run:
```
gcloud compute instances list
```

#### Ping the external IP addresses
SSH into mynet-eu-vm and ping the external IP address of the three VMs
```
gcloud compute ssh mynet-us-vm --zone=us-central1-c
ping -c 3 MYNET-EU-VM-EXTERNAL-IP
ping -c 3 MANAGEMENTNET-US-VM-EXTERNAL-IP
ping -c 3 PRIVATENET-US-VM-EXTERNAL-IP
```
 
#### Ping the internal IP addresses
While still in the SSH shell of mynet-eu-vm, ping the internal IP address of the three VMs
```
ping -c 3 MYNET-EU-VM-INTERNAL-IP
ping -c 3 MANAGEMENTNET-US-VM-INTERNAL-IP
ping -c 3 PRIVATENET-US-VM-INTERNAL-IP
```
