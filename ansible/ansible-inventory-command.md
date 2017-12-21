# ansible-inventory

inventory file の情報をいい感じに整形して出力してくれるコマンド
通常の `ansible-*` と同様に `-i` で inventory file 指定や、 `-l` で limit 指定が可能
inventory が 従来の ini 形式ではなく yaml inventory だったら `-y` をつける。outputも yaml になる


[enginyoyen/ansible-best-practises](https://github.com/enginyoyen/ansible-best-practises) を使って試す

# `--graph`
group と host の紐付けを tree 表示する。children に指定した入れ子も解決してくれる。

```
ansible-best-practises> ansible-inventory -i production.ini --graph
@all:
  |--@postgresql:
  |  |--postgresql000.prod.example.com
  |--@ungrouped:
  |--@webservers:
  |  |--webserver000.prod.example.com
  |  |--webserver001.prod.example.com
  |  |--webserver002.prod.example.com
  |  |--webserver003.prod.example.com
  |  |--webserver004.prod.example.com
```


# `--host HOSTNAME`
単一の host を指定し、group_vars, host_vars を集めて変数を表示する。

```json
ansible-best-practises> ansible-inventory -i production.ini --host webserver000.prod.example.com
{
    "apt_default_key_urls": [
        "http://nginx.org/keys/nginx_signing.key"
    ],
    "apt_default_packages": [
        "nginx=1.6.2-1~precise"
    ],
    "apt_default_repositories": [
        "deb http://nginx.org/packages/ubuntu/ precise nginx",
        "deb-src http://nginx.org/packages/ubuntu/ precise nginx"
    ],
    "apt_update_source_list": "template",
    "apt_update_source_list_mirror": "http://nl.archive.ubuntu.com/ubuntu/",
    "environment": "prod",
    "nginx_apt_state": "present",
    "nginx_etc": "/etc/nginx",
    "nginx_install_repo": "nginx",
    "nginx_max_clients": 1024,
    "unattended_install": true,
    "webserver000_host_vars": "000_vars"
}
```

## `--list`
`--host` に加えて group や meta も含めた全ての情報を dump する

<details><summary>長いので省略</summary><div>

```json
ansible-best-practises> ansible-inventory -i production.ini --list
{
    "_meta": {
        "hostvars": {
            "postgresql000.prod.example.com": {
                "apt_default_packages": [
                    "python-apt",
                    "unattended-upgrades",
                    "language-pack-en-base",
                    "unzip"
                ],
                "apt_update_source_list": "template",
                "apt_update_source_list_mirror": "http://nl.archive.ubuntu.com/ubuntu/",
                "environment": "prod",
                "postgresql_cluster_reset": true,
                "postgresql_data_directory": "/mnt/db/postgresql/{{postgresql_version}}/{{postgresql_cluster_name}}",
                "postgresql_databases": [
                    {
                        "name": "products"
                    },
                    {
                        "name": "partners"
                    }
                ],
                "postgresql_listen_addresses": [
                    "0.0.0.0"
                ],
                "postgresql_pg_hba_trust_hosts": [
                    "0.0.0.0/0"
                ],
                "postgresql_user_privileges": [
                    {
                        "db": "products",
                        "name": "jane"
                    },
                    {
                        "db": "partners",
                        "name": "joe"
                    }
                ],
                "postgresql_users": [
                    {
                        "name": "jane",
                        "pass": "{{ postgresql_users_jane }}"
                    },
                    {
                        "name": "joe",
                        "pass": "{{ postgresql_users_joe }}"
                    }
                ],
                "unattended_install": true
            },
            "webserver000.prod.example.com": {
                "apt_default_key_urls": [
                    "http://nginx.org/keys/nginx_signing.key"
                ],
                "apt_default_packages": [
                    "nginx=1.6.2-1~precise"
                ],
                "apt_default_repositories": [
                    "deb http://nginx.org/packages/ubuntu/ precise nginx",
                    "deb-src http://nginx.org/packages/ubuntu/ precise nginx"
                ],
                "apt_update_source_list": "template",
                "apt_update_source_list_mirror": "http://nl.archive.ubuntu.com/ubuntu/",
                "environment": "prod",
                "nginx_apt_state": "present",
                "nginx_etc": "/etc/nginx",
                "nginx_install_repo": "nginx",
                "nginx_max_clients": 1024,
                "unattended_install": true,
                "webserver000_host_vars": "000_vars"
            },
            "webserver001.prod.example.com": {
                "apt_default_key_urls": [
                    "http://nginx.org/keys/nginx_signing.key"
                ],
                "apt_default_packages": [
                    "nginx=1.6.2-1~precise"
                ],
                "apt_default_repositories": [
                    "deb http://nginx.org/packages/ubuntu/ precise nginx",
                    "deb-src http://nginx.org/packages/ubuntu/ precise nginx"
                ],
                "apt_update_source_list": "template",
                "apt_update_source_list_mirror": "http://nl.archive.ubuntu.com/ubuntu/",
                "environment": "prod",
                "nginx_apt_state": "present",
                "nginx_etc": "/etc/nginx",
                "nginx_install_repo": "nginx",
                "nginx_max_clients": 1024,
                "unattended_install": true
            },
            "webserver002.prod.example.com": {
                "apt_default_key_urls": [
                    "http://nginx.org/keys/nginx_signing.key"
                ],
                "apt_default_packages": [
                    "nginx=1.6.2-1~precise"
                ],
                "apt_default_repositories": [
                    "deb http://nginx.org/packages/ubuntu/ precise nginx",
                    "deb-src http://nginx.org/packages/ubuntu/ precise nginx"
                ],
                "apt_update_source_list": "template",
                "apt_update_source_list_mirror": "http://nl.archive.ubuntu.com/ubuntu/",
                "environment": "prod",
                "nginx_apt_state": "present",
                "nginx_etc": "/etc/nginx",
                "nginx_install_repo": "nginx",
                "nginx_max_clients": 1024,
                "unattended_install": true
            },
            "webserver003.prod.example.com": {
                "apt_default_key_urls": [
                    "http://nginx.org/keys/nginx_signing.key"
                ],
                "apt_default_packages": [
                    "nginx=1.6.2-1~precise"
                ],
                "apt_default_repositories": [
                    "deb http://nginx.org/packages/ubuntu/ precise nginx",
                    "deb-src http://nginx.org/packages/ubuntu/ precise nginx"
                ],
                "apt_update_source_list": "template",
                "apt_update_source_list_mirror": "http://nl.archive.ubuntu.com/ubuntu/",
                "environment": "prod",
                "nginx_apt_state": "present",
                "nginx_etc": "/etc/nginx",
                "nginx_install_repo": "nginx",
                "nginx_max_clients": 1024,
                "unattended_install": true
            },
            "webserver004.prod.example.com": {
                "apt_default_key_urls": [
                    "http://nginx.org/keys/nginx_signing.key"
                ],
                "apt_default_packages": [
                    "nginx=1.6.2-1~precise"
                ],
                "apt_default_repositories": [
                    "deb http://nginx.org/packages/ubuntu/ precise nginx",
                    "deb-src http://nginx.org/packages/ubuntu/ precise nginx"
                ],
                "apt_update_source_list": "template",
                "apt_update_source_list_mirror": "http://nl.archive.ubuntu.com/ubuntu/",
                "environment": "prod",
                "nginx_apt_state": "present",
                "nginx_etc": "/etc/nginx",
                "nginx_install_repo": "nginx",
                "nginx_max_clients": 1024,
                "unattended_install": true
            }
        }
    },
    "all": {
        "children": [
            "postgresql",
            "ungrouped",
            "webservers"
        ],
        "vars": {
            "environment": "prod"
        }
    },
    "postgresql": {
        "hosts": [
            "postgresql000.prod.example.com"
        ]
    },
    "ungrouped": {},
    "webservers": {
        "hosts": [
            "webserver000.prod.example.com",
            "webserver001.prod.example.com",
            "webserver002.prod.example.com",
            "webserver003.prod.example.com",
            "webserver004.prod.example.com"
        ]
    }
}
```
</div></details>
