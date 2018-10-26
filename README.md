# db2-hadr
setup db2 for hadr between 2 sites

SITE01
#db2 "CREATE DATABASE NAMEDB AUTOMATIC STORAGE YES ON '/data-logs' DBPATH ON '/file-logs' USING CODESET UTF-8 TERRITORY US COLLATE USING SYSTEM PAGESIZE 32768"

SITE02
#db2 "CREATE DATABASE NAMEDB AUTOMATIC STORAGE YES ON '/data-logs' DBPATH ON '/file-logs/' USING CODESET UTF-8 TERRITORY US COLLATE USING SYSTEM PAGESIZE 32768"

Primary
#db2 update db cfg for NAMEDB using HADR_LOCAL_HOST SITE01
#db2 update db cfg for NAMEDB using HADR_LOCAL_SVC XXX01
#db2 update db cfg for NAMEDB using HADR_REMOTE_HOST SITE02
#db2 update db cfg for NAMEDB using HADR_REMOTE_SVC XXX02
#db2 update db cfg for NAMEDB using HADR_REMOTE_INST db1inst1
#db2 update db cfg for NAMEDB using HADR_TIMEOUT 120
#db2 update db cfg for NAMEDB using HADR_SYNCMODE NEARSYNC
#db2 update db cfg for NAMEDB using HADR_PEER_WINDOW 500
#db2 update db cfg for NAMEDB using LOGARCHMETH1 DISK:/LOGARCHIVE/
#db2 update db cfg for NAMEDB using AUTO_DEL_REC_OBJ ON
#db2 update db cfg for NAMEDB using LOGINDEXBUILD ON
#db2 deactivate db NAMEDB
#db2 backup db NAMEDB
#scp NAMEDB* db2inst1@X.X.X.X:.
#db2 activate db NAMEDB

Standby
#db2 restore db NAMEDB
#db2 update db cfg for NAMEDB using HADR_LOCAL_HOST SITE02
#db2 update db cfg for NAMEDB using HADR_LOCAL_SVC XXX02
#db2 update db cfg for NAMEDB using HADR_REMOTE_HOST SITE01
#db2 update db cfg for NAMEDB using HADR_REMOTE_SVC XXX01

#db2 start hadr on db NAMEDB as standby
#db2pd -db NAMEDB -hadr

Primary
#db2 start hadr on db NAMEDB as primary
