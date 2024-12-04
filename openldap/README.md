# docker-openldap

## Instructions

1. Create permanent volumes routes:
    ```bash
    sudo mkdir -p ldap/data
    ```

2. Create certificates:
   
   a. Create the certificate authority:

      ```bash
      # Generate a private key
      openssl genpkey -algorithm RSA -out ldap-server.key -pkeyopt rsa_keygen_bits:2048

      # Generate a CSR
      openssl req -new -key ldap-server.key -out ldap-server.csr
      ```

      During the CSR generation, you will be prompted to enter information such as country, state, and organization. Make sure the "Common Name" matches the hostname of your LDAP server.

   b. Generate a self-signed certificate

      ```bash
      openssl x509 -req -days 365 -in ldap-server.csr -signkey ldap-server.key -out ldap-server.crt
      ```
      This will create a self-signed certificate valid for 365 days.

   