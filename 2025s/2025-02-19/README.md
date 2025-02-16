# System Request: Introduction/History

- BIOS software interrupt → signals OS to invoke low-level functions
- Old software often bypassed the OS for keyboard input, so Sys Rq was used as a special key for switching between operating systems.
- Introduced with the IBM PC family in the mid-1980s
- Originally given a dedicated key
	- Later, moved to <kbd>Alt</kbd> + <kbd>Print Screen</kbd>
	- Today, many keyboards don't provide the function at all.
- Used today for debugging purposes
- Commonly abbreviated "Sys Req" or "Sys Rq"

# Configuration

- Enable by building kernel with `CONFIG_MAGIC_SYSRQ` set.
- `/proc/sys/kernel/sysrq` bitmask (read/write) for keyboard activation
	- 0: disable all Sys Rq functions
	- 1: enable all Sys Rq functions
	- 0x2: enable control of console logging level
	- 0x4: enable control of keyboard (SAK, unraw)
	- 0x8: enable debugging dumps of processes etc.
	- 0x10: enable sync command
	- 0x20: enable remount read-only
	- 0x40: enable signalling of processes (term, kill, oom-kill)
	- 0x80: allow reboot/poweroff
	- 0x100: allow nicing of all RT tasks

# Usage

- Either:
	- Press <kbd>Alt</kbd> + <kbd>SysRq</kbd> + [command key]
	- Write [command key] to `/proc/sysrq-trigger`
- "Fn" might be needed on some keyboards or laptops
- ChromeBooks: <kbd>Alt</kbd> + <kbd>Volume Up (F10)</kbd> + [command key]

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
|k      |Secure Access Key (SAK) Kills all programs on the current virtual console. NOTE: See important comments below in SAK section.                                                                                     |
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
|v      |Causes ETM buffer dump [ARM-specific]                                                                                                                                                                             |
|w      |Dumps tasks that are in uninterruptible (blocked) state.                                                                                                                                                          |
|x      |Used by xmon interface on ppc/powerpc platforms. Show global PMU Registers on sparc64. Dump all TLB entries on MIPS.                                                                                              |
|y      |Show global CPU Registers [SPARC-64 specific]                                                                                                                                                                     |
|z      |Dump the ftrace buffer                                                                                                                                                                                            |
|0-9    |Sets the console log level, controlling which kernel messages will be printed to your console. (0, for example would make it so that only emergency messages like PANICs or OOPSes would make it to your console.)|
|R      |Replay the kernel log messages on consoles.                                                                                                                                                                       |
