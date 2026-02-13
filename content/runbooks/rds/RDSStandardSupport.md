---
title: Standard Support Close to Expiration
---

# RDSStandardSupport

## Meaning

Alert is triggered when an instance is close to reaching the end of AWS standard support period.

## Impact

{{< hint warning >}}
**Important**

Extended Support charges are applied automatically once standard support ends. These costs are in addition to your regular RDS instance charges and can substantially increase your monthly bill.
{{< /hint >}}

After the standard support period ends, AWS will automatically enroll the database engine version into Extended Support, which incurs additional charges. Extended Support provides security patches and critical fixes during a transition window to give you more time to upgrade.

Extended Support costs can be significant and vary by engine type and instance size. Without upgrading, you will continue to pay Extended Support fees until you upgrade to a supported engine version.

## Diagnosis

Identify the instance(s) approaching end of standard support:

- Check the RDS console for engine version information
- Or use the following AWS CLI command to list instances and their engine versions:

  ```bash
  aws rds describe-db-instances --query 'DBInstances[*].[DBInstanceIdentifier,Engine,EngineVersion]' --output table
  ```

- Check the current engine version against AWS documentation to determine the standard support end date:
  - [Amazon RDS for PostgreSQL versions](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html)
  - [Amazon RDS for MySQL versions](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MySQL.Concepts.VersionMgmt.html)
  - [Amazon Aurora PostgreSQL versions](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html)
  - [Amazon Aurora MySQL versions](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.html)

## Mitigation

{{< hint info >}}
**Tips**

- Major version upgrades may require application compatibility testing
- Consider using Blue/Green deployments for zero-downtime upgrades
- Review the upgrade documentation for your specific engine for breaking changes
- Use the RDS Extended Support cost calculator to estimate potential charges if you delay the upgrade
{{< /hint >}}

Plan and execute a database engine upgrade before the standard support period ends:

1. **Review upgrade paths**: Identify the target engine version that has active standard support
2. **Test the upgrade**: Create a snapshot and test the upgrade in a non-production environment
3. **Plan the upgrade window**: Schedule the upgrade during a maintenance window with minimal impact
4. **Perform the upgrade**:

   ```bash
   aws rds modify-db-instance \
       --db-instance-identifier <your_db_instance> \
       --engine-version <target_version> \
       --apply-immediately
   ```

   Or schedule it for the next maintenance window by omitting `--apply-immediately`

## Additional resources

- [Amazon RDS Extended Support](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/extended-support.html)
- [Estimating the charges for Amazon RDS Extended Support](https://aws.amazon.com/blogs/aws-cloud-financial-management/estimating-the-charges-for-amazon-rds-extended-support/)
- [RDS Calculator for Extended Support costs](https://calculator.aws/#/createCalculator/RDSPostgreSQL)
- [Upgrade your Amazon RDS for PostgreSQL or Amazon Aurora PostgreSQL database, Part 1: Comparing upgrade approaches](https://aws.amazon.com/blogs/database/part-1-upgrade-your-amazon-rds-for-postgresql-database-comparing-upgrade-approaches/)
- [Upgrading a DB instance engine version](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_UpgradeDBInstance.Upgrading.html)
