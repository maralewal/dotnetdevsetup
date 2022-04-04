# dotnetdevsetup

## setup dev environment (as of 20220314)
I'm a .NET developer, so for this, I have to feed some info into the project that Docker Desktop will read in order to set up the development environment.

The dev environment is a Docker image, so the info for creating that image goes into a Dockerfile just as you would do for any other custom image. If you've created Docker images before, this will be familiar. However, as it's for special purposes, the file needs to be tucked into a special subfolder named `.docker` that must be present at the root of the repository. Note the period in front as well as the fact that it's lower case. Currently, the Dev Environment builder won't see that folder if it's not named using all lower case, so be sure to do that up front.

### create the Dockerfile 

The contents of the Dockerfile are:

```Dockerfile
# https://hub.docker.com/_/microsoft-dotnet-sdk/
FROM mcr.microsoft.com/dotnet/sdk:6.0-bullseye-slim

RUN useradd -s /bin/bash -m vscode
RUN groupadd docker && usermod -aG docker vscode

USER vscode

ENTRYPOINT ["sleep", "infinity"]
```

The first line is just a comment, so I didn't count it among the four. It's pointing to the Docker Hub URL where you can read the description of the SDK image.

The FROM command says to use this particular .NET 6 SDK image as the base for your new image. It then creates a user named vscode and a group called docker, then adds the vscode user to the docker group. Next it sets the current user to vscode. Then it tells the Dockerfile that when the container is spun up, just hang around forever (or until someone shuts it down). Otherwise, the container will think it's finished with its tasks and shut down immediately.

You may be wondering why you're setting up this vscode user. This grants Visual Studio Code the necessary rights to install its server APIs in the container

Now you need one more little itty bitty file also in the .docker folder. This is to guide the Docker Desktop dev environment builder to find the source of the image, which, in this case, is defined by your Dockerfile. You'll add a new file called config.json and in it, reference the Dockerfile by name. It's also possible to point to a pre-existing image and you'll get to do that later on in this article.
