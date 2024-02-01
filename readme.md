
# Github Actions Runner - Self Hosted Docker Container

This folder contains a docker file and start shell script to create a docker container for a self-hosted GitHub Actions Runner which can be replicated using Docker Compose, Automated through GitHub's Hosted Actions, and deployed to DockerHub's image ( see example [here](https://hub.docker.com/r/initcyber/github-actions/tags) ).

 

<div align='center'>

<a href="https://semgrep.dev/"> <img src="https://img.shields.io/badge/Trivy_status-Monitored-aqua" alt="trivytatus"></a>
![GitHub Repo stars](https://img.shields.io/github/stars/initcyber/gha-container)
![GitHub forks](https://img.shields.io/github/forks/initcyber/gha-container)
![GitHub PR's](https://img.shields.io/github/issues-pr/initcyber/gha-container)
![GitHub issues](https://img.shields.io/github/issues/initcyber/gha-container)
![GitHub last commit](https://img.shields.io/github/last-commit/initcyber/gha-container)
![GitHub contributors](https://img.shields.io/github/contributors/initcyber/gha-container)
![Docker Pulls](https://img.shields.io/docker/pulls/initcyber/github-actions)
<a href="https://hub.docker.com/u/initcyber" target="blank"><img src="https://img.shields.io/badge/dockerhub-images-success.svg?logo=Docker" alt="DockerHub">


</div>

 
 
 
 
 

## 🚀Assumptions 🚀

- You have a GitHub Personal Access Token ready - Needs Repo Read/Write (Full Control) and Read:Org. Keep this a secret folks.

- You have a Docker Hub or Nexus Repository to push this image to

- You have Docker and Docker Compose Installed

 

## 🏗️ Build This Manually 🏗️

### Change some settings
Copy/clone this repository. Change the following Personal Information to your liking:

 - Docker File:
	 - ARG RUNNER_VERSION=2.3xx, replace this to whatever the newest version is, whatever version you want to pin to, or use "latest" if you dare.


Build the container using:

```bash
docker build --tag {{OwnerName/RepoName:Version}} . 
```
Don't forget the "." at the end, and change everything in {{Name/Repo:Version}}, including the braces, to your Container Repository Owner Name, Container Repository Name, and Version Number you want to use. 
Ex: If using Docker Hub, I would use " initcyber/github-actions:latest " . You could also replace "latest" with a version number.
 

Then the newly created image will be available in your local image repository (on your computer).

 

To test your image:

 

```bash
docker run -e GH_TOKEN={{PATTOKEN}} -e GH_OWNER={{ORGNAME OR YOURGITHUBNAME}} -e GH_REPOSITORY={{REPOSITORY NAME}} -d {{THE-IMAGE-NAME-THAT-YOU-MADE-EARLIER}}
```
Where:

 - {{PATTOKEN}} - This is your GitHub **P**ersonal **A**ccess **T**oken string. As noted above, Needs Repo Read/Write (Full Control) and Read:Org. And keep this a secret.
 - {{ORGNAME OR YOURGITHUBNAME}} - This is your GitHub Organization Name or your Personal Github Name (Mine's initcyber)
 - {{REPOSITORY NAME}} - This is the Repository in which you want this (these if multiple later on) runners to be installed on. If you made a new repository named "Chess_Game" you would replace this with "Chess_Game"

**Remember to take off all of the brackets {{ }}** or else you'll have errors when running from CLI.

Now that it's tested and assuming it works, tear it down (and unregister from GitHub):

Find your running Docker Container:
```bash
docker ps
```
Then:
```bash
docker stop {{container_id}}
```
Finally:
```bash
docker container rm {{container_id}}
```

If you don't have any other running containers on your machine ~~or you just want to watch the world burn~~ and know for fact that you won't affect anything else in production:
```bash
docker stop $(docker ps -aq) && docker rm $(docker ps -aq)
```

 #### Optional

Push this image to the container repository of your choice. I have a public copy at Docker Hub [Here](https://hub.docker.com/r/initcyber/github-actions) but I use Nexus primarily.

 

```bash
docker push initcyber/github-actions:tag
```

## 🤖 Automated Build ("the sweet stuff") 🤖
This uses GitHub's Hosted Actions to build the Docker Image and deploy it to Docker Hub... But wait, I want everything Self Hosted? You can do so if you really want to (automate the automation, to build the automation...? This is like Docker in Docker?)

 - .github/workflow/docker-image.yml:
	 - ${{ secrets.DOCKER_USERNAME }} - This is your DockerHub username, save this to GitHub Secrets
	 - ${{ secrets.DOCKER_PASSWORD }} - This is your DockerHub Password OR token, save this to GitHub Secrets
	 - tags: ${{ secrets.DOCKER_USERNAME }}/github-actions:2.3xx: I just mimic the current [GitHub Self Hosted Runners](https://github.com/actions/runner/releases/download/) version, Will need to find an automated way to update this if @renovate doesn't pick up on this. 🤞

 




 

## 🖊️Docker Compose File Template🖊️

See attached [docker-compose.yml](https://github.com/initcyber/gha-container/blob/main/docker-compose.yml) file template which you can use assuming you have uploaded the previously made Docker Container image to your own Docker Hub registry. You can use mine too if you want, No guarantees on it (Use at your own risk).

Within this file change the following variables to your liking:
##### The Image Repository to pull from
```
image: initcyber/github-actions:latest 
```
Match this to your own Docker Hub repository if you want, or to your personal Nexus repository, etc.

##### The Environment Variables:
```
environment:

- GH_OWNER=$OWNER #Replace with your information here

- GH_REPOSITORY=$REPO #Replace with your information here

- GH_TOKEN=$TOKEN #Replace with your GH PAT Token here
```
Should be self explanatory as this point

##### How many Replicas you want/need in your environment - THIS IS PER REPOSITORY:
```
replicas: 3 #Change to the number of containers you want running
```
Finally:
##### The number of Resources you want per container (refer to your environment):
```
resources:

limits:

cpus: "0.75" # These

memory: 2048M # Are

reservations:

cpus: "0.5" # All

memory: 128M # Changeable
```
Just like a Virtual Machine, you can over-provision the CPU's, don't over-provision the memory or you will get "Out of Memory" issues.

## 👮 Security Scanning (this particular repository) 👮
I use [Trivy GitHub Actions](https://github.com/aquasecurity/trivy-action) to build and scan the container periodically and upload any known vulnerabilitys. I'll publish anything I find [here](https://github.com/initcyber/gha-container/security) if it can't be remediated immediately (i.e. if it's a critical finding, and actively exploited. I will more than likely also be removing the runners immediately following that nifty docker command above). Realistically, it will be reliant upon the upstream found in [GitHub's Action Runner](https://github.com/actions/runner/) to make a majority of the fixes however.