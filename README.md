# Project-3 
# Matomo-Web-Analytics-on-Cloud

In this project you will install Matomo and a MariaDB database using Docker Compose, then install Nginx to act as a reverse proxy for the Matomo app. Finally, you will enable secure HTTPS connections by using Certbot to download and configure SSL certificates from the Let’s Encrypt Certificate Authority.

# Introduction
## What is Matomo?

Matomo, formerly known as Piwik, is a downloadable, Free (GPL licensed) web analytics software platform. It provides detailed reports on your website and its visitors, including the search engines and keywords they used, the language they speak, which pages they like, the files they download and so much more. Matomo (Piwik) aims to be an open source alternative to Google Analytics. Matomo is PHP MySQL software which you download and install on your own webserver.

So, let's begin this project. 

Step 1:

We need an Ubuntu 20.04 server, with the UFW firewall enabled. In Projects 1 and 2 we have discussed in detail on how to create a free tier AWS account and set up our EC2 server. The initial set up and configuration process for this project is the same so inorder to follow up with these steps please refer to https://github.com/Senaith/Project-1-Web-stack-implementation-on-aws or https://github.com/Senaith/Project-2-LEMP-Stack-Implementation
Following these steps will increase the security and usability of your server, and will give you a solid foundation for subsequent actions.

Step 2:

To log into your server, you will need to know your server’s public IP address.  You will also need the password or — if you installed an SSH key for authentication — the private key for the root user’s account. 
 
![q4](https://user-images.githubusercontent.com/91766546/156699072-126b9a13-a724-4cc8-80bc-9e4b7d5ff3c7.png)

Step 3

If you are not already connected to your server, log in now as the root user using the following command (substitute the highlighted portion of the command with your server’s public IP address):

![2](https://user-images.githubusercontent.com/91766546/156699329-0103f386-6611-4a26-8f63-4ef90911136b.png)

Step 4:

Create a new user using the following command.

![3](https://user-images.githubusercontent.com/91766546/156699720-5199792c-4ef6-4269-87f5-4ddd652a0283.png)

Once you are logged in as root, you’ll be able to add the new user account. In the future, we’ll log in with this new account instead of root.
This command creates a user named 'sunny'. You can replace that with a user name of your own. 

As you can see above, after running that command you will be asked a few questions, starting with the account password. Enter a strong password and, optionally, fill in any of the additional information if you would like. This is not required and you can just hit `ENTER` in any field you wish to skip.

Step 5:

Let's grant the new user administrative privileges. Run the following command in your terminal.This will allow our normal user to run commands with administrative privileges by putting the word `sudo` before the command. To add these privileges to our new user, we need to add the user to the **sudo** group. By default, on Ubuntu 20.04, users who are members of the **sudo** group are allowed to use the `sudo` command.

![4](https://user-images.githubusercontent.com/91766546/156700154-78672756-23b9-4130-9217-5a5eee247c9e.png)

Step 6:

The UFW firewall will be used to ensure that only connections to certain services are permitted. The OpenSSH is the service that will allows us to connect to our server, and it has a profile registered with UFW. IN order to check this we need to run this command:

![5](https://user-images.githubusercontent.com/91766546/156703876-75e7f8b4-d927-4b5b-8cfc-69918d858805.png)

We need to make sure that the firewall allows SSH connections so that we can log back in next time. We can allow these connections by typing:

![6](https://user-images.githubusercontent.com/91766546/156703929-6454401c-04b7-42c5-89d0-215d046713c1.png)

Afterwards, we can enable the firewall by running the following command. Hit the Y key and Enter when prompted.

![7](https://user-images.githubusercontent.com/91766546/156704102-50508378-715a-45f3-9618-9a6402121806.png)

You can see that SSH connections are still allowed by typing:

![8](https://user-images.githubusercontent.com/91766546/156704275-5d629e7f-e0de-4be6-8714-9fc1ceaabf9a.png)

The firewall is currently blocking all connections except for SSH, so if we install and configure additional services, we will need to adjust the firewall settings to allow traffic in. 

Step 7:

Enabling External Access for Your Regular User

The process for configuring SSH access for our new user depends on whether our server’s root account uses a password or SSH keys for authentication. If you logged in to your root account using a password, then password authentication is enabled for SSH. You can SSH to your new user account by opening up a new terminal session and using SSH with your new username.

![456](https://user-images.githubusercontent.com/91766546/156705382-49ff2f06-a01d-4f52-ae57-a69b2aa2100d.png)

Troubleshooting:

It is possible to receive an error message that says: Permission Denied (publickey). 

![454](https://user-images.githubusercontent.com/91766546/156705474-ca82fb3f-f4c1-4ae7-9ab2-4026df7ffe79.png)

If you do not have this error, please skip ahead to the next section below. Otherwise, if you do have this error, then we can resolve it by modifying the configuration file. Use the following command in your Ubuntu server.

![10](https://user-images.githubusercontent.com/91766546/156705587-9e2ee2f1-290d-41a9-9c41-db542cf21c16.png)

This will open our nano editor and now we can scroll through the file and make the following changes.

Change "#PermitRootLogin prohibit-password" to the following:

# PermitRootLogin yes

Next, change # PasswordAuthentication no to the following:

# PasswordAuthentication yes

Next, we need to restart our server using the following command.

![11](https://user-images.githubusercontent.com/91766546/156885024-fd174a77-0b2e-4756-954b-5fa012b77360.png)

Now we should be able to SSH with your new username. Let's try this command again:

![12](https://user-images.githubusercontent.com/91766546/156885048-09c26609-2d75-425d-9699-bf4ef81e7413.png)


Step 8:

The next step is to set Up SSH Keys on our PC. SSH keys provide a secure way of logging into our server and are recommended for all users.
The first step is to create a key pair on the client machine (usually our computer).

![keygen](https://user-images.githubusercontent.com/91766546/156885579-c1feb5cf-54a0-4140-b541-7adea1f00d8c.png)

After entering the command, we will see the following output. Press Enter to save the key pair into the .ssh/ subdirectory in your home directory, or specify an alternate path.

![k1](https://user-images.githubusercontent.com/91766546/156885832-72343a9f-7eda-4753-b1cd-1f8124015f71.png)

we will then see a prompt asking us to enter a passphrase. Here we optionally may enter a secure passphrase, which is highly recommended. A passphrase adds an additional layer of security to prevent unauthorized users from logging in. After entering our passphrase, we will see an out put similar to this one on our terminal.

![k2](https://user-images.githubusercontent.com/91766546/156885868-b4e1683b-328e-479c-a1f8-91f8bf366951.png)

Step 9:

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

Step 1:

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

Step 2:

Executing the Docker Command Without Sudo (Optional)

By default, the docker command can only be run the root user or by a user in the docker group, which is automatically created during Docker’s installation process.

![21](https://user-images.githubusercontent.com/91766546/156895949-aa154cbf-9201-4f01-8ce8-da28bd7a4421.png)

If you want to avoid typing sudo whenever you run the docker command, add your username to the docker group.

![21](https://user-images.githubusercontent.com/91766546/156896266-070ee72c-7f6f-447a-b169-200252bcc12e.png)

To apply the new group membership, log out of the server and back in, or type the following:

![22](https://user-images.githubusercontent.com/91766546/156896344-8b71332e-0015-4d69-96c2-2d443d68ca4c.png)

You will be prompted to enter your user’s password to continue. Confirm that your user is now added to the Docker group by typing the following:

![k5](https://user-images.githubusercontent.com/91766546/156896450-d627f52d-00e1-4814-8738-7b08f0202501.png)







































