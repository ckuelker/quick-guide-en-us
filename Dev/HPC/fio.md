---
title: Fio
author: Christian Külker
date: 2020-05-19
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- HPC
- Benchmark
commands:
- fio
- tar
- make
tags:
- Fio
- OFED
description: Fio

---

## Installing Fio

### As A Package

Without **OFED** on some systems installation from package works

```shell
# aptitude install fio
```

**CentOS** (and Red Hat Enterprise Linux) have rather limited main repositories
than most distributions. If you haven't already, you'll need to add the
**EPEL** repository to **CentOS/RHEL** to get **fio**. However it might clash
if you are using **Infiniband** with **OFED**. For this, use installation from
source.

```shell
# yum install epel-release -y
# yum install fio
```

### From Source

Download the [2.0.14] `tar` (2013), because **RPM** might clash with **OFED**
stack

```shell
$ tar xvf fio-2.0.14.tar
$ cd fio-2.0.14
$ ./configure
$ make
# make install
install -m 755 -d /usr/local/bin
install fio fio_generate_plots /usr/local/bin
install -m 755 -d /usr/local/man/man1
install -m 644 fio.1 /usr/local/man/man1
install -m 644 fio_generate_plots.1 /usr/local/man/man1
```

The latest version is [3.19] (2020):

```shell
$USERc@$HOST:/tmp$  wget https://github.com/axboe/fio/archive/fio-3.19.tar.gz
$USER@$HOST:/tmp$ tar xvzf fio-3.19.tar.gz
$USER@$HOST:/tmp$ cd fio-fio-3.19/
$USER@$HOST:/tmp$ .configure
$USER@$HOST:/tmp$ make
root@$HOST:/tmp# make install
install -m 755 -d /usr/local/bin
install fio t/fio-genzipf t/fio-btrace2fio t/fio-dedupe t/fio-verify-state \
./tools/fio_generate_plots ./tools/plot/fio2gnuplot ./tools/genfio \
./tools/fiologparser.py ./tools/hist/fiologparser_hist.py \
./tools/fio_jsonplus_clat2csv /usr/local/bin
install -m 755 -d /usr/local/man/man1
install -m 644 ./fio.1 /usr/local/man/man1
install -m 644 ./tools/fio_generate_plots.1 /usr/local/man/man1
install -m 644 ./tools/plot/fio2gnuplot.1 /usr/local/man/man1
install -m 644 ./tools/hist/fiologparser_hist.py.1 /usr/local/man/man1
install -m 755 -d /usr/local/share/fio
install -m 644 ./tools/plot/*gpm /usr/local/share/fio/
```

Read the [documentation] for details

## Running Fio

```shell
# fio --name=random-write --ioengine=posixaio --rw=randwrite --bs=4k \
--numjobs=1 --size=4g --iodepth=1 --runtime=60 --time_based --end_fsync=1
```

The has a huge output, but most important is the write speed

```
...
Run status group 0 (all jobs):
  WRITE: bw=127MiB/s (133MB/s), 127MiB/s-127MiB/s (133MB/s-133MB/s), \
  io=8192MiB (8590MB), run=64602-64602msec
...
```

Reminder: MiB = Mebibyte (basis 1014), MB = Megabyte (basis 1000)

As we can see here the disk was fast, we requested 4g and it was able to run
though its approximately 2 times in 60 seconds. The most interesting value
is **bw=127MiB/s**.

If you have the CPU core number and the bandwidth to the storage subsystem, use
more jobs in parallel. After this repeat with different block sizes, like 1m.

In contrast the newest version on an 12 year old laptop:

```shell
# fio --name=random-write --ioengine=posixaio --rw=randwrite --bs=4k \
--numjobs=1 --size=4g --iodepth=1 --runtime=60 --time_based --end_fsync=1
random-write: (g=0): rw=randwrite, bs=(R) 4096B-4096B, (W) 4096B-4096B, (T) \
4096B-4096B, ioengine=posixaio, iodepth=1
fio-3.19
Starting 1 process
random-write: Laying out IO file (1 file / 4096MiB)
Jobs: 1 (f=1): [F(1)][100.0%][w=23.4MiB/s][w=5992 IOPS][eta 00m:00s]
random-write: (groupid=0, jobs=1): err= 0: pid=2427: Tue May 19 11:55:53 2020
  write: IOPS=10.1k, BW=39.3MiB/s (41.2MB/s)(2411MiB/61288msec); 0 zone resets
    slat (usec): min=2, max=118728, avg=14.89, stdev=369.70
    clat (nsec): min=1327, max=245121k, avg=71777.23, stdev=1507737.18
     lat (usec): min=19, max=250225, avg=86.66, stdev=1556.20
    clat percentiles (usec):
     |  1.00th=[    3],  5.00th=[    3], 10.00th=[    3], 20.00th=[   23],
     | 30.00th=[   26], 40.00th=[   29], 50.00th=[   30], 60.00th=[   30],
     | 70.00th=[   30], 80.00th=[   37], 90.00th=[   63], 95.00th=[   91],
     | 99.00th=[  326], 99.50th=[  799], 99.90th=[ 5866], 99.95th=[ 8717],
     | 99.99th=[82314]
   bw (  KiB/s): min=16406, max=66802, per=100.00%, avg=41176.64, \
stdev=8856.09, samples=118
   iops        : min= 4101, max=16700, avg=10293.88, stdev=2214.04, samples=118
  lat (usec)   : 2=0.09%, 4=11.76%, 10=0.01%, 20=0.28%, 50=74.46%
  lat (usec)   : 100=8.98%, 250=3.08%, 500=0.64%, 750=0.18%, 1000=0.09%
  lat (msec)   : 2=0.16%, 4=0.12%, 10=0.12%, 20=0.02%, 50=0.01%
  lat (msec)   : 100=0.01%, 250=0.01%
  cpu          : usr=10.78%, sys=15.06%, ctx=775939, majf=5, minf=29
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=0,617184,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=1

Run status group 0 (all jobs):
  WRITE: bw=39.3MiB/s (41.2MB/s), 39.3MiB/s-39.3MiB/s (41.2MB/s-41.2MB/s), \
io=2411MiB (2528MB), run=61288-61288msec

Disk stats (read/write):
    dm-1: ios=2381/631516, merge=0/0, ticks=9376/82883752, in_queue=82929236, \
util=80.66%, aggrios=3246/655729, aggrmerge=0/0, aggrticks=10692/87229256, \
aggrin_queue=87239968, aggrutil=81.29%
    dm-0: ios=3246/655729, merge=0/0, ticks=10692/87229256, in_queue=87239968,\
 util=81.29%, aggrios=3235/653548, aggrmerge=11/2181, aggrticks=1945/108056, \
aggrin_queue=62344, aggrutil=31.25%
  sda: ios=3235/653548, merge=11/2181, ticks=1945/108056, in_queue=62344, \
util=31.25%
```

And again the line with the most interest:

```
  WRITE: bw=39.3MiB/s (41.2MB/s), 39.3MiB/s-39.3MiB/s (41.2MB/s-41.2MB/s), \
io=2411MiB (2528MB), run=61288-61288msec
```

The laptop is much slower with **bw=39.3MiB/s** for 1 job

```
WRITE: bw=52.6MiB/s (55.2MB/s), 24.6MiB/s-28.0MiB/s (25.8MB/s-29.4MB/s), \
io=3232MiB (3389MB), run=61428-61428msec
```

And for 2 jobs **bw=52.6MiB/s**. Comparing this 12 year old laptop
with a 5 year desktop from it holds quite well:

```
WRITE: bw=26.8MiB/s (28.1MB/s), 26.8MiB/s-26.8MiB/s (28.1MB/s-28.1MB/s), \
io=2581MiB (2706MB), run=96397-96397msec
```

As the desktop freezes when `fio` is run on one core, and the performance is
bad, it is a matter of system design, not of age in some cases.

[documentation]: https://fio.readthedocs.io/en/latest/fio_doc.html
[git]: git://git.kernel.dk/fio.git
[2.0.14]: https://github.com/axboe/fio/archive/fio-2.0.14.tar.gz
[3.19]: https://github.com/axboe/fio/archive/fio-3.19.tar.gz
