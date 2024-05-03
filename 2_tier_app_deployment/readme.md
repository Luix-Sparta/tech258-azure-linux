# 2 Tier App deployment

This guide outlines the steps to deploy a 2-tier application on Microsoft Azure. The deployment process involves setting up SSH keys, configuring virtual networks, provisioning virtual machines, deploying a database, and running application scripts. Below are the detailed steps for each component of the deployment process:

- [2 Tier App deployment](#2-tier-app-deployment)
  - [SSH Key](#ssh-key)
  - [- Virtual Network](#--virtual-network)
  - [- Virtual Machine (App)](#--virtual-machine-app)
  - [Database Deployment](#database-deployment)
  - [Database Script](#database-script)
  - [App Script](#app-script)
  - [Testing](#testing)
- [How to create a VM Image in Azure (Database And App)](#how-to-create-a-vm-image-in-azure-database-and-app)
- [Differences between AWS and Azure: SSH Keys, Virtual Networks, and Virtual Machines](#differences-between-aws-and-azure-ssh-keys-virtual-networks-and-virtual-machines)
  - [SSH Keys](#ssh-keys)
    - [AWS](#aws)
    - [Azure](#azure)
  - [Virtual Networks](#virtual-networks)
    - [AWS](#aws-1)
    - [Azure](#azure-1)
  - [Virtual Machines](#virtual-machines)
    - [AWS](#aws-2)
    - [Azure](#azure-2)
  - [More Differences](#more-differences)
  - [User Data](#user-data)
  - [Images](#images)
  - [Monitoring, Alert Management and Auto Scaling](#monitoring-alert-management-and-auto-scaling)
    - [Monitoring Solutions](#monitoring-solutions)
    - [Alert Management](#alert-management)
    - [Auto Scaling](#auto-scaling)
    - [Creating A Dashboard](#creating-a-dashboard)
    - [Performance Testing](#performance-testing)
    - [How To Create An Alert](#how-to-create-an-alert)
    - [Testing Alert](#testing-alert)
  - [Autoscaling Groups](#autoscaling-groups)
    - [Understanding Virtual Machine Scale Sets (VMSS)](#understanding-virtual-machine-scale-sets-vmss)
  - [Steps to Create an Autoscale group](#steps-to-create-an-autoscale-group)
  - [Reimage And Upgrade (If the Userdata is modified)](#reimage-and-upgrade-if-the-userdata-is-modified)
  - [Scale Set Instance Health](#scale-set-instance-health)
  - [How to SSH into Scale Set Instance](#how-to-ssh-into-scale-set-instance)
  - [How to Delete Scale Set](#how-to-delete-scale-set)
  - [Making the Database Private (Azure)](#making-the-database-private-azure)
  - [How to create the 3-subnet architecture (Private Database)](#how-to-create-the-3-subnet-architecture-private-database)
    - [Update NSG rules to make the Private Subnet Stricter](#update-nsg-rules-to-make-the-private-subnet-stricter)


## SSH Key
1. Step 1
Search For SSH keys in azure and click on "SSH keys"

![Step 1](../images/image.png)

2. Step 2
Click "Create"

![Step 2](../images/image-1.png)

3. Step 3
Choose a subscription

![Step 3](../images/image-2.png)

4. Step 4
Select the resource group "tech258"
![Step 4](../images/image-3.png)

5. Step 5
Make sure the correct Region is selected
![Step 5](../images/image-4.png)

6. Step 6
Enter the Key pair name
![Step 6](../images/image-5.png)

7. Step 7
For "SSH public key source" either choose Generate new key pair or Upload existing public key, For this I will chooe Upload existing public key

![Step 7](../images/image-7.png)

8. Step 8
Enter existing public key
![Step 8](../images/image-8.png)

9. Step 9
Create tags
![Step 9](../images/image-12.png)

10. Step 10
Review Changes

![Step 10](../images/image-13.png)

11. Step 11
Click create

![Step 11](../images/image-15.png)

12. Step 12
Refresh and make sure the key is in the list

![Step 12](../images/image-16.png)

## - Virtual Network 
13. Step 13 
In the seach bar of Azure, Input Virutal Network and click it, as in step 1.

![alt text](../images/image-23.png)

14. Step 14
Click Create

![alt text](../images/image-24.png)

15. Step 15
Input the Subscription, Resource group, Virtual Network name and Region

![alt text](../images/image-17.png)

16. Step 16
Click IP Addresses

![alt text](../images/image-25.png)

17. Step 17
Click Add Subnet

![alt text](../images/image-20.png)

18. Step 18
Change the name to public-subnet and change the starting address to 10.0.1.0 and change the Size to /24 

![alt text](../images/image-18.png)

19. Step 19
Create a new subnet and Change the name to private-subnet and change the starting address to 10.0.3.0 and change the Size to /24 

![alt text](../images/image-19.png)

20. Step 20
Then hit next and contiue to the tags section and write owner with your tag name

![alt text](../images/image-21.png)

21. Step 21
Review the Virtual Network and press create

![alt text](../images/image-22.png)

22. Step 22
Refresh and Check the list of networks

![alt text](../images/image-26.png)

## - Virtual Machine (App)
23. Step 23 
Click on the serach bar and input "virtual machines" and click Virtual machines

![alt text](../images/image-27.png)

24. Step 24
Click Create

![alt text](../images/image-34.png)

25. Step 25
Click Azure virtual machine
![alt text](../images/image-35.png)

26. Step 26
In project details, Select the Subscription and Resource group

![alt text](../images/image-36.png)

27. Step 27
In the instance details section, Change the Virutal machine name, Region, Image and Size

![alt text](../images/image-37.png)

28. Step 28
In the Administrator account section, Cahnge the Authentication type to SSH public key, Change the Username to adminuser, change the SSH public key source to Use existing key stored in Azure and Select the previous key with Stored Keys

![alt text](../images/image-38.png)

29. Step 29
Click on Disks

![alt text](../images/image-39.png)

30. Step 30
In OS disk, change OS disk type to Standard SSD (locally-redundant storage)

![alt text](../images/image-40.png)

31. Step 31
Click on Networking

![alt text](../images/image-41.png)

32. Step 32
In Network interface, Choose the Virtual network from previous steps and select the public-subnet Subnet.

![alt text](../images/image-42.png)

33. Step 33
Click Advanced for NIC network security group

![alt text](../images/image-43.png)

34. Step 34
For Configure network security group, Click Create new

![alt text](../images/image-44.png)

35. Step 35
Choose a Name and click "Add an inbound rule"

![alt text](../images/image-45.png)

36. Step 36
Click Serivce and then HTTP, Then click Add

![alt text](../images/image-46.png)

37. Step 37
Add another inbound rule, however change Destination port ranges to 3000 and protocol to TCP then click Add 

![alt text](../images/image-47.png)

38. Step 38
Review changes and click ok

![alt text](../images/image-48.png)

39. Step 39
Navigate to Tags

![alt text](../images/image-49.png)

40. Step 40
Click Owner and add Name

![alt text](../images/image-52.png)

41. Step 41
Click Review + create
![alt text](../images/image-53.png)

42. Step 42
Review changes and click Create

![alt text](../images/image-54.png)

43. Step 43
Click refresh and check the list

![alt text](../images/image-55.png)

## Database Deployment
44. Step 44
Complete the steps for the App deployment however for the networking change the subnet to private-subnet.

## Database Script
45. Step 45
Connect to the instance and run the Database script

```
#!/bin/bash

echo updating...
sudo apt update -y
echo done!

echo upgrading packages...
sudo DEBIAN_FRONTEND=noninteractive apt upgrade -y
echo done!

echo importing the public key used by the package management system
sudo apt-get install gnupg curl
echo done!

echo importing the MongoDB public GPG key
curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | \
   sudo -E gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
   --dearmor --yes
echo done!

echo creating a list file for MongoDB
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
echo done!

echo reloading local package database
sudo apt-get update
echo done!

echo installing mongo db 7.0.6
sudo DEBIAN_FRONTEND=noninteractive apt-get install -y mongodb-org=7.0.6 mongodb-org-database=7.0.6 mongodb-org-server=7.0.6 mongodb-mongosh=2.2.4 mongodb-org-mongos=7.0.6 mongodb-org-tools=7.0.6
echo done!

echo set versions
echo "mongodb-org hold" | sudo dpkg --set-selections
echo "mongodb-org-database hold" | sudo dpkg --set-selections
echo "mongodb-org-server hold" | sudo dpkg --set-selections
echo "mongodb-mongosh hold" | sudo dpkg --set-selections
echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
echo "mongodb-org-tools hold" | sudo dpkg --set-selections
echo done!

echo configuring bind IP in mongo db config file - 0.0.0.0
sudo sed -i "s,\\(^[[:blank:]]*bindIp:\\) .*,\\1 0.0.0.0," /etc/mongod.conf
echo done!

echo restarting mongo db #f(or start)
sudo systemctl restart mongod
echo done!

echo enabling mongo db
sudo systemctl enable mongod
echo done!
```
## App Script
46. Step 46
Connect to the instance and run the App Deployment script

```
#!/bin/bash

echo updating...
sudo apt update -y
echo done!

echo upgrading packages...
sudo DEBIAN_FRONTEND=noninteractive apt upgrade -y
echo done!

echo installing nginx...
sudo DEBIAN_FRONTEND=noninteractive apt install nginx -y 
echo done!

# configure reverse proxy
sudo sed -i '51s/.*/\t        proxy_pass http:\/\/localhost:3000;/' /etc/nginx/sites-available/default
# changing a config file

echo restarting nginx...
sudo systemctl restart nginx
echo done!

echo enabling nginx...
sudo systemctl enable nginx
echo done!

echo install node js
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo DEBIAN_FRONTEND=noninteractive -E bash - && sudo DEBIAN_FRONTEND=noninteractive apt-get install -y nodejs
echo done!

echocheck js version
note -v
echo done!

echo set DB_HOST env var
export DB_HOST=mongodb://10.0.0.4:27017/posts
echo done!

echo install app folder
git clone https://github.com/Luix-Sparta/tech258-sparta-test-app.git
echo done!

echo cd app folder
cd ~/tech258-sparta-test-app/app
echo done!

echo install npm
sudo -E npm install
echo done!

echo install pm2
sudo -E npm install -g pm2
echo done!

echo stop app
pm2 stop app
echo done!

echo start app
pm2 start app.js app
echo done!

```

## Testing
47. Step 47
Go to the App Deployment Virtual Machine find the Public Ip

![alt text](../images/image-56.png)

48. Step 48
Enter IP Address/Posts into Web Browser
![alt text](../images/image-57.png)

# How to create a VM Image in Azure (Database And App)

1. **Prepare and Configure the Virtual Machine**:
   - Log in to the Azure portal at https://portal.azure.com.
   - Navigate to "Virtual machines" in the Azure portal.
   - Create a new virtual machine or select an existing one that you want to capture as an image.
   - Configure the VM with your desired operating system, size, networking, and any necessary software or configurations.

![img.png](../images/img_image.png)
   
2. **Generalize the Virtual Machine**:
   - Once the VM is configured, you need to generalize it. Generalizing removes any unique information from the VM.
   - SSH into the Instance
   - Type `waagent -deprovision+user` into the instance. 
   - This will prepare the VM for capturing an image by removes any unique information from the VM.

3. **Capture the VM Image**:
   - After generalizing the VM, you can capture it as a VM image.
   - Select your VM in the Azure portal, go to "Capture" under the "Operations" section, and provide the necessary details such as the image name, resource group, and region.
   - Click on "OK" to start the image capture process.
   
![img.png](../images/img_capture.png)

4. **Monitor Image Capture Progress**:
   - Azure will start capturing the VM as an image. You can monitor the progress in the notifications area of the Azure portal.

5. **Use the Image to Create New VMs**:
   - Once the image is successfully captured, it will be available in your Azure account.
   - You can use this image to create new virtual machines in Azure. Navigate to "Images" in the Azure portal, select your captured image, and click on "Create VM" to deploy new VMs based on this image.

# Differences between AWS and Azure: SSH Keys, Virtual Networks, and Virtual Machines

## SSH Keys

### AWS
- In AWS, when you create an EC2 instance, you can choose to use SSH key pairs for authentication.
- AWS uses the concept of key pairs, where you generate a key pair locally and then upload the public key to AWS. The private key remains on your local machine.
- You can associate a specific SSH key pair with an EC2 instance during the instance creation process or later by modifying the instance settings.

### Azure
- Similarly, in Azure, SSH key pairs can be used for authentication when accessing virtual machines.
- Azure also uses the concept of key pairs, but it handles the generation and management of SSH keys within the Azure portal.
- When creating a virtual machine in Azure, you can either use password authentication or SSH key authentication. If SSH key authentication is chosen, Azure generates the key pair and associates it with the virtual machine.

## Virtual Networks

### AWS
- In AWS, virtual networks are known as Virtual Private Clouds (VPCs).
- VPCs allow you to logically isolate your resources in the cloud, providing network-level control and security.
- You can create multiple VPCs in different AWS regions and connect them using VPC peering or VPN connections.

### Azure
- Azure's equivalent of VPCs is called Virtual Networks (VNets).
- VNets also offer network isolation and segmentation for Azure resources.
- Azure VNets support features like subnetting, Network Security Groups (NSGs), and Virtual Network Gateways for connecting on-premises networks to Azure VNets.

## Virtual Machines

### AWS
- AWS provides Elastic Compute Cloud (EC2) for virtual machine provisioning.
- EC2 instances come in various types and sizes, allowing you to choose resources based on your workload requirements.
- AWS offers a wide range of operating system choices for EC2 instances, including various Linux distributions and Windows Server.

### Azure
- Azure offers Virtual Machines (VMs) for running applications and workloads in the cloud.
- Similar to AWS, Azure VMs come in different sizes and configurations to meet varying performance and scalability needs.
- Azure VMs support both Windows and Linux operating systems, with a variety of pre-configured images available in the Azure Marketplace.

## More Differences 
- For Azure, A default rule for a security group, allow any internal traffic on your Virtual Network.
- Resources for the Virtual machine are split up into resources such as Disk and Ip Address.
- Public IP for an Azure Virtual Machine is set, however for AWS by default changes when the Instance restarts.

## User Data

**Deploying MongoDB using User Data in Azure**

When deploying your MongoDB database VM in Azure, you can streamline the installation and configuration process by utilizing User Data. User Data allows you to automate the setup steps, ensuring that MongoDB is installed and configured correctly without manual intervention. Below is a guide on how to implement User Data for your MongoDB deployment:

1. **Prepare Your User Data Script**: Before deploying your VM, prepare a Bash script containing the commands necessary to install and configure MongoDB. This script will be executed automatically upon VM creation.

2. **Access User Data Section**: During the VM creation process in Azure, you'll encounter a section for specifying User Data. In this section, you can paste your Bash script.

3. **Paste Your Database Script**: Copy the full database script, including any necessary commands to install and configure MongoDB, and paste it into the User Data section. Ensure that the script is properly formatted and does not contain any errors.

4. **Ensure Proper Execution**: Since User Data runs as the root user and only executes once immediately after VM creation, you don't need to include sudo in your script commands. However, make sure that your script accounts for the root user environment and starting file location (/).

5. **Verification**: After the VM is created, SSH into the instance to verify that the User Data script executed successfully. Check MongoDB's installation and configuration status to ensure that the database is properly set up.

6. **Troubleshooting**: If any issues arise during the deployment process, you can troubleshoot by examining the log files. Look for any errors or warnings that may indicate problems with the execution of your User Data script.

By utilizing User Data, you can automate the deployment of MongoDB in Azure, saving time and ensuring consistency across your environments. Remember to test your User Data script thoroughly before deploying it in a production environment.

Ways Of Checking The File:

- Use sudo -E 
- (Recurisvely) Change permissions using chmod 
- (Recursively) Take ownership of the folder. 
- Temporarily login as root 

**Deploying the App Using User Data in Azure**

Similar to deploying MongoDB, you can streamline the deployment of your application VM in Azure by leveraging User Data. Below are the steps to deploy your app using User Data:

1. **Prepare Your User Data Script**: Create a Bash script containing the commands necessary to set up your application, including dependencies, configurations, and starting the server.

2. **Access User Data Section**: During the VM creation process in Azure, locate the User Data section where you can input your script.

3. **Paste Your App Script**: Copy the full application script, including any commands to install dependencies, configure settings, and start the server, and paste it into the User Data section.

4. **Adjust Paths if Necessary**: Ensure that any file paths specified in your script are relative to the root directory (/), as User Data runs from this location.

5. **Verification**: Once the VM is created, SSH into the instance and verify that the User Data script executed successfully. Test your application to ensure that it is functioning correctly.

6. **Troubleshooting**: If you encounter any issues during deployment, refer to the log files for debugging information. Look for errors or warnings that may indicate problems with the execution of your User Data script.

Remember to check Image of Ubuntu

By using User Data, you can automate the deployment process for your application in Azure, simplifying the setup and ensuring consistency across your environments.

Remember to thoroughly test your User Data scripts before deploying them in a production environment to avoid any potential issues.

At the end of the day, remember to document your deployment process fully and share the documentation link in the main chat for reference. This documentation will serve as a valuable resource for future deployments and troubleshooting efforts.

## Images

**Using Images for Your 2-Tier App Deployment on Azure**

When deploying your 2-tier application on Azure, utilizing images can streamline the process and ensure consistency across your deployments. Here's how to incorporate images into your deployment workflow:

1. **Prepare Custom Images**: Create custom images for both the database and application tiers of your 2-tier application. These images should include all necessary dependencies, configurations, and software installations.

2. **Database Tier Image**: For the database tier, create an image that includes the configured MongoDB instance along with any additional settings required for your application's database layer.

3. **Application Tier Image**: Similarly, create an image for the application tier that includes your application code, web server configurations, and any other dependencies needed to run your application.

4. **Upload Images to Azure**: Once your custom images are prepared, upload them to Azure Image Gallery. This allows you to easily access and deploy these images when creating new virtual machines.

5. **Deploy Virtual Machines**: When creating virtual machines for your 2-tier application, select the custom images you've uploaded to Azure Image Gallery. This ensures that each VM is provisioned with the pre-configured environments defined in your images.

6. **Configuration and Testing**: After deploying your VMs using custom images, ensure that both the database and application tiers are functioning correctly. Test the connectivity between the tiers and verify that your application is operational.

7. **Scaling and Maintenance**: With images in place, scaling your application becomes more manageable. You can quickly spin up additional instances based on your custom images to handle increased demand. Additionally, maintaining consistency across deployments is simplified since each VM is provisioned from the same image.

8. **Additional Considerations**: 
    - **#!/bin/bash in User Data**: Make sure to include `#!/bin/bash` at the beginning of your User Data script to specify the shell interpreter.
    - **Verify All Details**: Double-check all deployment details including network configurations, security settings, and resource allocations to ensure they align with your requirements.
    - **Documentation**: Remember to document the image creation process, deployment steps, and any custom configurations applied to the VMs for future reference.

By using custom images for your 2-tier app deployment on Azure, you can automate and standardize the provisioning process, leading to improved efficiency and reliability.

Remember to update your custom images regularly to incorporate any changes or updates to your application stack. Documentation detailing the image creation process and deployment steps should also be maintained for reference.

1. **What is an Azure Image - what does it include, what is the equivalent called on AWS?**

   An Azure Image is a snapshot of a virtual machine (VM) that includes the operating system, installed software, configurations, and any data on the disk at the time the image was created. Essentially, it captures the entire state of a VM at a specific point in time, allowing you to replicate that VM's environment on other instances. The equivalent on AWS is called an Amazon Machine Image (AMI), which serves the same purpose of providing a template for launching EC2 instances.

2. **What is not included in the image and why?**

   While an Azure Image includes the operating system, installed software, configurations, and data, it typically does not include runtime data or user-specific settings. This exclusion ensures that the image remains generic and reusable across different environments and users. Additionally, any dynamic changes made to the VM after the image creation, such as runtime data or user-specific configurations, will not be captured in the image.

3. **What is the side-effect of creating an image of a VM on Azure? (After creating the image, can you log back into the VM used to create the image?)**

   When creating an image of a VM on Azure, the VM is deallocated during the image creation process. This means that it is stopped and temporarily taken offline to capture its state. After the image is created, the original VM is no longer available in its previous state. However, you can still access the VM's disk and data through the image. As for logging back into the VM used to create the image, it depends on the configuration and management settings. If the VM is configured to allow remote access, you can log back in after the image creation process is complete.

4. **The little bit of user data needed to get the app running with the posts page (and what can be commented out if you don't need the posts page to work)**

   To get the app running with the posts page using User Data in Azure, you typically need to include commands to set up the environment, install dependencies, and start the server. Specifically for the posts page functionality, you might need to ensure that the database connection is configured correctly. Here's a basic example of User Data script:

   ```bash
    #!/bin/bash

    echo set DB_HOST env var
    export DB_HOST=mongodb://10.0.3.5:27017/posts
    echo done!

    echo cd app folder
    cd ~/tech258-sparta-test-app/app
    echo done!

    echo install npm
    sudo -E npm install
    echo done!

    echo install pm2
    sudo -E npm install -g pm2
    echo done!

    pm2 delete app

    echo stop app
    pm2 stop app
    echo done!

    echo start app
    pm2 start app.js app
    echo done!
   ```

If the posts page functionality is not needed, you can comment out or remove the relevant sections from the User Data script. For example, you can exclude the installation and configuration of dependencies related to the posts page, as well as any specific setup steps or environment variables associated with it.

Speed to deploy- 

![img.png](../images/img_image_diagram.png)

## Monitoring, Alert Management and Auto Scaling


Monitoring, alert management, and auto-scaling are crucial components of cloud infrastructure management, ensuring optimal performance, timely responses to incidents, and efficient resource utilization.

Monitoring, alert management, and auto-scaling are crucial components of cloud infrastructure management, ensuring optimal performance, timely responses to incidents, and efficient resource utilization.

### Monitoring Solutions

- **Azure Monitor:** Azure's monitoring solution provides comprehensive insights into resource utilization, performance metrics, and application health, allowing users to monitor various aspects such as CPU load, memory usage, and network traffic.

- **AWS CloudWatch:** CloudWatch is Amazon Web Services' monitoring and observability service that collects and tracks metrics, logs, and events from AWS resources and applications. It offers detailed monitoring capabilities for monitoring CPU load, memory utilization, and other performance metrics.

### Alert Management

Monitoring solutions like Azure Monitor and AWS CloudWatch enable users to set up alarms based on predefined thresholds for metrics such as CPU load, memory usage, and response times. When an alarm is triggered, notifications are sent to designated recipients via email, SMS, or other communication channels.

### Auto Scaling

Auto scaling is a key feature for dynamically adjusting the capacity of cloud resources based on demand. Both Azure and AWS offer auto-scaling capabilities:

- **Azure Virtual Machine Scale Sets:** Azure VM Scale Sets automatically scale the number of VM instances based on demand or a defined schedule, ensuring optimal performance and resource utilization.

- **AWS Auto Scaling Group:** AWS Auto Scaling automatically adjusts the number of EC2 instances or other AWS resources in response to changes in demand or metrics, helping maintain application availability and performance.

![img.png](../images/img_monitoring_diagram.png)

Scaling up - Current virtual machine replaced by larger machine and work is shifted over, small one is removed.
Scaling down - Current virtual machine replaced by smaller machine and work is shifted over, larger one is removed.

Auto Scaling - Increase number of virtual machines

### Creating A Dashboard

1. Go to App Instance - Overview

![img.png](../images/monitoring_images/img.png)

2. Click Monitoring

![img_1.png](../images/monitoring_images/img_1.png)

3. Cpu (average)

![img_2.png](../images/monitoring_images/img_2.png)

4. To see more charts

![img_3.png](../images/monitoring_images/img_3.png)

5. Click the Pin 

![img_4.png](../images/monitoring_images/img_4.png)

6. Click Create new and fill in the details.

![img_5.png](../images/monitoring_images/img_5.png)

7. To Pin Exisitng click Pin

![img_6.png](../images/monitoring_images/img_6.png)

8. Choose Dashboard and click Create

![img_7.png](../images/monitoring_images/img_7.png)

9. Write Dashboard and click on that

![img_8.png](../images/monitoring_images/img_8.png)

10. Click on Dashboard

![img_9.png](../images/monitoring_images/img_9.png)

11. Click Edit

![img_11.png](../images/monitoring_images/img_11.png)

12. Edit and save

![img_10.png](../images/monitoring_images/img_10.png)

13. Click on Chart

![img_12.png](../images/monitoring_images/img_12.png)

14. Change Time

![img_13.png](../images/monitoring_images/img_13.png)

15. Click Save to Dashboard

![img_18.png](../images/monitoring_images/img_18.png)

### Performance Testing

Performance testing with tools like Apache Bench (ab) helps simulate load conditions on the application to assess its scalability and performance under various scenarios.

**Command:**
```ab -n 1000 -c 100 http://20.90.163.28/.com/```

**Result:**
```
adminuser@tech258-l-app-for-monitoring-vm:~$ ab -n 1000 -c 100 http://20.90.163.28/.com/
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 20.90.163.28 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests


Server Software:        nginx/1.18.0
Server Hostname:        20.90.163.28
Server Port:            80

Document Path:          /.com/
Document Length:        144 bytes

Concurrency Level:      100
Time taken for tests:   1.712 seconds
Complete requests:      1000
Failed requests:        0
Non-2xx responses:      1000
Total transferred:      419000 bytes
HTML transferred:       144000 bytes
Requests per second:    584.26 [#/sec] (mean)
Time per request:       171.158 [ms] (mean)
Time per request:       1.712 [ms] (mean, across all concurrent requests)
Transfer rate:          239.07 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    2   2.8      1      22
Processing:     7  162  31.7    164     227
Waiting:        3  162  31.7    164     227
Total:          8  164  30.7    165     228

Percentage of the requests served within a certain time (ms)
  50%    165
  66%    172
  75%    176
  80%    180
  90%    193
  95%    209
  98%    223
  99%    225
 100%    228 (longest request)
```

```ab -n 10000 -c 200 http://20.90.163.28/.com/```

This command above and checking the dashboard helps us to check the CPU usage.

![img_20.png](../images/monitoring_images/img_20.png)

![img_21.png](../images/monitoring_images/img_21.png)

This image shows the spike due to testing the CPU.

### How To Create An Alert

1. Navigate to your instance

2. Scroll down and click Monitoring

3. Click "Set up recommendations"

![img_23.png](../images/monitoring_images/img_23.png)

4. Change 80% to 3%

![img_24.png](../images/monitoring_images/img_24.png)

5. Also enable the toggle on the right

![img_25.png](../images/monitoring_images/img_25.png)

6. Scroll down and make sure the "Notify me by" section, has Email enabled with an email inside the field.

![img_26.png](../images/monitoring_images/img_26.png)

7. Click Save

![img_27.png](../images/monitoring_images/img_27.png)

8. Click on the three dots and click "Alert rules"

![img_28.png](../images/monitoring_images/img_28.png)

9. The alert should be shown here

![img_29.png](../images/monitoring_images/img_29.png)

10. Open it, click Edit and click conditions

![img_30.png](../images/monitoring_images/img_30.png)

11. Change it to every 1 Minute and save

### Testing Alert

1. Run this command `ab -n 10000 -c 200 http://20.90.163.28/.com/`

![img_31.png](../images/monitoring_images/img_31.png)

2. Check Email

![img_32.png](../images/monitoring_images/img_32.png)

![img_33.png](../images/monitoring_images/img_33.png)

## Autoscaling Groups

### Understanding Virtual Machine Scale Sets (VMSS)

- **VMSS Purpose**: It's like a team of identical virtual machines that automatically grows or shrinks based on how busy the application is. This keeps things running smoothly even when lots of people are using the app.

- **Custom Images**: These are like blueprints for the virtual machines, containing all the stuff needed for the app to work properly.

- **Auto Scaling**: Imagine adding more chairs to a restaurant when it gets busy and removing them when it's quiet. VMSS does that automatically for virtual machines based on how much work they're doing.

- **Minimum and Maximum Instances**: You can set rules for how many virtual machines are always running (minimum) and how many can be created at most (maximum). This ensures there's always enough but not too many.

- **Availability Zones**: It's like having your team spread out across different locations in case something goes wrong in one place. This helps keep the app running even if there's a problem in one area.

- **Load Balancer**: Think of it like a traffic officer directing cars to different lanes. The load balancer sends users to different virtual machines so no one gets overwhelmed with too much work.

Extra Info: Default is now called initial

![img_1.png](../images/autoscale_group/img_1.png)

**High Availability:**

In a 2-tier architecture, the frontend tier typically handles client requests and the backend tier processes data. By distributing both tiers across multiple VM instances within a VMSS, high availability is achieved.
If one frontend or backend VM instance fails, the load balancer redirects traffic to other healthy instances, ensuring continuous availability of the application.
Additionally, by utilizing Availability Zones, VM instances from both tiers can be spread across different data centers within the same region, enhancing fault tolerance and ensuring that the application remains accessible even in the event of a data center failure.

**Scalability:**

VMSS facilitates scalability by automatically adjusting the number of VM instances in both the frontend and backend tiers based on workload demand.
As the number of client requests increases, VMSS dynamically adds more frontend instances to handle the increased traffic, ensuring responsiveness and optimal user experience.
Similarly, as the backend tier processes data from frontend requests, VMSS scales out the number of backend instances to distribute the processing load efficiently, maintaining the application's performance under heavy workloads.
Scaling policies, such as those based on CPU utilization or incoming request rate, can be configured to automate the scaling process, allowing the architecture to adapt seamlessly to changing workload conditions while optimizing resource utilization and cost efficiency.

## Steps to Create an Autoscale group

Extra Info: Load Balancer has an IP address

1. Write "Virtual Machine Scale Set" in Azure and navigate to it
![img_2.png](../images/autoscale_group/img_2.png)

2. Click "Create"
![img_3.png](../images/autoscale_group/img_3.png)

3. Set the Subscription and Resource Group
![img_4.png](../images/autoscale_group/img_4.png)

4. Set the name
![img_5.png](../images/autoscale_group/img_5.png)

5. Set the Availability zones
By setting Availability Zones for a Scale Set, you're essentially spreading the VM instances across different data centers within the same region. This provides redundancy and fault tolerance, as it ensures that if one data center or Availability Zone experiences an outage, the VM instances in other zones can continue to operate without disruption.
![img_6.png](../images/autoscale_group/img_6.png)

6. Change the Orchestration mode to "Uniform"
![img_7.png](../images/autoscale_group/img_7.png)

7. Change the Scaling mode to Autoscaling
![img_8.png](../images/autoscale_group/img_8.png)

8. Click "Configure" on Scaling configuration
![img_9.png](../images/autoscale_group/img_9.png)

9. Click "Default condition"
![img_10.png](../images/autoscale_group/img_10.png)

10. Change Maximum to 3 and Scale out CPU threshold to 75 and click save
In the Autoscaling configuration, changing the maximum number of instances to 3 and setting the CPU threshold for scaling out to 75% means that the system will automatically add VM instances up to a maximum of 3 when CPU usage hits 75% or more. This helps manage resource allocation efficiently without overspending on unnecessary VMs.
![img_11.png](../images/autoscale_group/img_11.png)

11. Click "Save"
![img_12.png](../images/autoscale_group/img_12.png)

12. Select Image
![img_13.png](../images/autoscale_group/img_13.png)

13. Change Administrator account details
![img_14.png](../images/autoscale_group/img_14.png)

14. Select Other for License type
![img_15.png](../images/autoscale_group/img_15.png)

15. Click Disks
![img_16.png](../images/autoscale_group/img_16.png)

16. Change OS disk type to Standard SSD
![img_17.png](../images/autoscale_group/img_17.png)

17. Click Networking
![img_18.png](../images/autoscale_group/img_18.png)

18. Choose Virtual network
![img_19.png](../images/autoscale_group/img_19.png)

19. Click on the edit button
![img_20.png](../images/autoscale_group/img_20.png)

20. Select the Network Interface Settings and click ok
![img_21.png](../images/autoscale_group/img_21.png)

21. Click Create a load balancer
Clicking "Create a load balancer" sets up a load balancer for the 2-tier architecture scale set in Azure. This load balancer helps evenly distribute incoming traffic across multiple instances of the frontend tier, ensuring high availability and scalability for your application.
![img_23.png](../images/autoscale_group/img_23.png)

22. Input a name and click Create
![img_22.png](../images/autoscale_group/img_22.png)

23. Go to Health
![img_24.png](../images/autoscale_group/img_24.png)

24. Enable application health monitoring
**Application Health Monitoring:** Azure provides tools to monitor the health of applications running on VMSS instances. This includes checking for application failures, responsiveness, and overall performance.
![img_25.png](../images/autoscale_group/img_25.png)

25. Enable Automatic Repairs
**Automatic Repairs:** Azure allows automatic repairs to be configured for VMSS instances. If an instance becomes unhealthy due to application failures or other issues, Azure can automatically initiate repair actions to restore the instance's health.
![img_26.png](../images/autoscale_group/img_26.png)

26. Go to Advanced
![img_27.png](../images/autoscale_group/img_27.png)

27. Enable and input user data

**Code:** 
```
#!/bin/bash

echo cd app folder
cd /tech258-sparta-test-app/app
echo done!

echo start app
pm2 start app.js app
echo done!
```
![img_28.png](../images/autoscale_group/img_28.png)

28. Go to Tags
![img_29.png](../images/autoscale_group/img_29.png)

29. Input Tags
Create a Tag to track resources
![img_30.png](../images/autoscale_group/img_30.png)

30. Go to Review + Create and review 
![img_31.png](../images/autoscale_group/img_31.png)

31. Click Create
![img_32.png](../images/autoscale_group/img_32.png)

32. Navigate to the Scale group and check if its running
![img_33.png](../images/autoscale_group/img_33.png)

## Reimage And Upgrade (If the Userdata is modified)

Re-Image - Replace the OS disk with a fresh image from when you originally set up the scale set

Upgrade - Start it from the newest version, e.g the Modified Userdata will be used

## Scale Set Instance Health

* **Continuous Monitoring:** Azure constantly monitors the health of each virtual machine instance within the Virtual Machine Scale Set (VMSS).
* **Health Probes:** Azure Load Balancer regularly sends health probes to each VM instance to check its responsiveness and availability.
* **Automatic Remediation:** If an instance is found to be unhealthy due to issues like resource exhaustion or software failures, Azure may automatically take corrective actions, such as restarting the instance or provisioning a replacement.
* **Load Balancing:** Healthy instances receive incoming traffic from the load balancer, ensuring even distribution of requests. Unhealthy instances are temporarily removed from the pool to prevent them from serving requests until they recover.
* **Integration with Monitoring Tools:** Scale Set Instance Health data can be integrated with Azure monitoring tools like Azure Monitor, allowing administrators to track the health status of instances, set up alerts, and perform analysis for proactive management and troubleshooting.
* **Fault Tolerance:** Monitoring and managing the health of VM instances within the scale set contribute to the overall fault tolerance of the application. By quickly identifying and addressing issues with individual instances, the system maintains high availability and reliability.

## How to SSH into Scale Set Instance

1. **Use the Load Balancer IP and Port 50000**: The first step is to identify the Load Balancer IP address associated with the scale set. Since the load balancer distributes traffic to multiple instances, you can use its IP address along with the designated port number (in this case, port 50000) to access the first instance in the scale set.

2. **SSH Command**: Once you have the Load Balancer IP and port number, you can use the SSH command in your terminal to establish a secure shell connection to the instance. The command syntax typically includes the path to your SSH key (`-i ~/.ssh/tech258-luixhiano-az-key`), the port number (`-p 50000`), and the username and IP address of the instance (`adminuser@4.158.68.162`).

    ```
    ssh -i ~/.ssh/tech258-luixhiano-az-key -p 50000 adminuser@4.158.68.162
    ```

3. **Changing Port for Different Instances**: If you need to access a different instance within the scale set, you can modify the port number in the SSH command accordingly. Each instance in the scale set typically listens on a different port, allowing you to SSH into specific instances based on your requirements.

    For example, to SSH into the second instance, you might use port 50001:

    ```
    ssh -i ~/.ssh/tech258-luixhiano-az-key -p 50001 adminuser@4.158.68.162
    ```

    Similarly, for the third instance, you would use port 50002, and so on.

SSH access via the Load Balancer IP and designated ports provides a convenient way to manage and troubleshoot individual instances within a scale set, allowing you to SSH into specific instances as needed.

## How to Delete Scale Set
 
1. Delete Scale Set
    1. Navigate to the Azure Portal and sign in.
    2. Find the Scale Set resource group.
    3. Click on the Scale Set to open its overview page.
    4. Access Scale Set Settings.
    5. Click on "Delete".
    6. Confirm the deletion.
![img_34.png](../images/autoscale_group/img_34.png)

2. Delete Load Balancer
    1. Navigate to the Load Balancer resource.
    2. Click on the Load Balancer to open its overview page.
    3. Access Load Balancer Settings.
    4. Click on "Delete".
    5. Confirm the deletion.
![img_35.png](../images/autoscale_group/img_35.png)

3. Delete Load Balancer Ip
    1. Navigate to the Load Balancer IP resource.
    2. Click on the Load Balancer IP to open its overview page.
    3. Access Load Balancer IP Settings.
    4. Click on "Delete".
    5. Confirm the deletion.
![img_36.png](../images/autoscale_group/img_36.png)


## Making the Database Private (Azure)

Diagram Keys -
Safe Traffic - Green
Unsafe Traffic - Red
Steps - Blue

![network(v2).drawio.png](/images/network(v2).drawio.png)

Virtual Network (VNet): The VNet has an address range of 10.0.0.0/16. This means it can accommodate up to 65,536 IP addresses.

Subnets:
- **Public Subnet (10.0.2.0/24)**: This subnet is designated for publicly accessible components. It contains:
  - **Network Security Group (NSG)**: Defines rules for inbound and outbound traffic. It allows SSH (port 22) and HTTP (port 80) traffic.
  - **Network Interface Card (NIC)**: Connects the virtual machine (App VM) to the network.
  - **Application Virtual Machine (App VM)**: Hosts the application accessible from the internet.
  - **Public IP Address**: Provides an external IP address for users to access the application hosted on the App VM.

- **DMZ Subnet**: This subnet is designated as a demilitarized zone (DMZ) for hosting intermediary network devices.
  - **NSG**: Allows SSH traffic.
  - **NIC**: Requires IP forwarding enabled. This allows the VM to forward traffic to other destinations.
  - **Network Virtual Appliance (NVA) Virtual Machine**: Acts as a router or firewall between the DMZ and private subnet. It also requires IP forwarding enabled and may have additional iptables rules configured for traffic filtering.

- **Private Subnet**: This subnet is for hosting sensitive components that should not be directly accessible from the internet.
  - **NSG**: Allows SSH and MongoDB (port 27017) traffic while denying all other traffic.
  - **NIC**: Connects the virtual machine (DB VM) to the network.
  - **Database Virtual Machine (DB VM)**: Hosts the database and is not directly accessible from the internet.

Route Table: There's a route table named "to-private-subnet" that directs traffic from the public subnet to the private subnet through the DMZ subnet, possibly via the NVA VM.

Color Coding (Blue):
These represent the major steps or tasks in setting up the architecture:
- Setting up the NVA in the DMZ subnet.
- Applying stricter rules on the private subnet.
- Possibly deleting the public IP for the private subnet to enhance security.

Public IPs: Both the DMZ and private subnets have public IP addresses, likely for specific purposes such as management or external access to certain services.

The described 2-tier architecture on Azure with an App and a Database offers the following benefits:
- Scalability: Allows independent scaling of the application and database tiers based on their specific requirements.
- Security: Utilizes network security groups (NSGs) to control traffic flow and restrict direct access to the database from the internet, enhancing data security.
- Resilience: Leverages Azure's availability sets and fault domains for high availability and fault tolerance, minimizing downtime risks.
- Efficient Network Routing: Optimizes traffic routing between subnets for improved network performance and latency reduction.
- Ease of Management: Simplifies network configuration and management tasks using Azure's built-in networking features, with monitoring and logging capabilities for proactive management and troubleshooting.

## How to create the 3-subnet architecture (Private Database)
 


1. Navigate to Virtual Network

![img.png](../images/3_tier_subnet/img.png)

2. Click Create

![img_1.png](../images/3_tier_subnet/img_1.png)

3. Select Subscription and Resource Group

![img_2.png](../images/3_tier_subnet/img_2.png)

4. Change the Virtual network name and Region

![img_3.png](../images/3_tier_subnet/img_3.png)

5. Click default in Ip addresses

![img_4.png](../images/3_tier_subnet/img_4.png)

6. Change Name to "public-subnet" and Starting address to "10.0.2.0" and click Add
   (This is the creation of the public-subnet that would hold the app)
![img_5.png](../images/3_tier_subnet/img_5.png)

7. Then Click Add a Subnet and input "dmz-subnet" for the name and "10.0.3.0" for the Starting address and click Add
   (This is the creation of the dmz-subnet that would hold the NVA, which acts as a firewall)
![img_6.png](../images/3_tier_subnet/img_6.png)

8. Then Click Add A Subnet and input "private-subnet" for the name and "10.0.4.0" for the Starting address
   (This is the creation of the public-subnet that would hold the app)

![img_7.png](../images/3_tier_subnet/img_7.png)

9. Scroll down and Enable private subnet (no default outbound access) and click Add

![img_8.png](../images/3_tier_subnet/img_8.png)

10. Click Review + Create, Check it and then click Create

![img_9.png](../images/3_tier_subnet/img_9.png)

11. Create a DB instance from the DB image

12. Make Sure to change Availability Zone to "Zone 3"

![img_10.png](../images/3_tier_subnet/img_10.png)

13. Change these settings in Networking
- Virtual network 
- Subnet
- Change Public IP to NONE

![img_11.png](../images/3_tier_subnet/img_11.png)

14. Check Review + create and click Create

![img_13.png](../images/3_tier_subnet/img_13.png)

15. Create an App Instance from the App image

16. Make Sure to change Availability Zone to "Zone 1"

![img_16.png](../images/3_tier_subnet/img_16.png)

17. Change these settings in Networking
- Virtual network
- Subnet
- Public IP
- Add HTTP(80) for Networking and Basics

![img_17.png](../images/3_tier_subnet/img_17.png)

18. Go to Advanced, Enable and Input the User Data, use the Private IP for the database

User Data:
```
#!/bin/bash

echo cd app folder
cd /tech258-sparta-test-app/app
echo done!

echo set DB_HOST env var
export DB_HOST=mongodb://10.0.4.4:27017/posts
echo done!

npm install

pm2 kill

echo start app
pm2 start app.js app
echo done!
```

![img_18.png](../images/3_tier_subnet/img_18.png)

19. Check Review + Create and then click Create

![img_19.png](../images/3_tier_subnet/img_19.png)

20. Input the App Public IP Address/posts to check if its working at this point

![img_20.png](../images/3_tier_subnet/img_20.png)

21. Create a New Virutal Machine

21. Input the Name, Change the Availability Zone and select Ubuntu 22.04 for the image.

![img_21.png](../images/3_tier_subnet/img_21.png)

22. Change these settings in Networking
- Virtual network
- Subnet
- Public IP

![img_22.png](../images/3_tier_subnet/img_22.png)

23. Check Review + Create and then click Create

![img_23.png](../images/3_tier_subnet/img_23.png)

24. Navigate to Route tables
The Route table instructs traffic from the public subnet to pass through the NVA before reaching the private subnet.
![img_24.png](../images/3_tier_subnet/img_24.png)

25. Click Create
![img_25.png](../images/3_tier_subnet/img_25.png)

26. Input these settings

![img_26.png](../images/3_tier_subnet/img_26.png)

27. Input Tags

![img_27.png](../images/3_tier_subnet/img_27.png)

28. Check Review + Create and then click Create

![img_28.png](../images/3_tier_subnet/img_28.png)

29. Click on Resource, Then Settings, Then Routes

![img_29.png](../images/3_tier_subnet/img_29.png)

30. Click Add

![img_30.png](../images/3_tier_subnet/img_30.png)

31. Input
- Route name
- Destination type - IP Addresses
- Destination IP Addresses/CIDR ranges (10.0.4.0/24)
- Next hop type (Virtual appliance)
- Next hop address (10.0.3.4)

Then Click Add

![img_31.png](../images/3_tier_subnet/img_31.png)

32. Navigate to subnets and clcik Associate

![img_32.png](../images/3_tier_subnet/img_32.png)

33. Choose the Virtual network and Subnet and click Ok

![img_33.png](../images/3_tier_subnet/img_33.png)

34. Navigate to the NVA instance, Click Network Settings and select the Network Interface

![img_34.png](../images/3_tier_subnet/img_34.png)

35. Select Enable IP forwarding and click Apply

![img_35.png](../images/3_tier_subnet/img_35.png)

36. Navigate to the NVA VM and connect via SSH

![img_36.png](../images/3_tier_subnet/img_36.png)

![img_37.png](../images/3_tier_subnet/img_37.png)

37. Now Run these 2 commands (This should be the first commands you run)
- `sudo apt update -y`
- `sudo apt upgrade -y`

38. Use this `sysctl net.ipv4.ip_forward` command to check if its forwarding
- 0 for No
- 1 for Yes

![img_38.png](../images/3_tier_subnet/img_38.png)

39. Use this command `sudo nano /etc/sysctl.conf` and remove # from the forwarding line

![img_39.png](../images/3_tier_subnet/img_39.png)

40. Save the document

50. Run `sudo sysctl -p` to restart 

![img_40.png](../images/3_tier_subnet/img_40.png)

51. Next create a script using `nano ip_forward.sh`

52. Inside the Script use this code

**ip_forward.sh:**
```
#!/bin/bash

# configure iptables

echo "Configuring iptables..."

# Allow loopback traffic
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT

# Allow established and related incoming connections
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Allow established outgoing connections
sudo iptables -A OUTPUT -m state --state ESTABLISHED -j ACCEPT

# Drop invalid incoming packets
sudo iptables -A INPUT -m state --state INVALID -j DROP

# Allow SSH incoming and outgoing connections
sudo iptables -A INPUT -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
sudo iptables -A OUTPUT -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT

# Allow SSH into NVA only through the public subnet (app VM as a jumpbox)
# Must be done once the NVA's public IP address is removed
#sudo iptables -A INPUT -p tcp -s 10.0.2.0/24 --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
#sudo iptables -A OUTPUT -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT

# Allow SSH to other servers using the NVA as a jumpbox
# If need to make outgoing SSH connections with other servers from NVA
#sudo iptables -A OUTPUT -p tcp --dport 22 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
#sudo iptables -A INPUT -p tcp --sport 22 -m conntrack --ctstate ESTABLISHED -j ACCEPT

# Allow MongoDB traffic from public subnet to private subnet
sudo iptables -A FORWARD -p tcp -s 10.0.2.0/24 -d 10.0.4.0/24 --destination-port 27017 -m tcp -j ACCEPT

# Allow ICMP traffic from public subnet to private subnet
sudo iptables -A FORWARD -p icmp -s 10.0.2.0/24 -d 10.0.4.0/24 -m state --state NEW,ESTABLISHED -j ACCEPT

# Drop all other incoming and forwarding traffic by default
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP

echo "Done!"
echo ""

# make iptables rules persistent
# it will ask for user input by default

echo "Make iptables rules persistent..."
sudo DEBIAN_FRONTEND=noninteractive apt install iptables-persistent -y
echo "Done!"
echo ""

```

![img_42.png](../images/3_tier_subnet/img_42.png)

53. Add Execute permissions to the File and Run it
- `sudo chmod +x ip_forward.sh`
- `./ip_forward.sh`

![img_43.png](../images/3_tier_subnet/img_43.png)

54. Check if its running

![img_44.png](../images/3_tier_subnet/img_44.png)

### Update NSG rules to make the Private Subnet Stricter

![img_45.png](../images/3_tier_subnet/img_45.png)










