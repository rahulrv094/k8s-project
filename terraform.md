## terraform

---

✅ 1. What is Terraform and why do you use it?

🗣️ Your Answer:

> "Terraform is an open-source Infrastructure as Code tool developed by HashiCorp. I use it to provision and manage cloud infrastructure like AWS EC2, VPC, security groups, etc., in a repeatable and version-controlled way. It allows me to define infrastructure in a simple declarative language (HCL), and manage resources across multiple cloud providers using the same tool."


---

✅ 2. What is the difference between Terraform and Ansible?

🗣️ Your Answer:

> "Terraform is primarily used for provisioning infrastructure — like setting up EC2 instances or VPCs — whereas Ansible is more for configuration management — like installing packages or configuring applications. Terraform is declarative and maintains state, so it knows what’s already been deployed. Ansible is imperative and usually stateless, running tasks step by step."


---

✅ 3. What is HCL?

🗣️ Your Answer:

> "HCL stands for HashiCorp Configuration Language. It's the syntax Terraform uses to define infrastructure resources. It's both human-readable and machine-friendly, and makes it easy to describe resources like compute instances, networks, and databases."


---

✅ 4. What is a Terraform state file and why is it important?

🗣️ Your Answer:

> "The state file (terraform.tfstate) is where Terraform keeps track of the infrastructure it manages. It maps the current real-world infrastructure to my configuration files. This helps Terraform know what resources exist, and what changes need to be made during apply. Without the state file, Terraform wouldn't know the current status of the resources."


---

✅ 5. What happens if the Terraform state file is deleted?

🗣️ Your Answer:

> "If the state file is deleted, Terraform loses track of the resources it created. It won't be able to manage or destroy those resources safely. It may try to create new resources from scratch, which can lead to duplication or configuration drift. That’s why it's a best practice to store the state file securely and back it up."


---

✅ 6. Difference between terraform init and terraform apply?

🗣️ Your Answer:

> "terraform init is the first command I run in a new working directory. It initializes the directory, downloads the necessary provider plugins, and sets up the backend.
terraform apply, on the other hand, actually provisions the infrastructure defined in the .tf files. It applies the changes after showing me a detailed execution plan."


---

✅ 7. What are providers in Terraform?

🗣️ Your Answer:

> "Providers are plugins that Terraform uses to interact with cloud platforms and services. For example, the AWS provider allows me to create resources like EC2 or S3 in AWS. Terraform supports many providers like AWS, Azure, GCP, Kubernetes, and even third-party services."

---

✅ 8. How do you store Terraform state securely?

🗣️ Your Answer:

> "In production, I use remote backends like AWS S3 to store the state file. I also enable state locking with DynamoDB to prevent concurrent operations. The state file is encrypted at rest and during transit, which ensures security and consistency when working in teams."

---

## 🎯 Terraform Interview Questions – Phase 2 (Intermediate Level)

---


✅ 1. How do you use variables in Terraform?

🗣️ Your Answer:

> "I use variables in Terraform to make configurations dynamic and reusable.
I define variables in a variables.tf file like this:

variable \"instance_type\" {
  default = \"t2.micro\"
}

Then, I pass actual values either in a terraform.tfvars file or through the CLI.
For example:

instance_type = \"t3.small\"

Finally, I reference them using var.instance_type in my resource definitions. This makes it easy to switch between environments or resource configurations."


---

✅ 2. What are output values in Terraform and how do you use them?

🗣️ Your Answer:

> "Output values allow me to extract and display important information after a Terraform run — like the public IP of an EC2 instance.
I define them in an outputs.tf file like:

output \"instance_ip\" {
  value = aws_instance.example.public_ip
}

These outputs are helpful for documentation, debugging, or passing values between modules using output and module.module_name.output_name."


---

✅ 3. What are modules in Terraform? Have you worked with them?

🗣️ Your Answer:

> "Yes, I use modules to make Terraform code reusable and organized. A module is a self-contained block of Terraform configurations that I can reuse across environments.

For example, I created a VPC module and used it in multiple environments by referencing it like this:

module \"vpc\" {
  source     = \"./modules/vpc\"
  cidr_block = var.vpc_cidr
}

Modules make large infrastructures easier to manage and reduce code duplication."


---

✅ 4. What is a remote backend in Terraform and why is it important?

🗣️ Your Answer:

> "A remote backend is used to store the Terraform state file in a centralized and secure location like AWS S3.
This is especially important in team environments to avoid state file conflicts and allow collaboration.

I typically configure it like this:

terraform {
  backend \"s3\" {
    bucket         = \"my-tf-state\"
    key            = \"env/dev/terraform.tfstate\"
    region         = \"us-east-1\"
    dynamodb_table = \"terraform-locks\"
    encrypt        = true
  }
}

This also includes state locking using DynamoDB to prevent simultaneous changes by different users."


---

✅ 5. What are workspaces in Terraform and when do you use them?

🗣️ Your Answer:

> "Workspaces in Terraform allow me to manage multiple state files using the same configuration. I use them for small-scale environment separation like dev, test, and prod.

For example:

terraform workspace new dev
terraform workspace select dev
terraform apply

Each workspace has its own state file. However, for larger setups, I prefer directory or file-based environment segregation with different variable files and backends."


---
✅ 6. Provision a Basic EC2 Instance in AWS
🎯 Question:
"Can you write a Terraform configuration to launch a single EC2 instance in us-east-1 region with Ubuntu AMI and a tag 'Environment=Dev'?"
Answer (Terraform Code):
'''
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
'''
Explanation for Interview:
“This is a basic EC2 setup. The AMI is region-specific. In a real project, I’d parameterize region and instance type with variables.”



