# postgresql basic

install postgresql package on AUR repo.
for configuration first we should create a new database cluster; this is for initilizing database required storage area on disk.

and also a database cluster is a set of databased that managed by a single instance of running database server. after intalling and configuring cluster properly, the cluster will contain a databse named `postgres` which used for utilities, users and thirthy party libraries.

In file system terms, a database cluster is a single directory under which all data will be stored. this directory called `data directory` or `data area`. for initilizing database cluster we can use `intidb` command and desired databsae location indicated by `-D` option, for example: `initdb -D /usr/local/pgsql/data`

#### Extera Tip:

     As an alternative to the -D option, you can set the environment variable PGDATA.

and as documentation say this directory should be owned by `postgres` user, so: `chown /usr/local/pgql/data postgres`
and also keep in mind that if we change the root to anything other than `/var/lib/postgres` we should edit the `PIDFile` section in service file.

### Create first database/user

#### TIP:

    If you create a PostgreSQL user with the same name as your Linux username, it allows you to access the PostgreSQL database shell without having to specify a user to login (which makes it quite convenient).

for creating user and database we can use `createuser --interactive` and `crreatedb dbName` commands

## shell

start primary database shell, `psql` where we can do all `create` user and database and set permissions and run raw SQL commands. using `-d` option, postgres will try to connect a database wich match with this username.

- `\c <database>` connect to database
- `\du` list all users and thier permission
- `\dt` show summary of all tables in current database

## Optional Configurations

the postgres databse server configuration file is `postgresql.conf` which located in cluster path. this folder house other main configuration files, including the `pg_hba.conf` which defind authentication and setting for both `local` and `other hosts` users.
the default `pg_hba.conf` file allow any local user to access any databaes. we can change it by editing file and for row `local` change user from `all` ==> `postgres` and method from `trust` ==> `peer`

## setting up master-slave replication

in master and slave mode we have two role, `master` which take all writes and `slave` which take read-only queries. postgres uses WAL shipping based replication. WAL is stand for `Write-Ahead Loggin`, which is a log file contain all modifications to the database before applying them.WAL is used for backup when database crash. also WAL used for keep databse atomicity and durablity.
the WAL file is created in master node and will shipped to keep databases in sync. master can directly copy or just share it with other standby servers. a WAL log file can contain only up to 16MB, and it's shipped only after reaches to that threshold. this will cause delay and increase chance of losing data if master crash and log data did not archived.

### streaming WAL records

server send WAL record chunks to slaves due to keep them in sync. WAL records are streaming as they are generated. the streaming of WAL records need no wait for the WAL file to be filled. **By Default,streaning replication is asynchronous even though it also support synchronous replication**

#### configuring on master node

- initilize master node with initdb utility
- create a role/user to have replication privileges by running this command: `CREATE USER <user_name> REPLICATION LOGIN ENCRYPTED PASSWORD <password>`;
- configure streaming related properties on master postgresql - configuration files as follow:
