**Terraform Provisioners:**

**Introduction**

In the world of **Infrastructure as Code (IaC)**, **Terraform** is widely used for automating cloud infrastructure deployment. However, provisioning infrastructure alone is not enough; you often need to configure instances and deploy applications automatically. This is where **Terraform Provisioners** come into play.

This article will provide an **in-depth understanding** of Terraform Provisioners with a **real-world DevOps use case**. We will first walk through a **practical implementation**, then cover the theoretical concepts, best practices, and use cases.

---

**Real-World DevOps Use Case: Deploying a Python Flask Application on AWS Using Terraform Provisioners**

**Scenario**

A **development team** requires a temporary AWS infrastructure to test changes in their **Python Flask application (app.py)**. Instead of manually setting up infrastructure every time, the **DevOps team** automates the process using **Terraform**. The setup includes:

•	Creating an **AWS VPC** with a **public subnet.**

•	Deploying an **EC2 instance** inside the VPC.

•	Configuring **security groups** to allow HTTP (port 80) and SSH (port 22) access.

**•	Deploying the Flask application** on the EC2 instance.

**•	Automating deployment** using Terraform **provisioners**.

---

**Step-by-Step Implementation in Terraform**

**1. Setting Up Terraform Provider**

We start by defining **AWS as the cloud provider** and specifying the **region.**

```sh
provider "aws" {
  region = "us-east-1"
}
```

**2. Creating the VPC**

We define a **VPC** with a CIDR block of 10.0.0.0/16.

```sh
resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "DevOps-VPC"
  }
}
```

**3. Creating a Public Subnet**

We configure a **public subnet** within the VPC.

```sh
resource "aws_subnet" "my_subnet" {
  vpc_id     = aws_vpc.my_vpc.id
  cidr_block = "10.0.1.0/24"

  map_public_ip_on_launch = true

  tags = {
    Name = "Public-Subnet"
  }
}
```

**4. Setting Up an Internet Gateway and Route Table**

To enable internet access, we attach an **Internet Gateway** and update the **Route Table.**

```sh
resource "aws_internet_gateway" "my_igw" {
  vpc_id = aws_vpc.my_vpc.id

  tags = {
    Name = "My-Internet-Gateway"
  }
}

resource "aws_route_table" "my_route_table" {
  vpc_id = aws_vpc.my_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.my_igw.id
  }

  tags = {
    Name = "Public-Route-Table"
  }
}

resource "aws_route_table_association" "my_route_table_association" {
  subnet_id      = aws_subnet.my_subnet.id
  route_table_id = aws_route_table.my_route_table.id
}
```

**5. Creating an EC2 Key Pair**

To securely access the EC2 instance via SSH, we generate and **upload an SSH key.**

```sh
resource "aws_key_pair" "my_key_pair" {
  key_name   = "devops-key"
  public_key = file("~/.ssh/id_rsa.pub")
}
```

**6. Configuring Security Groups**

The security group allows **SSH (port 22)** and **HTTP (port 80)** access.

```sh
resource "aws_security_group" "my_security_group" {
  vpc_id = aws_vpc.my_vpc.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "My-Security-Group"
  }
}
```

**7. Creating an EC2 Instance**

We deploy an **EC2 instance** in the **public subnet** and configure **Terraform provisioners.**

```sh
resource "aws_instance" "my_ec2" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  key_name      = aws_key_pair.my_key_pair.key_name
  subnet_id     = aws_subnet.my_subnet.id
  security_groups = [aws_security_group.my_security_group.name]

  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("~/.ssh/id_rsa")
    host        = self.public_ip
  }

  # **File Provisioner: Copy Python Flask App to EC2**
  provisioner "file" {
    source      = "app.py"
    destination = "/home/ubuntu/app.py"
  }

  # **Remote Exec Provisioner: Install Python & Run Flask App**
  provisioner "remote-exec" {
    inline = [
      "sudo apt update",
      "sudo apt install -y python3 python3-pip",
      "pip3 install flask",
      "nohup python3 /home/ubuntu/app.py &"
    ]
  }

  tags = {
    Name = "Terraform-Provisioner-Instance"
  }
}
```

---

**Understanding Terraform Provisioners**

**1. What Are Provisioners in Terraform?**

Provisioners allow Terraform to perform **post-deployment configuration** by executing scripts, transferring files, and installing dependencies.

**2. Types of Provisioners in Terraform**

**a. Remote Exec Provisioner**

•	Executes commands **inside** the created resource (e.g., EC2 instance).

•	Used for software installation, configuration, and application deployment.

```sh
provisioner "remote-exec" {
  inline = [
    "sudo apt update",
    "sudo apt install -y nginx"
  ]
}
```

**b. File Provisioner**

•	Transfers files from the local system to the remote instance.

•	Useful for copying application code, configuration files, or secrets.

```sh
provisioner "file" {
  source      = "config.json"
  destination = "/etc/myapp/config.json"
}
```

**c. Local Exec Provisioner**

•	Runs commands **on the local machine** instead of the remote resource.

•	Used for logging, triggering external scripts, or notifying APIs.

```sh
provisioner "local-exec" {
  command = "echo 'Terraform execution started' >> log.txt"
}
```

---

**Best Practices for Using Terraform Provisioners**

**•	Use Provisioners Only When Necessary:** Prefer cloud-native solutions like **AWS User Data** or **Ansible** for configuration management.

**•	Ensure Idempotency:** Avoid redundant execution by writing idempotent scripts.

**•	Manage Secrets Securely:** Never hardcode sensitive information in provisioners.

**•	Use Remote Backend for State Management:** Store the Terraform state securely using **S3 + DynamoDB Locking.**

---

**Conclusion**

Terraform **Provisioners** provide a powerful way to **configure instances automatically** during infrastructure deployment. By combining **file provisioners, remote execution, and local execution**, DevOps Engineers can automate infrastructure provisioning and deployment efficiently.

This article demonstrated a **real-world scenario** where Terraform automates the provisioning of an AWS **EC2 instance** and deploys a **Flask application** without manual intervention.
