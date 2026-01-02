# Amazon CloudWatch – Complete Deep Dive README

---

# 📘 1. What is Amazon CloudWatch?

Amazon CloudWatch is a **monitoring and observability service** that collects:

* Metrics
* Logs
* Events
* Alarms
* Application performance insights

It is used for:

* Monitoring AWS resources
* Setting alerts
* Troubleshooting performance
* Creating dashboards
* Triggering automated actions (Lambda, SNS, OpsCenter)

---

# 📂 2. CloudWatch Namespaces

A **namespace** groups related CloudWatch metrics.

Examples:

* `AWS/EC2`
* `AWS/Lambda`
* `AWS/RDS`
* `AWS/ApplicationELB`
* `Custom/MyApplication`

Each service publishes metrics to its own namespace.

For custom metrics, you **define your own namespace**.

Example:

```bash
aws cloudwatch put-metric-data \
  --namespace "Custom/App" \
  --metric-data file://metrics.json
```

---

# 📊 3. CloudWatch Dashboards

CloudWatch Dashboards allow you to create visual monitoring panels.

### Features:

* Global view for multiple regions
* Graphs for CPU, RAM, Latency, Errors
* Custom widgets
* Can display metrics + logs + alarms

### Common Dashboard Use Cases

* EC2 health dashboard
* Lambda performance dashboard
* Application microservice dashboard
* Billing dashboard

---

# 🔔 4. CloudWatch Alarms (Trigger Alerts)

Alarms watch a metric and trigger a notification/action.

### Alarm Actions:

* Send SNS notification
* Trigger an AWS Lambda function
* Auto-scale EC2 (scale out/in)
* Stop/Terminate/Recover EC2 instance

### Alarm Creation Example (AWS CLI)

```bash
aws cloudwatch put-metric-alarm \
  --alarm-name CPUHigh \
  --metric-name CPUUtilization \
  --namespace AWS/EC2 \
  --statistic Average \
  --period 300 \
  --threshold 80 \
  --comparison-operator GreaterThanThreshold \
  --dimensions Name=InstanceId,Value=i-001 \
  --evaluation-periods 2 \
  --alarm-actions arn:aws:sns:us-east-1:123456789012:NotifyMe
```

---

# 💰 5. How to Create AWS Billing Alarm

Billing alarms work only in **us-east-1** region.

### Steps:

1. Enable **Billing Alerts** in the AWS Billing console.
2. Go to CloudWatch → Alarms
3. Create alarm → Metric → Billing → Total Estimated Charge
4. Set threshold (example: "> 5 USD")
5. Choose SNS topic (email/SMS)

---

# 🖥 6. CloudWatch Agent Installation & Configuration

The CloudWatch Agent collects:

* OS Metrics (CPU, RAM, Disk)
* Application Logs
* Custom metrics

### Install CloudWatch Agent on EC2 (Amazon Linux)

```bash
sudo yum install amazon-cloudwatch-agent -y
```

### Configure Agent

Create **config.json**:

```json
{
  "metrics": {
    "append_dimensions": {
      "InstanceId": "${aws:InstanceId}"
    },
    "metrics_collected": {
      "cpu": {"measurement": ["usage_system"], "metrics_collection_interval": 60}
    }
  }
}
```

### Start Agent

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -c file:/opt/aws/amazon-cloudwatch-agent/config.json \
  -s
```

---

# 📝 7. Sending Application Logs to CloudWatch

### Method 1: CloudWatch Agent

Add your app log path in config.json:

```json
"logs": {
  "logs_collected": {
    "files": {
      "collect_list": [
        {
          "file_path": "/var/log/myapp.log",
          "log_group_name": "myapp-log-group",
          "log_stream_name": "app-stream"
        }
      ]
    }
  }
}
```

### Method 2: AWS SDK (App writes directly)

Node.js:

```javascript
const AWS = require('aws-sdk');
const cwLogs = new AWS.CloudWatchLogs();
```

### Method 3: Lambda automatically sends logs

* Anything printed via `console.log()` or Python `print()` goes to CloudWatch.

---

# 📈 8. Metrics and Custom Metrics

## Default Metrics:

AWS services automatically publish metrics. Examples:

* **EC2** → CPUUtilization, NetworkIn/Out
* **Lambda** → Duration, Errors, Throttles
* **RDS** → CPUUtilization, FreeStorageSpace

## Custom Metrics:

Used to measure application‑specific values.

Example CLI:

```bash
aws cloudwatch put-metric-data \
  --namespace "Custom/App" \
  --metric-name ActiveUsers \
  --value 420
```

Example Python:

```python
cloudwatch.put_metric_data(
  Namespace='Custom/App',
  MetricData=[{"MetricName": "ActiveUsers", "Value": 420}]
)
```

---

# 📄 9. CloudWatch Logs Deep Dive

CloudWatch Logs store log streams from:

* Lambda
* EC2 (via agent)
* ECS, EKS
* API Gateway
* VPC Flow Logs
* Load Balancers (ALB/NLB)

### Features:

* Log retention policies
* Log Insights (SQL-like querying)
* Subscription filters (send logs to S3, Lambda, Firehose)

Query Example (CloudWatch Logs Insights):

```sql
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
```



