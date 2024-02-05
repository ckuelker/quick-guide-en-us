---
title: Nasm
author: Christian Külker
date: 2024-02-05
version: 0.1.1
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Assembler
commands:
- nasm
- ndisasm
- sm
- make
- strip
- asciidoc
- xmlto
- configure
description: Netwide Assembler (NASM), an asssembler for the x86 CPU

---

## Resources

- <https://nasm.us/>
- <https://github.com/netwide-assembler/nasm>
- <https://github.com/netwide-assembler/nasm.git>

## Debian Versions

| Debian   |  # | Nasm         |
| -------- | -- | ------------ |
| Bookworm | 12 | 2.16.01-1    |
| Bullseye | 11 | 2.15.05-1    |
| Buster   | 10 | 2.14-1       |
| Stretch  |  9 | 2.12.01-1+b1 |

## Compiling from Source

The compiling from the git source has been tested on Debian 9 Stretch for
`nasm` 2.16.01. Meanwhile this version is available via package. The
current (2024-02-05) source release is 2.16.02rc10.

### Dependencies

```bash
aptitude install xmlto asciidoc docbook autoconf
```

### Source

```bash
mkdir -p /srv/g/github.com/netwide-assembler
cd /srv/g/github.com/netwide-assembler
git clone https://github.com/netwide-assembler/nasm.git
```

###  Compiling

The following will compile `nasm` from source and install it in `/opt`. And it
requires a modern version of `autoconf` 2.71 or higher. This would work on
Debian 10 Buster and Debian 11 Bullseye, but requires `autoconf` [from
source](../Build/autoconf.md) for Debian 9 Stretch.

```bash
# As root:
mkdir -p /opt/{bin,share}
chown $USER.$USER /opt/{bin,share}

# As user $USER
sh autogen.sh
aclocal: installing 'autoconf/m4/pa_add_cflags.m4' from [...]
[...]

configure --prefix=/opt
make
[...]
sm -I./output -I./output -Wl,--gc-sections -o ndisasm disasm/ndisasm.o libnasm.a
make[1]: Leaving directory '/srv/g/github.com/netwide-assembler/nasm'

make strip
strip --strip-unneeded nasm ndisasm

make manpages
asciidoc -b docbook -d manpage -o nasm.xml nasm.txt
xmlto man --skip-validation nasm.xml 2>/dev/null
asciidoc -b docbook -d manpage -o ndisasm.xml ndisasm.txt
xmlto man --skip-validation ndisasm.xml 2>/dev/null

make install
```

This will install into `/opt`:

~~~
bin
├── nasm
└── ndisasm
share
└── man
    └── man1
            ├── nasm.1
                    └── ndisasm.1
~~~


## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2024-02-05 | Debian 12 Bookworm                                   |
| 0.1.0   | 2023-03-08 | Initial release                                      |