# AUR pkg for Arch & Manjaro

Comodo Antivirus for Linux - is that needed? no. yes. who knows. sometimes you just need to have a AV scanner nearby and besides ClamAV (which does not provide on-access/realtime scanning) COMODO is the only free one available atm.

Comodo AV was not developed anymore since 2015 (or so) but it seems they're back (at least they promoting their solution as CAV Linux 2022).

well yea ... and.. after finishing the whole package here I found that the **CURRENT** build version (`1.1.268025.1`) is mentioned in 2015 - so hooray just marketing buzz over there.. too bad.. and too bad I did not recognized it earlier..

Comodo provides several packages for Ubuntu, Debian, RHEL, SuSE, Mint, ... while I have chosen the Ubuntu one as it contains useful control scripts which I re-use in the arch package.

# Status

The reason why this package is not in AUR already is .. that it does not work. Mainly because the incredible old and outdated redirfs approach which is even abandoned by its creator long time ago. Some ppl tried to fix compilation issues for redirfs (successfully) which are all included here as well.

Unfortunately the whole thing here has no meaning atm - because without working redirfs+avflt the scanner does not work at all.

I leave that here in the hope someone find a solution some day and so this could be the base for a working package.

## What works

- fully packaged version of CAV
- fully adapted redirfs + avflt kernel modules to get it build for 5.10 kernels (might work on newer kernels as well but untested)
- init scripts which are included adapted so they work on Arch (no sysvinit needed though!)
- fully configured and working with DKMS
- module avflt loads
- module redirfs loads
- in theory the mail gateway part should work as there is no dependency to kernel modules (untested though)

## What does not

- accessing `/sys/fs/redirfs/filters/avflt/paths` seems to work once but then fails (afai could see in the strace)
- cmdagent which loads avflt and redirfs modules hangs when tryint to read/write `/sys/fs/redirfs/filters/avflt/paths` which then leads to defunction of the whole scanner
- updating the virus database (so even scheduled scans do not work). Either this is due to the reason cmdagent is hanging or because this whole thing is a server site issue (keep in mind that the actual program code is from 2015... maybe the server is off lol)


