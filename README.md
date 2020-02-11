# Yak Tutorial (Your AWS Kredentials)

[yak](https://github.com/redbubble/yak) is a tool that allows users to generate AWS access tokens via Okta.

## Installation
### Mac
Using Homebrew run the follwing commands
```
brew tap redbubble/yak
brew install yak
```

### Linux
Consult [github](https://github.com/redbubble/yak) repo for directions 

No windows support yet.

## Initial Setup
1. Create a yak config file
```
mkdir -p ~/.yak
cat <<EOF >>~/.yak/config.toml
[okta]
domain = "https://<<domain>>.okta.com"
aws_saml_endpoint = "/app/amazon_aws/<<>>/sso/saml"
username = "OKTA_USERNAME"
[aws]
session_duration = 3600
EOF
```
2. Call Yak List roles command, you will be prompted for your OKTA password
```
yak --list-roles
```
list of available IAM roles should appear
```
    arn:aws:iam::<<account>>:role/role-admins
    arn:aws:iam::<<account>>:role/role-admins
    arn:aws:iam::<<account>>:role/group-admins
    ...
```
3. You now can add the desired IAM roles as aliases to your `~/.yak/config.toml` file as shown below
```
[okta]
domain = "https://<<domain>>.okta.com"
aws_saml_endpoint = "/app/amazon_aws/<<>>/sso/saml"
username = "OKTA_USERNAME"
[aws]
session_duration = 3600
[alias]
cxd-dev = "arn:aws:iam::<<account>>:role/role-admins"
cxd-sandbox = "arn:aws:iam::<<account>>:role/role-admins"
cxd-prod = "arn:aws:iam::<<account>>:role/group-admins"
```

4. You can now authenticate to the desired role

```
» yak account-dev
password:
export AWS_ACCESS_KEY_ID=XXXXXXXXXXXX
export AWS_SECRET_ACCESS_KEY=XXXXXXXXXXXXX
export AWS_SESSION_TOKEN=XXXXXXXXXXXXXXXXXXXX
```
5. You have two different ways of using tokens now
-  use yak to run the command directly 
```
» yak cxd-dev aws sts get-caller-identity
{
    "UserId": "XXXXXXXXXXX:",
    "Account": "",
    "Arn": "arn:aws:sts:::assumed-role/role-admins/"
}
```
- inject tokens into the current ENV
```
» eval $(yak account-dev)
» aws sts get-caller-identity

```
