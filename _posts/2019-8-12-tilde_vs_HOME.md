---
layout: post
title: "~ vs $HOME"
tags: shell
---

`~` is expanded to current user home directory by shell, just like the variable `$HOME`.

While `$HOME` is expanded in most places (except inside single quotes), `~` is expanded only in a few specific conditions.

Here are a few examples where `~` is not expanded:

- `echo "~" '~'`
- `echo ~@ ~~` (also note that ~u is meant to expand to the home directory of user u).
- `echo @~`
- `(( HOME == ~ )), $(( var + ~ ))`
- with extglob: `case $var in @(~|other))`... (though case $var in ~|other) is OK).
- `./configure --prefix=~` (as --prefix is not a valid variable name)
- `cmd "foo"=~` (in bash, because of the quotes).
- when invoked as sh: `export "foo"=~`, `env JAVA_HOME=~ cmd` ...

Especially for the `./configure --prefix=~` case, the following code doen't work as intended.

```bash
borg create --compression auto,zstd,10 --stats --list --filter=AME \
    --exclude "~/.config/mpv/watch_later" \
    ~/.config
```

The second `~` works, but the first one in `--exclude` doesn't work. This has confused me for a long time.

Maybe it's better to stick to `$HOME`.

# References
- <https://unix.stackexchange.com/questions/146671/does-always-equal-home>