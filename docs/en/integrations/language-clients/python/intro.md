---
sidebar_label: Introduction
sidebar_position: 10
keywords: [clickhouse, python, client, connect, integrate]
slug: /en/integrations/language-clients/python/intro
description: The ClickHouse Connect project suite for connecting Python to ClickHouse
---

# Python Integration with ClickHouse Connect

## Introduction

ClickHouse Connect is a suite of Python packages providing interoperability with a wide range of Python applications.
The three primary components are:

- A low level driver in the package `clickhouse_connect.driver`. This package includes a basic client that handles
  all ClickHouse Connect requests to the ClickHouse Server, as well assorted helper classes and utility functions.
- A limited [SQLAlchemy](https://www.sqlalchemy.org/) dialect in the package `clickhouse_connect.cc_sqlalchemy`. This
  package focuses implements query/cursor functionality, and does not generally support SQLAlchemy DDL and ORM
  operations.
  (SQLAlchemy is targeted toward OLTP databased, and we recommend more specialized tools and frameworks to manage
  the ClickHouse OLAP database.)
- A [Superset](https://superset.apache.org/) EngineSpec in the `clickhouse_connect.cc_superset`. This package will
  automatically add a **ClickHouse Connect** Superset connector when ClickHouses Connect installed. This EngineSpec
  supports all core Superset query functionality, but does not currently support certain advanced features such as file
  upload to a ClickHouse table.

## Requirements and Compatibility

| Python    | | Platform¹   | | ClickHouse | | SQLAlchemy² | | Apache Superset | |
|--:|:--|--:|:--|--:|:--|--:|:--|--:|:--|
| 2.x, <3.7 | ❌ | Linux (x86)     | ✅      | <22.3³      | 🟡 | <1.3       | ❌      | <1.4     | ❌      |
| 3.7.x     | ✅ | Linux (Aarch64) | ✅      | 22.3.x     | ✅  | 1.3.x      | ✅      | 1.4.x    | ✅      |
| 3.8.x     | ✅ | MacOS (x86)     | ✅      | 22.4-22.6³ | 🟡   | 1.4.x      | ✅      | 1.5.x    | ✅      |
| 3.9.x     | ✅ | MacOs (M1)      | ✅      | 22.7.x     | ✅  | >=2.x      |  ❌      | 2.0.x    | ✅      |
| 3.10.x    | ✅ | Windows         | ✅      | 22.8.x     | ✅   |            |        |          |        |
|           |    |                 |         | 22.9.x     |  ✅  |            |        |          |        |


¹ClickHouse Connect has been tested against the listed platforms.  Because it can run in pure Python (without the
optional C optimizations), it is likely to work on any support Python installation.

²Again SQLAlchemy support is limited primarily to query functionality.  The full SQLAlchemy API is not supported.

³ClickHouse Connect has been tested against all currently supported ClickHouse versions. Because it uses the HTTP
protocol, it should also work correctly for most other versions of ClickHouse, although there may be some
incompatibilities with certain advanced data types.


## Installation

Install ClickHouse Connect from PyPI via pip:

`pip install clickhouse-connect`

ClickHouse Connect can also be installed from source by checking out the
[GitHub repository](https://github.com/ClickHouse/clickhouse-connect) and running `pip install .`
in the project root directory.

## Support Policy

ClickHouse Connect is currently in beta and only the current beta release is actively supported. Please update to the latest
version before reported any issues. Issues should be filed in
the [GitHub project](https://github.com/ClickHouse/clickhouse-connect/issues).  
Future releases of ClickHouse Connect are guaranteed to be compatible with actively supported ClickHouse versions at the
time of release (generally the three most recent `stable` and two most recent `lts` releases).

## Basic Usage

Use a ClickHouse Connect client instance to connect to the ClickHouse server:

```python
import clickhouse_connect

client = clickhouse_connect.get_client(host='localhost', username='default', password='password')
```

To execute a ClickHouse SQL command, use the client `command` method:

```python
client.command('CREATE TABLE new_table (key UInt32, value String, metric Float64) ENGINE MergeTree ORDER BY key')
```

To insert batch data, use the client `insert` method with a two-dimensional array of rows and values:

```python
row1 = [1000, 'String Value 1000', 5.233]
row2 = [2000, 'String Value 2000', -107.04]
data = [row1, row2]
client.insert('new_table', data, column_names=['key', 'value', 'metric']) 
```

To retrieve data using ClickHouse SQL, use the client `query` method:

```python
result = client.query('SELECT max(key), avg(metric) FROM new_table')
result.result_set
Out[13]: [(2000, -50.9035)]
```

