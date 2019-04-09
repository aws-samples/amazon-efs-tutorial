![](https://s3.amazonaws.com/aws-us-east-1/tutorial/AWS_logo_PMS_300x180.png)

![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_available.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_ingergration.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_ecryption-lock.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_fully-managed.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_lowcost-affordable.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_performance.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_scalable.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_storage.png)
# **Amazon Elastic File System (Amazon EFS)**

## Scale-out Tutorial

### Version 1.0.2

efs-st-1.0.2

---

© 2018 Amazon Web Services, Inc. and its affiliates. All rights reserved.
This sample code is made available under the MIT-0 license. See the LICENSE file.

Errors or corrections? Email us at [darrylo@amazon.com](mailto:darrylo@amazon.com).

---

## Tutorial Overview

### Overview

This tutorial is designed to help you better understand the distributed data storage design of Amazon Elastic File System (Amazon EFS) and how to best leverage this design by taking advantage of scale-out achitectures.

### Prerequisites

* An AWS account with administrative level access
* An Amazon EC2 key pair
* An Amazon VPC
* An Amazon EFS file system

If a key pair has not been previously created in your account, please refer to [Creating a Key Pair Using Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair) from the AWS EC2 User's Guide.  

Verify that the key pair is created in the same AWS region you will use for the tutorial.

If a VPC and EFS file system have not been previously created, please refer to [Create a File System tutorial](/create-file-system)

### The Environment

The tutorial steps will launch an EC2 Spot Fleet. Please use the recommended default instance type. All instances will automatically mount the Amazon EFS file system and register with EC2 SSM, which will be used to remotely execute a script to transfer objects from Amazon S3 to Amazon EFS in parallel.

Each instance will download all S3 objects from the public dataset https://aws.amazon.com/public-datasets/dc-lidar/.

For more information about this dataset, please refer to http://geospatial.dcgis.dc.gov/templates/dcfinder/s2.html?appid=62c9607bcfb349d5988e39390d50e995

WARNING!! This tutorial environment will exceed your free-usage tier. You will incur charges as a result of building this environment and executing the scripts included in this tutorial. Delete all files on the EFS file system that were created during this tutorial and delete the  stack so you don’t continue to incur additional compute and storage charges.

## Tutorial

### Step 1: Create Amazon EC2 Spot Fleet Role

> This step involves creating an IAM role used to run and launch an Amazon EC2 spot fleet.

**1.1.** Sign in to the [AWS Management Console](https://console.aws.amazon.com/console/home) and navigate to the IAM service page

**1.2.** Select **Roles** on the left frame and select **Create Role**

**1.3.** Select **AWS Service** as the type of trusted entity, **EC2** as the service that will use this role, and **EC2 Spot Fleet Role** as the use case. Select **Next: Permissions**.

**1.4.** Verify the **AmazonEC2SpotFleetRole** policy is listed and select **Next: Review**.

**1.5.** Give the role a name, like **efs-scale-out-tutorial-ec2-spot-fleet-role** and select **Create Role**.

### Step 2: Create Amazon EC2 Role

> This step involves creating an IAM role used by the EC2 instances.

**2.1.** Sign in to the [AWS Management Console](https://console.aws.amazon.com/console/home) and navigate to the IAM service page

**2.2.** Select **Roles** on the left frame and select the **Create Role**.

**2.3.** Select **AWS Service.** as the type of trusted entity, **EC2** as the service that will use this role, and **EC2 Role for Simple Systems Manager** as the use case. Select **Next: Permissions**.

**2.4.** Verify the **AmazonEC2RoleforSSM** policy is listed and select **Next: Review**.

**2.5.** Give the role a name, like **efs-scale-out-tutorial-ec2-instance-role** and select **Create Role**.

**2.6.** Find the role you just created by typing in it's name in the search field **efs-scale-out-tutorial-ec2-instance-role** and click the role's name link.

**2.7.** Select the **+Add inline policy** link on the bottom right side of the window.

**2.8.** Select **Custom Policy** and **Select**.

**2.9.** Give the policy a name, like **efs-scale-out-tutorial-ec2-instance-policy** and paste the policy snippet below into the **Policy Document** field.

```sh
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "ec2:CreateTags",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeNetworkInterfaceAttribute",
                "ec2:DescribeNetworkInterfaces",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSubnets",
                "ec2:DescribeVpcAttribute",
                "ec2:DescribeVpcs",
                "elasticfilesystem:Describe*",
                "kms:ListAliases"
            ],
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
```
**2.10.** Select **Validate Policy** to make sure the policy is valid. Select **Apply Policy**.

### Step 3: Create an Amazon EC2 Spot Request

> This step involves creating an Amazon EC2 Spot request

**3.1.** Sign in to the [AWS Management Console](https://console.aws.amazon.com/console/home) and navigate to the EC2 service page

**3.2.** Select **Spot Instances** on the left frame under **INSTANCES** and select the **Request Spot Instances**

**3.3.** Create a Spot Instance Request using the following settings:

**Select request type** section

- **Request type**: **Request**
- **Target capacity**: Select the size of the fleet (recommend 5)
- **AMI**: Select the latest Amazon Linus AMI (e.g. Amazon Linux AMI 2017.09.0.20170930 x86_64 HVM GP2)
- **Instance type(s)**: **r4.large**
- **Allocation strategy**: **Lowest price**
- **Network**: Select the appropriate VPC
- **Availability Zone**: **Select all availability zones**
- **Maximum price** **Use automated bidding (recommended)**

- Select **Next**

**Configure storage** section

- **Keep all defaults**

- **Set instance details** section

- **Monitoring**: **Enable CloudWatch detailed monitoring**
- **Tenancy**: **Default - run a shared hardware instance**
- **User data**: **As text** paste the cloud_config snippet below into the text box. IMPORTANT!! There is one placeholder in the script below that needs to be updated with the EFS file system id you want to use. In the **runcmd:** section replace ```!!!Add_file_system_id_here!!!``` with the file system id you want to use. **e.g. - filesystem=fs-123456af**

```sh
#cloud-config
repo_update: true
repo_upgrade: all

packages:
- parallel

write_files: 
- content: |
    #!/bin/bash -xe
    FILE_SYSTEM_ID=$1

    # get instance-id, availability-zone, and region from intance metadata
    instanceid=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
    availabilityzone=$(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone)
    region=${availabilityzone:0:-1}

    # wait for file system DNS name to be propagated
    results=1
    while [[ $results != 0 ]]; do
      nslookup $FILE_SYSTEM_ID.efs.$region.amazonaws.com
      results=$?
      if [[ results = 1 ]]; then
        sleep 30
      fi
    done

    cd /$FILE_SYSTEM_ID/lidar/${instanceid}

    # create threads variable
    threads=$(($(nproc --all) * 8))

    aws ec2 create-tags --resources ${instanceid} --tags "Key=Name,Value=Scale-out Tutorial parallel s3 cp (waiting)" --region ${region}
    
    # create a list of objecst to get
    aws s3api list-objects --bucket dc-lidar-2015 --output text --query 'Contents[].{Key:Key}|[?contains(Key, `.las`) == `true`]|[?contains(Key, `.lasx`) == `false`]' | awk '{print "s3://dc-lidar-2015/" $1}' > /tmp/$FILE_SYSTEM_ID-dc-lidar-2015.txt

    aws ec2 create-tags --resources ${instanceid} --tags "Key=Name,Value=Scale-out Tutorial parallel s3 cp (running)" --region ${region}

    # get the s3 objects in parallel
    sudo cat /tmp/$FILE_SYSTEM_ID-dc-lidar-2015.txt | parallel --will-cite -j ${threads} 'aws s3 cp {} .; grep -o ".\{8\}$"'

    aws ec2 create-tags --resources ${instanceid} --tags "Key=Name,Value=Scale-out Tutorial parallel s3 cp (complete)" --region ${region}

  path: /tmp/scale-out-tutorial-get-lidar-data.sh
  permissions: 0777

runcmd:
# set variables
- instanceid=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
- availabilityzone=$(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone)
- region=${availabilityzone:0:-1}
- filesystem=!!!Add_file_system_id_here!!!

# installs
- yum --enablerepo=epel install nload -y

# make directories and mount file system
- mkdir -p /${filesystem}
- chown ec2-user:ec2-user /${filesystem}
- echo "${filesystem}.efs.${region}.amazonaws.com:/ /${filesystem} nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 0 0" | sudo tee /etc/fstab
- mount -a -t nfs4
- mkdir -p /${filesystem}/lidar/${instanceid}

# tag the instance
- aws ec2 create-tags --resources ${instanceid} --tags "Key=Name,Value=Scale-out Tutorial" --region ${region}
```

- **Instance tags**: Add the following Kay:Value pair
- **Key**: **Name**
- **Value**: **Scale-out Tutorial**

**Set keypair and role** section

- **Key pair name**: Select an existing EC2 key pair
- **IAM instance profile**: Select the EC2 instance role you created in **Step 2** above. (e.g. **efs-scale-out-tutorial-ec2-instance-role**)

**Manage firewall rules** section

- **Security groups**: Select a security group that has NFS (TCP 2049) inbound access to the file system's mount targets

- **auto-assign IPv4 Public IP**: **Use subnet setting (Enable)**

- Accept the defaults for the remaining settings on this page.

- Select **Review**

- Review all the settings.

- Select **Launch**

It will take a few minutes for the Amazon EC2 Spot request to be accepted and fulfilled. Before continuing, wait for all EC2 instances requested to show up in the EC2 console with the key:value pair **Name** : **Scale-out Tutorial**.

### Step 4: Use SSM to remotely start file transfer from Amazon S3 to Amazon EFS

> This step involves remotely running a script using SSM to start the transfer from S3 to EFS.

**4.1.** Sign in to the [AWS Management Console](https://console.aws.amazon.com/console/home) and navigate to the EC2 service page

**4.2.** Select **Managed Instances** on the left frame under **SYSTEMS MANAGER SHARED RESOURCES** and select the **Request Spot Instances**

**4.3.** Verify all instances in the spot fleet are displayed. Instances should have the key:value pair **Name** : **Scale-out Tutorial**.

**4.4.** Select **Run a command**

**4.5.** Create a command using the following settings:

- Select **AWS-RunShellScript** as the **Command document**.

- Select **Specifying a Tag** as the **Select Targets by** option and select the key:value pair **Name** : **Scale-out Tutorial**.

- Paste the command below into the **Commands** text box, replacing  ```!!!Add_file_system_id_here!!!``` with the your EFS file system id. (e.g. /tmp/scale-out-tutorial-get-lidar-data.sh fs-123456af)

```sh
/tmp/scale-out-tutorial-get-lidar-data.sh !!!Add_file_system_id_here!!!
```

- Select **Run**

### Step 5: Monitor the transfer

**5.1.** Verify the script is executing on each EC2 instance
Once the script has been successfully executed, the Name tag of each instance will change from **Scale-out Tutorial** to **Scale-out Tutorial  parallel s3 cp (running)**.

**5.2.** Monitor the total throughput to the EFS file system using CloudWatch
Select the CloudWatch dashboard for your EFS file system that was created in the **Create file system** tutorial.

**5.3.** Monitor for 10 minutes
Monitor the CloudWatch metrics for 10 minutes or so, looking at the **Throughput** & **IOPS** widgets. Optionally, you can SSH into one or more of the EC2 instances and monitor the throughput of that instance in realtime by running the script below on the instance.

```sh
nload -u M
```

### Step 6: Terminate the EC2 Spot Request

**6.1.** Sign in to the [AWS Management Console](https://console.aws.amazon.com/console/home) and navigate to the EC2 service page

**6.2.** Select **Spot Requests** on the left frame under **INSTANCES** and select the checkbox next to the request id you just created.

**6.3.** Select **Actions** then **Cancel Spot request**. Confirm the **Terminate instances** checkbox is checked and select **Confirm**.


### Sample Results

Click on the image to watch a screen capture*

[![video](/images/efs_scale_out_demo_results.png)](https://s3.amazonaws.com/aws-us-east-1/tutorial/create-efs-resources/efs_scale_out_demo_results.mp4)

*this was achieved using a file system with a permitted throughput greater than 3 GB/s


## Conclusion
The distributed data storage design of Amazon EFS enables high levels of availability, durability, and scalability. This distributed architecture results in a small latency overhead for each file operation. Due to this per-operation latency, overall throughput generally increases as the average I/O size increases, because the overhead is amortized over a larger amount of data. Amazon EFS supports highly parallelized workloads (for example, using concurrent operations from multiple threads and multiple Amazon EC2 instances), which enables high levels of aggregate throughput and operations per second.

---

### Bonus

#### Having issues? - Want to save time?

### Launch the AWS CloudFormation Stack

This AWS CloudFormation stack will automatically create the environment created in Steps 1 - 3 above. Steps 4 - 5 are also scripted out below and you can run those in a terminal session. You will still need to complete Step 6 to delete the environment when you're finished with the tutorial.

Click the  ![cloudformation-launch-stack](/images/deploy_to_aws.png) link below to create the AWS CloudFormation stack in your account and desired AWS region. This region must an existing Amazon EFS file system which you will use with this tutorial.

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=efs-scale-out-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-scale-out-tutorial-cfn-template-20171110c.yml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=efs-scale-out-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-scale-out-tutorial-cfn-template-20171110c.yml) |
| us-west-1 |US West (N. California)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/new?stackName=efs-scale-out-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-scale-out-tutorial-cfn-template-20171110c.yml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=efs-scale-out-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-scale-out-tutorial-cfn-template-20171110c.yml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=efs-scale-out-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-scale-out-tutorial-cfn-template-20171110c.yml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=efs-scale-out-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-scale-out-tutorial-cfn-template-20171110c.yml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=efs-scale-out-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/tutorial/efs-scale-out-tutorial-cfn-template-20171110c.yml) |

After launching the AWS CloudFormation Stack above, you should see the Amazon EC2 instances running in your VPC.  Wait for the **Name** tag of each instance to read "Scale-out Tutorial" before continuing.

## Verify all instances have registered with SSM
Run this command to verify all EC2 instances in the spot fleet have registered with SSM. You should see one entry per instance.
```sh
aws ssm describe-instance-information --query "InstanceInformationList[*]" --output json
```

## Remotely execute script to download objects from S3 to Amazon EFS
Run this command to remotely execute the scale-out-tutorial-get-lidar-data.sh script which will download all objects from the DC-LiDAR public dataset to the Amazon EFS file system specified in the CloudFormation template parameters.
```sh
aws ssm send-command --targets "Key=tag:Name,Values=Scale-out Tutorial" --document-name "AWS-RunShellScript" --comment "Scale-out Tutorial" --parameters commands=/tmp/scale-out-tutorial-get-lidar-data.sh --output text --query "Command.CommandId"
```

## Verify the script is executing on each EC2 instance
Once the script has been successfully executed, the Name tag of each instance will change from 'Scale-out Tutorial' to 'Scale-out Tutorial  parallel s3 cp (running)'

## Delete the CloudFormation stack
Delete the cloud formation stack to terminate all EC2 instances in the fleet.

## Troubleshooting
For feedback, suggestions, or corrections, please email me at [darrylo@amazon.com](mailto:darrylo@amazon.com).

## License
This sample code is made available under the MIT-0 license. See the LICENSE file.
