# tbls [![Build Status](https://travis-ci.org/k1LoW/tbls.svg?branch=master)](https://travis-ci.org/k1LoW/tbls) [![GitHub release](https://img.shields.io/github/release/k1LoW/tbls.svg)](https://github.com/k1LoW/tbls/releases) [![codecov](https://codecov.io/gh/k1LoW/tbls/branch/master/graph/badge.svg)](https://codecov.io/gh/k1LoW/tbls)

`tbls` is a CI-Friendly tool for document a database, written in Go.

Key features of `tbls` are:

- Single binary
- Document in GitHub Friendly Markdown format
- CI-Friendly

[Usage](#usage) | [Sample](sample/postgres/) | [Integration with CI tools](#integration-with-ci-tools) | [Installation](#installation) | [Database Support](#database-support)

## Usage

```console
$ tbls
tbls is a tool for document a database, written in Go.

Usage:
  tbls [command]

Available Commands:
  diff        diff database and document
  doc         document a database
  help        Help about any command
  lint        check database document
  out         analyzes a database and output
  version     print tbls version

Flags:
  -h, --help   help for tbls

Use "tbls [command] --help" for more information about a command.
```

### Document a database schema

`tbls doc` analyzes a database and generate document in GitHub Friendly Markdown format.

```console
$ tbls doc postgres://user:pass@hostname:5432/dbname ./dbdoc
```

If you can use Graphviz `dot` command, `tbls doc` generate ER diagram images at the same time.

Sample [document](sample/postgres/) and [schema](testdata/pg.sql).

> NOTICE: If you are using a symbol such as `#` `<` in database password, URL-encode the password

### Diff database schema and document

`tbls diff` shows the difference between database schema and generated document.

```console
$ tbls diff postgres://user:pass@hostname:5432/dbname ./dbdoc
```

**Notice:** `tbls diff` shows the difference Markdown documents only.

## Integration with CI tools

1. Commit document using `tbls doc`.
2. Check document updates using `tbls diff`

Set following code to [`your-ci-config.yml`](https://github.com/k1LoW/tbls/blob/ffad9d7463bb22baa236c7b673fd679f1850f37d/.travis.yml#L19).

```sh
$ tbls diff postgres://user:pass@localhost:5432/testdb?sslmode=disable ./dbdoc
```

Makefile sample is following

``` makefile
doc: ## Document database schema
	tbls doc postgres://user:pass@localhost:5432/testdb?sslmode=disable ./dbdoc

testdoc: ## Test database schema document
	tbls diff postgres://user:pass@localhost:5432/testdb?sslmode=disable ./dbdoc
```

**Tips:** If the order of the columns does not match, you can use the `--sort` option.

## How to specify DSN and Document path

### 1. Command arguments

``` console
$ tbls doc my://root:mypass@localhost:33306/testdb sample/mysql
```

### 2. Use `.tbls.yml` or set `--config` option

Put `.tbls.yml` on execute directory or specify with the `--config` option.

YAML format is follows

``` yaml
---
dsn: my://root:mypass@localhost:33306/testdb
docPath: sample/mysql
```

``` yaml
---
dsn: my://${MYSQL_USER}:${MYSQL_PASSWORD}@localhost:33306/${MYSQL_DATABASE}
docPath: sample/mysql
```

### 3. Envirionment

``` console
$ env TBLS_DSN=my://root:mypass@localhost:33306/testdb TBLS_DOC_PATH=sample/mysql tbls doc
```

## Add additional data (relations, comments) to schema

To add additional data to the schema, add settings to `.tbls.yml` or `--config` like [YAML file](testdata/additional_data.yml) (`relations`, `comments`)

## Lint database document

To check database document, add settings to `.tbls.yml` or `--config` like [YAML file](testdata/additional_data.yml) (`lint`)

## Installation

```console
$ go get github.com/k1LoW/tbls
```

or

```console
$ brew install k1LoW/tap/tbls
```

## Database Support

- PostgreSQL
- MySQL
- SQLite
