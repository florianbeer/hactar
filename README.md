# hactar
An incremential daily backup script using rsync

Copyright (c)2015 by [Florian Beer](https://github.com/florianbeer)

Version 0.1


This script comes with ABSOLUTELY NO WARRANTY.
This is free software, and you are welcome to redistribute it
under certain conditions. See CC BY-NC-SA 4.0 for details.
https://creativecommons.org/licenses/by-nc-sa/4.0/

Usage: `hactar [OPTIONS] SOURCE TARGET`

## Explanation
Hactar is meant to be run daily, preferably when there is little load on your machine and rsyncs the contents of SOURCE to TARGET. Each day there will be a new subdirectory with the current date. Unchanged files will automatically be hardlinked to existing files to save diskspace.

Hactar sends a message tagged "hactar" to syslog stating SOURCE and TARGET on start and logs the total runtime when finished. This feature depends on `logger` and `python` being installed on your machine.

## Options
```
 -r NUM         number of days to keep old backups
 -e FILE        specify rsync excludes filename
 -v             increase verbosity
 -d             dry run
 -h             show this message
 -V             show version number
```

## Examples
Source or target can be a remote directory (via SSH) or a local path. See `rsync` manpage for details.
```
 hactar -v -e my_exludes / /backup
 hactar example.com:/home /backup
 hactar /var/www example.com:/mnt/backup
 hactar -r 7 /var/log /backup/logs
```

## Usage
* Save this shell script to e.g. `/usr/local/bin/hactar` and make it executable: `chmod +x /usr/local/bin/hactar`

* Create a new file called `/etc/hactar.excludes`and list all directories and/or filtermaps you don't need in your backup (see `rsync` manpage for details).
My excludes contents:
```
/backup
/bin
/boot
/dev
/lib
/lib64
/lost+found
/media
/mnt
/proc
/run
/sbin
/selinux
/sys
/tmp
/var/cache
/var/lock
/var/run
/var/tmp
/var/swap.img
```

* Create a cron entry to run this script each day: `crontab -e`
```
0 3 * * * /usr/local/bin/hactar / /backup > /dev/null
```
This line calls the script each night at 3am and rsyncs the contents of `/` to `/backup` and sends you an email you if any errors occured during runtime.
