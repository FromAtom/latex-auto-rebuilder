# latex-auto-rebuilder
You no longer need to push `build` button or `C-c t d` command.

# Setup

```
$ bundle install
```

and edit `config.yml` file.

## Build only once

```
$ bundle exec rake
```

## Watch tex and bib files and auto rebuild

```
$ bundle exec rake watch
```

# Help

```
$ rake --task
```

# Reference
- [Ruby - ファイルの更新を監視してTeX文書を自動ビルド - Qiita](http://qiita.com/61503891/items/1e45be2fcc3fda21088d)
- [texをコンパイルしたりするRakefileつくった - ポクポク](http://pokutuna.hatenablog.com/entry/20110928/1317201209)
