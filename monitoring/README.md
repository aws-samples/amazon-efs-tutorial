![](https://s3.amazonaws.com/aws-us-east-1/tutorial/AWS_logo_PMS_300x180.png)

![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_available.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_ingergration.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_ecryption-lock.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_fully-managed.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_lowcost-affordable.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_performance.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_scalable.png)![](https://s3.amazonaws.com/aws-us-east-1/tutorial/100x100_benefit_storage.png)
# **Amazon Elastic File System (Amazon EFS)**

## Monitoring Tutorial

### Version 1.0.1

efs-mt-1.0.1

---

© 2018 Amazon Web Services, Inc. and its affiliates. All rights reserved.
This sample code is made available under the MIT-0 license. See the LICENSE file.

Errors or corrections? Email us at [darrylo@amazon.com](mailto:darrylo@amazon.com).

---

## Tutorial Overview

### Overview

This tutorial is designed to help you better understand how to use Amazon CloudWatch to monitor the performance of your Amazon EFS file systems.

### Prerequisites

* An AWS account
* An Amazon EFS file system

### Amazon CloudWatch Metric Math & Dashboards

The best way to understand the workload your file system serves is by monitoring the metrics Amazon CloudWatch collects and processes. By monitoring the EFS CloudWatch metrics TotalIOBytes, DataWriteIOBytes, DataReadIOBytes, and MetaDataIOBytes in the CloudWatch console, you can see in near real-time your file system performance. These metrics are sent to CloudWatch at 1-minute intervals and are available for the next 15 months, so you can access historical information about the workload that has run on your file system over time.

Metric Math, a feature within Amazon CloudWatch, makes it easy to perform math analytics on your metrics to derive additional insights into the health and performance of your AWS resources and applications. In this case your Amazon EFS file system. 

Amazon CloudWatch dashboards are customizable home pages in the CloudWatch console that you can use to monitor your resources in a single view. You can use CloudWatch dashboards to create customized views of the metrics and alarms for your AWS resources. You can have up to 500 dashboards in your AWS account. All dashboards are global, not region-specific.

![](/images/cw_dashboard_mm_efs_03.png)

Below are some useful metric math expressions for EFS file systems.

#### Throughput (MiB/s)

To calculate the average throughput (MiB/s) for a period, divide the respective IOBytes sum statistic (Total, DataRead, DataWrite, Metadata) by the number of seconds in the period.

Logic: ( sum of …IOBytes ÷ 1048576(to convert to MiB) ) ÷ seconds in the period

| CloudWatch Attribute | Id | Details | Statistic | Period |
| --- | --- | --- | --- | ---
| Metric math expression | e1 | (m1/1048576)/PERIOD(m1) | | |
| Metric | m1 | …IOBytes | Sum | 1 Minute |

#### IOPS

To calculate the average operations per second (iops) for a period, divide the respective IOBytes sample count statistic (Total, DataRead, DataWrite, Metadata) by the number of seconds in the period.

Logic: ( sample count of …IOBytes ) ÷ seconds in the period

| CloudWatch Attribute | Id | Details | Statistic | Period |
| --- | --- | --- | --- | ---
| Metric math expression | e1 | m1/PERIOD(m1) | | |
| Metric | m1 | …IOBytes | Sample count | 1 Minute |

#### Percent Throughput (%)

To calculate the percent throughput (%) of the different IO types (DataRead, DataWrite, Metadata) for a period, multiple the respective IOBytes sum statistic by 100 and divide by the sum statistic of TotalIOBytes for the same period.

Logic: ( sum of …IOBytes x 100(to convert to %) ) ÷ sum of TotalIOBytes

| CloudWatch Attribute | Id | Details | Statistic | Period |
| --- | --- | --- | --- | ---
| Metric math expression | e1 | (m2*100)/m1 | | |
| Metric | m1 | TotalIOBytes | Sum | 1 Minute |
| Metric | m2 | …IOBytes | Sum | 1 Minute |

#### Percent IOPS (%)

To calculate the percent operations per second (%) of the different IO types (DataRead, DataWrite, Metadata) for a period, multiple the respective IOBytes sample count statistic by 100 and divide by the sample count statistic of TotalIOBytes for the same period.

Logic: ( sample count of …IOBytes x 100(to convert to %) ) ÷ sample count of TotalIOBytes

| CloudWatch Attribute | Id | Details | Statistic | Period |
| --- | --- | --- | --- | ---
| Metric math expression | e1 | (m2*100)/m1 | | |
| Metric | m1 | TotalIOBytes | Sample count | 1 Minute |
| Metric | m2 | …IOBytes | Sample count | 1 Minute |

#### Throughput Utilization

To calculate the percent throughput utilization for a time period, first calculate the average throughput (in MiB/second) for a time period by dividing the sum statistic of TotalIOBytes by 1048576 (to convert it to MiB) and divide by the number of seconds in the period. Then take that result and multiply by 100, divide by the sum statistic of PermittedThroughput multiplied by 1048576 (to convert to it MiB).

Logic: ((( sum of TotalIOBytes ÷ 1048576 (to convert to MiB) ) ÷ seconds in the period ) x 100 ) ÷ ( sum of PermittedThroughput ÷ seconds in the period )

| CloudWatch Attribute | Id | Details | Statistic | Period |
| --- | --- | --- | --- | ---
| Metric math expression | e1 | (m1/1048576)/PERIOD(m1) | | |
| Metric math expression | e2 | m2/1048576 | | |
| Metric math expression | e3 | e2-e1 | | |
| Metric math expression | e4 | ((e1)*100)/(e2) | | |
| Metric | m1 | TotalIOBytes | Sum | 1 Minute |
| Metric | m2 | PermittedThroughput | Sum | 1 Minute |

#### IOPS Utilization

No metric math is needed to calculate the percent of IOPS utilization for General Purpose performance mode file systems. This metric is available as PercentIOLimit. This metric is not available for Max I/O performance mode file systems.

| CloudWatch Attribute | Id | Details | Statistic | Period |
| --- | --- | --- | --- | ---
| Metric | m1 | PercentIOLimit | Average | 1 Minute |

#### Average IO Size (KiB)

To calculate the average IO size (KiB) for a period, divide the respective IOBytes sum statistic (DataRead, DataWrite, Metadata) by the same IOBytes sample count statistic.

Logic: ( sum of …IOBytes ÷ 1024(to convert to KiB) ) ÷ ( sample count of …IOBytes )

| CloudWatch Attribute | Id | Details | Statistic | Period |
| --- | --- | --- | --- | ---
| Metric math expression | e1 | (m1/1024)/m2 | | |
| Metric | m1 | …IOBytes | Sum | 1 Minute |
| Metric | m2 | …IOBytes | Sample count | 1 Minute |

For more information about Amazon CloudWatch, please refer to https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html

### Launch the AWS CloudFormation Stack

Click the  ![cloudformation-launch-stack](/images/deploy_to_aws.png) link below to create the AWS CloudFormation stack in your account and desired AWS region. You must also have an Amazon EFS file system in this region to use with this tutorial. It creates an Amazon CloudWatch dashboard with widgets displaying file system performance metrics (throughput, IOPS, etc.) using metric math expressions. The metric math expressions described above are included in this [template](/monitoring/templates/cw_dashboard_with_mm_for_efs.yaml). An AWS Lambda function is also created and scheduled to run every minute which gets the metered size of the file system, from the describe-file-system command, and adds the value as a custom metric to CloudWatch. This custom metric is added to the ***File system metrics*** widget.

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=efs-monitoring-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/monitoring/templates/cw_dashboard_with_mm_for_efs.yaml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=efs-monitoring-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/monitoring/templates/cw_dashboard_with_mm_for_efs.yaml) |
| us-west-1 |US West (N. California)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/new?stackName=efs-monitoring-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/monitoring/templates/cw_dashboard_with_mm_for_efs.yaml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=efs-monitoring-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/monitoring/templates/cw_dashboard_with_mm_for_efs.yaml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=efs-monitoring-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/monitoring/templates/cw_dashboard_with_mm_for_efs.yaml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=efs-monitoring-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/monitoring/templates/cw_dashboard_with_mm_for_efs.yaml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=efs-monitoring-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/monitoring/templates/cw_dashboard_with_mm_for_efs.yaml) |
| ap-northeast-1 |AP (Tokyo)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/new?stackName=efs-monitoring-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/monitoring/templates/cw_dashboard_with_mm_for_efs.yaml) |
| ap-northeast-2 |AP (Seoul)| [![cloudformation-launch-stack](/images/deploy_to_aws.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-northeast-2#/stacks/new?stackName=efs-monitoring-tutorial&templateURL=https://s3.amazonaws.com/aws-us-east-1/amazon-efs-tutorial/monitoring/templates/cw_dashboard_with_mm_for_efs.yaml) |

After launching the AWS CloudFormation Stack above, you should see a new dashboard in your Amazon CloudWatch console using the naming convention ***aws-region***_***efs-filesystem-id***_mm.

![](/images/cw_dashboard_mm_efs_03.png)

## Troubleshooting
For feedback, suggestions, or corrections, please email me at [darrylo@amazon.com](mailto:darrylo@amazon.com).

## License
This sample code is made available under the MIT-0 license. See the LICENSE file.
