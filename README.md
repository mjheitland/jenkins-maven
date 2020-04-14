# jenkins-maven
Jenkins example project for Maven: https://jenkins.io/doc/tutorials/build-a-java-app-with-maven/

## Setup Jenkins using Docker

Start Jenkins:

```bash
docker network create jenkins
docker volume create jenkins-docker-certs
docker volume create jenkins-data
docker container run --name docker --rm --detach \
  --privileged --network jenkins --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --volume "$HOME":/home docker:dind
docker container run --name jenkins --rm --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  --volume "$HOME":/home --publish 8080:8080 --publish 50000:50000 jenkinsci/blueocean
```

Copy password from
  `docker logs <jenkinsci/blueocean containerId>`
to
  http://localhost:8080/ 
Setup Jenkins Admin and
install recommended plugins
For more details: [Configure Jenkins](https://jenkins.io/doc/tutorials/build-a-java-app-with-maven/#setup-wizard)

Remoting into Jenkins container:
```bash
docker container exec -it jenkins bash
cd /var/jenkins_home/logs
```

Stop Jenkins and Docker container:
```
docker container stop jenkins docker
```

Delete Jenkins and Docker container:
```
docker rm jenkins docker
```

In Jenkins:
Create a pipeline, for Definition choose "pipeline script from SCM" and set url to "https://github.com/mjheitland/jenkins-maven.git"
