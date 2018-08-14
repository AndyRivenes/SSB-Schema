# DBIM
Database In-Memory SSB Schema and Example Scripts

A small data pump export file for the SSB schema that can be used on a laptop

Miscellaneous SQL scripts similar to the DBIM Hands On Lab scripts

The schema has been export using Data Pump.
The schema is roughly 1344MB in size when imported.

SQL> select segment_name, segment_type, bytes/1024/1024 from user_segments;

SEGMENT_NAME         SEGMENT_TYPE       BYTES/1024/1024
-------------------- ------------------ ---------------
CUSTOMER             TABLE                           64
DATE_DIM             TABLE                           64
LINEORDER            TABLE                          832
PART                 TABLE                           64
STEP3_3              INDEX                          256
SUPPLIER             TABLE                           64

6 rows selected.

SQL>

The dump files can be uncompressed with the following:

for file in `ls ssbdp_??.dmp.gz`
do
 gunzip $file
done

Create a database with an SSB schema and a tablespace allocated:

create tablespace ts_data datafile 
size 100m autoextend on next 100m maxsize 1500m;

create user ssb identified by ssb
default tablespace ts_data
quota unlimited on ts_data;

grant connect, dba to ssb;
grant alter session to ssb;
grant select any table to ssb;
grant select any dictionary to ssb;

create directory dpdir as ‘/tmp’;

The import can be accomplished with:

impdp parfile=impdp.par

respond with "/ as sysdba" or appropriate username/password for a privileged account.

init.ora Parameters:

inmemory_expressions_usage	     string      ENABLE
inmemory_size			                big integer 1100M
inmemory_virtual_columns	       string      ENABLE

sga_target			                   big integer 2400M


