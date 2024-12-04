# Postgresql Server with TLS

The container use the latest version of the postgres image. Changes to the configuration are made in the `docker-compose.yml` file.
The configuration of the TLS part of the server that is not in the container goes as follow:

## Get certificates

1. Install tools:
   ```bash
   sudo nala update
   sudo nala install certbot
   ```

2. Verify that the firewall is up and running and port 80 is open:
   ```bash
   sudo ufw status
   ```
   If not, run:
   ```bash
   sudo ufw allow 80
   ```

   Don't forget to remove port 80 when you're done if the server is not exposed to the internet.

3. Get the certificates:
   ```bash
   sudo certbot certonly --standalone -d yourdomain.com
   ```

   You will be asked for an email and a few questions.

4. Create the folder structure:
   ```bash
   sudo  mkdir -p </path/to/your/postgres/folder>/certs
   ```

5. Copy the certificates:
   ```bash
   sudo cp /etc/letsencrypt/live/yourdomain.com/fullchain.pem </path/to/your/postgres/folder>/certs/server.crt
   sudo cp /etc/letsencrypt/live/yourdomain.com/privkey.pem </path/to/your/postgres/folder>/certs/server.key
   ```

## Configure the server

Modify the docker-compose.yml file to add the following environment variables:
```yaml
    environment:
    - POSTGRES_PASSWORD=yourpassword
    - POSTGRES_USER=yourusername
    - POSTGRES_DB=yourdatabase
    volumes:
    - /path/to/your/postgres/folder/certs:/certs
    - /path/to/your/postgres/folder/data:/var/lib/postgresql/data
    command: [
      "postgres",
      "-c", "ssl=on",
      "-c", "ssl_cert_file=/certs/server.crt",
      "-c", "ssl_key_file=/certs/server.key"
    ]

## Run the container to initialize the database
```bash
docker-compose up
```

It will show an error message like this:
```
"FATAL: could not load private key file '/certs/server.key': Permission denied"
```

Thats because the permissions of the files are not correct.
We need to change the permissions of the files and the folder, but in order to do that we need the container user id in the container.
```bash
docker run --rm postgres:latest id -u postgres
```

Take note of the output, it's an integer.

## Change ownership and permissions of the files and folders
```bash
sudo chown -R <ID>:<ID> </path/to/your/postgres/folder>
sudo chmod -R 600 </path/to/your/postgres/folder>/certs/server.key
```

## Run the container again (now with -d flag)
```bash
docker-compose up -d
```

## Test the connection with TLS

Use some tool like pgAdmin to test the connection.
In the **Parameters** tab, select **SSL mode** and change to **require**.
    

## IMPORTANT NOTE
If you want to change the user and/or password of the database, you need to not only delete the container and recreate it, but also delete the data folder.
