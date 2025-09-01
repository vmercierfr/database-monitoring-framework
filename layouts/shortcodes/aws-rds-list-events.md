<!-- markdownlint-disable-next-line MD041 // this content is included in section -->

```bash
aws rds describe-events \
--source-identifier ${INSTANCE_IDENTIFIER} \
--duration 720 \
--source-type db-instance \
| jq -r '.Events[] | "\(.Date) [\(.EventCategories[0])] \(.Message)"'
```
