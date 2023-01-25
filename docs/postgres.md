# PostgreSQL
![PostgreSQL](./svgs/postgreql.svg "PostgreSQL")
*Big elephant database, everyone uses it so may as well get used to it ig.* 

### Installing on CentOS

[Here's the installation URL](https://www.postgresql.org/download/linux/redhat/)  
**Install the repository RPM:**

    sudo yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm

**Install PostgreSQL:**

    sudo yum install -y postgresql15-server

**Optionally initialize the database and enable automatic start:**

    sudo /usr/pgsql-15/bin/postgresql-15-setup initdb
    sudo systemctl enable postgresql-15
    sudo systemctl start postgresql-15

### Get all up in the data's business
`psql` to get into the database shell, might have to --   
`su postgres` first. By default postgresql adds a user named postgres with no password and all privileges.

### Commands you will forget for sure and have to look up on a cheat sheet lmao
[Good cheatsheet](https://postgrescheatsheet.com/#/databases)  
`\l` list databases  
`\du` shows all users and permissions  
`\c database-name` moves you into database  
`\dt` lists all tables in current schema (how things are categorized in db)  
`\d+ table-name` lists table deets  
`SHOW hba_file` shows the config file for postgres if you have sudo access  
`\password user` changes password for user if you have appropriate permissions
`\conninfo` shows the port the database is running on

    CREATE DATABASE mydb  

    CREATE TABLE IF NOT EXISTS sometable (
      id SERIAL PRIMARY KEY,
      name VARCHAR(50) NOT NULL,    
      phone NUMERIC(10) NOT NULL
    );

    INSERT INTO sometable (param1, param2) VALUES  
    
import schema using .sql file  

    psql -h hostname -d databasename -U username -f file.sql -L logfile.log
    psql -h jsonbournesupremacy -d jsonb -U postgres -f file.sql -L logfile.log

## DBeaver
The database solution for people who like free stuff. 

If all databases aren't appearing after connection try: 

right click on connection > Edit connnection > PostgreSQL tab > check "show all databases"
