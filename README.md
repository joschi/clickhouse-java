# ClickHouse Java Libraries

[![GitHub release (latest SemVer including pre-releases)](https://img.shields.io/github/v/release/ClickHouse/clickhouse-java?style=plastic&include_prereleases&label=Latest%20Release)](https://github.com/ClickHouse/clickhouse-java/releases/) [![GitHub release (by tag)](https://img.shields.io/github/downloads/ClickHouse/clickhouse-java/latest/total?style=plastic)](https://github.com/ClickHouse/clickhouse-java/releases/) [![Coverage](https://sonarcloud.io/api/project_badges/measure?project=ClickHouse_clickhouse-jdbc&metric=coverage)](https://sonarcloud.io/summary/new_code?id=ClickHouse_clickhouse-jdbc) [![Sonatype Nexus (Snapshots)](https://img.shields.io/nexus/s/com.clickhouse/clickhouse-java?style=plastic&label=Nightly%20Build&server=https%3A%2F%2Fs01.oss.sonatype.org)](https://s01.oss.sonatype.org/content/repositories/snapshots/com/clickhouse/) [![GitHub milestone](https://img.shields.io/github/milestones/progress-percent/ClickHouse/clickhouse-java/4?style=social)](https://github.com/ClickHouse/clickhouse-java/milestone/4)

Java libraries for connecting to ClickHouse and processing data in various formats. Java client is async, lightweight, and low-overhead library for ClickHouse; while JDBC and R2DBC drivers are built on top of the Java client with more dependencies and features. Java 8 or higher is required to use the libraries. In addition, please use ClickHouse 20.7+ or any of [active releases](https://github.com/ClickHouse/ClickHouse/pulls?q=is%3Aopen+is%3Apr+label%3Arelease).

![image](https://user-images.githubusercontent.com/4270380/212460181-2b806482-bc1c-492c-bd69-cdeb2c8845b5.png)

## Features

| Category          | Feature                                                                             | Supported          | Remark                                                                                                                                                                                                                   |
| ----------------- | ----------------------------------------------------------------------------------- | ------------------ |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| API               | [JDBC](https://docs.oracle.com/javase/8/docs/technotes/guides/jdbc/)                | :white_check_mark: |                                                                                                                                                                                                                          |
|                   | [R2DBC](https://r2dbc.io/)                                                          | :white_check_mark: | supported since 0.4.0                                                                                                                                                                                                    |
| Protocol          | [HTTP](https://clickhouse.com/docs/en/interfaces/http/)                             | :white_check_mark: | recommended, defaults to `java.net.HttpURLConnection` and it can be changed to `java.net.http.HttpClient`(unstable) or `Apache HTTP Client 5`. Note that the latter was added in 0.4.0 to support custom socket options. |
|                   | [gRPC](https://clickhouse.com/docs/en/interfaces/grpc/)                             | :white_check_mark: | :warning: experimental, works with 22.3+, known to has issue with lz4 compression and may cause high memory usage on server. Planed to be removed from version 0.7.0                                                     |
|                   | [TCP/Native](https://clickhouse.com/docs/en/interfaces/tcp/)                        | :x: | `clickhouse-cli-client`(wrapper of ClickHouse native command-line client) was added in 0.3.2-patch10.                                                                                                                    |
|                   | [Local/File](https://clickhouse.com/docs/en/operations/utilities/clickhouse-local/) | :x:                | `clickhouse-cli-client` will be enhanced to support `clickhouse-local`                                                                                                                                                   |
| Compatibility     | Server < 20.7                                                                       | :x:                | use 0.3.1-patch(or 0.2.6 if you're stuck with JDK 7)                                                                                                                                                                     |
|                   | Server >= 20.7                                                                      | :white_check_mark: | use 0.3.2 or above. All [active releases](https://github.com/ClickHouse/ClickHouse/pulls?q=is%3Aopen+is%3Apr+label%3Arelease) are supported.                                                                             |
| Compression       | [lz4](https://lz4.github.io/lz4/)                                                   | :white_check_mark: | default                                                                                                                                                                                                                  |
|                   | [zstd](https://facebook.github.io/zstd/)                                            | :white_check_mark: | supported since 0.4.0, works with ClickHouse 22.10+                                                                                                                                                                      |
| Data Format       | RowBinary                                                                           | :white_check_mark: | `RowBinaryWithNamesAndTypes` for query and `RowBinary` for insertion                                                                                                                                                     |
|                   | TabSeparated                                                                        | :white_check_mark: | :warning: does not support as many data types as RowBinary                                                                                                                                                               |
| Data Type         | AggregateFunction                                                                   | :white_check_mark: | :warning: does not support `SELECT * FROM table ...`                                                                                                                                                                     |
|                   | Array(\*)                                                                           | :white_check_mark: |                                                                                                                                                                                                                          |
|                   | Bool                                                                                | :white_check_mark: |                                                                                                                                                                                                                          |
|                   | Date\*                                                                              | :white_check_mark: |                                                                                                                                                                                                                          |
|                   | DateTime\*                                                                          | :white_check_mark: |                                                                                                                                                                                                                          |
|                   | Decimal\*                                                                           | :white_check_mark: | `SET output_format_decimal_trailing_zeros=1` in 21.9+ for consistency                                                                                                                                                    |
|                   | Enum\*                                                                              | :white_check_mark: | can be treated as both string and integer                                                                                                                                                                                |
|                   | Geo Types                                                                           | :white_check_mark: | Point, Ring, Polygon, and MultiPolygon                                                                                                                                                                                   |
|                   | Int\*, UInt\*                                                                       | :white_check_mark: | UInt64 is mapped to `long`                                                                                                                                                                                               |
|                   | IPv\*                                                                               | :white_check_mark: |                                                                                                                                                                                                                          |
|                   | Map(\*)                                                                             | :white_check_mark: |                                                                                                                                                                                                                          |
|                   | Nested(\*)                                                                          | :white_check_mark: | :warning: broken before 0.4.1                                                                                                                                                                                            |
|                   | Object('JSON')                                                                      | :white_check_mark: | supported since 0.3.2-patch8                                                                                                                                                                                             |
|                   | SimpleAggregateFunction                                                             | :white_check_mark: |                                                                                                                                                                                                                          |
|                   | \*String                                                                            | :white_check_mark: | :warning: requires `use_binary_string=true` for binary string support since v0.4.0                                                                                                                                       |
|                   | Tuple(\*)                                                                           | :white_check_mark: |                                                                                                                                                                                                                          |
|                   | UUID                                                                                | :white_check_mark: |                                                                                                                                                                                                                          |
| High Availability | Load Balancing                                                                      | :white_check_mark: | supported since 0.3.2-patch10                                                                                                                                                                                            |
|                   | Failover                                                                            | :white_check_mark: | supported since 0.3.2-patch10                                                                                                                                                                                            |
| Transaction       | Transaction                                                                         | :white_check_mark: | supported since 0.3.2-patch11, use ClickHouse 22.7+ for native implicit transaction support                                                                                                                              |
|                   | Savepoint                                                                           | :x:                |                                                                                                                                                                                                                          |
|                   | XAConnection                                                                        | :x:                |                                                                                                                                                                                                                          |
| Misc.             | Centralized Configuration                                                           | :white_check_mark: | supported since 0.4.2, limited to JDBC driver and requires custom server setting `custom_jdbc_config` for all connected JDBC clients - [#1290](../../pull/1290)                                                          |
|                   | INFILE & OUTFILE                                                                    | :white_check_mark: | supported since 0.4.2, limited to JDBC driver and requires `localFile` option - [#1291](../../pull/1291)                                                                                                                 |
|                   | Implicit Type Conversion                                                            | :white_check_mark: | String/number to Date/Time/Timestamp and more                                                                                                                                                                            |
|                   | Object mapping                                                                      | :white_check_mark: | supported since 0.4.6, slow and limited to simple data types                                                                                                                                                             |

## Planed to be removed

| Feature                        | Version | Remark                                           |
|--------------------------------|---------|--------------------------------------------------|
| Clickhouse ClI Client package  | 0.7.0   | Use clickhouse-client directly instead           | 
| ClickHouse GRPC Client package | 0.7.0   | Please use the clickhouse http protocol instead  |
## Usage

The library can be downloaded from both [Github Releases](../../releases) and [Maven Central](https://repo1.maven.org/maven2/com/clickhouse/). Development snapshots(aka. nightly build) are available on [Sonatype OSSRH](https://s01.oss.sonatype.org/content/repositories/snapshots/com/clickhouse/).

```xml
<repositories>
    <repository>
        <id>ossrh</id>
        <name>Sonatype OSSRH</name>
        <url>https://s01.oss.sonatype.org/content/repositories/snapshots/</url>
    </repository>
</repositories>
```

### Java Client

See [examples](../../tree/main/examples/client) and the [docs on the ClickHouse website](https://clickhouse.com/docs/en/integrations/language-clients/java/client).

### JDBC Driver

See [examples](../../tree/main/examples/jdbc) and the [docs on the ClickHouse website](https://clickhouse.com/docs/en/integrations/language-clients/java/jdbc).

## Contributing

Check out our [contributing guide](./CONTRIBUTING.md).
