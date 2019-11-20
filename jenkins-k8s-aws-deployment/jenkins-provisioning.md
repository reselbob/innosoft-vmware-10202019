# Setting Up a Jenkins Server on an AWS Virtual Machine

**Step 1:** `ssh` into the virtual machine you created on AWS that you decided to use as the Jenkins server. 

`ssh -i /path/to/your.pem ubuntu@<aws_dns_for_jenkins_server>`

**Step 2:**: Install Docker

`apt update`

`apt install docker.io -y`

`systemctl enable docker`

`sudo usermod -a -G docker $USER`


**Step 3:** Clone the repository code

`git clone https://github.com/reselbob/fatjenkins.git`

**Step 4:** Go to the project's directory

`cd fatjenkins`


## Installation

**Step 1:** Build the container image locally.

`docker build -t fatjenkins:v1 .`

**Step 2:** Create the container based on the local Docker image, making the host's version of docker available to the Jenkins container

`docker run --name jenkins -d -p 8080:8080 -p 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock -v /usr/bin/docker:/usr/bin/docker fatjenkins:v1`

**Step 3:** Get the initial login ID that you'll need to access Jenkins. (Give the container a little time to "warm up".)

`docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword`

You'll output that is similar to, but not exactly the following:

`4b36a89b91a24c51a5d4042433c013e2`

**Step 4:** Confirm the `docker` command works from inside the Jenkins container

`docker exec -it jenkins docker ps`

**Step 5:** Go to your browser and spin the Jenkins site up at `<dns_that_aws_created_for_your_vm>:8080`.

The first time you go the site you'll be asked to enter the login ID you generated above in Step 3. Then you'll need to create some login credentials. For there, install the default plugins. You're now set to go.