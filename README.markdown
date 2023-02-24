---
title: Quick Guide README
author: Christian Külker
version: 0.1.9
date: 2023-02-24
type: doc

---

# Overview

This document briefly describes the purpose and contents of the git repository
[quick-guide]. The goal of [quick-guide] is to collect information about tasks
and provide a guide that can quickly reproduce the task. The primary format of
this repository is [markdown].

![Github license](https://img.shields.io/github/license/ckuelker/quick-guide.svg)
![Github issues](https://img.shields.io/github/issues/ckuelker/quick-guide.svg?style=popout-square)
![Github code size in bytes](https://img.shields.io/github/languages/code-size/ckuelker/quick-guide.svg)
![Git repo size](https://img.shields.io/github/repo-size/ckuelker/quick-guide.svg)
![Last commit](https://img.shields.io/github/last-commit/ckuelker/quick-guide.svg)
[![GFDL 1.3][gfdl-1.3-shield]][gfdl-1.3]
[![CC BY-SA 4.0][cc-by-sa-4.0-shield]][cc-by-sa-4.0]

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.9   | 2023-02-24 | Disptach LICENSE and COPYING file,                   |
|         |            | Bump copyright year, DevOps/Tools/fzf.md,            |
|         |            | Dev/License/check-github-license.md, Dev/HPC/fio.md, |
|         |            | Dev/Diff/kdiff3.md, Dev/HPC/mpich.md,                |
|         |            | Dev/HPC/atlas.md, Dev/HPC/linpack.md,                |
|         |            | DevOps/Mail/Client/overview.md,                      |
|         |            | Dev/Python/I18n/i18n-l10n.md                         |
|         |            |                                                      |
| 0.1.8   | 2023-01-18 | Improve README.markdown, index.md,                   |
|         |            | Dev/Python/Web/simple-http-server.md,                |
|         |            | Dev/Rust/hello-world.md,                             |
|         |            | DevOps/Mail/Policy/overview.md, Dev/HPC/hpcc.md,     |
|         |            | DevOps/HPC/index.md, Dev/Rust/hello-world.rs,        |
|         |            | DevOps/Mail/Policy/postfix-mail-flow-v0.1.0.svg      |
|         |            |                                                      |
| 0.1.7   | 2022-11-13 | DevOps/Mail/postfix-statistics.md,                   |
|         |            | Dev/VCS/Git/git-with-other-default-branch.md,        |
|         |            | Dev/HPC/linpack.md, Dev/Rust/hello-world.md,         |
|         |            | DevOps/HPC/index.md, Dev/HPC/index.md,               |
|         |            | DevOps/Certificates/index.md, Dev/HPC/atlas.md,      |
|         |            | Dev/VCS/index.md, Dev/VCS/index.md,                  |
|         |            | Dev/VCS/Git/git-with-other-default-branch.md,        |
|         |            | DevOps/Hardware/Control/BMC/yafuflash.md,            |
|         |            | Dev/Python/Web/simple-http-server.md,                |
|         |            | ...nvironment/managing-dependencies-with-pipenv.md,  |
|         |            | .../Environment/creating-virtual-environments.md,    |
|         |            | Dev/Web/Design/favicon.md,                           |
|         |            | Dev/Python/Style-Guide/flake8.md                     |
| 0.1.6   | 2022-07-02 | DevOps/Hardware/Control/BMC/yafuflash.md,            |
|         |            | DevOps/Hardware/Information/cpuinfo.md,              |
|         |            | DevOps/Hardware/Control/BMC/ipmitool.md,             |
|         |            | DevOps/Monitoring/smem.md, Dev/Diff/kdiff3.md,       |
|         |            | DevOps/Monitoring/goaccess.md, Dev/HPC/mpich.md,     |
|         |            | DevOps/Ftp/curlftps.md, DevOps/Daemons/tftpd.md,     |
|         |            | Dev/VCS/Git/github-manageing-forks.md,               |
|         |            | Dev/VCS/Git/github-pull-request.md, Dev/HPC/iperf.md |
|         |            | Dev/VCS/Git/git-ssh-agent-conversation.md,           |
|         |            | Dev/HPC/spec2006.md, Dev/HPC/taskset.md,             |
|         |            | Dev/HPC/vasp.md, Dev/HPC/hpcc.md,                    |
|         |            | DevOps/Backup/linux-backup-solutions.md,             |
|         |            | DevOps/Time/ntpd.md, DevOps/Network/infiniband.md    |
|         |            | Dev/Python/Best-Practice/python-best-practice.md,    |
|         |            | DevOps/Monitoring/zabbix.md                          |
| 0.1.5   | 2022-06-06 | +DevOps/Productivity/Logfiles/tail.md,               |
|         |            | DevOps/Monitoring/mrtg.md,                           |
|         |            | DevOps/Monitoring/icinga2.md,                        |
|         |            | DevOps/Mail/postfix-statistics.md,                   |
|         |            | DevOps/Monitoring/bmon.md, DevOps/HPC/index.md,      |
|         |            | DevOps/Daemons/nginx.md, Dev/VCS/index.md,           |
|         |            | Dev/VCS/Git/github-pull-request.md,                  |
|         |            | Dev/VCS/Git/git.md, DevOps/Memory/swap.md            |
| 0.1.4   | 2022-05-26 | index.md, +DevOps/Fs/sparse.md, README.markdown      |
|         |            | update: Dev/HPC/{index,numa,fio}.md,                 |
|         |            | Dev/Perl/Style-Guide/perltidy.md, Dev/HPC/hdd.md,    |
|         |            | ...Environment/managing-dependencies-with-poetry.md, |
|         |            | Dev/Python/Package/packaging-python-projects.md      |
| 0.1.3   | 2022-05-14 | .gitignore, +ntpd.md, +iperf.md +Simple MSS Example, |
|         |            | +linux-backup-solutions.md, +postfix-statistics.md;  |
|         |            | update: git.md, radicale.md, kubernetes.md,          |
|         |            | managing-dependencies-with-pipenv.md,                |
|         |            | managing-dependencies-with-poetry.md,                |
|         |            | compiling-perl-from-source.md                        |
| 0.1.2   | 2021-05-28 | Add kubernetes.md, goaccess.md, icinga2.md, swap.md, |
|         |            | iperf.md, graphite-carbon-whisper-schematics         |
| 0.1.1   | 2021-05-15 | Add CC-BY-SA-4.0 license, bump copyright to 2021,    |
|         |            | +xorg.md, +kdiff3.md, +radicale.md, +grc.md,         |
|         |            | +rust/hello-world.md, +bmon.md, +zabbix.md,          |
|         |            | +smem.md, +nginx.md, +fio.md, +infiniband.md,        |
|         |            | +perltidy.md, +favion.md, hdd.md, +cpuinfo.md,       |
|         |            | +vasp.md, +hpcc.md, +spec2006.md, +linpack.md,       |
|         |            | +taskset.md, +yafuflash.md, +ipmitool.md, +tftpd.md, |
|         |            | +openhpc-installation-and-usage.md                   |
|         |            |                                                      |
| 0.1.0   | 2020-04-20 | Initial release                                      |


# Author

    Christian Külker <c@c8i.org>

# License And Copyright

    Copyright (C) 2020 - 2023 by Christian Kuelker

## GNU Free Document License, Version 1.3

Copyright (c)  2020-2023 Christian Külker

Permission is granted to copy, distribute and/or modify this documents under
the terms of the GNU Free Documentation License, Version 1.3 or any later
version published by the Free Software Foundation; with no Invariant Sections,
no Front-Cover Texts, and no Back-Cover Texts. A copy of the license is
included in the file [COPYING](COPYING).

[gfdl-1.3-shield]: https://img.shields.io/badge/License-GFDL--1.3-lightgrey.svg
[gfdl-1.3]:  https://www.gnu.org/licenses/fdl-1.3.en.html

## Creative Commons Attribution-ShareAlike 4.0 International License

Permission is granted to copy, distribute and/or modify this documents under
the terms of the Creative Commons Attribution-ShareAlike License, Version
[4.0](https://creativecommons.org/licenses/by-sa/4.0/) published by the
Creative Commons Corporation global nonprofit organization ("Creative
Commons").

[![CC BY-SA 4.0][cc-by-sa-4.0-image]][cc-by-sa-4.0]

[cc-by-sa-4.0]: http://creativecommons.org/licenses/by-sa/4.0/
[cc-by-sa-4.0-image]: https://licensebuttons.net/l/by-sa/4.0/88x31.png
[cc-by-sa-4.0-shield]: https://img.shields.io/badge/License-CC%20BY--SA%204.0-lightgrey.svg

# DISCLAIMER OF WARRANTY

    THERE IS NO WARRANTY FOR THE PROGRAM, TO THE EXTENT PERMITTED BY APPLICABLE
    LAW. EXCEPT WHEN OTHERWISE STATED IN WRITING THE COPYRIGHT HOLDERS AND/OR
    OTHER PARTIES PROVIDE THE PROGRAM “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
    WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE. THE
    ENTIRE RISK AS TO THE QUALITY AND PERFORMANCE OF THE PROGRAM IS WITH YOU.
    SHOULD THE PROGRAM PROVE DEFECTIVE, YOU ASSUME THE COST OF ALL NECESSARY
    SERVICING, REPAIR OR CORRECTION.

# LIMITATION OF LIABILITY

    IN NO EVENT UNLESS REQUIRED BY APPLICABLE LAW OR AGREED TO IN WRITING WILL
    ANY COPYRIGHT HOLDER, OR ANY OTHER PARTY WHO MODIFIES AND/OR CONVEYS THE
    PROGRAM AS PERMITTED ABOVE, BE LIABLE TO YOU FOR DAMAGES, INCLUDING ANY
    GENERAL, SPECIAL, INCIDENTAL OR CONSEQUENTIAL DAMAGES ARISING OUT OF THE
    USE OR INABILITY TO USE THE PROGRAM (INCLUDING BUT NOT LIMITED TO LOSS OF
    DATA OR DATA BEING RENDERED INACCURATE OR LOSSES SUSTAINED BY YOU OR THIRD
    PARTIES OR A FAILURE OF THE PROGRAM TO OPERATE WITH ANY OTHER PROGRAMS),
    EVEN IF SUCH HOLDER OR OTHER PARTY HAS BEEN ADVISED OF THE POSSIBILITY OF
    SUCH DAMAGES.

[Markdown]: https://en.wikipedia.org/wiki/Markdown
[quick-guide]: https://github.com/ckuelker/quick-guide

