
# INFO about the phpipam installation

When the containers run, we can access the phpipam installation via the URL: *<host_IP:8080>*

To install a fresh instance of phpipam, click on **"New phpipam installation"**. Easiest option is to allow phpipam installer to create database for you automatically. Here we must us the root username and root password (set as *MYSQL_ROOT_PASSWORD* env variable in the compose file). Any other user created in maridb will not have permissions by default to create databases. These permissions must be given explicitly inside the mariabdb container. Automatically a phpipam user with the password provided (set as *IPAM_DATABASE_PASS* env variable in the compose file)  will then be created in mariab. This can be checked from within the mariadb container. Click on continue.

Set an Admin password for the UI. The default user will be admin. Click on "Proceed to login". Login using user *admin* user and password set previously.

After the initial installation you will be advised to disable installation scripts.  This can be done in 2 ways:
1. Disable via Docker environment variable (Easier)
   Add the env variable *IPAM_DISABLE_INSTALLER=1*  to your `phpipam-web` service in the compose file. Then restart the containers:
   ```
     docker compose up -d
     ```
2. Disable via `config.php`
   Inside the phpipam container edit the `/phpipam/config.php` file by updating it:`disable_install=true`. Restart of the containers is not required.

# INFO about compose file

- The user provided in the the environment variable - *IPAM_DATABASE_USER* will automatically be created once an initial database is created using the phpipam installation mentioned above. If  no user is provided then by default *phpipam* usr will be created. Hence, creating an user in mariadb is not needed ssing the env variables:
    - *MYSQL_USER*
    - *MYSQL_PASSWORD*
- Initially I tried to set the root password by the env variable (I did not know better) *MYSQL_ROOT_PASSWORD_=/run/secrets/db_root_password*.  But this keep on using the entire string as the password */run/secrets/db_root_password* and did not recognize I am passing a file. So i make a workaround by:
  ```
  command: >
    bash -c "
    export MYSQL_ROOT_PASSWORD=$(cat /run/secrets/db_root_password) &&
    exec docker-entrypoint.sh mariadbd
    "
   ```
   Later i discovered there is another variable that can be used for fetching secrets from file: *MYSQL_ROOT_PASSWORD_FILE*. So the workaround was not needed anymore, but it wasgood to know it.