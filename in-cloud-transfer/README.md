
<img align="left" src="https://s3.amazonaws.com/aws-us-east-1/tutorial/AWS_logo_PMS_300x180.png"><img align="right" src="https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/in-cloud-transfer/Amazon-Elastic-File-System_EFS_light-bg.png"><img align="right" src="https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/in-cloud-transfer/AWS-DataSync_light-bg.png">
&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

# **Amazon Elastic File System (Amazon EFS)**

## AWS DataSync Quick Start In-cloud Transfer and Scheduler

### Version 1.0.0

efs-ts-1.0.0

---

© 2019 Amazon Web Services, Inc. and its affiliates. All rights reserved. This work may not be  reproduced or redistributed, in whole or in part, without prior written permission from Amazon Web Services, Inc. Commercial copying, lending, or selling is prohibited.

Errors or corrections? Email us at [darrylo@amazon.com](mailto:darrylo@amazon.com).

---

## Quick Start Overview

### Overview

AWS DataSync is a managed data transfer service that simplifies moving, copying, and synchronizing large amounts of data between on-premises storage systems and AWS storage services. DataSync accelerates and automates data transfer, removing the need to modify applications, develop scripts, or manage infrastructure. DataSync uses a purpose-built protocol to accelerate and secure data transfer at speeds up to 10 times faster than open-source tools.

The AWS DataSync In-cloud Transfer Quick Start and Scheduler supports Network File System (NFS) on Amazon EC2 or Amazon Elastic File System (EFS) to EFS data transfer tasks. This quick start is an AWS CloudFormation template that creates DataSync resources and schedules data transfer tasks from an NFS or EFS source file system to an EFS destination file system. Source and destination file systems can be in the same or different Amazon VPCs or AWS regions.


### Prerequisites

Prior to launching the Quick Start, you must have an existing NFS file system as a source (this could be an EFS file system), and an existing EFS file system as the destination. You will need to provide the DNS name of the source file system and the EFS file system id of the destination file system as parameter values when you launch the CloudFormation stack.

### Use cases

The AWS DataSync In-cloud Transfer Quick Start and Scheduler can be used for a wide variety of use cases to transfer NFS file data from one file system to another. Below are a few examples.

•	Migrate an NFS file system from Amazon EC2 to Amazon EFS within the same AWS region

•	Replicate an NFS file system from Amazon EC2 in one AWS region to an Amazon EFS file system in a different AWS region for disaster recovery.

•	Migrate an Amazon EFS file system from EFS standard (no lifecycle management) to an EFS file system with lifecycle management enabled. File systems with lifecycle management enabled will automatically move files to a lower-cost Infrequent Access storage class (EFS IA) based on a predefined lifecycle policy.

•	Migrate an Amazon EFS file system from one performance mode to another performance mode within the same AWS region.

•	Replicate an Amazon EFS file system from one AWS region to another Amazon EFS file system in a different AWS region for disaster recovery.

### The Environment and architecture

The AWS DataSync In-cloud Transfer Quick Start and Scheduler is an AWS CloudFormation template that must be launched in the AWS region where the source file system resides. A DataSync agent is deployed as an EC2 instance in the source region. This instance must have network access to the source NFS or EFS file system. The agent will self-activate in the destination region and setup DataSync locations in the destination region for the source and destination file systems.  A data transfer task, using the default task configuration settings (e.g. which attributes will transfers, verification task, etc.) is also created in the destination region. For more information on DataSync task configuration settings, please refer to the Working with Tasks section of the [AWS DataSync User Guide.](https://docs.aws.amazon.com/datasync/latest/userguide/working-with-tasks.html) DataSync does not support Amazon CloudFormation so all DataSync resources are created by a script run on the agent EC2 instance. This EC2 instance fulfills three main roles. First, it runs a script once to create DataSync resources in the destination AWS region. Second, it runs a script to start task executions triggered by an AWS System Manager Maintenance Window based on the cron expression schedule setup when launching the CloudFormation stack. And third, it is the DataSync agent transferring files from the source location to the destination location over a TLS 1.2 connection. Please refer to How AWS DataSync Works in the [AWS DataSync User Guide.](https://docs.aws.amazon.com/datasync/latest/userguide/how-datasync-works.html) 

There are many variables involved in determining how fast these transfers will perform. For example, transferring larger files will result in higher throughput while smaller files will yield slower throughput. Performance characteristics of the source or destination EFS file systems, i.e. bursting or provisioned throughput, general purpose or Max I/O performance modes, will also play a role in the overall transfer speed. Please refer to Amazon EFS Performance in the [Amazon EFS User Guide.](https://docs.aws.amazon.com/efs/latest/ug/performance.html) 

![](https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/in-cloud-transfer/AWS_DataSync_Task_Scheduler_Architecture_20190305.png)

### Resources created

AWS DataSync resources can’t be created natively by AWS CloudFormation so a shell script is dynamically generated and executes AWS CLI commands, using CloudFormation parameter values, to create AWS DataSync resources in the destination region. This script is executed from the AWS agent EC2 instance. Below is a list of AWS resources created by the CloudFormation template and the shell script executed on the AWS DataSync agent.

For a detailed description of all the resources created by this template, please refer to the [AWS DataSync In-cloud Transfer Quick Start and Scheduler user guide.](https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/in-cloud-transfer/datasync_in-cloud_transfer_quick_start_scheduler_ug.pdf)

### Launching the Quick Start

To better understand how the CloudFormation template and input parameters map to resources found in the quick start’s architecture, please refer to the [AWS DataSync In-cloud Transfer Quick Start and Scheduler user guide.](https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler_ug.pdf) 

You can optionally launch the CloudFormation template from a command line using a parameter file. Links to these sample scripts are below:

The CloudFormation template.

[https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.yaml](https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.yaml)


A CloudFormation parameter file.

[https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler_parameter_file.json](https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler_parameter_file.json)

Shell script to launch the CloudFormation stack using a local parameter file and template from an Amazon S3 bucket.

[https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.sh](https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.sh)


To launch the Quick Start, click on the link below for the source AWS region and enter the input parameters.



| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=in-cloud-transfer&templateURL=https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.yaml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=in-cloud-transfer&templateURL=https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.yaml) |
| us-west-1 |US West (N. California)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/new?stackName=in-cloud-transfer&templateURL=https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.yaml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=in-cloud-transfer&templateURL=https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.yaml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=in-cloud-transfer&templateURL=https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.yaml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=in-cloud-transfer&templateURL=https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.yaml) |
| ap-northeast-1 |AP (Tokyo)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/new?stackName=in-cloud-transfer&templateURL=https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.yaml) |
| ap-northeast-2 |AP (Seoul)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-northeast-2#/stacks/new?stackName=in-cloud-transfer&templateURL=https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.yaml) |
| ap-southeast-1 |AP (Singapore)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks/new?stackName=in-cloud-transfer&templateURL=https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.yaml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=in-cloud-transfer&templateURL=https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler.yaml) |


## Managing the Quick Start

For a detailed description and examples on how to manage, run, edit, etc. in-cloud transfers, please refer to the [AWS DataSync In-cloud Transfer Quick Start and Scheduler user guide.](https://s3.amazonaws.com/solution-references/datasync/datasync_in-cloud_transfer_quick_start_scheduler_ug.pdf)

## Deleting resources
AWS DataSync resources can’t be created natively with AWS CloudFormation so non-DataSync resources are created as a part of the CloudFormation stack while DataSync resources are created from a shell script on the DataSync agent EC2 instance. If you delete the CloudFormation stack it will only delete the non-DataSync resources and the DataSync resources will remain. In order to clean up all AWS resources created with this quick start, go to the Outputs of the CloudFormation stack in the source AWS region. Copy the value of the ViewDeleteDataSyncResourcesScript key and execute it in a terminal window. The output of this script will return commands that you copy, paste, and run in a terminal window to delete the resources. This will properly delete all DataSync resources, including the agent, source and destination locations (not the actual file systems but the DataSync locations for the file systems), task, and the CloudFormation stack. When the CloudFormation stack is deleted with this script, all non-DataSync resources created by the stack will be deleted as well.

![](https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/in-cloud-transfer/datasync_in-cloud_transfer_quick_start_and_scheduler_delete_output.png)


The output should look something like this.
```sh
aws datasync delete-task --task-arn arn:aws:datasync:us-east-2:123456789012:task/task-0b109d5ee32dc38e0 --region us-east-2
aws datasync delete-location --location-arn arn:aws:datasync:us-east-2:123456789012:location/loc-08111de0de3c27c29 --region us-east-2
aws datasync delete-location --location-arn arn:aws:datasync:us-east-2:123456789012:location/loc-0a742c91224053674 --region us-east-2
aws datasync delete-agent --agent-arn arn:aws:datasync:us-east-2:123456789012:agent/agent-08765fc41d7dd6453 --region us-east-2
aws cloudformation delete-stack --stack-name datasync-task-scheduler-AA9411 --region us-east-1
```

---


## Troubleshooting
For feedback, suggestions, or corrections, please email me at [darrylo@amazon.com](mailto:darrylo@amazon.com).


## License
This sample code is made available under the MIT-0 license. See the LICENSE file.
