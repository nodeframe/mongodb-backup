# mongodb-backup [![Docker Repository on Quay](https://quay.io/repository/nodeframe/mongo-db-backup/status "Docker Repository on Quay")](https://quay.io/repository/nodeframe/mongo-db-backup)

This image runs mongodump to backup data using cronjob to folder `/backup`

## Usage:

    docker run -d \
        --env MONGODB_HOST=db \
        --env MONGODB_PORT=27017 \
        --env MONGODB_USER=admin \
        --env MONGODB_PASS=password \
        --link mongo_db_container:db
        --volume host.folder:/backup
        quay.io/nodeframe/mongo-db-backup

Moreover, if you link `quay.io/nodeframe/mongodb-backup` to a mongodb container(e.g. `mongodb:3.2`) with an alias named mongodb, this image will try to auto load the `host`, `port`, `user`, `pass` if possible.

### Secondary Sotrage:
If you have a use case such as a secondary storage driver, i.e. [Convoy](http://rancher.com/introducing-convoy-a-docker-volume-driver-for-backup-and-recovery-of-persistent-data/) etc.
You can set the flag `SECONDARY_STORAGE_PATH`. The backup script will simply moved the backup script file into the designate folder.

## Parameters

    MONGODB_HOST    the host/ip of your mongodb database
    MONGODB_PORT    the port number of your mongodb database
    MONGODB_USER    the username of your mongodb database. If MONGODB_USER is empty while MONGODB_PASS is not, the image will use admin as the default username
    MONGODB_PASS    the password of your mongodb database
    MONGODB_DB      the database name to dump. If not specified, it will dump all the databases
    EXTRA_OPTS      the extra options to pass to mongodump command
    CRON_TIME       the interval of cron job to run mongodump. `0 0 * * *` by default, which is every day at 00:00
    MAX_BACKUPS     the number of backups to keep. When reaching the limit, the old backup will be discarded. No limit, by default
    INIT_BACKUP     if set, create a backup when the container launched
    SECONDARY_STORAGE_PATH if set, the backup will copy the backup file to the this path location after finishing the backup

## Restore from a backup

See the list of backups, you can run:

    docker exec mongodb-backup ls /backup

To restore database from a certain backup, simply run:

    docker exec mongodb-backup /restore.sh /backup/2015.08.06.171901
