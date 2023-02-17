# Terraform

Terraform unlike Ansible uses a declarative language. This means you define your end goal (say creating an EC2 instance) and not the steps to get to the end goal, and Terraform will handle the rest for you. Opposite to this is procedural programming languages like Ansible where we write the steps instead.

---
**Terraform language**

Terraform has its own very simple configuration language. A Terraform configuration is typically consists of multiple blocks. Each block represents an object e.g. a resource in AWS like EC2. Inside a block, you can have arguments. An argument assigns a value to a particular name. The context where the argument appears determines what value types are valid (for example, each resource type has a schema that defines the types of its arguments).
```
<BLOCK TYPE> "<BLOCK LABEL>" "<BLOCK LABEL>" {
    # block body
    <IDENTIFIER> = <EXPRESSION> #argument
}
```

---
**Directories and modules**

A module is a collection of `.tf` and/or `.tf.json` files kept together in a directory. A Terraform module only consists of the top-level configuration files in a directory; nested directories are treated as completely separate modules, and are not automatically included in the configuration. Separating various blocks into different files is purely for the convenience of readers and maintainers, and has no effect on the module's behaviour. Terraform always runs in the context of a single root module. The root module is the working directory where Terraform is invoked. A Terraform module (usually the root module of a configuration) can call other modules to include their resources into the configuration. This is how a child is called in the root module:
```
module "servers" {
    source = "./app-cluster"
    servers = 5
}
```

```
resource "aws_elb" "example" {
    # ...
    instances = module.servers.instance_ids
}
```

---
**Resources**

A resource block describes one or more infrastructure objects, such as virtual networks, compute instances. A resource block declares a resource of a given type ("aws_instance") with a given local name ("web"). The name is used to refer to this resource from elsewhere in the same Terraform module, but has no significance outside that module's scope. Each resource type is implemented by a Provider (see provider section). Every Terraform provider has its own documentation, describing its resource types and their arguments, so google!
```
resource "aws_instance" "web" {
    ami           = "ami-a1b2c3d4"
    instance_type = "t2.micro" 
}
```

The Terraform language defines several meta-arguments, which can be used with any resource type to change the behavior of resources:
> `depends_on`, for specifying hidden dependencies

> `count`, for creating multiple resource instances according to a count

> `for_each`, to create multiple instances according to a map, or set of strings

> `provider`, for selecting a non-default provider configuration

> `lifecycle`, for lifecycle customizations

> `provisioner`, for taking extra actions after resource creation

In summary, applying a Terraform configuration will:
* Create resources that exist in the configuration but are not associated with a real infrastructure object in the state.
* Destroy resources that exist in the state but no longer exist in the configuration.
* Update in-place resources whose arguments have changed.
* Destroy and re-create resources whose arguments have changed but which cannot be updated in-place due to remote API limitations.

Expressions within a Terraform module can access information about resources in the same module, and you can use that information to help configure other resources. Use the `<RESOURCE TYPE>.<NAME>.<ATTRIBUTE>` syntax to reference a resource attribute in an expression. Another terminology is Provisioners. This is more advance and is only needed if there will be certain behaviours that can't be directly represented in Terraform's declarative model. Checkout documentation if ever this feature is needed.
 
---
**Data sources**

Data sources allow Terraform to use information defined outside of Terraform, defined by another separate Terraform configuration, or modified by functions. Each provider may offer data sources alongside its set of resource types. This is an example of a data block. This data instance will export one or more attributes, which can be used in other resources as reference expressions of the form data.`<TYPE>.<NAME>.<ATTRIBUTE>`:
```
# find the latest available AMI that is tagged with Component = web
data "aws_ami" "web" {
    filter {
        name   = "state"
        values = ["available"] 
    }
    filter {
        name   = "tag:Component"
        values = ["web"]  
    }
    most_recent = True
}
```
```
resource "aws_instance" "web" {
    ami           = data.aws_ami.web.id
    instance type = "t1.micro"
}
```

---
**Providers**

Providers are Terraform plugins that let Terraform interact with cloud platforms and other services via their APIs. e.g. there is an AWS provider to manage AWS resources. Terraform configurations must declare which providers they require so that Terraform can install and use them. Additionally, some providers require configuration (like endpoint URLs or cloud regions) before they can be used. Each provider adds a set of resource types and/or data sources that Terraform can manage. Each provider has its own documentation, describing its resource types and their arguments. A provider is defined with a provider block:
```
provider "aws" {
    profile = "default"
    region  = "us-west-2" 
}
```

There are two "meta-arguments" that are defined by Terraform itself and available for all provider blocks:
1. `alias`, for using the same provider with different configurations for different resources
2. `version`, which we no longer recommend (use provider requirements instead)

Some providers require configuration (like endpoint URLs or cloud regions) before they can be used. This is declared in a required_providers block inside a top-level terraform block:
```
terraform {
    required_providers {
        mycloud = {
            source = "mycorp/mycloud"
            version = "~>1.0"
        }
    }
}
```

---
**Variables and outputs**

You can compare Terraform modules to function definitions in any programming language:
* Input variables are like function arguments.
* Output values are like function return values
* Local values are like a function's temporary local variables.

Each input variable must be declared using a variable block:
```
variable "image_id" {
    type = string
}

variable "availability_zones" {
    type = list(string)
    defaults = ["us-west-1a"]
}

variable "docker_ports" {
    type = list(object({
        internal = number
        external = number
        protocolo = string
    }))
    default = [
        {
            internal = 8300
            external = 8300
            protocol = "tcp"
        }
    ]
}
```


Terraform CLI defines the following optional arguments for variable declarations (for type constraints etc refer to documentation):
* `default` - A default value which then makes the variable optional.
* `type` - This argument specifies what value types are accepted for the variable.
* `description` - This specifies the input variable's documentation.
* `validation` - A block to define validation rules, usually in addition to type constraints.
* `sensitive` - Limits Terraform UI output when the variable is used in configuration.
* `nullable` - Specify if the variable can be null within the module.

Output values have several uses:
* A child module can use outputs to expose a subset of its resource attributes to a parent module.
* A root module can use outputs to print certain values in the CLI output after running terraform apply.
* When using remote state, root module outputs can be accessed by other configurations via a terraform_remote_state data source.

```
# main.tf
module "foo" {
    source = "./mod"
}

resource "test_instance" "x" {
    some_attribute = modile.mod.a # resource attribute references a sensitive output
}

output "out" {
    value = "xyz"
    sensitive = true
}

# mod/main.tf, our module containing a sensitive output
output "a" {
    value = "secret"
    sensitive = true
}
```

A local value assigns a name to an expression, so you can use it multiple times within a module without repeating it:
```
locals {
    service_name = "forum"
    owner        = "Community team"
}
```

```
locals {
    # ids for multiple sets of EC2 instances, merged together
    instance_ids = concat(aws_instance.*.id, aws_instance.green.*.id)
}
locals {
    # common tags to be assigned to all resources
    Service = local.service_name
    Owner = local.owner
}
```

```
resource "aws_instance" "example" {
    # ...
    tags = local.common_tags
}
```

---
**State**

Terraform must store state about your managed infrastructure and configuration. This state is used by Terraform to map real world resources to your configuration, keep track of metadata, and to improve performance for large infrastructures. This state is stored by default in a local file named `terraform.tfstate`, but it can also be stored remotely, which works better in a team environment. Terraform uses this local state to create plans and make changes to your infrastructure. Prior to any operation, Terraform does a refresh to update the state with the real infrastructure.

---
**Backends**

Backends define where Terraform's state snapshots are stored. A given Terraform configuration can either specify a backend, integrate with Terraform Cloud, or do neither and default to storing state locally. Terraform includes a built-in selection of backends. The built-in backends are the only backends. You cannot load additional backends as plugins. Examples are `s3`, `kubernetes`, etcd. Each Terraform configuration can specify a backend, which defines where state snapshots are stored.
```
terraform {
    backend "s3" {
        bukcet = "mybucket"
        key    = "path/to/my/key"
        region = "us-east-1"
    }
}
```

---
**Basic CLI features**

You can install terraform with package managers, like `brew`, check documentation. The terraform command accepts subcommands:

```
Usage: terraform [global options] <subcommand> [args]

The available commands for execution are listed below.
The primary workflow commands are given first, followed by
less common or more advanced commands.

Main commands:
  init          Prepare your working directory for other commands
  validate      Check whether the configuration is valid
  plan          Show changes required by the current configuration
  apply         Create or update infrastructure
  destroy       Destroy previously-created infrastructure

All other commands:
  console       Try Terraform expressions at an interactive command prompt
  fmt           Reformat your configuration in the standard style
  force-unlock  Release a stuck lock on the current workspace
  get           Install or upgrade remote Terraform modules
  graph         Generate a Graphviz graph of the steps in an operation
  import        Associate existing infrastructure with a Terraform resource
  login         Obtain and save credentials for a remote host
  logout        Remove locally-stored credentials for a remote host
  output        Show output values from your root module
  providers     Show the providers required for this configuration
  refresh       Update the state to match remote systems
  show          Show the current state or a saved plan
  state         Advanced state management
  taint         Mark a resource instance as not fully functional
  untaint       Remove the 'tainted' state from a resource instance
  version       Show the current Terraform version
  workspace     Workspace management

Global options (use these before the subcommand, if any):
  -chdir=DIR    Switch to a different working directory before executing the
                given subcommand.
  -help         Show this help output, or the help for a specified subcommand.
  -version      An alias for the "version" subcommand.
```

---
**references**
1. [developer.hashicorp.com](https://developer.hashicorp.com/terraform)