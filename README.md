# Goldencat Bank

Goldencat Bank is a Spring Boot-based banking web application that allows users to register, sign in, deposit funds, withdraw funds, transfer money to other users, and view transaction history. The project also includes CI/CD workflow configuration and container deployment support.

## Features

- User registration and login
- Secure authentication with Spring Security
- Deposit and withdrawal operations
- Money transfer between user accounts
- Transaction history view
- MySQL-backed persistence
- Docker and Kubernetes deployment support
- GitHub Actions CI/CD pipeline

## Tech Stack

- Java 17
- Spring Boot 3.3.3
- Spring MVC + Thymeleaf
- Spring Security
- Spring Data JPA
- MySQL
- Maven
- Docker / Kubernetes

## Prerequisites

Before running the project, make sure you have:

- Java 17 or newer installed
- Maven installed
- MySQL 8 running locally
- Git installed

## Database Setup

1. Create a MySQL database named `bankappdb`.
2. Update the database credentials in [src/main/resources/application.properties](src/main/resources/application.properties) if needed.
3. You can also use the SQL script in [src/main/resources/static/mysql/SQLScript.txt](src/main/resources/static/mysql/SQLScript.txt).

Example:

```sql
CREATE DATABASE bankappdb;
```

## Running the Application

### Using Maven

From the project root, run:

```bash
./mvnw spring-boot:run
```

On Windows PowerShell, use:

```powershell
./mvnw.cmd spring-boot:run
```

The application will start on:

```text
http://localhost:8080/login
```

### Using Docker

Build the image:

```bash
docker build -t goldencat-bank .
```

Run the container:

```bash
docker run -p 8080:8080 goldencat-bank
```

## Testing

Run the test suite with:

```bash
./mvnw test
```

## Project Structure

```text
src/main/java/com/example/bankapp
├── controller/      # Web controllers
├── model/           # Entity models
├── repository/      # JPA repositories
├── service/         # Business logic
└── config/          # Security configuration
```

## CI/CD

The repository includes a GitHub Actions workflow in [.github/workflows/CICD.yml](.github/workflows/CICD.yml) that automates the full delivery pipeline for the application. This workflow is designed to validate the code, improve security, analyze quality, build a deployable artifact, containerize the app, and deploy it to an AWS EKS cluster.

### What the workflow does

The pipeline is triggered automatically on every push to the `main` branch and can also be started manually through the GitHub Actions UI using `workflow_dispatch`.

The workflow is split into several jobs, each responsible for a specific stage of the delivery lifecycle:

#### 1. Compile
This is the first stage of the pipeline and ensures that the application can be built successfully.

- Checks out the repository code from GitHub
- Sets up Java 17 using the Temurin distribution
- Caches Maven dependencies for faster builds
- Runs `mvn compile` to validate that the source code compiles without errors

This job acts as a basic build gate. If the code does not compile, the pipeline stops early.

#### 2. Security-check
This stage focuses on protecting the project before it moves further down the pipeline.

- Downloads project dependencies with `mvn dependency:go-offline`
- Runs a Trivy filesystem scan to detect known vulnerabilities, exposed secrets, and misconfiguration issues
- Uses Gitleaks to scan the repository for accidentally committed sensitive information such as API keys or passwords

The purpose of this stage is to prevent insecure or risky code from reaching the build and deployment phases.

#### 3. Test
This stage runs the automated test suite.

- Checks out the code again
- Sets up Java 17 and Maven cache
- Executes `mvn test`

This ensures the application still behaves as expected after changes are made.

#### 4. SonarQube analysis
This stage performs static code quality analysis and packaging.

- Packages the application into a JAR artifact with `mvn package -DskipTests`
- Uploads the generated JAR as a workflow artifact so it can be reused in later jobs
- Sends the code to SonarQube for quality analysis
- Verifies the SonarQube quality gate result before continuing

This step helps maintain code quality, identify bugs, and enforce coding standards.

#### 5. Docker build and push
Once the application artifact is validated, the pipeline builds a container image.

- Downloads the JAR artifact from the previous stage
- Logs into Docker Hub using stored credentials
- Uses Docker Buildx to build the image
- Pushes the image to Docker Hub with the tag `ravikudal/app:latest`

This stage makes the application portable and ready for container-based deployment.

#### 6. AWS EKS deployment
The final stage deploys the application to a Kubernetes cluster on AWS EKS.

- Configures AWS CLI and AWS credentials
- Installs kubectl
- Loads the kubeconfig from a GitHub secret
- Applies the Kubernetes manifests from [ds.yml](ds.yml) to the cluster

This allows the latest version of the application to be deployed automatically after a successful build and image push.

### Workflow triggers

The workflow runs when:

- A commit is pushed to the `main` branch
- The workflow is manually triggered from GitHub Actions using the `Run workflow` option

### Required GitHub secrets and variables

To make the workflow run successfully, configure the following repository settings:

- Secrets:
  - `SONAR_TOKEN`
  - `DOCKERHUB_TOKEN`
  - `AWS_ACCESS_KEY_ID`
  - `AWS_SECRET_ACCESS_KEY`
  - `EKS_KUBECONFIG`

- Variables:
  - `SONAR_HOST_URL`
  - `DOCKERHUB_USERNAME`

### Why this CI/CD pipeline is useful

This pipeline helps automate the software delivery process by:

- catching compile errors early
- improving security through vulnerability scanning
- validating code quality with SonarQube
- running tests automatically
- creating Docker images consistently
- deploying the app to Kubernetes without manual effort

In short, it turns the repository into a more reliable and production-ready delivery pipeline.

## Notes

- The application uses Spring Security for authentication and authorization.
- Default database configuration expects MySQL running locally on port 3306.
- If you encounter database connection errors, verify your MySQL server is running and the credentials in [src/main/resources/application.properties](src/main/resources/application.properties) are correct.
