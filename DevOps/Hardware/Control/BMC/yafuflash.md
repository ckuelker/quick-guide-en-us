---
title: YAFUFlash
type: doc
author: Christian Külker
date: 2020-05-02
keywords:
    - YAFUFlash
categroies:
    - SysAdm
    - BMC
tags:
    - Firmware
    - OEM
description: Writing The BMC Firmware

---

## Writing The BMC Firmware

The **Board Management Controller** (BMC) firmware can be written via the
running OS. One tool for this is called **Yet Another Firmware Upgrade Flash**
(YAFUFlash) available for Linux or an other proprietary OS. YAFUFlash is
copyrighted by American Megatrends Inc (AMI). Usually the BMC firmware is
developed by the OEM (with the help of external companies) or solely by an
external company. Often BIOS developers and vendors also offer BMC firmware
services as the BMC needs to access BIOS information. Therefor often the BIOS
vendor is also the BMC firmware vendor on a given platform and usually one
should use the proprietary tools for flashing the BMC. The following example
uses YAFUFlash from AMI.

```shell
./Yafuflash -cd -info bmc_fw_v0.1.0.ima
./Yafuflash -cd  bmc_fw_v0.1.0.ima

