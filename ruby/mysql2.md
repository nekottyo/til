mysql2 gem の接続先の書き方(Rails)
=====

`config/database.yml` で host の書き方を `localhost` にすると `/var/run/mysqld/mysqld.sock` を見に行くが

```yaml
development:
  adapter: mysql2
  host: localhost
```

`127.0.0.1` にすると socket 経由ではなく port `3306`  に行く

```yaml
development:
  adapter: mysql2
  host: 127.0.0.1
```

