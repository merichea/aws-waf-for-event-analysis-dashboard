# WAF Logs Dashboard For DDoS Analysis Project

During major online events like live broadcasts, security teams need a fast and clear understanding of attack patterns and behaviors to distinguish between normal and malicious traffic flows. The solution outlined here allows filtering flow logs by "Client IP", "URI", "Header name", and "Header value" to analyze these fields and pinpoint values specifically associated with attack traffic versus normal traffic. For example, the dashboard can identify the top header values that are atypical for normal usage. The security team can then create an AWS WAF rule to block requests containing these header values, stopping the attack.
 
This project demonstrates using AWS Glue crawlers to categorize and structure WAF flow log data and Amazon Athena for querying. Amazon Quicksight is then employed to visualize query results in a dashboard. Once deployed, the dashboard provides traffic visualization similar to the example graphs shown in _Images_ folder in under project , empowering security teams with insight into attacks and defense.

For more information: please check the related workshop https://catalog.us-east-1.prod.workshops.aws/workshops/520cb1cc-0fc5-4408-8fbe-d9527bc59d2d/en-US

**Architecture Principles**

![architecture](/Images/Waf-dashboard-Architecture.png)

The AWS WAF for Event Analysis Dashbaord is made of the following compenents:

1. The AWS WAF will capture WAF flow logs that will be stored in an S3  bucket in your account
1. AWS Glue crawlers are used to discover schema and store it in the AWS Glue Data Catalog
1. Amazon Athena is then used for building the view of the WAF Flow logs.
1. Amazon QuickSight can now query and build visualizations to discover insights in your baseline and during the Event

***Once Deployed:***
The Amazon Quicksight User will have access to the AWS WAF for Event Analysis Dashbaord which is made of 4 tabs:
1. 1st Tab: the Overall traffic - To help identify any deviation from the baseline traffic that worths to be analayzed
1. 2nd Tab: Client IP, Country, HTTP Method Insights and comparaison between Baseline Traffic and Event Traffic
1. 3rd Tab:  Header Value insights and comparison between Baseline Traffic and Event Traffic
1. 4th Tab: Ja3fingerprint combined with header_value insights and comparison between Baseline Traffic and Event Traffic

# Included in this project

1. AWS CloudFormation Template to create the AWS Glue crawler which will be used to discover the schema and store it in the AWS Glue Data Catalog
1. a cid-cmd template to create the Amazon Athena view, the Amazon Quicksight Dataset and the Amazon Quicksight Dashbaord.

# Deployment
**Step1: install Pre-requisites**

 Install the cid-cmd tool using the following Command Line: 
    
`pip3 install cid-cmd`

**Step2: AWS WAF Logs Dashboard Data Set Deployment**

2. Sign in to the **AWS Management Console**
3. Navigate to the **AWS CloudFormation **console >** Create Stack** > **“With new resources” **

* Note: the template will ask you to enter an S3 bucket name. The bucket name has to start with aws-waf-logs prefix as required by AWS WAF service 

4. Download the AWS CloudFormation template **"AWS-WAF-Logs-Dashboard-Set-UP-CFN.yaml"** file from this git repository. In the create wizard specify the **AWS-WAF-Logs-Dashboard-Set-UP-CFN.yaml**  template.
5. fill the following parameter **"S3BucketName"** with a bucket name where you store the waf logs. it has to start with aws-waf-logs.
6. Specify a **“Stack name” **and choose Next
7. Leave the **“Configure stack options”** at default values and choose Next
8. Review the details on the final screen and under **“Capabilities”** check the box for **“I acknowledge that AWS CloudFormation might create IAM resources with custom names”**
9. Choose **Submit**

**Step3 : Prepare Amazon QuickSight**

 Amazon QuickSight is the AWS Business Intelligence tool that will allow you to not only view the Standard AWS provided insights into all of your accounts, but will also allow to produce new versions of the Dashboards we provide or create something entirely customized to you. If you are already a regular Amazon QuickSight user you can skip these steps. 

1. Log into your AWS Account and search for QuickSight in the list of Services
2. You will be asked to sign up before you will be able to use it
3. After pressing the Sign up button you will be presented with 2 options, please ensure you select the Enterprise Edition during this step
4. Select continue and you will need to fill in a series of options in order to finish creating your account.
    1. Ensure you select the region that is most appropriate based on where your S3 Bucket is located containing your AWS WAF Logs  report files.
5. Enable the Amazon S3 option and select the bucket where your AWS WAF Log data created via Data Collection Lab are located 

**Step4 : Deploy the Dashboard**


We will be using the **WAF-logs-Dashboard-for-DDoS-Analysis-Quicksight-Template.yaml** template to deploy the dashboard in the quicksight. This template will also create a dataset in Amazon Quicksight.

1. Open your favorite terminal, under the directory where you saved AWS-WAF-Logs-Dashboard-For-Event-Analysis.yaml template and run the following cli:

`cid-cmd deploy --resources ./WAF-logs-Dashboard-for-DDoS-Analysis-Quicksight-Template.yaml` 

2. during the creation you will be prompted to select: 
[dashboard-id] Please select dashboard to install: 
Select  _AWS-WAF-Logs-Dashboard-For-Event-Analysis-V1_
[athena-workgroup] Select AWS Athena workgroup to use:
Select _primary_
[athena-database] Select AWS Athena database to use: 
Select _waflogsdb_


after a successful run , the output will be :
```
#######
####### Congratulations!
####### AWS-WAF-Logs-Dashboard-For-Event-Analysis-V1 is available at: https://[region].quicksight.aws.amazon.com/sn/dashboards/AWS_WAF_For_Event_Analysis_Dashboard_name
#######
```
You can now start analyzing your traffic pattern (you'll be able to analyze traffic from the past 3 months)

**FAQ:**
1. If you get the following error whith the "waf_logs_antiddos_least_fields_v2" Dataset:
sourceErrorCode: 100071
[Simba]AthenaJDBC An error has been thrown from the AWS Athena client. Permission denied on S3 path: s3://xxxxxxxx
sourceErrorState:	HY000
sourceException:	java.sql.SQLException
sourceType:	ATHENA

Please check  https://docs.aws.amazon.com/quicksight/latest/user/athena.html to fix the problem








