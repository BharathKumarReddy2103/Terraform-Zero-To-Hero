**Terraform Scenario-Based Interview Questions and Solutions**

**Introduction**

Terraform is a widely used Infrastructure as Code (IaC) tool that allows teams to manage and provision cloud infrastructure efficiently. While learning Terraform concepts is essential, many engineers struggle with **scenario-based interview questions**. Interviewers often ask practical Terraform use cases that require problem-solving skills beyond basic commands.

In this article, we will cover **two real-world Terraform scenarios** frequently asked in DevOps interviews:

**1.	Terraform Migration** – Importing existing infrastructure into Terraform.

**2.	Drift Detection** – Identifying and managing manual changes in Terraform-managed infrastructure.

We will walk through these scenarios with hands-on demonstrations, best practices, and real-world use cases.

---

**Scenario 1: Terraform Migration (Importing Existing Infrastructure)**

**Problem Statement**

Imagine your team has been using **AWS CloudFormation** to manage infrastructure, and an **EC2 instance** has already been created. Now, your organization decides to **migrate to Terraform.**

The challenge:

•	You **cannot manually recreate** the infrastructure in Terraform.

•	You need to **import the existing EC2 instance** into Terraform while maintaining its configuration.

•	You must ensure **Terraform does not recreate the resource** but instead recognizes it as an existing resource.

**Solution**

Terraform provides a built-in command called **terraform import**, which helps bring existing resources under Terraform's management.

**Step 1: Initialize the Terraform Project**

```sh
mkdir terraform-migration
cd terraform-migration
terraform init
```

**Step 2: Write the main.tf File**

Before importing, create a basic main.tf file to define the AWS provider:

```sh
provider "aws" {
  region = "us-east-1"
}
```

**Step 3: Import the Existing EC2 Instance**

To import an existing EC2 instance, identify its **instance ID** from the AWS console and run:

```sh
terraform import aws_instance.example i-1234567890abcdef0
```

**Step 4: Generate Terraform Configuration**

Since Terraform doesn’t automatically generate configuration files, use the following command:

```sh
terraform plan -generate-config-out=generated.tf
```

This will create a new Terraform file (generated.tf) containing the **entire configuration** of the imported EC2 instance.

**Step 5: Merge the Imported Configuration**

•	Open generated.tf, copy the **resource block** for aws_instance.example.

•	Paste it into main.tf.

•	Delete generated.tf, as it is no longer needed.

Example of the final main.tf:

```sh
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-0abcdef1234567890"
  instance_type = "t2.micro"
  subnet_id     = "subnet-0abcdef1234567890"
  security_groups = ["sg-0abcdef1234567890"]
}
```

**Step 6: Verify with terraform plan**

Now, run:

```sh
terraform plan
```

If everything is correct, Terraform will show **"No changes. Infrastructure is up to date."**, meaning the migration is successful.

---

**Scenario 2: Drift Detection in Terraform**

**Problem Statement**

Your organization has **hundreds of AWS resources** managed by Terraform. A DevOps engineer manually updates an **S3 bucket lifecycle policy** via the AWS console.

Challenges:

•	Terraform does **not automatically detect manual changes** to infrastructure.

•	If you run terraform apply, Terraform **may override** those manual changes.

•	You need a way to **detect and track drifts** in your infrastructure.

**Solution**

Terraform provides two common approaches for drift detection:

**1.	Using** terraform refresh

**2.	Automating Drift Detection with AWS CloudWatch and Lambda**

**Option 1: Using terraform refresh**

Terraform's **refresh** command updates the state file to reflect manual changes:

```sh
terraform refresh
terraform plan
```

**Limitations:**

•	terraform refresh does not notify teams of drift automatically.

•	You need to run it **manually or set up a cron job** to detect changes periodically.

**Option 2: Automating Drift Detection with AWS CloudWatch and Lambda**

To automate drift detection, use **AWS CloudWatch** and **Lambda functions:**

**1.	Enable AWS CloudTrail** to log all API changes to Terraform-managed resources.

**2.**	Set up **CloudWatch Alarms** to detect changes in specific services like EC2, S3, or RDS.

**3.**	Configure a **Lambda function** to check if the change was made by **Terraform** or a **manual IAM user**.

**4.**	Send an **alert** via SNS (Simple Notification Service) or Slack.

**Example AWS Lambda Code for Drift Detection**

```sh
import boto3

def lambda_handler(event, context):
    client = boto3.client('logs')
    
    response = client.filter_log_events(
        logGroupName='/aws/lambda/terraform-drift-detection',
        filterPattern='{ ($.userIdentity.arn != "arn:aws:iam::123456789012:role/TerraformRole") }'
    )
    
    if response['events']:
        sns = boto3.client('sns')
        sns.publish(
            TopicArn='arn:aws:sns:us-east-1:123456789012:TerraformAlerts',
            Message="Drift detected! Manual change found in Terraform-managed resources."
        )

    return "Drift Detection Executed"
```

**Automated Workflow Summary**

**•	CloudTrail** records manual infrastructure changes.

**•	CloudWatch** triggers a **Lambda function.**

•	Lambda checks if the change was **manual or Terraform-managed.**

•	If a **manual change is found**, it **sends an alert** via **SNS or Slack**.

---

**Best Practices for Terraform Migration & Drift Detection**

**1. Terraform Migration Best Practices**

**•	Use terraform import to migrate existing resources** instead of manually recreating configurations.

**•	Clean up unnecessary attributes** after importing to avoid excessive Terraform state changes.

**•	Use** terraform plan **frequently** to verify that resources are correctly mapped.

**2. Drift Detection Best Practices**

**•	Implement IAM least privilege** – Restrict manual access to cloud resources.

**•	Automate drift detection** using **Terraform Cloud, AWS Lambda, and CloudTrail logs.**

**•	Set up alerts** for detected drifts to ensure teams can act before Terraform overwrites changes.

**•	Schedule periodic** **terraform plan runs** to catch drifts before deployment.

---

**Conclusion**

Terraform scenario-based questions are common in **DevOps and Cloud interviews**, and knowing how to handle them can **differentiate you from other candidates**.

**Key Takeaways:**

✔ **Terraform Migration:** Use terraform import to bring existing infrastructure under Terraform management.

✔ **Drift Detection:** Use terraform refresh or an **automated AWS Lambda approach** to detect unauthorized manual changes.

✔ **Best Practices:** Regularly run terraform plan, restrict IAM access, and set up CloudWatch monitoring for better infrastructure management.

By mastering these scenarios, you not only **prepare for DevOps interviews** but also **enhance your real-world Terraform expertise.**

---

This article is **open for contributions** If you have additional Terraform migration or drift detection techniques, feel free to **submit a pull request.**

⭐ **Star this repository if you found it helpful.**
