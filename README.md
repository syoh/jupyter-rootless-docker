# Python and R development in Devcontainer

The code in this repository provides a way to start Python and R development environments. The environment is based on [Jupyter container images](https://jupyter-docker-stacks.readthedocs.io/en/latest/).

## Introduction to Development Containers and Codespaces

If you are familiar with Visual Studio Code, the concept of devcontainer in VS Code, and Codespaces, skip to the next section.

If the concept of containers are new to you, I recommend reading the section on [containers in data science](https://the-turing-way.netlify.app/reproducible-research/renv/renv-containers.html) context in [The Turing Way handbook](https://the-turing-way.netlify.app/welcome.html). 

[Devcontainer in Visual Studio Code](https://code.visualstudio.com/docs/devcontainers/containers) allows VS Code to deploy and connect to a container instance and provide seamless access to software tools inside the container. [GitHub Codespaces](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers) is a way to start a web-based VS Code interface and connect to a container running on GitHub's infrastructure.

Devcontainer specification are given in file `.devcontainer/devcontainer.json`. The container images are [fully customizable](https://code.visualstudio.com/docs/devcontainers/create-dev-container#_path-to-creating-a-dev-container) using `Dockerfile` and `docker-compose.yml` that also live in `.devcontainer` directory. See the section on [What this repository provides](#what-this-repository-provides) for more details on customizing files in this repository.

## Using Python and R in container

After starting up a [Codespace](https://code.visualstudio.com/docs/remote/codespaces#_getting-started) session or [VS Code devcontainer](https://code.visualstudio.com/docs/devcontainers/tutorial#_get-the-sample) environment with this repository, open the command palette (`Ctrl+Shift+P`), and start typing `Ports: Focus on Ports View`.

In Ports View, you will see an entry that says "Jupyter (8888)". Click on the link under "Local Address" to open a browser window to login to your Jupyter server!! The security token can be obtained when you open a terminal in VS Code (`` Ctrl+` ``). The password can be changed by using the interface at the bottom of the login screen.

## What this repository provides

Following files in this repository are the essential pieces for running RStudio in development containers.

* [`.devcontainer/devcontainer.json`](.devcontainer/devcontainer.json)
* [`.devcontainer/Dockerfile`](.devcontainer/Dockerfile)
* [`.devcontainer/docker-compose.yml`](.devcontainer/docker-compose.yml)

### Devcontainer configuration: `devcontainer.json`

[`devcontainer.json`](.devcontainer/devcontainer.json) is the main configuration file for specifying a devcontainer. ([documentation on `devcontainer.json`](https://containers.dev/implementors/json_reference/)).

### Development environment: `Dockerfile`

[`Dockerfile`](.devcontainer/Dockerfile) specifies the content of the container image. Modifying this file will customize the contents of your devcontainer: e.g. system libraries, Python and R packages, and RStudio.

For example, adding the folliwng line installs an R package, `devtools`:
```Dockerfile
RUN R -q -e 'install.packages("devtools", repos="cloud.r-project.org")'
```
Adding the following line installs a Python package using `pip`:
```Dockerfile
RUN pip install pyjoke
```
Adding the following lines between `USER root` and `USER ${NB_USER}` lines will install a system package for Ubuntu (underlying OS for the base image being used).
```Dockerfile
RUN apt update && \
    apt install -y --no-install-recommends curl && \
    rm -rf /var/lib/apt/lists/*
```
Reference: [installing system packages](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#run) in `Dockerfile`.

### Container launch configuration: `docker-compose.yml`

[`docker-compose.yml`](.devcontainer/docker-compose.yml) specifies container initialization and sets environment variables. See Jupyter documentation on [docker options](https://jupyter-docker-stacks.readthedocs.io/en/latest/using/common.html#docker-options) that can be set. Note that Jupyter lab is set to restart: i.e. `RESTARTABLE: 'yes'`.