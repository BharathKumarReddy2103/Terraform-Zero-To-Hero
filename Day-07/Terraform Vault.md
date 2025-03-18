**Secrets Management in Terraform Using HashiCorp Vault**

**Introduction**

Secrets management is a critical aspect of modern DevOps workflows, ensuring that sensitive data such as **API tokens, passwords, and certificates** are securely stored and managed. In this guide, we will explore how to integrate **Terraform with HashiCorp Vault**, a widely used secrets management tool.

We will cover:

•	Why **secrets management** is essential in Terraform, Ansible, Kubernetes, and CI/CD pipelines

**•	Step-by-step implementation** of HashiCorp Vault on AWS

**•	Integrating Terraform** with Vault to retrieve secrets securely

**•	Best practices** for using Vault in production environments

By the end of this guide, you will have a **fully working** setup where Terraform retrieves secrets from HashiCorp Vault and uses them dynamically in an AWS **EC2 instance**.

---

**1. Why Secrets Management is Essential in DevOps**

Secrets management is critical in DevOps and cloud-native applications because engineers frequently deal with sensitive data, including:

**•	API tokens**

**•	Database credentials**

**•	SSH keys**

**•	TLS certificates**

Using a proper secrets management solution like **HashiCorp Vault** helps in: 

✔ **Avoiding accidental credential leaks in Git repositories**

✔ **Enforcing role-based access control (RBAC)**

✔ **Enhancing security by encrypting secrets at rest and in transit**

---

**2. Setting Up HashiCorp Vault on AWS**

We will deploy **HashiCorp Vault** on an **AWS EC2 instance** running Ubuntu. Follow these steps:

**Step 1: Launch an AWS EC2 Instance**

**1.**	Log in to the **AWS Management Console.**

**2.**	Navigate to **EC2 > Launch Instance.**

**3.**	Configure the instance:

o	**Name**: vault-server

o	**AMI**: Ubuntu (latest version)

o	**Instance Type**: t2.micro (for demo purposes)

o	**Security Group**: Allow **port 8200** (Vault UI) and **port 22** (SSH).

**4.**	Click **Launch** and wait for the instance to start.

**Step 2: Connect to the EC2 Instance**

Once the instance is running, connect to it via SSH:

```sh
ssh -i my-key.pem ubuntu@<EC2_PUBLIC_IP>
```

**Step 3: Install HashiCorp Vault**

Run the following commands to install **Vault** on Ubuntu:

```sh
sudo apt update && sudo apt install -y gpg
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
echo "deb https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install -y vault
```

**Step 4: Start Vault in Development Mode**

To start Vault in **development mode** (useful for testing), run:

```sh
vault server -dev
```

The server will start, and you will see a **root token** in the output. Copy this token as it will be required for authentication.

---

**3. Configuring HashiCorp Vault**

Now that Vault is running, we need to:

**1.	Enable a key-value (KV) secrets engine**

**2.	Store a secret**

**3.	Set up authentication for Terraform**

**Step 1: Enable the KV Secrets Engine**

Run the following command to enable **KV (key-value) secrets storage:**

```sh
vault secrets enable -path=kv kv
```

**Step 2: Store a Secret in Vault**

Create a secret with a **username and password:**

```sh
vault kv put kv/test-secret username="admin" password="SecurePass123"
```

To verify, run:

```sh
vault kv get kv/test-secret
```

**Step 3: Enable AppRole Authentication**

Terraform will use **AppRole authentication** to access Vault securely. Enable AppRole:

```sh
vault auth enable approle
```

Create a policy (terraform-policy.hcl) that grants read access to secrets:

```sh
path "kv/*" {
  capabilities = ["read"]
}
```

Apply the policy:

```sh
vault policy write terraform terraform-policy.hcl
```

Create an AppRole for Terraform:

```sh
vault write auth/approle/role/terraform \
    token_policies="terraform" \
    token_ttl=1h
```

Retrieve the **Role ID:**

```sh
vault read auth/approle/role/terraform/role-id
```

Retrieve the **Secret ID:**

```sh
vault write -force auth/approle/role/terraform/secret-id
```

---

**4. Integrating Terraform with HashiCorp Vault**

Now, let’s configure Terraform to retrieve secrets from Vault dynamically.

**Step 1: Create a Terraform Configuration**

Create a new Terraform directory and **main.tf** file:

```sh
mkdir terraform-vault-demo && cd terraform-vault-demo
touch main.tf
```

**Step 2: Define Providers**

```sh
provider "aws" {
  region = "us-east-1"
}

provider "vault" {
  address = "http://<EC2_PUBLIC_IP>:8200"
  skip_child_token = true
}
```

**Step 3: Authenticate with Vault**

```sh
data "vault_approle_auth_backend_login" "terraform" {
  role_id   = "<ROLE_ID>"
  secret_id = "<SECRET_ID>"
}
```

**Step 4: Retrieve the Secret**

```sh
data "vault_kv_secret_v2" "example" {
  mount = "kv"
  name  = "test-secret"
}
```

**Step 5: Use Secret in AWS EC2 Instance**

```sh
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0" # Replace with latest AMI ID
  instance_type = "t2.micro"

  tags = {
    secret_username = data.vault_kv_secret_v2.example.data["username"]
  }
}
```
---

**5. Deploying Terraform Configuration**

Run the following Terraform commands:

```sh
terraform init
terraform apply -auto-approve
```

Once applied, go to the **AWS EC2 console**, select your instance, and check the **Tags** section. You should see:

| Key              | Value          |
|-----------------|--------------|
| secret_username | admin        |

---

**6. Best Practices for Using HashiCorp Vault in Production**

For **production-grade** environments:

✔ **Use a dedicated Vault server with TLS encryption**

✔ **Avoid running Vault in dev mode** (use HA mode with storage backends like Consul or DynamoDB)

✔ **Use IAM roles instead of hardcoding credentials**

✔ **Enable logging and auditing for security compliance**

✔ **Use Vault’s dynamic secrets feature** for temporary credentials

---

**Conclusion**

We successfully integrated **Terraform with HashiCorp Vault**, allowing us to store and retrieve secrets securely. This setup enhances security, prevents credential leaks, and follows best practices in **DevOps, Cloud Security, and CI/CD pipelines**.
