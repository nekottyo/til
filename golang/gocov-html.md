coveradge report をいい感じに作って drone から gh-pages でホストする
=====

drone に事前に api token を設定しておく。

```yaml
pipeline:
  publish:
    image: golang:latest
    sectets: [gh_push_token]
    commands:
      - go get github.com/axw/gocov/gocov github.com/jpoles1/gopherbadger github.com/matm/gocov-html
      - mkdir -p doc && gocov test ./... | gocov-html > doc/index.html
      - gopherbadger -prefix ''; mv coverage_badge.png doc/
      - git config user.name "${DRONE_COMMIT_AUTHOR}"
      - git config user.email "${DRONE_COMMIT_AUTHOR_EMAIL}"
      - rm .gitignore # doc を .gitignore から抜く
      - git add doc
      - git commit -m "[skip ci] Update docs from ${DRONE_BUILD_LINK}"
      - git remote add gh https://nekottyo:$${GH_PUSH_TOKEN}@github.com/${DRONE_REPO}.git
      - git push -f gh $(git subtree split --prefix doc/ master):gh-pages
    when:
      branch: master
```
