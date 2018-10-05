testinfra でsudo したあとに test を実行する
===

ansible で以下のような playbook を書いた場合

```yaml
- name: copy hoge
  copy:
    src: path/to/hoge.txt
    dest: /path/to/hoge.txt
    mode: 0600
    owner: root
    group: root
```

testinfra が対象ホストに root 以外で接続している場合

```python
def test_hoge(host):
    host.file('/path/to/hoge').exists
```

が読めなくてエラーになる。これをうまく回避してあげる場合は、

```python
def test_hoge(host):
    with host.sudo():
        host.file('/path/to/hoge').exists
```

とすると、sudo 状態で `/path/to/hoge` を見に行くことが出来る。

https://testinfra.readthedocs.io/en/latest/modules.html#sudo
