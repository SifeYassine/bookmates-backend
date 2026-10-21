# BookMates Backend

The **Laravel 11 REST API** behind **BookMates**, a book-exchange platform where readers list books they own, post offers to swap them for books they want, bookmark interesting offers, send exchange requests, and rate each other. Authentication uses **JWT** (`tymon/jwt-auth`).

> Frontend: [bookmates-frontend](https://github.com/SifeYassine/bookmates-frontend)

## Features

- **JWT authentication**: register, login, logout, token refresh, and current-user endpoint
- **Location-aware users**: latitude, longitude, city, and country stored at sign-up
- **Roles**: `Admin` and `User` are created automatically, and the **first registered user becomes Admin**
- **Books**: title, author, description, ISBN, page count, published year, genre, and an uploaded **cover image**
- **Genres** management
- **Book posts (exchange offers)**: an *offered* book in exchange for a *wished* book
- **Bookmarks** to save book posts
- **Exchange requests** with status `pending` / `accepted` / `declined`
- **User ratings** (rater → ratee)
- **Auto-generated API docs** via [laravel-request-docs](https://github.com/rakutentech/laravel-request-docs)

## Tech Stack

- PHP 8.2+, Laravel 11
- tymon/jwt-auth
- MySQL (or SQLite)
- rakutentech/laravel-request-docs

## Data Model

| Table               | Key fields |
| ------------------- | ---------- |
| `users`             | `name`, `email`, `password`, `latitude`, `longitude`, `city`, `country`, `role_id` |
| `roles`             | `name` |
| `genres`            | `name` |
| `books`             | `title`, `author`, `description`, `cover_image`, `page_count`, `published_year`, `isbn`, `user_id`, `genre_id` |
| `book_posts`        | `offerer_id`, `offeredBook_id`, `wishedBook_id` |
| `bookmarks`         | `user_id`, `bookPost_id` |
| `exchange_requests` | `requester_id`, `bookPost_id`, `status`, `notification_sent` |
| `ratings`           | `rater_id`, `ratee_id`, `rating` |

## Getting Started

### Prerequisites

- PHP 8.2+ and [Composer](https://getcomposer.org/)
- MySQL (or SQLite)

### Installation

```bash
git clone https://github.com/SifeYassine/bookmates-backend.git
cd bookmates-backend

composer install
cp .env.example .env
php artisan key:generate
php artisan jwt:secret
```

### Configure the database

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=bookmates
DB_USERNAME=root
DB_PASSWORD=
```

### Migrate and link storage

```bash
php artisan migrate
php artisan storage:link   # serves uploaded book covers from storage/app/public
```

### Run

```bash
php artisan serve
```

- API base URL: `http://127.0.0.1:8000/api`
- API docs: `http://127.0.0.1:8000/request-docs`

## API Overview

All routes are prefixed with `/api`. Except for `register` and `login`, send `Authorization: Bearer <jwt>`.

### Auth

| Method | Endpoint         | Description |
| ------ | ---------------- | ----------- |
| POST   | `/auth/register` | `name`, `email`, `password`, optional `latitude`, `longitude`, `city`, `country` |
| POST   | `/auth/login`    | Returns a JWT |
| POST   | `/auth/logout`   | Invalidates the token |
| POST   | `/auth/refresh`  | Issues a fresh token |
| POST   | `/auth/me`       | Current user |

### Resources

| Resource            | Endpoints |
| ------------------- | --------- |
| `roles`             | `create`, `index`, `update/{id}`, `delete/{id}` |
| `genres`            | `create`, `index`, `update/{id}`, `delete/{id}` |
| `books`             | `create` (multipart, `cover_image`), `index`, `update/{id}` (**POST**, multipart), `delete/{id}` |
| `book_posts`        | `create`, `index`, `show/{id}`, `update/{id}`, `delete/{id}` |
| `bookmarks`         | `create`, `index`, `delete/{id}` |
| `exchange_requests` | `create`, `index`, `update/{id}`, `delete/{id}` |
| `ratings`           | `create`, `index`, `update/{id}`, `delete/{id}` |

HTTP verbs follow the convention `POST create`, `GET index/show`, `PUT update`, `DELETE delete`. Book updates are the exception and use `POST` so files can be uploaded.

## Related Repositories

- [bookmates-frontend](https://github.com/SifeYassine/bookmates-frontend): Vue.js frontend

## Author

**Sifeddine Yassine** · [@SifeYassine](https://github.com/SifeYassine)
