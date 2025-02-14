# sql-metadata

[![PyPI](https://img.shields.io/pypi/v/sql_metadata.svg)](https://pypi.python.org/pypi/sql_metadata)
[![Tests](https://github.com/macbre/sql-metadata/actions/workflows/python-ci.yml/badge.svg)](https://github.com/macbre/sql-metadata/actions/workflows/python-ci.yml)
<a href="https://github.com/psf/black"><img alt="Code style: black" src="https://img.shields.io/badge/code%20style-black-000000.svg"></a>
[![Maintenance](https://img.shields.io/badge/maintained%3F-yes-green.svg)](https://github.com/macbre/sql-metadata/graphs/commit-activity)
[![Downloads](https://pepy.tech/badge/sql-metadata/month)](https://pepy.tech/project/sql-metadata)

Uses tokenized query returned by [`python-sqlparse`](https://github.com/andialbrecht/sqlparse) and generates query metadata.
**Extracts column names and tables** used by the query. Provides a helper for **normalization of SQL queries** and **tables aliases resolving**.

Supported queries syntax:

* MySQL
* PostgreSQL
* [Apache Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML)

## Usage

```
pip install sql-metadata
```

```python
>>> import sql_metadata

>>> sql_metadata.get_query_tokens("SELECT * FROM foo")
[<DML 'SELECT' at 0x7F14FFDEB808>, <Wildcard '*' at 0x7F14FFDEB940>, <Keyword 'FROM' at 0x7F14FFDEBBB0>, <Name 'foo' at 0x7F14FFDEB9A8>]

>>> sql_metadata.get_query_columns("SELECT test, id FROM foo, bar")
[u'test', u'id']

>>> sql_metadata.get_query_tables("SELECT a.* FROM product_a.users AS a JOIN product_b.users AS b ON a.ip_address = b.ip_address")
['product_a.users', 'product_b.users']

>>> sql_metadata.get_query_columns("INSERT /* VoteHelper::addVote xxx */  INTO `page_vote` (article_id,user_id,`time`) VALUES ('442001','27574631','20180228130846')")
['article_id', 'user_id', 'time']

>>> sql_metadata.get_query_columns("SELECT a.* FROM product_a.users AS a JOIN product_b.users AS b ON a.ip_address = b.ip_address")
['a.*', 'a.ip_address', 'b.ip_address']

>>> sql_metadata.get_query_tables("SELECT test, id FROM foo, bar")
[u'foo', u'bar']

>>> sql_metadata.get_query_limit_and_offset('SELECT foo_limit FROM bar_offset LIMIT 50 OFFSET 1000')
(50, 1000)

>>> sql_metadata.get_query_limit_and_offset('SELECT foo_limit FROM bar_offset limit 2000,50')
(50, 2000)

>>> sql_metadata.get_query_table_aliases("SELECT test FROM foo AS f")
{'f': 'foo'}
```

> See `test/test_query.py` file for more examples of a bit more complex queries.

### Queries normalization

```python
>>> from sql_metadata import generalize_sql
>>> generalize_sql('SELECT /* Test */ foo FROM bar WHERE id in (1, 2, 56)')
'SELECT foo FROM bar WHERE id in (XYZ)'
```

> See `test/test_normalization.py` file for more examples of a bit more complex queries.

## Stargazers over time

[![Stargazers over time](https://starchart.cc/macbre/sql-metadata.svg)](https://starchart.cc/macbre/sql-metadata)
