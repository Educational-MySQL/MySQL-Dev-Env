# MySQL-Dev-Env
Azərbaycan dilində, MySQL development mühitin hazırlanması haqqında faydalı məlumatların yerləşdiyi repo.

Bu doc Yusif  Yusifli tərəfindən hazırlanmışdır. Təşəkkür edirik.

MYSQL Server 5.7.17(5.7)–nin Github repo-dan endirilib, compile edilərək işə salınması haqqında manual.

Əməliyyat sistemi kimi, Ubuntu 16.04 LTS isitifadə olunub. ƏS qurulumu zamanı, seçilən paketlərdən asılı olaraq, compile zamanı qarşılaşdığınız xətalar fərqli ola bilər. Burda göstərilən bütün əməliyyatlar `root` istifadəçisi ilə olunduğundan, əmrlərin qarşısında `sudo` istifadə edilməyib. Əgər root-dan fərqli istifadəçi istifadə edirsinizsə, bəzi komandaların qarşısına sudo əlavə etmək lazım gələcək. Məsələn – `sudo apt-get install git`
Addımlar sırası ilə aşağıdakı kimidir:

1. Github-dan Repo-nu endirə bilmək üçün `git` yüklənməlidir (əgər yoxdursa). Var olmasını yoxlamaq üçün, git –version komandasından istifadə edə bilərsiniz. Paketin yazılması - `apt-get install git`

2. Mysql Server rəsmi repo linki  - [https://github.com/mysql/mysql-server]
Reponu öz local kompüterinizə endirmək üçün – `git clone https://github.com/mysql/mysql-server` (Ən son versiyanı endirəcək ),(komandanı işə saldığınız zaman olduğunuz qovluğa yükləyəcək. Əgər fərqli qovluq istəyirsinizsə öncədən yaradıb orada işə salın)
Əgər mysql-server in ən son versiyasını yox başqa versiyalarını endirmək istəyirsinizsə aşağıdakı əməliyyatları etmək lazım gələcək:
```
root@ip-172-31-17-224:/github/mysql-server# git checkout 5.6
Branch 5.6 set up to track remote branch 5.6 from origin.
Switched to a new branch '5.6'
```

Hal- hazırda olduğunuz versiyanı yoxlamaq üçün (Qarşısında ulduz olan aktiv sayılır)
```
root@ip-172-31-17-224:/github/mysql-server# git branch
* 5.6
  5.7
```  

Yenidən 5.7 versiyasına dönmək üçün
```
root@ip-172-31-17-224:/github/mysql-server# git checkout 5.7
Checking out files: 100% (21703/21703), done.
Switched to branch '5.7'
Your branch is up-to-date with 'origin/5.7'.
root@ip-172-31-17-224:/github/mysql-server# git branch
  5.6
* 5.7
```

Nəzərə alın ki, compile komandasını işə saldığınız zaman yalnız aktiv olan repo compile olunur.
3. Bəzi lazımi paketlərin yüklənməsi. Mənim aldığım xətalara və Şəhriyarın yazdıqlarına əsasən aşağıdakıları yüklədim. Yükləmə qaydasını 1-ci addımdakı nümunədə görə bilərsiniz.
build-essential
bison
libssl-dev
valgrind
cmake
gcc
cpp
ncurses
gcc-c++
libncurses5-dev
zlib1g-dev
libreadline6-dev
4. Paketlər yükləndikdən sonra compile işləminə başlaya bilərsiniz. Compile komandası da həmçinin Şəhriyarın mysql.az –da yazdığı məqaləyə uyğun olaraq aşağıdakı kimi seçildi. Hər hansı xəta alsanız həll etmədən növbəti addıma keçməyin. Əgər xəta ilə qarşılaşsanız səbəbini həll edib repo olan qovluqda (Mis - /github/mysql-server/) yaranan CmakeCache.txt faylını silmək lazımdır. Faylı sildikdən sonra compile comandasını yenidən işə sala bilərsiniz.
cmake -DCMAKE_INSTALL_PREFIX=/opt/mysql57 -DMYSQL_DATADIR=/opt/mysql57/data -DSYSCONFDIR=/opt/mysql57 -DWITH_SSL=system -DMYSQL_TCP_PORT=3307 -DMYSQL_UNIX_ADDR=/opt/mysql57/mysqld.sock -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci -DWITH_DEBUG=1 -DCOMPILATION_COMMENT="Yusif Yusifov MYSQL 5.7" -DOPTIMIZER_TRACE=1 -DWITH_ZLIB=system -DWITH_VALGRIND=1 -DCMAKE_C_FLAGS=-DHAVE_purify -DCMAKE_CXX_FLAGS=-DHAVE_purify -DENABLE_DOWNLOADS=1 -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/opt/mysql57
5. Yuxarıdakı addım uğurla bitdikdən sonra make və make install komandaları ilə compile olunmuş faylları yuxarıda göstərdiyiniz qovluğa yükləyəcək.
6. Əgər mysql_sandbox –dan istifadə etmək istəyirsinizsə, bu addımdan sonra 14 – cü addıma keçin. Mysql_sandbox istifadə etmək istəməsəniz, 14-cü addıma kimi davam edə bilərsiniz.
7. Mysql binary faylları olan qovluğu env-yə əlavə edin export PATH = /opt/mysql57/bin/:$PATH
8. /opt/mysql57 qovuluğu altında data və binary loglar üçün yer ayırın. Yeri fərqli də seçə bilərsiniz. mkdir –p /opt/mysql57/data/ /opt/mysql57/mblog/
9. Mysql istifadçisini və qrupunu yaradın (groupadd mysql) və (useradd –g mysql). /opt/mysql57 qovluğunun sahibini mysql useri edin – chown –R mysql.mysql /opt/mysql57/
10. my.cnf faylınızı yaradın. Aşağıdakı parametrlər test sistem üçündür və sizin server parametrlərinə görə dəyişə bilər.
[mysqld]
datadir=/opt/mysql57/data/
sql_mode = ''
socket=/opt/mysql57/data/mysql.sock
skip-name-resolve
symbolic-links=0
log-error=/var/log/mysqld.log
pid-file=/opt/mysql57/data/mysqld.pid
max_connections=400
key_buffer_size = 128M
max_allowed_packet = 16M
table_open_cache = 1000
sort_buffer_size = 1M
read_buffer_size = 32K
read_rnd_buffer_size = 32K
myisam_sort_buffer_size = 16
query_cache_limit       = 1M
query_cache_size        = 32M
long_query_time = 10
#INNODB
innodb_buffer_pool_size = 64M
innodb_data_home_dir =
innodb_data_file_path =/opt/mysql57/data/ibdata1:128M;/opt/mysql57/data/ibdata2:128M:autoextend
innodb_log_group_home_dir = /opt/mysql57/data/
innodb_log_files_in_group=2
innodb_log_file_size=32M
innodb_log_buffer_size=8M
innodb_flush_log_at_trx_commit=2
innodb_flush_method = O_DIRECT
innodb_thread_concurrency = 2
innodb_file_per_table=1
innodb_io_capacity=100
innodb_io_capacity_max=2000
thread_cache_size =8
innodb_read_io_threads =8
innodb_write_io_threads =8
#Replication
server-id=1
log-bin = /opt/mysql57/mblog/binlog
max_binlog_size = 100M
[client]
socket=/opt/mysql57/data/mysql.sock

11. MySQL Server initialize əmrini verək mysqld --defaults-file=/etc/my.cnf –initialize Bu komanda ilə my.cnf faylında göstərdiyiniz parametrlərə uyğun olaraq serverin işləməsi üçün lazım olan fayllar və sistem database-ləri generate olunacaq.
12. Serveri start edək. mysqld_safe –defaults-file=/etc/my.cnf &  Mən test etdiyim zaman root userindən istifadə etdiyimə görə  aşağıdakı kimi vəziyyət yaranmışdı. Həll yolu kimi mysql error log faylını owneri-ni mysql userinə dəyişdim.
root@ip-172-31-17-224:/opt/mysql57/bin# 2016-12-22T08:52:55.618919Z mysqld_safe Logging to '/var/log/mysql-error.log'.
2016-12-22T08:52:55.620029Z mysqld_safe Logging to '/var/log/mysql-error.log'.
2016-12-22T08:52:55.630692Z mysqld_safe Starting mysqld daemon with databases from /opt/mysql57/data/
2016-12-22T08:52:55.815570Z mysqld_safe mysqld from pid file /opt/mysql57/data/mysql.pid ended

[1]+  Done                    ./mysqld_safe

13. Qurduğumuz serverə bağlanaq. Bağlanmamışdan öncə mysql error logundan initialize zamanı generate olunmuş temporary şifrəni götürməniz lazımdır.

root@ip-172-31-17-224:/var/log# cat /var/log/mysql-error.log |grep "temporary pass"
2016-12-22T07:59:27.722674Z 1 [Note] A temporary password is generated for root@localhost: olbeoY?Wv3Hr


 root@ip-172-31-17-224:/opt/mysql57/bin# ./mysql –u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.17-debug-valgrind

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> exit

Həmçinin unutmaq lazım deyil ki, bu temporary password birdəfəlik istifadə üçündür və connect olduqdan sonra, dərhal dəyişmək lazımdır.
mysql> set password=password(‘YourPass’)

14. Mysql_sandbox istifadə etməyə qərar verdinizsə, ilk öncə github reponu endirib install etmək lazımdır. Addımlar aşağıdakı kimidir.
git clone https://github.com/datacharmer/mysql-sandbox
Cloning into 'mysql-sandbox'...
remote: Counting objects: 2092, done.
remote: Compressing objects: 100% (7/7), done.
remote: Total 2092 (delta 0), reused 0 (delta 0), pack-reused 2084
Receiving objects: 100% (2092/2092), 639.36 KiB | 0 bytes/s, done.
Resolving deltas: 100% (1275/1275), done.
Checking connectivity... done.
cd mysql-sandbox/

Rəsmi github reposunda göstərildiyi kimi install edək. Addımlar və loglar aşağıdakı kimidir. Rahatlıq üçün komandaları bold etmişəm.

root@ip-172-31-17-224:/github/mysql-sandbox# perl Makefile.PL
Checking if your kit is complete...
Looks good
Generating a Unix-style Makefile
Writing Makefile for MySQL::Sandbox
Writing MYMETA.yml and MYMETA.json
root@ip-172-31-17-224:/github/mysql-sandbox# make
cp lib/MySQL/Sandbox/Recipes.pm blib/lib/MySQL/Sandbox/Recipes.pm
cp lib/MySQL/Sandbox.pm blib/lib/MySQL/Sandbox.pm
cp lib/MySQL/Sandbox/Scripts.pm blib/lib/MySQL/Sandbox/Scripts.pm
cp repo_list.pl blib/lib/MySQL/repo_list.pl
cp bin/make_multiple_sandbox blib/script/make_multiple_sandbox
"/usr/bin/perl" -MExtUtils::MY -e 'MY->fixin(shift)' -- blib/script/make_multiple_sandbox
cp bin/make_sandbox_from_installed blib/script/make_sandbox_from_installed
"/usr/bin/perl" -MExtUtils::MY -e 'MY->fixin(shift)' -- blib/script/make_sandbox_from_installed
cp bin/make_multiple_custom_sandbox blib/script/make_multiple_custom_sandbox
"/usr/bin/perl" -MExtUtils::MY -e 'MY->fixin(shift)' -- blib/script/make_multiple_custom_sandbox
cp bin/test_sandbox blib/script/test_sandbox
"/usr/bin/perl" -MExtUtils::MY -e 'MY->fixin(shift)' -- blib/script/test_sandbox
cp bin/low_level_make_sandbox blib/script/low_level_make_sandbox
"/usr/bin/perl" -MExtUtils::MY -e 'MY->fixin(shift)' -- blib/script/low_level_make_sandbox
cp bin/make_sandbox_from_url blib/script/make_sandbox_from_url
"/usr/bin/perl" -MExtUtils::MY -e 'MY->fixin(shift)' -- blib/script/make_sandbox_from_url
cp bin/make_replication_sandbox blib/script/make_replication_sandbox
"/usr/bin/perl" -MExtUtils::MY -e 'MY->fixin(shift)' -- blib/script/make_replication_sandbox
cp bin/deploy_to_remote_sandboxes.sh blib/script/deploy_to_remote_sandboxes.sh
"/usr/bin/perl" -MExtUtils::MY -e 'MY->fixin(shift)' -- blib/script/deploy_to_remote_sandboxes.sh
cp bin/msb blib/script/msb
"/usr/bin/perl" -MExtUtils::MY -e 'MY->fixin(shift)' -- blib/script/msb
cp bin/msandbox blib/script/msandbox
"/usr/bin/perl" -MExtUtils::MY -e 'MY->fixin(shift)' -- blib/script/msandbox
cp bin/make_sandbox blib/script/make_sandbox
"/usr/bin/perl" -MExtUtils::MY -e 'MY->fixin(shift)' -- blib/script/make_sandbox
cp bin/sbtool blib/script/sbtool
"/usr/bin/perl" -MExtUtils::MY -e 'MY->fixin(shift)' -- blib/script/sbtool
cp bin/make_sandbox_from_source blib/script/make_sandbox_from_source
"/usr/bin/perl" -MExtUtils::MY -e 'MY->fixin(shift)' -- blib/script/make_sandbox_from_source
Manifying 3 pod documents
root@ip-172-31-17-224:/github/mysql-sandbox# make test
PERL_DL_NONLAZY=1 "/usr/bin/perl" "-MExtUtils::Command::MM" "-MTest::Harness" "-e" "undef *Test::Harness::Switches; test_harness(0, 'blib/lib', 'blib/arch')" t/*.t
t/01_modules.t ...................... ok
t/02_test_binaries.t ................ ok
t/03_test_sandbox.t ................. ok
t/04_test_sbtool.t .................. ok
t/05_test_smoke.t ................... ok
t/06_test_user_defined.t ............ ok
t/07_test_user_defined.t ............ ok
t/08_test_single_port_checking.t .... ok
t/09_test_multiple_port_checking.t .. ok
t/10_check_start_restart.t .......... ok
t/11_replication_parameters.t ....... ok
t/12_custom_user_pwd.t .............. ok
t/13_innodb_plugin_install.t ........ ok
t/14_semi_synch_plugin_install.t .... ok
t/15_user_privileges.t .............. ok
t/16_replication_options.t .......... ok
t/17_replication_flow.t ............. ok
t/18_force_creation.t ............... ok
t/19_replication_gtid.t ............. ok
t/20_add_option.t ................... ok
t/21_replication_gtid_option.t ...... ok
t/22_init_exec_sql.t ................ ok
t/23_mysqlx_plugin.t ................ ok
All tests successful.
Files=23, Tests=25,  1 wallclock secs ( 0.07 usr  0.01 sys +  0.80 cusr  0.03 csys =  0.91 CPU)
Result: PASS
root@ip-172-31-17-224:/github/mysql-sandbox# make install
Manifying 3 pod documents
Installing /usr/local/share/perl/5.22.1/MySQL/Sandbox.pm
Installing /usr/local/share/perl/5.22.1/MySQL/repo_list.pl
Installing /usr/local/share/perl/5.22.1/MySQL/Sandbox/Recipes.pm
Installing /usr/local/share/perl/5.22.1/MySQL/Sandbox/Scripts.pm
Installing /usr/local/man/man3/MySQL::Sandbox.3pm
Installing /usr/local/man/man3/MySQL::Sandbox::Recipes.3pm
Installing /usr/local/man/man3/MySQL::Sandbox::Scripts.3pm
Installing /usr/local/bin/make_multiple_sandbox
Installing /usr/local/bin/make_sandbox_from_url
Installing /usr/local/bin/low_level_make_sandbox
Installing /usr/local/bin/msandbox
Installing /usr/local/bin/test_sandbox
Installing /usr/local/bin/make_replication_sandbox
Installing /usr/local/bin/deploy_to_remote_sandboxes.sh
Installing /usr/local/bin/msb
Installing /usr/local/bin/make_sandbox
Installing /usr/local/bin/make_sandbox_from_installed
Installing /usr/local/bin/make_multiple_custom_sandbox
Installing /usr/local/bin/make_sandbox_from_source
Installing /usr/local/bin/sbtool
Appending installation info to /usr/local/lib/x86_64-linux-gnu/perl/5.22.1/perllocal.pod

15. İnstal etdikdən sonra compile etdiyimiz mysql source kodundan yeni instance yaradaq.  Əgər root user istifadə edirsinizsə SANDBOX_AS_ROOT parametrini 0 – dan fərqli dəyərə set etmək lazımdır. export SANDBOX_AS_ROOT=1
Əks halda run edə bilməyəcəksiniz.

make_sandbox_from_source /github/mysql-server single

Nəticə aşağıdakı kimi olacaq. Loglarla yanaşı sizə qurulmuş instance barədə məlutlar da veriləcək.
no old tarball found
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: MySQL
CPack: - Install project: MySQL
CPack: Create package
CPack: - package: /github/mysql-server/mysql-5.7.17-linux-x86_64.tar.gz generated.
unpacking /github/mysql-server/mysql-5.7.17-linux-x86_64.tar.gz
Executing low_level_make_sandbox --basedir=/github/mysql-server/5.7.17 \
        --sandbox_directory=msb_5_7_17 \
        --install_version=5.7 \
        --sandbox_port=5717 \
        --no_ver_after_name \
        --my_clause=log-error=msandbox.err
    The MySQL Sandbox,  version 3.2.04
    (C) 2006-2016 Giuseppe Maxia
Installing with the following parameters:
upper_directory                = /root/sandboxes
sandbox_directory              = msb_5_7_17
sandbox_port                   = 5717
check_port                     =
no_check_port                  =
datadir_from                   = script
install_version                = 5.7
basedir                        = /github/mysql-server/5.7.17
tmpdir                         =
my_file                        =
operating_system_user          = root
db_user                        = msandbox
remote_access                  = 127.%
bind_address                   = 127.0.0.1
ro_user                        = msandbox_ro
rw_user                        = msandbox_rw
repl_user                      = rsandbox
db_password                    = msandbox
repl_password                  = rsandbox
my_clause                      = log-error=msandbox.err
init_options                   =
init_my_cnf                    =
init_use_cnf                   =
master                         =
slaveof                        =
high_performance               =
gtid                           =
pre_start_exec                 =
pre_grants_exec                =
post_grants_exec               =
pre_grants_sql                 =
post_grants_sql                =
pre_grants_file                =
post_grants_file               =
load_plugin                    =
plugin_mysqlx                  =
mysqlx_port                    =
prompt_prefix                  = mysql
prompt_body                    =  [\h] {\u} (\d) >
force                          =
no_ver_after_name              = 1
verbose                        =
load_grants                    = 1
no_load_grants                 =
no_run                         =
no_show                        =
keep_uuid                      =
history_dir                    =
do you agree? ([Y],n) Y
# Starting server
... sandbox server started
# Loading grants
Your sandbox server was installed in $HOME/sandboxes/msb_5_7_17

Qurduğunuz serverə bağlanmaq üçün son sətirdə qeyd edilmiş qovluğa keçid edib aşağıdakı komandanı işə sala bilərsiniz.

cd $HOME/sandboxes/msb_5_7_17
root@ip-172-31-17-224:~/sandboxes/msb_5_7_17# ./use –uroot
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 7
Server version: 5.7.17-debug-valgrind Yusif Yusifov MYSQL 5.7

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql [localhost] {root} ((none)) > select 1 from dual;
+---+
| 1 |
+---+
| 1 |
+---+
1 row in set (0.00 sec)

mysql [localhost] {root} ((none)) >

mysql_sandbox ilə qurlumuş serverlə rahat işləmək üçün bəzi hazır scriptləri qurulduğu folderdən tapa bilərsiniz. Misal

root@ip-172-31-17-224:~/sandboxes/msb_5_7_17# ./stop   -- Serveri stop edir
root@ip-172-31-17-224:~/sandboxes/msb_5_7_17# ./start   -- Serveri start edir
. sandbox server started
root@ip-172-31-17-224:~/sandboxes/msb_5_7_17# ./status  -- Statusunu göstərir
msb_5_7_17 on
