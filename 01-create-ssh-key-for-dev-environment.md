# Create ssh key for Dev environment

Create SSH key private and public material and save it to file. Use SSH public key to create AWS key pair.

:warning: Use this method only in development environment.

**Pros:**
* easy creation of ssh keys  

**Cons:**
* ssh keys visible in state and plan  


## Solution
```hcl
provider "aws" {
  region = "eu-west-2"
}

resource "tls_private_key" "ssh_key" {
  algorithm = "RSA"
}

resource "local_file" "file_ssh_key_priv" {
  content  = "${tls_private_key.ssh_key.private_key_pem}"
  filename = "${path.module}/key.pem"
}

resource "local_file" "file_ssh_key_pub" {
  content  = "${tls_private_key.ssh_key.public_key_openssh}"
  filename = "${path.module}/key.pem.pub"
}

resource "aws_key_pair" "ssh_key" {
  key_name   = "ssh-key"
  public_key = "${tls_private_key.ssh_key.public_key_openssh}"
}
```