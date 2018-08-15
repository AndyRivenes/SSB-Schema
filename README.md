# Database In-Memory Sample SSB Schema and Example Scripts

A small data pump export file for the SSB schema that can be used on a laptop

Miscellaneous SQL scripts similar to the DBIM Hands On Lab scripts

The Star Schema Benchmark Whitepaper: https://www.cs.umb.edu/~poneil/StarSchemaB.PDF

A version of dbgen if you want to build your own: https://github.com/electrum/ssb-dbgen

A nice how to if you roll your own: https://jorgebarbablog.wordpress.com/2016/03/21/how-to-load-the-ssb-schema-into-an-oracle-database/

And of course if you want more information about this setup: https://www.andyrivenes.com/


The schema has been export using Data Pump.
The schema is roughly 1344MB in size when imported.

```
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
```

The dump files can be uncompressed with the following:

```
for file in `ls ssbdp_??.dmp.gz`
do
 gunzip $file
done
```

Create a database with an SSB schema and a tablespace allocated:

```
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
```

The import can be accomplished with:

```
impdp parfile=impdp.par

respond with "/ as sysdba" or appropriate username/password for a privileged account.
```

init.ora Parameters:

```
inmemory_expressions_usage = ENABLE
inmemory_size = 1100M
inmemory_virtual_columns = ENABLE

sga_target = 2400M

db_keep_cache_size = 800M
```
