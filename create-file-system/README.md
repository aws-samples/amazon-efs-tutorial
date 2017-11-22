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


### The Environment

This tutorial will create an Amazon Virtual Private Cloud (VPC) and an Amazon Elastic File System (EFS) using AWS Cloudformation templates. The Cloudformation templates that create the EFS file system will also add data and grow the file system.

Each instance will download all S3 objects from the public dataset https://aws.amazon.com/public-datasets/dc-lidar/.

For more information about this dataset, please refer to http://geospatial.dcgis.dc.gov/templates/dcfinder/s2.html?appid=62c9607bcfb349d5988e39390d50e995

WARNING!! This tutorial environment will exceed your free-usage tier. You will incur charges as a result of building this environment and executing the scripts included in this tutorial. Delete all files on the EFS file system that were created during this tutorial and delete the  stack so you don’t continue to incur additional compute and storage charges.

## Tutorial

### Step 1: Create Amazon Virtual Private Cloud (Amazon VPC)

> Click on the link below in the desired AWS region to create the AWS Cloudformation stack that will create an Amazon VPC in your AWS account. This VPC will host the Amazon EFS file system and the other AWS resouces created as a part of this tutorial.

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=efs-create-vpc-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-create-vpc.yml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=efs-create-vpc-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-create-vpc.yml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=efs-create-vpc-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-create-vpc.yml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=efs-create-vpc-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-create-vpc.yml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=efs-create-vpc-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-create-vpc.yml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=efs-create-vpc-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-create-vpc.yml) |


### Step 2: Create Amazon Elastic File System (Amazon EFS) w/ data and CloudWatch dashboard, alarms, and size metric

> Click on the link below in the same AWS region as above to create the AWS Cloudformation stack that will create an Amazon EFS file system and supporting resources in your AWS account. This stack is a series of nested templates that will: 1) create a file system with data; 2) create AWS CloudWatch alarms to alert when burst credit balance thresholds are breached; 3) create CloudWatch dashboard with metrics and alarms. This file system will be used in the **Performance** and **Scale-out** tutorials.

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=efs-create-file-system-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-file-system-master.yml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=efs-create-file-system-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-file-system-master.yml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=efs-create-file-system-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-file-system-master.yml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=efs-create-file-system-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-file-system-master.yml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=efs-create-file-system-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-file-system-master.yml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=efs-create-file-system-tutorial&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-file-system-master.yml) |

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

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=efs-create-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-burst-credit-balance-alarms.yml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=efs-create-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-burst-credit-balance-alarms.yml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=efs-create-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-burst-credit-balance-alarms.yml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=efs-create-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-burst-credit-balance-alarms.yml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=efs-create-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-burst-credit-balance-alarms.yml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=efs-create-alarms&templateURL=https://s3.amazonaws.com/amazon-elastic-file-system/tutorial/create-file-system/efs-burst-credit-balance-alarms.yml) |

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
