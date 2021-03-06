# Airflow Tutorial


## Running Airflow using Docker
There is full version of the airflow docker - "docker-compose.yaml" which is composed of airflow-websever, airflow-scheduler, postgres for database, redis for message queue, worker for celery, flower for monitoring worker. Official source is : https://airflow.apache.org/docs/apache-airflow/stable/start/docker.html.
There are two simple versions to exercising the functionalities of airflow. 

Internal database (sqlite) with SequentialExecutor:
* docker-compose-local-sqlite.yaml
* The sequential executor can execute only one task at a time.
```
$ docker-compose -f docker-compose-local-sqlite.yaml up -d
# this runs two containers.e.g., airflow-init and airflow-webserver.
# the airflow-init is the starter container 
```

External database (postgres) with LocalExecutor: 
* docker-compose-local.yaml
* The local executor can execute multiple tasks at a time as local processes.
```
$ docker-compose -f docker-compose-local.yaml up -d
# this runs three containers. airflow-init, airflow-webserver, and postgres.
# the airflow-init is the start container
# the postgres is the database container
```

### Parallelism in Airflow Executor
```
-- airflow.cfg --
# The amount of parallelism as a setting to the executor. This defines
# the max number of task instances that should run simultaneously
# on this airflow installation
parallelism = 32

# The number of task instances allowed to run concurrently by the scheduler
# in one DAG. Can be overridden by ``concurrency`` on DAG level.
dag_concurrency = 16

# The maximum number of active DAG runs per DAG
max_active_runs_per_dag = 16
```