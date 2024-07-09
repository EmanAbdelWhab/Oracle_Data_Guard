# Oracle Data Guard

## Overview

Oracle Data Guard is a feature of the Oracle Database that ensures high availability, data protection, and disaster recovery for enterprise data. Data Guard maintains standby databases as copies of the primary database. If the primary database becomes unavailable due to planned or unplanned outages, Data Guard can switch the standby database to the primary role, thus minimizing downtime and data loss.

## Key Components of Data Guard

1. **Primary Database**: The production database that is accessed by users.
2. **Standby Database**: A copy of the primary database. There are two types:
   - **Physical Standby**: An exact block-for-block copy of the primary database. It uses Redo Apply to apply redo logs received from the primary.
   - **Logical Standby**: Contains the same logical information as the primary database, although the physical organization and structure of the data can be different. It uses SQL Apply to apply SQL statements.
3. **Data Guard Broker**: A management tool that automates and centralizes the creation, maintenance, and monitoring of Data Guard configurations.

## Data Guard Parameters

Data Guard involves several initialization parameters that control its behavior. Here are some of the most important ones:

### Initialization Parameters for Primary and Standby Databases

1. **DB_NAME**: The database name.
2. **DB_UNIQUE_NAME**: A unique name for each database in the Data Guard configuration.
3. **LOG_ARCHIVE_CONFIG**: Specifies all databases in the Data Guard configuration.
   - Example: `LOG_ARCHIVE_CONFIG='DG_CONFIG=(primary_db,standby_db)'`
4. **LOG_ARCHIVE_DEST_n**: Defines up to 30 (n = 1 to 30) archive log destinations.
   - Example:
     ```sql
     LOG_ARCHIVE_DEST_1='LOCATION=/archivelogs/'
     LOG_ARCHIVE_DEST_2='SERVICE=standby_db VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=standby_db'
     ```
5. **LOG_ARCHIVE_DEST_STATE_n**: Controls the availability state (ENABLE or DEFER) of the corresponding `LOG_ARCHIVE_DEST_n`.
   - Example: `LOG_ARCHIVE_DEST_STATE_2=ENABLE`
6. **FAL_SERVER**: Specifies the FAL (Fetch Archive Log) server for the standby database to request missing archive logs from.
   - Example: `FAL_SERVER=primary_db`
7. **FAL_CLIENT**: Specifies the FAL client for the primary database.
   - Example: `FAL_CLIENT=standby_db`
8. **STANDBY_FILE_MANAGEMENT**: Controls automatic addition of new datafiles to the standby database.
   - Example: `STANDBY_FILE_MANAGEMENT=AUTO`
9. **REMOTE_LOGIN_PASSWORDFILE**: Ensures the same password file is used on both primary and standby databases.
   - Example: `REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE`
10. **DB_FILE_NAME_CONVERT**: Converts primary database filenames to standby database filenames.
    - Example: `DB_FILE_NAME_CONVERT='/primary_data/','/standby_data/'`
11. **LOG_FILE_NAME_CONVERT**: Converts primary database log filenames to standby database log filenames.
    - Example: `LOG_FILE_NAME_CONVERT='/primary_logs/','/standby_logs/'`
12. **ARCHIVE_LAG_TARGET**: Sets a limit on the amount of data that can be lost in the event of a failover.
    - Example: `ARCHIVE_LAG_TARGET=1800`

## Data Guard Broker Parameters

When using Data Guard Broker, additional configuration is done using the `DGMGRL` command-line interface or Oracle Enterprise Manager. Some important Data Guard Broker configuration parameters include:

1. **Configuration Protection Mode**:
   - **Maximum Protection**: No data loss.
   - **Maximum Availability**: Minimal data loss.
   - **Maximum Performance**: Performance-focused with potential for some data loss.
2. **Fast-Start Failover (FSFO)**:
   - Enables automatic failover to a standby database if the primary database becomes unavailable.
   - Parameters include `FastStartFailoverTarget`, `FastStartFailoverThreshold`, and `FastStartFailoverAutoReinstate`.
3. **Redo Transport Services**:
   - **SYNC**: Synchronous redo transport for zero data loss.
   - **ASYNC**: Asynchronous redo transport for improved performance.

