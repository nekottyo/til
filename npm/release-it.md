release-it w/ Github Actions to publish Github packages
===

https://github.com/nekottyo/renovate-config

release-it を使った release で、npm registry が github のとき

# ポイント

- `.npmrc` を置く必要がある
    - https://github.com/nekottyo/renovate-config/blob/master/.github/workflows/release.yml#L21-L24
    - secret 経由
- commit が走るため user.name, user.email を入れる
    - https://github.com/nekottyo/renovate-config/blob/master/.github/workflows/release.yml#L26-L28
- release-it は `--no-git.requireCleanWorkingDir` を入れる
    - .npmrc を触るため

