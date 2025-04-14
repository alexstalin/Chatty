#FROM python:3.11-slim
#
#ENV PYTHONUNBUFFERED=1
#
#WORKDIR /app
#COPY . /app
#
#RUN apt-get update && apt-get install -y netcat-openbsd && apt-get clean
#
#RUN pip install --no-cache-dir --upgrade pip \
#    && pip install --no-cache-dir -r requirements.txt
#
##RUN chmod +x docker-entrypoint.sh
#
#EXPOSE 8005
#
##ENTRYPOINT ["./docker-entrypoint.sh"]
#
#CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8005"]


FROM python:3.11-slim

ENV PYTHONUNBUFFERED=1

WORKDIR /app

COPY . /app

RUN apt-get update && apt-get install -y \
    netcat-openbsd \
    build-essential \
    gcc \
    libffi-dev \
    libssl-dev \
    python3-dev \
    git \
    && apt-get clean

RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir wheel \
    && pip install --no-cache-dir -r requirements.txt

EXPOSE 8005

# Выполнение Alembic миграций ПЕРЕД запуском сервера (одной строкой)
CMD bash -c "\
    echo 'Waiting for DB at $DB_HOST:$DB_PORT...' && \
    until nc -z \$DB_HOST \$DB_PORT; do echo 'Waiting...'; sleep 1; done && \
    echo 'Waiting for RabbitMQ at $RABBITMQ_HOST:$RABBITMQ_PORT...' && \
    until nc -z \$RABBITMQ_HOST \$RABBITMQ_PORT; do echo 'Waiting...'; sleep 1; done && \
    alembic upgrade head && \
    echo 'Starting Uvicorn...' && \
    uvicorn main:app --host 0.0.0.0 --port 8005 --reload"