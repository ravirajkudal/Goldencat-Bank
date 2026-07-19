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

The repository includes a GitHub Actions workflow for continuous integration and deployment. The deployment files for Kubernetes are available in [ds.yml](ds.yml).

## Notes

- The application uses Spring Security for authentication and authorization.
- Default database configuration expects MySQL running locally on port 3306.
- If you encounter database connection errors, verify your MySQL server is running and the credentials in [src/main/resources/application.properties](src/main/resources/application.properties) are correct.
