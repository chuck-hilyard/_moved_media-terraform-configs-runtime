

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
{\
  "_comment_env_configs": "high level environment configuration for the terraform run",\
  "environment": [\
    {\
      "aws_account_number": "762858336698",\
      "aws_region": "us-west-2",\
      "environment": "dev",\
      "platform": "usa",\
      "web_subnets": ["subnet-8497e4e3", "subnet-cab32683", "subnet-f2a84ba9"],\
      "app_subnets": ["subnet-a295e6c5", "subnet-63b92c2a", "subnet-0fb25154"],\
      "db_subnets": [],\
      "admin_subnets": [],\
      "on_prem_dns": "10.10.21.1",\
      "newrelic_license": "",\
      "security_groups": ["sg-57c9162d"],\
      "username": "user1",  <------- change this to YOUR username\
      "subdomain": "user1",\
      "domain": "media.dev.usa.reachlocalservices.com",\
      "VAULTKEY": "7d23ll1-3992-7639-93aa-2f48b952ce13",   <-------- change this to YOUR vaultkey\
      "vpc_id": "vpc-e9be9a8e"\
    }\
  ],\
  "_comment_modules": *"order is important: built from top to bottom; destroyed from bottom to top",\
  "services"*: [\
    {\
      "serviceName": "terraform-aws-route53-subdomain",   <----- this will be created before the serviceName below\
      "state": "enabled"\
    },\
    {\
      "serviceName": "terraform-aws-certificates",  <------- this will be destroyed before the serviceName above\
      "state": "enabled"\
    },\
    {\
      "serviceName": "terraform-aws-vault",\
      "state": "ignore"  <------ this will be ignored during a terraform apply or destroy\
    }\
}\
