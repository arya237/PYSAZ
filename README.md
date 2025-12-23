# PYSAZ - Go Backend Service

A robust and scalable Go backend service designed to provide enterprise-grade API functionality with comprehensive database support and professional architecture.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Setup Instructions](#setup-instructions)
- [Database Configuration](#database-configuration)
- [API Endpoints](#api-endpoints)
- [Development](#development)
- [Testing](#testing)
- [Deployment](#deployment)
- [Contributing](#contributing)
- [License](#license)

## Overview

PYSAZ is a production-ready Go backend service built with modern best practices. It provides a solid foundation for building scalable applications with clean architecture, comprehensive error handling, and professional logging capabilities.

### Technology Stack

- **Language**: Go 1.19+
- **Database**: PostgreSQL / MySQL (configurable)
- **HTTP Framework**: Gin/Echo (or your chosen framework)
- **ORM**: GORM
- **Logging**: Structured logging with JSON output
- **Configuration**: Environment-based configuration

## Features

### Core Features

- ✅ **RESTful API**: Comprehensive REST API with standard HTTP methods
- ✅ **Database Integration**: Full ORM support with migration tools
- ✅ **Authentication**: JWT-based authentication and authorization
- ✅ **Error Handling**: Centralized error handling and custom error types
- ✅ **Logging**: Structured logging with multiple output levels
- ✅ **Validation**: Request body validation and data sanitization
- ✅ **CORS Support**: Cross-Origin Resource Sharing configuration
- ✅ **Rate Limiting**: Built-in rate limiting for API protection
- ✅ **Database Transactions**: Support for complex database operations
- ✅ **Middleware Stack**: Request/response middleware pipeline
- ✅ **Health Checks**: Service health and readiness endpoints
- ✅ **Graceful Shutdown**: Proper cleanup and connection management

## Project Structure

```
PYSAZ/
├── cmd/
│   └── main.go                 # Application entry point
├── pkg/
│   ├── api/
│   │   ├── handlers/          # HTTP request handlers
│   │   ├── middleware/        # HTTP middleware
│   │   └── routes.go          # Route definitions
│   ├── config/
│   │   └── config.go          # Configuration management
│   ├── database/
│   │   ├── migrations/        # Database migrations
│   │   └── db.go              # Database connection setup
│   ├── models/
│   │   └── *.go               # Data models
│   ├── repository/
│   │   └── *.go               # Data access layer
│   ├── service/
│   │   └── *.go               # Business logic layer
│   ├── utils/
│   │   ├── logger.go          # Logging utilities
│   │   ├── response.go        # Response formatting
│   │   └── validator.go       # Validation utilities
│   └── errors/
│       └── errors.go          # Custom error types
├── migrations/
│   └── *.sql                  # SQL migration files
├── tests/
│   ├── unit/                  # Unit tests
│   ├── integration/           # Integration tests
│   └── fixtures/              # Test data
├── docs/
│   ├── API.md                 # API documentation
│   ├── SETUP.md               # Detailed setup guide
│   └── DATABASE.md            # Database documentation
├── .env.example               # Environment variables template
├── docker-compose.yml         # Docker Compose for local development
├── Dockerfile                 # Docker image configuration
├── go.mod                     # Go module definition
├── go.sum                     # Go module checksums
└── README.md                  # This file
```

## Prerequisites

Before you begin, ensure you have the following installed:

- **Go**: Version 1.19 or higher
  - Download: https://golang.org/dl/
- **PostgreSQL**: Version 12 or higher (or MySQL 5.7+)
  - Download: https://www.postgresql.org/download/
- **Git**: For version control
  - Download: https://git-scm.com/
- **Docker** (Optional): For containerized development
  - Download: https://www.docker.com/products/docker-desktop
- **Make** (Optional): For running development tasks
  - macOS/Linux: Pre-installed
  - Windows: Install via `choco install make`

## Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/arya237/PYSAZ.git
cd PYSAZ
```

### 2. Environment Configuration

Copy the example environment file and configure it:

```bash
cp .env.example .env
```

Edit `.env` with your configuration:

```env
# Server Configuration
SERVER_PORT=8080
SERVER_ENV=development
LOG_LEVEL=info

# Database Configuration
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=your_password
DB_NAME=pysaz_dev
DB_SSL_MODE=disable

# JWT Configuration
JWT_SECRET=your_jwt_secret_key_change_this
JWT_EXPIRY=24h

# Service Configuration
SERVICE_TIMEOUT=30s
SERVICE_MAX_CONNECTIONS=25
```

### 3. Install Dependencies

Download and install Go module dependencies:

```bash
go mod download
go mod tidy
```

### 4. Database Setup

#### Option A: Using PostgreSQL (Recommended)

Create the database:

```bash
createdb pysaz_dev
```

Run migrations:

```bash
go run cmd/main.go migrate
```

Or manually:

```bash
psql -d pysaz_dev -f migrations/001_initial_schema.sql
```

#### Option B: Using Docker Compose

Start PostgreSQL and other services:

```bash
docker-compose up -d
```

The database will be automatically initialized with default credentials from `docker-compose.yml`.

### 5. Run the Application

```bash
go run cmd/main.go
```

The server will start on `http://localhost:8080`

Or build and run:

```bash
go build -o bin/pysaz cmd/main.go
./bin/pysaz
```

### 6. Verify Installation

Check if the service is running:

```bash
curl http://localhost:8080/health
```

Expected response:

```json
{
  "status": "healthy",
  "timestamp": "2025-12-23T15:59:45Z",
  "uptime": "5s"
}
```

## Database Configuration

### PostgreSQL Configuration

#### Connection String Format

```
postgres://username:password@host:port/database?sslmode=disable
```

#### Configuration Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| DB_HOST | localhost | Database host address |
| DB_PORT | 5432 | Database port number |
| DB_USER | postgres | Database user |
| DB_PASSWORD | - | Database password |
| DB_NAME | pysaz_dev | Database name |
| DB_SSL_MODE | disable | SSL mode (disable/require/verify-ca/verify-full) |
| DB_MAX_CONNECTIONS | 25 | Maximum connection pool size |
| DB_CONN_TIMEOUT | 5s | Connection timeout duration |

#### Creating Database and User

```sql
-- Connect to PostgreSQL
psql -U postgres

-- Create database
CREATE DATABASE pysaz_dev;

-- Create user
CREATE USER pysaz_user WITH PASSWORD 'secure_password';

-- Grant privileges
ALTER ROLE pysaz_user SET client_encoding TO 'utf8';
ALTER ROLE pysaz_user SET default_transaction_isolation TO 'read committed';
ALTER ROLE pysaz_user SET default_transaction_deferrable TO on;
GRANT ALL PRIVILEGES ON DATABASE pysaz_dev TO pysaz_user;

-- Connect to database and grant schema privileges
\c pysaz_dev
GRANT ALL ON SCHEMA public TO pysaz_user;
```

### MySQL Configuration

#### Connection String Format

```
mysql_user:mysql_password@tcp(host:port)/database?charset=utf8mb4&parseTime=True
```

#### Configuration Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| DB_HOST | localhost | Database host address |
| DB_PORT | 3306 | Database port number |
| DB_USER | root | Database user |
| DB_PASSWORD | - | Database password |
| DB_NAME | pysaz_dev | Database name |
| DB_CHARSET | utf8mb4 | Character set |

### Database Migrations

#### Running Migrations

```bash
# Run all pending migrations
go run cmd/main.go migrate up

# Rollback last migration
go run cmd/main.go migrate down

# Check migration status
go run cmd/main.go migrate status
```

#### Creating New Migrations

```bash
# Generate a new migration file
go run cmd/main.go migrate create create_users_table

# Edit the generated file in migrations/ directory
# Then run: go run cmd/main.go migrate up
```

#### Migration File Structure

```sql
-- migrations/001_initial_schema.sql
-- Users table
CREATE TABLE users (
    id BIGSERIAL PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_deleted_at ON users(deleted_at);
```

## API Endpoints

### Health Check

```
GET /health
```

Response:

```json
{
  "status": "healthy",
  "timestamp": "2025-12-23T15:59:45Z",
  "uptime": "1h30m45s"
}
```

### Authentication

```
POST /api/v1/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123"
}
```

### User Management

```
GET    /api/v1/users                    # List all users
POST   /api/v1/users                    # Create new user
GET    /api/v1/users/:id                # Get user by ID
PUT    /api/v1/users/:id                # Update user
DELETE /api/v1/users/:id                # Delete user
```

## Development

### Running Tests

```bash
# Run all tests
go test ./...

# Run with coverage
go test -cover ./...

# Run specific test package
go test ./pkg/service/...

# Run tests with verbose output
go test -v ./...
```

### Code Formatting

```bash
# Format all Go files
go fmt ./...

# Run linter
golangci-lint run

# Run vet
go vet ./...
```

### Building Locally

```bash
# Build for current OS
go build -o bin/pysaz cmd/main.go

# Build for specific OS
GOOS=linux GOARCH=amd64 go build -o bin/pysaz-linux cmd/main.go
GOOS=darwin GOARCH=amd64 go build -o bin/pysaz-macos cmd/main.go
GOOS=windows GOARCH=amd64 go build -o bin/pysaz.exe cmd/main.go
```

## Testing

### Unit Tests

```bash
go test -run TestUserService ./pkg/service/...
```

### Integration Tests

```bash
go test -run Integration ./tests/integration/...
```

### Test Coverage Report

```bash
go test -cover -coverprofile=coverage.out ./...
go tool cover -html=coverage.out
```

## Deployment

### Docker Deployment

```bash
# Build Docker image
docker build -t pysaz:latest .

# Run container
docker run -p 8080:8080 --env-file .env pysaz:latest

# Using Docker Compose
docker-compose -f docker-compose.yml up -d
```

### Environment Variables for Production

```env
SERVER_ENV=production
LOG_LEVEL=warn
DB_SSL_MODE=require
JWT_SECRET=your_secure_secret_key
SERVICE_TIMEOUT=60s
```

## Contributing

### Code Standards

1. Follow Go conventions and best practices
2. Write tests for new features (maintain >80% coverage)
3. Use meaningful commit messages
4. Keep functions small and focused
5. Add documentation for public functions

### Pull Request Process

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Code Review Guidelines

- All PRs require at least one approval
- Tests must pass before merging
- Code coverage should not decrease
- Documentation should be updated

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For issues, questions, or suggestions:

- **GitHub Issues**: [Open an Issue](https://github.com/arya237/PYSAZ/issues)
- **Documentation**: See `/docs` directory for detailed guides
- **Email**: Contact project maintainers

## Acknowledgments

- Built with [Go](https://golang.org/)
- Database: [PostgreSQL](https://www.postgresql.org/) / [MySQL](https://www.mysql.com/)
- ORM: [GORM](https://gorm.io/)

---

**Last Updated**: December 23, 2025

**Version**: 1.0.0

**Status**: Active Development
