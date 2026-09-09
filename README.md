# Oracle-Database-RAC-26ai-Release-Update-Patching (23.26.3.0.0)
This article provides a practical overview of applying Oracle Database 26ai Release Update (RU) 23.26.3.0.0 in an Oracle RAC environment.

## Prerequisite
Before starting the Oracle Database RU patching, make sure Oracle Grid Infrastructure has already been successfully patched to Release Update 23.26.3.0.0 on all RAC nodes.

## 1) Check the OPatch Version
```sql
cd /soft/patches/
[oracle@ms-ol-node-01 patches]$ ls -ltr
total 3078236
drwxr-xr-x. 8 grid oinstall        159 Jul  6 02:26 39568945
-rw-rw-r--. 1 grid oinstall       2690 Aug 13 22:32 PatchSearch.xml
-rwxrwxr-x. 1 grid oinstall 3126777339 Sep  1 14:19 p39568945_230000_Linux-x86-64.zip
-rw-r--r--. 1 grid oinstall   25323536 Sep  1 14:43 p6880880_230000_Linux-x86-64.zip
-rw-r--r--. 1 grid oinstall        161 Sep  2 15:36 patch_list_gihome.txt
[oracle@ms-ol-node-01 patches]$
```
### Update OPatch Version
```sql
[oracle@ms-ol-node-01 patches]$ cd $ORACLE_HOME
[oracle@ms-ol-node-01 dbhome_1]$ mv OPatch OPatch.bak
[oracle@ms-ol-node-01 dbhome_1]$ unzip p6880880_230000_Linux-x86-64.zip -d $ORACLE_HOME

[oracle@ms-ol-node-01 patches]$ $ORACLE_HOME/OPatch/opatch version
OPatch Version: 12.2.0.1.52
OPatch succeeded.
[oracle@ms-ol-node-01 patches]$
```

## 2) Check minimum opatch version
```sql
[oracle@ms-ol-node-01 patches]$ $ORACLE_HOME/OPatch/opatch prereq CheckMinimumOPatchVersion -phBaseDir  /soft/patches/39568945/39578879                   Oracle Interim Patch Installer version 12.2.0.1.52
Copyright (c) 2026, Oracle Corporation.  All rights reserved.

PREREQ session

Oracle Home       : /u01/app/oracle/product/26.0.0/dbhome_1
Central Inventory : /u01/app/oraInventory
   from           : /u01/app/oracle/product/26.0.0/dbhome_1/oraInst.loc
OPatch version    : 12.2.0.1.52
OUI version       : 12.2.0.9.0
Log file location : /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatch/opatch2026-09-08_16-51-27PM_1.log

Invoking prereq "checkminimumopatchversion"

Prereq "checkMinimumOPatchVersion" passed.

OPatch succeeded.
[oracle@ms-ol-node-01 patches]$
```
## 3) Check conflict patch 
```sql
[oracle@ms-ol-node-01 patches]$ $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /soft/patches/39568945/39578879
Oracle Interim Patch Installer version 12.2.0.1.52
Copyright (c) 2026, Oracle Corporation.  All rights reserved.

PREREQ session

Oracle Home       : /u01/app/oracle/product/26.0.0/dbhome_1
Central Inventory : /u01/app/oraInventory
   from           : /u01/app/oracle/product/26.0.0/dbhome_1/oraInst.loc
OPatch version    : 12.2.0.1.52
OUI version       : 12.2.0.9.0
Log file location : /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatch/opatch2026-09-08_16-52-23PM_1.log

Invoking prereq "checkconflictagainstohwithdetail"

Prereq "checkConflictAgainstOHWithDetail" passed.

OPatch succeeded.
[oracle@ms-ol-node-01 patches]$
[oracle@ms-ol-node-01 patches]$ $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /soft/patches/39568945/39578859
Oracle Interim Patch Installer version 12.2.0.1.52
Copyright (c) 2026, Oracle Corporation.  All rights reserved.

PREREQ session

Oracle Home       : /u01/app/oracle/product/26.0.0/dbhome_1
Central Inventory : /u01/app/oraInventory
   from           : /u01/app/oracle/product/26.0.0/dbhome_1/oraInst.loc
OPatch version    : 12.2.0.1.52
OUI version       : 12.2.0.9.0
Log file location : /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatch/opatch2026-09-08_16-53-11PM_1.log

Invoking prereq "checkconflictagainstohwithdetail"

Prereq "checkConflictAgainstOHWithDetail" passed.

OPatch succeeded.
[oracle@ms-ol-node-01 patches]$
```
## 4) Stop database node1 
```sql
[oracle@ms-ol-node-01 patches]$ srvctl stop instance -d cdb26ai -i cdb26ai1
[oracle@ms-ol-node-01 patches]$ srvctl status database -d cdb26ai
Instance cdb26ai1 is not running on node ms-ol-node-01
Instance cdb26ai2 is running on node ms-ol-node-02
[oracle@ms-ol-node-01 patches]$
```
## 5) Apply patch using OPATCHAUTO
```sql
[root@ms-ol-node-01 output]# /u01/app/oracle/product/26.0.0/dbhome_1/OPatch/opatchauto apply /soft/patches/39568945 -oh /u01/app/oracle/product/26.0.0/dbhome_1

OPatchauto session is initiated at Tue Sep  8 16:57:30 2026

System initialization log file is /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatchautodb/systemconfig2026-09-08_04-57-41PM.log.

Session log file is /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatchauto/opatchauto2026-09-08_04-58-05PM.log
The id for this session is N2JZ

Executing OPatch prereq operations to verify patch applicability on home /u01/app/oracle/product/26.0.0/dbhome_1
Patch applicability verified successfully on home /u01/app/oracle/product/26.0.0/dbhome_1


Executing patch validation checks on home /u01/app/oracle/product/26.0.0/dbhome_1
Patch validation checks successfully completed on home /u01/app/oracle/product/26.0.0/dbhome_1


Updating OJVM libraries on all hosts for home /u01/app/oracle/product/26.0.0/dbhome_1
Successfully updated OJVM libraries on all hosts


Enabling two-stage patches on home /u01/app/oracle/product/26.0.0/dbhome_1
Successfully enabled two-stage patches on home /u01/app/oracle/product/26.0.0/dbhome_1


Verifying SQL patch applicability on home /u01/app/oracle/product/26.0.0/dbhome_1
SQL patch applicability verified successfully on home /u01/app/oracle/product/26.0.0/dbhome_1


Preparing to bring down database service on home /u01/app/oracle/product/26.0.0/dbhome_1

WARNING: The service cdb26ai_AI26PDB1 configured on cdb26ai will not be switched as it is not configured to run on any other node(s).
Successfully prepared home /u01/app/oracle/product/26.0.0/dbhome_1 to bring down database service


Bringing down database service on home /u01/app/oracle/product/26.0.0/dbhome_1
Database service successfully brought down on home /u01/app/oracle/product/26.0.0/dbhome_1


Performing prepatch operation on home /u01/app/oracle/product/26.0.0/dbhome_1
Prepatch operation completed successfully on home /u01/app/oracle/product/26.0.0/dbhome_1


Start applying binary patch on home /u01/app/oracle/product/26.0.0/dbhome_1
Binary patch applied successfully on home /u01/app/oracle/product/26.0.0/dbhome_1


Running rootadd_rdbms.sh on home /u01/app/oracle/product/26.0.0/dbhome_1
Successfully executed rootadd_rdbms.sh on home /u01/app/oracle/product/26.0.0/dbhome_1


Performing postpatch operation on home /u01/app/oracle/product/26.0.0/dbhome_1
Postpatch operation completed successfully on home /u01/app/oracle/product/26.0.0/dbhome_1


Starting database service on home /u01/app/oracle/product/26.0.0/dbhome_1
Database service successfully started on home /u01/app/oracle/product/26.0.0/dbhome_1


Preparing home /u01/app/oracle/product/26.0.0/dbhome_1 after database service restarted
No step execution required.........


Trying to apply SQL patch on home /u01/app/oracle/product/26.0.0/dbhome_1
No SQL patch operations are required on local node for this home

OPatchAuto successful.

--------------------------------Summary--------------------------------

Patching is completed successfully. Please find the summary as follows:

Host:ms-ol-node-01
RAC Home:/u01/app/oracle/product/26.0.0/dbhome_1
Version:23.0.0.0.0
Summary:

==Following patches were SKIPPED:

Patch: /soft/patches/39568945/39578865
Reason: This patch is not applicable to this specified target type - "rac_database"

Patch: /soft/patches/39568945/39578862
Reason: This patch is not applicable to this specified target type - "rac_database"

Patch: /soft/patches/39568945/39578856
Reason: This patch is not applicable to this specified target type - "rac_database"


==Following patches were SUCCESSFULLY applied:

Patch: /soft/patches/39568945/39578859
Log: /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatchauto/core/opatch/opatch2026-09-08_16-59-19PM_1.log

Patch: /soft/patches/39568945/39578879
Log: /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatchauto/core/opatch/opatch2026-09-08_16-59-19PM_1.log


Patching session reported following warning(s):
_________________________________________________

[WARNING] The database instance 'cdb26ai1' from '/u01/app/oracle/product/26.0.0/dbhome_1', in host'ms-ol-node-01' is not running. SQL changes, if any,  will not be applied.
To apply. the SQL changes, bring up the database instance and run the command manually from any one node (run as oracle).
Refer to the readme to get the correct steps for applying the sql changes.

OPatchauto session completed at Tue Sep  8 17:06:58 2026
Time taken to complete the session 9 minutes, 18 seconds
[root@ms-ol-node-01 output]#

```
## 6) Validate patch applied
```sql
[oracle@ms-ol-node-01 patches]$ $ORACLE_HOME/OPatch/opatch lspatches
39578879;Database Release Update : 23.26.3.0.0 (39578879)
39578859;OCW RELEASE UPDATE 23.26.3.0.0 (39578859)

OPatch succeeded.
[oracle@ms-ol-node-01 patches]$
```

## 7) Start database instance 1
```sql
[oracle@ms-ol-node-01 patches]$ srvctl start instance -d cdb26ai -i cdb26ai1
[oracle@ms-ol-node-01 patches]$ srvctl status database -d cdb26ai
Instance cdb26ai1 is running on node ms-ol-node-01
Instance cdb26ai2 is running on node ms-ol-node-02
[oracle@ms-ol-node-01 patches]$
```

## 8) Proceed the same from 1 to 7 on node2 (ms-ol-node-02)
```sql
[oracle@ms-ol-node-02 patches]$ $ORACLE_HOME/OPatch/opatch version
OPatch Version: 12.2.0.1.52

OPatch succeeded.
[oracle@ms-ol-node-02 patches]$
[oracle@ms-ol-node-02 patches]$
[oracle@ms-ol-node-02 patches]$ $ORACLE_HOME/OPatch/opatch lspatches
38743688;OCW RELEASE UPDATE 23.26.1.0.0 (GOLD IMAGE) (38743688) Gold Image
38743669;Database Release Update : 23.26.1.0.0 (38743669) Gold Image

OPatch succeeded.
[oracle@ms-ol-node-02 patches]$
[oracle@ms-ol-node-02 patches]$
[oracle@ms-ol-node-02 patches]$
[oracle@ms-ol-node-02 patches]$ $ORACLE_HOME/OPatch/opatch prereq CheckMinimumOPatchVersion -phBaseDir  /software/patches/39568945/39578879
Oracle Interim Patch Installer version 12.2.0.1.52
Copyright (c) 2026, Oracle Corporation.  All rights reserved.

PREREQ session

Oracle Home       : /u01/app/oracle/product/26.0.0/dbhome_1
Central Inventory : /u01/app/oraInventory
   from           : /u01/app/oracle/product/26.0.0/dbhome_1/oraInst.loc
OPatch version    : 12.2.0.1.52
OUI version       : 12.2.0.9.0
Log file location : /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatch/opatch2026-09-08_17-10-33PM_1.log

Invoking prereq "checkminimumopatchversion"

Prereq "checkMinimumOPatchVersion" passed.

OPatch succeeded.
[oracle@ms-ol-node-02 patches]$
[oracle@ms-ol-node-02 patches]$
[oracle@ms-ol-node-02 patches]$ $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /software/patches/39568945/39578879
Oracle Interim Patch Installer version 12.2.0.1.52
Copyright (c) 2026, Oracle Corporation.  All rights reserved.

PREREQ session

Oracle Home       : /u01/app/oracle/product/26.0.0/dbhome_1
Central Inventory : /u01/app/oraInventory
   from           : /u01/app/oracle/product/26.0.0/dbhome_1/oraInst.loc
OPatch version    : 12.2.0.1.52
OUI version       : 12.2.0.9.0
Log file location : /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatch/opatch2026-09-08_17-10-54PM_1.log

Invoking prereq "checkconflictagainstohwithdetail"

Prereq "checkConflictAgainstOHWithDetail" passed.

OPatch succeeded.
[oracle@ms-ol-node-02 patches]$ $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /software/patches/39568945/39578859
Oracle Interim Patch Installer version 12.2.0.1.52
Copyright (c) 2026, Oracle Corporation.  All rights reserved.

PREREQ session

Oracle Home       : /u01/app/oracle/product/26.0.0/dbhome_1
Central Inventory : /u01/app/oraInventory
   from           : /u01/app/oracle/product/26.0.0/dbhome_1/oraInst.loc
OPatch version    : 12.2.0.1.52
OUI version       : 12.2.0.9.0
Log file location : /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatch/opatch2026-09-08_17-11-05PM_1.log

Invoking prereq "checkconflictagainstohwithdetail"

Prereq "checkConflictAgainstOHWithDetail" passed.

OPatch succeeded.
[oracle@ms-ol-node-02 patches]$
```
### Apply patch using OPATCHAUTO On Node2
```sql
[root@ms-ol-node-02 install]# /u01/app/oracle/product/26.0.0/dbhome_1/OPatch/opatchauto apply /software/patches/39568945 -oh /u01/app/oracle/product/26.0.0/dbhome_1

OPatchauto session is initiated at Tue Sep  8 17:13:20 2026

System initialization log file is /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatchautodb/systemconfig2026-09-08_05-13-30PM.log.


Session log file is /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatchauto/opatchauto2026-09-08_05-24-44PM.log
The id for this session is 6ZTS

Executing OPatch prereq operations to verify patch applicability on home /u01/app/oracle/product/26.0.0/dbhome_1
Patch applicability verified successfully on home /u01/app/oracle/product/26.0.0/dbhome_1


Executing patch validation checks on home /u01/app/oracle/product/26.0.0/dbhome_1
Patch validation checks successfully completed on home /u01/app/oracle/product/26.0.0/dbhome_1


Enabling two-stage patches on home /u01/app/oracle/product/26.0.0/dbhome_1
Successfully enabled two-stage patches on home /u01/app/oracle/product/26.0.0/dbhome_1


Verifying SQL patch applicability on home /u01/app/oracle/product/26.0.0/dbhome_1
SQL patch applicability verified successfully on home /u01/app/oracle/product/26.0.0/dbhome_1


Preparing to bring down database service on home /u01/app/oracle/product/26.0.0/dbhome_1
No step execution required.........


Bringing down database service on home /u01/app/oracle/product/26.0.0/dbhome_1
Database service successfully brought down on home /u01/app/oracle/product/26.0.0/dbhome_1


Performing prepatch operation on home /u01/app/oracle/product/26.0.0/dbhome_1
Prepatch operation completed successfully on home /u01/app/oracle/product/26.0.0/dbhome_1


Start applying binary patch on home /u01/app/oracle/product/26.0.0/dbhome_1

Binary patch applied successfully on home /u01/app/oracle/product/26.0.0/dbhome_1


Running rootadd_rdbms.sh on home /u01/app/oracle/product/26.0.0/dbhome_1
Successfully executed rootadd_rdbms.sh on home /u01/app/oracle/product/26.0.0/dbhome_1


Performing postpatch operation on home /u01/app/oracle/product/26.0.0/dbhome_1
Postpatch operation completed successfully on home /u01/app/oracle/product/26.0.0/dbhome_1


Starting database service on home /u01/app/oracle/product/26.0.0/dbhome_1
Database service successfully started on home /u01/app/oracle/product/26.0.0/dbhome_1


Preparing home /u01/app/oracle/product/26.0.0/dbhome_1 after database service restarted
No step execution required.........


Trying to apply SQL patch on home /u01/app/oracle/product/26.0.0/dbhome_1
No SQL patch operations are required on local node for this home

OPatchAuto successful.

--------------------------------Summary--------------------------------

Patching is completed successfully. Please find the summary as follows:

Host:ms-ol-node-02
RAC Home:/u01/app/oracle/product/26.0.0/dbhome_1
Version:23.0.0.0.0
Summary:

==Following patches were SKIPPED:

Patch: /software/patches/39568945/39578865
Reason: This patch is not applicable to this specified target type - "rac_database"

Patch: /software/patches/39568945/39578862
Reason: This patch is not applicable to this specified target type - "rac_database"

Patch: /software/patches/39568945/39578856
Reason: This patch is not applicable to this specified target type - "rac_database"


==Following patches were SUCCESSFULLY applied:

Patch: /software/patches/39568945/39578859
Log: /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatchauto/core/opatch/opatch2026-09-08_17-26-39PM_1.log

Patch: /software/patches/39568945/39578879
Log: /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/opatchauto/core/opatch/opatch2026-09-08_17-26-39PM_1.log


Patching session reported following warning(s):
_________________________________________________

[WARNING] The database instance 'cdb26ai2' from '/u01/app/oracle/product/26.0.0/dbhome_1', in host'ms-ol-node-02' is not running. SQL changes, if any,  will not be applied.
To apply. the SQL changes, bring up the database instance and run the command manually from any one node (run as oracle).
Refer to the readme to get the correct steps for applying the sql changes.

OPatchauto session completed at Tue Sep  8 17:34:33 2026
Time taken to complete the session 21 minutes, 3 seconds
[root@ms-ol-node-02 install]#
```
### Start database instance on node2 
```sql
[oracle@ms-ol-node-02 patches]$ srvctl start instance -d cdb26ai -i cdb26ai2
[oracle@ms-ol-node-02 patches]$
[oracle@ms-ol-node-02 patches]$ ps -ef |grep pmon
grid     1452459       1  0 11:24 ?        00:00:07 asm_pmon_+ASM2
oracle   1684382       1  0 17:34 ?        00:00:00 ora_pmon_cdb26ai2
oracle   1685388 1480216  0 17:35 pts/3    00:00:00 grep --color=auto pmon
[oracle@ms-ol-node-02 patches]$
[oracle@ms-ol-node-02 patches]$ $ORACLE_HOME/OPatch/opatch lspatches
39578879;Database Release Update : 23.26.3.0.0 (39578879)
39578859;OCW RELEASE UPDATE 23.26.3.0.0 (39578859)

OPatch succeeded.
[oracle@ms-ol-node-02 patches]$
```

## 9) Run Datapatch 
### Run sanity check (Optional)
```sql
[oracle@ms-ol-node-01 ~]$ cd $ORACLE_HOME/OPatch
[oracle@ms-ol-node-01 OPatch]$ ./datapatch -sanity_checks
SQL Patching sanity checks version 23.26.3.0.0 on Wed 09 Sep 2026 10:34:43 AM +07
Copyright (c) 2021, 2026, Oracle.  All rights reserved.

Log file for this invocation: /u01/app/oracle/cfgtoollogs/sqlpatch/sanity_checks_20260909_103443_214227/sanity_checks_20260909_103443_214227.log

RAC detected, verifying SSH connection...
Done

Warning: SSH connection between nodes failed.
Some system checks will run only on the current node and others will be skipped.
Refer to debug log for more details

Running checks
JSON report generated in /u01/app/oracle/cfgtoollogs/sqlpatch/sanity_checks_20260909_103443_214227/sqlpatch_sanity_checks_summary.json file
Checks completed. Printing report:

Check: Database component status - OK
Check: PDB Violations - OK
Check: Invalid System Objects - OK
Check: Tablespace Status - OK
Check: Backup jobs - OK
Check: Temp file exists - OK
Check: Temp file online - OK
Check: Data Pump running - OK
Check: Container status - OK
Check: Oracle Database Keystore - OK
Check: Dictionary statistics gathering - WARNING
  Patching the database without recent data dictionary statistics gathered may lead to performance issues.
  Data dictionary statistics are older than 7 days.
  Run the following queries to start gathering the dictionary statistics:
    EXEC DBMS_STATS.GATHER_DICTIONARY_STATS;
    EXEC DBMS_SYSTEM.GATHER_FIXED_OBJECTS_STATS;
  Refer to MOS 457926.1 for more details.
  AI26PDB1:
    |     LATEST      |        OPERATION        |  STATUS   |
    |-----------------+-------------------------+-----------|
    | 28-AUG-26 10:23 | gather_dictionary_stats | COMPLETED |
    |-----------------+-------------------------+-----------|
  CDB$ROOT:
    |     LATEST      |        OPERATION        |  STATUS   |
    |-----------------+-------------------------+-----------|
    | 28-AUG-26 10:22 | gather_dictionary_stats | COMPLETED |
    |-----------------+-------------------------+-----------|
  PDB$SEED:
    |     LATEST      |        OPERATION        |  STATUS   |
    |-----------------+-------------------------+-----------|
    | 28-AUG-26 10:23 | gather_dictionary_stats | COMPLETED |
    |-----------------+-------------------------+-----------|
Check: Scheduled Jobs - OK
Check: GoldenGate triggers - OK
Check: GoldenGate Integrated Extract/Replicat - OK
Check: Logminer DDL triggers - OK
Check: Check sys public grants - OK
Check: Statistics gathering running - OK
Check: Optim dictionary upgrade parameter - OK
Check: Symlinks on oracle home path - OK
Check: Java Virtual Machine Enable - OK
Check: Oracle Database Vault Enabled - OK
Check: Looping Chain Synonyms - OK
Check: Duplicated ORACLE_HOME values - SKIPPED
  Message: Skipped as SSH remote connection failed.
Check: Recyclebin Empty - OK
Check: Central Inventory - OK
Check: Queryable Inventory locks - OK
Check: Queryable Inventory database directories - OK
Check: Queryable Inventory external table - OK
Check: Queryable Inventory package - OK
Check: Imperva processes - OK
Check: Guardium processes - OK
Check: Locale - OK

Refer to MOS Note 2975965.1 and debug log
/u01/app/oracle/cfgtoollogs/sqlpatch/sanity_checks_20260909_103443_214227/sanity_checks_debug_20260909_103443_214227.log

SQL Patching sanity checks completed on Wed 09 Sep 2026 10:35:41 AM +07
[oracle@ms-ol-node-01 OPatch]$
```

### Execute datapatch -verbose
```sql
[oracle@ms-ol-node-01 OPatch]$ cd $ORACLE_HOME/OPatch
[oracle@ms-ol-node-01 OPatch]$
[oracle@ms-ol-node-01 OPatch]$ ./datapatch -verbose
SQL Patching tool version 23.26.3.0.0 Production on Wed Sep  9 10:36:44 2026
Copyright (c) 2012, 2026, Oracle.  All rights reserved.

Log file for this invocation: /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/sqlpatch/sqlpatch_sid_cdb26ai1_ts_2026_09_09_10_36_44_pid_218096/sqlpatch_invocation.log

Connecting to database...OK
Gathering database info...done

Note:  Datapatch will only apply or rollback SQL fixes for PDBs
       that are in an open state, no patches will be applied to closed PDBs.
       Please refer to Note: Datapatch: Database 12c Post Patch SQL Automation
       (Doc ID 1585822.1)

Bootstrapping registry and package to current versions...done
Determining current state...done

Current state of interim SQL patches:
  No interim patches found

Current state of release update SQL patches:
  Binary registry:
    23.26.3.0.0 Release_Update 260705162604: Installed
  PDB AI26PDB1:
    Applied 23.26.1.0.0 Release_Update 260117080636 successfully on 28-AUG-26 10.21.28.248235 AM
  PDB CDB$ROOT:
    Applied 23.26.1.0.0 Release_Update 260117080636 successfully on 28-AUG-26 10.21.27.878214 AM
  PDB PDB$SEED:
    Applied 23.26.1.0.0 Release_Update 260117080636 successfully on 28-AUG-26 10.21.28.248235 AM

Adding patches to installation queue and performing prereq checks...done
Installation queue:
  For the following PDBs: CDB$ROOT PDB$SEED AI26PDB1
    No interim patches need to be rolled back
    Patch 39578879 (Database Release Update : 23.26.3.0.0 (39578879)):
      Apply from 23.26.1.0.0 Release_Update 260117080636 to 23.26.3.0.0 Release_Update 260705162604
    No interim patches need to be applied

Installing patches...

Patch installation complete.  Total patches installed: 3

Validating logfiles...done
Patch 39578879 apply (pdb CDB$ROOT): SUCCESS
  logfile: /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/sqlpatch/sqlpatch_sid_cdb26ai1_ts_2026_09_09_10_36_44_pid_218096/39578879_apply_CDB26AI_CDBROOT_2026Sep09_10_38_02.log (no errors)
Patch 39578879 apply (pdb PDB$SEED): SUCCESS
  logfile: /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/sqlpatch/sqlpatch_sid_cdb26ai1_ts_2026_09_09_10_36_44_pid_218096/39578879_apply_CDB26AI_PDBSEED_2026Sep09_10_51_51.log (no errors)
Patch 39578879 apply (pdb AI26PDB1): SUCCESS
  logfile: /u01/app/oracle/product/26.0.0/dbhome_1/cfgtoollogs/sqlpatch/sqlpatch_sid_cdb26ai1_ts_2026_09_09_10_36_44_pid_218096/39578879_apply_CDB26AI_AI26PDB1_2026Sep09_10_51_50.log (no errors)
SQL Patching tool complete on Wed Sep  9 11:02:11 2026
[oracle@ms-ol-node-01 OPatch]$
```

## 10) Validate Patch
### Check INVALID Objects
```sql
set lines 400 pages 40000
col owner for a15
col object_name for a35
select owner, object_name, object_type, status from dba_objects where status='INVALID';
```

### Check dba_registry & dba_registry_sqlpatch
```sql
SQL> set lines 400 pages 400
col COMP_ID for a15
col COMP_NAME for a40
col VERSION for a20
col STATUS for a20
select COMP_ID, COMP_NAME, VERSION, STATUS from dba_registry;SQL> SQL> SQL> SQL> SQL>

COMP_ID         COMP_NAME                                VERSION              STATUS
--------------- ---------------------------------------- -------------------- --------------------
CATALOG         Oracle Database Catalog Views            23.0.0.0.0           VALID
CATPROC         Oracle Database Packages and Types       23.0.0.0.0           VALID
RAC             Oracle Real Application Clusters         23.0.0.0.0           VALID
JAVAVM          JServer JAVA Virtual Machine             23.0.0.0.0           VALID
XML             Oracle XDK                               23.0.0.0.0           VALID
CATJAVA         Oracle Database Java Packages            23.0.0.0.0           VALID
APS             OLAP Analytic Workspace                  23.0.0.0.0           VALID
XDB             Oracle XML Database                      23.0.0.0.0           VALID
OWM             Oracle Workspace Manager                 23.0.0.0.0           VALID
CONTEXT         Oracle Text                              23.0.0.0.0           VALID
XOQ             Oracle OLAP API                          23.0.0.0.0           VALID
SDO             Spatial                                  23.0.0.0.0           VALID
OLS             Oracle Label Security                    23.0.0.0.0           VALID
DV              Oracle Database Vault                    23.0.0.0.0           VALID

14 rows selected.

SQL>
```
```sql
SQL> set lines 500 pages 1000
set long 2000000
col action_time format a12
col action format a10
col comments format a30
col description format a60
col namespace format a20
col status format a10
select to_char(action_time, 'yyyy-mm-dd') as action_time, action, status, description, patch_id from sys.dba_registry_sqlpatch order by action_time;

SQL> SQL> SQL> SQL> SQL> SQL> SQL> SQL>
ACTION_TIME  ACTION     STATUS     DESCRIPTION                                                    PATCH_ID
------------ ---------- ---------- ------------------------------------------------------------ ----------
2026-08-28   APPLY      SUCCESS    Database Release Update : 23.26.1.0.0 (38743669) Gold Image    38743669
2026-09-09   APPLY      SUCCESS    Database Release Update : 23.26.3.0.0 (39578879)               39578879

SQL>
```

