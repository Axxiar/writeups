---
title: Movie Night 1
tags:
  - system
  - CTF
  - 🇫🇷
---


- Author: Log_s
- Difficulty: very-easy
- Points: 50

## Description

> Dallas: Something has attached itself to him. We have to get him to the infirmary right away.

> Ripley: What kind of thing? I need a clear definition.

\- Alien (1979)

===

The flag for this challenge is located at `/home/dev/flag.txt`.

Format: `^Hero{\S+}$`

## Solution

### Quick overview

After logging in through ssh as `user` (provided credentials), we see there are three directories in `/home/` : `admin/`, `dev/`, and `user/`.

We only have access to `/user`, can't read or access anything in `/dev` so we don't have access to `dev/flag.txt`

### Looking for anything related to dev

What about processes ?

```bash
$ ps aux | grep dev
dev           31  0.0  0.0   4548  3144 ?        Ss   15:35   0:00 tmux -S /tmp/tmux-1002 new-session -d -s work bash
dev           32  0.0  0.0   4196  3476 pts/0    Ss+  15:35   0:00 bash
user         107  0.0  0.0   3332  1676 pts/1    S+   15:59   0:00 grep dev
```

`dev` has a tmux session opened on socket `/tmp/tmux-1002`, interesting...

### Understanding tmux sessions 

After reading a bit the tmux manual, here are some more information on sessions :

> The tmux server manages clients, sessions, windows and panes. <br>
> Clients are attached to sessions to interact with them, either <br>
> when they are created with the new-session command, or later with <br>
> the attach-session command.  Each session has one or more windows <br>
> linked into it. <br>
> ...

A few explanation on the command `tmux -S /tmp/tmux-1002 new-session -d -s work bash`
- `-S /tmp/tmux-1002` : specify socket used by the client (process that displays tmux sessions on screen) and server (process that manages tmux sessions) to communicate
- `new-session [...] -s work bash` : create a new session named `work` and run bash in initial window
- `-d` : do not attach session to the current terminal

Looks like `list-sessions` and `attach-session` will be useful to us.

```bash
$ tmux -S /tmp/tmux-1002 list-sessions
work: 1 windows (created Sat Nov 29 15:35:46 2025)
```

Nice, looks promising, we have a session named `work` as mentioned in `ps aux` (`... -s work ...`)

### (Re-)Attaching something to him

Now we can try to reattach to it :

```bash
$ tmux -S /tmp/tmux-1002 attach-session
```
and there it is, we are attached to the `work` session as the `dev` user! Which mean we can now `cat /home/dev/flag.txt`

## Flag

<details> 
<summary>Spoiler</summary>

```
Hero{1s_1t_tmux_0r_4l13n?_a20bac4b5aa32e8d9a8ccb75d228ca3e}
```

</details>
