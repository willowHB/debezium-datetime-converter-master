> [参考大牛的源码](https://github.com/holmofy/debezium-datetime-converter#readme)
[![Debezium architecture](https://debezium.io/documentation/reference/1.4/_images/debezium-architecture.png)](https://debezium.io/documentation/reference/1.4/connectors/mysql.html)

[![Build Status(https://github.com/holmofy/debezium-datetime-converter/actions/workflows/release.yml/badge.svg)](https://github.com/holmofy/debezium-datetime-converter/actions/workflows/release.yml/badge.svg)](https://github.com/holmofy/debezium-datetime-converter/releases)

# debezium-datetime-converter

Debezium [custom converter](https://debezium.io/documentation/reference/development/converters.html) is used to deal
with
mysql [datetime type problems](https://debezium.io/documentation/reference/1.5/connectors/mysql.html#mysql-temporal-types)
.

| mysql                               | binlog-connector                         | debezium                          | schema                 |
| ----------------------------------- | ---------------------------------------- | --------------------------------- | ----------------------------------- |
| date<br>(2021-01-28)                | LocalDate<br/>(2021-01-28)               | Integer<br/>(18655)               | io.debezium.time.Date               |
| time<br/>(17:29:04)                 | Duration<br/>(PT17H29M4S)                | Long<br/>(62944000000)            | io.debezium.time.Time               |
| timestamp<br/>(2021-01-28 17:29:04) | ZonedDateTime<br/>(2021-01-28T09:29:04Z) | String<br/>(2021-01-28T09:29:04Z) | io.debezium.time.ZonedTimestamp     |
| Datetime<br/>(2021-01-28 17:29:04)  | LocalDateTime<br/>(2021-01-28T17:29:04)  | Long<br/>(1611854944000)          | io.debezium.time.Timestamp          |

> For details, please refer to [this article](https://blog.hufeifei.cn/2021/03/13/DB/mysql-binlog-parser/)
>
> <font color=#00ffff size=4>在此基础上，我对代码进行了改造，我需要把MySql的Datetime类型转成时间戳，即long类型数值，然后存入es.(其他类型也已修改，使用前，请充分测试！！！)</font>

# Usage

1. [Download](https://github.com/willowHB/debezium-datetime-converter-master/releases) the extended jar package and put it in
   the same level directory of the debezium plugin.或者debezium的lib目录下，当然debezium本身也是在plugin目录下的。

2. In debezium-connector,如果想把时间转换成string类型的格式, Add the following configuration:

```properties
"connector.class": "io.debezium.connector.mysql.MySqlConnector",
# ...
"converters": "datetime",
"datetime.type": "com.darcytech.debezium.converter.MySqlDateTimeConverter",
"datetime.format": "yyyy-MM-dd",
"datetime.format.time": "HH:mm:ss",
"datetime.format.datetime": "yyyy-MM-dd HH:mm:ss",
"datetime.format.timestamp": "yyyy-MM-dd HH:mm:ss",
"datetime.format.timestamp.zone": "UTC+8"
```

3. In debezium-connector, 如果想把时间转换成timestamp,Add the following configuration:
```properties
"connector.class": "io.debezium.connector.mysql.MySqlConnector",
# ...
"converters": "datetime",
"datetime.type": "com.darcytech.debezium.converter.MySqlDateTime2TimestampConverter",
"datetime.format": "yyyy-MM-dd",
"datetime.format.time": "HH:mm:ss",
"datetime.format.datetime": "yyyy-MM-dd HH:mm:ss",
"datetime.format.timestamp": "yyyy-MM-dd HH:mm:ss",
"datetime.format.timestamp.zone": "+08:00"
```
同步前mysql数据：(mysql时区：北京时间)
![image](https://github.com/willowHB/debezium-datetime-converter-master/assets/42429887/1ddbbb45-41e6-49c0-a5da-63cc29c4e9aa)

同步后数据：
![image](https://github.com/willowHB/debezium-datetime-converter-master/assets/42429887/5bd6df8a-ff54-4282-932d-01b6faad5cfa)
