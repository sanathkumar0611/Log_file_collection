# AWS Event-Driven Log Processing Automation

## Project Overview

This project demonstrates a **serverless event-driven log processing system** using AWS.
Whenever a log file is uploaded to an Amazon S3 bucket, an AWS Lambda function is automatically triggered. The Lambda function reads the log file, **extracts all ERROR messages**, and stores those error details in another S3 bucket.

This helps engineers quickly identify the **exact errors occurring in the application** without manually reading the entire log file.

---

## Architecture

```
Upload log file
      ↓
Amazon S3 (Input Bucket)
      ↓
S3 Event Notification
      ↓
AWS Lambda Function
      ↓
Extract ERROR Messages
      ↓
Amazon S3 (Output Bucket)
```

---

## AWS Services Used

| Service           | Purpose                                      |
| ----------------- | -------------------------------------------- |
| Amazon S3         | Stores input log files and processed output  |
| AWS Lambda        | Reads and processes log files                |
| Amazon CloudWatch | Stores Lambda execution logs                 |
| AWS IAM           | Provides permissions for Lambda to access S3 |

---

## Use Case

Applications generate large log files containing information, warnings, and errors.
Manually checking these logs is time-consuming.

This automation helps by:

* Automatically detecting uploaded log files
* Extracting **actual error messages**
* Storing error details for quick troubleshooting
* Reducing manual log analysis

Example use cases:

* Application failure monitoring
* DevOps troubleshooting
* Automated log analysis
* Production incident investigation

---

## Example Log File (app.log)

```
INFO Server started successfully
INFO User login successful
ERROR Database connection failed
INFO API request received
ERROR Timeout while connecting to service
INFO Request completed
```

---

## Example Output File (error-details.txt)

```
ERROR Database connection failed
ERROR Timeout while connecting to service
```

This output shows the **actual error messages** detected in the log file.

---

## Lambda Function Logic

The Lambda function performs the following steps:

1. Detect the uploaded file from the S3 event.
2. Read the log file content.
3. Identify all lines containing **ERROR**.
4. Extract those error messages.
5. Store the extracted errors in another S3 bucket.

---

## Sample Lambda Code (Python)

```python
import boto3

s3 = boto3.client('s3')

def lambda_handler(event, context):

    bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']

    response = s3.get_object(Bucket=bucket, Key=key)
    content = response['Body'].read().decode('utf-8')

    lines = content.splitlines()

    error_lines = [line for line in lines if "ERROR" in line]

    result = "\n".join(error_lines)

    output_bucket = "logoutputbucket112"
    output_key = "error-details.txt"

    s3.put_object(
        Bucket=output_bucket,
        Key=output_key,
        Body=result
    )

    return result
```

---

## Setup Steps

1. Create two S3 buckets

   * Input bucket for uploading logs
   * Output bucket for storing extracted errors

2. Create a Lambda function

   * Runtime: Python
   * Grant IAM permissions to access S3

3. Configure S3 Event Notification

   * Trigger Lambda when a log file is uploaded

4. Upload a log file to the input bucket.

5. Verify the extracted error messages in the output bucket.

---

## Benefits of This Architecture

* Serverless (no infrastructure management)
* Event-driven automation
* Quickly identify real application errors
* Automatically scales with incoming logs
* Reduces manual troubleshooting effort

---

## Future Improvements

Possible enhancements:

* Send alerts using Amazon SNS when errors are detected
* Store error logs in DynamoDB for analytics
* Build monitoring dashboards
* Integrate with DevOps alerting tools

---

## Conclusion

This project demonstrates how AWS serverless services can be used to **automatically analyze application logs and extract error messages**, helping DevOps teams quickly identify and troubleshoot system issues.
