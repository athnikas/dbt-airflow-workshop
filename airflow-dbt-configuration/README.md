
## Create a virtual environment

```
pip3 install virtualenv
python3 -m virtualenv airflow_env
echo 'source ~/dbt-airflow-workshop airflow_env/bin/activate' >> ~/.bashrc
source ~/.bashrc
```

## Install airflow and dbt 
```
AIRFLOW_VERSION=2.5.0
PYTHON_VERSION="$(python --version | cut -d " " -f 2 | cut -d "." -f 1-2)"
CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-no-providers-${PYTHON_VERSION}.txt"
pip install "apache-airflow==${AIRFLOW_VERSION}" --constraint "${CONSTRAINT_URL}"
pip install -r requirements.txt
```

## Deploy an airflow instance
```
mkdir airflow
echo 'export AIRFLOW_HOME=~/dbt-airflow-workshop/airflow' >> ~/.bashrc 
source ~/.bashrc
mkdir ${AIRFLOW_HOME}/dags
airflow db init
airflow users create \
      --role Admin \
      --username admin \
      --email admin \
      --firstname admin \
      --lastname admin \
      --password admin
```
## Start the scheduler

```
airflow scheduler
```

## Start the webserver
```
airflow webserver
```
