
# docker-compose.yml

```
version: '3.9'

services:
  backend:
    build:
      context: ./app
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgresql://postgres:12345@db/appTime
    depends_on:
      - db
      - parser

  parser:
    build:
      context: ./parser
      dockerfile: Dockerfile
    environment:
      - DATABASE_URL=postgresql://postgres:12345@db/appTime
    depends_on:
      - db
    ports:
      - "8000:8000"

  celery_worker:
    build:
      context: ./parser
      dockerfile: Dockerfile.celery
    environment:
      - CELERY_BROKER_URL=redis://redis:6379/0
      - CELERY_RESULT_BACKEND=redis://redis:6379/0
      - DATABASE_URL=postgresql://postgres:12345@db/appTime
    depends_on:
      - db
      - redis

  db:
    image: postgres
    container_name: postgres_db
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=12345
      - POSTGRES_DB=appTime

  redis:
    image: redis:alpine
    container_name: redis

volumes:
  postgres_data:
```