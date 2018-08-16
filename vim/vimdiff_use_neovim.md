vimdiff で neovim を使いたい
=====

neovim の diff mode は `-d`

```
Options:
  --                    Only file names after this
  +                     Start at end of file
  --cmd <cmd>           Execute <cmd> before any config
  +<cmd>, -c <cmd>      Execute <cmd> after config and first file

  -b                    Binary mode
  -d                    Diff mode
  -e, -E                Ex mode
  -es, -Es              Silent (batch) mode
  -h, --help            Print this help message
  -i <shada>            Use this shada file
  -m                    Modifications (writing files) not allowed
  -M                    Modifications in text not allowed
  -n                    No swap file, use memory only
  -o[N]                 Open N windows (default: one per file)
  -O[N]                 Open N vertical windows (default: one per file)
  -p[N]                 Open N tab pages (default: one per file)
  -r, -L                List swap files
  -r <file>             Recover edit state for this file
  -R                    Read-only mode
  -S <session>          Source <session> after loading the first file
  -s <scriptin>         Read Normal mode commands from <scriptin>
  -u <config>           Use this config file
  -v, --version         Print version information
  -V[N][file]           Verbose [level][file]
  -Z                    Restricted mode
```


単純に vimdiff を置き換えたければ、`.bashrc` や `.zshrc` に `alias vimdiff="vim -d"` でもよい


git の `difftool` や `mergetool` で使いたい場合は以下のような感じ

```toml:.gitconfig
[diff]
    tool = nvimdiff
[difftool "nvimdiff"]
    cmd = "nvim -d \"$LOCAL\" \"$REMOTE\""
[merge]
	tool = vimdiff
```


ref. https://github.com/neovim/neovim/issues/2377
