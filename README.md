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
