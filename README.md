# 🚀 Automated Cost Optimizer (AWS)

## 📌 Project Overview

The **Automated Cost Optimizer** is an AWS-based serverless project designed to reduce unnecessary cloud costs by automatically identifying and stopping idle EC2 instances.

This project uses AWS services like Lambda, EventBridge (CloudWatch Rules), and EC2 to monitor resource usage and take automated actions.

---

## 🎯 Objective

* Reduce AWS billing by stopping unused EC2 instances
* Automate monitoring and cost optimization
* Implement serverless architecture using AWS Lambda

---

## 🧰 AWS Services Used

* AWS Lambda
* Amazon EC2
* Amazon EventBridge (CloudWatch Rules)
* Amazon CloudWatch

---

## 🏗️ Architecture Workflow

1. EventBridge triggers Lambda function every 5 minutes
2. Lambda checks EC2 instances status
3. Fetches CPU utilization from CloudWatch
4. If CPU usage is below threshold → instance is stopped
5. Logs are stored in CloudWatch

---

## ⚙️ Implementation Steps

### Step 1: Launch EC2 Instance

* Created an EC2 instance (t2.micro)
* Instance kept idle for testing

---

### Step 2: Create IAM Role

* Role created for Lambda
* Permissions:

  * AmazonEC2FullAccess
  * CloudWatchFullAccess

---

### Step 3: Create Lambda Function

* Runtime: Python
* Function checks CPU utilization
* Stops EC2 instance if idle

---

### Step 4: Add Automation Trigger

* EventBridge rule created
* Schedule: rate(5 minutes)
* Target: Lambda function

---

### Step 5: Monitoring

* Logs stored in CloudWatch
* Verified execution and automation

---

## 💻 Lambda Code

```python
import boto3
from datetime import datetime, timedelta

ec2 = boto3.client('ec2')
cloudwatch = boto3.client('cloudwatch')

def lambda_handler(event, context):
    instances = ec2.describe_instances()

    for reservation in instances['Reservations']:
        for instance in reservation['Instances']:
            instance_id = instance['InstanceId']
            state = instance['State']['Name']

            if state == 'running':
                cpu = get_cpu_utilization(instance_id)
                print(f"Instance {instance_id} CPU: {cpu}")

                if cpu < 20:
                    print(f"Stopping instance: {instance_id}")
                    ec2.stop_instances(InstanceIds=[instance_id])

def get_cpu_utilization(instance_id):
    metrics = cloudwatch.get_metric_statistics(
        Namespace='AWS/EC2',
        MetricName='CPUUtilization',
        Dimensions=[{'Name': 'InstanceId', 'Value': instance_id}],
        StartTime=datetime.utcnow() - timedelta(minutes=10),
        EndTime=datetime.utcnow(),
        Period=300,
        Statistics=['Average']
    )

    if metrics['Datapoints']:
        return metrics['Datapoints'][0]['Average']
    return 0
```

---

## ✅ Final Output

The EC2 instance is automatically stopped when CPU utilization is low.

![Final Output](images/final-output.png)

---

## 📊 Key Features

* Fully automated cost optimization
* Serverless architecture
* Real-time monitoring
* Scalable and efficient

---

## 🚀 Future Enhancements

* Auto-start instances during working hours
* SNS alerts for notifications
* Tag-based instance filtering
* Multi-region support

---

## 📌 Conclusion

This project demonstrates how cloud automation can significantly reduce operational costs using AWS services. It showcases practical implementation of serverless computing and monitoring.

---

## 👩‍💻 Author

Disha
