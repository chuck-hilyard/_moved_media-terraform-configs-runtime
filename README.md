

## terraform-environment.json ## 
this is the high level environment config used for the each terraform run

here you can set environment values and order of create/destroy for the platform
enabled - will create or update
disabled - will destroy
ignore - does not execute any action


## per application json configuration - allowed variables ##
# variable syntax is $${variable_name} #
environment
platform
subdomain
fqdn
