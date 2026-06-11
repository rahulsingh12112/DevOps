**Why We Create Our Own Docker Image**

When you write an application, it has specific dependencies, libraries, and configurations that are unique to your app. If you only use a base image like Node.js or Python, you would need to manually install everything each time you start a container, which is time-consuming and error-prone. 

By creating your own custom image using a Dockerfile, you build a complete, pre-configured package that contains everything your app needs. This Dockerfile acts as a recipe that specifies which base OS to use, which packages to install, which environment variables to set, which code to copy, and which command to run when the container starts. When you build this image, you create an immutable snapshot that works the same way everywhere—on your laptop, on a server, or in the cloud. 

This ensures consistency across all environments. Once you push this image to a registry like Docker Hub or AWS ECR, anyone can pull it and instantly run your application without any manual setup.

Commands to Create Image from Running Container
1. Container ko commit karke image banao:

# docker commit container-id image-name:tag

Example:

# docker commit abc123def456 my-app:1.0
