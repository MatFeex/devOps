# Use the PostgreSQL 14.1 image based on Alpine Linux as the base image.
FROM postgres:14.1-alpine

# Set environment variables for the PostgreSQL container.
# - POSTGRES_DB: Name of the database to be created.
# - POSTGRES_USER: Username for the database.
# - POSTGRES_PASSWORD: Password for the database user.
ENV POSTGRES_DB=db \
    POSTGRES_USER=usr \
    POSTGRES_PASSWORD=pwd

# Copy SQL scripts to the /docker-entrypoint-initdb.d/ directory.
# These scripts will be executed when the PostgreSQL container is started.
COPY CreateScheme.sql /docker-entrypoint-initdb.d/
COPY InsertData.sql /docker-entrypoint-initdb.d/
