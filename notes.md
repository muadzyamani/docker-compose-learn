# Introduction

### Course Goal
- To understand and effectively implement **Docker Compose** to simplify and improve the local development workflow with Docker.

### Key Benefits of Docker Compose
- **Simplifies Complexity:** Takes the complexity out of local development in Docker.
- **Increases Efficiency:** Makes developers more efficient and happier.
- **Solves Pain Points:** Addresses common challenges faced by Docker users.

### Learning Objectives
- **Understand the Tool:** Define what Docker Compose is and, importantly, what it is not.
- **Master Core Functionality:** Walk through all core features using real-world examples.
- **Add Flexibility:** Learn how Docker Compose can be used to add flexibility to your systems.

## What you should know

### Prerequisites
- **Required:**
    - Familiarity with software containerization tools in general.
    - Specific knowledge and experience with **Docker**.
- **Important:** This is **not** an introductory Docker course.

### Self-Assessment Check
- Before starting, you should be able to clearly explain the difference between a **Docker image** and a **Docker container**.
- If this distinction is unclear, it is recommended to watch a foundational Docker course first.

### Course Concepts
- The course will cover concepts such as:
    - **Storage Volumes**
    - **Port Mappings**
- **Knowledge Level:**
    - **If concepts are new:** You should read about them before starting the course.
    - **If you are "fuzzy on the details":** The instructor will provide a brief review of each concept as it becomes relevant, so you can still follow along.

---

# 1. Understanding Docker Compose

## Compose in the Docker tool ecosystem

### The Challenge of Complex Systems
- Real-world software systems are rarely simple one or two-container applications.
- A mature system often has:
    - Multiple Dockerized services, each with unique configurations.
    - Shared third-party dependencies.
    - Service dependencies requiring a specific initialization order.
    - In microservice architectures, this can scale to hundreds of services.
- Manually starting and managing many containers with individual commands is tedious, error-prone, and can become impossible at scale.

### The Solution: Docker Compose
- **Docker Compose** is a tool that simplifies the management of multi-container Docker applications.
- It comes standard with most Docker distributions.
- **Core Concept:** Docker Compose is fundamentally a **YAML language** that provides scaffolding for developers to specify their Docker configurations **as code**.
- Instead of running many manual `docker` commands, developers can define their entire application stack in a single configuration file and manage it with simple commands (e.g., `docker-compose up`).

### Role in the Docker Ecosystem
- Docker Compose **does not add new functionality** to Docker itself.
- It makes the **existing functionality** (like creating networks, volumes, and containers) significantly **easier to use and orchestrate**.

## Docker Compose basics

### Configuration as Code
- This is the core principle behind Docker Compose.
- **Configuration** includes all settings needed for a system to run:
    - Persistent data locations (volumes).
    - Service discovery and communication.
    - Environment-specific variables.

### Declarative vs. Procedural Configuration
- **Procedural (The "Old Way"):**
    - You define a specific sequence of steps to execute (e.g., in a shell script).
    - Each step depends on the previous one.
    - Prone to errors if the environment's state doesn't match implicit assumptions.
    - **Example:** A script to `docker run` a container will fail the second time if the container from the first run is still running and not explicitly stopped.

- **Declarative (The Docker Compose Way):**
    - You specify the **desired end state** of your system in a configuration file.
    - Docker Compose handles the implementation details (the "how") to reach that state. This includes startup, cleanup, and dependency resolution.
    - **Idempotent:** Running the configuration multiple times will produce the same end result, as Compose will automatically add, stop, or recreate containers as needed to match the desired state.

### Advantages of Configuration as Code
1.  **Version Control:** The configuration file can be checked into Git, allowing for change tracking, collaboration, and easy rollbacks.
2.  **Self-Documenting:** The `docker-compose.yml` file clearly describes the application's services, dependencies, and setup, eliminating the need to remember complex commands.
3.  **Environment Management:** Makes it simple to manage multiple environments (e.g., `local`, `testing`) by using separate, slightly different configuration files for each.

## Where to use Docker Compose

### Ideal Use Cases
- Docker Compose is designed for a **single hosted server**.
- It is well-suited for non-production environments like:
    - **Local development**
    - **Staging servers**
    - **Continuous Integration (CI) testing environments**

### Limitations and Non-Ideal Use Cases
- Docker Compose is **not designed for production environments** with significant user traffic.
- It lacks features necessary for production-grade orchestration:
    - **No distributed system support:** Cannot manage containers across multiple host machines.
    - **No independent scaling:** You cannot scale a single service without scaling the entire stack.
    - **No auto-scaling:** Cannot automatically adjust the number of containers based on traffic load.

### Case Study: KinetEco
- A clean energy business with two applications: a **storefront** and a **scheduler**.
- **Scenario:** The storefront expects a temporary traffic spike from a sale and needs to be scaled up.
- **Docker Compose Limitation:** The only way to scale is to run the entire Compose configuration on multiple hosts. This would unnecessarily scale up the `scheduler` service as well, wasting resources.

### Production-Ready Alternatives
- For distributed, scalable production environments, use a dedicated container orchestration tool like:
    - **Docker Swarm**
    - **Kubernetes**

## Chapter Quiz

**Question 1 of 2**

Which type of environment is not well suited to use Docker Compose?

-   `local`
-   `staging`
-   **`production` (Correct)**
    > **Explanation:** Docker Compose is not intended for independently scaling services or services that run across multiple hosts, which are common requirements for production environments.
-   `test`

---

**Question 2 of 2**

Which definition best describes declarative configuration tools like Docker Compose?

-   `They allow you to specify either the desired end result or an exact sequence of steps to follow.`
-   `They are an alternative to coding languages like Java and Python.`
-   `They allow you to specify an exact sequence of steps to follow.`
-   **`They allow you to specify the desired end result only.` (Correct)**
    > **Explanation:** Declarative configuration tools allow you to specify the desired end result only and abstract out the steps to achieve that result.

---

# 2. Getting Started with Docker Compose

## Writing a Docker Compose configuration

### File Format and Naming
-   A Docker Compose configuration must be in a **YAML** file.
-   **YAML** stands for "Yet Another Markup Language" and is a standard for data serialization, similar to JSON.
-   The file must be named `docker-compose.yaml`.

### Basic File Structure
-   A typical `docker-compose.yaml` file has the following structure:

1.  **`version`**:
    -   Specifies the version of the Docker Compose file format being used.
    -   This is usually the first line in the file.
    -   Example: `version: '3.8'`

2.  **`services`**:
    -   A top-level key where all the application's containers (services) are defined.
    -   Indentation (spaces or tabs) is crucial in YAML to define nested objects.

### Example: KinetEco Application
-   **Scenario**: An online storefront that uses a MySQL database for inventory.

```yaml
# Specifies the version of the Docker Compose file format
version: '3.8'

# Defines all the containers the application needs
services:
  # Defines the first service, named 'storefront'
  storefront:
    # 'build' tells Compose to build an image from a local Dockerfile
    # The '.' means the Dockerfile is in the current directory
    build: .

  # Defines the second service, named 'database'
  database:
    # 'image' tells Compose to pull a pre-built image from Docker Hub
    image: mysql
```

### Defining Services
-   **Service Names**:
    -   Services can be given any name (e.g., `storefront`, `database`).
    -   These names are human-readable and should be meaningful to developers.
-   **Building from a Dockerfile (`build`)**:
    -   Use the `build` keyword to instruct Docker Compose to build a Docker image from a local source.
    -   Provide the path to the directory containing the `Dockerfile`.
    -   A `.` denotes the current directory (where `docker-compose.yaml` is located).
-   **Using a Pre-built Image (`image`)**:
    -   Use the `image` keyword to specify a pre-built image from a container registry like Docker Hub.
    -   Docker Compose will automatically fetch (pull) the specified image if it's not available locally.

## Core Docker Compose commands

### Overview
-   Docker Compose commands are used to manage the lifecycle of the services defined in `docker-compose.yaml`.
-   All commands are run from a terminal in the same directory as the configuration file.
-   The basic syntax is `docker-compose <command>`.

### Common Commands
-   **`docker-compose up`**
    -   The primary command to start your application.
    -   It's an all-in-one command that performs three main actions in order:
        1.  **Builds** the images for services that have a `build` instruction.
        2.  **Creates** the containers for all services.
        3.  **Starts** the containers.
    -   You can target a specific service (and its dependencies) by name: `docker-compose up storefront`.

-   **`docker-compose down`**
    -   The opposite of `up`. It performs a full teardown.
    -   **Stops** all running containers defined in the file.
    -   **Deletes** the stopped containers.
    -   Removes any artifacts created by `up`, such as default networks.
    -   This is a more aggressive cleanup than `stop` and is useful when you've made changes and need a fresh start.

-   **`docker-compose stop`**
    -   Stops the running containers but **does not delete them**.
    -   Useful for temporarily pausing the environment to free up system resources like memory or CPU/battery. The state of the containers is preserved.

-   **`docker-compose restart`**
    -   A convenient shortcut for `docker-compose stop` followed by `docker-compose start`.
    -   It's the equivalent of "turning it off and on again" and can be useful for fixing transient errors.

-   **Individual Lifecycle Commands**
    -   For more granular control, you can use individual commands instead of the all-in-one `up`.
    -   `docker-compose build`: Builds or rebuilds the images.
    -   `docker-compose create`: Creates the containers but does not start them.
    -   `docker-compose start`: Starts existing, stopped containers.
    -   `docker-compose rm`: Deletes stopped containers.

-   **`docker-compose --help`**
    -   Displays a complete list of all available commands with descriptions, serving as a helpful reference.

## Chapter Quiz

**Question 1 of 2**

You can think of some Docker Compose commands as combinations of other Docker Compose commands. Which combination of commands is correct?

-   `build + create + run = up`
-   `down + up = restart`
-   `build + stop = down`
-   **`build + create + start = up` (Correct)**
    > **Explanation:** `docker-compose up` is a shortcut for building images, creating containers, and then starting them.

**Question 2 of 2**

What is a valid format for a Docker Compose file?

-   `.txt`
-   `.json`
-   **`.yaml` (Correct)**
    > **Explanation:** Docker Compose configurations must be written in YAML format and typically named `docker-compose.yaml`.
-   `.html`