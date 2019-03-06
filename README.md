Based on [instructions](https://medium.com/concourse-ci/getting-started-with-concourse-ci-on-macos-fb3a49a8e6b4)

https://specify.io/how-tos/concourse-ci-continious-integration-and-delivery-of-microservices

https://github.com/emerald-squad/artifactory-resource


## Start Concourse

```bash
wget -nv -O docker-compose.yml https://concourse-ci.org/docker-compose.yml
docker-compose up
```

## Find out IP address of artifactory - in our case refer to host IP address

### Loging to `Concourse` container

```bash
docker exec -it 10cd7e6747c3 bash
/sbin/ip route|awk '/default/ { print $3 }'
```

Use IP address of the host shown in above script to add to `artifactory-resource`
 
## Login

```
fly login -t from-artifactory -u test -p test -c http://127.0.0.1:8080/
```

## Fly pipeline commands

```bash
fly -t from-artifactory set-pipeline -p from-artifactory-pipeline -c push-from-artifactory.yml -l cf-creds.yml
fly -t from-artifactory unpause-pipeline -p from-artifactory-pipeline
fly -t from-artifactory trigger-job -j from-artifactory-pipeline/trigger-when-new-file-is-added-to-artifactory -w


fly -t from-artifactory delete-pipeline -p from-artifactory-pipeline


fly -t from-artifactory check-resource-type --resource-type from-artifactory-pipeline/artifactory
fly -t from-artifactory check-resource-type --resource-type from-artifactory-pipeline/deploy-app-resource
```

## Setup Artifactory

```bash
docker pull docker.bintray.io/jfrog/artifactory-oss
docker run -p 8081:8081 docker.bintray.io/jfrog/artifactory-oss
```

> use admin/password credentials to login
