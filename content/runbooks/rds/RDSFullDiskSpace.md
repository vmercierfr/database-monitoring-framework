---
title: Full disk space
---

# RDSFullDiskSpace

## Meaning

Alert is triggered when RDS instance storage is full

## Impact

PostgreSQL automatically stops when it detects there is no more disk space available.

**All database accesses are blocked**, causing application errors

## Diagnosis

You need to increase the RDS storage. Determine whether it's a long-term growth trend requiring storage increase or abnormal disk usage reflecting another problem.

{{< hint danger >}}
Since RDS disk space cannot be reduced and storage modifications are limited to once every 6 hours, you should carefully evaluate your storage requirements before making changes."
{{< /hint >}}

## Mitigation

RDS instances is **no more reachable**, you **must increase the RDS storage allocated disk**.

{{< hint danger >}}
{{% aws-rds-storage-increase-limitations %}}
{{< /hint >}}

{{% aws-rds-storage-increase-commands %}}

1. Wait for instance to pass in `storage-optimization` status

   The instance becomes accessible after the `modifying` operation is complete.

   {{<hint>}}
   {{% aws-rds-status-storage-optimization %}}
   {{< /hint >}}

   See RDS instance status:

   ```bash
   aws rds describe-db-instances \                       
      --db-instance-identifier ${INSTANCE_IDENTIFIER} \
      --query "DBInstances[0].[DBInstanceStatus]"
   ```

   Additionally you can follow RDS event for this instance:

   {{% aws-rds-list-events %}}

1. Backport changes in Terraform

## Additional resources

- [RDS Storage Modification](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PIOPS.StorageTypes.html#USER_PIOPS.ModifyingExisting)
- [AWS RDS Storage Autoscaling](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PIOPS.StorageTypes.html#USER_PIOPS.Autoscaling)
