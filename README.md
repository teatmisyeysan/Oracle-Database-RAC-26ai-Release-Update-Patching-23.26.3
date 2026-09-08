# Oracle-Database-RAC-26ai-Release-Update-Patching (23.26.3.0.0)
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

