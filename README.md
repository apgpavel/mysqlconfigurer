# MySQL configurer

[![Build Status - Master](https://travis-ci.com/initlabopen/mysqlconfigurer.svg?branch=master)](https://travis-ci.com/initlabopen/mysqlconfigurer)

## Description
**MySQL Configurer** is a script and online service to assist you prepare performance optimized configuration of your MySQL server based on the MySQLTuner report (MySQL status and system information). The service analyzes the MySQLTuner report, MySQL status and system information of your server and provides settings recommendations in the form of a MySQL configuration file.

## Features
- Fully automated MySQL performance optimized configuration creation. 
- **MySQL configurer** recommended configuration deliver a [30% boost](#Tests) to MySQL performance compare to the default configuration.
- **MySQL Configurer** supports 19 parameters of MySQL/Percona/MariaDB server.
- With **MySQL Configurer** you could prepare configuration file for your MySQL server just in [60 seconds](https://youtu.be/QluJpSl6dGk).
- You could use **MySQL Configurer** for getting the recommended values for your server and insert in your configuration.

## Warning
**Always** test recommended configuration on staging environments, and **always** keep in mind that improvements in one area can **negatively** affect MySQL in other areas.

It's also important to wait at least a day of uptime to get accurate results.

## Requirements

*** MINIMAL REQUIREMENTS ***
- Perl 5.6 or later (with perl-doc package)
- Perl module JSON
- Unix/Linux based operating system (tested on Linux, BSD variants, and Solaris variants)
- Unrestricted read access to the MySQL server (OS root access recommended for MySQL < 5.1)

## Technical details
**MySQL Configurer** is a Bash script which
1. downloads last version of the MySQLTuner
2. runs MySQLTuner with options "--json --verbose --notbstat"
3. uploads MySQLTuner report in the JSON to AIOps online service https://api.servers-support.com/v1/mysql
4. downloads recommended MySQL config file

## Tests
We tested the results with Sysbench on a virtual server running Debian 9 (2 CPU, 2GB Ram) the table contained 10 million entries.
Two configurations were tested, the MySQL default configuration and the configuration recommended by the **MySQLConfigurer** service. The tests were two-step: read (test1) only and read/write (test2).

Recommended configuration delivered a 30% boost to MySQL performance compared to the default configuration. Follow this link to see test results:
https://docs.google.com/spreadsheets/d/1J9FDgBGbvNA356d74WKYBaEzSwK7H-wgjHEQgYh8CMI/edit?usp=sharing


## Usage
1. Download mysqlconfigurer.sh
```bash
wget https://raw.githubusercontent.com/initlabopen/mysqlconfigurer/master/mysqlconfigurer.sh
```
of 
```bash
curl -o mysqlconfigurer.sh  https://raw.githubusercontent.com/initlabopen/mysqlconfigurer/master/mysqlconfigurer.sh
```
2. Run mysqlconfigurer.sh
```bash
/bin/bash mysqlconfigurer.sh
```
3. In the /tmp/.mysqlconfigurer folder you could see
```bash
root@mysqlconfigurer# ls -l /tmp/.mysqlconfigurer/
total 264
-rw-r--r-- 1 root root    479 Dec 19 06:03 z_aiops_mysql.cnf
-rw-r--r-- 1 root root 226002 Dec 18 16:44 mysqltuner.pl
-rw-r--r-- 1 root root  33410 Dec 18 16:44 mysqltunerreport.json
```
- **mysqltunerreport.json** - the MySQLTuner report file in the JSON format
- **z_aiops_mysql.cnf** - recommended MySQL config file downloaded from api.server-support.com

4. If you want to use this mysql.conf file you could copy it in the /etc/mysql/conf.d/ directory and restart MySQL server
```bash
cp /tmp/.mysqlconfigurer/z_aiops_mysql.cnf  /etc/mysql/conf.d/
service mysql restart
```

Example of the recommended configuration file /tmp/.mysqlconfigurer/z_aiops_mysql.cnf:
```
[mysqld]
query_cache_type = 1 ### Previous value : OFF
query_cache_size = 128M ### Previous value : 1048576
query_cache_limit = 16M ### Previous value : 1048576
thread_cache_size = 128 ### Previous value : 9
key_buffer_size = 196M ### Previous value : 8388608
sort_buffer_size = 24M ### Previous value : 262144
bulk_insert_buffer_size = 2M ### Previous value : 8388608
myisam_sort_buffer_size = 24M ### Previous value : 8388608
innodb_buffer_pool_instances = 1 ### Previous value : 1
innodb_buffer_pool_size = 18022 ### Previous value : 134217728
max_heap_table_size = 256M ### Previous value : 16777216
tmp_table_size = 256M ### Previous value : 16777216
join_buffer_size = 8M ### Previous value : 262144
max_connections = 400 ### Previous value : 151
interactive_timeout = 1200 ### Previous value : 28800
wait_timeout = 1200 ### Previous value : 28800
table_open_cache = 65536 ### Previous value : 2000
innodb_flush_log_at_trx_commit = 2 ### Previous value : 1
innodb_log_file_size = 4505 ### Previous value : 50331648
```

## Contribute

You can help this project by reporting problems, suggestions or contributing to the code.

### Report a problem or suggestion

Go to our [issue tracker](https://github.com/initlabopen/mysqlconfigurer/issues) and check if your problem/suggestion is already reported. If not, create a new issue with a descriptive title and detail your suggestion or steps to reproduce the problem.
