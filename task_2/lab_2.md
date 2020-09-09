# Creating Virtual Machines
### Objectives
- Create several standard VMs
- Create advanced VMs

## Task 1: Create a utility virtual machine
#### Create a VM
The following command creates a virtual machine of name my-vm in region us-central1 and zone us-central1-c. Machine type is n1-standard-1 (1 vCPUs, 3.75 GB memory). It has no external IP
```
gcloud compute instances create my-vm --zone=us-central1-c --machine-type=n1-standard-1 --no-address
```
#### Explore the VM details
You can view details of your VM using the following commands. To see an overview, use the first command. To see more detailed information, use the second. 
```
gcloud compute instances list | grep my-vm
gcloud compute instances describe my-vm --zone=us-central1-c
```

To examine your VM’s Availability policies, run the following command and scroll to the scheduling section.
```
gcloud compute instances describe my-vm --zone=us-central1-c
```
 
#### Explore the VM logs
Well, I haven’t found any command to do this.

## Task 2: Create a Windows virtual machine
#### Create a VM
The following command creates an n1-standard-2 Windows VM in zone europe-west2-a using the Windows Server 2016 Datacenter Core  image and a SSD persistent disk boot type.
```
gcloud compute instances create my-win-vm --zone=europe-west2-a --machine-type=n1-standard-2 --tags=https-server --image=windows-server-2016-dc-core-v20200813 --image-project=windows-cloud --boot-disk-size=100GB --boot-disk-type=pd-standard
```
 
This command creates a firewall rule that applies to the just created VM. This rule allows HTTP and HTTPS traffic
```
gcloud compute firewall-rules create default-allow-https --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80,tcp:443 --source-ranges=0.0.0.0/0 --target-tags=https-server
```
 
#### Set the password for the VM
I don’t think this can be done from the cloud shell.


## Task 3: Create a custom virtual machine
#### Create a VM
The following command creates a custom VM in zone us-west1-b with 6 vCPU cores and 32Gb of memory.
```
gcloud compute instances create my-custom-vm --zone=us-west1-b --custom-cpu=6 --custom-memory=32
```
#### Connect via SSH to your custom VM
To connect via SSH to the VM you just created, use the following command
```
gcloud compute ssh my-custom-vm --zone=us-west1-b
```

To see information about unused and used memory and swap space on your custom VM, run the following command:
```
free
```
To see details about the RAM installed on your VM, run the following command:
```
sudo dmidecode -t 17
```
To verify the number of processors, run the following command:
```
nproc
```
To see details about the CPUs installed on your VM, run the following command:
```
lscpu
```
To exit the SSH terminal, run the following command:
```
exit
```
