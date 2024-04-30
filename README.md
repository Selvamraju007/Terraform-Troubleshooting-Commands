# Terraform-Troubleshooting-Commands

**Apply specific file in Terraform**
==============================================

The syntax for targeting a specific resource when running terraform apply is as follows:

terraform apply -target=resource_type.resource_name

Ex: **terraform apply -target="aws_s3_bucket.exampleurse"**

**Migrate .tfstate from local to S3 bucket:**
=============================================

**terraform init -migrate-state**

**Output**:

Initializing the backend...
Acquiring state lock. This may take a few moments...
Do you want to copy existing state to the new backend?
  Pre-existing state was found while migrating the previous "local" backend to the
  newly configured "s3" backend. No existing state was found in the newly
  configured "s3" backend. Do you want to copy this state to the new "s3"
  backend? Enter "yes" to copy and "no" to start with an empty state.

  Enter a value: yes

 Deleted the resource from AWS console manually and try to run terraform apply then check the status for s3 versioning check:
 ================================================================================================================================

EX: RDS db was deleted from aws console which is created by terraform. 

terraform state file had the content and terraform apply recreated the rds db resource. 

S3 versioning has nothing to do with terraform state file
**Note: Before the resource deletion we have enabled S3 versioning for testing purpose.**

**Match both environments (AWS and Terraform):**
=======================================================

**terraform plan -refresh-only**

We have done a instance type changes on aws_launch_template.auto-scaling-group from t2.micro to t2.nano for testing purpose.

Output:

    aws_launch_template.auto-scaling-group has changed
    ~ resource "aws_launch_template" "auto-scaling-group" {
      + description             = "test-terraform"
        id                      = "lt-0a5ef2ae49a80f01b"
      ~ instance_type           = "t2.micro" -> "t2.nano"
      ~ latest_version          = 1 -> 2
        name                    = "auto-scaling-group20240429124429746800000001"
      + security_group_names    = []
      + tags                    = {}
      + vpc_security_group_ids  = []
        # (8 unchanged attributes hidden)

      ~ network_interfaces {
          + ipv4_addresses     = []
          + ipv4_prefixes      = []
          + ipv6_addresses     = []
          + ipv6_prefixes      = []
            # (8 unchanged attributes hidden)
        }
    }



**Terraform plan:** **(without refresh only)**
==================================

Output:

Terraform plan:
===============
	
	# aws_launch_template.auto-scaling-group will be updated in-place
        ~ resource "aws_launch_template" "auto-scaling-group" {
       - description             = "test-terraform" -> null
        id                      = "lt-0a5ef2ae49a80f01b"
       ~ instance_type           = "t2.nano" -> "t2.micro"
       ~ latest_version          = 2 -> (known after apply)
        name                    = "auto-scaling-group20240429124429746800000001"
        tags                    = {}
        # (10 unchanged attributes hidden)

        # (1 unchanged block hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

**terraform apply -refresh-only:**
==================================

**Output**:

Would you like to update the Terraform state to reflect these detected changes?
  Terraform will write these changes to the state without modifying any real infrastructure.
  There is no undo. Only 'yes' will be accepted to confirm.

  **terraform taint:**
  ===========================

  The terraform taint command informs Terraform that a particular object has become degraded or damaged. Terraform represents this by marking the object as "tainted" in the Terraform state, and Terraform will propose to replace it in the next plan you create.

  Ex: **terraform apply -taint="aws_launch_template.auto-scaling-group"**

  
