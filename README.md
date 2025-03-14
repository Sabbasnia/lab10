


Before launching packer, I needed to **import an SSH public key** into AWS.

#### **Command:**
```bash
./scripts/import_lab_key ~/.ssh/aws-4640.pub
```


Next, I used **Packer** to create a custom AMI with **Ansible provisioning**.

#### **Command:**
```bash
cd packer
packer init .
packer validate ansible-web.pkr.hcl
packer build ansible-web.pkr.hcl
```



Instead of directly creating an EC2 instance, I use the above files in the module.

#### **Key Changes:**
- **Removed** `aws_instance "web"` from `main.tf`
- **Created a module** in `terraform/modules/web-server/` with also creating three files of the variables.tf outputs.tf main.tf
- please look into my three files in my repo to verify the contents 
- **Updated `main.tf`** to use the three files under the module:
- **Updated `main.tf`** in the root module to use the web-server module:

```hcl
module "web-server" {
  source                  = "./modules/web-server"
  project_name            = local.project_name
  ami                     = data.aws_ami.ansible-nginx.id
  instance_type           = "t2.micro"
  key_name                = "aws-4640"
  vpc_security_group_ids  = [aws_security_group.web.id]
  subnet_id               = aws_subnet.web.id
}
```



#### **Command:**
```bash
cd terraform
terraform init
terraform validate
```

---


#### **Command:**
```bash
terraform apply
```



Once Terraform completed, I verified that the EC2 instance was running.

#### **Command:**
```bash
aws ec2 describe-instances --filters "Name=tag:Name,Values=Web"
```



After completing the lab, I destroyed all AWS resources.

#### **Command:**
```bash
terraform destroy
```


./scripts/delete_lab_key
```




