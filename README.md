# adminbdd
# RLWRAP
$RLWRAP_HOME or /usr/share/rlwrap/completions
https://oracledba.blogspot.com/2019/09/rlwrap-and-auto-completion-in-sqlplus.html
```bash
alias sqlplus='rlwrap -f ~/sql.wordlist sqlplus'
```

# TP1
## 5)
```bash
# BEGIN ANSIBLE MANAGED BLOCK
export ORACLE_BASE="/opt/oracle"
export ORACLE_HOME="$ORACLE_BASE/product/18c/dbhome_1"
export ORACLE_SID=ORCLCDB
# END ANSIBLE MANAGED BLOCK
```

## 6)
```bash
find $ORACLE_HOME/bin -type f -executable
```

# 1
## 1.1
### 1
Fichiers PFILE
```bash
find $ORACLE_BASE -type f -name init.ora
/opt/oracle/product/18c/dbhome_1/srvm/admin/init.ora
/opt/oracle/product/18c/dbhome_1/dbs/init.ora
```
Fichiers SPFILE
```bash
find $ORACLE_BASE -type f -name spfile$ORACLE_SID.ora                                              
/opt/oracle/product/18c/dbhome_1/dbs/spfileORCLCDB.ora
```
### 2
```bash
less $ORACLE_HOME/dbs/init.ora
db_name= 'ORCL'
db_block_size= 8192
remote_login_passwordfile='EXCLUSIVE'
> The password file can only be used by one database. Contains SYS and non SYS users 
```
### 3
```bash
find $ORACLE_BASE -type f -name *.ctl
/opt/oracle/cfgtoollogs/dbca/ORCLCDB/tempControl.ctl
/opt/oracle/oradata/ORCLCDB/control02.ctl
/opt/oracle/oradata/ORCLCDB/control01.ctl
/opt/oracle/product/18c/dbhome_1/md/admin/LoadClobs.ctl
/opt/oracle/product/18c/dbhome_1/assistants/dbca/templates/Seed_Database.ctl
```
### 4
```bash
find $ORACLE_BASE -type f -name *.dbf
/opt/oracle/oradata/ORCLCDB/temp01.dbf
/opt/oracle/oradata/ORCLCDB/pdbseed/undotbs01.dbf
/opt/oracle/oradata/ORCLCDB/pdbseed/sysaux01.dbf
/opt/oracle/oradata/ORCLCDB/pdbseed/system01.dbf
/opt/oracle/oradata/ORCLCDB/pdbseed/temp012019-10-02_11-39-23-059-AM.dbf
/opt/oracle/oradata/ORCLCDB/undotbs01.dbf
/opt/oracle/oradata/ORCLCDB/sysaux01.dbf
/opt/oracle/oradata/ORCLCDB/users01.dbf
/opt/oracle/oradata/ORCLCDB/system01.dbf
/opt/oracle/oradata/ORCLCDB/ORCLPDB1/temp01.dbf
/opt/oracle/oradata/ORCLCDB/ORCLPDB1/undotbs01.dbf
/opt/oracle/oradata/ORCLCDB/ORCLPDB1/sysaux01.dbf
/opt/oracle/oradata/ORCLCDB/ORCLPDB1/users01.dbf
/opt/oracle/oradata/ORCLCDB/ORCLPDB1/system01.dbf
```
## 1.2
### 5
Connexion sqlplus
```bash
sqlplus myusername/mypassword@ORCL
```
Or ici SYSDBA n'est pas un user mais un privilège!
```bash
sqlplus / as sysdba
```
### 6
Dans l'interpréteur SQL
```sql
STARTUP NOMOUNT
```
Attendre un peu, ça prends du temps
```
ORACLE instance started.

Total System Global Area  771747944 bytes
Fixed Size                  8900712 bytes
Variable Size             612368384 bytes
Database Buffers          142606336 bytes
Redo Buffers                7872512 bytes
```

### 7
```sql
ALTER DATABASE MOUNT;
```
database altered.

### 8
```sql
SELECT name FROM V$DATABASE;
NAME
---------
ORCLCDB
```

### 9
```sql
SELECT name,bytes FROM V$DATAFILE;

/opt/oracle/oradata/ORCLCDB/system01.dbf
 880803840

/opt/oracle/oradata/ORCLCDB/sysaux01.dbf
 534773760

/opt/oracle/oradata/ORCLCDB/undotbs01.dbf
 325058560

/opt/oracle/oradata/ORCLCDB/pdbseed/system01.dbf
 283115520

/opt/oracle/oradata/ORCLCDB/pdbseed/sysaux01.dbf
 377487360

/opt/oracle/oradata/ORCLCDB/users01.dbf
   5242880

/opt/oracle/oradata/ORCLCDB/pdbseed/undotbs01.dbf
 104857600

/opt/oracle/oradata/ORCLCDB/ORCLPDB1/system01.dbf
 283115520

/opt/oracle/oradata/ORCLCDB/ORCLPDB1/sysaux01.dbf
 387973120

/opt/oracle/oradata/ORCLCDB/ORCLPDB1/undotbs01.dbf
 104857600

/opt/oracle/oradata/ORCLCDB/ORCLPDB1/users01.dbf
   5242880

```
