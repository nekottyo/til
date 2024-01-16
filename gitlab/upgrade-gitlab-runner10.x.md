# gitlab-runner 9.X から 10.X への update

9.x からの 10.x update は package manager の update だけではできない  
gitlab-ci-multirunner から gitlab-runner へ名前が変わったため

https://docs.gitlab.com/runner/install/linux-repository.html



## install
`CentOS Linux release 7.3.1611 (Core)` 環境

```
# curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.rpm.sh | sudo bash
# yum install gitlab-runner
```
9.x で install されていた `/etc/systemd/system/gitlab-runner.service` の unit file まできれいに消されるので install コマンドで再生成する  
9.x までは default install すると directory が /home/gitlab-runner, user も gitlab-runner なので合わせる（無指定だと /home/admin, root になる）
```
# gitlab-runner install -u gitlab-runner -d /home/gitlab-runner
```
すると以下のような unit file が生成される
```
# cat /etc/systemd/system/gitlab-runner.service
[Unit]
Description=GitLab Runner
After=syslog.target network.target
ConditionFileIsExecutable=/usr/bin/gitlab-runner

[Service]
StartLimitInterval=5
StartLimitBurst=10
ExecStart=/usr/bin/gitlab-runner "run" "--working-directory" "/home/gitlab-runner" "--config" "/etc/gitlab-runner/config.toml" "--service" "gitlab-runner" "--syslog" "--user" "gitlab-runner"

Restart=always
RestartSec=120

[Install]
WantedBy=multi-user.target
```
## start
```
# gitlab-runner start
```
