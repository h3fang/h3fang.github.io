---
layout: post
title: Be careful when using pipes in shell script
---

> `"|"` pipe operator redirects I/O to a subshell.

> Variables in a subshell are not visible outside the block of code in the subshell.

Thus the following code doesn't work as intended.

```bash
# check every window
all_closed=1
wmctrl -l | awk '{print $1}' | while read APP; do
    notify-send "[$APP] is not closed, please check unsaved content and close it manually."
    $all_closed=0
done

if [[ $all_closed -eq 0 ]]; then
    exit 2
fi
```

The **all_closed** variable is modified in the subshell created by pipes. The outside one remains unchanged. And the script will never exit with error code **2** no matter all windows are closed or not.

Fixing this piece of code is easy, just avoid using subshell (pipe).

```bash
for APP in $(wmctrl -l | awk '{print $1}'); do
    notify-send "[$APP] is not closed, please check unsaved content and close it manually."
    $all_closed=0
done
```

### References

* <https://www.tldp.org/LDP/abs/html/subshells.html>
