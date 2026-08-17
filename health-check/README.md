# PostgreSQL Health Check

The PostgreSQL health check generates an HTML report for Amazon RDS for PostgreSQL and Amazon Aurora PostgreSQL. It combines PostgreSQL system-catalog diagnostics with selected Amazon CloudWatch metrics to highlight configuration, capacity, maintenance, and performance risks.

## What It Checks

- Instance configuration and PostgreSQL version
- Largest tables and indexes
- Table bloat and dead tuples
- Unused and duplicate indexes
- Vacuum, autovacuum, and transaction ID wraparound risk
- Sequence exhaustion risk
- Tables without primary keys
- CloudWatch CPU, memory, I/O, and connection metrics
- Key parameter settings and recommendations

## Prerequisites

- A Linux environment with network access to the database
- `psql` installed
- AWS CLI credentials with read access to the relevant CloudWatch and RDS metadata
- A PostgreSQL user with sufficient catalog and statistics visibility
- PostgreSQL 13 or later

Use read-only or least-privilege AWS credentials whenever possible.

## Run the Check

From the repository root:

```bash
chmod +x health-check/postgres_health_check.sh
./health-check/postgres_health_check.sh
```

Follow the prompts for the database endpoint, port, database, user credentials, and report name. The script writes an HTML report to the current directory.

## Output and Safety

- The diagnostic queries are read-only and do not execute DDL or modify database data.
- The generated report can contain database object names, sizes, configuration values, and operational metrics. Store and share it according to your organization's data-handling requirements.
- Validate the script in a non-production environment before relying on it for production assessments.

See the [sample report](sample-reports/sample-health-check-report.html) for an example.

## Contributors and Contributing

- See [component contributors](CONTRIBUTORS.md) for authorship and attribution.
- See the repository [contribution guidelines](../CONTRIBUTING.md) to report issues or propose changes.

## Related Documentation

- [Repository overview](../README.md)
- [Amazon RDS CloudWatch alarms](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/creating_alarms.html)
- [Amazon RDS for PostgreSQL log access](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.PostgreSQL.html)
- [License](../LICENSE)
