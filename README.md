# Symfony Dockerized Application Scaffold

This project provides a comprehensive Dockerized environment scaffold for developing Symfony applications.

## Overview

The setup utilizes Docker Compose to orchestrate multiple services required for a typical modern web application. It includes containers for PHP, Apache, MariaDB, Redis, RabbitMQ, Elasticsearch, and Node.js.

## Services

The `compose.yaml` file defines the following services:

*   **`php`**: 
    *   Runs PHP 8.3.20-FPM on Alpine Linux.
    *   Builds from `docker/dev/php/Dockerfile`.
    *   Includes common PHP extensions: `memcached`, `apcu`, `redis`, `intl`, `opcache`, `pdo_mysql`, `zip`.
    *   Installs Composer (v2.8.8) and Xdebug.
    *   Mounts the `./symfony` directory to `/var/www/html` inside the container.
    *   Container Name: `app-php-dev`

*   **`apache`**: 
    *   Uses the official `httpd:2.4.63` image.
    *   Serves the Symfony application's public directory (`./symfony/public`).
    *   Uses a custom Apache configuration (`./docker/base/apache/httpd.conf`).
    *   Depends on the `php` service.
    *   Exposes port 80.
    *   Container Name: `app-apache`

*   **`mariadb`**: 
    *   Uses the `mariadb:10.11.11` image.
    *   Provides the database service.
    *   Configured with environment variables for root password, database name, user, and password.
    *   Uses a named volume (`mariadb_data`) for persistent storage.
    *   Container Name: `app-mariadb`

*   **`adminer`**: 
    *   Uses the `adminer` image.
    *   Provides a web-based database management tool.
    *   Exposes port 8081 (maps to 8080 in the container).
    *   Container Name: `app-adminer`

*   **`node`**: 
    *   Uses the `node:22.14-alpine3.20` image.
    *   Intended for frontend tasks like asset building (e.g., running npm or yarn).
    *   Container Name: `app-node`

*   **`redis`**: 
    *   Uses the `redis:alpine` image.
    *   Provides an in-memory data structure store, often used for caching or session storage.
    *   Container Name: `app-redis`

*   **`rabbitmq`**: 
    *   Uses the `rabbitmq:4.0.8-alpine` image.
    *   Provides a message broker service.
    *   Exposes the RabbitMQ management UI port 15672.
    *   Container Name: `app-rabbitmq`

*   **`elasticsearch`**: 
    *   Uses the `docker.elastic.co/elasticsearch/elasticsearch:8.17.4` image.
    *   Provides a search and analytics engine.
    *   Configured for single-node discovery with security disabled.
    *   Container Name: `app-elasticsearch`

## Networking

All services are connected to a custom bridge network named `app_network`, allowing them to communicate with each other using their service names as hostnames.

## Volumes

*   **`./symfony`**: The main Symfony application code is mounted into the `php` and `apache` containers.
*   **`./docker/base/apache/httpd.conf`**: Custom Apache configuration is mounted into the `apache` container.
*   **`mariadb_data`**: A named volume is used to persist MariaDB data across container restarts.

## Getting Started

1.  **Prerequisites**: Ensure you have Docker and Docker Compose installed.
2.  **Clone the repository** (if applicable).
3.  **Build and start the containers**:
    ```bash
    docker compose build
    docker compose up -d
    ```
4.  **Access the application**: Open your web browser and navigate to `http://localhost` (or the appropriate IP/hostname if running Docker elsewhere).
5.  **Access Adminer**: Navigate to `http://localhost:8081`.
6.  **Access RabbitMQ Management**: Navigate to `http://localhost:15672`.

## Development

*   The PHP container (`app-php-dev`) includes Composer and Xdebug for development purposes.
*   You can execute commands inside containers using `docker compose exec <service_name> <command>`.
    *   Example (running composer install): `docker compose exec php composer install`
    *   Example (accessing bash in PHP container): `docker compose exec php bash`
*   The Node container (`app-node`) can be used for frontend asset management:
    *   Example: `docker compose run --rm node npm install`

## Stopping the Environment

To stop the containers:

```bash
docker compose down
```

To stop and remove volumes:

```bash
docker compose down -v