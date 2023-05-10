pip3 install virtualenv

python3 -m virtualenv airflow_env
mkdir airflow

AIRFLOW_VERSION=2.5.0
PYTHON_VERSION="$(python --version | cut -d " " -f 2 | cut -d "." -f 1-2)"
CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-no-providers-${PYTHON_VERSION}.txt"


echo 'source ~/dbt-airflow-workshop airflow_env/bin/activate' >> ~/.bashrc 
echo 'export AIRFLOW_HOME=~/dbt-airflow-workshop/airflow' >> ~/.bashrc 

source ~/.bashrc

mkdir ${AIRFLOW_HOME}/dags



pip install -r requirements.txt


airflow db init


airflow users create \
      --role Admin \
      --username admin2 \
      --email admin2 \
      --firstname admin2 \
      --lastname admin2 \
      --password admin

airflow scheduler
airflow webserver