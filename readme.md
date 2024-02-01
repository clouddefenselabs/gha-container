# Github Actions Runner - Docker Container
This folder contains a docker file and start shell script to create a docker container for a self-hosted GitHub Actions Runner.

<div align='center'>

![GitHub Repo stars](https://img.shields.io/github/stars/initcyber/gha-container)
![GitHub forks](https://img.shields.io/github/forks/initcyber/gha-container)
![GitHub PR's](https://img.shields.io/github/issues-pr/initcyber/gha-container)
![GitHub issues](https://img.shields.io/github/issues/initcyber/gha-container)
![GitHub last commit](https://img.shields.io/github/last-commit/initcyber/gha-container)
![GitHub contributors](https://img.shields.io/github/contributors/initcyber/gha-container)

</div>





# Assumptions:
- You have a GitHub Personal Access Token ready - Needs Repo Read/Write (Full Control) and Read:Org. Keep this a secret folks.
- You have a Docker Hub or Nexus Repository to push this image to
- You have Docker and Docker Compose Installed

### Building this 

Copy/clone this repository. Go through the code and change personal information.

Within the DockerFile - for ARG RUNNER_VERSION=xxxxx, replace the xxxx to whatever the newest version is, whatever version you want to pin to, or use "latest" if you dare.

Build the container using:

```bash
docker build --tag {{Name/Repo:Version typically if using Docker Hub}} (in my case initcyber/github-actions:(VersionOfRunner)) .
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
Push this image to the container repository of your choice. I have a public copy at Docker Hub [Here](https://hub.docker.com/r/initcyber/github-actions) but I use Nexus primarily. 

```bash
docker push initcyber/github-actions:tag
```

## Docker Compose File Template
See attached Docker Compose file template which you can use assuming you have uploaded it to your own Docker Hub registry. You can use mine too if you want, No guarentees on it.