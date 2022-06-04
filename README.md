# mlflow-docker-compose
Deploy mlflow with docker-compose

# Deploy
## 1. Create resources (a bucket and an IAM user) in account a and grant permissions

See https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-walkthroughs-managing-access-example1.html .



## 2. Create .env file
In `docker-composa.yaml`, some parameters is loaded from `.env` file.
Set following parameters in `.env`.

- HOST: host name(If you don't use domain, any name is accepted. If use, speciy it)
- POSTGRES_USER: postgresql db user
- POSTGRES_PASSWORD: postgresql db user password
- DEFAULT_ARTIFACT_ROOT: s3://{YOUR_BUCKT_NAME}/artifact_location
- AWS_ACCESS_KEY_ID
- AWS_SECRET_ACCESS_KEY
- AWS_DEFAULT_REGION

```
HOST=mlflow.dev
POSTGRES_USER=demo-user
POSTGRES_PASSWORD=demo-password
DEFAULT_ARTIFACT_ROOT= s3://{YOUR_BUCKT_NAME}/artifact_location
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=
```

## 3. Set up NGINX Basic Authentication
Because mlflow doesn't provide authentication, use NGINX proxy for basic authentication system.

```sh
$ sudo echo "{USER_NAME}:$(openssl passwd -apr1 {PASSWORD})" >> ${HOST}
```

`${HOST}` is host name you set in chapter 2.

## 4. Build and deploy
Build mlflow Dockerfilw, and then deploy applications.

```sh
$ sudo docker-compose build
$ sudo docker-compose up -d
```

# Client
To use Basic authentication, mlflow use following parameters passing HTTP authentication.
Set following environment parameters in local,  same as [3. Set up NGINX Basic Authentication](#3-Set-up-NGINX-Basic-Authentication)

- MLFLOW_TRACKING_USERNAME
- MLFLOW_TRACKING_PASSWORD

See also https://www.mlflow.org/docs/latest/tracking.html#logging-to-a-tracking-server

# Update MLflow version
If you want update MLflow, stop container and remove images, and then rebuild MLflow container.

```sh
$ sudo docker-compose stop mlflow && \
  sudo docker-compose rm mlflow && \
  docker images mlflow-docker-compose_mlflow --format '{{.ID}}'|xargs docker rmi && \
  sudo docker-compose build && \
  sudo docker-compose up -d
```

# On-Premises version

In case you'd like to run MLflow on your on-premises server, `on_premises` folder is for you.
Batteries (sample setting files) included.
