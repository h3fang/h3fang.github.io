---
layout: post
title: The Bash Trap Command
tags: shell
---

Just want to recommend a very good [writeup](https://www.linuxjournal.com/content/bash-trap-command) for bash trap command.

Quick highlights:

- Interrupted commands are not restarted.
- Reset signal handling by using `-` as command, e.g. `trap - SIGINT`.
- Traps caught by the shell are reset to the values inherited from the shell's parent.
- If you need to test something with `sudo` and want to make sure that everything is working both when `sudo` prompts for a password and when it does not prompt for a password.

# References
- <https://www.linuxjournal.com/content/bash-trap-command>