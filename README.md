![](https://s3.amazonaws.com/efs-us-east-1/tutorial/AWS_logo_PMS_300x180.png)

![](https://s3.amazonaws.com/efs-us-east-1/tutorial/100x100_benefit_available.png)![](https://s3.amazonaws.com/efs-us-east-1/tutorial/100x100_benefit_ingergration.png)![](https://s3.amazonaws.com/efs-us-east-1/tutorial/100x100_benefit_ecryption-lock.png)![](https://s3.amazonaws.com/efs-us-east-1/tutorial/100x100_benefit_fully-managed.png)![](https://s3.amazonaws.com/efs-us-east-1/tutorial/100x100_benefit_lowcost-affordable.png)![](https://s3.amazonaws.com/efs-us-east-1/tutorial/100x100_benefit_performance.png)![](https://s3.amazonaws.com/efs-us-east-1/tutorial/100x100_benefit_scalable.png)![](https://s3.amazonaws.com/efs-us-east-1/tutorial/100x100_benefit_storage.png)

# **Amazon Elastic File System (Amazon EFS)**

## Tutorials

### Version 1.2.2

efs-t-1.2.1

---

© 2020 Amazon Web Services, Inc. and its affiliates. All rights reserved.
This sample code is made available under the MIT-0 license. See the LICENSE file.

Errors or corrections? Email us at [darrylo@amazon.com](mailto:darrylo@amazon.com).

---

### Table of Contents  
[Tutorials](#tutorials) 

[1. Create a file system](#1-create-a-file-system) 

[2. Performance](#2-performance) 

[3. Scale-out](#3-scale-out)

[4. Monitoring](#4-monitoring)

[5. In-cloud Transfer](#5-in-cloud-transfer)

[6. Data science](#6-data-science)


---

### Tutorials

These six (6) tutorials are designed to help you better understand the performance characteristics of Amazon Elastic File System (Amazon EFS) and how parallelism, I/O size, and Amazon EC2 instance types affects file system IOPS and throughput.
#
### 1. Create a file system
This tutorial is a set of AWS Cloudformation templates that will create an Amazon EFS file system and pre-load data to grow the file system to obtain higher levels of IOPS and throughput. Throughput and IOPS on Amazon EFS scales as a file system grows, so larger file systems are able to achieve higher levels of throughput and IOPS. Because file-based workloads are typically spiky—driving high levels of throughput for short periods of time, and low levels of throughput the rest of the time—Amazon EFS is designed to burst to high throughput levels for periods of time. Amazon EFS uses a credit system to determine when file systems can burst. File systems can be monitored using AWS CloudWatch metrics. These Cloudformation templates will also create an AWS CloudWatch dashboard, custom metrics, alarms, scheduled events, AWS Lambda function, SNS notification, and an Auto Scaling group to monitor and dynamically adjust alarm thresholds as the file system grows and shrinks.

Click on the ![](/images/efs_tutorial.png) link below to go to the **Create a file system** tutorial. Once you've finished that tutorial move on to **Performance**.

| Tutorial | Link
| --- | ---
| **Create a file system** | [![](/images/efs_tutorial.png)](/create-file-system) |

### 2. Performance
This tutorial is a set of scripts that will demonstrate:
- different instance types provide different levels of network performance when accessing a file system
- different I/O sizes (block sizes) and sync() freqencies (the rate data is persisted to disk) effects file system throughput
- increasing the number of threads accessing a file system will increase IOPS and throughput

Click on the ![](/images/efs_tutorial.png) link below to go to the **Performance** tutorial. Once you've finished that tutorial move on to **Scale-out**.

| Tutorial | Link
| --- | ---
| **Performance** | [![](/images/efs_tutorial.png)](/performance) |


### 3. Scale-out
This tutorial is a Cloudformation template that will create an Amazon EC2 spot fleet and download objects in parallel from an Amazon S3 bucket.

Click on the ![](/images/efs_tutorial.png) link below to go to the **Scale-out** tutorial. Once you've finished that tutorial move on to the **Scenarios**.

| Tutorial | Link
| --- | ---
| **Scale-out** | [![](/images/efs_tutorial.png)](/scale-out) |

### 4. Monitoring
This tutorial is designed to help you better understand how Amazon EFS is performing by using Amazon CloudWatch and Metric Math to monitor file system performance.

Click on the ![](/images/efs_tutorial.png) link below to go to the **Monitoring** tutorial.

| Tutorial | Link
| --- | ---
| **Monitoring** | [![](/images/efs_tutorial.png)](/monitoring) |

### 5. In-cloud Transfer
The AWS DataSync In-cloud Transfer Quick Start and Scheduler creates a one-time or recurring schedule to transfer files between source and destination Amazon EFS file systems. These file systems could be in the same or different AWS regions.

Click on the ![](/images/efs_tutorial.png) link below to go to the **In-cloud Transfer** Quick Start.

| Quick Start | Link
| --- | ---
| **In-cloud Transfer** | [![](/images/efs_tutorial.png)](/in-cloud-transfer) |

### 6. Data sciense workshop
This tutorial covers how to use Amazon EFS, a highly available, highly durable and elastic cloud native file storage for Data Science workloads.

Click on the ![](/images/efs_tutorial.png) link below to go to the **Data science** tutorial.

| Tutorial | Link
| --- | ---
| **Data science** | [![](/images/efs_tutorial.png)](/data-science) |


---

## Troubleshooting


For feedback, suggestions, or corrections, please email me at [darrylo@amazon.com](mailto:darrylo@amazon.com).


## License

This sample code is made available under the MIT-0 license. See the LICENSE file.
