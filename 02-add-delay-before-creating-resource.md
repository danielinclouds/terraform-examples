
# Add delay before creating resource

Simple example that shows how to add delay before creating a resource.

## Usecase

When creating aurora DB the DNS record is not ready imediately so creating schema in DB might fail.
The solution is to add delay between creating aurora DB and schema creation. 


## Solution 1
Use when cluster and schema creation is in single module.
```hcl
provider "aws" {
  region = "eu-west-2"
}

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

resource "null_resource" "delay" {
  provisioner "local-exec" {
    command = "sleep 10"
  }
  triggers = {
    "before" = "${aws_vpc.main.id}"
  }
}

resource "aws_vpc" "second" {
  cidr_block = "10.0.0.0/16"
  depends_on = [null_resource.delay]
}
```

## Solution 2
When schema creation is in different module than DB, it might be beneficial to delay DB module output.
```hcl
provider "aws" {
  region = "eu-west-2"
}

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

resource "null_resource" "delay" {
  provisioner "local-exec" {
    command = "sleep 20"
  }
  triggers = {
    "before" = aws_vpc.main.id
  }
}

output "vpc_id" {
  value = aws_vpc.main.id

  depends_on = [null_resource.delay]
}
```