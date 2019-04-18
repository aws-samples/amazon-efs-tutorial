![](https://s3.amazonaws.com/aws-us-east-1/tutorial/AWS_logo_PMS_300x180.png)

![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_available.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_ingergration.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_ecryption-lock.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_fully-managed.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_lowcost-affordable.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_performance.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_scalable.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_storage.png)
# **Amazon Elastic File System (Amazon EFS)**

## Performance Tutorial

### Version 1.1.4

efs-pt-1.1.4

---

© 2018 Amazon Web © 2018 Amazon Web Services, Inc. and its affiliates. All rights reserved.
This sample code is made available under the MIT-0 license. See the LICENSE file.

Errors or corrections? Email us at [darrylo@amazon.com](mailto:darrylo@amazon.com).

---

## Tutorial Overview

### Overview

This tutorial is designed to help you better understand the performance characteristics of Amazon Elastic File System (Amazon EFS) and how parallelism, I/O size, and Amazon EC2 instance types have a profound effect on file system performance.

This tutorial is divided into four sections.

- **Section 1 -** will demonstrate that not all Amazon EC2 instance types are created equal and different instance types provide different levels of network performance when accessing an EFS file system.

- **Section 2 -** will demonstrate how different I/O sizes (block sizes) and sync() frequencies (the rate data is persisted to disk) have a profound impact on EFS performance when compared to EBS.

- **Section 3 -** will demonstrate how increasing the number of threads accessing EFS will significantly improve performance when compared to EBS.

- **Section 4 -** will compare and demonstrate how different file transfer tools affect performance when accessing an EFS file system.


### Prerequisites

The AWS CloudFormation template below will create the compute environment you need to run the tutorial. You must have an existing Amazon EFS file system in the region where you launch the CloudFormation stack and it must have mount targets in the VPC where you launch your EC2 instances. You will need to provide the EFS file system id as a parameter value when you launch the CloudFormation stack. If you don't already have a file system you can use for this tutorial, click the link to go to the [***Create a file system***](/create-file-system) tutorial.

### The Environment

The AWS CloudFormation template will launch three EC2 instances, each in their own Auto Scaling group. Please use the recommended default instance types for each Auto Scaling group. The file system, whose id you entered as a CloudFormation parameter, will be automatically mounted to each EC2 instance. These instances will also have a 20 GB gp2 EBS data volume mounted and 5GB of test data will be generated on that volume. The following open-source applications will also be installed on each instance.

- **nload -** is a console application that monitors network traffic and bandwidth usage in real time

- **smallfile -** [https://github.com/bengland2/smallfile](https://github.com/bengland2/smallfile) - used to generate test data; Developer: Ben England

- **GNU Parallel -** [https://www.gnu.org/software/parallel/](https://www.gnu.org/software/parallel/) - used to parallelize single-threaded commands; O. Tange (2011): GNU Parallel - The Command-Line Power Tool, ;login: The USENIX Magazine, February 2011:42-47

- **fpart -** [https://github.com/martymac/fpart](https://github.com/martymac/fpart) - sorts file trees and packs them into partitions; Author Ganaël Laplanche

- **fpsync -** wraps fpart + rsync together as a multi-threaded transfer utility - included in the tools/ directory of fpart


NOTICE!! Amazon Web Services does NOT endorse specific 3rd party applications. These software packages are used for demonstration purposes only.  Follow all expressed or implied license agreements associated with these 3rd party software products.

WARNING!! This tutorial environment will exceed your free-usage tier. You will incur charges as a result of launching this CloudFormation stack and executing the scripts included in this tutorial. This tutorial will take approximately 1 hour to complete and at a cost of ~$0.83. Delete all files on the EFS file system that were created during this tutorial and delete the CloudFormation stack so you don’t continue to incur additional compute and storage charges.

### Launch the AWS CloudFormation Stack

Click the  ![cloudformation-launch-stack](/images/deploy_to_aws.png) link below to create the AWS CloudFormation stack in your account and desired AWS region. This region must an existing Amazon EFS file system which you will use with this tutorial. It creates three separate autoscaling groups and launches one instance per group. You can select different attributes per instance, including the instance type, Linux distribution (Amazon Linux, Amazon Linux 2), and whether the Amazon EFS file system automatically mounts over TLS (enabling encryption of data in transit). Select the instance type and Linux distribution you want to use to complete the tutorial.

Amazon Elastic File System (EFS) now allows you to encrypt data in transit when using an EFS file system.  Previously, there was no way to encrypt network communications between an EFS file system and its clients. EFS uses industry-standard Transport Layer Security (TLS) 1.2 to encrypt network communications. To simplify the experience of encrypting data in transit, AWS has published an EFS-specific mount helper that manages tunneling your EFS traffic over a TLS network tunnel. 

The EFS mount helper, amazon-efs-utils, is available in the Amazon repository, so if you’re running Amazon Linux or Amazon Linux 2 you install it using a simple sudo yum install amazon-efs-utils command. We have opened sourced the EFS mount helper and its available in the AWS repository on github. So for other Linux distributions, you can download, build, and install the package yourself. We’ve verified the package against the latest version of Amazon Linux, Amazon Linux 2, CentOS7, RHEL7, Debian 9, and Ubuntu 16.04. We’re working to add efs-utils to other Linux repositories.



![](/images/efs_tutorial_parameters_screenshot_01.png)



If you choose not to automatically mount the file system as a part of the Cloudformation template, you must run the commands below to mount the file system and build the directory structure to run the tutorial.

```sh
instance_id=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
efs_mount_point=/mnt/efs/01
sudo mount -t efs ***file-system-id*** ${efs_mount_point}
sudo mkdir -p ${efs_mount_point}/tutorial/touch/${instance_id}
sudo mkdir -p ${efs_mount_point}/tutorial/dd/${instance_id}
sudo mkdir -p ${efs_mount_point}/tutorial/cp/${instance_id}
sudo mkdir -p ${efs_mount_point}/tutorial/rsync/${instance_id}
sudo mkdir -p ${efs_mount_point}/tutorial/parallelcp/${instance_id}
sudo mkdir -p ${efs_mount_point}/tutorial/fpsync/${instance_id}
sudo mkdir -p ${efs_mount_point}/tutorial/parallelcpio/${instance_id}
sudo chown ec2-user:ec2-user ${efs_mount_point}/tutorial/ -R
```

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=efs-performance-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/performance/templates/efs_performance_tutorial_1.1.4.yaml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=efs-performance-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/performance/templates/efs_performance_tutorial_1.1.4.yaml) |
| us-west-1 |US West (N. California)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/new?stackName=efs-performance-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/performance/templates/efs_performance_tutorial_1.1.4.yaml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=efs-performance-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/performance/templates/efs_performance_tutorial_1.1.4.yaml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=efs-performance-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/performance/templates/efs_performance_tutorial_1.1.4.yaml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=efs-performance-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/performance/templates/efs_performance_tutorial_1.1.4.yaml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=efs-performance-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/performance/templates/efs_performance_tutorial_1.1.4.yaml) |
| ap-northeast-1 |AP (Tokyo)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/new?stackName=efs-performance-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/performance/templates/efs_performance_tutorial_1.1.4.yaml) |
| ap-northeast-2 |AP (Seoul)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-northeast-2#/stacks/new?stackName=efs-performance-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/performance/templates/efs_performance_tutorial_1.1.4.yaml) |

After launching the AWS CloudFormation Stack above, you should see three Amazon EC2 instances running in your VPC.  Each instance **Name** tag will change from "EFS Performance Tutorial - Launching..." to "EFS Performance Tutorial - Ready". Wait for the **Name** tag of each instance to read "EFS Performance Tutorial - Ready" before continuing.

## Section 1
### Demonstrate different methods to evaluate IOPS performance and generate 1024 files
 ___
This section will demonstrate the best methods to generate lots of small files.

### 1.1.  Add SSH inbound access for your IP address

- Sign in to the [AWS Management Console](https://console.aws.amazon.com/console/home) and navigate to the EC2 service page
- Select **Security Groups** on the left frame under **NETWORK & SECURITY** and select the default security group of your VPC.
- Add an inbound rule to allow SSH access to this security group from your IP address (e.g. selecting **My IP** as the Source).

### 1.2.  SSH into the c5.2xlarge EC2 instance
### 1.3.  Use 'touch' to generate zero-byte files using a single thread
Run this command against the c5.2xlarge instance to generate 1,024 zero-byte files.
```sh

directory=$(echo $(uuidgen)| grep -o ".\{6\}$")
mkdir -p /mnt/efs/01/tutorial/touch/${directory}

time for i in {1..1024}; do
  touch /mnt/efs/01/tutorial/touch/${directory}/test-1.3-$i;
  done;
```
Record run time.
### 1.4.  Use 'touch' to generate zero-byte files using multiple threads
Run this command against the c5.2xlarge instance to generate 1,024 zero-byte files using multiple threads.
```sh

directory=$(echo $(uuidgen)| grep -o ".\{6\}$")
mkdir -p /mnt/efs/01/tutorial/touch/${directory}

time seq 1 1024 | parallel --will-cite -j 128 touch /mnt/efs/01/tutorial/touch/${directory}/test-1.4-{}
```
Record run time.
### 1.5.  Use 'touch' to generate zero-byte files in multiple directories using multiple threads
Run this command against the c5.2xlarge instance to generate 1,024 zero-byte files using multiple threads 
```sh

directory=$(echo $(uuidgen)| grep -o ".\{6\}$")
mkdir -p /mnt/efs/01/tutorial/touch/${directory}/{1..32}

time seq 1 32 | parallel --will-cite -j 32 touch /mnt/efs/01/tutorial/touch/${directory}/{}/test1.5{1..32}
```
Record run time.
### 1.6.  Close all SSH sessions
#
#
### Results
To best way to leverage the distributed data storage design of Amazon EFS is to use multiple threads and inodes in parallel.

| Step | EC2 Instance Type | File Count | Duration | Files/s |
| :--- | :--- | --- | --- | --- |
| 1.3. | c5.2xlarge | 1,024 | 16.219 sec. | 63 |
| 1.4. | c5.2xlarge | 1,024 | 8.061 sec. | 127 |
| 1.5. | c5.2xlarge | 1,024 | 0.901 sec. | 1136 |

![](/images/efs_performance_tutorial_touch_results.png)

## Section 2
### Compare the network performance of different EC2 instance types accessing EFS
 ___
This section will demonstrate that not all Amazon EC2 instance types are created equal and different instance types provide different levels of network performance when accessing EFS.


### 2.1.  Add SSH inbound access for your IP address

- Sign in to the [AWS Management Console](https://console.aws.amazon.com/console/home) and navigate to the EC2 service page
- Select **Security Groups** on the left frame under **NETWORK & SECURITY** and select the default security group of your VPC.
- Add an inbound rule to allow SSH access to this security group from your IP address (e.g. selecting **My IP** as the Source).

### 2.2.  SSH into one Amazon EC2 instance

- Start with the t2.micro instance
- Run the command in 2.3. below and wait for it to complete.
- What happened after ~15GB was written?
- Disconnect from that instance and do the same thing for the m4.large instance then the c5.2xlarge instance
- What's the difference between all three instances?
- Can you explain the performance results between the t2.micro instance and the m4.large instance?

### 2.3.  Use dd to write 20 GB of data to EFS from each instance
Run this command against all three instances to create a 20 GB file on EFS and monitor network traffic and throughput in real-time
```sh
time dd if=/dev/zero of=/mnt/efs/01/tutorial/dd/20G-dd-$(date +%Y%m%d%H%M%S.%3N) bs=1M count=20480 conv=fsync &
nload -u M
```

### 2.4.  Close all SSH sessions
#
#
### Results
All EC2 instance types have different network performance characteristics so each can drive different levels of throughput to EFS. While the t2.micro instance initially appears to have better network performance when compared against an m4.large instance, its high network throughput is short lived as a result of the burst characteristics on t2 instances.

| Step | EC2 Instance Type | Data Size | Duration | Burst Throughput | Baseline Throughput | Average Throughput |
| :--- | :--- | --- | --- | --- | --- | --- |
| 2.3. | t2.micro | 20 GB | 720 seconds | 120 MB/s | 7 MB/s | 30 MB/s |
| 2.3. | m4.large | 20 GB | 384 seconds | - | - | 56 MB/s |
| 2.3. | c5.2xlarge | 20 GB | 143 seconds | - | - | 150 MB/s* |

*this was achieved using a file system with a permitted throughput greater than 200 MB/s

## Section 3
### Demonstrate how different I/O sizes and sync frequencies affects throughput to EFS
 ___
This section will compare how different I/O sizes (block sizes) and sync frequencies (the rate data is persisted to disk) have a profound impact on performance between EBS and EFS.

### 3.1.  SSH into the c5.2xlarge EC2 instance
### 3.2.  Write to EBS using 1 MB block size and sync once after each file
Run this command against the c5.2xlarge instance and use dd to create a 2 GB file on EBS using a 1 MB block size and issuing a sync once at the end to ensure everything is written to disk.
```sh
time dd if=/dev/zero of=/ebs/tutorial/dd/2G-dd-$(date +%Y%m%d%H%M%S.%3N) bs=1M count=2048 status=progress conv=fsync
```
Record run time.
### 3.3.  Write to EFS using 1 MB block size and sync once after each file
Run this command against the c5.2xlarge instance and use dd to create a 2 GB file on EFS using a 1 MB block size and issuing a sync once at the end to ensure everything is written to disk.
```sh
time dd if=/dev/zero of=/mnt/efs/01/tutorial/dd/2G-dd-$(date +%Y%m%d%H%M%S.%3N) bs=1M count=2048 status=progress conv=fsync
```
Record run time.
### 3.4.  Write to EBS using 16 MB block size and sync once after each file
Run this command against the c5.2xlarge instance and use dd to create a 2 GB file on EBS using a 16 MB block size and issuing a sync once at the end to ensure everything is written to disk.
```sh
time dd if=/dev/zero of=/ebs/tutorial/dd/2G-dd-$(date +%Y%m%d%H%M%S.%3N) bs=16M count=128 status=progress conv=fsync
```
Record run time.
### 3.5.  Write to EFS using 16 MB block size and sync once after each file
Run this command against the c5.2xlarge instance and use dd to create a 2 GB file on EFS using a 16 MB block size and issuing a sync once at the end to ensure everything is written to disk.
```sh
time dd if=/dev/zero of=/mnt/efs/01/tutorial/dd/2G-dd-$(date +%Y%m%d%H%M%S.%3N) bs=16M count=128 status=progress conv=fsync
```
Record run time.
### 3.6.  Write to EBS using 1 MB block size and sync after each block
Run this command against the c5.2xlarge instance and use dd to create a 2 GB file on EBS using a 1 MB block size and issuing a sync after each block to ensure each block is written to disk.
```sh
time dd if=/dev/zero of=/ebs/tutorial/dd/2G-dd-$(date +%Y%m%d%H%M%S.%3N) bs=1M count=2048 status=progress oflag=sync
```
Record run time.
### 3.7.  Write to EFS using 1 MB block size and sync after each block
Run this command against the c5.2xlarge instance and use dd to create a 2 GB file on EFS using a 1 MB block size and issuing a sync after each block to ensure each block is written to disk.
```sh
time dd if=/dev/zero of=/mnt/efs/01/tutorial/dd/2G-dd-$(date +%Y%m%d%H%M%S.%3N) bs=1M count=2048 status=progress oflag=sync
```
Record run time.
### 3.8.  Write to EBS using 16 MB block size and sync after each block
Run this command against the c5.2xlarge instance and use dd to create a 2 GB file on EBS using a 16 MB block size and issuing a sync after each block to ensure each block is written to disk.
```sh
time dd if=/dev/zero of=/ebs/tutorial/dd/2G-dd-$(date +%Y%m%d%H%M%S.%3N) bs=16M count=128 status=progress oflag=sync
```
Record run time.
### 3.9.  Write to EFS using 16 MB block size and sync after each block
Run this command against the c5.2xlarge instance and use dd to create a 2 GB file on EFS using a 16 MB block size and issuing a sync after each block to ensure each block is written to disk.
```sh
time dd if=/dev/zero of=/mnt/efs/01/tutorial/dd/2G-dd-$(date +%Y%m%d%H%M%S.%3N) bs=16M count=128 status=progress oflag=sync
```
Record run time.
#
#
### Results
All EC2 instance types have different network performance characteristics so each can drive different levels of throughput to EFS. While the t2.micro instance appears to have better network performance when initially compared to an m4.large instance, its high network throughput is short lived as a result of the burst characteristics on t2 instances.

| Step | EC2 Instance Type | Operation | Data Size | Block Size | Sync | Storage | Duration | Throughput |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 3.2 | c5.2xlarge | Create | 2 GB | 1 MB | After each file | EBS | 17.0 seconds | 126 MB/s |
| 3.3 | c5.2xlarge | Create | 2 GB | 1 MB | After each file | EFS | 10.7 seconds | 201 MB/s* |
| 3.4 | c5.2xlarge | Create | 2 GB | 16 MB | After each file | EBS | 17.0 seconds | 126 MB/s |
| 3.5 | c5.2xlarge | Create | 2 GB | 16 MB | After each file | EFS | 10.6 seconds | 202 MB/s* |
| 3.6 | c5.2xlarge | Create | 2 GB | 1 MB | After each block | EBS | 17.3 seconds | 124 MB/s |
| 3.7 | c5.2xlarge | Create | 2 GB | 1 MB | After each block | EFS | 85.5 seconds | 25 MB/s* |
| 3.8 | c5.2xlarge | Create | 2 GB | 16 MB | After each block | EBS | 16.3 seconds | 132 MB/s |
| 3.9 | c5.2xlarge | Create | 2 GB| 16 MB | After each block | EFS | 23 seconds | 93 MB/s* |

*this was achieved using a file system with a permitted throughput greater than 200 MB/s

## Section 4
### Demonstrate how multi-threaded access improves throughput and IOPS
 ___
This section will demonstrate how increasing the number of threads accessing EFS will significantly improve performance when compared to EBS.

### 4.1.  SSH into the c5.2xlarge EC2 instance
### 4.2.  Write to EBS using 4 threads and sync after each block
Run this command against the c5.2xlarge instance which will use dd to write 2 GB of data to EBS using a 1 MB block size and issuing a sync after each block to ensure everything is written to disk.
```sh
time seq 0 3 | parallel --will-cite -j 4 dd if=/dev/zero of=/ebs/tutorial/dd/2G-dd-$(date +%Y%m%d%H%M%S.%3N)-{} bs=1M count=512 oflag=sync
```
Record run time.
### 4.3.  Write to EFS using 4 threads and sync after each block
Run this command against the c5.2xlarge instance which will use dd to write 2 GB of data to EFS using a 1 MB block size and issuing a sync after each block to ensure everything is written to disk.
```sh
time seq 0 3 | parallel --will-cite -j 4 dd if=/dev/zero of=/mnt/efs/01/tutorial/dd/2G-dd-$(date +%Y%m%d%H%M%S.%3N)-{} bs=1M count=512 oflag=sync
```
Record run time.
### 4.4.  Write to EBS using 16 threads and sync after each block
Run this command against the c5.2xlarge instance which will use dd to write 2 GB of data to EBS using a 1 MB block size and issuing a sync after each block to ensure everything is written to disk.
```sh
time seq 0 15 | parallel --will-cite -j 16 dd if=/dev/zero of=/ebs/tutorial/dd/2G-dd-$(date +%Y%m%d%H%M%S.%3N)-{} bs=1M count=128 oflag=sync
```
Record run time.
### 4.5.  Write to EFS using 16 threads and sync after each block
Run this command against the c5.2xlarge instance which will use dd to write 2 GB of data to EFS using a 1 MB block size and issuing a sync after each block to ensure everything is written to disk.
```sh
time seq 0 15 | parallel --will-cite -j 16 dd if=/dev/zero of=/mnt/efs/01/tutorial/dd/2G-dd-$(date +%Y%m%d%H%M%S.%3N)-{} bs=1M count=128 oflag=sync
```
Record run time.
#
#
### Results
The distributed data storage design of EFS means that multi-threaded applications can drive substantial levels of aggregate throughput and IOPS. If you parallelize your writes to EFS by increasing the number of threads, you can increase the overall throughput and IOPS to EFS.

| Step | Operation | Data Size | Block Size | Threads | Sync | Storage | Duration | Throughput |
| --- | --- | --- | --- | --- | --- | --- | --- | ---
| 4.2 | Create | 2 GB | 1 MB | 4 | After each block | EBS | 16.6 seconds | 131 MB/s |
| 4.3 | Create | 2 GB | 1 MB | 4 | After each block | EFS | 21.7 seconds | 99 MB/s* |
| 4.4 | Create | 2 GB | 1 MB | 16 | After each block | EBS | 16.4 seconds | 131 MB/s |
| 4.5 | Create | 2 GB | 1 MB | 16 | After each block | EFS | 7.9 seconds | 271 MB/s* |

*this was achieved using a file system with a permitted throughput greater than 200 MB/s

## Section 5
### Compare different file transfer tools
 ___
This section will compare and demonstrate how different file transfer tools affect performance when accessing an EFS file system.

### 5.1.  SSH into the c5.2xlarge EC2 instance
### 5.2.  Review the data to transfer
Run this command against the c5.2xlarge instance to view the total size and count of files to be trasnferred.
```sh
du -csh /ebs/tutorial/data-1m/
find /ebs/tutorial/data-1m/. -type f | wc -l
```
### 5.3.  Set the $instanceid variable
Run this command against the c5.2xlarge instance to set the $instanceid variable which will be used in the preceeding steps.
```sh
instance_id=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
```
### 5.4.  Transfer files from EBS to EFS using ***rsync***
Run this command against the c5.2xlarge instance to drop caches and transfer 5,000 files (~1 MB each) totalling 5 GB from EBS to EFS using rsync.
```sh
sudo su
sync && echo 3 > /proc/sys/vm/drop_caches
exit
time rsync -r /ebs/tutorial/data-1m/ /mnt/efs/01/tutorial/rsync/${instance_id} &
nload -u M
```
### 5.5.  Transfer files from EBS to EFS using ***cp***
Run this command against the c5.2xlarge instance to drop caches and transfer 5,000 files (~1 MB each) totalling 5 GB from EBS to EFS using cp.
```sh
sudo su
sync && echo 3 > /proc/sys/vm/drop_caches
exit
time cp -r /ebs/tutorial/data-1m/* /mnt/efs/01/tutorial/cp/${instance_id} &
nload -u M
```
### 5.6.  Set the $threads variable
Run this command against the c5.2xlarge instance to set the $threads variable to 4 threads per vcpu. This variable will be used in the subsequent steps.
```sh
threads=$(($(nproc --all) * 4))
```
### 5.7.  Transfer files from EBS to EFS using ***fpsync***
Run this command against the c5.2xlarge instance to drop caches and transfer 5,000 files (~1 MB each) totalling 5 GB from EBS to EFS using fpsync.
```sh
sudo su
sync && echo 3 > /proc/sys/vm/drop_caches
exit
time /usr/local/bin/fpsync -n ${threads} -v /ebs/tutorial/data-1m/ /mnt/efs/01/tutorial/fpsync/${instance_id} &
nload -u M
```
### 5.8.  Transfer files from EBS to EFS using ***cp + GNU Parallel***
Run this command against the c5.2xlarge instance to drop caches and transfer 5,000 files (~1 MB each) totalling 5 GB from EBS to EFS using cp + GNU Parallel.
```sh
sudo su
sync && echo 3 > /proc/sys/vm/drop_caches
exit
time find /ebs/tutorial/data-1m/. -type f | parallel --will-cite -j ${threads} cp {} /mnt/efs/01/tutorial/parallelcp &
nload -u M
```
### 5.9.  Transfer files from EBS to EFS using ***fpart + cpio + GNU Parallel***
Run this command against the c5.2xlarge instance to drop caches and transfer 5,000 files (~1 MB each) totalling 5 GB from EBS to EFS using fpart + cpio + GNU Parallel.
```sh
sudo su
sync && echo 3 > /proc/sys/vm/drop_caches
exit
cd /ebs/tutorial/smallfile
time /usr/local/bin/fpart -Z -n 1 -o /home/ec2-user/fpart-files-to-transfer .
head /home/ec2-user/fpart-files-to-transfer.0
time parallel --will-cite -j ${threads} --pipepart --round-robin --delay .1 --block 1M -a /home/ec2-user/fpart-files-to-transfer.0 sudo "cpio -dpmL /mnt/efs/01/tutorial/parallelcpio/${instance_id}" &
nload -u M
```
#
#
### Results
Not all file transfer utilities are created equal. File systems are distributed across an unconstrained number of storage servers and this distributed data storage design means that multithreaded applications like fpsync, mcp, and GNU parallel can drive substantial levels of throughput and IOPS to EFS when compared to single-threaded applications.


| Step | File Transfer Tool | File Count | File Size | Total Size | Threads | Duration | Throughput |
| --- | --- | --- | --- | --- | --- | --- | ---
| 5.4 | rsync | 5000 | 1 MB | 5 GB | 1 | 435 seconds | 11.7 MB/s* |
| 5.5 | cp | 5000 | 1 MB | 5 GB | 1 | 329 seconds | 15.6 MB/s* |
| 5.7 | fpsync | 5000 | 1 MB | 5 GB | 32 | 210 seconds | 24.4 MB/s* |
| 5.8 | cp + GNU Parallel | 5000 | 1 MB | 5 GB | 32 | 73 seconds | 70.1 MB/s* |
| 5.9 | fpart + cpio + GNU Parallel | 5000 | 1 MB | 5 GB | 32 | 42 seconds | 122 MB/s* |

*this was achieved using a file system with a permitted throughput greater than 200 MB/s

![](/images/efs_performance_tutorial_file_transfer_results_20180726.png)

## Section 6
### Cleanup
Delete all files on the EFS file system that were created during this tutorial and delete the CloudFormation stack so you don't continue to incur additional charges for these resources.

### 6.1.  Delete all files on the EFS file system created during the tutorial
Run this command on the c5.2xlarge instance to delete the /mnt/efs/01/tutorial data.
```sh
cd /mnt/efs/01/tutorial/;
time /bin/ls -d -- */ | parallel --will-cite -j$(($(nproc --all) * 8)) sudo rm {} -r & 
```
### 6.2.  Delete the AWS CloudFormation stack you launched during the tutorial
![](https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-performance-tutorial-delete-cf-stack-screenshot.png)

## Conclusion
The distributed data storage design of Amazon EFS enables high levels of availability, durability, and scalability. This distributed architecture results in a small latency overhead for each file operation. Due to this per-operation latency, overall throughput generally increases as the average I/O size increases, because the overhead is amortized over a larger amount of data. Amazon EFS supports highly parallelized workloads (for example, using concurrent operations from multiple threads and multiple Amazon EC2 instances), which enables high levels of aggregate throughput and operations per second.

---
## Next tutorial
### Click on the link below to go to the next Amazon EFS tutorial

| Tutorial | Link
| --- | ---
| **Scale-out** | [![](/images/efs_tutorial.png)](/scale-out) |

---


## Troubleshooting
For feedback, suggestions, or corrections, please email me at [darrylo@amazon.com](mailto:darrylo@amazon.com).


## License
This sample code is made available under the MIT-0 license. See the LICENSE file.
