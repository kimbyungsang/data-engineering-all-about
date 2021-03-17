## Airflow Plugins Example


### Running necessary elasticsearch containers
```
$ cd etl/airflow/docker-compose-elk-pgsql.yaml
$ cat docker-compose-elk.yaml
elk:
  image: sebp/elk
  ports:
    - "5601:5601"
    - "9200:9200"
    - "5044:5044"

$ sudo sysctl -w  vm.max_map_count=262144
$ docker-compose -f docker-compose-elk.yaml up -d
```
### ElasticSearch Plugin

```
# creating necessary folders and file
$ mkdir -p plugins/elasticsearch_plugin/hooks
$ cd plugins/elasticsearch_plugin/hooks
$ touch elastic_hook.py

```
```
# in the airflow container
# install elasticsearch python library
$ pip install elasticsearch==7.6.0
```

### Postgres_to_elastic
```
# run postgres docker container
$ docker run -d \
    --name some-postgres \
    -e POSTGRES_PASSWORD=postgres \
    -e PGDATA=/var/lib/postgresql/data/pgdata \
    -p 5432:5432 \
    postgres:12
```

```
# in the postgres container
airflow@324a46965199:/opt/airflow$ psql -h 192.168.10.53 -U postgres
## password: postgres
postgres=# select version();
                                                     version                                                      
------------------------------------------------------------------------------------------------------------------
 PostgreSQL 12.6 (Debian 12.6-1.pgdg100+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 8.3.0-6) 8.3.0, 64-bit
(1 row)

# create table and insert data
postgres=# create table category_stage
postgres-# (catid smallint default 0,
postgres(# catgroup varchar(10) default 'General',
postgres(# catname varchar(10) default 'General',
postgres(# catdesc varchar(50) default 'General');
CREATE TABLE
postgres=# select * from category_stage 
postgres-# ;
 catid | catgroup | catname | catdesc 
-------+----------+---------+---------
(0 rows)

postgres=# insert into category_stage values (12, 'Concerts', 'Comedy', 'All stand-up comedy performances');
INSERT 0 1
postgres=# insert into category_stage values (13, 'Concerts', 'Other', default);
INSERT 0 1
postgres=# select * from category_stage;
 catid | catgroup | catname |             catdesc              
-------+----------+---------+----------------------------------
    12 | Concerts | Comedy  | All stand-up comedy performances
    13 | Concerts | Other   | General
(2 rows)

postgres=# inset into category_stage values (14, default, default, default);
ERROR:  syntax error at or near "inset"
LINE 1: inset into category_stage values (14, default, default, defa...
        ^
postgres=# insert into category_stage values (14, default, default, default);
INSERT 0 1
postgres=# select * from category_stage;
 catid | catgroup | catname |             catdesc              
-------+----------+---------+----------------------------------
    12 | Concerts | Comedy  | All stand-up comedy performances
    13 | Concerts | Other   | General
    14 | General  | General | General
(3 rows)

postgres=# Ctrl + D
```

```
#creating necessary folders and file
$ mkdir -p plugins/elasticsearch_plugin/operators
$ touch plugins/elasticsearch_plugin/operators/postgres_to_elastic.py
```
* [plugins/elasticsearch_plugin/operators/postgres_to_elastic.py](./plugins/elasticsearch_plugin/operators/postgres_to_elastic.py)
* [plugins/elasticsearch_plugin/hooks/elastic_hook.py](./plugins/elasticsearch_plugin/hooks/elastic_hook.py)
* [dags/elasticsearch_dag.py](./dags/elasticsearch_dag.py)
```
airflow@324a46965199:/opt/airflow$ curl -X GET "http://192.168.10.53:9200/connections/_search" -H "Content-type: application/json" -d '{"query":{"match_all":{}}}'
{"error":{"root_cause":[{"type":"index_not_found_exception","reason":"no such index [connections]","resource.type":"index_or_alias","resource.id":"connections","index_uuid":"_na_","index":"connections"}],"type":"index_not_found_exception","reason":"no such index [connections]","resource.type":"index_or_alias","resource.id":"connections","index_uuid":"_na_","index":"connections"},"status":404}
```
