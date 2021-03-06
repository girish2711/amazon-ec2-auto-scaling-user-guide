# Monitoring Your Auto Scaling Groups and Instances Using Amazon CloudWatch<a name="as-instance-monitoring"></a>

Amazon CloudWatch enables you to retrieve statistics as an ordered set of time\-series data, known as metrics\. You can use these metrics to verify that your system is performing as expected\.

Amazon EC2 sends metrics to CloudWatch that describe your Auto Scaling instances\. These metrics are available for any EC2 instance, not just those in an Auto Scaling group\. For more information, see [Instance Metrics](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/viewing_metrics_with_cloudwatch.html#ec2-cloudwatch-metrics) in the *Amazon EC2 User Guide for Linux Instances*\.

Auto Scaling groups can send metrics to CloudWatch that describe the group itself\. You must enable these metrics\.

**Topics**
+ [Auto Scaling Group Metrics](#as-group-metrics)
+ [Dimensions for Auto Scaling Group Metrics](#as-group-metric-dimensions)
+ [Enable Auto Scaling Group Metrics](#as-enable-group-metrics)
+ [Configure Monitoring for Auto Scaling Instances](#enable-as-instance-metrics)
+ [View CloudWatch Metrics](#as-view-group-metrics)
+ [Create Amazon CloudWatch Alarms](#CloudWatchAlarm)

## Auto Scaling Group Metrics<a name="as-group-metrics"></a>

The `AWS/AutoScaling` namespace includes the following metrics\.


| Metric | Description | 
| --- | --- | 
| GroupMinSize |  The minimum size of the Auto Scaling group\.  | 
| GroupMaxSize |  The maximum size of the Auto Scaling group\.  | 
| GroupDesiredCapacity |  The number of instances that the Auto Scaling group attempts to maintain\.  | 
| GroupInServiceInstances |  The number of instances that are running as part of the Auto Scaling group\. This metric does not include instances that are pending or terminating\.  | 
| GroupPendingInstances |  The number of instances that are pending\. A pending instance is not yet in service\. This metric does not include instances that are in service or terminating\.  | 
| GroupStandbyInstances |  The number of instances that are in a `Standby` state\. Instances in this state are still running but are not actively in service\.  | 
| GroupTerminatingInstances |  The number of instances that are in the process of terminating\. This metric does not include instances that are in service or pending\.   | 
| GroupTotalInstances |  The total number of instances in the Auto Scaling group\. This metric identifies the number of instances that are in service, pending, and terminating\.  | 

## Dimensions for Auto Scaling Group Metrics<a name="as-group-metric-dimensions"></a>

To filter the metrics for your Auto Scaling group by group name, use the `AutoScalingGroupName` dimension\.

## Enable Auto Scaling Group Metrics<a name="as-enable-group-metrics"></a>

When you enable Auto Scaling group metrics, Auto Scaling sends sampled data to CloudWatch every minute\.

**To enable group metrics using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Auto Scaling Groups**\.

1. Select your Auto Scaling group\.

1. On the **Monitoring** tab, for **Auto Scaling Metrics**, choose **Enable Group Metrics Collection**\. If you don't see this option, select **Auto Scaling** for **Display**\.  
![\[Enable Auto Scaling group metrics collection.\]](http://docs.aws.amazon.com/autoscaling/ec2/userguide/images/monitoring_group_metrics_enable.png)

**To disable group metrics using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Auto Scaling Groups**\.

1. Select your Auto Scaling group\.

1. On the **Monitoring** tab, for **Auto Scaling Metrics**, choose **Disable Group Metrics Collection**\. If you don't see this option, select **Auto Scaling** for **Display**\.  
![\[Disable Auto Scaling group metrics collection.\]](http://docs.aws.amazon.com/autoscaling/ec2/userguide/images/monitoring_group_metrics_disable.png)

**To enable group metrics using the AWS CLI**  
Enable one or more group metrics using the [enable\-metrics\-collection](http://docs.aws.amazon.com/cli/latest/reference/autoscaling/enable-metrics-collection.html) command\. For example, the following command enables the GroupDesiredCapacity metric\.

```
aws autoscaling enable-metrics-collection --auto-scaling-group-name my-asg --metrics GroupDesiredCapacity --granularity "1Minute"
```

If you omit the \-\-metrics option, all metrics are enabled\.

```
aws autoscaling enable-metrics-collection --auto-scaling-group-name my-asg --granularity "1Minute"
```

**To disable group metrics using the AWS CLI**  
Use the [disable\-metrics\-collection](http://docs.aws.amazon.com/cli/latest/reference/autoscaling/disable-metrics-collection.html) command\. For example, the following command disables all Auto Scaling group metrics:

```
aws autoscaling disable-metrics-collection --auto-scaling-group-name my-asg
```

## Configure Monitoring for Auto Scaling Instances<a name="enable-as-instance-metrics"></a>

By default, basic monitoring is enabled when you create a launch configuration using the AWS Management Console and detailed monitoring is enabled when you create a launch configuration using the AWS CLI or an API\.

If you have an Auto Scaling group and need to change which type of monitoring is enabled for your Auto Scaling instances, you must create a new launch configuration and update the Auto Scaling group to use this launch configuration\. After that, the instances that the Auto Scaling group launches will use the updated monitoring type\. Note that existing instances in the Auto Scaling group continue to use the previous monitoring type\. You can terminate these instances so that Auto Scaling replaces them, or update each instance individually using the [monitor\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/monitor-instances.html) and [unmonitor\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/unmonitor-instances.html)\.

If you have CloudWatch alarms associated with your Auto Scaling group, use the [put\-metric\-alarm](http://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html) command to update each alarm so that its period matches the monitoring type \(300 seconds for basic monitoring and 60 seconds for detailed monitoring\)\. If you change from detailed monitoring to basic monitoring but do not update your alarms to match the five\-minute period, they continue to check for statistics every minute and might find no data available for as many as four out of every five periods\.

**To configure CloudWatch monitoring using the console**  
When you create the launch configuration using the AWS Management Console, on the **Configure Details** page, select **Enable CloudWatch detailed monitoring**\. Otherwise, basic monitoring is enabled\. For more information, see [Creating a Launch Configuration](create-launch-config.md)\.

**To configure CloudWatch monitoring using the AWS CLI**  
Use the [create\-launch\-configuration](http://docs.aws.amazon.com/cli/latest/reference/autoscaling/create-launch-configuration.html) command with the `--instance-monitoring` option\. Set this option to `true` to enable detailed monitoring or `false` to enable basic monitoring\.

```
--instance-monitoring Enabled=true
```

## View CloudWatch Metrics<a name="as-view-group-metrics"></a>

You can view the CloudWatch metrics for your Auto Scaling groups and instances using the Amazon EC2 console\. These metrics are displayed as monitoring graphs\.

Alternatively, you can view these metrics using the CloudWatch console\.

**To view metrics using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Auto Scaling Groups**\.

1. Select your Auto Scaling group\.

1. Choose the **Monitoring** tab\.

1. \(Optional\) To filter the results by time, select a time range from **Showing data for**\.

1. To view the metrics for your groups, for **Display**, choose **Auto Scaling**\. To get a larger view of a single metric, select its graph\. The following metrics are available for groups:
   + Minimum Group Size — `GroupMinSize`
   + Maximum Group Size — `GroupMaxSize`
   + Desired Capacity — `GroupDesiredCapacity`
   + In Service Instances — `GroupInServiceInstances`
   + Pending Instances — `GroupPendingInstances`
   + Standby Instances — `GroupStandbyInstances`
   + Terminating Instances — `GroupTerminatingInstances`
   + Total Instances — `GroupTotalInstances`

1. To view metrics for your instances, for **Display**, choose **EC2**\. To get a larger view of a single metric, select its graph\. The following metrics are available for instances:
   + CPU Utilization — `CPUUtilization`
   + Disk Reads — `DiskReadBytes`
   + Disk Read Operations — `DiskReadOps`
   + Disk Writes — `DiskWriteBytes`
   + Disk Write Operations — `DiskWriteOps`
   + Network In — `NetworkIn`
   + Network Out — `NetworkOut`
   + Status Check Failed \(Any\) — `StatusCheckFailed`
   + Status Check Failed \(Instance\) — `StatusCheckFailed_Instance`
   + Status Check Failed \(System\) — `StatusCheckFailed_System`

**To view metrics using the CloudWatch console**  
For more information, see [Aggregate Statistics by Auto Scaling Group](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/GetMetricAutoScalingGroup.html)\.

**To view CloudWatch metrics using the AWS CLI**  
To view all metrics for all your Auto Scaling groups, use the following [list\-metrics](http://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command:

```
aws cloudwatch list-metrics --namespace "AWS/AutoScaling"
```

To view the metrics for a single Auto Scaling group, specify the `AutoScalingGroupName` dimension as follows:

```
aws cloudwatch list-metrics --namespace "AWS/AutoScaling" --dimensions Name=AutoScalingGroupName,Value=my-asg
```

To view a single metric for all your Auto Scaling groups, specify the name of the metric as follows:

```
aws cloudwatch list-metrics --namespace "AWS/AutoScaling" --metric-name GroupDesiredCapacity
```

## Create Amazon CloudWatch Alarms<a name="CloudWatchAlarm"></a>

A CloudWatch *alarm* is an object that monitors a single metric over a specific period\. A metric is a variable that you want to monitor, such as average CPU usage of the EC2 instances, or incoming network traffic from many different EC2 instances\. The alarm changes its state when the value of the metric breaches a defined range and maintains the change for a specified number of periods\.

An alarm has three possible states:
+ `OK`— The value of the metric remains within the range that you've specified\.
+ `ALARM`— The value of the metric is out of the range that you've specified for a specified time duration\.
+ `INSUFFICIENT_DATA`— The metric is not yet available or there is not enough data available to determine the alarm state\.

When the alarm changes to the `ALARM` state and remains in that state for a number of periods, it invokes one or more actions\. The actions can be a message sent to an Auto Scaling group to change the desired capacity of the group\.

You configure an alarm by identifying the metrics to monitor\. For example, you can configure an alarm to watch over the average CPU usage of the EC2 instances in an Auto Scaling group\.

**To create a CloudWatch alarm**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. On the navigation pane, choose **Alarms**\.

1. Choose **Create Alarm**\.

1. Choose the **EC2 Metrics** category\.

1. \(Optional\) You can filter the results\. To see the instance metrics, choose **Per\-Instance Metrics**\. To see the Auto Scaling group metrics, choose **By Auto Scaling Group**\.

1. Select a metric, and then choose **Next**\.

1. Specify a threshold for the alarm and the action to take\.

   For more information, see [Creating CloudWatch Alarms](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) in the *Amazon CloudWatch User Guide*\.

1. Choose **Create Alarm**\.