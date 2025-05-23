# Create a three tier AWS VPC network

This terraform module will deploy the following services:
- VPC
  - Subnets
  - Internet Gateway
  - NAT Gateway
  - Route Tables
  - NACLs
  - Security Groups
  - Flow Logs
- Route53
  - Private Hosted Zone
- CloudWatch
  - Log Group
- S3

## Licence:
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

MIT Licence. See [Licence](LICENCE) for full details.

# Usage Instructions:

## Example:
```terraform
module "vpc" {
  source = "github.com/terrablocks/aws-three-tier-network.git"

  network_name     = "pvt-network"
  azs              = ["us-east-1a", "us-east-1b", "us-east-1c"]
  pub_subnet_mask  = 24
  pvt_subnet_mask  = 22
  data_subnet_mask = 24
}
```
## Variables
| Parameter             | Type    | Description                                                               | Default                      | Required |
|-----------------------|---------|---------------------------------------------------------------------------|------------------------------|----------|
| cidr_block            | string  | CIDR block for VPC                                                        | 10.0.0.0/16                  | N        |
| network_name          | string  | Name to be used for VPC resources                                         |                              | Y        |
| azs                   | list    | List of availability zones to be used for launching resources             | ["us-east-1a", "us-east-1b"] | N        |
| pub_subnet_mask       | string  | Subnet mask to use for public subnet                                      | 24                           | N        |
| pvt_subnet_mask       | string  | Subnet mask to use for private subnet                                     | 24                           | N        |
| data_subnet_mask      | string  | Subnet mask to use for data subnet                                        | 24                           | N        |
| create_pvt_nat            | boolean | Whether to create NAT gateway for private subnet                                     | true                           | N        |
| create_data_nat            | boolean | Whether to create NAT gateway for data subnet                                     | true                           | N        |
| create_flow_logs      | boolean | Whether to enable flow logs for VPC                                     | true                           | N        |
| flow_logs_destination | string  | Destination to store VPC flow logs. Possible values: s3, cloud-watch-logs | cloud-watch-logs             | N        |
| flow_logs_cw_log_group_arn | string  | ARN of CloudWatch log group to send VPC flow logs to. Leave it blank to create a new log group   |     | N        |
| flow_logs_bucket_arn | string  | ARN of S3 bucket to store VPC flow logs. Leave it blank to create a new bucket    |     | N        |
| create_private_zone          | boolean | Whether to create private hosted zone for VPC                             | false                        | N        |
| private_zone_domain   | string  | Domain name to be used for private hosted zone                            | server.internal.com          | N        |
| create_sgs   | boolean  | Whether to create default security groups (public, private, internal and ssh)                            | false          | N        |
| tags   | map  | Map of key-value pair to associate with resources             |          | N        |
| add_eks_tags   | boolean  | Add `kubernetes.io/role/elb: 1` and `kubernetes.io/role/internal-elb: 1` tags to respective subnets for load balancer            | false          | N        |

## Outputs
| Parameter            | Type   | Description                                                      |
|----------------------|--------|------------------------------------------------------------------|
| id               | string | ID of VPC created                                                |
| cidr               | string | CIDR block of VPC created                                                |
| public_subnet_ids     | list   | ID of public subnet(s) created                                   |
| public_subnet_cidrs  | list   | CIDR block of public subnet(s) created                           |
| private_subnet_ids    | list   | ID of private subnet(s) created                                  |
| private_subnet_cidrs | list   | CIDR block of private subnet(s) created                          |
| data_subnet_ids       | list   | ID of data subnet(s) created                                  |
| data_subnet_cidrs    | list   | CIDR block of data subnet(s) created                          |
| nat_public_ip        | string | Elastic IP of NAT gateway                                        |
| internal_sg          | string | Security group ID for internal communication                     |
| ssh_only_sg          | string | Security group ID for accepting only SSH connection              |
| public_web_dmz_sg    | string | Security group ID for public facing web servers or load balancer |
| private_web_dmz_sg   | string | Security group ID for internal web/app servers                   |
| private_zone_id      | string | Route53 private hosted zone id                                   |
| private_zone_ns      | list   | List of private hosted zone name servers                         |

## Deployment
- `terraform init` - download plugins required to deploy resources
- `terraform plan` - get detailed view of resources that will be created, deleted or replaced
- `terraform apply -auto-approve` - deploy the template without confirmation (non-interactive mode)
- `terraform destroy -auto-approve` - terminate all the resources created using this template without confirmation (non-interactive mode)
