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
- PostgreSQL 19 TLS client readiness using `pg_stat_ssl` and the effective `ssl_min_protocol_version` and `ssl_max_protocol_version` settings

## PostgreSQL 19 TLS Readiness

For a target major version of PostgreSQL 19 or later, the report adds a non-blocking TLS readiness assessment for both RDS for PostgreSQL and Aurora PostgreSQL. It assumes the currently planned PostgreSQL 19 service default of `ssl_min_protocol_version=TLSv1.3`; verify the final PostgreSQL 19 release and service documentation before making a change.

The check:

- Aggregates the protocols of currently connected candidate client backends from the `pg_stat_ssl` view on the connected PostgreSQL instance.
- Excludes the check's own session, PostgreSQL background workers, and the `rdsadmin` service user.
- Counts `rdsproxyadmin` database-facing backends separately; they do not count as candidate clients or trigger non-TLS warnings.
- Reports the effective `ssl_min_protocol_version` and `ssl_max_protocol_version` settings even when no clients are connected.
- Warns when TLS 1.2 or older, non-TLS connections, statistics-visibility gaps, or a server maximum that prevents TLS 1.3 negotiation are observed.
- Reports **inconclusive**, rather than passing, when no representative client connections are visible or the evidence cannot be collected.
- Reports **no incompatible clients observed**, rather than guaranteeing compatibility, only when every observed candidate connection has visible TLS details and uses TLS 1.3.

The TLS result never fails or blocks the upgrade. With the planned PostgreSQL 19 default, a TLS handshake from a client that cannot negotiate TLS 1.3 is expected to fail unless the target parameter group explicitly allows `TLSv1.2`. Acceptance of non-TLS connections is separate from `ssl_min_protocol_version` and depends on the applicable service and connection policy. Prefer updating affected client drivers and poolers; use a lower minimum only as an explicitly reviewed compatibility measure.

`pg_stat_ssl` is a point-in-time server-side snapshot for the connected PostgreSQL instance. For Aurora, it does not combine activity from other writer or reader instances; assess every client-serving instance or endpoint separately. Candidate connections can include applications, people, monitoring, maintenance jobs, and direct pooler connections. The database role needs `pg_monitor` or equivalent statistics visibility; hidden TLS details make the result inconclusive rather than non-TLS. AWS-managed `rdsproxyadmin` database-facing backends are reported separately and do not establish client-to-proxy TLS compatibility. If those are the only visible sessions, the result is inconclusive and the proxy endpoint must be assessed separately. An idle clone or test cluster usually cannot represent the production client fleet, so run the check against production during representative traffic, well before the upgrade window.

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

### Run through an existing SSM tunnel

The script can keep the real RDS/Aurora endpoint for AWS metadata while routing PostgreSQL connections through a local port forward. Start the approved tunnel in one terminal and keep it open:

```bash
export TUNNEL_PROFILE=your-approved-profile
export SSM_TARGET=i-0123456789abcdef0
export DATABASE_ENDPOINT=example.cluster-example.us-west-2.rds.amazonaws.com

aws ssm start-session \
  --profile "$TUNNEL_PROFILE" \
  --region us-west-2 \
  --target "$SSM_TARGET" \
  --document-name AWS-StartPortForwardingSessionToRemoteHost \
  --parameters "{\"host\":[\"$DATABASE_ENDPOINT\"],\"portNumber\":[\"5432\"],\"localPortNumber\":[\"15432\"]}"
```

In a second terminal, run the complete check with explicit tunnel overrides:

```bash
AWS_PROFILE="$TUNNEL_PROFILE" \
PG_TUNNEL_HOST=127.0.0.1 \
PG_TUNNEL_PORT=15432 \
./pre-upgrade-check/pg_upgrade_pre_check.sh
```

At the prompts, enter the real RDS/Aurora endpoint and its real database port (`5432`), not `127.0.0.1` or the local forwarded port. The script preserves the real endpoint as the libpq host for TLS SNI and hostname verification and uses it for AWS metadata and report identity; the tunnel address is supplied separately as libpq `hostaddr`. Do not place database passwords in environment variables or shell history.

## Output and Safety

- The diagnostic queries are read-only and do not execute DDL or initiate an upgrade.
- A passing report reduces known upgrade risk but does not replace testing on a restored snapshot or non-production clone.
- Resolve hard blockers before scheduling a production upgrade.
- The report can contain database object names, configuration details, and AWS resource metadata. Handle it according to your organization's data-handling requirements.

See the [complete sample report](sample-reports/TestRun_pg15_pre-upgrade-check_report.html) for the existing report format and the [PostgreSQL 19 TLS warning example](sample-reports/PostgreSQL19_TLS_readiness_warning_example.html) for the new non-blocking readiness output.

## Contributors and Contributing

- See [component contributors and reviewers](CONTRIBUTORS.md) for historical attribution.
- See the repository [contribution guidelines](../CONTRIBUTING.md) to report issues or propose changes.

## Related Documentation

- [Repository overview](../README.md)
- [Upgrading Amazon RDS for PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_UpgradeDBInstance.PostgreSQL.html)
- [Upgrading Amazon Aurora PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_UpgradeDBInstance.PostgreSQL.html)
- [License](../LICENSE)
