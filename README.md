# 🧾 Terraform Cheat Sheet

Terraform code is made of **blocks**. Each block has a purpose:  
some set **settings**, some **build infra**, some **control behavior**.

---

## 🔹 Core Blocks (Most Used)

| Block / Meta-Arg   | What It Does (Simple Words) | Common Inside It | Example Use |
|---------------------|-----------------------------|------------------|-------------|
| **terraform**       | Settings for Terraform itself (version, providers, backend). | `required_version`, `required_providers`, `backend` | `terraform { required_version = ">= 1.3" }` |
| **provider**        | Connects Terraform to a cloud/service (AWS, Azure, GCP, GitHub…). | `region`, `profile`, `access_key`, `secret_key`, `default_tags` | `provider "aws" { region = "us-east-1" }` |
| **resource**        | The real "things" Terraform builds (VMs, S3 buckets, VPCs…). | Resource-specific arguments (e.g., `ami`, `instance_type`, `tags`) | `resource "aws_instance" "vm" { ... }` |
| **data**            | Reads existing resources (without creating new ones). | Provider-specific lookups (e.g., `filter`, `most_recent`, `tags`) | `data "aws_ami" "ubuntu" { ... }` |
| **variable**        | Inputs to make code reusable (like function arguments). | `type`, `default`, `description`, `sensitive` | `variable "instance_type" { default = "t2.micro" }` |
| **output**          | Prints useful info after apply (like IPs, IDs). | `value`, `description`, `sensitive` | `output "ip" { value = aws_instance.vm.public_ip }` |
| **locals**          | Helper values inside config (shortcuts, naming). | Key-value pairs | `locals { env = "dev" }` |
| **module**          | Reuse another Terraform config (like calling a function). | `source`, `version`, variables | `module "vpc" { source = "terraform-aws-modules/vpc/aws" }` |
| **backend** (inside `terraform`) | Where Terraform state file is stored (local/S3/etc.). | `bucket`, `key`, `region`, `dynamodb_table` (for locking) | `backend "s3" { bucket = "mybucket" }` |

---

## 🔹 Advanced / Special Blocks

| Block / Meta-Arg   | What It Does | Common Inside It | Example Use |
|---------------------|-------------|------------------|-------------|
| **provisioner**     | Runs scripts after resource create/destroy (not recommended for production). | `inline`, `script`, `file` | Install a package on EC2 after launch. |
| **connection**      | Defines how to connect (SSH/WinRM) for provisioners. | `type`, `user`, `password`, `private_key`, `host` | `connection { type = "ssh" user = "ec2-user" }` |
| **dynamic**         | Generates repeated blocks with loops. | `for_each`, `content` | Multiple security group rules from a list. |
| **moved**           | Marks a resource as renamed → avoids destroy/recreate. | `from`, `to` | `moved { from = aws_instance.old to = aws_instance.new }` |
| **import**          | Bring an existing resource under Terraform management. | CLI: `terraform import <resource> <id>` | Import a manually-created EC2 into state. |
| **check**           | Verify conditions after apply. | `assert`, `condition`, `error_message` | Ensure a deployed URL is reachable. |
| **lifecycle** (inside resource) | Control behavior (keep resource, replace before destroy, ignore changes). | `prevent_destroy`, `create_before_destroy`, `ignore_changes` | `prevent_destroy = true` for S3 bucket. |
| **depends_on**      | Force order of creation. | List of resources/modules | Ensure IAM role is created before EC2. |
| **count**           | Create many resources by number. | Any number/int expression | `count = 3` → 3 EC2 instances. |
| **for_each**        | Create resources from a list/map (better than count when naming matters). | Maps/Sets | `for_each = toset(var.names)` |

---

## ⚡ Quick Memory Tip

- **terraform / provider / backend** → settings.  
- **resource / data / module** → build infra.  
- **variable / locals / output** → inputs & outputs.  
- **provisioner / connection / dynamic** → special cases.  
- **moved / import / check / lifecycle / depends_on** → advanced handling.  
- **count / for_each** → loops.  


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


# Terraform Functions Cheat Sheet (AWS-focused)

This document explains common Terraform functions in **simple words**, focusing on how they are often used in **AWS** projects.  

---

## 1. `toset()`
**Description:** Converts a list into a set (unique values, no duplicates).  
**What It Does (Simple Words):**  
- Removes duplicates and makes items unordered.  
- Useful when looping over AWS resources (like multiple subnets).  

---

## 2. `tolist()`
**Description:** Converts a set or tuple into a list.  
**What It Does (Simple Words):**  
- Makes sure data is in list format (ordered, can have duplicates).  
- Often used to loop AWS resources in order.  

---

## 3. `tomap()`
**Description:** Converts a list of key/value pairs into a map.  
**What It Does (Simple Words):**  
- Helps store AWS values like `{ "Name" = "EC2", "Env" = "Dev" }`.  
- Commonly used for AWS **tags**.  

---

## 4. `tostring()`
**Description:** Converts any value to a string.  
**What It Does (Simple Words):**  
- Makes sure numbers, IDs, or bools are in text format.  
- Example: converting AWS instance IDs to strings for tagging.  

---

## 5. `tonumber()`
**Description:** Converts a string into a number.  
**What It Does (Simple Words):**  
- Helps when AWS values (like port `"80"`) need to be numbers.  
- Example: setting security group rules with numeric ports.  

---

## 6. `try()`
**Description:** Tries multiple values and picks the first valid one.  
**What It Does (Simple Words):**  
- Useful when something may not exist.  
- Example: `try(module.vpc.private_subnets, [])` → picks subnets if exist, else empty list.  

---

## 7. `coalesce()`
**Description:** Returns the first non-null value.  
**What It Does (Simple Words):**  
- Picks the first valid AWS value.  
- Example: `coalesce(var.instance_type, "t2.micro")` → fallback default.  

---

## 8. `concat()`
**Description:** Joins multiple lists together.  
**What It Does (Simple Words):**  
- Used to merge AWS subnet lists or SG rules.  
- Example: combine public + private subnets.  

---

## 9. `merge()`
**Description:** Combines two or more maps.  
**What It Does (Simple Words):**  
- Used to merge AWS **tags** from multiple sources.  
- Example: merge default tags with environment tags.  

---

## 10. `length()`
**Description:** Returns how many items are in a list, map, or string.  
**What It Does (Simple Words):**  
- Often used in AWS loops (like number of subnets).  
- Example: `length(var.subnets)` = total subnets.  

---

## 11. `element()`
**Description:** Picks an item from a list by its index.  
**What It Does (Simple Words):**  
- Helps pick specific AWS subnet or AZ from a list.  
- Example: `element(var.subnets, 0)` = first subnet.  

---

## 12. `lookup()`
**Description:** Gets a value from a map by its key.  
**What It Does (Simple Words):**  
- Helps fetch AWS config values.  
- Example: `lookup(var.tags, "Environment", "dev")`.  

---

## 13. `join()`
**Description:** Joins list elements into a string with a separator.  
**What It Does (Simple Words):**  
- Useful for AWS names or tags.  
- Example: `join("-", ["dev", "app", "ec2"])` → `dev-app-ec2`.  

---

## 14. `split()`
**Description:** Splits a string into a list.  
**What It Does (Simple Words):**  
- Opposite of `join()`.  
- Example: split an AWS ARN into parts.  

---

## 15. `file()`
**Description:** Reads the contents of a file.  
**What It Does (Simple Words):**  
- Used for AWS **user_data** scripts or policy files.  
- Example: `file("userdata.sh")`.  

---

## 16. `cidrsubnet()`
**Description:** Splits a CIDR block into smaller subnets.  
**What It Does (Simple Words):**  
- Helps create AWS VPC subnets automatically.  
- Example: Split `10.0.0.0/16` into `/24` subnets.  

---

## 17. `regex()`
**Description:** Extracts values using a regex pattern.  
**What It Does (Simple Words):**  
- Helps when parsing AWS ARNs, names, or IDs.  
- Example: Extract account ID from an ARN.  

---

## 18. `format()`
**Description:** Formats strings with placeholders.  
**What It Does (Simple Words):**  
- Helps build AWS names consistently.  
- Example: `format("%s-%s", var.env, "ec2")` → `dev-ec2`.  

---

# 📌 Summary  
These functions make Terraform flexible by converting data types, looping, merging, and formatting values. In AWS projects, they are most often used for **subnets, tags, names, security groups, and instance configurations**.  

