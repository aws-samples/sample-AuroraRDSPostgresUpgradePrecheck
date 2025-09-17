# Aurora RDS PostgreSQL Sample Pre Upgrade Check Automation Tool



## Getting started

As the part of Amazon Aurora for PostgreSQL and RDS PostgreSQL database engine upgrade, RDS service runs pre-upgrade-checks which provides any incompatibilities or issues that needs to be addressed before upgrade can be initiated. This pre-checks are run only when the actual upgrade is initiated. Customers need a feature where the upgrade pre-checks can be run beforehand to identify any issues, resolve them and be ready for the fail-safe actual upgrade. This way customers are ensured that while running the actual upgrade, the pre-checks will pass. We have PFR for this, and as of now, we don't have any ETA for this feature.

The Aurora/RDS PostgreSQL pre-upgrade check automation tool aims for fill this gap, identifies major blockers for upgrade, and recommends steps to fix the issue. The pre-upgrade-check exercise saves potential downtime during Aurora and RDS Postgres upgrades.

## Why tool is needed?
Many customers have asked for the feature of running pre-upgrade-check isolated from RDS upgrade cycle so that they can identify blockers or issues while running actual upgrading Aurora/RDS PostgreSQL databases. This helps them managing downtime and preparing for applications outage. As of now, RDS service runs the pre-upgrade during the downtime, and if any check fails, forces customer to plan the upgrade again. Planning database upgrade requires substantial resources including  application/service outage and DBA hours. By identifying and resolving upgrade blockers beforehand, customers can manage upgrade better and save precious downtime.

## About the tool
Aurora/RDS PostgreSQL pre-upgrade check automation tool is written in bash language. The script identifies RDS and Aurora PostgreSQL version 10-14, runs pre-check commands and suggests fixes. The tool runs 9 AWS CLI commands and 15 SQL queries to run checks for:

1. Target Postgres version
2. Unsupported DB instance classes
3. Open prepared transactions
4. Unsupported reg* data types
5. Logical replication slots
6. Storage issues
7. 'Incompatible Parameter' error
8. Unknown data types
9. Read replica upgrade failure
10. Postgres extensions
11. User access
12. 'sql_identifier' data type
13. Views dependency
14. Incorrect primary user name
15. Maintenance task

In addition, the tool recommends a few AWS user-guides and data blogs for further readings on upgrade steps and best practices.

## Does this replace RDS service pre-upgrade-check during upgrade cycle?
The tool is built after researching 100s of support cases and TTs/SIMs identifying most common Postgres upgrade failure reasons. Though it targets to catch most of the blockers, it doesn't guarantee to replace RDS service. The tool is built on best effort basis and continuous feedback is needed for more comprehensive checks.

## What are limitations of PostgreSQL pre-upgrade check automation tool?
Tool version V01 runs the check only on the database connected. Further scripting will be needed to run the script on other user databases. The checks included are based on research and selecting top checks. This means it may miss certain edge cases.

## How to share the script with customer?
Script is hosted at S3. You can share this link with customer to share the script.

## How to run the script?
Customers need to run the script on an EC2 instance. EC2 instance should have AWS CLI and psql client installed. Below are the steps to run the script:

1. Copy script on Amazon EC2 Linux instance with AWS CLI configured, and psql client installed with accessibility to RDS/Aurora Postgres instance
2. Make script executable: chmod +x pg_upgrade_pre_check.sh
3. Run the script: ./pg_upgrade_pre_check.sh
4. Use the RDS PostgreSQL or Aurora PostgreSQL Cluster endpoint URL for connection
5. The database user should have READ access on all of the tables to get better metrics
6. It will take around 2-3 mins to run (depending on size of instance), and generate html report:  <CompanyName>_<DatabaseIdentifier>_report_<date>.html

## What are the inputs for the script?
Customers need to provide RDS/Aurora PostgreSQL endpoint URL, database name, port, database user name, password and company name, and target Postgres version.

## Do we store any customer inputs?
Inputs are used to run the script successfully. We don't store any inputs.

## Does script generate recommendations?

Script recommends fixes for the failed checks, and mentions best practices for the PostgreSQL upgrade user-guides and blogs.

## Is there any performance impact while running the script?
Script only queries system catalogs and runs AWS CLI commands. Typically, these commands are quick and doesn't impact performance. Its recommended to read the script thoroughly, and test it in testing environment before running it in production environment.

## Do we collect any customer data?
The script doesn't collect any customer data.

## What is the next step after getting pre-upgrade check HTML report?
Open a support case for further investigation.

## Where can I find the sample report?
Sample reports are uploaded in the same repo.

## Do we have any customer wordings for sharing the script?

Postgres pre-upgrade-checks tool aims to identify most of the issues and blockers for Aurora and RDS PostgreSQL db engine version upgrades. The tool identifies issues that can fail upgrades, and recommends fixes. The tool is built on best effort basis and doesn't replace the RDS service pre-checks during actual upgrade cycle. The user running this script should have access to all tables, and the tool should be installed on and EC2 instance with AWS CLI installed and connectivity to PostgreSQL database via psql client. We recommend to go through the script and run in the testing environment first. Below is the link for the script:

<link>

How can share feedback and suggestions about the script?
Reach out to @siviv for any feedback and suggestions about the tool.

## Authors and acknowledgment
Author: Vivek Singh (@siviv)
Reviewers: @elangovu, @llkanha, @dzsharma

## License
Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.

