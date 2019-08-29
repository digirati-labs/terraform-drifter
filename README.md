# Drifter

Terraform module for scheduled checking of Terraform drift using Drifter (https://github.com/digirati-labs/drifter), with notifications sent to a Slack webhook and metrics emitted to Cloudwatch.

| Variable                | Description                                                         | Default           |
|-------------------------|---------------------------------------------------------------------|-------------------|
| prefix                  | Prefix to give to AWS resources                                     |                   |
| slack_webhook_url       | Slack Webhook URL for notifications                                 |                   |
| terraform_identifier    | Identifier for the Drifter task (e.g. `my-tf-repo-master`)          |                   |
| terraform_s3_bucket     | Name of S3 bucket that the Terraform resides in                     |                   |
| terraform_s3_key        | S3 Key of the Terraform remote state file                           | terraform.tfstate |
| terraform_github_repo   | GitHub repository in format `user/repo`                             |                   |
| terraform_github_branch | GitHub repository branch to use                                     | master            |
| terraform_github_folder | Subfolder within GitHub repository for Terraform                    |                   |
| terraform_github_token  | GitHub access token for the repo defined in `terraform_github_repo` |                   |
| cloudwatch_namespace    | AWS CloudWatch metric namespace where metrics should be shipped     |                   |
| tmp_folder              | Temporary folder to use                                             | /tmp              |
| log_group_name          | CloudWatch log group name that the container will emit logs to      |                   |
| region                  | AWS Region for resources                                            |                   |
| account_id              | AWS account ID                                                      |                   |
| cluster_id              | The cluster on which to run the scheduled ECS task                  |                   |
| cron_expression         | Cron scheduling expression in form `cron(x x x x x x)`              |                   |

## Example

```
module "drifter_estate" {
  source                 = "git::https://github.com/digirati-labs/terraform-drifter.git//"
  slack_webhook_url      = "${var.slack_webhook_status}"
  terraform_identifier   = "my-terraform-repo-master"
  terraform_s3_bucket    = "my-state-bucket"
  terraform_github_repo  = "my-github-user/my-terraform-repo"
  terraform_github_token = "${data.aws_ssm_parameter.terraform_github_token.value}"
  cloudwatch_namespace   = "terraform-drift"
  log_group_name         = "${var.log_group_name}"
  prefix                 = "${var.prefix}"
  region                 = "${var.region}"
  account_id             = "${var.account_id}"
  cluster_id             = "${module.metropolis_cluster.cluster_id}"
  cron_expression        = "cron(0 0 * * ? *)"
}

```
