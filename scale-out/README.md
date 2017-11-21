![](https://s3.amazonaws.com/aws-us-east-1/tutorial/AWS_logo_PMS_300x180.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_available.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_ingergration.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_ecryption-lock.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_fully-managed.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_lowcost-affordable.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_performance.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_scalable.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_storage.png)
# **Amazon Elastic File System (Amazon EFS)**

## Scale-out Tutorial

### Version 1.0.1

efs-st-1.0.1

---

© 2017 Amazon Web Services, Inc. and its affiliates. All rights reserved. This work may not be  reproduced or redistributed, in whole or in part, without prior written permission from Amazon Web Services, Inc. Commercial copying, lending, or selling is prohibited.

Errors or corrections? Email us at [darrylo@amazon.com](mailto:darrylo@amazon.com).

---

## Tutorial Overview

### Overview

This tutorial is designed to help you better understand the distributed data storage design of Amazon Elastic File System (Amazon EFS) and how to best leverage this design by taking advantage of scale-out achitectures.

### Prerequisites

The AWS CloudFormation template below will create the compute environment you need to run the tutorial. You must have an existing Amazon EFS file system in the region where you launch the CloudFormation stack and it must have mount targets in the VPC where you launch your EC2 instances. You will need to provide the EFS file system id as a parameter value when you launch the CloudFormation stack.

### The Environment

The AWS CloudFormation template will launch an EC2 Spot Fleet. Please use the recommended default instance type. The file system, whose id you entered as a CloudFormation parameter, will be automatically mounted to each EC2 instance. All instances will automatically register with EC2 SSM which will be used to remotely execute a script to download objects from Amazon S3.

Each instance will download all S3 objects from the public dataset https://aws.amazon.com/public-datasets/dc-lidar/.

For more information about this dataset, please refer to http://geospatial.dcgis.dc.gov/templates/dcfinder/s2.html?appid=62c9607bcfb349d5988e39390d50e995

WARNING!! This tutorial environment will exceed your free-usage tier. You will incur charges as a result of launching this CloudFormation stack and executing the scripts included in this tutorial. Delete all files on the EFS file system that were created during this tutorial and delete the CloudFormation stack so you don’t continue to incur additional compute and storage charges.

### Launch the AWS CloudFormation Stack

Click the  ![cloudformation-launch-stack](https://s3.amazonaws.com/aws-us-east-1/tutorial/deploy_to_aws_20171004_v2.png) link below to create the AWS CloudFormation stack in your account and desired AWS region. This region must an existing Amazon EFS file system which you will use with this tutorial.

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](https://s3.amazonaws.com/aws-us-east-1/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=efs-scale-out-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-scale-out-tutorial-cfn-template-20171110c.yml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](https://s3.amazonaws.com/aws-us-east-1/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=efs-scale-out-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-scale-out-tutorial-cfn-template-20171110c.yml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](https://s3.amazonaws.com/aws-us-east-1/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=efs-scale-out-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-scale-out-tutorial-cfn-template-20171110c.yml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](https://s3.amazonaws.com/aws-us-east-1/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=efs-scale-out-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-scale-out-tutorial-cfn-template-20171110c.yml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](https://s3.amazonaws.com/aws-us-east-1/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=efs-scale-out-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-scale-out-tutorial-cfn-template-20171110c.yml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](https://s3.amazonaws.com/aws-us-east-1/tutorial/deploy_to_aws_20171004_v2.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=efs-scale-out-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-scale-out-tutorial-cfn-template-20171110c.yml) |

After launching the AWS CloudFormation Stack above, you should see the Amazon EC2 instances running in your VPC.  Wait for the **Name** tag of each instance to read "Scale-out Tutorial" before continuing.

## Verify all instances have registered with SSM
Run this command to verify all EC2 instances in the spot fleet have registered with SSM. You should see one entry per instance.
```sh
aws ssm describe-instance-information --query "InstanceInformationList[*]" --output json
```

## Remotely execute script to download objects from S3 to Amazon EFS
Run this command to remotely execute the scale-out-tutorial-get-lidar-data.sh script which will download all objects from the DC-LiDAR public dataset to the Amazon EFS file system specified in the CloudFormation template parameters.
```sh
sh_command_id=$(aws ssm send-command --targets "Key=tag:Name,Values=Scale-out Tutorial" --document-name "AWS-RunShellScript" --comment "Scale-out Tutorial" --parameters commands=/tmp/scale-out-tutorial-get-lidar-data.sh --output text --query "Command.CommandId")
```

## Verify the script is executing on each EC2 instance
Once the script has been successfully executed, the Name tag of each instance will change from 'Scale-out Tutorial' to 'Scale-out Tutorial  parallel s3 cp (running)'

## Monitor the total throughput to the EFS file system using CloudWatch
Select the File System
Select the TotalIOBytes metric
Change the Statistic to Sum
Change the Period to 1 Minute
To calculate the throughput, take the sum of all bytes within a period (1 minute) and divide it by the number of seconds in that period (e.g. 60 seconds).

## Delete the CloudFormation stack
Delete the cloud formation stack to terminate all EC2 instances in the fleet.

For feedback, suggestions, or corrections, please email me at [darrylo@amazon.com](mailto:darrylo@amazon.com).
