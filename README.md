# mariadbfix

MARIADBFIX

On CentOS 7 with MariaDB, there is one problem that the daemon does not start after a restart.

On startup, the daemon crashes with the following error:


[ERROR] mariadbd: Can't create/write to file '/var/run/mysqld/mysqld.pid' (Errcode: 2 "No such file or directory")
[ERROR] Can't start server: can't create PID file: No such file or directory
systemd[1]: mariadb.service: main process exited, code=exited, status=1/FAILURE
systemd[1]: Failed to start MariaDB 10.5.13 database server.
The solution is trivial, creating the necessary directories and assigning rights:


mkdir /var/run/mysqld/
chown mysql:mysql /var/run/mysqld
service mysqld start
This is a temporary solution to start the daemon right now.

In the future, you can create a fix to solve this problem (in fact, the problem is not solved, but the "crutch" is simply screwed on)

Create a file


nano /etc/systemd/system/mariafix.service
with the following content:


[Unit]
Description=Fix permission with /var/run/mysqld
After=network.target

[Service]
Type=simple
ExecStartPre=-/usr/bin/mkdir /var/run/mysqld
ExecStart=/usr/bin/chown mysql:mysql /var/run/mysqld
Restart=on-abort

[Install]
WantedBy=multi-user.target
And turn it on:


systemctl enable mariafix
systemctl start mariafix

