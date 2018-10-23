
# the files in this project are referenced during a terraform apply or destroy #

these files set variables/values for each terraform run, but for different purposes.  here's a quick breakdown;

* terraform-environment.json - these are high level vars/values used by ALL terraform modules during the terraform run, 
    additionally you may order the create/destroy of services 
* terraform-aws-${application-name} - these are high level vars/values used by their associated terraform modules during
    a terraform run
* interface.tf (found in each terraform module) - these are lower level vars/values used by the specific module during a 
    terraform run
* ${filename}.tf (found in each terraform module) - many of the terraform tf files use and/or set variables specific to the
    resources defined within 

# terraform-environment.json #
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


# terraform-environment.json details #
```
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
      "username": "user1",  <**------- change this to YOUR username
      "subdomain": "user1",
      "domain": "media.dev.usa.reachlocalservices.com",
      "VAULTKEY": "7d23ll1-3992-7639-93aa-2f48b952ce13",   <**-------- change this to YOUR vaultkey
      "vpc_id": "vpc-e9be9a8e"
    }
  ],
  **"_comment_modules": "order is important: built from top to bottom; destroyed from bottom to top",**
  "services": [
    {
      "serviceName": "terraform-aws-route53-subdomain",   <**----- this will be created before the serviceName below
      "state": "enabled"
    },
    {
      "serviceName": "terraform-aws-certificates",  <**------- this will be destroyed before the serviceName above
      "state": "enabled"
    },
    {
      "serviceName": "terraform-aws-vault",
      "state": "ignore"  <**------ this will be ignored during a terraform apply or destroy
    }
}
```


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

# application json details #
```{
"-_comment_env_configs": "environment or platform specific overrides",
"serviceName": "terraform-aws-madmin-client",
"consul": [  <**---- variables in the consul list will be set in consul**
  {
  "branch": "dockerizing",    <**----- the branch variable is used in several places around the devops environment**
  "CLIENT_BASE_URL_AUS": "https://madmin-client.$${subdomain}.media.$${env}.aus.reachlocalservices.com",
  "CLIENT_BASE_URL_CAN": "https://madmin-client.$${subdomain}.media.$${env}.can.reachlocalservices.com",
  "CLIENT_BASE_URL_EUR": "https://madmin-client.$${subdomain}.media.$${env}.eur.reachlocalservices.com",
  "CLIENT_BASE_URL_GBR": "https://madmin-client.$${subdomain}.media.$${env}.gbr.reachlocalservices.com",
  "CLIENT_BASE_URL_JPN": "https://madmin-client.$${subdomain}.media.$${env}.jpn.reachlocalservices.com",
  "CLIENT_BASE_URL_USA": "https://madmin-client.$${subdomain}.media.$${env}.usa.reachlocalservices.com",
  "MEDIA_GATEWAY_BASE_URL": "http://media-gateway.$${fqdn}",
  "ADMIN_PORTAL_URL_AUS": "https://platweb-aus.$${env}.reachlocal.com",
  "ADMIN_PORTAL_URL_CAN": "https://platweb-can.$${env}.reachlocal.com",
  "ADMIN_PORTAL_URL_EUR": "https://platweb-eur.$${env}.reachlocal.com",
  "ADMIN_PORTAL_URL_GBR": "https://platweb-gbr.$${env}.reachlocal.com",
  "ADMIN_PORTAL_URL_JPN": "https://platweb-jpn.$${env}.reachlocal.com",
  "ADMIN_PORTAL_URL_USA": "https://platweb-usa.$${env}.reachlocal.com",
  "CORP_PORTAL_URL": "https://corp.$${env}.reachlocal.com",
  "SSO_SIGNOUT_URL": "https://sso$${env}.reachlocal.com/adfs/ls/RLSignout.aspx",
  "SALES_FORCE_BASE_URL": "https://test.salesforce.com",
  "NEWRELIC_LICENSE_KEY": "fe42e623fe",
  "NEWRELIC_APPLICATION_ID": "110049921",
  "INTERCOM_APPLICATION_ID": "x934uzl0",
  "YAHOO_ADPLATFORM_QUICKLINK_LABEL": "l10n-quick-link.yahooGeminiLogin",
  "YAHOO_ADPLATFORM_QUICKLINK_URL": "https://login.yahoo.com/config/login_verify2?.intl=us&&.src=gemini&.pd=c%3DDP7Q1..72e53flgv6OCrT4Lchg--&.done=https://gemini.yahoo.com/advertiser/home?.done=https:%2F%2Fgemini.yahoo.com%2F.scrumb=0",
  "MEDIA_CORE_UI_URL": "https://media-core-ui.media.dev.usa.reachlocalservices.com"
  }
],
"ecs": [
  {
    "container_CPU": "1024",   <**---- here we set the cpu quantity for the madmin-client container in this environment 
    "container_MEMORY": "2048"
  }
]
}
```

# terraform interface.tf example (from terraform-aws-madmin-client/interface.tf) #
```
// inputs (consul) - if a value is the same no matter the environment and/or platform, set it here
variable "AWS_ACCOUNT_NUMBER"               { default = "" }
variable "FQDN"                             { default = "" }
variable "PLATFORM"                         { default = "" }
variable "ENVIRONMENT"                      { default = "" }
variable "REGION"                           { default = "" }
variable "CLIENT_BASE_URL_AUS"              { default = "" }
variable "CLIENT_BASE_URL_CAN"              { default = "" }
variable "CLIENT_BASE_URL_EUR"              { default = "" }
variable "CLIENT_BASE_URL_GBR"              { default = "" }
variable "CLIENT_BASE_URL_JPN"              { default = "" }
variable "CLIENT_BASE_URL_USA"              { default = "" }
variable "MEDIA_GATEWAY_BASE_URL"           { default = "" }
variable "ADMIN_PORTAL_URL_AUS"             { default = "" }
variable "ADMIN_PORTAL_URL_CAN"             { default = "" }
variable "ADMIN_PORTAL_URL_EUR"             { default = "" }
variable "ADMIN_PORTAL_URL_GBR"             { default = "" }
variable "ADMIN_PORTAL_URL_JPN"             { default = "" }
variable "ADMIN_PORTAL_URL_USA"             { default = "" }
variable "CORP_PORTAL_URL"                  { default = "" }
variable "SSO_SIGNOUT_URL"                  { default = "" }
variable "SALES_FORCE_BASE_URL"             { default = "" }
variable "NEWRELIC_LICENSE_KEY"             { default = "" }
variable "NEWRELIC_APPLICATION_ID"          { default = "" }
variable "INTERCOM_APPLICATION_ID"          { default = "" }
variable "LOG_LEVEL"                        { default = "INFO" }  <**----- this is the same in all environments but may be overridden for specific environment configurations via the "per application json configuration"
variable "YAHOO_ADPLATFORM_QUICKLINK_LABEL" { default = "" }
variable "YAHOO_ADPLATFORM_QUICKLINK_URL"   { default = "" }
variable "MEDIA_CORE_UI_URL"                { default = "" }
```

