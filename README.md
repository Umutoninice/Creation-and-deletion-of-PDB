# Creation-and-deletion-of-PDB
## System user

C:\Users\user>sqlplus sys as sysdba

SQL*Plus: Release 21.0.0.0.0 - Production on Thu Oct 3 12:10:59 2024
Version 21.3.0.0.0

Copyright (c) 1982, 2021, Oracle.  All rights reserved.

Enter password:

Connected to:
Oracle Database 21c Express Edition Release 21.0.0.0.0 - Production
Version 21.3.0.0.0

SQL> SELECT instance_name FROM v$instance;
##INSTANCE_NAME

INSTANCE_NAME
----------------
xe
## showing available pdbs
SQL> show pdbs;

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 XEPDB1                         READ WRITE NO
SQL> SELECT CON_ID, TABLESPACE_NAME, FILE_NAME
  2  FROM CDB_DATA_FILES
  3  WHERE CON_ID = 3;

    CON_ID TABLESPACE_NAME
---------- ------------------------------
FILE_NAME
--------------------------------------------------------------------------------
         3 SYSTEM
C:\APP\USER\PRODUCT\21C\ORADATA\XE\XEPDB1\SYSTEM01.DBF

         3 SYSAUX
C:\APP\USER\PRODUCT\21C\ORADATA\XE\XEPDB1\SYSAUX01.DBF

         3 UNDOTBS1
C:\APP\USER\PRODUCT\21C\ORADATA\XE\XEPDB1\UNDOTBS01.DBF


    CON_ID TABLESPACE_NAME
---------- ------------------------------
FILE_NAME
--------------------------------------------------------------------------------
         3 USERS
C:\APP\USER\PRODUCT\21C\ORADATA\XE\XEPDB1\USERS01.DBF
## creating pluggable database

SQL> CREATE PLUGGABLE DATABASE PLSQL_CLASS2024DB
  2  admin user pdbadmin identified by admin
  3  file_name_convert=('C:\APP\USER\PRODUCT\21C\ORADATA\XE\pdbseed\','C:\APP\USER\PRODUCT\21C\ORADATA\XE\PLSQL_CLASS2024DB\');

Pluggable database created.

SQL> alter session set container=PLSQL_CLASS2024DB;

Session altered.
## opening pdb
SQL> alter pluggable database PLSQL_CLASS2024DB open;

Pluggable database altered.

SQL> alter pluggable database PLSQL_CLASS2024DB save state;

Pluggable database altered.
## creating user ni_plsqlauca
SQL> create user ni_plsqlauca identified by nice23;

User created.

SQL> grant all privileges to ni_plsqlauca;

Grant succeeded.

SQL> show pdbs;

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         4 PLSQL_CLASS2024DB              READ WRITE NO
SQL> alter session set container=cdb$root;

Session altered.
## creating user  ni_to_delete_pdb
SQL> CREATE PLUGGABLE DATABASE ni_to_delete_pdb
  2  from PLSQL_CLASS2024DB
  3  file_name_convert=('C:\APP\USER\PRODUCT\21C\ORADATA\XE\PLSQL_CLASS2024DB\','C:\APP\USER\PRODUCT\21C\ORADATA\XE\ni_to_delete_pdb\');

Pluggable database created.

SQL> show pdbs;

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 XEPDB1                         READ WRITE NO
         4 PLSQL_CLASS2024DB              READ WRITE NO
         5 NI_TO_DELETE_PDB               MOUNTED
SQL> alter session set container=ni_to_delete_pdb;

Session altered.
## opening  ni_to_delete_pdb
SQL> alter pluggable database ni_to_delete_pdb open;

Pluggable database altered.

SQL> alter pluggable database ni_to_delete_pdb save state;

Pluggable database altered.

SQL> show pdbs;

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         5 NI_TO_DELETE_PDB               READ WRITE NO
SQL> alter pluggable database NI_TO_DELETE_PDB close immediate;

Pluggable database altered.


SQL> alter session set container=cdb$root;

Session altered.

SQL> alter pluggable database NI_TO_DELETE_PDB unplug into 'C:\app\user\product\21c\admin\XE\dpdump\ni_to_delete_pdb.xml';

Pluggable database altered.
## deleting ni_to_delete_pdb
SQL> drop pluggable database ni_to_delete_pdb;

Pluggable database dropped.

SQL>
