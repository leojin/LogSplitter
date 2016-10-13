# logspliter
A small tool to split your program log(e.g. nginx access log)
This tool works with crontab or some other dispatchers.
It needs a conf file in 'ini' format and the default path is /etc/logsplitter.ini.

# usage
```shell
leojindeMacBook-Pro:LogSplitter leojin$ ./log_splitter -h
Usage: ./log_splitter [options] [args] section1 section2 ...

--config-file <PATH>    : set the path of configfile
 -c                       DEFAULT: /etc/logsplitter.ini

--test                  : just check the config file
 -t

--help                  : display this usage
 -h
```

The tool needs python 2.7.

With --config-file/-c, you can use custom path for your conf file.
With --test/-t, you can just test your config instead of running the split program directly.

'Sections' is the same concept in ini file.
Every section is an independent conf for a log file.

All the options are described detailly in the conf.ini, your can copy it(e.g. copy it to /etc and rename it to 'logsplitter.ini')

Keep the default section and its options is helper, unless you want to write all the options.

# Example

Except the default section, i have another two.

```ini
[ACCESS_BLOG]
file_path = /data/log/service/blog/blog_access.log
file_action = back
split_granularity = hour
server_pid_file = /data/var/nginx/nginx.pid
backup_path = /data/bak
custom_command = echo `date` "Access Blog Done"

[ACCESS_WEIXIN]
file_path = /data/log/service/weixin/weixin_access.log
file_action = back
split_granularity = hour
server_pid_file = /data/var/nginx/nginx.pid
backup_path = /data/bak
custom_command = echo `date` "Access Weixin Done"
```

And here is my crontab config

```shell
 0 * * * * /root/opbin/logSplitter/log_splitter ACCESS_BLOG ACCESS_WEIXIN >> /root/opbin/logSplitter/runtime.log 2>&1
```

This tools helps me split the log, compress the log, delete the log, and reload the server config(it works for nginx/httpd by sending an usr1 signal to the main process, so you need to set the server_pid_file option)

Logs splited by different granularity need separately crontab command.
For example if you have those two section:

```ini
[SPLIT_DAY]
file_path = log_file_1
file_action = back
split_granularity = day

[SPLIT_HOUR]
file_path = log_file_2
file_action = back
split_granularity = hour
```

Then you need two crontab commands:

```shell
0 0 * * * /root/opbin/logSplitter/log_splitter SPLIT_DAY
0 * * * * /root/opbin/logSplitter/log_splitter SPLIT_HOUR
```



