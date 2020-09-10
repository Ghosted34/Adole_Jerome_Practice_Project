# Task one: Intro to containers and dockers v1.6

## Using a pre-provisioned VM with the necessary tools pre-installed:
	(In this case, k8s-workshop-module-1-lab)
	
## Setting up the environment
1.	`gcloud config set project [PROJECT_ID]`
2.	`gcloud compute ssh k8s-workshop-module-1-lab --zone= europe-west1-f`
## Run and Distribute Containers with Docker
### Run the web server manually
**For this scenario, the working directory is named kickstart. Switch to the directory.** 
3.	`cd /kickstart`
**…and list the contents**
4.	`ls -lh`
**Install dependencies, which include the latest version python and pip**
5.	`sudo apt-get install -y python3 python3-pip (install python and pip)`
**…and tornado library required for this lab**
6.	`pip3 install tornado` 
**Run the python application**
7.	`python3 web-server.py & (python web server run)`
**…and run an accessibility check**
8.	`curl http://localhost:8888`
*The output would be:  “Hostname: k8s-module-1-workshop”*
**With that done, End the server**
9.	`kill %1`
-------------------------------------------------
### Package using Docker
**Take a quick look at the Dockerfile**
1.	`cat Dockerfile`
**…and build a Docker image with the web server**
2.	`sudo docker build -t py-web-server:v1 .` 
*(Be sure to include the '.' at the end of the command. This tells Docker to start looking for the Dockerfile in the current working directory.)*
**Run the server using Docker**
3.	`sudo docker run -d -p 8888:8888 --name py-web-server -h my-web-server py-web-server:v1`
**Run an accessibility check again**
4.	`curl http://localhost:8888`
**…and stop the container**
5.	`sudo docker rm -f py-web-server`
-------------------------------------------------
### Upload the Image to a Registry
**Add the signed in user to the Docker group as an authenticated user**
1.	 `sudo usermod -aG docker $USER (user add)`
**Restart the instance**
	*Logout from the instance*
2.	`exit`
	*…and ssh into the instance again*
3.	`gcloud compute ssh k8s-workshop-module-1-lab --zone= europe-west1-f`
**Navigate to the working directory**
4.	`cd /kickstart`
**Store your GCP project name in an environment variable**
5.	 ``export GCP_PROJECT=`gcloud config list core/project --format='value(core.project)'``
**…and rebuild the Docker image with a tag that includes the registry name ‘gcr.io’ and the project ID as a prefix**
6.	`docker build -t "gcr.io/${GCP_PROJECT}/py-web-server:v1" .`
*(again, include the ’.’ at the end of the command)*
-----------------------------------------------------
### Make the Image Publicly Accessible
**Configure Docker to use gcloud as a Container Registry credential helper (you are only required to do this once)**
1.	`PATH=/usr/lib/google-cloud-sdk/bin:$PATH`
*(When prompted, press ENTER)*
2.	`gcloud auth configure-docker`
*(When prompted, press y and/or ENTER)*
**Push the image to `gcr.io`**
3.	`gcr.io/${GCP_PROJECT}/py-web-server:v1`
**To see the image stored as a bucket**
4.	 `gsutil ls`
**Update the permissions on Google Cloud Storage to make your image repository publicly accessible**
5.	`gsutil acl ch -u AllUsers:R gs://artifacts.${GCP_PROJECT}.appspot.com`
6.	`gsutil acl ch -r -u AllUsers:R gs://artifacts.${GCP_PROJECT}.appspot.com`
7.	`gsutil defacl ch -u AllUsers:R gs://artifacts.${GCP_PROJECT}.appspot.com`
----------------------------------------------------------
### Run the Web Server from Any Machine
**The Docker image can now be run from any machine that has Docker installed by running the following command**
1.	`docker run -d -p 8888:8888 -h my-web-server gcr.io/${GCP_PROJECT}/py-web-server:v1`
*(You can test it on your VM instance, re-using the curl command from above)*
**Logout from the instance**
2.	`exit`
