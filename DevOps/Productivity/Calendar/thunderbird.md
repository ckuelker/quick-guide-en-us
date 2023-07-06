---
title: Thunderbird
linkTitle: Thunderbird
author: Christian Külker
date: 2023-07-07
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Calendar
description: Thunderbird calendar

---

## Introduction

Thunderbird, while best known as an email client, also boasts a calendar mode.
Unique to this application, its calendar mode is constructed as a web page and
is seamlessly served via Chrome. Thanks to this tight integration, users often
remain unaware that Thunderbird operates as a local web application. This
document aims to provide helpful tips for utilizing and configuring the
Thunderbird calendar.

## Change the Date Format

By default, Thunderbird's date formatting adheres to the locale setting of the
operating system. For instance, if you're using the locale `en_us.UTF8`, you'll
encounter dates in the format "2023/07/30". But if you're utilizing the German
locale, it'll display as "30.07.2023". Now, what if you wish to employ the ISO
format while retaining the German language setting? Contrary to the Evolution
email client, Thunderbird's standard configuration dialog doesn't readily
support this preference.

__Solution 1:__ Opt for a locale that supports your desired short date format.

__Solution 2:__ This is specifically for users with Thunderbird version 91 or
later:

1. Open the `Config Editor` by navigating to Preferences > General. Scroll down
to the bottom of the page.

2. Adjust the setting `intl.regional_prefs.use_os_locales` to `false`.

     intl.regional_prefs.use_os_locales = false

3. Next, alter `intl.date_time.pattern_override.date_short` to the string value
   "`yyyy-MM-dd`".

    intl.date_time.pattern_override.date_short = yyyy-MM-dd

For a comprehensive guide on this topic, please refer to Mozilla's official
[guide](https://support.mozilla.org/en-US/kb/customize-date-time-formats-thunderbird).

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2023-07-07 | Initial release                                      |
