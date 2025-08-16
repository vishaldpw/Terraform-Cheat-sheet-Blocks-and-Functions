# 🧾 Terraform  Cheat Sheet

Terraform code is made of **blocks**. Each block has a purpose:  
some set **settings**, some **build infra**, some **control behavior**.

---

## 🔹 Core Blocks (Most Used)

| Block / Meta-Arg   | What It Does (Simple Words) | Example Use |
|---------------------|-----------------------------|-------------|
| **terraform**       | Settings for Terraform itself (version, providers, backend). | `terraform { required_version = ">= 1.3" }` |
| **provider**        | Connects Terraform to a cloud/service (AWS, Azure, GCP, GitHub…). | `provider "aws" { region = "us-east-1" }` |
| **resource**        | The real "things" Terraform builds (VMs, S3 buckets, VPCs…). | `resource "aws_instance" "vm" { ... }` |
| **data**            | Reads existing resources (without creating new ones). | `data "aws_ami" "ubuntu" { ... }` |
| **variable**        | Inputs to make code reusable (like function arguments). | `variable "instance_type" { default = "t2.micro" }` |
| **output**          | Prints useful info after apply (like IPs, IDs). | `output "ip" { value = aws_instance.vm.public_ip }` |
| **locals**          | Helper values inside config (shortcuts, naming). | `locals { env = "dev" }` |
| **module**          | Reuse another Terraform config (like calling a function). | `module "vpc" { source = "terraform-aws-modules/vpc/aws" }` |
| **backend** (inside `terraform`) | Where Terraform state file is stored (local/S3/etc.). | `backend "s3" { bucket = "mybucket" }` |

---

## 🔹 Advanced / Special Blocks

| Block / Meta-Arg   | What It Does | Example Use |
|---------------------|-------------|-------------|
| **provisioner**     | Runs scripts after resource create/destroy (not recommended for production). | Install a package on EC2 after launch. |
| **connection**      | Defines how to connect (SSH/WinRM) for provisioners. | `connection { type = "ssh" user = "ec2-user" }` |
| **dynamic**         | Generates repeated blocks with loops. | Multiple security group rules from a list. |
| **moved**           | Marks a resource as renamed → avoids destroy/recreate. | `moved { from = aws_instance.old to = aws_instance.new }` |
| **import**          | Bring an existing resource under Terraform management. | Import a manually-created EC2 into state. |
| **check**           | Verify conditions after apply. | Ensure a deployed URL is reachable. |
| **lifecycle** (inside resource) | Control behavior (keep resource, replace before destroy, ignore changes). | `prevent_destroy = true` for S3 bucket. |
| **depends_on**      | Force order of creation. | Ensure IAM role is created before EC2. |
| **count**           | Create many resources by number. | `count = 3` → 3 EC2 instances. |
| **for_each**        | Create resources from a list/map (better than `count` when naming matters). | `for_each = toset(var.names)` |

---

## ⚡ Quick Memory Tip
- **terraform / provider / backend** → settings.  
- **resource / data / module** → build infra.  
- **variable / locals / output** → inputs & outputs.  
- **provisioner / connection / dynamic** → special cases.  
- **moved / import / check / lifecycle / depends_on** → advanced handling.  
- **count / for_each** → loops.  

---

# ⚙️ Terraform Functions Cheat Sheet

Terraform has built-in **functions** to transform data, just like Excel or Python functions.  
They make code **shorter, smarter, reusable**.

---

## 🔹 Type Conversion
| Function      | What It Does | Example |
|---------------|--------------|---------|
| `toset()`     | Convert list → set (removes duplicates). | `toset(["a","b","a"])` → `["a","b"]` |
| `tolist()`    | Convert set/map → list. | `tolist(toset(["x","y"]))` → `["x","y"]` |
| `tomap()`     | Convert object/list → map. | `tomap({a=1, b=2})` |
| `tonumber()`  | Convert string → number. | `tonumber("42")` → `42` |
| `tostring()`  | Convert anything → string. | `tostring(123)` → `"123"` |

💡 **Tip**: Use these when Terraform complains about “wrong type”.

---

## 🔹 String Functions
| Function      | What It Does | Example |
|---------------|--------------|---------|
| `upper()`     | Convert to uppercase. | `upper("hello")` → `"HELLO"` |
| `lower()`     | Convert to lowercase. | `lower("HELLO")` → `"hello"` |
| `trimspace()` | Remove extra spaces. | `trimspace("  hi  ")` → `"hi"` |
| `replace()`   | Replace part of text. | `replace("abc","a","x")` → `"xbc"` |
| `substr()`    | Extract substring. | `substr("abcdef",1,3)` → `"bcd"` |
| `join()`      | Join list → string. | `join(",", ["a","b"])` → `"a,b"` |
| `split()`     | Split string → list. | `split(",", "a,b")` → `["a","b"]` |

---

## 🔹 Collection Functions (Lists, Maps, Sets)
| Function       | What It Does | Example |
|----------------|--------------|---------|
| `length()`     | Count items. | `length(["a","b"])` → `2` |
| `keys()`       | Get keys from map. | `keys({a=1,b=2})` → `["a","b"]` |
| `values()`     | Get values from map. | `values({a=1,b=2})` → `[1,2]` |
| `merge()`      | Merge maps. | `merge({a=1}, {b=2})` → `{a=1,b=2}` |
| `zipmap()`     | Make map from 2 lists. | `zipmap(["a","b"], [1,2])` → `{a=1,b=2}` |
| `contains()`   | Check if list has item. | `contains(["a","b"], "a")` → `true` |
| `distinct()`   | Remove duplicates. | `distinct([1,2,2])` → `[1,2]` |
| `sort()`       | Sort list. | `sort(["c","a","b"])` → `["a","b","c"]` |

---

## 🔹 Numeric Functions
| Function   | What It Does | Example |
|------------|--------------|---------|
| `min()`    | Smallest number. | `min(3,7,2)` → `2` |
| `max()`    | Largest number. | `max(3,7,2)` → `7` |
| `ceil()`   | Round up. | `ceil(3.1)` → `4` |
| `floor()`  | Round down. | `floor(3.9)` → `3` |
| `abs()`    | Remove minus sign. | `abs(-5)` → `5` |
| `coalesce()` | First non-null value. | `coalesce(null, "", "x")` → `"x"` |

---

## 🔹 Date & Time Functions
| Function        | What It Does | Example |
|-----------------|--------------|---------|
| `timestamp()`   | Current UTC time. | `"2025-08-16T05:30:00Z"` |
| `timeadd()`     | Add duration to time. | `timeadd("2025-01-01T00:00:00Z", "24h")` → `"2025-01-02T00:00:00Z"` |
| `formatdate()`  | Format date string. | `formatdate("YYYY", timestamp())` → `"2025"` |

---

## 🔹 Conditional & Logical
| Function / Syntax | What It Does | Example |
|-------------------|--------------|---------|
| `condition ? true_val : false_val` | If-Else logic. | `var.env == "dev" ? "t2.micro" : "t2.large"` |
| `can()`          | Checks if expression is valid. | `can(tonumber("x"))` → `false` |
| `try()`          | Try values until one works. | `try(var.a, var.b, "default")` |

---

# 📝 Final Tips for Beginners
- **Blocks = Structure** → “what Terraform builds”.  
- **Functions = Tools** → “how Terraform handles data”.  
- Start with: `terraform`, `provider`, `resource`, `variable`, `output`.  
- Add advanced ones (`module`, `count`, `for_each`) once comfortable.  
- Avoid `provisioner` unless testing (use Ansible/Puppet for setup).  
- Always check **terraform plan** before apply 🚀.  
