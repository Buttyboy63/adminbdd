# adminbdd
# RLWRAP
$RLWRAP_HOME or /usr/share/rlwrap/completions
https://oracledba.blogspot.com/2019/09/rlwrap-and-auto-completion-in-sqlplus.html
```bash
alias sqlplus='rlwrap -f ~/sql.wordlist sqlplus'
```
# config sqlplus
set linesize 512

column NAME format a32

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

# TP2
## 1.1
```sql 
select name,open_mode from v$pdbs;
```
```
PDB$SEED MOUNTED

ORCLPDB1 MOUNTED
```
## 1.2
Avant de monter la pluggable DB il faut s'assurer que la BD conteneur est monté et ouvert en READ WRITE
```sql 
select name,open_mode from V$DATABASE;
```


Si pas mounted -> 
```sql 
STARTUP
```


Sinon si pas READ WRITE -> 
```sql 
ALTER DATABASE ORCLCDB OPEN READ WRITE;
```
voili voilou

```sql 
ALTER PLUGGABLE DATABASE ORCLPDB1 OPEN READ WRITE;```

## 1.3
bdd courante: 
```sql 
SHOW con_name;
```
"CDB$ROOT"

user courant: 
```sql 
show user;
```
"USER is 'SYS'"

Nombre users: 
```sql 
SELECT COUNT(*) FROM all_users;
``` 
"35"

## 1.4
```sql 
ALTER SESSION SET CONTAINER = ORCLPDB1;
```

bdd courante: 
```sql 
SHOW con_name;
``` 
"ORCLPDB1"

user courant: 
```sql 
show user;
``` 
"USER is 'SYS'"

Nombre users: 
```sql 
SELECT COUNT(*) FROM all_users;
``` 
"36"

Le nouveau utilisateur est "PDBADMIN"

## 1.5
```sql 
ALTER USER pdbadmin IDENTIFIED BY sheep;
```

dunno what to do

## 1.6
Activate listener:

	In oracle bash shell (#tmux second pane): `$ORACLE_HOME/bin/lsnrctl`

	In LSNRCTL shell: `START`

Close and re-open sqlplus: sqlplus / as sysdba

Set sys password: 
```sql 
ALTER USER sys IDENTIFIED BY azerty;
```

```sql 
CONNECT sys/azerty@//localhost:1521/orclpdb1 AS sysdba
```

bdd courante: 
```sql
SHOW con_name;
``` 
"ORCLPDB1"

user courant: 
```sql 
show user;
``` 
"USER is 'SYS'"

Deuxième session

```bash
sqlplus pdbadmin/sheep@//localhost:1521/orclpdb1
```

bdd courante: 
```sql 
SHOW con_name;
```
"ORCLPDB1"

user courant: 
```sql 
show user;
```
"USER is 'PDBADMIN'"

# 2
# 2.7
```sql 
SELECT username, command, status FROM V$SESSION WHERE (username = 'SYS' OR username = 'PDBADMIN');
```

# 2.8
```sql 
SELECT username, command, status FROM V$SESSION WHERE (username = 'SYS' OR username = 'PDBADMIN');
```


# TP3
## 1
### 1.1
MDP sys: sheep
```
ALTER USER sys IDENTIFIED BY sheep;
```
### 1.2
```
select username,authentication_type from dba_users where username = 'SYS';

USERNAME                         AUTHENTI
-------------------------------- --------
SYS                              PASSWORD
```
Pour éteindre l'instance:
```
SHUTDOWN immediate;
Faire un rollback s'il se plaint d'une transaction en cours
```
### 1.3
```bash
rhdavies@vm-xx$ sqlplus sys/sheep AS sysdba
SHOW user;
	USER is "SYS"
STARTUP NOMOUNT;
	ORACLE instance started.

	Total System Global Area  771747944 bytes
	Fixed Size                  8900712 bytes
	Variable Size             633339904 bytes
	Database Buffers          121634816 bytes
	Redo Buffers                7872512 bytes
```
### 1.4
```
SHOW PARAMETERS remote_login_passwordfile;
	NAME                                 TYPE        VALUE
	------------------------------------ ----------- ----------
	remote_login_passwordfile            string      EXCLUSIVE

ALTER SYSTEM SET remote_login_passwordfile = none SCOPE = spfile;
Un show parameters montre que la modif n'a pas encore pris effet. -> redémarrer l'instance
SHUTDOWN IMMEDIATE;

```
### 1.5
```
STARTUP NOMOUNT;
	ORA-01031: insufficient privileges
```

### 1.6
```
oracle@vm-xx$ sqlplus sys/sheep as sysdba
STARTUP NOMOUNT;

Ca marche en passant par l'utilsateur oracle.
```

### 1.7
```
SHUTDOWN IMMEDIATE;

rhdavies@vm-xx$ groups oracle

sudo gpasswd -d oracle dba
sudo gpasswd -d oracle oper
groups oracle
	oracle : oinstall backupdba dgdba kmdba racdba
oracle@vm-xx$ sqlplus sys/sheep as sysdba
STARTUP NOMOUNT;
Ca ne marche pluuuuuuuuus! wow what a surprise....

rhdavies@vm-xx$ sudo gpasswd -a oracle dba
sudo gpasswd -a oracle oper

(oracle) SQL> STARTUP NOMOUNT;
wow ça marche!
```

