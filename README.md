# README.md

## Introduction

mymon(MySQL-Monitor) 是夜莺用来监控MySQL数据库运行状态的一个插件（在Open-Falcon的mymon监控插件上做了一点点的修改），采集包括global status, global variables, slave status以及innodb status等MySQL运行状态信息。

修改点：
- 所有metric加了mysql.的前缀，上报格式如：mysql.Innodb_buffer_pool_pages_dirty
- endpoint支持shell命令的方式采集

**注意：n9e版本至少需要高于[v1.1.0](https://github.com/didi/nightingale/releases/tag/v1.1.0)**

## Installation

```bash
# Build
go get -u github.com/n9e/mymon
cd $GOPATH/src/github.com/n9e/mymon
make

# Add to crontab
echo '* * * * * root cd ${WORKPATH} && ./mymon -c etc/myMon.cfg' > /etc/cron.d/mymon
```

## Metric

采集的metric信息，请参考./metrics.txt。该文件仅供参考，实际采集信息会根据MySQL版本、配置的不同而变化。

### 同步延迟

关于同步延迟检测的metric有两个: `Seconds_Behind_Master`、`Heartbeats_Behind_Master`。

`Seconds_Behind_Master`是MySQL`SHOW SLAVE STATUS`输出的状态变量。由于低版本的MySQL还不支持HEARTBEAT_EVENT，在低版本的MySQL中该状态可能会由于IO线程假死导致测量不准确，因此mymon增加了`Heartbeats_Behind_Master`。它依赖于`pt-heartbeat`，统计基于`pt-heartbeat`生成的mysql.heartbeat表中的ts字段值与从库当前时间差。如果未配置`pt-heartbeat`，则该项上报-1值。

关于pt-heartbeat的配置使用，链接如下：
https://www.percona.com/doc/percona-toolkit/LATEST/pt-heartbeat.html


## Contributors

* libin 微信：libin_cc 邮件：libin_dba@xiaomi.com [OLD]
* liuzidong [![Chat on gitter](https://badges.gitter.im/gitterHQ/gitter.png)](https://gitter.im/sylzd) 邮件：liuzidong@xiaomi.com [CURRENT]