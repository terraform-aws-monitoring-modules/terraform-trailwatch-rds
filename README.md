<p align="center">
  <a href="https://github.com/terraform-trailwatch-modules" title="Terraform Trailwatch Modules"><img src="https://raw.githubusercontent.com/terraform-trailwatch-modules/art/refs/heads/main/logo.jpg" height="100" alt="Terraform Trailwatch Modules"></a>
</p>

<h1 align="center">Relational Database Service (RDS)</h1>

<p align="center">
  <a href="https://github.com/terraform-trailwatch-modules/terraform-trailwatch-rds/releases" title="Releases"><img src="https://img.shields.io/badge/Release-1.0.1-1d1d1d?style=for-the-badge" alt="Releases"></a>
  <a href="https://github.com/terraform-trailwatch-modules/terraform-trailwatch-rds/blob/main/LICENSE" title="License"><img src="https://img.shields.io/badge/License-MIT-1d1d1d?style=for-the-badge" alt="License"></a>
</p>

## About
This Terraform module creates CloudWatch Log Metric Filters and associated Alarms for monitoring Amazon RDS instances and clusters based on specified event names. It helps ensure that critical changes to RDS resources are monitored effectively and alerts are sent to a pre-existing SNS topic.

## Features
- Creates CloudWatch Log Metric Filters for specified RDS instances and clusters.
- Creates CloudWatch Alarms that trigger based on metrics from the filters.
- Flexible configuration for events to monitor and alarm settings.

## Requirements
- Terraform 1.0 or later
- AWS Provider

## Inputs
| Variable                                      | Description                                                                                           | Type          | Default                                                   |
|-----------------------------------------------|-------------------------------------------------------------------------------------------------------|---------------|-----------------------------------------------------------|
| `rds_db_identifiers`                         | The list of RDS database identifiers to monitor.                                                    | `list(string)` | n/a                                                       |
| `rds_db_type`                               | The type of the RDS database: 'instance' for RDS instance or 'cluster' for RDS cluster.            | `string`      | `instance`                                               |
| `rds_db_instance_events`                     | List of RDS instance-related events to monitor.                                                     | `list(string)` | `["DeleteDBInstance", "ModifyDBInstance", "RebootDBInstance", "CreateDBSnapshot", "DeleteDBSnapshot", "RestoreDBInstanceFromDBSnapshot"]` |
| `rds_db_cluster_events`                      | List of RDS cluster-related events to monitor.                                                      | `list(string)` | `["CreateDBCluster", "DeleteDBCluster", "ModifyDBCluster", "CreateDBClusterSnapshot", "DeleteDBClusterSnapshot", "RestoreDBClusterFromS3", "FailoverDBCluster", "AddRoleToDBCluster", "RemoveRoleFromDBCluster"]` |
| `rds_db_parameter_group_events`              | List of RDS parameter group-related events to monitor.                                              | `list(string)` | `["CreateDBParameterGroup", "DeleteDBParameterGroup", "ModifyDBParameterGroup"]` |
| `rds_db_security_group_events`               | List of RDS security group-related events to monitor.                                               | `list(string)` | `["CreateDBSecurityGroup", "DeleteDBSecurityGroup", "ModifyDBSecurityGroup"]` |
| `cw_log_group_name`                          | The name of the CloudWatch log group storing CloudTrail logs.                                       | `string`      | n/a                                                       |
| `cw_metric_filter_namespace`                 | The namespace for the CloudWatch metric filter.                                                     | `string`      | `RDS/Monitoring`                                         |
| `cw_metric_filter_value`                     | The value to publish to the CloudWatch metric.                                                      | `string`      | `1`                                                       |
| `cw_metric_filter_alarm_comparison_operator` | The comparison operator for the CloudWatch metric filter alarm.                                      | `string`      | `GreaterThanOrEqualToThreshold`                          |
| `cw_metric_filter_alarm_evaluation_periods`  | The number of periods over which data is compared to the specified threshold.                        | `number`      | `1`                                                       |
| `cw_metric_filter_alarm_period`              | The period in seconds over which the specified statistic is applied.                                 | `number`      | `300`                                                     |
| `cw_metric_filter_alarm_statistic`           | The statistic to apply to the alarm's associated metric.                                            | `string`      | `Sum`                                                    |
| `cw_metric_filter_alarm_threshold`           | The value against which the specified statistic is compared.                                        | `number`      | `1`                                                       |
| `cw_metric_filter_alarm_actions`             | The list of actions to execute when the alarm transitions into an ALARM state.                       | `list(string)` | `[]`                                                      |

## Simple Example
```hcl
# database type: `instance`
module "terraform_trailwatch_rds" {
  source                         = "terraform-trailwatch-modules/rds/trailwatch"
  rds_db_identifiers             = ["acme-database-1"]
  cw_log_group_name              = "the-cloudtrail-log-group"
  cw_metric_filter_alarm_actions = ["arn:aws:sns:region:account-id:sns-topic"]
}

# database type: `cluster`
module "terraform_trailwatch_rds" {
  source                         = "terraform-trailwatch-modules/rds/trailwatch"
  rds_db_identifiers             = ["acme-database-1"]
  rds_db_type                    = "cluster"
  cw_log_group_name              = "the-cloudtrail-log-group"
  cw_metric_filter_alarm_actions = ["arn:aws:sns:region:account-id:sns-topic"]
}
```

## Advanced Example
```hcl
# database type: `instance`
module "aws_monitoring_rds" {
  source                                     = "path/to/module"
  rds_db_identifiers                         = ["acme-database-1"]
  rds_db_instance_events                     = ["DeleteDBInstance", "ModifyDBInstance"]
  cw_log_group_name                          = "the-cloudtrail-log-group"
  cw_metric_filter_namespace                 = "RDS/Monitoring"
  cw_metric_filter_value                     = "1"
  cw_metric_filter_alarm_comparison_operator = "GreaterThanOrEqualToThreshold"
  cw_metric_filter_alarm_evaluation_periods  = 1
  cw_metric_filter_alarm_period              = 300
  cw_metric_filter_alarm_statistic           = "Sum"
  cw_metric_filter_alarm_threshold           = 1
  cw_metric_filter_alarm_actions             = ["arn:aws:sns:region:account-id:sns-topic"]
}

# database type: `cluster`
module "aws_monitoring_rds" {
  source                                     = "path/to/module"
  rds_db_identifiers                         = ["acme-database-1"]
  rds_db_type                                = "cluster"
  rds_db_cluster_events                      = ["CreateDBCluster", "DeleteDBCluster"]
  cw_log_group_name                          = "the-cloudtrail-log-group"
  cw_metric_filter_namespace                 = "RDS/Monitoring"
  cw_metric_filter_value                     = "1"
  cw_metric_filter_alarm_comparison_operator = "GreaterThanOrEqualToThreshold"
  cw_metric_filter_alarm_evaluation_periods  = 1
  cw_metric_filter_alarm_period              = 300
  cw_metric_filter_alarm_statistic           = "Sum"
  cw_metric_filter_alarm_threshold           = 1
  cw_metric_filter_alarm_actions             = ["arn:aws:sns:region:account-id:sns-topic"]
}
```

## Changelog
For a detailed list of changes, please refer to the [CHANGELOG.md](CHANGELOG.md).

## License
This module is licensed under the [MIT License](LICENSE).
