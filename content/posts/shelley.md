---
title: "Shelly"
summary: "how to shutdown macOS with bash"

date: 2021-03-23
draft: false

categories: [Useful bash]
tags: [osascript, shutdown, bash, Terminal, CLI, macOS]

showtoc: true
tocopen: false
---

How to shutdown macOS with bash
===============================

let's shut down mac without touching our trackpad or mouse.

let's shut down mac with just a few keystrokes.

Things you'll need
------------------
macOS, Terminal, bash, osascript.

if you have a mac, these should all be installed already.

Let's shutdown
--------------
There are a number of ways to shutdown your macOS with bash. Of course, one way being with the `shutdown` command. However, this way reopens all windows you had open when you log back in.

the `shutdown` method:

```bash
$ sudo shutdown -h now
```

Instead, to imitate the act of clicking on the Apple icon and shutting down normally, we'll need to use `osascript`.

the `osascript` method:

```bash
$ osascript -e 'tell app "loginwindow" to «event aevtrsdn»'
```

This will open the usual shutdown dialog box and then you'll just need to press enter to shutdown. And when you log back in, you'll start off fresh with no open windows.

I have a personal `.bash_aliases` file to which I included the following:
 
```bash
# .bash_aliases
alias gnite="osascript -e 'tell app \"loginwindow\" to «event aevtrsdn»'"
```

Now I can just enter `gnite` into Terminal and shutdown like normal without using my trackpad.

So, my simple shutdown process goes like this:

1. `Cmd - <spacebar>` to open Spotlight
2. `t` and `<enter>` to focus Terminal
3. and `gnite` and `<enter>` to open Shutdown dialog box
4. `<enter>` to shutdown.

Happy days and now I can rest.

🛌

---

## Appendix
Shut down without showing a confirmation dialog:

```bash
osascript -e 'tell app "System Events" to shut down'
```

Shut down after showing a confirmation dialog:

```bash
osascript -e 'tell app "loginwindow" to «event aevtrsdn»'
```

Restart without showing a confirmation dialog:

```bash
osascript -e 'tell app "System Events" to restart'
```

Restart after showing a confirmation dialog:

```bash
osascript -e 'tell app "loginwindow" to «event aevtrrst»'
```

Log out without showing a confirmation dialog:

```bash
osascript -e 'tell app "System Events" to  «event aevtrlgo»'
```

Log out after showing a confirmation dialog:

```bash
osascript -e 'tell app "System Events" to log out'
```

Go to sleep (`pmset`):

```bash
pmset sleepnow
```

Go to sleep (AppleScript):

```bash
osascript -e 'tell app "System Events" to sleep'
```

Put displays to sleep (10.9 and later):

```bash
pmset displaysleepnow
```


## Advanced Explanation

these explanations are taken directly from a Stack Exchange thread.

### the `osascript` method:
The four letter codes for the Apple events are listed in `AERegistry.h`.

All System Events commands above send Apple events to the `loginwindow` process. `loginwindow` is sent the same Apple events as above when you log out, restart, shut down, or put the the Mac to sleep normally. See [Technical Q&A QA1134: Programmatically causing restart, shutdown and/or logout](https://developer.apple.com/library/mac/qa/qa1134/_index.html).

According to `man shutdown`, `shutdown -h now` and `shutdown -r now` send processes a `TERM` signal followed by a `KILL` signal.

According to the [Daemons and Services Programming Guide](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Lifecycle.html), when you tell `loginwindow` to log out, processes that support sudden termination are sent a `KILL` signal, and processes that don't support sudden termination are terminated in different ways: Cocoa applications receive the `applicationShouldTerminate:` delegate method, foreground applications receive the `kAEQuitApplication` Apple event, background applications receive the `kAEQuitApplication` Apple event followed by a `KILL` signal, and daemons receive a `TERM` signal followed by a `KILL` signal after a few seconds.

### the `shutdown` method:
How shutdown works is by sending a sigterm to all processes which should then deal with that e.g. save open files etc. If they don't exit then they will get sent a SIGKILL which forces them to die with no chance to respond. The signals are not sent via the normal key message queue so Apps have to deal with this separately to the code that gets called from quit on the menu. A good app should call common code from both.

## References
[the `osascript` method | Stack Exchange](https://apple.stackexchange.com/questions/103571/using-the-terminal-command-to-shutdown-restart-and-sleep-my-mac/103633#103633)

[the `shutdown` method | Stack Exchange](https://apple.stackexchange.com/questions/103571/using-the-terminal-command-to-shutdown-restart-and-sleep-my-mac/103633#103572)