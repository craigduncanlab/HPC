# Nimbus Virtual Machine Setup
Created 2019
Craig Duncan

These notes help getting started on a Nimbus instance, if you already have an account and login for Pawsey.

1. Use your allocation to setup an instance, configure OS image and data drives.
2. Start instance (launch through web dashboard for Nimbus)
3. Gather your login information (IP address, image type etc)
4. Setup your private keys and ssh login
5. Login

General Pawsey Resources:

**Pawsey Nimbus Docs**

[Pawsey_UsingNimbus](https://pawseysupercomputing.github.io/using-nimbus/)

[Pawsey_KeyPairs](https://pawseysupercomputing.github.io/using-nimbus/04-making-keypairs/index.html)

[Pawsey_SecurityGroup](https://pawseysupercomputing.github.io/using-nimbus/05-simple-security-groups-and-networking/index.html)

[Pawsey_LaunchNimbusInstance](https://pawseysupercomputing.github.io/using-nimbus/06-launch-an-instance/index.html)

[Pawsey_AttachingStorage](https://pawseysupercomputing.github.io/using-nimbus/07-attaching-storage/index.html)

**PawseySupportDocs**

[PawseySupport_CreateInstance](https://support.pawsey.org.au/documentation/display/US/Create+a+Nimbus+Instance)

[PawseySupport_AccessInstance](https://support.pawsey.org.au/documentation/display/US/Access+and+Use+Your+Nimbus+Instance)

[PawseySupport_Network](https://support.pawsey.org.au/documentation/display/US/Create+a+Nimbus+Instance#CreateaNimbusInstance-Selectthenetwork)


"You will (usually) use a Secure Shell (SSH) connection to run jobs and do work on your virtual machine. Meanwhile, you will use the Nimbus dashboard, a website we maintain, to manage your virtual machine/s (instances). This is shown below"

key aspects: 
* nimbus website (administration)
* SSH connection (to your 'virtual machine' (cloud instance) on the Nimbus server)

# Pawsey Nimbus Instance Glossary

| Term | Definition |
|:-----|:---------|
|Allocation | the infrastructure specifications you have been allocated for a specific project (# of instances, RAM, memory, etc.) |
| Instance | a virtual machine (located on Nimbus servers, you access via the SSH) |
| Instance Flavor | the size of your instance (RAM, VCPUs, root disk, ephemeral disk) |
| Key Pair | this is a key you generate which allows you to login to your instance |
| Security Groups | these are the incoming/outgoing permissions you allow for your instance (IP addresses, ports, etc.). You must allow at least allow ssh connections to access your instance! |
| IP Address | the virtual address of your instance |
| Snapshot & Image | a snapshot is a copy of your Root Disk storage you create before terminating your instance. You can launch this image later as a new instance (useful for booting a fully configured virtual machine with all required applications pre-installed) |
| Volume & Object Storage | similar to an external hard drive you attach and detach to one or multiple instances (useful for big and important data or fully configured virtual machines).|

# Nimbus Supercomputer portal 

The Nimbus dashboard is accessed through the main Nimbus login page.  It is an example of the 'OpenStack' HPC administration system.   Once you are familiar with this, working with Nimbus instances in the future will be easier.

[Nimbus Login](https://nimbus.pawsey.org.au) 

Open this URL now in a browser window, you will see the login window. 

For “domain”, enter ‘pawsey’ and your user name and password.

# Key pairs for secure access to OpenStack systems

What are key pairs?  Each connection made from a remote client computer using SSH (secure shell) requires a public/private key encryption pair to be created, preferably using the RSA protocol (not DSA) for linux servers.    

The purpose of the key pairs is to authenticate the login to an instance from a particular computer without needing the Project owner to provide the usual username and password for the Project. The holder of the private key of the authorised ‘key pair’ are, in effect, pre-authorised to gain access to the Nimbus instance directly for SSH purposes.

Significantly, the OpenStack software uses Cloud-Init, which restricts the ssh connection to one key pair (do not assume you can have multiple public keys used for this purpose per instance).

Creating and saving the public key requires cooperation from the Project owner who administers the Project and its instances (who will import or save the public key).  Alternatively the Project owner will create both the keys in the key pair using the Nimbus Dashboard (‘nova’) and then save the private key to their own or the instance user’s computer.   This option is less secure than having the person wanting the SSH login to supply the public key.

The VM doesn't set up passwords.  However, you will secure with a public [lock] and private [key] system.  The private key is a .pem file.
RFC1421-4 defines it. [RFC1421](https://tools.ietf.org/html/rfc1421])

"This document defines message encryption and authentication
   procedures, in order to provide privacy-enhanced mail (PEM) services
   for electronic mail transfer in the Internet."

See [WhatIsAPEMfile](https://serverfault.com/questions/9708/what-is-a-pem-file-and-how-does-it-differ-from-other-openssl-generated-key-file)

"Store your private key in a safe place! Losing your private key means losing every access to your instances. Not even the Nimbus support can give you access to your instances if you have lost your private key."

# Setting up the Instance

## Specifications to launch the Nimbus instance (OpenStack)

Each instance needs an instance name, an area (e.g. "nova") and if you want, you can create multiple instances with the same setup.  Probably not needed initially.

Each instance needs an operating system.  2019 - Pawsey support Ubuntu LTS 16.04 and Centos 7 as options.

Other options require more support from your own team.

## Security group for the instance

The 'openstack' web-based administration for Nimbus is used to set up security groups.

"To make a security group, you will go to the Network / Security Groups section and hit “Create Security Group”"

Some of the further setup includes options for having one or more ports for data input and output to the instance (ingress and egress).  

The instance configuration option 'SSH Access' will automatically connect the privileges for the instance with the external login user group.

Each virtual instance (VI) will be given a unique IP address and PORT address on that IP machine.

Each SSH (secure shell connection) is for a virtual machine, over port 22, so SSH login details ensure security of the machine.  

Do not share logins.

The default security and connection setup (“default” security group in the OpenStack API) does not allow remote SSH connections (i.e. from computers outside the cluster network).

In order to allow remote SSH connections, another security group must be added in the Dashboard. Within that group, you then need to specify a rule that permits SSH connections via TCP on Port 22, from any IP address.  Pawsey have a predefined group that does this, called "SSH Access" – just select this as the new Security Group.  When this is done, you should have 2 security groups: ‘default’ and something named ‘ssh’ or similar.

## Nimbus Instance "Flavor" (flavour is an OpenStack concept)

There's a 5GB default storage area, which relates to the virtual machine running an operating system, rather than the storage device for data work.  The secondary storage will be setup once the instance is live, so just choose a defalt option for now.

The 2019 setup by Pawsey includes image sizes up to 48GB by default (jumbo, xlarge etc)

Since Pawsey appears to be using OpenStack, this is also a bit of background on what Flavor is about : 
[OpenStackFlavor](https://docs.openstack.org/nova/pike/admin/flavors.html) 
[Pawsey_OpenStack](https://support.pawsey.org.au/documentation/display/US/Nimbus+-+Migrating+Instances+from+NeCTAR)

The web based setup is hiding some of the CLI commands from users.

## Network and login information

```
Pawsey requires you to select both networks, and the router.  Most likely, you’ll create an IP4 private network, DHCP enabled, on Google public DNS from routers that Pawsey allows you to select through its administration system.  This will then be expanded by adding a ‘floating IP’ address that will allow remote connections (e.g. through SSH, if permitted).
```

You may need to login to the web portal, and choose 'networks' from instance to see the public IP allocation.

There is a private network option. 

(Public networking is not local, and the setup is in the background so do not concern yourself with this)

Go to Nimbus web portal for administration.  
You may later need to login to your data, so choose 'networks' from the Instance menu to see the public IP allocation that you will use for your login.

e.g.
Network shows this:
Public external	

public-external-subnet	1.2.3.0/21	IPv4	1.2.3.1 (gateway)

Routers shows gateway for you project public IP address:

1.2.3.245

## Public/private keys and using them to SSH login

SSH Login on the terminal (in general):
ssh –i ~/.ssh/My_Key_Pair.pem login_name@###.###.##.##

So a keypair is essential to start.   The login is the linux OS image you chose, not you as a user!  Like this:

ssh -i ~/.ssh/SCG.pem ubuntu@###.###.##.##

(the -i flag is for 'identity file')
If your list of identities is empty, check with this: 
ssh-add -l

You may also need to do this:
ssh-add ~/.ssh/SCG.pem

if you are adding an identity, from a locally generated key, you just add the filename with the private key name.

## Issues with SSH permissions

Check your key permissions are set to the correct permissions (Pawsey notes at one stage said 700 but 600 is needed).

chmod 600 ~/.ssh/id_rsa

This also says chmod 600:

[Pawsey_Access](https://pawseysc.github.io/using-nimbus/04-access-and-use-instance/index.html)

If you have an SSH key pair through the Nimbus dashboard, you may instead want to try creating the SSH key on your local machine, then importing the public key into Nimbus. The SSH key pair can be generated locally with the command "ssh-keygen". Be sure to ONLY import the public key on Nimbus; the private key must remain where it is.

[PawseySupport_SSHAccess](https://support.pawsey.org.au/documentation/display/US/Common+Issues#CommonIssues-SSHAccess-permissiondenied)

Within the instance creation menus (where you import keys) it says this:

```
From a Linux system, generate the key pair with the ssh-keygen command:

ssh-keygen -t rsa -f cloud.key

This command generates a pair of keys: a private key (cloud.key) and a public key (cloud.key.pub).
```

You may have to relaunch instance (e.g. with Ubuntu 19.10) to get a public IP address showing for the instance
Then tried try to login again (with existing keys already there for project)

ssh -i ~/.ssh/SCG1 ubuntu@your.ip.address.0

# Data Storage setup - mount volumes ready for use on a running instance.

Three steps:

* Create volume (web interface handles this: an fdisk command)
* Format  (command line mkfs command : `sudo fdisk -l /dev/vdc`)
* Mount (two steps: mkdir data and then `sudo mount /dev/vdc`)


