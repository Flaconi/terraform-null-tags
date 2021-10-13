# Microservice Boilerplate

[![Lint Status](https://github.com/Flaconi/terraform-terraform-project-tags/actions/workflows/linting.yml/badge.svg?branch=master)](https://github.com/Flaconi/terraform-terraform-project-tags/actions/workflows/linting.yml)
[![Docs Status](https://github.com/Flaconi/terraform-terraform-project-tags/actions/workflows/terraform-docs.yml/badge.svg?branch=master)](https://github.com/Flaconi/terraform-terraform-project-tags/actions/workflows/terraform-docs.yml)
[![Tag](https://img.shields.io/github/tag/Flaconi/terraform-terraform-project-tags.svg)](https://github.com/Flaconi/terraform-terraform-project-tags/releases)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://opensource.org/licenses/MIT)

This Terraform module helps to create a unified tagging across different projects.

## Usage

### Typical Folder structure within a Terraform Project:
```
.
├── lambda-security-group
│   └── terragrunt.hcl
├── project-tags
│   └── terragrunt.hcl
├── redis
│   └── terragrunt.hcl
├── redis-security-group
│   └── terragrunt.hcl
└── terraform-aws-ssm-store
    └── terragrunt.hcl
```

### Typical terragrunt.hcl of project-tags
```
dependency "vpc" {
  config_path = "../../../infra/vpc"
}


dependency "project-tags" {
  config_path = "../project-tags"
}

terraform {
  source = "github.com/terraform-aws-modules/terraform-aws-security-group?ref=v3.1.0"
}

include {
  path = find_in_parent_folders()
}


locals {
}


inputs = {
  vpc_id = dependency.vpc.outputs.vpc_id

}
```

### Typical terragrunt.hcl of project-tags

```
dependency "vpc" {
  config_path = "../../../infra/vpc"
}

dependency "project-tags" {
  config_path = "../project-tags"
}

dependency "lambda-security-group" {
  config_path = "../lambda-security-group"
}

terraform {
  source = "github.com/terraform-aws-modules/terraform-aws-security-group?ref=v3.1.0"
}

include {
  path = find_in_parent_folders()
}


locals {
}


inputs = {
  vpc_id = dependency.vpc.outputs.vpc_id

  name        = "brands-api-redis-sg"
  description = "Security group for Redis brands-api"

  computed_ingress_with_source_security_group_id = [
    {
      rule                     = "redis-tcp"
      source_security_group_id = dependency.lambda-security-group.outputs.this_security_group_id
    }
  ]
  number_of_computed_ingress_with_source_security_group_id = 1

  tags = dependency.project-tags.outputs.tags
}
```

## Resources

No resources are created.
<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 0.12 |

## Providers

No providers.

## Modules

No modules.

## Resources

No resources.

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_additional_tag_map"></a> [additional\_tag\_map](#input\_additional\_tag\_map) | Additional tags for appending to each tag map | `map(string)` | `{}` | no |
| <a name="input_environment"></a> [environment](#input\_environment) | Environment, e.g. 'prod', 'staging', 'dev', 'pre-prod', 'UAT' | `string` | `""` | no |
| <a name="input_parent"></a> [parent](#input\_parent) | Parent folder | `string` | `""` | no |
| <a name="input_project"></a> [project](#input\_project) | Solution name, e.g. 'app' or 'jenkins' | `string` | `""` | no |
| <a name="input_region"></a> [region](#input\_region) | Region, e.g. 'eu-west-1', 'eu-central-1' | `string` | `""` | no |
| <a name="input_tags"></a> [tags](#input\_tags) | Additional tags (e.g. `map('BusinessUnit','XYZ')` | `map(string)` | `{}` | no |
| <a name="input_terraform_provider"></a> [terraform\_provider](#input\_terraform\_provider) | Namespace, which could be your organization name or abbreviation, e.g. 'eg' or 'cp' | `string` | `""` | no |

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_tags"></a> [tags](#output\_tags) | Normalized Tag map |
| <a name="output_tags_as_list_of_maps"></a> [tags\_as\_list\_of\_maps](#output\_tags\_as\_list\_of\_maps) | Additional tags as a list of maps, which can be used in several AWS resources |

<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->


## License

[MIT](LICENSE)

Copyright (c) 2019 [Flaconi GmbH](https://github.com/Flaconi)
