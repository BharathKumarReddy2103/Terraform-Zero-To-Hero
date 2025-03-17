**Introduction to Infrastructure as Code (IaC) and Terraform Setup**

**Introduction**

In modern DevOps and cloud engineering, **Infrastructure as Code (IaC)** has become a crucial concept to manage cloud resources efficiently. Terraform, a popular IaC tool, simplifies infrastructure automation across multiple cloud providers like **AWS, Azure, GCP, and OpenStack** using a **universal configuration language.**

This article is part of the **Terraform Zero to Hero** series, aimed at helping DevOps and cloud engineers master Terraform from the ground up. If you're new to Terraform, you don't need prior knowledge—this guide is structured to help you **understand, install, and use Terraform** effectively.

---

**Why Infrastructure as Code (IaC)?**

Manually provisioning cloud resources through web interfaces is inefficient for **large-scale deployments.** Imagine a scenario:

•	A **DevOps engineer** receives a request to create an S3 bucket in AWS.

•	Manually, this process takes about **2 minutes.**

•	Now, **100 different teams** need S3 buckets.

•	This means **200 minutes (3+ hours) of manual effort**

To **eliminate repetitive manual tasks**, DevOps engineers rely on **automation using APIs, CLI tools, or IaC tools like Terraform.**

---

**Why Terraform Over Other IaC Tools?**

There are several IaC tools available:

**•	AWS CloudFormation** (for AWS)

**•	Azure Resource Manager (ARM)** (for Azure)

**•	Google Cloud Deployment Manager** (for GCP)

**•	OpenStack Heat Templates** (for OpenStack)

However, these tools are **cloud-specific**, requiring engineers to learn different syntax and tooling for each provider.

**Terraform solves this problem** by offering a **universal IaC approach**, allowing engineers to manage multi-cloud environments using **one language—HCL (HashiCorp Configuration Language).**

**Key advantages of Terraform:**

**•	Multi-cloud support:** Works with AWS, Azure, GCP, Kubernetes, and more.

**•	Declarative syntax:** Simple and easy to understand.

**•	State management:** Maintains infrastructure state for consistency.

**•	Extensive community support:** Large user base and plugins.

---

**Installing Terraform**

Terraform can be installed on **Windows, Linux, and macOS**. Below are installation steps for each OS.

**1. Installing on macOS**

```sh
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

Verify installation:

```sh
terraform -version
```

**2. Installing on Linux (Ubuntu/Debian)**

```sh
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt-get update && sudo apt-get install terraform
```

**Verify installation:**

```sh
terraform -version
```

**3. Installing on Windows**

**1.**	Download Terraform from **Terraform Downloads** (https://developer.hashicorp.com/terraform/install).

**2.**	Extract the downloaded .zip file.

**3.**	Add Terraform to the system PATH.

**4.**	Verify installation:

```sh
terraform -version
```

---

**Alternative: Running Terraform in GitHub CodeSpaces**

If you **cannot install Terraform** on your local machine (e.g., office laptops with restrictions), **GitHub CodeSpaces** provides a **free cloud-based VS Code environment** with **2 CPUs & 4GB RAM for 60 hours/month.**

**Steps to use GitHub CodeSpaces:**

**1.**	Fork the **Terraform Zero to Hero** repository on GitHub.

**2.**	Click **"Code" > "Open in CodeSpaces".**

**3.**	Add Terraform and AWS CLI as dev containers:

o	**Terraform**: Modify active configuration > Add Dev Container > Terraform

o	**AWS CLI**: Modify active configuration > Add Dev Container > AWS CLI

**4.**	Rebuild the container and start using Terraform.

---

**Configuring AWS for Terraform**

To use Terraform with AWS, we need **AWS credentials**. Run the following command:

```sh
aws configure
```

Provide:

**•	Access Key ID**

**•	Secret Access Key**

**•	Default Region** (e.g., us-east-1)

**•	Output Format** (e.g., json)

Verify AWS connectivity:

```sh
aws s3 ls
```

---

**Writing Your First Terraform Script**

**1. Creating the Terraform Configuration File**

Create a file named main.tf and add the following code:

```sh
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0" # Use an official AWS AMI ID
  instance_type = "t2.micro"
  subnet_id     = "subnet-12345678" # Replace with a valid subnet ID
  key_name      = "my-key-pair" # Replace with your key pair
}
```

**2. Initializing Terraform**

Run:

```sh
terraform init
```

This command:

•	Initializes Terraform in the current directory.

•	Downloads necessary provider plugins.

**3. Checking the Plan**

Before applying changes, preview what Terraform will create:

```sh
terraform plan
```

**4. Applying the Terraform Configuration**

To deploy the infrastructure:

```sh
terraform apply
```

Type yes when prompted.

**5. Verifying in AWS**

•	Navigate to **AWS EC2 Console.**

•	Look for a **new running instance.**

**6. Destroying the Infrastructure**

To **avoid AWS billing, destroy the created instance:**

```sh
terraform destroy
```

Type yes when prompted.

---

**Understanding Terraform State Files**

Terraform maintains an internal **state file** (terraform.tfstate) to track created resources.

```sh
cat terraform.tfstate
```

**State files:**

•	Store details of managed infrastructure.

•	Help Terraform **understand changes** between runs.

•	Can be stored **locally or remotely** (S3, Git, etc.).

---

**Best Practices for Terraform**

✅ **Use Version Control (GitHub, GitLab, Bitbucket)**

✅ **Store State Files in Remote Storage (S3, Terraform Cloud)**

✅ **Use Terraform Workspaces for Multi-Environment Deployments**

✅ **Implement Variables and Modules for Reusability**

✅ **Always Review terraform plan Before Applying Changes**

---

**Conclusion**

This guide covered:

**•	What is Infrastructure as Code (IaC)?**

**•	Why Terraform over other IaC tools?**

**•	How to install Terraform on different OS platforms**

**•	How to use GitHub CodeSpaces for Terraform**

**•	Configuring AWS CLI for Terraform**

**•	Writing, applying, and destroying Terraform configurations**

**•	Understanding Terraform State Files**

**•	Best practices for Terraform usage**

Terraform is a **must-have skill for DevOps and cloud engineers**. By automating infrastructure provisioning, Terraform improves **efficiency, scalability, and maintainability.**

**Next Steps**

•	Explore Terraform **variables, modules**, and **provisioners.**

•	Learn about **Terraform remote backends.**

•	Deploy **multi-cloud environments** using Terraform.

**Resources for Further Learning**

**•	Terraform Official Documentation** (https://developer.hashicorp.com/terraform/docs)

**•	AWS Terraform Provider** (https://registry.terraform.io/providers/hashicorp/aws/latest/docs)

**•	Terraform GitHub Repository** (https://github.com/hashicorp/terraform)
