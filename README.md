

# terraform-environment.json #
this is the high level environment config used for the each terraform run

here you can set environment values and order of create/destroy for the platform
* enabled - will create or update
* disabled - will destroy
* ignore - does not execute any action


# per application json configuration - allowed variables #
## variable syntax is $${variable_name} ##
* environment
* platform
* domain
* subdomain
* fqdn


# anatomy of the terraform-environment.json config file #
{
  "_comment_env_configs": "high level environment configuration for the terraform run",
  "environment": [
    {
      "aws_account_number": "762858336698",
      "aws_region": "us-west-2",
      "environment": "dev",
      "platform": "usa",
      "web_subnets": ["subnet-8497e4e3", "subnet-cab32683", "subnet-f2a84ba9"],
      "app_subnets": ["subnet-a295e6c5", "subnet-63b92c2a", "subnet-0fb25154"],
      "db_subnets": [],
      "admin_subnets": [],
      "on_prem_dns": "10.10.21.1",
      "newrelic_license": "",
      "security_groups": ["sg-57c9162d"],
      "username": "user1",
      "subdomain": "user1",
      "domain": "media.dev.usa.reachlocalservices.com",
      "VAULTKEY": "7da38c81-6bcb-7639-05cd-2f48b952ce13",
      "vpc_id": "vpc-e9be9a8e"
    }
  ],
  "_comment_modules": "order is import: built from top to bottom; destroyed from bottom to top",
  "services": [
    {
      "serviceName": "terraform-aws-route53-subdomain",
      "state": "enabled"
    },
    {
      "serviceName": "terraform-aws-certificates",
      "state": "enabled"
    }
}
