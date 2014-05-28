Stouts.backup
=============

[![Build Status](https://travis-ci.org/Stouts/Stouts.backup.png)](https://travis-ci.org/Stouts/Stouts.backup)

Ansible role which manage backups. Support file backups, postgresql, mysql, mongo db backups.


#### Variables

The role variables and default values.

```yaml
backup_enabled: yes           # Enable the role
backup_remove: no             # Set yes for uninstall the role from target system

backup_user: "{{ansible_ssh_user}}" # Run backups from user
backup_home: /etc/backup      # Backup configuration directory
backup_work: /var/backup      # Working directory

backup_duplicity_ppa: ppa:duplicity-team/ppa

# Logging
backup_logdir: /var/log/backup
backup_logfile: backup-`date +%Y-%m-%d`.txt
backup_debug: no

# Posgresql
backup_postgres_user: postgres
backup_postgres_host: ""

# Mysql
backup_mysql_user: mysql
backup_mysql_pass: ""

backups: []                   # List of backup jobs
                              # Ex. backups:
                              #       - name: www
                              #         schedule: 0 0 * * 0
                              #         source: /var/www
                              #         max_age: 10D
                              #         target: s3://my.bucket/www
                              #       - name: postgresql
                              #         schedule: 0 4 * * *
                              #         source: postgresql://db_name
                              #         target: s3://my.bucket/postgresql

# Default values
# ==============
# (every value can be replaced in jobs individually)

# GPG
backup_gpg_key: disabled
backup_gpg_pw: ""
backup_gpg_opts: ''

# TARGET
# syntax is
#   scheme://[user:password@]host[:port]/[/]path
# probably one out of
#   file://[/absolute_]path
#   ftp[s]://user[:password]@other.host[:port]/some_dir
#   hsi://user[:password]@other.host/some_dir
#   cf+http://container_name
#   imap[s]://user[:password]@host.com[/from_address_prefix]
#   rsync://user[:password]@other.host[:port]::/module/some_dir
#   rsync://user@other.host[:port]/relative_path
#   rsync://user@other.host[:port]//absolute_path
#   # for the s3 user/password are AWS_ACCESS_KEY_ID/AWS_SECRET_ACCESS_KEY
#   s3://[user:password]@host/bucket_name[/prefix]
#   s3+http://[user:password]@bucket_name[/prefix]
#   ssh://user[:password]@other.host[:port]/some_dir
#   tahoe://alias/directory
#   webdav[s]://user[:password]@other.host/some_dir
backup_target: 'file:///var/backup'
# optionally the username/password can be defined as extra variables
backup_target_user:
backup_target_pass:

backup_max_age: 1M
backup_max_full_backups: 1

# verbosity of output (error 0, warning 1-2, notice 3-4, info 5-8, debug 9)
backup_verbosity: 3

backup_exclude: [] # List of filemasks to exlude
```

#### Usage

Add `Stouts.backup` to your roles and set variables in your playbook file.

Example:

```yaml

- hosts: all

  roles:
    - Stouts.backup

  vars:
    backup_target_user: aws_access_key
    backup_target_pass: aws_secret
    backups:
    # Backup uploads
    - name: uploads
        schedule: 0 3 * * 0                       # At 3am every day
        source: /usr/lib/project/uploads
        target: s3://s3-eu-west-1.amazonaws.com/backup.backet/{{inventory_hostname}}/uploads
    # Backup postgresql
    - name: postgresql
        schedule: 0 4 * * 0                       # At 4am every day
        source: postgresql://project
        target: s3://s3-eu-west-1.amazonaws.com/backup.backet/{{inventory_hostname}}/postgresql
        user: postgres

```

#### License

Licensed under the MIT License. See the LICENSE file for details.

#### Feedback, bug-reports, requests, ...

Are [welcome](https://github.com/Stouts/Stouts.backup/issues)!
