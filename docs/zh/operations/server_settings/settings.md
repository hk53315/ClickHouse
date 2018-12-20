# Server settings


## builtin_dictionaries_reload_interval

The interval in seconds before reloading built-in dictionaries.

ClickHouse reloads built-in dictionaries every x seconds. This makes it possible to edit dictionaries "on the fly" without restarting the server.

Default value: 3600.

**Example**

```xml
<builtin_dictionaries_reload_interval>3600</builtin_dictionaries_reload_interval>
```


## compression

Data compression settings.

!!! warning
    Don't use it if you have just started using ClickHouse.

The configuration looks like this:

```xml
<compression>
    <case>
      <parameters/>
    </case>
    ...
</compression>
```

You can configure multiple sections `<case>`.

Block field `<case>`:

- ``min_part_size`` – The minimum size of a table part.
- ``min_part_size_ratio`` – The ratio of the minimum size of a table part to the full size of the table.
- ``method`` – Compression method. Acceptable values ​: ``lz4`` or ``zstd``(experimental).

ClickHouse checks `min_part_size`  and `min_part_size_ratio`  and processes the `case` blocks that match these conditions. If none of the `<case>` matches, ClickHouse applies the `lz4` compression algorithm.

**Example**

```xml
<compression incl="clickhouse_compression">
    <case>
        <min_part_size>10000000000</min_part_size>
        <min_part_size_ratio>0.01</min_part_size_ratio>
        <method>zstd</method>
    </case>
</compression>
```


## default_database

The default database.

To get a list of databases, use the [SHOW DATABASES](../../query_language/misc.md#query_language_queries_show_databases) query.

**Example**

```xml
<default_database>default</default_database>
```


## default_profile

Default settings profile.

Settings profiles are located in the file specified in the parameter `user_config`.

**Example**

```xml
<default_profile>default</default_profile>
```


## dictionaries_config

The path to the config file for external dictionaries.

Path:

- Specify the absolute path or the path relative to the server config file.
- The path can contain wildcards \* and ?.

See also "[External dictionaries](../../query_language/dicts/external_dicts.md)".

**Example**

```xml
<dictionaries_config>*_dictionary.xml</dictionaries_config>
```


## dictionaries_lazy_load

Lazy loading of dictionaries.

If `true`, then each dictionary is created on first use. If dictionary creation failed, the function that was using the dictionary throws an exception.

If `false`, all dictionaries are created when the server starts, and if there is an error, the server shuts down.

The default is `true`.

**Example**

```xml
<dictionaries_lazy_load>true</dictionaries_lazy_load>
```


## format_schema_path

The path to the directory with the schemes for the input data, such as schemas for the [CapnProto](../../interfaces/formats.md#format_capnproto) format.

**Example**

```xml
  <!-- Directory containing schema files for various input formats. -->
  <format_schema_path>format_schemas/</format_schema_path>
```


## graphite

Sending data to [Graphite](https://github.com/graphite-project).

Settings:

- host – The Graphite server.
- port – The port on the Graphite server.
- interval – The interval for sending, in seconds.
- timeout – The timeout for sending data, in seconds.
- root_path – Prefix for keys.
- metrics – Sending data from a :ref:`system_tables-system.metrics` table.
- events – Sending data from a :ref:`system_tables-system.events` table.
- asynchronous_metrics – Sending data from a :ref:`system_tables-system.asynchronous_metrics` table.

You can configure multiple `<graphite>` clauses. For instance, you can use this for sending different data at different intervals.

**Example**

```xml
<graphite>
    <host>localhost</host>
    <port>42000</port>
    <timeout>0.1</timeout>
    <interval>60</interval>
    <root_path>one_min</root_path>
    <metrics>true</metrics>
    <events>true</events>
    <asynchronous_metrics>true</asynchronous_metrics>
</graphite>
```


## graphite_rollup

Settings for thinning data for Graphite.

For more details, see [GraphiteMergeTree](../../operations/table_engines/graphitemergetree.md).

**Example**

```xml
<graphite_rollup_example>
    <default>
        <function>max</function>
        <retention>
            <age>0</age>
            <precision>60</precision>
        </retention>
        <retention>
            <age>3600</age>
            <precision>300</precision>
        </retention>
        <retention>
            <age>86400</age>
            <precision>3600</precision>
        </retention>
    </default>
</graphite_rollup_example>
```


## http_port/https_port

The port for connecting to the server over HTTP(s).

If `https_port` is specified, [openSSL](#openssl) must be configured.

If `http_port` is specified, the openSSL configuration is ignored even if it is set.

**Example**

```xml
<https>0000</https>
```


## http_server_default_response

The page that is shown by default when you access the ClickHouse HTTP(s) server.

**Example**

Opens `https://tabix.io/` when accessing ` http://localhost: http_port`.

```xml
<http_server_default_response>
  <![CDATA[<html ng-app="SMI2"><head><base href="http://ui.tabix.io/"></head><body><div ui-view="" class="content-ui"></div><script src="http://loader.tabix.io/master.js"></script></body></html>]]>
</http_server_default_response>
```

<a name="server_settings-include_from"></a>

## include_from

The path to the file with substitutions.

For more information, see the section "[Configuration files](../configuration_files.md#configuration_files)".

**Example**

```xml
<include_from>/etc/metrica.xml</include_from>
```


## interserver_http_port

Port for exchanging data between ClickHouse servers.

**Example**

```xml
<interserver_http_port>9009</interserver_http_port>
```


## interserver_http_host

The host name that can be used by other servers to access this server.

If omitted, it is defined in the same way as the `hostname-f` command.

Useful for breaking away from a specific network interface.

**Example**

```xml
<interserver_http_host>example.yandex.ru</interserver_http_host>
```


## keep_alive_timeout

The number of seconds that ClickHouse waits for incoming requests before closing the connection. Defaults to 10 seconds

**Example**

```xml
<keep_alive_timeout>10</keep_alive_timeout>
```


## listen_host

Restriction on hosts that requests can come from. If you want the server to answer all of them, specify `::`.

Examples:

```xml
<listen_host>::1</listen_host>
<listen_host>127.0.0.1</listen_host>
```


## logger

Logging settings.

Keys:

- level – Logging level. Acceptable values: ``trace``, ``debug``, ``information``, ``warning``, ``error``.
- log – The log file. Contains all the entries according to `level`.
- errorlog – Error log file.
- size – Size of the file. Applies to ``log``and``errorlog``. Once the file reaches ``size``, ClickHouse archives and renames it, and creates a new log file in its place.
- count – The number of archived log files that ClickHouse stores.

**Example**

```xml
<logger>
    <level>trace</level>
    <log>/var/log/clickhouse-server/clickhouse-server.log</log>
    <errorlog>/var/log/clickhouse-server/clickhouse-server.err.log</errorlog>
    <size>1000M</size>
    <count>10</count>
</logger>
```

Writing to the syslog is also supported. Config example:

```xml
<logger>
    <use_syslog>1</use_syslog>
    <syslog>
        <address>syslog.remote:10514</address>
        <hostname>myhost.local</hostname>
        <facility>LOG_LOCAL6</facility>
        <format>syslog</format>
    </syslog>
</logger>
```

Keys:

- user_syslog — Required setting if you want to write to the syslog.
- address — The host[:порт] of syslogd. If omitted, the local daemon is used.
- hostname — Optional. The name of the host that logs are sent from.
- facility — [The syslog facility keyword](https://en.wikipedia.org/wiki/Syslog#Facility)
in uppercase letters with the "LOG_" prefix: (``LOG_USER``, ``LOG_DAEMON``, ``LOG_LOCAL3``, and so on).
Default value: ``LOG_USER`` if ``address`` is specified, ``LOG_DAEMON otherwise.``
- format – Message format. Possible values: ``bsd`` and ``syslog.``


## macros

Parameter substitutions for replicated tables.

Can be omitted if replicated tables are not used.

For more information, see the section "[Creating replicated tables](../../operations/table_engines/replication.md)".

**Example**

```xml
<macros incl="macros" optional="true" />
```


## mark_cache_size

Approximate size (in bytes) of the cache of "marks" used by [MergeTree](../../operations/table_engines/mergetree.md).

The cache is shared for the server and memory is allocated as needed. The cache size must be at least 5368709120.

**Example**

```xml
<mark_cache_size>5368709120</mark_cache_size>
```


## max_concurrent_queries

The maximum number of simultaneously processed requests.

**Example**

```xml
<max_concurrent_queries>100</max_concurrent_queries>
```


## max_connections

The maximum number of inbound connections.

**Example**

```xml
<max_connections>4096</max_connections>
```


## max_open_files

The maximum number of open files.

By default: `maximum`.

We recommend using this option in Mac OS X, since the `getrlimit()` function returns an incorrect value.

**Example**

```xml
<max_open_files>262144</max_open_files>
```


## max_table_size_to_drop

Restriction on deleting tables.

If the size of a [MergeTree](../../operations/table_engines/mergetree.md) table exceeds `max_table_size_to_drop` (in bytes), you can't delete it using a DROP query.

If you still need to delete the table without restarting the ClickHouse server, create the `<clickhouse-path>/flags/force_drop_table` file and run the DROP query.

Default value: 50 GB.

The value 0 means that you can delete all tables without any restrictions.

**Example**

```xml
<max_table_size_to_drop>0</max_table_size_to_drop>
```


## merge_tree

Fine tuning for tables in the [ MergeTree](../../operations/table_engines/mergetree.md).

For more information, see the MergeTreeSettings.h header file.

**Example**

```xml
<merge_tree>
    <max_suspicious_broken_parts>5</max_suspicious_broken_parts>
</merge_tree>
```


## openSSL

SSL client/server configuration.

Support for SSL is provided by the `libpoco` library. The interface is described in the file [SSLManager.h](https://github.com/ClickHouse-Extras/poco/blob/master/NetSSL_OpenSSL/include/Poco/Net/SSLManager.h)

Keys for server/client settings:

- privateKeyFile – The path to the file with the secret key of the PEM certificate. The file may contain a key and certificate at the same time.
- certificateFile – The path to the client/server certificate file in PEM format. You can omit it if `privateKeyFile` contains the certificate.
- caConfig – The path to the file or directory that contains trusted root certificates.
- verificationMode – The method for checking the node's certificates. Details are in the description of the [Context](https://github.com/ClickHouse-Extras/poco/blob/master/NetSSL_OpenSSL/include/Poco/Net/Context.h) class. Possible values: ``none``, ``relaxed``, ``strict``, ``once``.
- verificationDepth – The maximum length of the verification chain. Verification will fail if the certificate chain length exceeds the set value.
- loadDefaultCAFile – Indicates that built-in CA certificates for OpenSSL will be used. Acceptable values: `true`, `false`.  |
- cipherList – Supported OpenSSL encryptions. For example: `ALL:!ADH:!LOW:!EXP:!MD5:@STRENGTH`.
- cacheSessions – Enables or disables caching sessions. Must be used in combination with ``sessionIdContext``. Acceptable values: `true`, `false`.
- sessionIdContext – A unique set of random characters that the server appends to each generated identifier. The length of the string must not exceed ``SSL_MAX_SSL_SESSION_ID_LENGTH``. This parameter is always recommended, since it helps avoid problems both if the server caches the session and if the client requested caching. Default value: ``${application.name}``.
- sessionCacheSize – The maximum number of sessions that the server caches. Default value: 1024\*20. 0 – Unlimited sessions.
- sessionTimeout – Time for caching the session on the server.
- extendedVerification – Automatically extended verification of certificates after the session ends. Acceptable values: `true`, `false`.
- requireTLSv1 – Require a TLSv1 connection. Acceptable values: `true`, `false`.
- requireTLSv1_1 – Require a TLSv1.1 connection. Acceptable values: `true`, `false`.
- requireTLSv1 – Require a TLSv1.2 connection. Acceptable values: `true`, `false`.
- fips – Activates OpenSSL FIPS mode. Supported if the library's OpenSSL version supports FIPS.
- privateKeyPassphraseHandler – Class (PrivateKeyPassphraseHandler subclass) that requests the passphrase for accessing the private key. For example: ``<privateKeyPassphraseHandler>``, ``<name>KeyFileHandler</name>``, ``<options><password>test</password></options>``, ``</privateKeyPassphraseHandler>``.
- invalidCertificateHandler – Class (subclass of CertificateHandler) for verifying invalid certificates. For example: `` <invalidCertificateHandler> <name>ConsoleCertificateHandler</name>  </invalidCertificateHandler>`` .
- disableProtocols – Protocols that are not allowed to use.
- preferServerCiphers – Preferred server ciphers on the client.

**Example of settings:**

```xml
<openSSL>
    <server>
        <!-- openssl req -subj "/CN=localhost" -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout /etc/clickhouse-server/server.key -out /etc/clickhouse-server/server.crt -->
        <certificateFile>/etc/clickhouse-server/server.crt</certificateFile>
        <privateKeyFile>/etc/clickhouse-server/server.key</privateKeyFile>
        <!-- openssl dhparam -out /etc/clickhouse-server/dhparam.pem 4096 -->
        <dhParamsFile>/etc/clickhouse-server/dhparam.pem</dhParamsFile>
        <verificationMode>none</verificationMode>
        <loadDefaultCAFile>true</loadDefaultCAFile>
        <cacheSessions>true</cacheSessions>
        <disableProtocols>sslv2,sslv3</disableProtocols>
        <preferServerCiphers>true</preferServerCiphers>
    </server>
    <client>
        <loadDefaultCAFile>true</loadDefaultCAFile>
        <cacheSessions>true</cacheSessions>
        <disableProtocols>sslv2,sslv3</disableProtocols>
        <preferServerCiphers>true</preferServerCiphers>
        <!-- Use for self-signed: <verificationMode>none</verificationMode> -->
        <invalidCertificateHandler>
            <!-- Use for self-signed: <name>AcceptCertificateHandler</name> -->
            <name>RejectCertificateHandler</name>
        </invalidCertificateHandler>
    </client>
</openSSL>
```


## part_log

Logging events that are associated with [MergeTree](../../operations/table_engines/mergetree.md). For instance, adding or merging data. You can use the log to simulate merge algorithms and compare their characteristics. You can visualize the merge process.

Queries are logged in the ClickHouse table, not in a separate file.

Columns in the log:

- event_time – Date of the event.
- duration_ms – Duration of the event.
- event_type – Type of event. 1 – new data part; 2 – merge result; 3 – data part downloaded from replica; 4 – data part deleted.
- database_name – The name of the database.
- table_name – Name of the table.
- part_name – Name of the data part.
- size_in_bytes – Size of the data part in bytes.
- merged_from – An array of names of data parts that make up the merge (also used when downloading a merged part).
- merge_time_ms – Time spent on the merge.

Use the following parameters to configure logging:

- database – Name of the database.
- table – Name of the table.
- partition_by – Sets a [custom partitioning key](../../operations/table_engines/custom_partitioning_key.md).
- flush_interval_milliseconds – Interval for flushing data from memory to the disk.

**Example**

```xml
<part_log>
    <database>system</database>
    <table>part_log</table>
    <partition_by>toMonday(event_date)</partition_by>
    <flush_interval_milliseconds>7500</flush_interval_milliseconds>
</part_log>
```


## path

The path to the directory containing data.

!!! note
    The trailing slash is mandatory.

**Example**

```xml
<path>/var/lib/clickhouse/</path>
```


## query_log

Setting for logging queries received with the [log_queries=1](../settings/settings.md) setting.

Queries are logged in the ClickHouse table, not in a separate file.

Use the following parameters to configure logging:

- database – Name of the database.
- table – Name of the table.
- partition_by – Sets a [custom partitioning key](../../operations/table_engines/custom_partitioning_key.md).
- flush_interval_milliseconds – Interval for flushing data from memory to the disk.

If the table doesn't exist, ClickHouse will create it. If the structure of the query log changed when the ClickHouse server was updated, the table with the old structure is renamed, and a new table is created automatically.

**Example**

```xml
<query_log>
    <database>system</database>
    <table>query_log</table>
    <partition_by>toMonday(event_date)</partition_by>
    <flush_interval_milliseconds>7500</flush_interval_milliseconds>
</query_log>
```


## remote_servers

Configuration of clusters used by the Distributed table engine.

For more information, see the section "[Table engines/Distributed](../../operations/table_engines/distributed.md)".

**Example**

```xml
<remote_servers incl="clickhouse_remote_servers" />
```

For the value of the `incl` attribute, see the section "[Configuration files](../configuration_files.md#configuration_files)".


## timezone

The server's time zone.

Specified as an IANA identifier for the UTC time zone or geographic location (for example, Africa/Abidjan).

The time zone is necessary for conversions between String and DateTime formats when DateTime fields are output to text format (printed on the screen or in a file), and when getting DateTime from a string. In addition, the time zone is used in functions that work with the time and date if they didn't receive the time zone in the input parameters.

**Example**

```xml
<timezone>Europe/Moscow</timezone>
```


## tcp_port

Port for communicating with clients over the TCP protocol.

**Example**

```xml
<tcp_port>9000</tcp_port>
```


## tmp_path

Path to temporary data for processing large queries.

!!! note
    The trailing slash is mandatory.

**Example**

```xml
<tmp_path>/var/lib/clickhouse/tmp/</tmp_path>
```


## uncompressed_cache_size

Cache size (in bytes) for uncompressed data used by table engines from the [MergeTree](../../operations/table_engines/mergetree.md).

There is one shared cache for the server. Memory is allocated on demand. The cache is used if the option [use_uncompressed_cache](../settings/settings.md) is enabled.

The uncompressed cache is advantageous for very short queries in individual cases.

**Example**

```xml
<uncompressed_cache_size>8589934592</uncompressed_cache_size>
```

## user_files_path

The directory with user files. Used in the  table function [file()](../../query_language/table_functions/file.md).

**Example**

```xml
<user_files_path>/var/lib/clickhouse/user_files/</user_files_path>
```


## users_config

Path to the file that contains:

- User configurations.
- Access rights.
- Settings profiles.
- Quota settings.

**Example**

```xml
<users_config>users.xml</users_config>
```


## zookeeper

Configuration of ZooKeeper servers.

ClickHouse uses ZooKeeper for storing replica metadata when using replicated tables.

This parameter can be omitted if replicated tables are not used.

For more information, see the section "[Replication](../../operations/table_engines/replication.md)".

**Example**

```xml
<zookeeper incl="zookeeper-servers" optional="true" />
```


[Original article](https://clickhouse.yandex/docs/en/operations/server_settings/settings/) <!--hide-->