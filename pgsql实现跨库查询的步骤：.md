pgsql实现跨库查询的步骤：

PS：在当前模式中（foreign_dcs_dcsdb）按步骤运行如下的命令。

1. 在 PostgreSQL 数据库中安装支持 FDW 的扩展。如果尚未安装 postgres_fdw 扩展，可以使用以下语句进行安装：

```sql
CREATE EXTENSION postgres_fdw;
```

2. 创建一个外部服务器，表示连接到远程数据库。可以使用以下语句创建外部服务器：

```sql
CREATE SERVER dcs_dcsdb_server
    FOREIGN DATA WRAPPER postgres_fdw 
    OPTIONS (dbname 'dcs_dcsdb', host '10.1.1.28', port '7092');
```

其中，`dbname`、`host` 和 `port` 分别指定要连接的远程数据库名称、地址和端口号。

3. 创建一个用户映射，表示将本地数据库用户映射到远程数据库用户，并定义连接权限等。可以使用以下语句创建用户映射：

```sql
CREATE USER MAPPING FOR postgres
    SERVER dcs_dcsdb_server
    OPTIONS (user 'postgres', password 'RISREXxj');
```

其中，`postgres` 表示当前数据库的用户，`postgres` 和 `RISREXxj` 分别表示远程数据库的用户名和密码。

4. 创建外部表，表示连接到远程数据库中的 `tb_person` 表。可以使用以下语句创建外部表：

   ==PS：如果始终要在改库创建外部表，则把外部表放在这段代码里面即可。==

```sql
CREATE FOREIGN TABLE tb_person
    (
        id                     varchar(48)                        not null,
        person_id              varchar(48),
        person_name            varchar(64),
        pinyin                 varchar(256),
        gender                 integer,
        age                    integer,
        birthday               varchar(20),
        phone_no               varchar(64),
        address                varchar(500),
        email                  varchar(64),
        job_no                 varchar(48),
        student_id             varchar(32),
        certificate_type       integer,
        certificate_no         varchar(48),
        room_num               varchar(128),
        org_path               varchar(1024),
        org_path_name          varchar(1024),
        org_index_code         varchar(64),
        stu_end_time           timestamp with time zone,
        person_photo           text,
        extended_attribute     jsonb,
        dormitory_apply_his_id varchar(64),
        live_status            integer                  default 0,
        status                 integer                  default 0,
        create_time            timestamp with time zone default CURRENT_TIMESTAMP(3),
        update_time            timestamp with time zone default CURRENT_TIMESTAMP(3),
        account_status         integer                  default 0,
        face_num               integer,
        special_live           integer                  default 0 not null,
        other_remark           varchar(1024),
        entry_time             timestamp,
        unique_id              varchar,
        post_type              varchar
        )
    SERVER dcs_dcsdb_server
    OPTIONS (schema_name 'public', table_name 'tb_person');
```

其中，`schema_name` 和 `table_name` 分别指定远程数据库中要查询的表所在的模式和表名。

5. 进行跨库查询，查询 `tb_person` 表中的信息。可以使用以下语句进行跨库查询：

```sql
SELECT *
FROM foreign_dcs_dcsdb.tb_person;
```

这样就可以实现跨库查询 `db2` 数据库中的 `tb_person` 表了。需要注意的是，使用 FDW 可能会产生网络延迟，也可能导致性能瓶颈。因此，在使用 FDW 时需要慎重考虑，并根据实际情况进行优化和调整。





















