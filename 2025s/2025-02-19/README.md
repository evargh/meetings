# System Request: Introduction/History

- BIOS software interrupt → signals OS to invoke low-level functions
- Old software often bypassed the OS for keyboard input, so SysRq was used as a special key for switching between operating systems.
- Introduced with the IBM PC family in the mid-1980s
- Originally given a dedicated key
	- Later, moved to <kbd>Alt</kbd> + <kbd>Print Screen</kbd>
	- Today, many keyboards don't provide the function at all.
    - However, we will provide a quick demo on how to enable SysRq on an arbitrary keyboard.
- Used today for debugging purposes
	- Example: Linux's "magic SysRq key"
- Commonly abbreviated "SysRq" or "Sys Req"

# Configuration

- Enable by building kernel with `CONFIG_MAGIC_SYSRQ` set.
- `/proc/sys/kernel/sysrq` bitmask (read/write) for keyboard activation
	- 0: disable all SysRq functions
	- 1: enable all SysRq functions
	- 0x2: enable control of console logging level
	- 0x4: enable control of keyboard (SAK, unraw)
	- 0x8: enable debugging dumps of processes etc.
	- 0x10: enable sync command
	- 0x20: enable remount read-only
	- 0x40: enable signalling of processes (term, kill, oom-kill)
	- 0x80: allow reboot/poweroff
	- 0x100: allow nicing of all RT tasks

# Usage

Either:

- Press <kbd>Alt</kbd> + <kbd>SysRq</kbd> + [command key]
	- "Fn" might be needed on some keyboards or laptops
	- Check your laptop's documentation for other possible key combinations (e.g., <kbd>Fn</kbd> + <kbd>s</kbd>)
	- ChromeBooks: <kbd>Alt</kbd> + <kbd>Volume Up (F10)</kbd> + [command key]
- Write [command key] to `/proc/sysrq-trigger`

# How does it work?

SysRq is built into the kernel.
<https://github.com/torvalds/linux/blob/master/drivers/tty/sysrq.c>

Maybe we can add a few code snippets of line 583 onwards? If this is too dense, we can give a very brief outline of what happens?

There's also <https://github.com/torvalds/linux/blob/0ad2507d5d93f39619fc42372c347d6006b64319/kernel/debug/kdb/kdb_main.c#L1979C1-L1979C47> which shows how it's integrated into the kernel debugger.

# Command keys

When invoking functions with the keyboard, these command mappings assume a QWERTY layout.

|Command|Function                                                                                                                                                                                                          |
|-------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|b      |Will immediately reboot the system without syncing or unmounting your disks.                                                                                                                                      |
|c      |Will perform a system crash and a crashdump will be taken if configured.                                                                                                                                          |
|d      |Shows all locks that are held.                                                                                                                                                                                    |
|e      |Send a SIGTERM to all processes, except for init.                                                                                                                                                                 |
|f      |Will call the oom killer to kill a memory hog process, but do not panic if nothing can be killed.                                                                                                                 |
|g      |Used by kgdb (kernel debugger)                                                                                                                                                                                    |
|h      |Will display help (actually any other key than those listed here will display help. but h is easy to remember :-)                                                                                                 |
|i      |Send a SIGKILL to all processes, except for init.                                                                                                                                                                 |
|j      |Forcibly “Just thaw it” - filesystems frozen by the FIFREEZE ioctl.                                                                                                                                               |
|k      |Secure Access Key (SAK): Kills all programs on the current virtual console. NOTE: See important comments below in SAK section.                                                                                    |
|l      |Shows a stack backtrace for all active CPUs.                                                                                                                                                                      |
|m      |Will dump current memory info to your console.                                                                                                                                                                    |
|n      |Used to make RT tasks nice-able                                                                                                                                                                                   |
|o      |Will shut your system off (if configured and supported).                                                                                                                                                          |
|p      |Will dump the current registers and flags to your console.                                                                                                                                                        |
|q      |Will dump per CPU lists of all armed hrtimers (but NOT regular timer_list timers) and detailed information about all clockevent devices.                                                                          |
|r      |Turns off keyboard raw mode and sets it to XLATE.                                                                                                                                                                 |
|s      |Will attempt to sync all mounted filesystems.                                                                                                                                                                     |
|t      |Will dump a list of current tasks and their information to your console.                                                                                                                                          |
|u      |Will attempt to remount all mounted filesystems read-only.                                                                                                                                                        |
|v      |Forcefully restores framebuffer console                                                                                                                                                                           |
|w      |Dumps tasks that are in uninterruptible (blocked) state.                                                                                                                                                          |
|x      |Used by xmon interface on ppc/powerpc platforms. Show global PMU Registers on sparc64. Dump all TLB entries on MIPS.                                                                                              |
|z      |Dump the ftrace buffer                                                                                                                                                                                            |
|0-9    |Sets the console log level, controlling which kernel messages will be printed to your console. (0, for example would make it so that only emergency messages like PANICs or OOPSes would make it to your console.)|
|R      |Replay the kernel log messages on consoles.                                                                                                                                                                       |
# How do I Use SysRq without a dedicated SysRq Key?

- First, set your SysRq permissions. This can be done either through `proc` or `sysctl`.
- Often, the easiest way to get immediate feedback is by entering a TTY. Enter a TTY (this can often be done by a combination of modifier keys and your Function keys) and go into a root shell. Enter `echo h > /proc/sysrq-trigger`, which should output the help menu.
- You often use SysRq when something is wrong with your userland, though. Having a key is useful.
- Manufacturers often use an alternative key combination for SysRq functionality. Check with your manufacturer on those details. Alternatively, you can implement the scancode-keycode mapping yourself. We will do it now on a Systemd Debian install using `evtest` and `udev`. `evtest` binaries are available in most package repositories. There are forks of `udev`, like `eudev` that establish the same functionality on non-Systemd systems.
- First, run `evtest` without any options. It will give you a list of character devices. Pick your keyboard, which is hopefully easy to identify. Keep its event id in mind.
- When you run `evtest`, each key press and release is associated with a scancode. Enter your key combination that you want to be SysRq. Remember that any time you hit <kdb>Alt</kdb> + <kbd>\<your keys\></kbd> you will be running invasive commands, so ensure that it's not something you can fat finger easily.
- Recall that event id from before. If you run `cat /sys/class/input/event<number>/device/modalias`, you can obtain a brief view of the `sysfs` kernel identifier for your device. Laptop keyboards aren't going anywhere, but this functionality is useful for a usb keyboard.
- Now that we have this information, we can use `hwdb`. Go to `/etc/udev/hwdb.d/` and create a new file. For example, the demo can be `90-keyboard.hwdb`.
- Paste that `modalias` identifier you got from before. On the next line, enter a single space and write the line: `KEYBOARD_KEY_<your scancode>=sysrq`.
- Run `systemd-hwdb update; udevadm trigger` as root to load your new key-value pairs and update your device rules.
- Switch to TTY and enter <kdb>Alt</kdb> + <kdb>\<your keys\></kdb> + <kbd>h</kbd>. If you see the same help menu, you did it!
- For some reason, I don't know why this information doesn't appear under `udevadm info` for the device.

# Resources

- <https://docs.kernel.org/admin-guide/sysrq.html>
- <https://en.wikipedia.org/wiki/Magic_SysRq_key>
