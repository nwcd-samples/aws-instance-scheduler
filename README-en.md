# AWS Instance Scheduler

## Solution Overview
https://aws.amazon.com/solutions/instance-scheduler/

Amazon Web Services (AWS) offers cloud resource on demand so that customers can control their resource capacity and pay as you go. One approach to reduce costs is to stop resources that are not in use, and start those resources when their capacity is needed.

The AWS Instance Scheduler is a solution that enables customers to easily configure custom start and stop schedules and automates the starting (when resources capacity is needed) and stopping
(resources that are not in use) of Amazon Elastic Compute Cloud (Amazon EC2) and Amazon Relational Database Service (Amazon RDS) instances.

The Instance Scheduler leverages AWS resource tags and AWS Lambda function to automatically stop and restart instances across multiple AWS Regions and accounts on a customer-defined schedule which stored in Amazon DynamoDB. An Amazon CloudWatch event triggers an AWS Lambda function that checks the current state of each appropriately tagged instance and execute the action based on schedule.

The Lambda function also records the name of the schedule, the instances associated with that schedule, and the status of instances in Amazon DynamoDB.

## The solution architecture
![](resource/images/instance-scheduler-architecture.png)

Scheduler for Cross-Account and Cross-Region scheduling for EC2 and RDS instances
This repo is forked from https://github.com/awslabs/aws-instance-scheduler and made update for china region based on v1.3.0 version. The contents including:
1. Generate the AWS Instance Scheduler Cloudformation template
2. Package the Lambda function code of AWS Instance Scheduler
3. Upload the artifacts to user specified S3 bucket.

## Setup
Generate Cloudformation template via makefile

1. You can git clone this repo 

2. Run make command, you can specify your {s3_bucket} and {region} 

3. Make sure the aws cli, pip, zip command and pytz liberary have been installed on your machine

4. The setup commands have been verified on Amazon Linux, Ubuntu, MacOS

```bash
# git clone
git clone https://code.awsrun.com/csdc/aws-instance-scheduler.git
cd aws-instance-scheduler/source/code/

# Install pytz
pip install pytz
pytz_location=$(pip show pytz | grep Location | cut -d':' -f 2 | tr -d " ")
cp -r ${pytz_location}/pytz .

# Build
## define variable or specify the value of bucket, solution, version, region
export bucket=YOUR_S3_BUCKET //This bucket must unique 该S3桶必须唯一
export solution=THE_SOLUTION_NAMING
export version=THE_VERSION
export region=THE_REGION_OF_S3_BUCKET_LOCATED
make bucket=${bucket} solution=${solution} version=${version} region=${region}
## for example: make bucket=solutions-scheduler solution=aws-instance-scheduler version=v1.3.0 region=cn-northwest-1

# set s3 bucket PublicAccessBlock configuration, make sure you use upgrade your aws cli > 1.18
aws s3api put-public-access-block \
    --bucket ${bucket} \
    --public-access-block-configuration "BlockPublicAcls=false,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true" --region ${region}

# Deploy
make deploy bucket=${bucket} solution=${solution} version=${version} region=${region}
# for example: make deploy bucket=solutions-scheduler solution=aws-instance-scheduler version=v1.3.0 region=cn-northwest-1

# Delete pytz
rm -r pytz
```

## What's result the build and deploy successfully excuted? 
- A new S3 bucket ${bucket}-${region} will be automatically created if it is not existed
- The resources will be automatically uploaded to s3://${bucket}-${region}/${solution}/${version}/
- The S3 bucket public access block policy as: BlockPublicAcls=false,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true

## Refer below document to create cloudformation stack and execute demo testing 

[How to deploy cloudformation and execute demo testing](Testing.md)


***

Copyright 2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.

Licensed under the Apache License Version 2.0 (the "License"). You may not use this file except in compliance with the License. A copy of the License is located at

    http://www.apache.org/licenses/

or in the "license" file accompanying this file. This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, express or implied. See the License for the specific language governing permissions and limitations under the License.