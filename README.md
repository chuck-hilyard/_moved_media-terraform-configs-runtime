

# the files in this directory are referenced during a terraform apply or destroy #

these files set variables/values for each terraform run, but for different purposes.  here's a quick breakdown;

* [terraform-environment.json](#terraform-environment.json) - these are high level vars/values used by ALL terraform modules during the terraform run
* terraform-aws-${application-name} - these are high level vars/values used by their associated terraform modules during
    terraform run
* interface.tf (found in each terraform module) - these are lower level vars/values used by the specific module during its
    execution.
* ${filename}.tf (found in each terraform module) - many of the terraform tf files use and/or set variables specific to the
    resources defined within them

#terraform-environment.json#
this is the high level environment config used for the each terraform run

here you can set environment vars/values AND the order of create/destroy runs for the platform

**environment** (list): these are used by all modules during this terraform run, if there's a naming conflict vars/values set here
  win

**services** (list): these callout the modules that'll be used during the terraform run, the order in which they'll be created or
  destroyed, and what the modules state is during the run

**states:**
  * enabled - will create or update
  * disabled - will destroy if it exists
  * ignore - executes no action


# terraform-environment.json config file notes #
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
  **"_comment_modules": "order is important: built from top to bottom; destroyed from bottom to top",**\
  "services": [\
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



# per application json configuration - allowed variables #

these are var/value overrides for the respective terraform module.  the filename here should match the folder name under the main
terraform directory, eg.   terraform-aws-madmin-client.json and ../terraform-aws-madmin-client

to make these configs more portable and require less typing there is a syntax that may be employed:
**variable syntax is $${variable_name}** 

the following variables may be used in the config
* $${environment}
* $${platform}
* $${domain}
* $${subdomain}
* $${fqdn}

