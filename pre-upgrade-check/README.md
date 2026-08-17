# PostgreSQL Pre-Upgrade Check

The PostgreSQL pre-upgrade check assesses Amazon RDS for PostgreSQL and Amazon Aurora PostgreSQL readiness for a major-version upgrade. It combines read-only database checks with AWS control-plane metadata and generates an HTML report of blockers, warnings, and version-specific considerations.

## What It Checks

- Target engine-version availability
- Unsupported `reg*` data types
- Open prepared transactions
- Logical replication slots
- `unknown` and `sql_identifier` data types
- Extension compatibility
- Views that depend on system catalogs
- Instance-class compatibility
- Storage capacity and pending maintenance
- Read-replica configuration
- Version-specific upgrade considerations

## Prerequisites

- A Linux environment with network access to the database
- `psql` and AWS CLI installed
- AWS CLI credentials with read access to the relevant RDS or Aurora metadata
- A PostgreSQL user with sufficient catalog and statistics visibility

Use read-only or least-privilege AWS credentials whenever possible.

## Run the Check

From the repository root:

```bash
chmod +x pre-upgrade-check/pg_upgrade_pre_check.sh
./pre-upgrade-check/pg_upgrade_pre_check.sh
```

Follow the prompts for the database endpoint, port, database, authentication method, target PostgreSQL version, and report name. The script writes an HTML report to the current directory.

## Output and Safety

- The diagnostic queries are read-only and do not execute DDL or initiate an upgrade.
- A passing report reduces known upgrade risk but does not replace testing on a restored snapshot or non-production clone.
- Resolve hard blockers before scheduling a production upgrade.
- The report can contain database object names, configuration details, and AWS resource metadata. Handle it according to your organization's data-handling requirements.

See the [sample report](sample-reports/TestRun_pg15_pre-upgrade-check_report.html) for an example.

## Contributors and Contributing

- See [component contributors and reviewers](CONTRIBUTORS.md) for historical attribution.
- See the repository [contribution guidelines](../CONTRIBUTING.md) to report issues or propose changes.

## Related Documentation

- [Repository overview](../README.md)
- [Upgrading Amazon RDS for PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_UpgradeDBInstance.PostgreSQL.html)
- [Upgrading Amazon Aurora PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_UpgradeDBInstance.PostgreSQL.html)
- [License](../LICENSE)
