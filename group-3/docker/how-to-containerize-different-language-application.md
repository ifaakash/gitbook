---
description: >-
  This page include the list of important packages/files that are required to
  run any application
icon: docker
---

# How to containerize different language application

## Intro

This page include the details of the main files + packages, that are required to containerize and run any application. The content include the details to containerize below language application:

* **Node/Next.js**
* **Flask**
* **PHP**

## Creating image for next.js application

```docker
# Stage 1: Dependencies
FROM node:18-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci

# Stage 2: Builder
FROM node:18-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Stage 3: Runner
FROM node:18-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production

COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
COPY --from=builder /app/package*.json ./

EXPOSE 3000
CMD ["node", "server.js"]
```

**Must-have files:**

* `package.json` & `package-lock.json`
* `next.config.js` (or `.ts`)
* All your app code (`/pages`, `/app`, `/components`, etc.)
* `/public` folder (static assets)
* `.env.local` or `.env.production` (if needed)

## Creating image for Flask application

```docker
FROM python:3.11-slim

WORKDIR /app

# Copy and install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

EXPOSE 5000

# Use Gunicorn for production (better than flask dev server)
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "main:app"]
# OR for development:
# CMD ["python", "main.py"]
```

**Must-have files:**

* `requirements.txt` (with Flask + other deps)
* `main.py` (or `app.py` - your Flask app)
* Any templates folder (`/templates`)
* Any static files (`/static`)
* Config files if needed

**Pro tip:** For production, use `gunicorn` or `uwsgi` instead of Flask's built-in server!

## Creating image for PHP application

```docker
# Stage 1: Builder (Install dependencies)
FROM composer:2 AS builder
WORKDIR /app
COPY composer.json composer.lock ./
RUN composer install \
    --no-dev \
    --no-scripts \
    --no-autoloader \
    --prefer-dist

COPY . .
RUN composer dump-autoload --optimize --classmap-authoritative

# Stage 2: Runner (Production)
FROM php:8.2-fpm-alpine AS runner
WORKDIR /var/www/html

# Install PHP extensions if needed
RUN docker-php-ext-install pdo pdo_mysql

# Copy application from builder
COPY --from=builder /app /var/www/html

# Set permissions
RUN chown -R www-data:www-data /var/www/html

EXPOSE 9000
CMD ["php-fpm"]
```

## Quick Comparison

| Stack       | Dependency File      | Install Command                   | Runtime                        |
| ----------- | -------------------- | --------------------------------- | ------------------------------ |
| **Next.js** | `package*.json`      | `npm ci` → `npm run build`        | `node server.js`               |
| **Flask**   | `requirements.txt`   | `pip install -r requirements.txt` | `python main.py` or `gunicorn` |
| **PHP**     | `composer.json/lock` | `composer install`                | `php-fpm` or Apache            |

## dockerignore

### next.js

```
node_modules
.next
.git
```

### Flask

```
__pycache__
*.pyc
.env
venv
```

### PHP

```
vendor
.git
.env
```

