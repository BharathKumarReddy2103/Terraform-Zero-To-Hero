**Deploying an EKS Cluster with VPC Configuration Using Terraform**

**Introduction**

Amazon Elastic Kubernetes Service (EKS) simplifies the deployment, management, and scaling of Kubernetes clusters on AWS. To automate the setup of an EKS cluster with VPC configuration, we use **Terraform**, an Infrastructure as Code (IaC) tool.

In this guide, we will:

•	Create an **Amazon EKS cluster** with **worker nodes using auto-scaling.**

•	Set up a **VPC with public and private subnets.**

•	Configure **security groups** to enhance security.

•	Use **Terraform modules** to simplify the deployment.

•	Deploy and verify the cluster using kubectl.

By the end of this guide, you will have a fully functional **EKS cluster** running on AWS, following best practices.

**Prerequisites**

Before proceeding, ensure you have:

**1.	AWS CLI** installed and configured with the required permissions.

**2.	Terraform** installed on your system.

**3.	kubectl** installed for managing Kubernetes clusters.

**4.**	An **AWS account** with IAM permissions to create EKS, VPC, and related resources.

**Project Overview**

**Architecture**

We will use **Terraform** to provision the following AWS resources:

•	A **VPC** with **public and private subnets.**

•	An **EKS cluster** deployed within the VPC.

•	Worker nodes using **Auto Scaling Groups.**

**•	Security groups** to control inbound and outbound traffic.

**•	IAM roles** for EKS and worker nodes.

**Terraform Workflow**

**1.	Initialize Terraform** (terraform init).

**2.	Plan the deployment** (terraform plan).

**3.	Apply the changes** (terraform apply).

**4.	Verify the EKS cluster** using kubectl.

**5.	Destroy resources** after testing (terraform destroy).

---

**Step 1: Install AWS CLI and Terraform**

**Installing AWS CLI**

Run the following command based on your operating system:

**Linux / macOS**

```sh
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
```

**Windows** Download the installer from **AWS CLI Documentation** (https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) and follow the installation steps.

Verify installation:

```sh
aws --version
```

**Installing Terraform**

For **Linux/macOS**, run:

```sh
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install terraform
```

For **Windows**, install via Chocolatey:

```sh
choco install terraform
```

Verify installation:

```sh
terraform version
```

---

**Step 2: Configure AWS Credentials**

To authenticate Terraform with AWS, configure the AWS CLI:

```sh
aws configure
```

Provide:

**•	Access Key ID**

**•	Secret Access Key**

**•	Default AWS Region (e.g., us-east-1)**

Terraform will automatically use these credentials to deploy resources.

---

**Step 3: Writing Terraform Configuration**

**1. Terraform Provider Configuration**

Create a providers.tf file:

```sh
provider "aws" {
  region = var.aws_region
}
```

**2. VPC Configuration**

Instead of manually defining all VPC resources, we use the AWS VPC module:

**vpc.tf**

```sh
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.7.0"

  name = "eks-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["us-east-1a", "us-east-1b"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24"]

  enable_nat_gateway = true
  enable_dns_hostnames = true

  tags = {
    Name = "eks-vpc"
  }
}
```

**3. EKS Cluster Configuration**

**eks.tf**

```sh
module "eks" {
  source  = "terraform-aws-modules/eks/aws"
  version = "19.15.3"

  cluster_name    = "eks-cluster"
  cluster_version = "1.26"

  subnet_ids = module.vpc.private_subnets

  node_groups = {
    eks_nodes = {
      desired_capacity = 2
      max_capacity     = 6
      min_capacity     = 2

      instance_types = ["t3.medium"]
    }
  }

  tags = {
    Environment = "dev"
  }
}
```

**4. Security Groups**

**security.tf**

```sh
resource "aws_security_group" "eks_sg" {
  name   = "eks-sg"
  vpc_id = module.vpc.vpc_id

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

**5. Outputs**

**outputs.tf**

```sh
output "eks_cluster_id" {
  value = module.eks.cluster_id
}

output "eks_cluster_endpoint" {
  value = module.eks.cluster_endpoint
}
```

---

**Step 4: Deploying the EKS Cluster**

**1. Initialize Terraform**

Run:

```sh
terraform init
```

This will download required providers and modules.

**2. Plan the Deployment**

```sh
terraform plan
```

Terraform will show a preview of the changes.

**3. Apply the Configuration**

```sh
terraform apply -auto-approve
```

This will take 10-15 minutes to provision the EKS cluster.

---

**Step 5: Verifying the EKS Cluster**

Once the cluster is created, configure kubectl to connect to it:

```sh
aws eks --region us-east-1 update-kubeconfig --name eks-cluster
```

Check cluster nodes:

```sh
kubectl get nodes
```

Verify deployments:

```sh
kubectl get deployments --all-namespaces
```

---

**Step 6: Cleaning Up**

To avoid unnecessary AWS charges, destroy the resources:

```sh
terraform destroy -auto-approve
```

---

**Best Practices**

**•	Use Terraform Modules:** Helps modularize and reuse configurations.

**•	Store State Securely:** Use an **S3 bucket with DynamoDB** for state management in production.

**•	Least Privilege IAM Roles:** Follow **AWS IAM best practices.**

**•	Version Control with Git:** Keep Terraform code in a **Git repository.**

---

**Conclusion**

In this guide, we used **Terraform** to provision an **EKS cluster** with a **VPC, security groups, and worker nodes** using **Auto Scaling Groups**. We also covered best practices and deployment steps.
