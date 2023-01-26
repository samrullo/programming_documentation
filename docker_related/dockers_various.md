# How to reflect changes to docker container into original image

To reflect changes made to a running Docker container back into the original image, you can use the docker commit command.

The basic syntax of the docker commit command is:


```docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]```
For example, to commit changes made to a container with ID "abc123" to a new image called "mynewimage", you would use the following command:


```docker commit -m "added new files" -a "Author Name" abc123 mynewimage```
The -m option allows you to add a commit message describing the changes made in the container
The -a option allows you to specify the author of the changes.
You can also specify a repository and tag for the new image using the REPOSITORY[:TAG] argument.


```docker commit -m "added new files" -a "Author Name" abc123 myregistry/mynewimage:latest```
You can then use the docker push command to upload the new image to a remote repository.


```docker push myregistry/mynewimage:latest```
It's worth noting that the docker commit command only commits changes made to the container's file system. Any changes made to the container's running configuration, such as environment variables or exposed ports, will not be reflected in the new image.

In order to include those changes you can use a Dockerfile, which is a script that contains instructions for building a new image. You can use the FROM instruction to use the container's image as the base image, RUN instruction to execute commands inside the container and CMD instruction to configure the container to run a specific command when it starts. Once you created the Dockerfile you can use the docker build command to build the new image.

