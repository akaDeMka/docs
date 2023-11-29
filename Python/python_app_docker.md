# Python app docker

## Requirments

1. Gitlab
2. Gitlab runner in docker mode
3. Generated ssh for remote host access placed in variables for project (DOCKER_KEY in this config)
4. Database and database_user must be create manually on first deploy on a new host.

### Security Notions

- Database connection setting in git repository (dev.py)
- MySQL in container without password root access
- Access to application from any IP on host
- This deploy is not for production as the application use internal django develop web server

## Configuration

1. DockerFile in the root of project:

    ```bash
    FROM python:3.9
    ENV PYTHONUNBUFFERED=1
    WORKDIR /code

    COPY requirements.txt /code/
    RUN pip3 install -r requirements.txt
    COPY . /code/
    ```

1. docker-compose.yml in the root of project

    ```bash
    version: "3.7"

    services:
      db:
        hostname: <app>-db
        image: mariadb:latest
        volumes:
          - /var/lib/<app>:/var/lib/mysql
        environment:
          - MYSQL_ALLOW_EMPTY_PASSWORD=yes
          - MYSQL_DATABASE=<app>
          - MYSQL_USER=<user>
          - MYSQL_PASSWORD=<user>

      app:
        hostname: <app>
        image: ${CI_REGISTRY_IMAGE}/${CI_BUILD_REF_SLUG}
        build: .
        command:
          - /bin/bash
          - -c
          - |
            chmod 777 wait-for-it.sh
            ./wait-for-it.sh db:3306 -t 2 -- python3 manage.py makemigrations
            python3 manage.py migrate
            python3 manage.py runserver 0.0.0.0:8000
        ports:
          - "8000:8000"
        depends_on:
          - db
        links:
          - db
    ```

1. wait-for-it.sh can be found here <https://github.com/vishnubob/wait-for-it> place in the root of project. It is used for application to wait database starts and ready for work.

    How to run:
    `docker-compose -f docker-compose.dev.yml up -d`
1. .gitlab-ci.yml:

    ```bash
    $MINSK_TERA01 variable is ssh key to remote host already generated manually.
    .gitlab-ci.yml
    image: docker:latest
    variables:
      DOCKER_DRIVER: overlay2
      MINSK_TERA01_USER: "gitlab"
      MINSK_TERA01: "minsk-tera01.touchsoft.by"

    stages:
      - build-docker
      - deploy-minsk-tera01

    before_script:
      - docker login -u $CI_REGISTRY_USER -p $CI_BUILD_TOKEN $CI_REGISTRY_IMAGE

    build_docker:
      stage: build-docker
      only:
        variables:
          - "$BUILD_DOCKER"
      script:
        - docker build -t $CI_REGISTRY_IMAGE/$CI_BUILD_REF_SLUG .
        - docker push $CI_REGISTRY_IMAGE/$CI_BUILD_REF_SLUG

    deploy_minsk_tera01:
      stage: deploy-minsk-tera01
      only:
        variables:
          - "$DEPLOY_MINSK_TERA01"
      script:
        - apk add docker-compose openssh
        - eval $(ssh-agent -s)
        - echo "DOCKER_KEY" | tr -d '\r' | ssh-add - > /dev/null
        - docker-compose -H "ssh://$MINSK_TERA01_USER@$MINSK_TERA01" down --remove-orphans
        - docker-compose -H "ssh://$MINSK_TERA01_USER@$MINSK_TERA01" pull
        - docker-compose -H "ssh://$MINSK_TERA01_USER@$MINSK_TERA01" up -d
    database settings configured in settings.py 
    ```

1. application settings for this application in dev.py

    ```bash
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql',
            'NAME': 'databse_name',
            'USER': 'databse_user',
            'PASSWORD': 'databse_password',
            'HOST': 'db',
            'PORT': '3306',
        }
    }
    ```

## Possible issues:

```bash
docker.errors.DockerException: Error while fetching server API version: ('Connection aborted.', RemoteDisconnected('Remote end closed connection without response'))
```

Solution: </br>
You have to add ssh user on target host to docker group to allow him connect to docker API.
