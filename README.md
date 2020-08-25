# Automated PostgreSQL-Backups

## Sources:

- https://wiki.postgresql.org/wiki/Automated_Backup_on_Linux
- https://serverfault.com/a/734159


## Prerequisites

This guide assumes that there are two users:

- `deploy`: non root user that runs the application that uses the DB
- `postgres`: the db user that runs the backup scripts

The commands are executed in a shell that does not need sudo, so maybe you have to preceed some of the commands with sudo.

## Steps

```bash
# create folder for scripts and cd into it
mkdir /opt/pg_backup
cd /opt/pg_backup
# download the scripts
wget https://raw.githubusercontent.com/bstie/automated-postgres-backup/master/src/pg_backup.config
wget https://raw.githubusercontent.com/bstie/automated-postgres-backup/master/src/pg_backup.sh
wget https://raw.githubusercontent.com/bstie/automated-postgres-backup/master/src/pg_backup_rotated.sh
chmod +x pg_backup.sh pg_backup_rotated.sh
# create destination folder
mkdir /home/deploy/postgres-backups
# set owner "postgres" and owner group "deploy", so that postgres 
# and deploy user can read/write in this directory.
chown postgres:deploy /home/deploy/postgres-backups
```

Add password for postgres and add .pgpass if not already done
```bash
# open shell as postgres user
su - postgres
# open psql cli client
psql
# add password 
ALTER USER postgres WITH PASSWORD 'new_password';
# exit psql
exit
```

Then create a .pgpass file in the home directory of the user who's running the script!
```bash
echo '*:*:*:postgres:new_password' > ~/.pgpass
# change permissions
chmod 0600 ~/.pgpass
```

Create crontab (as postgres user)
```bash
crontab -e
```

Add the following line:
```bash
0 2 * * * /opt/pg_backup/pg_backup_rotated.sh
```

Test backup (as postgres user)
```bash
/opt/pg_backup/pg_backup_rotated.sh
```