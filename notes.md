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

- `local`
- `staging`
- **`production` (Correct)**
  > **Explanation:** Docker Compose is not intended for independently scaling services or services that run across multiple hosts, which are common requirements for production environments.
- `test`

---

**Question 2 of 2**

Which definition best describes declarative configuration tools like Docker Compose?

- `They allow you to specify either the desired end result or an exact sequence of steps to follow.`
- `They are an alternative to coding languages like Java and Python.`
- `They allow you to specify an exact sequence of steps to follow.`
- **`They allow you to specify the desired end result only.` (Correct)**
  > **Explanation:** Declarative configuration tools allow you to specify the desired end result only and abstract out the steps to achieve that result.

---

# 2. Getting Started with Docker Compose

## Writing a Docker Compose configuration

### File Format and Naming

- A Docker Compose configuration must be in a **YAML** file.
- **YAML** stands for "Yet Another Markup Language" and is a standard for data serialization, similar to JSON.
- The file must be named `docker-compose.yaml`.

### Basic File Structure

- A typical `docker-compose.yaml` file has the following structure:

1.  **`version`**:

    - Specifies the version of the Docker Compose file format being used.
    - This is usually the first line in the file.
    - Example: `version: '3.8'`

2.  **`services`**:
    - A top-level key where all the application's containers (services) are defined.
    - Indentation (spaces or tabs) is crucial in YAML to define nested objects.

### Example: KinetEco Application

- **Scenario**: An online storefront that uses a MySQL database for inventory.

```yaml
# Specifies the version of the Docker Compose file format
version: "3.8"

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

- **Service Names**:
  - Services can be given any name (e.g., `storefront`, `database`).
  - These names are human-readable and should be meaningful to developers.
- **Building from a Dockerfile (`build`)**:
  - Use the `build` keyword to instruct Docker Compose to build a Docker image from a local source.
  - Provide the path to the directory containing the `Dockerfile`.
  - A `.` denotes the current directory (where `docker-compose.yaml` is located).
- **Using a Pre-built Image (`image`)**:
  - Use the `image` keyword to specify a pre-built image from a container registry like Docker Hub.
  - Docker Compose will automatically fetch (pull) the specified image if it's not available locally.

## Core Docker Compose commands

### Overview

- Docker Compose commands are used to manage the lifecycle of the services defined in `docker-compose.yaml`.
- All commands are run from a terminal in the same directory as the configuration file.
- The basic syntax is `docker-compose <command>`.

### Common Commands

- **`docker-compose up`**

  - The primary command to start your application.
  - It's an all-in-one command that performs three main actions in order:
    1.  **Builds** the images for services that have a `build` instruction.
    2.  **Creates** the containers for all services.
    3.  **Starts** the containers.
  - You can target a specific service (and its dependencies) by name: `docker-compose up storefront`.

- **`docker-compose down`**

  - The opposite of `up`. It performs a full teardown.
  - **Stops** all running containers defined in the file.
  - **Deletes** the stopped containers.
  - Removes any artifacts created by `up`, such as default networks.
  - This is a more aggressive cleanup than `stop` and is useful when you've made changes and need a fresh start.

- **`docker-compose stop`**

  - Stops the running containers but **does not delete them**.
  - Useful for temporarily pausing the environment to free up system resources like memory or CPU/battery. The state of the containers is preserved.

- **`docker-compose restart`**

  - A convenient shortcut for `docker-compose stop` followed by `docker-compose start`.
  - It's the equivalent of "turning it off and on again" and can be useful for fixing transient errors.

- **Individual Lifecycle Commands**

  - For more granular control, you can use individual commands instead of the all-in-one `up`.
  - `docker-compose build`: Builds or rebuilds the images.
  - `docker-compose create`: Creates the containers but does not start them.
  - `docker-compose start`: Starts existing, stopped containers.
  - `docker-compose rm`: Deletes stopped containers.

- **`docker-compose --help`**
  - Displays a complete list of all available commands with descriptions, serving as a helpful reference.

## Chapter Quiz

**Question 1 of 2**

You can think of some Docker Compose commands as combinations of other Docker Compose commands. Which combination of commands is correct?

- `build + create + run = up`
- `down + up = restart`
- `build + stop = down`
- **`build + create + start = up` (Correct)**
  > **Explanation:** `docker-compose up` is a shortcut for building images, creating containers, and then starting them.

**Question 2 of 2**

What is a valid format for a Docker Compose file?

- `.txt`
- `.json`
- **`.yaml` (Correct)**
  > **Explanation:** Docker Compose configurations must be written in YAML format and typically named `docker-compose.yaml`.
- `.html`

---

# 3. Docker Compose Core Features

## Build arguments

### Build Arguments vs. Environment Variables

A key concept in Docker is understanding the difference between `build arguments` and `environment variables`.

- **Build Arguments (`args`)**:

  - Available to Docker **only at build time**.
  - They are **not accessible** from inside the running container.
  - **Use Case**: Passing configuration that influences the image build process, such as a tool version or a cloud provider region (e.g., `AWS_REGION=us-east-1`). This allows for creating region-specific images from a single `Dockerfile`.

- **Environment Variables (`environment` / `env_file`)**:
  - Passed into and available **inside the running container**.
  - **Use Case**: Providing runtime configuration to the application, such as the current environment (`dev`, `test`), API keys, or feature flags. The MySQL image, for example, requires environment variables for the root password and database name.

### Syntax in Docker Compose

- **To specify `build arguments`**:

  - The `build` key must be changed from the shorthand (`build: .`) to the long-form object syntax.

  ```yaml
  services:
    storefront:
      build:
        context: . # Path to the build directory
        args:
          - ARG_NAME=ARG_VALUE
          - AWS_REGION=us-west-2
  ```

- **To specify `environment variables`**:
  - **Method 1: Inline (`environment`)**
    - List variables directly in the YAML file.
    - To pass a variable's value from the host machine, omit the value (e.g., `- RUNTIME_ENV`). Compose will use the `RUNTIME_ENV` variable from the shell it's running in.
    ```yaml
    services:
      database:
        image: mysql
        environment:
          - MYSQL_ROOT_PASSWORD=my-secret-pw
          - RUNTIME_ENV=dev
    ```
  - **Method 2: From a file (`env_file`)**
    - A cleaner way to manage many variables, especially secrets.
    - Provide a path to a file containing `KEY=VALUE` pairs.
    ```yaml
    services:
      database:
        image: mysql
        env_file:
          - ./mysql_env_vars.env
    ```

## Mounting volumes

### Purpose

- Volumes are a Docker feature for **persisting data**.
- They ensure that important data is not deleted when a container is stopped or removed.

### Configuration

A volume mount requires at least a `target`, but typically includes a `source` and `mode`. The short syntax is `SOURCE:TARGET:MODE`.

- **`target`**: The directory path **inside** the running container where data will be written. This is mandatory.
  - Example: For MySQL, the default data directory is `/var/lib/mysql`.
- **`source`**: The directory path on the **host machine** where the data is stored.
  - If a source is omitted, Docker creates an **anonymous volume** with a random name on the host.
  - Paths can be relative (`./data`, `../data`) or absolute (`/var/data`).
- **`mode`**: The access permission.
  - `rw` (read-write): The default mode. The container can read from and write to the volume.
  - `ro` (read-only): The container can only read from the volume. This is useful for providing configuration or initial seed data that the application shouldn't change.

### Example Syntax

```yaml
services:
  database:
    image: mysql
    volumes:
      # Mount a local SQL dump to seed the database (read-only)
      - ./sql_dump:/docker-entrypoint-initdb.d:ro

      # Mount a local directory to persist the database data (read-write)
      - ./mysql_data:/var/lib/mysql:rw
```

## Named volumes

### The Problem with Unnamed Volumes

- When using a host path (`./mysql_data`), the volume's lifecycle is managed by the user.
- When omitting a source, Docker creates an **anonymous volume** (e.g., `a74b1e...:/var/lib/mysql`).
- Running `docker-compose up` repeatedly can create many unused anonymous volumes, consuming significant disk space.

### The Solution: Named Volumes

- **Named volumes** are managed by Docker and Compose, making their lifecycle easier to handle.
- **Advantages**:
  1.  **Prevents Proliferation**: Prevents the creation of a new anonymous volume on each run.
  2.  **Data Migration**: Compose automatically copies data from an old container's volume to a new one during restarts or updates, ensuring no data is lost.
  3.  **Easy Cleanup**: Running `docker-compose down --volumes` will automatically find and delete the named volumes associated with your application stack.

### Syntax

1.  **Declare the volume**: At the top level of the `docker-compose.yaml` file, add a `volumes` section and name your volume.
2.  **Use the volume**: In the service definition, use the name of the volume as the `source` of the mount.

```yaml
services:
  database:
    image: mysql
    volumes:
      # Use the named volume 'kineteco_data' as the source
      - kineteco_data:/var/lib/mysql

# Top-level declaration of the named volume
volumes:
  kineteco_data:
```

### Long vs. Short Syntax

Compose (version 3.2+) supports a more verbose "long syntax" which is more readable.

| Long Syntax (Recommended)                                                                          | Short Syntax                                      |
| -------------------------------------------------------------------------------------------------- | ------------------------------------------------- |
| `volumes:`<br/>`  - type: volume`<br/>`    source: kineteco_data`<br/>`    target: /var/lib/mysql` | `volumes:`<br/>`  - kineteco_data:/var/lib/mysql` |

## Exposing ports

### Purpose

- By default, containers are isolated and cannot be accessed from the host machine or the outside world.
- **Port mapping** creates a pathway from a port on the host to a port inside the container, enabling communication.

### Syntax

- Port mappings are defined under the `ports` key for a service.
- The format is **`"HOST_PORT:CONTAINER_PORT"`**. It's helpful to think of this as "traffic from the host port goes to the container port".

### Handling Port Collisions

- A port on the host machine can only be used by one process at a time.
- **Scenario**: The `storefront` and `scheduler` services both run on port 80 internally. Mapping both to port 80 on the host will cause a **port collision error**.
- **Solution**: Map the services to different host ports. The internal container port can remain the same.

```yaml
services:
  storefront:
    build: ./storefront
    ports:
      # Map host port 80 to container port 80
      - "80:80"
      # Expose a second port for monitoring
      - "443:443"

  scheduler:
    build: ./scheduler
    ports:
      # Avoid collision by mapping host port 81 to container port 80
      - "81:80"
```

- **Note**: It is recommended to quote port mappings (e.g., `"80:80"`) to avoid YAML parsing issues with numbers below 60.

## Enforcing start-up order

### The `depends_on` Flag

- In many applications, some services depend on others (e.g., a web application depends on a database).
- The `depends_on` flag tells Compose to start services in a specific order.

### Behavior

- `docker-compose up`: Starts dependencies first (e.g., `database` before `storefront`).
- `docker-compose down`: Stops services in the reverse order (`storefront` before `database`).
- `docker-compose up <service_name>`: Automatically starts the specified service and all of its dependencies.

### Syntax

```yaml
services:
  storefront:
    build: .
    ports:
      - "80:80"
    # This service depends on the 'database' service
    depends_on:
      - database

  database:
    image: mysql
    env_file:
      - ./mysql.env
```

### Important Limitation and Best Practice

- `depends_on` **ONLY guarantees the start order** of containers. It **DOES NOT** wait for the dependency to be "ready" or "healthy" (e.g., for the database process to be fully initialized and accepting connections).
- **Best Practice**: In a distributed system, services must be **resilient**. Your application code should handle cases where a dependency is temporarily unavailable (e.g., using connection retries with exponential backoff). Relying on a dependency to always be available creates a fragile, tightly coupled system. Third-party "wait-for-it" scripts exist but are not recommended as a substitute for building resilient applications.

## Chapter Quiz

**Question 1 of 5**
Which statement about port mapping is true?

- **Correct:** Two containers exposing the same port on the host machine will create port collision errors.
  > **Explanation:** If two containers are using the same internal port, at least one must be mapped to a different port number on the host machine to avoid collisions.

**Question 2 of 5**
What is the primary advantage of using a named volume?

- **Correct:** It prevents Docker from creating a new volume for each run, which will take up extra memory on the host machine.

**Question 3 of 5**
What is not valid syntax for specifying a Docker volume configuration in Docker Compose?

- **Correct:** `/persistence:/myvolume:ro:.`
  > **Explanation:** This configuration is invalid because there are a maximum of 3 arguments for specifying a volume: source, destination, and access mode. This example has 4 arguments.

**Question 4 of 5**
Which statement about build arguments and environment variables is true?

- **Correct:** Build arguments are accessible during build time only, and environment variables are accessible to the running container only.

**Question 5 of 5**
Why is enforcing the availability of dependencies on start-up not recommended?

- **Correct:** In a distributed system, it is important to gracefully handle unavailability.
  > **Explanation:** The `depends_on` configuration can only guarantee that a dependency has been started by Docker Compose, not that it is available or healthy.

---

# 4. Dynamic Configurations in Docker Compose

## Named subsets of services (Service Profiles)

### Use Case

- In large systems, different teams may work on different parts of an application (e.g., a "storefront" team and a "scheduler" team).
- To save local machine resources, teams only want to run the services they are actively working on.
- However, the entire system needs to run together for integration testing, so separate `docker-compose` files are not ideal.

### Solution: Service Profiles

- Service profiles allow you to categorize services within a single `docker-compose.yaml` file.
- You can then start up specific categories (profiles) of services as needed.

### Implementation

- Use the `profiles` keyword under a service definition and provide a list of one or more profile names.

**Example `docker-compose.yaml`:**

```yaml
services:
  storefront:
    build: ./storefront
    profiles:
      - storefront_services # This service belongs to the 'storefront_services' profile
    depends_on:
      - database

  scheduler:
    build: ./scheduler
    profiles:
      - scheduling_services # This service belongs to the 'scheduling_services' profile
    depends_on:
      - database

  database:
    image: mysql
    # No profile is assigned here
```

### The "Default" Profile

- Any service **without** a `profiles` key is automatically assigned to the **default profile**.
- **Behavior:**
  - Default services will start automatically whenever **any** profile is activated.
  - Running `docker-compose up` with no flags will **only** start the services in the default profile (in this example, just the `database`).

### How to Use Profiles

- Use the `--profile` flag with any `docker-compose` command (`up`, `down`, `restart`, etc.).
- **Command Syntax:** `docker-compose --profile <profile_name> <command>`

- **Examples:**
  - To run only the storefront and the database:
    ```bash
    docker-compose --profile storefront_services up
    ```
  - To run only the scheduler and the database:
    ```bash
    docker-compose --profile scheduling_services up
    ```
  - To run everything (both profiles + default):
    ```bash
    docker-compose --profile storefront_services --profile scheduling_services up
    ```

## Multiple compose files

### Use Case

- Ideal for situations with two distinct sets of behaviors that will **never coincide**.
- The best example is managing different **environments**, such as `local`, `staging`, and `CI`. You would never run a `local` and `staging` configuration on the same machine at the same time.
- **Anti-pattern:** Using multiple files for different parts of a single system (use **service profiles** for that instead).

### The Override Mechanism

- By default, Docker Compose reads two files:
  1.  `docker-compose.yaml` (the base configuration)
  2.  `docker-compose.override.yaml` (the override configuration)
- Compose merges these two files automatically. The override file does not need to be a complete configuration; it only needs to contain the specific values you want to change.

### Merge Rules

- **Array-based fields** (e.g., `ports`, `depends_on`, `volumes`): Values are **appended**. All entries from both files are combined.
- **Single-value fields** (e.g., `image`, `build`, `container_name`): The value from the **override file wins**.

### Custom Override Files

- You can have multiple, named override files (e.g., `docker-compose.local.yaml`, `docker-compose.staging.yaml`).
- To use these, you must specify them with the `-f` (or `--file`) flag.

### How to Use Custom Files

- The order of files in the command matters; configurations are merged in the order they are provided, with later files overriding earlier ones.

- **Command Syntax:**
  ```bash
  docker-compose -f docker-compose.yaml -f docker-compose.local.yaml up
  ```
- **Note:** File paths referenced in an override file must be relative to the location of the **primary** `docker-compose.yaml` file.

## Environment Variable Substitution

### Use Case

- For small, dynamic changes (like image tags, versions, or ports) where creating an entire override file is overkill.
- Allows for a single, flexible `docker-compose.yaml` file that can adapt to different environments.

### Syntax

- Use shell-like syntax to substitute variables from the host environment into the `docker-compose.yaml` file: `$VARIABLE` or `${VARIABLE}`. The curly-brace syntax is recommended for clarity.

**Example `docker-compose.yaml`:**

```yaml
services:
  database:
    # The image tag is now dynamic, controlled by the TAG environment variable
    image: mysql:${TAG:-latest} # Provides an inline default of 'latest'
```

### Providing Values and Defaults (Hierarchy of Precedence)

Values for variables are resolved in the following order (1 takes highest precedence):

1.  **Shell Environment:** A variable set in the command-line shell will always win.
    ```bash
    TAG=8.0.29 docker-compose up
    ```
2.  **`.env` File:** Docker Compose automatically reads a file named `.env` in the project root directory. This is the standard way to set default values for a project.
    ```
    # .env file contents
    TAG=8.0
    ```
3.  **Inline Default in `docker-compose.yaml`:** You can specify a fallback value directly in the compose file.
    - `:-` (default if unset or empty): `${VARIABLE:-default_value}`
    - `:` (default if unset): `${VARIABLE-default_value}`

### Forcing a Variable to be Set

- You can make a variable mandatory. If it's not set, Compose will exit with an error.
- **Syntax:** `${VARIABLE:?Error message if variable is not set}`
  ```yaml
  image: mysql:${TAG:?The TAG environment variable must be set!}
  ```

### Using a Custom Environment File

- If your environment file is named differently or is in another directory, specify it with the `--env-file` flag.
  ```bash
  docker-compose --env-file ./config/prod.env up
  ```

## Chapter Quiz

**Question 1 of 3**
An environment variable is set in three different places. Which one takes precedence?

- `the default value configured inline`
- `the value set in a .env file`
- **`the value set in the shell` (Correct)**
  > **Explanation:** Shell variables always override defaults set in `.env` files or inline in the compose file.

**Question 2 of 3**
What is not a recommended use case for multiple compose files?

- **`having multiple compose files for different parts of an application or system` (Correct)**
  > **Explanation:** This is not recommended because an engineer may need to run the whole system at once. Service profiles are the better tool for this use case.
- `having multiple compose files for independent versions of an application`
- `having multiple compose files for different environments, such as local and staging`

**Question 3 of 3**
What is the most correct definition of service profiles in Docker Compose?

- `Service profiles allow you to group sets of environment variables together within the Docker Compose configuration.`
- **`Service profiles allow you to organize Docker services into one or more categories for easy start-up.` (Correct)**
  > **Explanation:** Service profiles allow you to specify which groups of containers to start and stop or establish a default group.
- `Service profiles allow you to document Docker services inside a configuration file.`
