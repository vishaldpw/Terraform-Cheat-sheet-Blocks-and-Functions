# 🧾 Terraform Blocks Cheat Sheet

| Block / Meta-Arg   | Purpose (Simplest Words)                                   | Example Use |
|---------------------|------------------------------------------------------------|-------------|
| **terraform**       | Configures Terraform itself (version, backend, providers). | `terraform { required_version = ">= 1.3" }` |
| **provider**        | Connects Terraform to a cloud/service.                     | `provider "aws" { region = "us-east-1" }` |
| **resource**        | Creates/updates/destroys infrastructure.                   | `resource "aws_instance" "vm" { ... }` |
| **data**            | Reads existing info without creating new resources.        | `data "aws_ami" "ubuntu" { ... }` |
| **variable**        | Input values (make code reusable).                         | `variable "instance_type" { default = "t2.micro" }` |
| **output**          | Prints results after apply.                                | `output "ip" { value = aws_instance.vm.public_ip }` |
| **locals**          | Helper values inside config.                               | `locals { env = "dev" }` |
| **module**          | Reuse configs like a function.                             | `module "vpc" { source = "terraform-aws-modules/vpc/aws" }` |
| **backend** (inside `terraform`) | Where state is stored (local/S3/etc.).        | `backend "s3" { bucket = "mybucket" }` |
| **provisioner**     | Run scripts after resource create/destroy (not recommended). | `provisioner "remote-exec" { inline = ["sudo apt install nginx -y"] }` |
| **connection**      | Defines how to connect (SSH/WinRM) for provisioners.       | `connection { type = "ssh" user = "ec2-user" }` |
| **dynamic**         | Generate nested blocks with loops.                         | `dynamic "ingress" { for_each = var.rules ... }` |
| **moved**           | Tell Terraform a resource was renamed/moved (avoid re-creation). | `moved { from = aws_instance.old to = aws_instance.new }` |
| **import**          | Bring an existing resource into Terraform state.           | `import { id = "i-12345", to = aws_instance.vm }` |
| **check**           | Verify conditions after apply.                             | `check "url" { assert { condition = can(curl ...) } }` |
| **lifecycle** (inside resource) | Fine-tune behavior: keep, replace, ignore changes. | `lifecycle { prevent_destroy = true }` |
| **depends_on**      | Force order of resource creation.                          | `depends_on = [aws_iam_role.role]` |
| **count**           | Create multiple resources by number.                       | `count = 3` → makes 3 EC2s |
| **for_each**        | Create resources from a map/list.                          | `for_each = toset(var.names)` |

---

## ⚡ Quick Memory Tip
- **Terraform / provider / backend** → settings.  
- **Resource / data / module** → build & reuse infra.  
- **Variable / locals / output** → inputs & outputs.  
- **Provisioner / connection / dynamic** → advanced tweaks.  
- **Moved / import / check / lifecycle / depends_on** → special handling.  
- **Count / for_each** → loops for infra.
# terraform-Cheat-sheet-Blocks
terraform-Cheat-sheet For quick reference for Blocks
