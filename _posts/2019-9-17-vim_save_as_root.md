---
layout: post
title: "Save as root in non-root vim"
tags: vim linux
---

Sometimes, you edited some system files and want to save them, but then realized you forgot to open them in vim with root privileges. What can you do now?

- Cry, discard the changes and re-edit with `sudoedit`.
- Open another editor, copy the content (could be problematic, the default vim in Arch linux doesn't support to copy to system clipboard, or you might be using sway and wayland, both need extra software and setup), save a temporary copy, move the copy to target file with `sudo`.
- Open Chromium (or your favorite web browser) and ask the all mighty Google (or your favorite search engine) to save you.

If you choose the third one and arrived here, and yes there are better solutions. You can do the second one in vim with a one line command.

```
:w !sudo tee "%" > /dev/null
```

`w !` pipes the entire file (buffer) into the shell command `sudo tee "%" > /dev/null` to save it to target as root. The target `%` is replaced with the current file name.

You can also make a shortcut for this trick, like put the following in your `.vimrc` file.

```
cmap w!! w !sudo tee "%" > /dev/null
```

There are more you can find in the links listed in references.

# References
- <https://stackoverflow.com/questions/4484183/save-file-as-root-after-editing-as-non-root/4484222>
- <https://vim.fandom.com/wiki/Su-write>