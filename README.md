# 🏠 PG Management System

A full-featured **Paying Guest (PG) / Hostel Management Backend API** built with **Django REST Framework**. This system helps PG owners manage rooms, tenants, billing, and access control through a secure, token-authenticated REST API.

---

## 📋 Table of Contents

- [About the Project](#about-the-project)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Features](#features)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Environment Setup](#environment-setup)
  - [Running the Server](#running-the-server)
- [API Overview](#api-overview)
- [Admin Panel](#admin-panel)
- [Authentication](#authentication)
- [Environment Variables](#environment-variables)
- [Contributing](#contributing)

---

## 📖 About the Project

The **PG Management System** is a backend REST API designed to digitize and streamline the day-to-day operations of a Paying Guest accommodation. It handles everything from room inventory and tenant onboarding to electricity billing and access management — all through a clean API layer with JWT-based authentication.

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| **Language** | Python 3.x |
| **Framework** | Django + Django REST Framework |
| **Authentication** | JWT (via `rest_framework_simplejwt`) |
| **Database** | SQLite (Development) |
| **API Server** | ASGI (via `asgi.py`) |
| **Admin Panel** | Django Admin |

---

## 📁 Project Structure

```
pg_management/
│
├── masterapp/                  # Core application module
│   ├── migrations/             # Database migration files
│   ├── __init__.py
│   ├── admin.py                # Admin panel registrations
│   ├── apps.py                 # App configuration
│   ├── models.py               # Database models
│   ├── serializers.py          # DRF serializers
│   ├── tests.py                # Unit tests
│   └── views.py                # API views & business logic
│
├── pg_managementPRD/           # Project settings package
│   ├── __pycache__/
│   ├── __init__.py
│   ├── asgi.py                 # ASGI configuration
│   ├── settings.py             # Django settings
│   ├── urls.py                 # Root URL configuration
│   └── wsgi.py                 # WSGI configuration
│
├── staticfiles/                # Collected static files
│   ├── admin/
│   └── rest_framework/
│
├── venv/                       # Python virtual environment (not committed)
├── .env                        # Environment variables (not committed)
├── .gitignore
├── create_superuser.py         # Script to create Django admin superuser
├── db.sqlite3                  # SQLite database (not committed)
├── manage.py                   # Django management CLI
├── README.md
├── render.yaml                 # Render.com deployment config
└── requirements.txt            # Python dependencies
```

---

## ✨ Features

### Room Management
- Add, update, and delete rooms
- Room types: **Single**, **Double**, **Triple**, **Dormitory**
- AC / Non-AC classification
- Track room **capacity**, **occupancy**, and **full status**
- Facility tagging per room (e.g., `attach-late`, etc.)

### Tenant Management
- Register and manage tenants
- Assign tenants to specific rooms
- Maintain tenant history (`Tenant_historys`)

### Billing & Utilities
- Electricity bill tracking per tenant/room
- Access type and login type master tables for configuration

### Authentication & Security
- JWT Access Tokens (configurable expiry, default: 6000 minutes)
- JWT Refresh Tokens (configurable, default: 7 days)
- Token Blacklisting on logout
- Outstanding token tracking
- Role-based access via User Type Masters

### Admin Panel
- Full Django Admin interface
- Filter rooms by type (Single/Double/Triple/Dormitory) and AC status
- Manage all models from a clean UI

---

## 🚀 Getting Started

### Prerequisites

Make sure you have the following installed:

- Python 3.8 or higher
- pip
- Git

### Installation

**1. Clone the repository**

```bash
git clone https://github.com/albenusmurmu/PG_MANAGEMENT.git
cd PG_MANAGEMENT
```

**2. Create and activate a virtual environment**

```bash
# On Windows
python -m venv venv
venv\Scripts\activate

# On macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

**3. Install dependencies**

```bash
pip install -r requirements.txt
```

### Environment Setup

Create a `.env` file in the root directory of the project:

```env
SECRET_KEY=your_django_secret_key_here
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

JWT_ACCESS_MINUTES=6000
JWT_REFRESH_DAYS=7
```

> ⚠️ **Never commit your `.env` file.** It is already listed in `.gitignore`.

To generate a secure Django `SECRET_KEY`, run:

```bash
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

### Running the Server

**1. Apply database migrations**

```bash
python manage.py migrate
```

**2. Create a superuser (Admin)**

```bash
python create_superuser.py
# OR
python manage.py createsuperuser
```

**3. Collect static files**

```bash
python manage.py collectstatic
```

**4. Start the development server**

```bash
python manage.py runserver
```

The server will start at: `http://127.0.0.1:8000/`

---

## 🔌 API Overview

All API endpoints are prefixed based on the URL configuration in `pg_managementPRD/urls.py`.

### Authentication Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/token/` | Obtain JWT access & refresh tokens |
| POST | `/api/token/refresh/` | Refresh an expired access token |
| POST | `/api/token/blacklist/` | Blacklist a token (logout) |

### Room Endpoints (example)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/rooms/` | List all rooms |
| POST | `/api/rooms/` | Create a new room |
| GET | `/api/rooms/{id}/` | Retrieve a specific room |
| PUT | `/api/rooms/{id}/` | Update a room |
| DELETE | `/api/rooms/{id}/` | Delete a room |

### Tenant Endpoints (example)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/tenants/` | List all tenants |
| POST | `/api/tenants/` | Register a new tenant |
| GET | `/api/tenants/{id}/` | Get tenant details |
| PUT | `/api/tenants/{id}/` | Update tenant info |
| DELETE | `/api/tenants/{id}/` | Remove a tenant |

> 📝 All protected endpoints require the `Authorization: Bearer <access_token>` header.

---

## 🖥 Admin Panel

Access the Django Admin interface at:

```
http://127.0.0.1:8000/admin/
```

Log in with the superuser credentials you created. The admin panel provides management for:

**MASTERAPP**
- Access Type Masters
- Electricity Bills
- Facilities
- Login Type Masters
- Rooms (with filters by Room Type & AC/Non-AC)
- Tenant Historys
- Tenants
- User Type Masters

**AUTHENTICATION AND AUTHORIZATION**
- Groups
- Users

**TOKEN BLACKLIST**
- Blacklisted Tokens
- Outstanding Tokens

---

## 🔐 Authentication

This project uses **JSON Web Tokens (JWT)** for stateless authentication via the `djangorestframework-simplejwt` library.

**Workflow:**

```
1. User sends credentials → POST /api/token/
2. Server returns access_token + refresh_token
3. Client includes access_token in Authorization header for protected requests
4. When access_token expires → POST /api/token/refresh/ with refresh_token
5. On logout → POST /api/token/blacklist/ to invalidate the token
```

**Token Lifetimes** (configurable in `.env`):

| Token | Default |
|-------|---------|
| Access Token | 6000 minutes (~4 days) |
| Refresh Token | 7 days |

---

## ⚙️ Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `SECRET_KEY` | Django secret key | `7!h+*u%4)hefh2si@5)3w...` |
| `DEBUG` | Debug mode flag | `True` / `False` |
| `ALLOWED_HOSTS` | Comma-separated allowed hosts | `localhost,127.0.0.1` |
| `JWT_ACCESS_MINUTES` | Access token expiry in minutes | `6000` |
| `JWT_REFRESH_DAYS` | Refresh token expiry in days | `7` |

---

## 🤝 Contributing

Contributions are welcome! To contribute:

1. Fork the repository
2. Create a new branch: `git checkout -b feature/your-feature-name`
3. Make your changes and commit: `git commit -m "Add your feature"`
4. Push to your branch: `git push origin feature/your-feature-name`
5. Open a Pull Request

---

## 📄 License

This project is open source. Feel free to use, modify, and distribute it.

---

> Built with ❤️ using Django REST Framewor
