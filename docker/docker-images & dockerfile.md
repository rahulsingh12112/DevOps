**Why We Create Our Own Docker Image**

When you write an application, it has specific dependencies, libraries, and configurations that are unique to your app. If you only use a base image like Node.js or Python, you would need to manually install everything each time you start a container, which is time-consuming and error-prone. 

By creating your own custom image using a Dockerfile, you build a complete, pre-configured package that contains everything your app needs. This Dockerfile acts as a recipe that specifies which base OS to use, which packages to install, which environment variables to set, which code to copy, and which command to run when the container starts. When you build this image, you create an immutable snapshot that works the same way everywhere—on your laptop, on a server, or in the cloud. 

This ensures consistency across all environments. Once you push this image to a registry like Docker Hub or AWS ECR, anyone can pull it and instantly run your application without any manual setup.

Commands to Create Image from Running Container
1. Commit the container to create an image:

$ docker commit container-id image-name:tag

Example:

$ docker commit abc123def456 my-app:1.0

2. Find the container ID (if you don't know it):

$ docker ps -a

3. Verify the image was created:

$ docker images

4. Test the image by running it:

$ docker run -p 3000:3000 my-app:1.0

5. Push the image to AWS ECR (Elastic Container Registry):

**Step 1: Login to ECR**

$ aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789.dkr.ecr.us-east-1.amazonaws.com

**Step 2: Tag the image for ECR**

$ docker tag my-app:1.0 123456789.dkr.ecr.us-east-1.amazonaws.com/my-app:1.0

**Step 3: Push to ECR**

$ docker push 123456789.dkr.ecr.us-east-1.amazonaws.com/my-app:1.0

===================

**What is a Dockerfile?**

A Dockerfile is a text file that contains a set of instructions to build a Docker image. Think of it as a blueprint or recipe that tells Docker how to create a container image. Each line in a Dockerfile is an instruction that builds a layer on top of the previous one. When you run docker build, Docker reads the Dockerfile and executes each instruction sequentially to create the final image.

**Common Dockerfile instructions:**

**FROM** → Specifies the base image (starting point)

**WORKDIR** → Sets the working directory inside the container

**COPY** → Copies files from your machine to the container

**RUN** → Executes commands during image build (like installing packages)

**EXPOSE** → Declares which ports the container listens on

**ENV** → Sets environment variables

**CMD** → Specifies the default command to run when container starts

**ENTRYPOINT** → Configures the container to run as an executable

**Exmaple**

===========

**Dockerfile Example - Multi-stage Build for Python Application**

**Stage 1: Builder Stage**

$ FROM python:3.11-slim as builder

**Set working directory inside container**

$ WORKDIR /app

**Copy requirements file from host machine to container**

$ COPY requirements.txt .

**Install dependencies during image build**

$ RUN pip install --no-cache-dir -r requirements.txt

**Copy application source code**

$ COPY . .

**Stage 2: Runtime Stage**

$ FROM python:3.11-slim

**Set environment variables**

$ ENV PYTHONUNBUFFERED=1 \
    APP_ENV=production \
    LOG_LEVEL=INFO

**Set working directory**

$ WORKDIR /app

**Copy installed packages from builder stage**

$ COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages

**Copy application code from builder stage**

$ COPY --from=builder /app /app

**Declare which ports the container listens on**

$ EXPOSE 8000 5000

**Set the default command to run when container starts**

$ CMD ["python", "app.py"]

**Alternative: Use ENTRYPOINT to make container run as executable**

**ENTRYPOINT ["python", "app.py"]**

**CMD ["--host", "0.0.0.0", "--port", "8000"]**


<img width="800" height="800" alt="image" src="https://github.com/user-attachments/assets/67ed685a-5733-4846-9958-3146bf2979e3" />

======

<img width="800" height="800" alt="image" src="https://github.com/user-attachments/assets/1fcee735-66e1-4515-b664-776ed793ebc5" />


===========

**Why We Create Our Own Dockerfile**

We create our own Dockerfile because every application has unique requirements. When you write an application, it needs specific dependencies, libraries, configurations, and code that are unique to that application. If you only use a base image like Node.js or Python without a Dockerfile, you would have to manually install everything each time you start a container, which is time-consuming, error-prone, and not scalable.

By creating a Dockerfile, you define exactly what your application needs in a reproducible way. Once you build an image from your Dockerfile, that image contains everything your application needs—the base OS, all dependencies, your code, and configuration. This image is immutable, meaning it never changes. You can run this image on your laptop, on a server, or in the cloud (like AWS EKS), and it will behave exactly the same way everywhere because all the dependencies are already baked into the image.

Additionally, when you push your image to a container registry like AWS ECR or Docker Hub, anyone can pull that image and run it without needing to install anything or worry about missing dependencies. This makes deployment automated, consistent, and reliable. In a DevOps workflow, this is essential because you can version your images, track changes, and deploy with confidence knowing that what worked in development will work in production.

**Docker Layered Architecture**

Docker images are built from layers. Each instruction in a Dockerfile (FROM, RUN, COPY, etc.) creates a new layer. These layers are read-only and stack on top of each other. When a container runs, a writable layer (called the container layer) is added on top of all the read-only layers.

**How It Works**

Let's look at a simple example:

**Dockerfile:**

FROM ubuntu:20.04          → Layer 1 (Base OS)
RUN apt-get update         → Layer 2 (Updated packages)
RUN apt-get install python → Layer 3 (Python installed)
COPY app.py /app/          → Layer 4 (App code copied)
CMD ["python", "app.py"]   → Layer 5 (Default command)

When this image is built, 5 layers are created:

<img width="800" height="800" alt="image" src="https://github.com/user-attachments/assets/8d34d178-d22a-40b2-ad10-74f4fc91cca5" />

============

<img width="800" height="800" alt="image" src="https://github.com/user-attachments/assets/431f1fe5-cb4c-457c-948b-a9f9bb8a71d1" />


**Practical Scenario**

Imagine a company has 100 microservices, all Python-based:

**Without layering:**

Each service has its own 500MB image

Total storage: 100 × 500MB = 50GB

**With layering:**

Base Python image: 500MB (shared by all)

Each service's app code: 5MB

Total storage: 500MB + (100 × 5MB) = 1GB

Savings: 49GB! 🎉

**Key Concepts**

Union File System (UnionFS) - Docker uses UnionFS to mount all layers as a single file system. When you access a file, Docker starts from the top layer and searches downward until it finds the file.

Copy-on-Write (CoW) - When a container modifies a file, Docker first copies that file from the read-only layer to the container layer, then makes the modification. The original layer remains unchanged.

Layer Caching - During image builds, Docker caches layers. If a layer already exists, Docker reuses it instead of rebuilding it. This speeds up the build process significantly.

Image Size Optimization - Each layer has its own size. More layers mean larger images. This is why combining multiple RUN commands is a best practice.
