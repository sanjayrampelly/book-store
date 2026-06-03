# Book Store & Blog (FastAPI sandbox)

Two small FastAPI applications in one repository, used as a learning ground
for Pydantic v2 validation, SQLAlchemy ORM, dependency injection, and
password hashing.

## What's inside

### 1. `main.py` — in-memory Book API

A no-database CRUD service over a list of `Book` objects, focused on
**Pydantic v2 field validation** (`@field_validator`, `Field(min_length=...)`,
custom rules like "title must start with a capital letter" and "price must
be between ₹1 and ₹10,000").

Endpoints:

| Method | URL                    | Action                                |
| ------ | ---------------------- | ------------------------------------- |
| `GET`  | `/`                    | Hello world                           |
| `GET`  | `/greet`               | JSON greeting                         |
| `GET`  | `/books`               | List books                            |
| `GET`  | `/books/{book_id}`     | Retrieve a book                       |
| `POST` | `/book`                | Create a book (with full validation)  |
| `GET`  | `/jsonBooks`           | Return books as JSON strings          |

### 2. `blog/` — persistent Blog + Users API

A separate FastAPI app inside the `blog/` package that introduces
**SQLAlchemy 2.0**, **PostgreSQL**, and **bcrypt** password hashing.

Endpoints (under `blog.main:app`):

| Method   | URL              | Action                  |
| -------- | ---------------- | ----------------------- |
| `GET`    | `/`              | Ping                    |
| `POST`   | `/blog`          | Create a blog post      |
| `GET`    | `/blog`          | List all blog posts     |
| `GET`    | `/blog/{id}`     | Retrieve a post by id   |
| `PUT`    | `/blog/{id}`     | Update a post           |
| `DELETE` | `/blog/{id}`     | Delete a post           |
| `POST`   | `/user`          | Create a user (bcrypt)  |
| `GET`    | `/user`          | List users              |
| `GET`    | `/user/{id}`     | Retrieve a user         |

Schemas use Pydantic v2 (`from_attributes = True`); responses for users go
through `ShowUser` so the password is never returned.

## Tech stack

- Python 3.10+
- FastAPI 0.116, Uvicorn
- Pydantic 2.x
- SQLAlchemy 2.0 (only for the `blog/` app)
- PostgreSQL via `psycopg2-binary` (only for the `blog/` app)
- `passlib[bcrypt]` for hashing

## Setup

```bash
python -m venv env
.\env\Scripts\activate
pip install -r requirements.txt
```

### Running the in-memory book API

```bash
uvicorn main:app --reload
# -> http://127.0.0.1:8000/docs
```

### Running the blog API

```bash
cd blog
cp .env.example .env             # set DATABASE_URL
uvicorn main:app --reload
# -> http://127.0.0.1:8000/docs
```

The blog app reads `DATABASE_URL` from `blog/.env`. Default expectation is a
local Postgres at `localhost:5432` with a `BlogDB` database.

## Project structure

```text
book-store/
├── main.py                # in-memory Book API
├── blog/                  # persistent Blog + User API (separate FastAPI app)
│   ├── main.py
│   ├── models.py          # SQLAlchemy Blog / User
│   ├── schemas.py         # Pydantic schemas
│   ├── database.py        # engine, SessionLocal, get_db
│   └── utils/hashing.py   # bcrypt helpers
└── requirements.txt
```

## Notes

This repo is intentionally a two-app sandbox so I can compare patterns side
by side (in-memory vs ORM, raw vs hashed passwords, basic vs response_model).
A future split into two separate repos would make sense if this grew further.
