<!-- markdownlint-disable-next-line MD036 MD041 // this content is included in hint block -->

1. Set AWS_PROFILE

    ```bash
    export AWS_PROFILE=<AWS account>
    ```

2. Determine the minimum storage for the increase

    💡 RDS requires a minimal storage increase of 10%

    ```bash
    INSTANCE_IDENTIFIER=<replace with the RDS instance identifier>
    ```

    ```bash
    aws rds describe-db-instances --db-instance-identifier ${INSTANCE_IDENTIFIER} \
    | jq -r '{"Current IOPS": .DBInstances[0].Iops, "Current Storage Limit": .DBInstances[0].AllocatedStorage, "New minimum storage size": ((.DBInstances[0].AllocatedStorage|tonumber)+(.DBInstances[0].AllocatedStorage|tonumber*0.1|floor))}'
    ```

3. Increase storage:

    ```bash
    NEW_ALLOCATED_STORAGE=<replace with new allocated storage in GB>
    ```

    ```bash
    aws rds modify-db-instance --db-instance-identifier ${INSTANCE_IDENTIFIER} --allocated-storage ${NEW_ALLOCATED_STORAGE} --apply-immediately \
    | jq .DBInstance.PendingModifiedValues
    ```

    Instance will quickly pass in `modifying` then `storage-optimization` status.

    ❗ If the RDS instance has replicas instances, you must repeat the operation for each replicas to keep the same configuration between instances
