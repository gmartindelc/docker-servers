# docker-postgres

Create a postgresql container

## Instructions

1. Create permanent volumes routes:

    ```bash
    sudo mkdir -p pgsql/data
     ```

2. Run docker compose:

    Using docker compose:

    ```bash
    docker compose -f postgres.yml up -d
    ```

    Using script:

    ```bash
    ./postgres-start.sh
    ```

4. Stop docker compose:

    Using docker compose:
    
    ```bash
    docker compose -f postgres.yml down
    ```
    Using script:
    ```bash
    ./postgres-stop.sh
    ```
### Volumes

* /pgsql/data

### Ports

* 5432

### Environment variables (Change this to your needs)

* POSTGRES_USER: postgres
* POSTGRES_PASSWORD: postgres
* POSTGRES_DB: postgres

### Connection to DB

*Connect*: postgres container    (suggestion, could be anything)

*database*: postgres

*user*: postgres

*password*: postgres

*port*: 5432
