# Terraform-Troubleshooting-Commands

The syntax for targeting a specific resource when running terraform apply is as follows:
terraform apply -target=resource_type.resource_name

Ex: **terraform apply -target="aws_s3_bucket.exampleurse"**
