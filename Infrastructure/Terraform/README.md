# Terraform

## Learning plans
- According to [How I Would Learn Terraform (if I could start over) by Homebrew Henry](https://www.youtube.com/watch?v=wVmS7T7P3YM)
  1. Phase 1: 
     - How & why Infrastructure as code (IaC) is used --> automation; consistency across all setups; trace-ability with version control; and efficiency with fast & reliable scaling up/down
     - Declarative vs. Imperative --> won't duplicate even if 
     repeated instruction
     - Free account with a cloud provider (e.g. AWS ?)
  2. Phase 2: HasshiCorp (HCP) tutorials --> AWS one
     - Core workflow: plan -> apply
     - Managing change (before collaborate using HCP Terraform, which is for commercial platform)
  3. Phase 3: Terraform Up & Running by Yevgeniy Brikman --> chapter 2 (recap & extension of tutorials), 3 (managing state) and 4 (how to clean up --> modular)
  4. Phase 4: project: [Cloud resume challenge](https://cloudresumechallenge.dev/) with prev book as reference

## Commands
Basic workflows:
1. `terraform init`: download provider & init workspace
2. `TT plan`: show the action plan --> can specify output to save it
3. `TT apply`: show the plan, then confirm and execute (i.e. create required resources)
4. `TT destroy`: clean up (i.e. destroy specified resources)

Misc:
- `TT show`: show all infos
- `TT state list`: show high-level resources
- `TT state show`: show info for specific resource
- `TT output`: display "outputs" as defined

## Structure
- All "*.tf" files within folder will be parsed together
- Basic structures
  - `required_providers`: the "plugin" to translate terraform languanges into platform specific
  - `provider "xxx" {}`: configure for the specific provider
  - `resource "xxx_yyy" "name" {}`: configure for specific resource, using provider `xxx`, subtype `yyy` with id `name` --> can be referenced as `xxx_yy.name`
- `terraform.tfstate`: detail states of managed resources --> machine readable, for terraform to decide what changes are needed
  - In multi-user environment, this shouldn't checked in, but put into shared location
    ```hcl
    # Use AWS S3 as shared location with lock file
    terraform {
      backend "s3" {
         bucket         = "your-unique-terraform-state-bucket"
         key            = "production/infrastructure.tfstate"
         region         = "us-east-1"
         encrypt        = true
         use_lockfile   = true # Native S3 locking (Terraform 1.10+)
      }
    }
    ```
- Variables: for common identifier: `variable "variable_name" { default="something" }`
- Outputs: for print out specific identifier: `output "output_name" { value = xxx_yy.name.attribute_name }`
