# Before start
```bash
- Server installed rsync.
- Key /home/your_username/.ssh/id_rsa must be remote without password to the server.
```


# Raspberry pi 400

Backup folder `your_username@10.10.10.10:/Volumes` (server) -> `/backup_drive` (local)
```bash
docker run -d --name rsync \
  --volume "/backup_drive":"/backup" \ # replace by your backup folder
  --volume "/home/your_username/.ssh":"/root/.ssh" \
  --env REMOTE_HOSTNAME="10.10.10.10" \ # replace by your server ip
  --env SSH_IDENTITY_FILE="/root/.ssh/id_rsa" \ # private key ssh
  --env BACKUPDIR="your_username@10.10.10.10:/Volumes" \
  --env ARCHIVEROOT="/backup" \
  --env SSH_PORT=22 \
  --env CRON_TIME="0 */3 * * *" \ # every 3 hours
  cullen2205/rsync:pi400
```

The container can then be stopped with 
```bash
docker stop rsync && docker rm rsync
```

Log
```bash
docker logs -f rsync
```

## Environment variables

The backup can be configured using the environment variables, as show in the
examples. Here is a full list of the variables, default values and uses.

    REMOTE_HOSTNAME (""): Server being backed up. SSH host keys for this will be
      scanned and added to known_hosts. For the actual backup, set BACKUPDIR.
    BACKUPDIR ("/home"): Directory path to be archived. Usually remote or a
      mounted volume.
    SSH_PORT ("22"): Change if a non-standard SSH port number is used.
    SSH_IDENTITY_FILE ("/root/.ssh/id_rsa"): Change to use a key mounted from
      the host.
    ARCHIVEROOT ("/backup"): It's good to mount a volume at this path. A folder
      structure like this will be created:
        /backup
        ├── 2017-11-06 #Incremental backup for each day
        ├── 2017-11-07
        ├── 2017-11-08
        └── main # The latest backup, full
    EXCLUDES (""): Semicolon separated list of exclude patterns. Use the format
      described in the FILTER RULES section of the rsync man page. A limitation
      is that semicolon may not be present in any of the patterns.
    CRON_TIME ("0 1 * * *"): The time to do backups. The default is at 01:00
      every night.


# Support

Add a [GitHub issue](https://github.com/jswetzen/docker-rsync-backup/issues).
