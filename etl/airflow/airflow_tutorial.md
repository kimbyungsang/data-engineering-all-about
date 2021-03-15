# Airflow Tutorial


## Running Airflow using Docker
There is full version of the airflow docker - "docker-compose.yaml" which is composed of airflow-websever, airflow-scheduler, postgres for database, redis for message queue, worker for celery, flower for monitoring worker. Official source is : https://airflow.apache.org/docs/apache-airflow/stable/start/docker.html.
There are two simple versions to exercising the functionalities and tutorial
1. Internal database (sqlite) with SequentialExecutor : docker-compose-local.yaml
2. External database (postgres) with LocalExecutor: docker-compose-local-sqlite.yaml

### Parallelism in Airflow Executor
1. SequentialExecutor: Only one task at a time 
2. LocalExecutor: 
