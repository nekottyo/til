volume option の話
=====

https://docs.docker.com/docker-for-mac/osxfs-caching/

-v した時のオプションで `delegated`, `cached`, `consistent` などが取れる

`-v /path/to/host:/path/to/container:<ro|deletaged|cached|consistent>` 

## `delegated`

- コンテナからの書き込みが早くなる代わりにホストの書き込みは遅延する
- クラッシュするタイミングではデータがロストする可能性がある
- ephemeral なデータや build artifact のような再現性のあるデータの置き場に適している


## `cached`

- `delegated` よりも厳しい制約がある
- 重い読み込みのパフォーマンスが改善する
- ホスト - コンテナ間の一時的な整合性を捨てて読み込みのパフォーマンスを提供する

## `consistent`

- コンテナとホスト間は常に同期される。
- オーバーヘッドすごい
