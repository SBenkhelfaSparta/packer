## Packer Installation
- `curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -`
- `sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"`
- `sudo apt-get update && sudo apt-get install packer`

## Creating a Packer File for the Creation of an AMI
- Export your aws_secret_key and aws_access_key to your local environment to avoid others from seeing it
- Create a json file and enter this block of code:
```
{
  "variables": {
    "instance_size": "t2.micro",
    "ami_name": "eng89_salem_ami_packer",
    "base_ami": "ami-06e79b9a370267ca3",
    "ssh_username": "ubuntu",
    "vpc_id": "vpc-07e47e9d90d2076da",
    "subnet_id": "subnet-0429d69d55dfad9d2"
  },
  "builders": [
  {
    "type": "amazon-ebs",
    "region": "eu-west-1",
    "source_ami": "{{user `base_ami`}}",
    "instance_type": "{{user `instance_size`}}",
    "ssh_username": "{{user `ssh_username`}}",
    "access_key": "local.aws_access_key",
    "secret_key": "local.aws_secret_key",
    "ami_name": "{{user `ami_name`}}",
    "ssh_pty" : "true",
    "vpc_id": "{{user `vpc_id`}}",
    "subnet_id": "{{user `subnet_id`}}",
    "tags": {
    "Name": "App Name",
    "BuiltBy": "Packer"
    }
  }
]
}

```
This code copies an existing AMI. If you want to add provisioning code (which you most likely will) you can add a block like so:
```
"provisioners": [{
    "type": "shell"
    "script": "install.sh"
}
]
```
The "install.sh" just has to be in the same location as the json. If you want you can have inline code by using the line `inline: ["echo foo"]`
More information over at https://www.packer.io/docs/provisioners/shell
