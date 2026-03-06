# AWS CLOUD WATCH 

Welcome back to our "30 Days AWS Zero to Hero" series. Today, on Day 16, we will deep dive into AWS CloudWatch.

What is AWS CloudWatch?

AWS CloudWatch is a powerful monitoring and observability service provided by Amazon Web Services. It enables you to gain insights into the performance, health, and operational aspects of your AWS resources and applications. CloudWatch collects and tracks metrics, collects and monitors log files, and sets alarms to alert you on certain conditions.

Advantages of AWS CloudWatch:

    Comprehensive Monitoring: CloudWatch allows you to monitor various AWS resources such as EC2 instances, RDS databases, Lambda functions, and more. You get a unified view of your entire AWS infrastructure.

    Real-Time Metrics: It provides real-time monitoring of metrics, allowing you to respond quickly to any issues or anomalies that might arise.

    Automated Actions: With CloudWatch Alarms, you can set up automated actions like triggering an Auto Scaling group to scale in or out based on certain conditions.

    Log Insights: CloudWatch Insights lets you analyze and search log data from various AWS services, making it easier to troubleshoot problems and identify trends.

    Dashboards and Visualization: Create custom dashboards to visualize your application and infrastructure metrics in one place, making it easier to understand the overall health of your system.

Problem Solving with AWS CloudWatch:

CloudWatch helps address several critical challenges, including:

    Resource Utilization: Tracking resource utilization and performance metrics to optimize your AWS infrastructure efficiently.
    Proactive Monitoring: Identifying and resolving issues before they impact your applications or users.
    Troubleshooting: Analyzing logs and metrics to troubleshoot problems and reduce downtime.
    Scalability: Automatically scaling resources based on demand to ensure optimal performance and cost efficiency.

Practical Use Cases of AWS CloudWatch:

    Auto Scaling: CloudWatch can trigger Auto Scaling actions based on defined thresholds. For example, you can automatically scale in or out based on CPU utilization or request counts.

    Resource Monitoring: Monitor EC2 instances, RDS databases, DynamoDB tables, and other AWS resources to gain insights into their performance and health.

    Application Insights: Track application-specific metrics to monitor the performance of your applications and identify potential bottlenecks.

    Log Analysis: Use CloudWatch Logs Insights to analyze log data, identify patterns, and troubleshoot issues in real-time.

    Billing and Cost Monitoring: CloudWatch can help you monitor your AWS billing and usage patterns, enabling you to optimize costs.


    _________________________

    Use case /project
    Disk alert by installing the cloudwatch agent 


    
1️⃣ Install CloudWatch Agent

sudo yum install amazon-cloudwatch-agent -y

Verify:

rpm -qa | grep cloudwatch


---

2️⃣ Create Simple Configuration File

Create config file:

sudo vi /opt/aws/amazon-cloudwatch-agent/bin/config.json

Paste this working configuration:

{
 "metrics": {
   "append_dimensions": {
     "InstanceId": "${aws:InstanceId}"
   },
   "metrics_collected": {
     "cpu": {
       "measurement": [
         "cpu_usage_idle",
         "cpu_usage_iowait",
         "cpu_usage_user",
         "cpu_usage_system"
       ],
       "totalcpu": true
     },
     "mem": {
       "measurement": [
         "mem_used_percent"
       ]
     },
     "disk": {
       "measurement": [
         "used_percent"
       ],
       "resources": [
         "/"
       ]
     }
   }
 }
}

This collects:

CPU usage

Memory usage

Disk usage



---

3️⃣ Start CloudWatch Agent

Run:

sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
-a fetch-config \
-m ec2 \
-c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json \
-s


---

4️⃣ Verify Agent Running

sudo systemctl status amazon-cloudwatch-agent

Check logs if needed:

sudo tail -f /opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log


---

5️⃣ Check Metrics in AWS

Go to Amazon CloudWatch

Path:

Metrics
 → CWAgent
 → InstanceId

You will see metrics like:

cpu_usage_idle
mem_used_percent
disk_used_percent


---

6️⃣ Create Disk Alert

Create alarm in Amazon CloudWatch:

Metric:

CWAgent → disk_used_percent

Condition example:

>= 80%

Notification:

Use Amazon Simple Notification Service for email or Slack.


---

✅ Real production tip

Most DevOps teams add inode monitoring also, because disks fail due to inode exhaustion even when disk space looks fine.

Example metric:

disk_inodes_free


---

If you want, I can also show a very useful production setup:

✔ CloudWatch automatically alerts when

Disk > 80%

Memory > 85%

CPU > 90%
