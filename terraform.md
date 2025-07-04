## Terraform

---

### ✅ 1. What is Terraform and why do you use it?

🗣️ **Your Answer**:

> "Terraform is an open-source Infrastructure as Code tool developed by HashiCorp. I use it to provision and manage cloud infrastructure like AWS EC2, VPC, security groups, etc., in a repeatable and version-controlled way. It allows me to define infrastructure in a simple declarative language (HCL), and manage resources across multiple cloud providers using the same tool."

---

### ✅ 2. What is the difference between Terraform and Ansible?

🗣️ **Your Answer**:

> "Terraform is primarily used for provisioning infrastructure — like setting up EC2 instances or VPCs — whereas Ansible is more for configuration management — like installing packages or configuring applications. Terraform is declarative and maintains state, so it knows what’s already been deployed. Ansible is imperative and usually stateless, running tasks step by step."

---

### ✅ 3. What is HCL?

🗣️ **Your Answer**:

> "HCL stands for HashiCorp Configuration Language. It's the syntax Terraform uses to define infrastructure resources. It's both human-readable and machine-friendly, and makes it easy to describe resources like compute instances, networks, and databases."

---

### ✅ 4. What is a Terraform state file and why is it important?

🗣️ **Your Answer**:

> "The state file (terraform.tfstate) is where Terraform keeps track of the infrastructure it manages. It maps the current real-world infrastructure to my configuration files. This helps Terraform know what resources exist, and what changes need to be made during apply. Without the state file, Terraform wouldn't know the current status of the resources."

---

### ✅ 5. What happens if the Terraform state file is deleted?

🗣️ **Your Answer**:

> "If the state file is deleted, Terraform loses track of the resources it created. It won't be able to manage or destroy those resources safely. It may try to create new resources from scratch, which can lead to duplication or configuration drift. That’s why it's a best practice to store the state file securely and back it up."

---

### ✅ 6. Difference between terraform init and terraform apply?

🗣️ **Your Answer**:

> "terraform init is the first command I run in a new working directory. It initializes the directory, downloads the necessary provider plugins, and sets up the backend.
terraform apply, on the other hand, actually provisions the infrastructure defined in the .tf files. It applies the changes after showing me a detailed execution plan."

---

### ✅ 7. What are providers in Terraform?

🗣️ **Your Answer**:

> "Providers are plugins that Terraform uses to interact with cloud platforms and services. For example, the AWS provider allows me to create resources like EC2 or S3 in AWS. Terraform supports many providers like AWS, Azure, GCP, Kubernetes, and even third-party services."

---

### ✅ 8. How do you store Terraform state securely?

🗣️ **Your Answer**:

> "In production, I use remote backends like AWS S3 to store the state file. I also enable state locking with DynamoDB to prevent concurrent operations. The state file is encrypted at rest and during transit, which ensures security and consistency when working in teams."

---

## 🎯 Terraform Interview Questions – Phase 2 (Intermediate Level)

---

### ✅ 9. How do you use variables in Terraform?

🗣️ **Your Answer**:

> "I use variables in Terraform to make configurations dynamic and reusable.
I define variables in a variables.tf file like this:

```hcl
variable \"instance_type\" {
  default = \"t2.micro\"
}
````

Then, I pass actual values either in a terraform.tfvars file or through the CLI.
For example:

```hcl
instance_type = \"t3.small\"
```

Finally, I reference them using `var.instance_type` in my resource definitions. This makes it easy to switch between environments or resource configurations."

---

### ✅ 10. What are output values in Terraform and how do you use them?

🗣️ **Your Answer**:

> "Output values allow me to extract and display important information after a Terraform run — like the public IP of an EC2 instance.
> I define them in an `outputs.tf` file like:

```hcl
output \"instance_ip\" {
  value = aws_instance.example.public_ip
}
```

These outputs are helpful for documentation, debugging, or passing values between modules using `output` and `module.module_name.output_name`."

---

### ✅ 11. What are modules in Terraform? Have you worked with them?

🗣️ **Your Answer**:

> "Yes, I use modules to make Terraform code reusable and organized. A module is a self-contained block of Terraform configurations that I can reuse across environments.

For example, I created a VPC module and used it in multiple environments by referencing it like this:

```hcl
module \"vpc\" {
  source     = \"./modules/vpc\"
  cidr_block = var.vpc_cidr
}
```

Modules make large infrastructures easier to manage and reduce code duplication."

---

### ✅ 12. What is a remote backend in Terraform and why is it important?

🗣️ **Your Answer**:

> "A remote backend is used to store the Terraform state file in a centralized and secure location like AWS S3.
> This is especially important in team environments to avoid state file conflicts and allow collaboration.

I typically configure it like this:

```hcl
terraform {
  backend \"s3\" {
    bucket         = \"my-tf-state\"
    key            = \"env/dev/terraform.tfstate\"
    region         = \"us-east-1\"
    dynamodb_table = \"terraform-locks\"
    encrypt        = true
  }
}
```

This also includes state locking using DynamoDB to prevent simultaneous changes by different users."

---

### ✅ 13. What are workspaces in Terraform and when do you use them?

🗣️ **Your Answer**:

> "Workspaces in Terraform allow me to manage multiple state files using the same configuration. I use them for small-scale environment separation like dev, test, and prod.

For example:

```bash
terraform workspace new dev
terraform workspace select dev
terraform apply
```

Each workspace has its own state file. However, for larger setups, I prefer directory or file-based environment segregation with different variable files and backends."

---

### ✅ 14. Provision a Basic EC2 Instance in AWS

🎯 **Question**:
"Can you write a Terraform configuration to launch a single EC2 instance in the us-east-1 region with Ubuntu AMI and a tag 'Environment=Dev'?"

🗣️ **Your Answer**:

> **Terraform Code**:

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "dev_vm" {
  ami           = "ami-0c55b159cbfafe1f0"  # Ubuntu AMI (example)
  instance_type = "t2.micro"

  tags = {
    Name        = "DevVM"
    Environment = "Dev"
  }
}
```
### ✅ 15. What is the difference between Terraform and CloudFormation?

🗣️ **Your Answer**:

> "Terraform is a cloud-agnostic tool that can provision infrastructure across multiple cloud providers, whereas CloudFormation is specific to AWS. Terraform uses a declarative language (HCL) to define infrastructure, while CloudFormation uses JSON or YAML. Additionally, Terraform has a robust state management feature, whereas CloudFormation manages the state within AWS."

---

### ✅ 16. What is a Terraform plan?

🗣️ **Your Answer**:

> "A Terraform plan is an execution plan that Terraform generates before making any changes to the infrastructure. It shows a preview of the changes Terraform will make (add, modify, delete) based on the current state and the desired configuration. This helps in understanding the impact of changes before actually applying them."

---

### ✅ 17. How do you manage secrets (e.g., API keys, passwords) in Terraform?

🗣️ **Your Answer**:

> "I use services like AWS Secrets Manager or HashiCorp Vault to securely manage secrets in Terraform. These services can securely store secrets and provide them as environment variables or use them directly within Terraform by leveraging the `data` block to pull them into my configuration."

---

### **Basic Terraform Setup - Interview Q\&A**

---

**Q: What are the essential files in a basic Terraform setup?**

A:

1. **`main.tf`** – Defines the infrastructure resources (e.g., EC2 instances, VPC).
2. **`variables.tf`** – Declares variables used in the configuration.
3. **`terraform.tfvars`** – Contains values for the variables defined in `variables.tf`.
4. **`outputs.tf`** – Defines output values (e.g., instance IP).
5. **`provider.tf`** – Configures the cloud provider (e.g., AWS).

---

**Q: What does the `main.tf` file do in Terraform?**

A:
The `main.tf` file is where you define the infrastructure resources like EC2 instances, VPCs, etc. Example:

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  tags = {
    Name = "MyFirstEC2Instance"
  }
}
```

---

**Q: What is the purpose of the `variables.tf` file?**

A:
The `variables.tf` file declares variables for the project, making the configuration flexible and reusable. Example:

```hcl
variable "aws_region" {
  default = "us-east-1"
}

variable "instance_type" {
  default = "t2.micro"
}
```

---

**Q: What is the role of the `terraform.tfvars` file?**

A:
It contains values for the variables defined in `variables.tf`. This file is used for environment-specific values. Example:

```hcl
aws_region    = "us-east-1"
instance_type = "t2.micro"
```

---

**Q: How does the `outputs.tf` file work?**

A:
It defines output values that Terraform will display after applying the configuration, such as the public IP of an EC2 instance. Example:

```hcl
output "instance_public_ip" {
  value = aws_instance.example.public_ip
}
```

---

**Q: What is the purpose of the `provider.tf` file?**

A:
The `provider.tf` file configures the cloud provider (AWS, Azure, etc.). For example, in AWS:

```hcl
provider "aws" {
  region = "us-east-1"
}
```

---

### **Example Terraform Workflow**:

1. **Initialize Terraform**:

   ```bash
   terraform init
   ```

2. **Preview Changes**:

   ```bash
   terraform plan
   ```

3. **Apply Configuration**:

   ```bash
   terraform apply
   ```

4. **Retrieve Outputs**:

   ```bash
   terraform output
   ```

---

### **Summary**:

* **Simple Setup**: Minimal files (`main.tf`, `variables.tf`, `terraform.tfvars`, `outputs.tf`, `provider.tf`) for small environments.
* **Reusability**: Use variables and `terraform.tfvars` to manage configurations for different environments.
* **Clear Outputs**: Retrieve valuable information (e.g., instance IP) through outputs.

---

