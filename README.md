# Aurora RDS PostgreSQL Pre-Upgrade Check Tool

## Overview

The Aurora/RDS PostgreSQL Pre-Upgrade Check Tool helps identify potential issues before upgrading Amazon Aurora PostgreSQL and Amazon RDS PostgreSQL database engines. While the RDS service runs pre-upgrade checks only during the actual upgrade process, this tool allows you to run these checks proactively, helping you identify and resolve issues before initiating the actual upgrade.

## Why This Tool?

Database upgrades require careful planning and can involve application downtime. By identifying potential blockers beforehand:

- You can resolve issues before the scheduled upgrade window
- Reduce the risk of failed upgrades and extended downtime
- Better plan and prepare for the upgrade process
- Save valuable time and resources during the actual upgrade

## About the Tool

This pre-upgrade check tool is written in bash and supports PostgreSQL versions 12 and older. It executes AWS CLI commands and SQL queries to check for common upgrade blockers, including:

- Target PostgreSQL version compatibility
- Unsupported DB instance classes
- Open prepared transactions
- Unsupported reg* data types
- Logical replication slots
- Storage issues
- Incompatible parameters
- Unknown data types
- Read replica upgrade concerns
- PostgreSQL extensions compatibility
- User access issues
- SQL_identifier data type usage
- Views dependency problems
- Database user configuration
- GIST indexes
- ICU Collations
- Reserved keywords
- Templete1 database connection settings
- Maintenance tasks

The tool also provides recommendations for fixing identified issues and references to AWS documentation for best practices.

Version History:
- V05 (NOV 2025):
  - Added support for IAM authentication for Aurora and RDS PostgreSQL
  - Added cross-region support
  - Enhanced SSL certificate handling
  - Added custom port support
  - Fixed Aurora Serverless v2 compatibility issues
  - Improved shared_buffers calculation for Aurora
- V04 
 - Added 5 more checks
 - Supported instance class updated
- V03
  - Support for RDS PostgreSQL added


## Limitations

- The current version runs checks only on the connected database
- Additional scripting is needed to run checks across multiple databases
- While comprehensive, the tool may not catch all edge cases
- This tool complements but does not replace the official RDS pre-upgrade checks

## How to Run the Tool

Requirements:
- An Amazon EC2 instance with:
  - AWS CLI installed and configured
  - PostgreSQL client (psql) installed
  - Network access to your RDS/Aurora PostgreSQL instance

Steps:
1. Download the script to your EC2 instance
2. Make the script executable: `chmod +x pg_upgrade_pre_check.sh`
3. Run the script: `./pg_upgrade_pre_check.sh`
4. Provide the requested information:
   - RDS/Aurora PostgreSQL endpoint URL
   - Database name
   - Port
   - Database username
   - Password
   - Organization name
   - Target PostgreSQL version

The script will generate an HTML report (named `_report.html`) within 2-3 minutes, depending on the instance size.

## Important Notes

- The database user should have READ access to all tables for best results
- No customer data or inputs are stored or collected by the script
- We recommend testing the script in a non-production environment first
- The script only queries system catalogs and runs AWS CLI commands, minimizing performance impact

## After Running the Check

If issues are identified in the report:
1. Review the recommended fixes
2. Implement the suggested changes
3. Run the script again to verify the issues are resolved
4. Consult AWS Support if you need assistance with complex issues

## Sample Reports

Sample reports are available in this repository to help you understand the output format.

## Contributing

We welcome contributions and feedback to improve this tool. Please submit issues and pull requests through GitHub.

## Authors and Acknowledgment

- Author: Vivek Singh
- Contributors:Jonathan Klobucnik, Prashob Krishnan, Jigar Mandli, Rob Kiolbasa
- Reviewers: Umamaheswari Elangovan, Kanhaiya Lal, Deepak Sharma

## License

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
