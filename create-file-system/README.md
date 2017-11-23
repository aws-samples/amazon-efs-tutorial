![](https://s3.amazonaws.com/aws-us-east-1/tutorial/AWS_logo_PMS_300x180.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_available.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_ingergration.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_ecryption-lock.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_fully-managed.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_lowcost-affordable.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_performance.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_scalable.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_storage.png)
# **Amazon Elastic File System (Amazon EFS)**

## Create File System Tutorial

### Version 1.0.1

efs-cfst-1.0.1

---

© 2017 Amazon Web Services, Inc. and its affiliates. All rights reserved. This work may not be  reproduced or redistributed, in whole or in part, without prior written permission from Amazon Web Services, Inc. Commercial copying, lending, or selling is prohibited.

Errors or corrections? Email us at [darrylo@amazon.com](mailto:darrylo@amazon.com).

---

## Tutorial Overview

### Overview

This tutorial is designed to create an Amazon EFS environment for subsequent tutorials which are designed to help you better understand the distributed data storage design of Amazon Elastic File System (Amazon EFS) and how to best leverage this design by taking advantage of scale-out achitectures.

### Prerequisites

* An AWS account with administrative level access
* An Amazon EC2 key pair

If a key pair has not been previously created in your account, please refer to [Creating a Key Pair Using Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair) from the AWS EC2 User's Guide.  

Verify that the key pair is created in the same AWS region you will use for the tutorial.

WARNING!! This tutorial environment will exceed your free-usage tier. You will incur charges as a result of building this environment and executing the scripts included in this tutorial. Delete all files on the EFS file system that were created during this tutorial and delete the  stack so you don’t continue to incur additional compute and storage charges.

## Tutorial
### Step 1: Create an Amazon Virtual Private Cloud (Amazon VPC)
#### Parameters

- Select three (3) Availability Zones

- Use default **Network Configuration** parameters

- Select an existing Amazon EC2 key pair

- Use the default NAT instance size

---
![](https://s3.amazonaws.com/aws-us-east-1/tutorial/create-efs-resources/efs-create-vpc-2.png)

---

    Click on the link below in the desired AWS region to create the AWS Cloudformation stack that will create an Amazon VPC in your AWS account. This VPC will host the Amazon EFS file system and the other AWS resources created in this tutorial.

#

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=efs-create-vpc-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-create-vpc.yml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=efs-create-vpc-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-create-vpc.yml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=efs-create-vpc-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-create-vpc.yml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=efs-create-vpc-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-create-vpc.yml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=efs-create-vpc-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-create-vpc.yml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=efs-create-vpc-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-create-vpc.yml) |


---

### Step 2: Create Amazon Elastic File System (Amazon EFS) w/ data and CloudWatch dashboard, alarms, and size metric

#### Parameters
- Use default parameters
![](https://s3.amazonaws.com/aws-us-east-1/tutorial/create-efs-resources/efs-create-vpc-2.png)
#
> Click on the link below in the same AWS region as above to create the AWS Cloudformation stack that will create an Amazon EFS file system and supporting resources in your AWS account. This stack is a series of nested templates that will:
- create an Amazon EFS file system with data
- create AWS CloudWatch alarms to alert when the burst credit balance thresholds are breached
- create AWS CloudWatch dashboard with metrics and alarms
This file system will be used in the **Performance** and **Scale-out** tutorials.

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=efs-create-file-system-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-file-system-master.yml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=efs-create-file-system-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-file-system-master.yml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=efs-create-file-system-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-file-system-master.yml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=efs-create-file-system-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-file-system-master.yml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=efs-create-file-system-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-file-system-master.yml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=efs-create-file-system-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-file-system-master.yml) |


## Next tutorial

### Click on the link below to go to the next Amazon EFS tutorial

| Tutorial | Link
| --- | ---
| **Performance** | [![cloudformation-launch-stack](https://s3.amazonaws.com/aws-us-east-1/tutorial/create-efs-resources/efs-tutorial-button-20171120.png)](https://github.com/aws-samples/amazon-efs-tutorial/tree/master/performance) |


## Bonus

### Individual CloudFormation Templates for existing Amazon EFS file systems

> Below are the unnested CloudFormation templates from above that can be run individually against an existing file system. This allows you to add the features highlighted in this tutorial to an existing file system, like...

- adding data to grow a file system
- creating CloudWatch alarms to monitor burst credit balance and a CloudWatch dashboard with widgets for the burst credit balance alarms, and other important file system metrics, including a custom metric for file system size
- creating just the CloudWatch alarms to monitor burst credit balance
- creating just the CloudWatch dasboard with widgets for important file system metrics, including a file system size custom  metric


### Add data to grow a file system

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=efs-add-data&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-add-data.yml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=efs-add-data&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-add-data.yml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=efs-add-data&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-add-data.yml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=efs-add-data&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-add-data.yml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=efs-add-data&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-add-data.yml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=efs-add-data&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-add-data.yml) |

### Create an AWS CloudWatch Dashboard with a file system size custom metric and CloudWatch Alarms to monitor Burst Credit Balance

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=efs-create-dashboard-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-dashboard-with-size-monitor-and-burst-credit-balance-alarms.yml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=efs-create-dashboard-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-dashboard-with-size-monitor-and-burst-credit-balance-alarms.yml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=efs-create-dashboard-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-dashboard-with-size-monitor-and-burst-credit-balance-alarms.yml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=efs-create-dashboard-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-dashboard-with-size-monitor-and-burst-credit-balance-alarms.yml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=efs-create-dashboard-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-dashboard-with-size-monitor-and-burst-credit-balance-alarms.yml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=efs-create-dashboard-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-dashboard-with-size-monitor-and-burst-credit-balance-alarms.yml) |

### Create AWS CloudWatch Alarms to Monitor Burst Credit Balance

### Overview

This AWS Cloudformation template will create AWS resources to monitor and send notifications if the burst credit balance of the specified Amazon EFS file system drops below predefined thresholds.

Throughput on Amazon EFS scales as a file system grows. Because file-based workloads are typically spiky—driving high levels of throughput for short periods of time, and low levels of throughput the rest of the time—Amazon EFS is designed to burst to high throughput levels for periods of time. Amazon EFS uses a credit system to determine when file systems can burst. Each file system earns credits over time at a baseline rate that is determined by the size of the file system, and uses credits whenever it reads or writes data. The baseline rate is 50 MiB/s per TiB of storage (equivalently, 50 KiB/s per GiB of storage). Accumulated burst credits give the file system permission to drive throughput above its baseline rate. When a file system has a positive burst credit balance, it can burst. The burst rate is 100 MiB/s per TiB of storage (equivalently, 100 KiB/s per GiB of storage).

If a workload accessing a file system relies on the burst throughput for normal operations, running out of burst credits could negatively impact the workload so monitoring the file system's burst credit balance is essential. This AWS CloudFormation template will create two Amazon CloudWatch alarms that will send email notifications if the burst credit balance drops below two predefined thresholds, a 'Warning' threshold and a 'Critical' threshold.  These thresholds are based on the number of minutes it would take to completely use all burst credits if the file system was being driven at the highest throughput rate possible, the permitted throughput rate. You enter these minute variables as input parameters in the Cloudformation template. The 'Warning' threshold and has a default value of 180 minutes. This means that a CloudWatch alarm will send an email notification 180 minutes before the credit balance drops to zero, based on the latest permitted throughput rate. The second alarm and notification is a 'Critical' notification and has a default value of 60 minutes. This alarm will send an email notification 60 minutes before the credit balance drops to zero, based on the latest permitted throughput rate. Permitted throughput is dynamic, scaling up as the file systems grows and scaling down as the file system shrinks. Therefore a third and fourth alarm is create that monitors permitted throughput. If the permitted throughput increases or decreases, an email notification is sent and an Auto Scaling Group will launch an EC2 instance that dynamically resets the 'Warning' and 'Critical' thresholds based on the latest permitted throughput rate. This EC2 instance will auto terminate and a new instance will launch to reset the thresholds only when the permitted throughput rate increases or decreases.

CloudWatch Alarm Examples:

| Warning Alarm | Critical Alarm | Permitted Throughput Increase | Permitted Throughput Decrease |
| --- | --- | --- | ---
| ![burst-credit-balance-warning](https://s3.amazonaws.com/aws-us-east-1/tutorial/burst-credit-balance-warning-screenshot.png) | ![burst-credit-balance-critical](https://s3.amazonaws.com/aws-us-east-1/tutorial/burst-credit-balance-critical-screenshot.png) | ![permitted-throughput-increase](https://s3.amazonaws.com/aws-us-east-1/tutorial/permitted-throughput-increase-screenshot.png) | ![permitted-throughput-decrease](https://s3.amazonaws.com/aws-us-east-1/tutorial/permitted-throughput-decrease-screenshot.png) |

### 1) Launch the AWS CloudFormation Stack

Click the  ![cloudformation-launch-stack](https://s3.amazonaws.com/aws-us-east-1/tutorial/deploy_to_aws_20171004_v2.png) link below to create the AWS CloudFormation stack in your account and desired AWS region.

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=efs-create-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-burst-credit-balance-alarms.yml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=efs-create-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-burst-credit-balance-alarms.yml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=efs-create-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-burst-credit-balance-alarms.yml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=efs-create-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-burst-credit-balance-alarms.yml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=efs-create-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-burst-credit-balance-alarms.yml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=efs-create-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-burst-credit-balance-alarms.yml) |


### 2) Confirm SNS subscription

The email address entered as an input parameter will automatically be subscribed to the SNS topic and will receive an **AWS Notification - Subscription Confirmation email**. Click on the ***Confirm subscription*** link in the email.

![](https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-burst-credit-balance-notifications-confirm-subscription.png)

### 3) Verify CloudWatch alarms have been created

Verify all four CloudWatch alarms have been created. See the AWS Resources section below for the alarm names.

### 4) Verify the EC2 instance terminates after a few minutes

The EC2 instance will automatically terminate once the script runs that calcualtes the burst credit balance thresholds.

## Parameters
 ___
![](https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-burst-credit-balance-notifications-screenshot.png)
 ___
### Stack name

The name of the AWS Cloudformation stack. This must be unique and its recommended that you append a short GUID suffix to keep each iteration of this stack unique. The stack name is used in the name of the SNS topic and CloudWatch alarms.

>```example: efs-burst-credit-balance-notifications-6ECABFA1```

### Amazon EFS File System Id

The Amazon EFS file system id of the file system you want to monitor.

>```example: fs-26e6418e```

### Burst Credit Balance 'Warning' Threshold (Minutes)

The number of minutes before the burst credit balance drops to zero, based on the latest permitted throughput rate. This is when the 'Warning' email notification will be send. Default is 180 minutes (3 hours).

>```example: 180```

### Burst Credit Balance 'Critical' Threshold (Minutes)

The number of minutes before the burst credit balance drops to zero, based on the latest permitted throughput rate. This is when the 'Critical' email notification will be send. Default is 60 minutes (1 hour).

>```example: 60```

### SNS Email Address

The email address that will receive the 'Warning' and 'Critical' notifications.

>```example: darrylo@amazon.com```

### Instance Type

The EC2 instance type launched in an Auto Scaling group that runs a script to reset the burst credit balance alarm threshold values. Default is t2.nano.

>```example: t2.nano```

### Subnet for AZ 0

A VPC public subnet for the Auto Scaling group.

>```example: subnet-ab123dc4```

### Subnet for AZ 1

A VPC public subnet for the Auto Scaling group.

>```example: subnet-ab123dc5```

### Subnet for AZ 2

A VPC public subnet for the Auto Scaling group.

>```example: subnet-ab123dc6```

## AWS Resources Created

### SNS Topic w/ the email address subscribed

One SNS topic and subscription that receives all SNS notifications.

>Naming convention: {file-system-id}-notifications-{cloudformation-stack-name}

>```example: fs-26e6418e-notifications-efs-burst-credit-balance-notifications-6ECABFA1```

### CloudWatch Alarms

Four CloudWatch alarms.

One 'Warning' alarm that alerts when the burst credit balance drops below the 'warning' threshold for 5 minutes.

>Naming convention: {file-system-id} burst credit balance - Warning - {cloudformation-stack-name}

>```example: fs-26e6418e burst credit balance - Warning - efs-burst-credit-balance-notifications-6ECABFA1```

One 'Critical' alarm that alerts when the burst credit balance drops below the 'critical' threshold for 5 minutes.

>Naming convention: {file-system-id} burst credit balance - Warning - {cloudformation-stack-name}

>```example: fs-26e6418e burst credit balance - Critical - efs-burst-credit-balance-notifications-6ECABFA1```

One burst credit balance increase threshold alarm that alerts when the permitted throughput increases for 5 minutes.

>Naming convention: {file-system-id} burst credit balance increase threshold - {cloudformation-stack-name}

>```example: fs-26e6418e burst credit balance increase threshold - efs-burst-credit-balance-notifications-6ECABFA1```

One burst credit balance decrease threshold alarm that alerts when the permitted throughput decreases for 5 minutes.

>Naming convention: {file-system-id} burst credit balance decrease threshold - {cloudformation-stack-name}

>```example: fs-26e6418e burst credit balance increase threshold - efs-burst-credit-balance-notifications-6ECABFA1```

### VPC Security Group

One new security group is created in the VPC. This security group is locked down and has no inbound rules defined.

>```Security group description: No inbound access security group```

### IAM Policy & EC2 Instance Profile

One IAM policy and EC2 instance profile attached to the auto scaling launch configuration. This grants API permissions for the script to run.

| Allows |
| --- |
| cloudwatch:GetMetricStatistics |
| cloudwatch:PutMetricAlarm |
| autoscaling:DescribeAutoScalingGroups |
| autoscaling:DescribeAutoScalingInstances |
| autoscaling:UpdateAutoScalingGroup |
| elasticfilesystem:DescribeFileSystems |
| sns:Publish |

>```Policy name: efs-burst-credit-balance-cloudwatch-alarms```

### Auto Scaling Group & Launch Configuration

One auto scaling group and launch configuration to launch and EC2 instance that runs a script to calculate the burst credit balance thresholds.

>The auto scaling group will have a maximum size of 1, a desired size of 1, and a minimum size of 0.

>The launch configuration will launch instances with no EC2 key-pair and the security group created above. A script is dynamically generated in the /tmp directory. This script will run at boot-time and will calculate the burst credit balance thresholds.

### Create an AWS CloudWatch Dashboard with a file system size custom metric

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=efs-create-dashboard&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-dashboard-with-size-monitor.yml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=efs-create-dashboard&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-dashboard-with-size-monitor.yml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=efs-create-dashboard&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-dashboard-with-size-monitor.yml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=efs-create-dashboard&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-dashboard-with-size-monitor.yml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=efs-create-dashboard&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-dashboard-with-size-monitor.yml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=efs-create-dashboard&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-dashboard-with-size-monitor.yml) |



For feedback, suggestions, or corrections, please email me at [darrylo@amazon.com](mailto:darrylo@amazon.com).
