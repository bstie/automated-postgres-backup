# Automated PostgreSQL-Backups

## Sources:

- https://wiki.postgresql.org/wiki/Automated_Backup_on_Linux
- https://serverfault.com/a/734159


## Steps

```bash
# create the scripts
mkdir /opt/pg_backup
cd /opt/pg_backup
wget https://raw.githubusercontent.com/bstie/automated-postgres-backup/master/src/pg_backup.config
wget https://raw.githubusercontent.com/bstie/automated-postgres-backup/master/src/pg_backup.sh
wget https://raw.githubusercontent.com/bstie/automated-postgres-backup/master/src/pg_backup_rotated.sh
chmod +x pg_backup.sh pg_backup_rotated.sh
# create destination folder
mkdir /home/deploy/postgres-backups
chown postgres:deploy /home/deploy/postgres-backups

```

```bash
# add password for postgres and add .pgpass if not already done
su - postgres
psql

$ ALTER USER postgres WITH PASSWORD 'new_password';
$ exit
```

Then create a .pgpass file in the use system home who's running the script !
```
echo '*:*:*:postgres:new_password' > ~/.pgpass
chmod 0600 ~/.pgpass
```

Create crontab (as postgres user)

```bash
crontab -e
```

Add the following line:
```crontab
0 2 * * * /opt/pg_backup/pg_backup_rotated.sh
```

Test backup (as postgres user)
```
/opt/pg_backup/pg_backup_rotated.sh
```