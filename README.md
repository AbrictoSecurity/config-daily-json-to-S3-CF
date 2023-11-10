
# AWS Config Daily Reporter To JSON
 
The Config Daily Reporter solution can be used in order to generate a daily JSON report.
The report will include new or changed resources, gathering all the information about the resources to be processed through the Aardwolf Platform.
The reporter is triggered using a CloudWatch event, that will trigger a Lambda function. The Lambda will use S3 to create a file.


### Prerequisites
Before getting started, make sure that you have a basic understating of the following:
* Amazon EventBridge rule that runs on a schedule
* Multi-Account Multi-Region Data Aggregation
* AWS Lambda Function
* Python and Boto3.
* CDK environments.  

You will also need to have a pre-configured Multi-Account AWS Config Aggregator and Amazon S3.


### Architecture
1. Amazon CloudWatch event - will trigger Lambda every day
2. AWS Lambda - will run Python3 code which includes an AWS Config Query and update a json file in S3 Bucket.
3. AWS Config - aggregator which will get a query from the Lambda function.
4. AWS S3 - will store the json data to be processed.


### Getting Started


1. ```git clone https://github.com/AbrictoSecurity/config-daily-json-to-S3.git```
2. ```cd config-daily-json-to-S3/cdk```
3. ```cdk bootstrap```
4. ```cdk deploy --parameters aggregator=<aggregator name>  --parameters BUCKET_NAME=<name of bucket to be stored>  --parameters HOUR=<time in UTC (hour)>  --parameters MINUTE=< time in UTC (minute)> --profile <profile of SSO> ```  

    Replace the parameters as follows:
    * aggregator - Name of AWS Config Aggregator.
    * BUCKET_NAME - Name of the Bucket to be deployed.
    * HOUR - The hour (UTC) the Lambda will run.
    * MINUTE - The minute (UTC) the Lambda will run.
    
5. The deployment will generate a report.
6. Check S3 Bucket.
7. Configure cross account read access. 
  
   
* Create an IAM role in account to access bucket. (Within the reading organization account)
```
{
  "Version": "2012-10-17",
   "Statement": [
     {
      "Effect": "Allow",
       "Action": [
       "s3:GetObject",
       "s3:GetObjectVersion"
       ],
       "Resource": "arn:aws:s3:::<BUCKET>/*"

      }
    ]
} 
```

 * Configure Bucket policy. (Within the writing organization account)
   
 ```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::<ReaderID>:role/<ROLENAME>"
            },
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": "arn:aws:s3:::<BUCKETNAME>/*"
        }
    ]
} 
```

## License
This library is licensed under the MIT-0 License. See the LICENSE file.

