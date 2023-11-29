# Bitbucket runner

## Some info about Bitbucket runners

Command to start runner shows in Bitbucket during installation. But you have to add parameter --restart unless-stopped to auto startup docker runner container after server restart or etc.
To update Bitbucket runner:

```bash
#stop container
docker stop <container id>
#remove container
docker rm <container id>
docker system prune
docker rmi <image id>
#start new container with latest image 
docker container run -it -v /tmp:/tmp -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers:ro \
-e ACCOUNT_UUID=<ACCOUNT_UUID> -e RUNNER_UUID=<RUNNER_UUID> -e RUNTIME_PREREQUISITES_ENABLED=true \
-e OAUTH_CLIENT_ID=<OAUTH_CLIENT_ID> -e OAUTH_CLIENT_SECRET=<OAUTH_CLIENT_SECRET> \
-e WORKING_DIRECTORY=/tmp --name <runner name> --restart unless-stopped \
docker-public.packages.atlassian.com/sox/atlassian/bitbucket-pipelines-runner:1
```
