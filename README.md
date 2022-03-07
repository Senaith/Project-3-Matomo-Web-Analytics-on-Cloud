# Project-3 
# Matomo-Web-Analytics-on-Cloud

In this project we will install Matomo and a MariaDB database using Docker Compose, then install Nginx to act as a reverse proxy for the Matomo app. Finally, you will enable secure HTTPS connections by using Certbot to download and configure SSL certificates from the Let’s Encrypt Certificate Authority.

## Introduction
### What is Matomo?

Matomo, formerly known as Piwik, is a downloadable, Free (GPL licensed) web analytics software platform. It provides detailed reports on your website and its visitors, including the search engines and keywords they used, the language they speak, which pages they like, the files they download and so much more. Matomo (Piwik) aims to be an open source alternative to Google Analytics. Matomo is PHP MySQL software which you download and install on your own webserver.

So, let's begin this project. 

## Part 1
###### Step 1:

We need an Ubuntu 20.04 server, with the UFW firewall enabled. In Projects 1 and 2 we have discussed in detail on how to create a free tier AWS account and set up our EC2 server. The initial set up and configuration process for this project is the same so inorder to follow up with these steps please refer to https://github.com/Senaith/Project-1-Web-stack-implementation-on-aws or https://github.com/Senaith/Project-2-LEMP-Stack-Implementation
Following these steps will increase the security and usability of your server, and will give you a solid foundation for subsequent actions.

###### Step 2:

To log into your server, you will need to know your server’s public IP address.  You will also need the password or — if you installed an SSH key for authentication — the private key for the root user’s account. 
 
![q4](https://user-images.githubusercontent.com/91766546/156699072-126b9a13-a724-4cc8-80bc-9e4b7d5ff3c7.png)

###### Step 3

If you are not already connected to your server, log in now as the root user using the following command (substitute the highlighted portion of the command with your server’s public IP address):

![2](https://user-images.githubusercontent.com/91766546/156699329-0103f386-6611-4a26-8f63-4ef90911136b.png)

###### Step 4:

Create a new user using the following command.

![3](https://user-images.githubusercontent.com/91766546/156699720-5199792c-4ef6-4269-87f5-4ddd652a0283.png)

Once you are logged in as root, you’ll be able to add the new user account. In the future, we’ll log in with this new account instead of root.
This command creates a user named 'sunny'. You can replace that with a user name of your own. 

As you can see above, after running that command you will be asked a few questions, starting with the account password. Enter a strong password and, optionally, fill in any of the additional information if you would like. This is not required and you can just hit `ENTER` in any field you wish to skip.

###### Step 5:

Let's grant the new user administrative privileges. Run the following command in your terminal.This will allow our normal user to run commands with administrative privileges by putting the word `sudo` before the command. To add these privileges to our new user, we need to add the user to the **sudo** group. By default, on Ubuntu 20.04, users who are members of the **sudo** group are allowed to use the `sudo` command.

![4](https://user-images.githubusercontent.com/91766546/156700154-78672756-23b9-4130-9217-5a5eee247c9e.png)

###### Step 6:

The UFW firewall will be used to ensure that only connections to certain services are permitted. The OpenSSH is the service that will allows us to connect to our server, and it has a profile registered with UFW. IN order to check this we need to run this command:

![5](https://user-images.githubusercontent.com/91766546/156703876-75e7f8b4-d927-4b5b-8cfc-69918d858805.png)

We need to make sure that the firewall allows SSH connections so that we can log back in next time. We can allow these connections by typing:

![6](https://user-images.githubusercontent.com/91766546/156703929-6454401c-04b7-42c5-89d0-215d046713c1.png)

Afterwards, we can enable the firewall by running the following command. Hit the Y key and Enter when prompted.

![7](https://user-images.githubusercontent.com/91766546/156704102-50508378-715a-45f3-9618-9a6402121806.png)

You can see that SSH connections are still allowed by typing:

![8](https://user-images.githubusercontent.com/91766546/156704275-5d629e7f-e0de-4be6-8714-9fc1ceaabf9a.png)

The firewall is currently blocking all connections except for SSH, so if we install and configure additional services, we will need to adjust the firewall settings to allow traffic in. 

###### Step 7:

Enabling External Access for Your Regular User

The process for configuring SSH access for our new user depends on whether our server’s root account uses a password or SSH keys for authentication. If you logged in to your root account using a password, then password authentication is enabled for SSH. You can SSH to your new user account by opening up a new terminal session and using SSH with your new username.

![456](https://user-images.githubusercontent.com/91766546/156705382-49ff2f06-a01d-4f52-ae57-a69b2aa2100d.png)

###### Troubleshooting:

It is possible to receive an error message that says: Permission Denied (publickey). 

![454](https://user-images.githubusercontent.com/91766546/156705474-ca82fb3f-f4c1-4ae7-9ab2-4026df7ffe79.png)

If you do not have this error, please skip ahead to the next section below. Otherwise, if you do have this error, then we can resolve it by modifying the configuration file. Use the following command in your Ubuntu server.

![10](https://user-images.githubusercontent.com/91766546/156705587-9e2ee2f1-290d-41a9-9c41-db542cf21c16.png)

This will open our nano editor and now we can scroll through the file and make the following changes.

Change "#PermitRootLogin prohibit-password" to the following:

#PermitRootLogin yes

Next, change # PasswordAuthentication no to the following:

#PasswordAuthentication yes

Next, we need to restart our server using the following command.

![11](https://user-images.githubusercontent.com/91766546/156885024-fd174a77-0b2e-4756-954b-5fa012b77360.png)

Now we should be able to SSH with your new username. Let's try this command again:

![12](https://user-images.githubusercontent.com/91766546/156885048-09c26609-2d75-425d-9699-bf4ef81e7413.png)

###### Step 8:

The next step is to set Up SSH Keys on our PC. SSH keys provide a secure way of logging into our server and are recommended for all users.
The first step is to create a key pair on the client machine (usually our computer).

![keygen](https://user-images.githubusercontent.com/91766546/156885579-c1feb5cf-54a0-4140-b541-7adea1f00d8c.png)

After entering the command, we will see the following output. Press Enter to save the key pair into the .ssh/ subdirectory in your home directory, or specify an alternate path.

![k1](https://user-images.githubusercontent.com/91766546/156885832-72343a9f-7eda-4753-b1cd-1f8124015f71.png)

we will then see a prompt asking us to enter a passphrase. Here we optionally may enter a secure passphrase, which is highly recommended. A passphrase adds an additional layer of security to prevent unauthorized users from logging in. After entering our passphrase, we will see an out put similar to this one on our terminal.

![k2](https://user-images.githubusercontent.com/91766546/156885868-b4e1683b-328e-479c-a1f8-91f8bf366951.png)

###### Step 9:

Moving on to our next step, let's copy the Public Key to our Ubuntu Server

The ***ssh-copy-id*** tool is included by default in many operating systems. For this method to work, we must already have password-based SSH access to our server. To use the utility, we specify the remote host that we would like to connect to, and the user account that we have password-based SSH access to. This is the account to which our public SSH key will be copied.

The syntax is:

![k3](https://user-images.githubusercontent.com/91766546/156889080-296e301e-323d-4e51-85fe-82448b27f177.png)

Our output should be something similar to this:

![13](https://user-images.githubusercontent.com/91766546/156885610-9b3c407c-367d-43fc-89a3-16c1f8e5f644.png)

Now, our id_rsa.pub key has been successfully uploaded to the remote account!

## Part 2:
### Docker installation

[Docker](https://www.docker.com/) is an application that simplifies the process of managing application processes in containers. Containers let us run our applications in resource-isolated processes. They’re similar to virtual machines, but containers are more portable, more resource-friendly, and more dependent on the host operating system. In this section, we’ll install and use Docker Community Edition (CE) on our PC. we’ll install Docker itself, work with containers and images, and push an image to a Docker Repository.

#### Prerequisites

- One Ubuntu 20.04 server set up  including a sudo non-root user and a firewall.
- An account on [Docker Hub](https://hub.docker.com/) if you wish to create your own images and push them to Docker Hub

Let's begin.

###### Step 1:

To ensure we get the latest version, we’ll install Docker from the official Docker repository. To do that, we’ll add a new package source, add the GPG key from Docker to ensure the downloads are valid, and then install the package.

First, we need to update our existing list of packages by running this command.

![14](https://user-images.githubusercontent.com/91766546/156889338-af26a4c9-a5e4-459b-a7b6-ce4ee7eb219a.png)

Next, install a few prerequisite packages which let apt use packages over HTTPS:

![15](https://user-images.githubusercontent.com/91766546/156889370-0239ff82-03d9-4f33-8fb7-c9f481b0ea24.png)

Then add the GPG key for the official Docker repository to our system:

![16](https://user-images.githubusercontent.com/91766546/156889398-da834a80-60b5-414f-bd34-74d424ce5391.png)

Add the Docker repository to APT sources:

![17](https://user-images.githubusercontent.com/91766546/156889414-d5c0c4b6-1dd9-49ac-94c9-19ad8a9dfb3f.png)

This will also update our package database with the Docker packages from the newly added repo. We have to make sure we are about to install from the Docker repo instead of the default Ubuntu repo.

![18](https://user-images.githubusercontent.com/91766546/156895833-9f19468c-0d67-4882-88e4-0425c3ad4b0c.png)

Finally, let's install Docker.

![19](https://user-images.githubusercontent.com/91766546/156895865-bcbe0163-e35d-4182-9e11-a795529ae605.png)

Docker should now be installed, the daemon started, and the process enabled to start on boot. Run the following command to check that it’s running.

![k4](https://user-images.githubusercontent.com/91766546/156895929-17aa324f-9161-4929-a1fb-948367777376.png)

 And the output should be similar to the following, showing that the service is active and running:

![20](https://user-images.githubusercontent.com/91766546/156895886-d07d1a30-e079-4edc-b76b-91a4e2788cb7.png)

###### Step 2:

Executing the Docker Command Without Sudo (Optional)

By default, the docker command can only be run the root user or by a user in the docker group, which is automatically created during Docker’s installation process.

![21](https://user-images.githubusercontent.com/91766546/156895949-aa154cbf-9201-4f01-8ce8-da28bd7a4421.png)

If you want to avoid typing sudo whenever you run the docker command, add your username to the docker group.

![21](https://user-images.githubusercontent.com/91766546/156896266-070ee72c-7f6f-447a-b169-200252bcc12e.png)

To apply the new group membership, log out of the server and back in, or type the following:

![22](https://user-images.githubusercontent.com/91766546/156896344-8b71332e-0015-4d69-96c2-2d443d68ca4c.png)

You will be prompted to enter your user’s password to continue. Confirm that your user is now added to the Docker group by typing the following:

![k5](https://user-images.githubusercontent.com/91766546/156896450-d627f52d-00e1-4814-8738-7b08f0202501.png)

If we need to add a user to the docker group that we’re not logged in as, declare that username explicitly using:

![21](https://user-images.githubusercontent.com/91766546/156896558-4dd6aac7-1a5f-465f-982b-a38aa313cace.png)

Let’s explore the docker command next.

###### Step 3: Using the Docker Command

To view all available subcommands, type:

![24](https://user-images.githubusercontent.com/91766546/156908199-f5ba53bf-ce2e-4ad3-af50-d0a6c4d12479.png)

To view system-wide information about Docker, use:

![25](https://user-images.githubusercontent.com/91766546/156925397-e82b5e10-506b-45f6-913c-c6485253700d.png)

Let’s explore some of these commands. We’ll start by working with images.

###### Step 4: Working with Docker Images

Docker containers are built from Docker images. By default, Docker pulls these images from [Docker Hub](https://hub.docker.com/), a Docker registry managed by Docker, the company behind the Docker project. Anyone can host their Docker images on Docker Hub, so most applications and Linux distributions you’ll need will have images hosted there.

To check whether we can access and download images from Docker Hub, we will run the following command and the output should indicate that Docker in working correctly.

![26](https://user-images.githubusercontent.com/91766546/156925525-d9733e29-4717-4e06-843e-31cd9187b1a5.png)

Docker was initially unable to find the hello-world image locally, so it downloaded the image from Docker Hub, which is the default repository. Once the image downloaded, Docker created a container from the image and the application within the container executed, displaying the message.

You can search for images available on Docker Hub by using the docker command with the search subcommand. For example, to search for the Ubuntu image, type:

![k7](https://user-images.githubusercontent.com/91766546/156925620-5298e069-583d-4b4b-8d48-2d31e743225a.png)

The script will crawl Docker Hub and return a listing of all images whose name match the search string. This is the output you will get:

![27](https://user-images.githubusercontent.com/91766546/156925629-ad550e08-164c-479b-bce0-42f9d2fcbc5c.png)

Once you’ve identified the image that you would like to use, you can download it to your computer using the pull subcommand.

![28](https://user-images.githubusercontent.com/91766546/156925667-3005ed5d-2ba2-4fc2-9fdf-1278e2dcc3ab.png)

After an image has been downloaded, you can then run a container using the downloaded image with the `run` subcommand. To see the images that have been downloaded to our computer, let's run the following command.

![29](https://user-images.githubusercontent.com/91766546/156925733-60d701a8-5e0c-42c4-96c8-9d4d6726cf40.png)

Next, let’s look at how to run containers in more detail.

#### Step 5 — Running a Docker Container

The hello-world container ran in the previous step is an example of a container that runs and exits after emitting a test message. Containers can be much more useful than that, and they can be interactive. Containers are similar to virtual machines, but more resource-friendly.

Let’s run a container using the latest image of Ubuntu.

![k9](https://user-images.githubusercontent.com/91766546/156926153-0b5da7d9-3b7e-4e51-840e-bc4b39bb849d.png)

As you can see after we run the ***$ docker run -it ubuntu*** command our prompt should change to reflect the fact that we’re now working inside the container and should take this form:

![k8](https://user-images.githubusercontent.com/91766546/156926023-c2136045-53b0-4c79-89c6-5d0db0f7f3e6.png)

Note the container id in the command prompt. In this example, it is cf1adc04b5a0. we’ll need that container ID later to identify the container when we want to remove it.

Now we can run any command inside the container. For example, let’s update the package database inside the container. You don’t need to prefix any command with sudo, because you’re operating inside the container as the root user:

![k99](https://user-images.githubusercontent.com/91766546/156926308-57cff537-17f5-4aa2-88ff-e4d34c421823.png)

Then install any application in it. Let’s install Node.js:

![k999](https://user-images.githubusercontent.com/91766546/156926278-1bd46b6f-3f09-467a-bdc7-1ca34a82b8e0.png)

This installs Node.js in the container from the official Ubuntu repository. Please select the geographic area that you live in. Select the city and region that best corresponds to your time zone.

![31](https://user-images.githubusercontent.com/91766546/156926380-2fb1a533-4a0e-4311-8fd9-6dae529c679e.png)

When the installation finishes, verify that Node.js is installed:

![32](https://user-images.githubusercontent.com/91766546/156926350-b956f08a-8459-4933-88a5-2fcaa2fb2da2.png)

To exit the container, type exit.

Nice work! In the next section, we will look at managing the containers on our system.

#### Step 6 — Managing Docker Containers

After using Docker for a while, you’ll have many active (running) and inactive containers on your computer. To view the active ones, use:

![33](https://user-images.githubusercontent.com/91766546/156926459-181517e5-5190-4b9e-aa00-9a52942369b8.png)

you started two containers; one from the `hello-world` image and another from the `ubuntu` image. Both containers are no longer running, but they still exist on your system. To view all containers — active and inactive, let's run this command:

![34](https://user-images.githubusercontent.com/91766546/156926476-47fd34e3-821e-4d7f-b00d-bb6bc64e077c.png)

And in order to check the latest container we created we can use the following command.

![35](https://user-images.githubusercontent.com/91766546/156926520-ca048440-1943-4bd7-9d37-2e29aac85488.png)

When we want to start a stopped container, we can use docker start, followed by the container ID or the container’s name. 

![36](https://user-images.githubusercontent.com/91766546/156926552-00e4ce4a-85b8-47b2-8b65-3a125e8510af.png)

Now if we use the ***docker ps*** command and check we can see that our container is up.

![37](https://user-images.githubusercontent.com/91766546/156926588-f329a4be-ef76-48bd-8382-3a820bab8651.png)

To stop a running container, use docker stop, followed by the container ID or name. 

![38](https://user-images.githubusercontent.com/91766546/156926613-94d63b6e-eba8-403c-a836-fd86a14b47c2.png)

When we no longer need a container, we can remove it with the ***docker rm*** command, again using either the container ID or the name. Let's use the docker ps -a command to find the container name for the container associated with the hello-world image and remove it.

![40](https://user-images.githubusercontent.com/91766546/156926636-d43db43b-5d2e-4c26-a657-542dc2e2358d.png)

Containers can be turned into images which you can use to build new containers. Let’s look at how that works.

#### Step 7 — Committing Changes in a Container to a Docker Image

This section shows you how to save the state of a container as a new Docker image. After installing Node.js inside the Ubuntu container, we now have a container running off an image, but the container is different from the image we used to create it. But we might want to reuse this Node.js container as the basis for new images later.

Then let's commit the changes to a new Docker image instance using the following command.

![41](https://user-images.githubusercontent.com/91766546/156926781-85f6aee1-4745-4361-b748-1c041c67d0fd.png)

The -m switch is for the commit message that helps us and others know what changes we made, while -a is used to specify the author which is 'sunny' in this case. The container_id is the one we noted earlier in the project. Unless you created additional repositories on Docker Hub, the repository is usually your Docker Hub username.

Listing the Docker images again will show the new image, as well as the old one that it was derived from like this.

![42](https://user-images.githubusercontent.com/91766546/156926902-9123cc38-c505-4a41-9e59-605850ad5a84.png)

In this example, ubuntu-nodejs is the new image, which was derived from the existing ubuntu image from Docker Hub. The size difference reflects the changes that were made. The change was that NodeJS was installed. So next time we need to run a container using Ubuntu with NodeJS pre-installed, we can just use the new image.

#### Step 8 — Pushing Docker Images to a Docker Repository

The next logical step after creating a new image from an existing image is to share it with a select few of our friends, the whole world on Docker Hub, or other Docker registry that we have access to. To push an image to Docker Hub or any other Docker registry, we must have an account there. If you don't have a Docker hub account already then please follow the instructions from this video https://youtu.be/ty91qhd7L24 and create one.

We’ll be prompted to authenticate using our Docker Hub password. If our password is correct then, authentication should succeed.

![43](https://user-images.githubusercontent.com/91766546/156932324-e894624f-ee14-4c80-97c2-73c1d45fc9c7.png)

Note: If the Docker registry username is different from the local username we used to create the image, we will have to tag our image with our registry username. For the example given in the last step, use the following command:

![44](https://user-images.githubusercontent.com/91766546/156932546-7c01e1f8-c753-4394-af10-625c55cff814.png)

To push the ubuntu-nodejs image to your Docker repository, the command would be:

![45](https://user-images.githubusercontent.com/91766546/156932576-02540bab-758c-4624-898b-7b0789cdb982.png)

After pushing an image to a registry, it should be listed on your account’s dashboard, like that show in the image below.

![k77](https://user-images.githubusercontent.com/91766546/156935092-df2e851d-9182-4c94-8878-7152021f5f61.png)

We can now use docker pull senaith/ubuntu-nodejs to pull the image to a new machine and use it to run a new container.

## Part 3
### Installing Docker Compose

[Docker Compose](https://docs.docker.com/compose/) is a tool that allows you to run multi-container application environments based on definitions set in a YAML file. It uses service definitions to build fully customizable environments with multiple containers that can share networks and data volumes.

Let's install Docker Compose on an Ubuntu 20.04 server and see how to get started using this tool.

#### Prerequisites

We will need:

- Access to an Ubuntu 20.04 local machine or development server as a non-root user with sudo privileges. If we’re using a remote server, it’s advisable to have an active firewall installed.
- Docker installed on our server or local machine

To make sure we obtain the most updated stable version of Docker Compose, we’ll download this software from its [official Github repository](https://github.com/docker/compose).

First, confirm the latest version available in their [releases page](https://github.com/docker/compose/releases). At the time of this writing, the most current stable version is `1.27.4`.

The following command will download the `1.27.4` release and save the executable file at ***/usr/local/bin/docker-compose***, which will make this software globally accessible as ***docker-compose***:

![46](https://user-images.githubusercontent.com/91766546/156935528-4817f168-52e5-4d30-b6d4-26d8271bb235.png)

Next, set the correct permissions so that the docker-compose command is executable:

![47](https://user-images.githubusercontent.com/91766546/156935640-4329f884-c568-4776-8c6f-625cd444b669.png)

To verify that the installation was successful, we can run:

![48](https://user-images.githubusercontent.com/91766546/156935708-4b21e807-a951-41fc-89b9-fb4143403bd0.png)

Docker Compose is now successfully installed on our system. In the next section, we’ll see how to set up a docker-compose.yml file and get a containerized environment up and running with this tool.

### Setting Up a docker-compose.yml File

To demonstrate how to set up a docker-compose.yml file and work with Docker Compose, we’ll create a web server environment using the official Nginx image from Docker Hub, the public Docker registry. This containerized environment will serve a single static HTML file.

Let's start by creating a new directory in our home folder, and then moving into it:

![49](https://user-images.githubusercontent.com/91766546/156936813-3b712e4e-0544-4ddd-8898-6edbff5af288.png)

In this directory, we will set up an application folder to serve as the document root for our Nginx environment:

![50](https://user-images.githubusercontent.com/91766546/156936855-fd0973ed-0cb8-4ebe-ab1d-3712f3ea4e78.png)

Using our preferred text editor, we will create a new index.html file within the app folder. For this project the text editor we are using is Nano.

![51](https://user-images.githubusercontent.com/91766546/156936869-2b800016-faa8-4200-96d0-9d2cb1f0bdc7.png)

Place the following content into this file:

![k65](https://user-images.githubusercontent.com/91766546/156937260-6ef23498-3926-427c-8fb9-7088236bf0f6.png)

Let's save and close the file typing `CTRL+X`, then `Y` and `ENTER` to confirm.

Next, create the `docker-compose.yml` file:

![52](https://user-images.githubusercontent.com/91766546/156937350-ee0823a3-50c6-4818-95b3-c4348688ddda.png)

Place this content into this file.

![k55](https://user-images.githubusercontent.com/91766546/156937400-aded816b-01c2-4862-9f42-02969efe70a2.png)

Save and close the file.

We have set up a demo page and a `docker-compose.yml` file to create a containerized web server environment that will serve it. In the next step, we’ll bring this environment up with Docker Compose.

### Running Docker Compose

With the docker-compose.yml file in place, we can now execute Docker Compose to bring our environment up. The following command will download the necessary Docker images, create a container for the web service, and run the containerized environment in background mode:

![53](https://user-images.githubusercontent.com/91766546/156937780-57e61d53-3d46-4302-bd2b-2a8bb74f3f5f.png)

Our environment is now up and running in the background. To verify that the container is active, let's run the following command. This command will show you information about the running containers and their state, as well as any port redirections currently in place.

![54](https://user-images.githubusercontent.com/91766546/156937860-b68518ea-f44b-4ac5-b50c-4e470c3a179c.png)

We can now access the demo application by pointing our browser to either `localhost:8000` if we are running this demo on our local machine, or `our_server_domain_or_IP:8000` if it's are running on a remote server.

We’ll see a page like this:

![67](https://user-images.githubusercontent.com/91766546/156938011-6deaab59-bf23-482c-bffc-0a817abf7e75.png)

#### Getting Familiar with Docker Compose Commands

Let's see how to use Docker Compose commands to manage and interact with our containerized environment.

To check the logs produced by our Nginx container, we can use the `logs` command:

![55](https://user-images.githubusercontent.com/91766546/156938171-a8088544-0036-4166-9277-75f834173c42.png)

If we want to pause the environment execution without changing the current state of our containers, we can use:

![56](https://user-images.githubusercontent.com/91766546/156938227-52e220ba-9d1f-40d3-93be-9de2d4f57e59.png)

To resume execution after issuing a pause:

![57](https://user-images.githubusercontent.com/91766546/156938243-b5a16074-3ce5-411b-8058-c768c5de5a9e.png)

Additionally, we can use the **stop** command to terminate the container execution, but it won’t destroy any data associated with our containers:

    $ docker-compose stop

Output
Stopping compose-demo_web_1 ... done

If we want to remove the containers, networks, and volumes associated with this containerized environment, use the down command:

   $ docker-compose down

    Output
    Removingcompose-demo_web_1 ... done
    Removing networkcompose-demo_default

Notice that this won’t remove the base image used by Docker Compose to spin up your environment (in our case, nginx:alpine). This way, whenever you bring your environment up again with a docker-compose up, the process will be much faster since the image is already on your system.

We can also use the following command in case we want to also remove the base image from your system:
 
  $ docker image rm nginx:alpine

We’ve seen how to install Docker Compose and set up a containerized environment based on an Nginx web server image. We’ve also seen how to manage this environment using Compose commands.

Finally, to enable SSL you’ll need a domain name pointed at your server’s public IP address. This should be something like example.com or matomo.example.com. For this project I used a domain purchased from Godaddy.com. but feel free to use a host website of your choice.

After purchasing our domain, we will need to add a DNS record as we can see below.

![k44](https://user-images.githubusercontent.com/91766546/156939407-cdcc8065-0e8b-4d10-9853-15acdd6f419b.png)

## Part 4
## Running Matomo and MariaDB with Docker Compose

Our first step will be to create the Docker Compose configuration that will launch containers for both the Matomo app and a MariaDB database.

This section will put your configuration inside a `matomo` directory in our home directory. First ensure you’re in your home directory and then create the matomo directory and cd into it:

![58](https://user-images.githubusercontent.com/91766546/156939541-a696a847-c636-4687-9aba-59c2f2064f2d.png)

Now let's open a new blank YAML file called docker-compose.yml:

![59](https://user-images.githubusercontent.com/91766546/156939567-6b0db32c-c7c1-486b-9feb-8a24a07ee730.png)

This is the configuration file that the docker-compose software will read when bringing up your containers. Open your text editor and paste the following into the file:

![k33](https://user-images.githubusercontent.com/91766546/156939631-d976cd31-837f-4e63-8d8c-51a55ecdcb0d.png)

Save the file and exit your text editor to continue. 

The MariaDB container needs some configuration to be passed to it through environment variables in order to function. The `docker-compose.yml` file lists these environment variables, but not all of them have associated values. That’s because it’s good practice to keep passwords out of your `docker-compose.yml` file, especially if you’ll be committing it to a Git repository or other source control system.

Instead, we’ll put the necessary information in a `.env` file in the same directory, which the `docker-compose` command will automatically load when we start our containers.

Open a new `.env` file with `nano` and insert the following text.

![60](https://user-images.githubusercontent.com/91766546/156939682-83bb7a46-88ae-4f38-911c-8691b1911fc0.png)

![pass](https://user-images.githubusercontent.com/91766546/156939725-666bcc71-fcee-4ff9-99b3-ecddbba1cc99.png)

We’ll need to fill in a user name and password, as well as a strong password for the MariaDB root superuser account. One way of generating a strong password is to use the openssl command, which should be available on most any operating system. The following command will print out a random 30 character hash that you can use as a password:

![61](https://user-images.githubusercontent.com/91766546/156939743-98b04682-16aa-41d9-9b63-003a23a05b8b.png)

When you’re done filling out the information in your `.env` file, save it and exit your text editor.

You’re now ready to bring up the two containers with `docker-compose`.

![62](https://user-images.githubusercontent.com/91766546/156940092-76ec9348-4f66-4ff8-8704-012eba77b198.png)

When that’s done, Matomo should be running. we can test that a webserver is running at localhost:8080 by fetching the homepage using the curl command. This will print out only the HTTP headers from the response.

![63](https://user-images.githubusercontent.com/91766546/156940103-53911f45-9767-4482-b383-d2bf452957b9.png)

### Installing and Configuring Nginx

Putting a web server such as Nginx in front of your Matomo server can improve performance by offloading caching, compression, and static file serving to a more efficient process. 
First, refresh your package list, then install Nginx using apt.

![64](https://user-images.githubusercontent.com/91766546/156955540-05c2cdd8-9f68-4022-a4ad-1e8e955cc054.png)

![65](https://user-images.githubusercontent.com/91766546/156955556-eacd9937-0164-425e-ac64-04d7670733ea.png)

Allow public traffic to ports 80 and 443 (HTTP and HTTPS) using the “Nginx Full” UFW application profile:

![66](https://user-images.githubusercontent.com/91766546/156955706-261b555c-2e64-40c1-947a-80a1bcdd601d.png)

Next, open up a new Nginx configuration file in the /etc/nginx/sites-available directory. We’ll call it matomo.conf.

![67](https://user-images.githubusercontent.com/91766546/156955749-0151efd0-8735-4ee5-b54a-5bdea3ba7027.png)

Paste the following into the new configuration file, being sure to replace your_domain_here with the domain that you’ve configured to point to your Matomo server. 

![k34](https://user-images.githubusercontent.com/91766546/156956036-e4bbfa26-0525-4b25-95db-5c6587e7dbda.png)

Save and close the file, then enable the configuration by linking it into /etc/nginx/sites-enabled/

![68](https://user-images.githubusercontent.com/91766546/156955948-b657b420-a06e-4bb9-b540-99f87b5cc02a.png)

Use nginx -t to verify that the configuration file syntax is correct.

![69](https://user-images.githubusercontent.com/91766546/156957377-e8a7b125-ddec-4726-bbb2-20cb429e692f.png)

And finally, reload the nginx service to pick up the new configuration.

![70](https://user-images.githubusercontent.com/91766546/156957436-dc063d9c-4810-4efa-8c48-e05d6b746117.png)

Your Matomo site should now be available on plain HTTP. Load http://your_domain_here (you may have to click through a security warning) and it will look like this.

![matomopage](https://user-images.githubusercontent.com/91766546/156959582-de971f39-7cd6-494d-bf51-bedf82ef7eec.png)

Now that you have your site up and running over HTTP, it’s time to secure the connection with Certbot and Let’s Encrypt certificates. You should do this before going through Matomo’s web-based setup procedure.

### Installing Certbot and Setting Up SSL Certificates

First, install Certbot and its Nginx plugin:

![71](https://user-images.githubusercontent.com/91766546/156957921-bdc402ac-57b6-4aa6-b940-f34203baf2c0.png)

Next, run certbot in --nginx mode, and specify the same domain you used in the Nginx server_name config. You’ll be prompted to agree to the Let’s Encrypt terms of service, and to enter an email address. Afterwards, you’ll be asked if you want to redirect all HTTP traffic to HTTPS. It’s up to you, but this is generally recommended and safe to do. After that, Let’s Encrypt will confirm your request and Certbot will download your certificate:

![72](https://user-images.githubusercontent.com/91766546/156957979-fd672344-9186-48a0-8fff-46e99222ba1b.png)

Certbot will automatically reload Nginx to pick up the new configuration and certificates. Reload your site and it should switch you over to HTTPS automatically if you chose the redirect option.

Your site is now secure and it’s safe to continue with the web-based setup steps.

### Setting Up Matomo

Back in your web browser you should now have Matomo’s **Welcome!** page open via a secure `https://` connection. Now you can enter usernames and passwords safely to complete the installation process.

Click the **Next** button. You’ll be taken to the **System Check** step:

![matomo2](https://user-images.githubusercontent.com/91766546/156958486-a64ddd27-d534-4d39-9055-ca50b5c28eac.png)

This is a summary of the system Matomo is running on, and everything should be green checkmarks indicating there are no problems. Scroll all the way to the bottom and click the **Next** button.

Now you’ll be on the **Database Setup** page:

![matomo4](https://user-images.githubusercontent.com/91766546/156958563-c71a98c8-c092-4075-b0d2-e819c18f894b.png)

The information you fill in on this page will tell the Matomo application how to connect to the MariaDB database. You’ll need the MARIADB_USER and MARIADB_PASSWORD that you chose in Step 1. You can copy them out of your .env file if you need to.

Fill out the first four fields:

Database Server: db
Login: the username you set in the MARIADB_USER environment variable
Password: the password you set in the MARIADB_PASSWORD environment variable
Database Name: matomo

Click Next once more. You’ll get a confirmation that the database Tables were set up correctly.

![matomo5](https://user-images.githubusercontent.com/91766546/156958700-201b4c59-da8e-4493-9d16-ce5c2ccc3d0d.png)

Click Next again. You’ll then need to set up an admin user, and finally you’ll set up information about the first website you want to collect analytics for.

![matomo6](https://user-images.githubusercontent.com/91766546/156958747-08856550-d1b8-4cec-83ef-ce72937103ff.png)

![matomo8](https://user-images.githubusercontent.com/91766546/156959664-414963d8-b182-4af8-b835-c7dab3289b9e.png)

After all that, you should end up on step 8, a Congratulations page. You’re almost all done. Scroll down to the bottom and click the Continue to Matomo button, and you’ll be taken to the homepage.

![Screenshot_20220302-172112_Chrome](https://user-images.githubusercontent.com/91766546/156959382-9a0fe403-990b-4de4-80bf-1492d196a02a.jpg)

Back on the command line, open up the configuration file with a text editor.

![k11](https://user-images.githubusercontent.com/91766546/156960714-eed6f4c8-6e42-467f-bcac-0e880c1f6d01.png)

Near the top you should have a [General] section as shown below. Add these lines to the end of that section:

trusted_hosts[] = "localhost:8080"
assume_secure_protocol = 1
force_ssl = 1

Let's save and close the configuration file, then switch back to our browser and reload the page. The error should be gone, and you’ll be presented with a login prompt. Log in with the admin account we created during setup, and we should be taken to the dashboard.

![matomo9](https://user-images.githubusercontent.com/91766546/156960955-309d2ac2-0c9a-41b6-a30b-8bf178c64f72.png)

Please note, that since you probably have not set up your tracking code yet, the dashboard will indicate that no data has been recorded.

Simply follow the instructions to finish setting up the JavaScript code on your website to start receiving analytics data!

## Conclusion

Congratulations! we have successfully launched the Matamo analytics app and a MariaDB database using Docker Compose, and then set up an Nginx reverse proxy and secured it using Let’s Encrypt SSL certificates! Good job!!!






























