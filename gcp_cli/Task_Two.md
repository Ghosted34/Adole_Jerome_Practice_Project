# Task two:  Getting started with compute engine

## Setting up the environment
1.	`gcloud config set project [PROJECT_ID]`
2.	`gcloud config set compute/zone us-central1-a`
## Creating virtual instances
1.	Creating first virtual instance 
```
gcloud beta compute  instances create my-vm-1 --zone=us-central1-a \
 --machine-type=e2-medium --subnet=default \
 --tags=http-server --image=debian-9-stretch-v20200805 \
 --image-project=debian-cloud --boot-disk-size=10GB \
 --boot-disk-type=pd-standard
```
   Firewall rule to allow HTTP traffic

```
gcloud compute firewall-rules create default-allow-http --direction=INGRESS \
--priority=1000 --network=default \
--action=ALLOW --rules=tcp:80 \
--source-ranges=0.0.0.0/0 \
--target-tags=http-server
```

2.	Creating second virtual instance 
```
gcloud config set compute/zone us-central1-b
gcloud compute instances create "my-vm-2" \
--machine-type "n1-standard-1" \
--image-project "debian-cloud" \
--image "debian-9-stretch-v20190213" \
--subnet "default"
```

## Connection between VM instances
**Use the gcloud command to ssh into my-vm-2**
1.	`gcloud compute ssh my-vm-2 -- zone=us-central1-b`
**Use the ping command to ensure my-vm-2 can reach my-vm-1**
2.	`ping my-vm-1`

**_CTRL + C_ to abort the ping command**

**Use the ssh command to access my-vm-1**
3.	`ssh my-vm-1`
_Enter yes when prompted._

**Install nginx server on my-vm-1 instance**
4.	`sudo apt-get install nginx-light –y`

**Using _nano_, add a custom message to the home page of the web server**
5.	`sudo apt-get install nginx-light –y`

**Using the arrow keys, move the cursor to the line just below the h1 tag.  Add this message and change `YOUR_NAME` to your name**
6.	`Hi from [YOUR_NAME]`

**Hit _CTRL + O_, ENTER to save the file, and _CTRL + X_ to exit the editor**

**Confirm that the web server is serving your new page. At the command prompt on my-vm-1, execute this command:**
7.	`curl http://localhost/`

**Exit the command prompt on my-vm-1**
8.	`exit`

**To confirm that my-vm-2 can reach the web server on my-vm-1, at the command prompt on my-vm-2, execute this command:**
9.	`curl http://my-vm-1/`

