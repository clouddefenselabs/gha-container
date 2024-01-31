# Github Actions Runner - Docker Container
This folder contains a docker file and start shell script to create a docker container for a self-hosted GitHub Actions Runner.


# Assumptions:
- You have a GitHub Personal Access Token ready - Needs Repo Read/Write (Full Control) and Read:Org. Keep this a secret folks.
- You have a Docker Hub or Nexus Repository to push this image to
- You have Docker and Docker Compose Installed

### Building this 

Copy/clone this repository. Go through the code and change personal information.

Build the container using:

```bash
docker build --build-arg RUNNER_VERSION={{LATESTVERSIONOFRUNNER}} --tag {{WHATEVERYOUWANTTOTAGorNAMEIT}} (in my case initcyber/github-actions:latest)
```

Then the newly created image will be available in your local image repository.

To test your image:

```bash
docker run -e GH_TOKEN={{PATTOKEN}} -e GH_OWNER={{ORGNAME OR YOURGITHUBNAME}} -e GH_REPOSITORY={{REPOSITORY NAME}} -d {{THE-IMAGE-NAME-THAT-YOU-MADE-EARLIER}}
```

To tear it down (This will also unregister the Docker Container from Github):

```bash
docker stop $(docker ps -aq) && docker rm $(docker ps -aq)
```

#### Optional
Push this image to the container repository of your choice. I have a public copy at Docker Hub (Here)[https://hub.docker.com/r/initcyber/github-actions] but I use Nexus primarily. 

```bash
docker push devop.home.initcyber.com:9001/docker/github-actions
```

## Docker Compose File Template
See attached Docker Compose file template which you can use assuming you have uploaded it to your own Docker Hub registry.