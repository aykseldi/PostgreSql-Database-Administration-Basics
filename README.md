# POSTGRESQL DATABASE  ADMINISTRATION BASICS

We will do our practice on Vagrant. Vagrant is:

    #Vagrant is a tool for building and managing virtual machine environments in a single workflow. #With an easy-to-use workflow and focus on automation, Vagrant lowers development environment setup time, increases production parity, and makes the "works on my machine" excuse a relic of the past.

It is compatible with VirtualBox.
Installing / Getting started

    Go to (https://www.postgresql.org/download/linux/redhat/) and select to rpm section

    Give the command at the below.

yum install https://download.postgresql.org/pub/repos/yum/11/redhat/rhel-7-x86_64/pgdg-centos11-11-2.noarch.rpm
Initial Configuration

    After download is finished.

yum install postgresql

yum install postgresql11-server

/ usr / pgsql-11 / bin / postgresql-11-setup initdb
systemctl enable postgresql-11
systemctl start postgresql-11

    Searching data directiory of postgresql. /var/lib/pgsql/11/data

cd / var / lib / pgsql / 11 / data
-rw -------. 1 postgres postgres 88 Feb 9 15:09 postgresql.auto.conf
-rw -------. 1 postgres postgres 23788 Feb 9 15:09 postgresql.conf
-rw -------. 1 postgres postgres 4269 Feb 9 15:09 pg_hba.conf

Diving into PostgreSql Basic Artictecture

    Login to database with psql command.

"?" Command enables database objects to be listed with.

    Data base files, where data is

/ Var / lib / pgsql / 11 / data / base

    Change password on VT

postgres = # \ password
Enter new password:
Enter it again:

    Database Firewall SETUP also connection setup type

 vi pg_hba.conf

**Content of pg_hba.conf file should be changed like below. **

# TYPE DATABASE USER ADDRESS METHOD

# "local" is for Unix domain socket connections only
local all all md5
# IPv4 local connections:
host all all 127.0.0.1/32 md5
# IPv6 local connections:
host all all :: 1/128 md5
# Allow replication connections from localhost, by a user with the
# replication privilege.
local replication all md5
host replication all 127.0.0.1/32 ident
host replication all :: 1/128 ident

    Change the IP address that database will listen to

 vi postgresql.conf

We have made listen_addresses * below.

# - Connection Settings -

listen_addresses = '*' # what IP address (es) to listen on;
                                        # comma-separated list of addresses;
                                        # defaults to 'localhost'; use '*' for all

    After the above operation, the restart database with the sudo priviledge user.

systemctl restart postgresql-11

    See the user's password from VT

postgres = # select * from pg_shadow;
 usename | usesysid | usecreatedb | usesuper | userepl | usebypassrls |
       passwd | valuntil | useconfig
+ ------------- + ---------- + ---------- + ---------- --- ------ + -------------- + --------
----------------------------- --------- + ---------- + -
 postgres | 10 | t | t | t | t | md53175
bce1d3201d16594cebf9d7eb3f9d | |
(1 row)

    The listener can be reloaded with the following on database.

select pg_reload_conf ();

Some PostgreSql Commands

    \ dn command lists the schemes

postgres = # create schema experiment;
CREATE SCHEMA
postgres = # \ dn
  List of schemas
  Name | Owner
-------- + ----------
 trial | postgreS
 public | postgreS
(2 rows)

postgres = # set search_path to try;

The definition of defaul search_pat is in postgresql.conf file.

grep search_path postgresql.conf

If we do not define SEARCH PATH, the related table function cannot be indexed.

postgres = # show search_path;
 search_path
-------------
 trial
(1 row)

show all;
postgres = # show all;

 allow_system_table_mods | off
 | Allows modifications of the structure of system tables.
 application_name | pSQL
 | Sets the application name to be reported in statistics and logs.
 archive_command | (Disabled)
 | Sets the shell command that will be called to archive a WAL file.
 archive_mode | off
 | Allows archiving of WAL files using archive_command.
 

 autovacuum_multixact_freeze_max_age | 400000000
 | Multixact age at which to autovacuum a table to prevent multixact wraparound.
 autovacuum_naptime | 1m
 | Time to sleep between autovacuum runs.

    Command showing database status at that time

-bash-4.2 $ / usr / pgsql-11 / bin / pg_controldata

pg_control version number: 1100
Catalog version number: 201809051
Database system identifier: 6655971624051644294
Database cluster state: in production
pg_control last modified: Sun 10 Feb 2019 10:41:37 AM +03
Latest checkpoint location: 0 / 16DA2E0
Latest checkpoint's REDO location: 0 / 16DA2A8
Latest checkpoint's REDO WAL file: 000000010000000000000001
Latest checkpoint's TimeLineID: 1
Latest checkpoint's PrevTimeLineID: 1
Latest checkpoint's full_page_writes: on
Latest checkpoint's NextXID: 0: 573
Latest checkpoint's NextOID: 24580
Latest checkpoint's NextMultiXactId: 1
Latest checkpoint's NextMultiOffset: 0
Latest checkpoint's oldestXID: 561
Latest checkpoint's oldestXID's DB: 1
Latest checkpoint's oldestActiveXID: 573
Latest checkpoint's oldestMultiXid: 1
Latest checkpoint's oldestMulti's DB: 1
Latest checkpoint's oldestCommitTsXid: 0
Latest checkpoint's newestCommitTsXid: 0
Time of latest checkpoint: Sun 10 Feb 2019 10:41:36 AM +03
Fake LSN counter for unlogged rels: 0/1
Minimum recovery ending location: 0/0
Min recovery ending loc's timeline: 0
Backup start location: 0/0
Backup end location: 0/0
End-of-backup record required: no
wal_level setting: replica
wal_log_hints setting: off
max_connections setting: 100
max_worker_processes setting: 8
max_prepared_xacts setting: 0
max_locks_per_xact setting: 64
track_commit_timestamp setting: off
Maximum data alignment: 8
Database block size: 8192
Blocks per segment of large relation: 131072
WAL block size: 8192
Bytes per WAL segment: 16777216
Maximum length of identifiers: 64
Maximum columns in an index: 32
Maximum size of a TOAST chunk: 1996
Size of a large-object chunk: 2048
Date / time type storage: 64-bit integers
Float4 argument passing: by value
Float8 argument passing: by value
Data page checksum version: 0
Mock authentication nonce: c6b52e05ac991181fea4478d68ffb3d1cb2393903dcb3143d3d4391965f75d6a

Database Role, User and Version

    Creating a role on database

postgres = # create role ali;
CREATE ROLE
postgres = # alter role ali with login;
ALTER ROLE

Roles and Users are basically the same thing. It is not authorized to login with the role. But the user has login authority by default.

    Extensions on database

postgres = # \ dx
                 List of installed extensions
  Name | Version | Schema | Description
----------------- --------- + --------- + ------------ + -------------
 plpgsql | 1.0 | pg_catalog | PL / pgSQL procedural language

[root @ localhost ~] # cd / usr / pgsql-11 / share / extension /
[root @ localhost extension] # ll
total 12
-rw-r - r--. 1 root root 332 Nov 7 14:36 ​​plpgsql - 1.0.sql
-rw-r - r--. 1 root root 179 Nov 7 14:36 ​​plpgsql.control
-rw-r - r--. 1 root root 381 Nov 7 14:36 ​​plpgsql - unpackaged - 1.0.sql

WAL (Write Ahead Log) Files

    Location of wal files

cd / var / lib / pgsql / 11 / data / pg_wal

-bash-4.2 $ ll
total 16 384
-rw -------. 1 postgres postgres 16777216 Feb 10 11:31 000000010000000000000001
------ drwxr. 2 postgres postgres 6 Feb 9 15:09

    Here, we quickly generated data below and these were written to the transaction log file, and our transaction log file, which is one above, increased to 4 after the data.

postgres = # insert into number select generate_series (1,1000000);
INSERT 0 1000000
postgres = # \ q
-bash-4.2 $ ls -lart insert into number select generate_series (1,1000000);
total 81920
-rw -------. 1 postgres postgres 16777216 Feb 10 11:39 000000010000000000000001
-rw -------. 1 postgres postgres 16777216 Feb 10 11:39 000000010000000000000002
-rw -------. 1 postgres postgres 16777216 Feb 10 11:39 000000010000000000000003
-rw -------. 1 postgres postgres 16777216 Feb 10 11:39 000000010000000000000004
-rw -------. 1 postgres postgres 16777216 Feb 10 11:39 000000010000000000000005
------ drwxr. 2 postgres postgres 6 Feb 9 15:09 archive_status

    Help command

\h -> means help

Shows all commands related to. \D -> Means Describe
Some Sql Tuning

    SQL Tuning, looking at the plans of their queries

There are sites on Google that will post it visually by saying Postgre explain plan

postgres = # explain select count (*) from number;
                                       QUERY PLAN

-------------------------------------------------- ------------------------------
---------
 Finalize Aggregate (cost = 58799.21..58799.22 rows = 1 width = 8)
   -> Gather (cost = 58799.00..58799.21 rows = 2 width = 8)
         Workers Planned: 2
         -> Partial Aggregate (cost = 57799.00..57799.01 rows = 1 width = 8)
               -> Parallel Seq Scan on number (cost = 0.00..51549.00 rows = 2500000
width = 0)
(5 rows)

postgres = # explain (analyze) select count (*) from number;
                                                                QUERY PLAN

-------------------------------------------------- ------------------------------
-----------------------------------------------------------
  Finalize Aggregate (cost = 58799.21..58799.22 rows = 1 width = 8) (actual time = 1759.
312..1759.313 rows = 1 loops = 1)
    -> Gather (cost = 58799.00..58799.21 rows = 2 width = 8) (actual time = 1756.744..1
759.431 rows = 3 loops = 1)
          Workers Planned: 2
          Workers Launched: 2
          -> Partial Aggregate (cost = 57799.00..57799.01 rows = 1 width = 8) (actual
  time = 1734.485..1734.486 rows = 1 loops = 3)
                -> Parallel Seq Scan on number (cost = 0.00..51549.00 rows = 2500000
width = 0) (actual time = 0.040..931.428 rows = 2000000 loops = 3)
  Planning Time: 0.110 ms
  Execution Time: 1759.482 ms
(8 rows)

    You can use version control system for database operations.

Database Version Control

    Used for external data loading. It can be from sources like Csv. below command is useful for that operations.

\ copy
References

Vagrant Auxiliary information for postgresql
Links

    Project homepage: https://github.com/aykseldi/PostgreSql-Notes/
    Repository: https://github.com/aykseldi/PostgreSql-Notes
    Issue tracker: https://github.com/aykseldi/PostgreSql-Notes/issues
        In case of sensitive bugs like security vulnerabilities, please contact aykseldi1@yahoo.com directly instead of using issue tracker. We value your effort to improve the security and privacy of this project!
    Related projects:
        Your other project: https://github.com/aykseldi/PostgreSql-Notes/blob/master/2.%20Replication

Licensing

"The code in this project is licensed under MIT license."
