database/sql のチューニング
===

# 前提

DataBase は connection pool を持っている(idle and open connection)。

SQL の実行などの DB タスクを実行する時、connection は open connection としてマークされ、タスクが終わると idle connection に戻る。

sql.DB は DB タスクを実行する際、初めに idle connection が利用可能化をチェックし、利用可能であれば、 Go は open connection としてマークし、タスクを実行する。

idle connection が connection pool に無い場合は、 Go は新しいコネクションを作成し、open connection としてマークする。


-----
# `SetConnMaxLifetime`

- コネクションが作られてから削除されるまでのライフタイム
- 最大接続数 * 1 Second 位が妥当


# `SetMaxIdleConns`

- connection を再利用できるため、メモリ効率が良くなり、allocate 回数が減る
- idle connection を維持する事自体もメモリを消費するため、単純に大きくすればいいというわけではない
	- コネクションが正常ではなくなった場合、Go は接続を2回試してだめだったら connection pool から削除して新しく connection を作るが、`MaxIdleConns` が大きかった場合より大きなリソースを消費する可能性がある
	- `MaxIdleConns <= MaxOpenConns` にする必要がある
 
# `SetMaxOpenConns`

- 数を増やしたほうが単純に同時接続数が増えるためパフォーマンスが上がる
- 負荷も上がる


----- 

- https://golang.org/pkg/database/sql/#DB.SetConnMaxLifetime
- http://go-database-sql.org/connection-pool.html
- http://dsas.blog.klab.org/archives/2018-02/configure-sql-db.html
- https://www.alexedwards.net/blog/configuring-sqldb
