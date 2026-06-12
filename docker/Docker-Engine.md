**Docker Engine Architecture**

This article explores Docker Engine architecture, its components, evolution, standards, key objects, registry model, container creation flow, and installation verification.

**Key Components**

Docker Engine consists of three primary parts that work together to build, ship, and run containers:

**Docker Daemon (dockerd)**

The background service that manages images, containers, networks, and volumes on your host.

**REST API**

A set of HTTP endpoints that expose the daemon’s functionality to clients and automation tools.

**Docker CLI (docker)**

The command-line interface that sends commands to the REST API.


**The Open Container Initiative (OCI)**

Before 2015, container formats and runtimes were fragmented. Docker, CoreOS, and other industry leaders formed the Open Container Initiative (OCI) to standardize:

1. Runtime Specification

Defines lifecycle operations (create, start, delete, etc.).

2. Image Specification

Specifies how container images are formatted and distributed.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/dc8f0a36-2a92-4143-bc09-dd2a1cf36505" />

**runC**

The OCI-compliant runtime that handles low-level container operations.

**containerd**

A daemon responsible for managing runC instances, image transfer, and storage.

**containerd-shim**

Allows containers to keep running independently of containerd, ensuring resilience if the daemon restarts.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/52facd31-43bd-432a-b43f-ad8fac300c15" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e4afe237-f985-422a-a5e1-f6ac72ee46d7" />

**Docker Registry**

A registry is a service for storing and distributing Docker images:

1. Docker Hub (default public registry)

2. Private Registry (self-hosted)

3.Docker Trusted Registry (DTR) (enterprise-grade, on-premises

<img width="694" height="547" alt="image" src="https://github.com/user-attachments/assets/a7111d8a-c2b7-41bd-a490-1c7747287032" />

<img width="729" height="608" alt="image" src="https://github.com/user-attachments/assets/8a7fbfcc-1bba-47e9-8cf3-f0ec9e686f40" />

<img width="727" height="538" alt="image" src="https://github.com/user-attachments/assets/9c0f771b-598b-471a-86a7-737510685500" />

For more details visit : https://notes.kodekloud.com/docs/Docker-Certified-Associate-Exam-Course/Docker-Engine/Section-Introduction/page

