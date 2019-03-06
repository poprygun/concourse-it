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
fly -t from-artifactory set-pipeline -p from-artifactory-pipeline -c ci/push-from-artifactory.yml -l ci/cf-creds.yml
fly -t from-artifactory unpause-pipeline -p from-artifactory-pipeline
fly -t from-artifactory trigger-job -j from-artifactory-pipeline/trigger-when-new-file-is-added-to-artifactory -w

fly -t from-artifactory e -c ci/tasks/list-contents.xml -i artifactory-resource-source=./artifactory-resource-source


fly -t from-artifactory delete-pipeline -p from-artifactory-pipeline
```

## Verify resource types

```bash
fly -t from-artifactory check-resource-type --resource-type from-artifactory-pipeline/artifactory
fly -t from-artifactory check-resource-type --resource-type from-artifactory-pipeline/deploy-app-resource
```

## Setup Artifactory

```bash
docker pull docker.bintray.io/jfrog/artifactory-oss
docker run -p 8081:8081 docker.bintray.io/jfrog/artifactory-oss
```

> use admin/password credentials to login
