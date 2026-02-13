---
title: Extended Support Close to Expiration
---

# RDSExtendedSupport

## Meaning

Alert is triggered when an RDS instance is close to reaching the end of AWS extended support period.

## Impact

{{< hint danger >}}
**Critical**

After extended support ends, AWS will force an automatic upgrade to a supported major engine version. This upgrade will happen during your maintenance window and cannot be prevented. You should upgrade proactively to maintain control over the process and timing.
{{< /hint >}}

RDS Extended Support is available for up to 3 years past the RDS end of standard support date for a major engine version. After the extended support period ends, Amazon RDS will automatically upgrade your database instance to a supported major engine version during a maintenance window.

Automatic upgrades can cause:

- Unexpected downtime during the upgrade process
- Application compatibility issues if not properly tested
- Potential breaking changes in the new major version
- Loss of control over upgrade timing

## Diagnosis

Identify the instance(s) approaching end of extended support:

- Check the RDS console for engine version and support status
- Or use the following AWS CLI command to list instances and their engine versions:

  ```bash
  aws rds describe-db-instances --query 'DBInstances[*].[DBInstanceIdentifier,Engine,EngineVersion]' --output table
  ```

- Verify the extended support end date for your engine version:
  - [Amazon RDS for PostgreSQL versions](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html)
  - [Amazon RDS for MySQL versions](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MySQL.Concepts.VersionMgmt.html)
  - [Amazon Aurora PostgreSQL versions](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html)
  - [Amazon Aurora MySQL versions](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.html)

## Mitigation

{{< hint warning >}}
**Important**

Major version upgrades require the `--allow-major-version-upgrade` flag and may involve:

- Longer downtime than minor version upgrades
- Parameter group compatibility changes
- Application code modifications
- Thorough testing before production deployment
{{< /hint >}}

{{< hint info >}}
- Use Blue/Green deployments for zero-downtime major version upgrades
- Consider using AWS Database Migration Service (DMS) for complex migrations
- Review the upgrade checklist for your specific database engine
- Test with a copy of production data to identify issues early
- Plan for rollback procedures in case of issues
{{< /hint >}}

Upgrade your database engine immediately to avoid automatic forced upgrades:

1. **Identify target version**: Choose a major engine version with active standard support
2. **Test thoroughly**: Create a snapshot and test the major version upgrade in a non-production environment
   - Verify application compatibility
   - Test all critical queries and features
   - Review breaking changes in the new major version
3. **Plan the upgrade**: Schedule during a maintenance window with minimal business impact
4. **Perform the upgrade**:

   ```bash
   aws rds modify-db-instance \
       --db-instance-identifier <your_db_instance> \
       --engine-version <target_major_version> \
       --allow-major-version-upgrade \
       --apply-immediately
   ```

   Or schedule for the next maintenance window by omitting `--apply-immediately`

## Additional resources

- [Amazon RDS Extended Support](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/extended-support.html)
- [Upgrading a DB instance engine version](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_UpgradeDBInstance.Upgrading.html)
- [Using Amazon RDS Blue/Green Deployments for database updates](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/blue-green-deployments.html)
- [Upgrade your Amazon RDS for PostgreSQL or Amazon Aurora PostgreSQL database, Part 1: Comparing upgrade approaches](https://aws.amazon.com/blogs/database/part-1-upgrade-your-amazon-rds-for-postgresql-database-comparing-upgrade-approaches/)
- [Best practices for upgrading Amazon RDS for MySQL and Amazon RDS for MariaDB](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_UpgradeDBInstance.MySQL.html)
