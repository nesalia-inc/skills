---
title: Project Architecture
description: How the project is structured
---

# Project Architecture

## Overview

This project uses a **microservices architecture** consisting of 5 services.

## Services

| Service | Purpose |
|---------|---------|
| `auth` | Authentication and authorization |
| `api-gateway` | API routing and request handling |
| `users` | User management |
| `orders` | Order processing and management |
| `inventory` | Inventory tracking and management |

## Data Layer

| Technology | Purpose |
|------------|---------|
| **PostgreSQL** | Main relational database for persistent data storage |
| **Redis** | Caching layer for performance optimization |

### Database Configuration

- **PostgreSQL**: Primary data store for all services
- **Redis**: Session cache and query caching