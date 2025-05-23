# Terraform EC2 Instance and AMI Creation

## Overview

This project demonstrates how to use [Terraform](https://www.terraform.io/) to automate the provisioning of an EC2 instance on AWS and create an Amazon Machine Image (AMI) from that instance. The primary goal is to understand infrastructure as code (IaC) principles and gain hands-on experience with Terraform and AWS.

---

## Objectives

- Write and structure basic Terraform configuration files.
- Provision an EC2 instance on AWS.
- Install software on the instance using Terraform provisioners.
- Create an AMI from the configured EC2 instance.

---

## Prerequisites

Ensure the following tools are installed and configured:

- [Terraform](https://developer.hashicorp.com/terraform/downloads)
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
- AWS account with access credentials configured (`aws configure`)
- An existing EC2 key pair in the target region (for SSH access)
- A valid public AMI ID for the specified region (e.g., Ubuntu or Amazon Linux)

---

## Project Structure

```bash
terraform-ec2-ami/
├── main.tf         
└── README.md        
```

## Configuration and Deployment
1. Clone the Project
```bash
git clone https://github.com/Danny-Flames/terraform-ec2-ami.git
cd terraform-ec2-ami
```


2. I created and edited main.tf
I replaced placeholder values like my-key-pair and the AMI ID with actual values from my AWS environment.

```bash
provider "aws" {
  region = "eu-north-1"
}

resource "aws_instance" "example_instance" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  key_name      = "my-key-pair"

  tags = {
    Name = "TerraformExampleInstance"
  }

  provisioner "remote-exec" {
    inline = [
      "sudo apt update -y",
      "sudo apt install -y nginx"
    ]

    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("~/.ssh/my-key.pem")
      host        = self.public_ip
    }
  }
}

resource "aws_ami" "example_ami" {
  name        = "example-ami"
  description = "AMI created from Terraform-managed EC2 instance"
  instance_id = aws_instance.example_instance.id
  depends_on  = [aws_instance.example_instance]
}
```


3. Initialize Terraform
```bash
terraform init
```

This command downloads the necessary provider plugins.


4. Apply the Configuration
```bash
terraform apply
```

I typed yes when prompted to confirm the resource creation. This will:
- Launch the EC2 instance.
- SSH into the instance and install NGINX.
- Create a new AMI from the instance.


5. Verify Resources
- I went to my AWS EC2 Dashboard
- Confirm the new instance and AMI are listed and available.


6. Destroy Resources (Optional)
To clean up and avoid ongoing charges:
```bash
terraform destroy
```