---
title: Thunderbird
linkTitle: Thunderbird
author: Christian Külker
date: 2023-07-10
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

While Thunderbird is primarily recognized as an email client, it also features
a unique calendar mode. Distinctively, this calendar mode is designed as a web
page and is seamlessly delivered through Chrome. Due to this tight integration,
many users might not even realize that Thunderbird functions as a local web
application. This document is intended to offer useful tips for maximizing the
use and configuration of the Thunderbird calendar.

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

    ```
    intl.regional_prefs.use_os_locales = false
    ```

3. Next, alter `intl.date_time.pattern_override.date_short` to the string value
   "`yyyy-MM-dd`".

    ```
    intl.date_time.pattern_override.date_short = yyyy-MM-dd
    ```

For a comprehensive guide on this topic, please refer to Mozilla's official
[guide](https://support.mozilla.org/en-US/kb/customize-date-time-formats-thunderbird).

## Change the Calendar Font Size

Although users can adjust the font and its size in the 'Preferences General'
setting of the Thunderbird application, it doesn't affect the calendar. For
instance, on Debian 12 Bookworm under MATE, the default font is Cantarell at a
size of 17, relatively large. To display four calendar entries spaced 15 minutes
apart within a single hour, users either need to significantly zoom in or the
display splits into two columns. Notably, unlike in Evolution, setting the time
division to 15 minutes doesn't alter this behavior.

As there's no direct configuration option to adjust the calendar font or size,
users need to employ a workaround by modifying the application's underlying
CSS.

To identify the current settings and values, initiate Thunderbird with web
tools using:` thunderbird --devtools`

1. Enable a Custom CSS File

   Navigate to the `Config Editor` and configure
   `toolkit.legacyUserProfileCustomizations.stylesheets` to `true`.

    ```
    toolkit.legacyUserProfileCustomizations.stylesheets = true
    ```

2. Set Up an Empty CSS File

   Create an empty file named `userChrome.css` inside the `chrome` directory.

    ```
    cd ~/.thunderbird/*.default-default
    mkdir chrome
    cd chrome
    touch useChrome.css
    ```

3. Test the CSS Integration

   To verify Thunderbird's use of the file, add a test CSS rule. For instance,
   adjusting the size of the calendar day header:

   ```
    .day-column-container {
       font-size: 12px;
    }
    ```

   It's advisable to utilize the development tools and the inspector to check
   __computed__ values before and after modifications to discern any changes.
   However, be mindful that the application's HTML and CSS might evolve over
   time.

4. Address the Font Size Issue

   To remedy the specific font size concern, reduce the font size from 17.333px
   to 16px and eliminate some padding. The original `.day-column-container`
   setting can be discarded.

   ```
   .calendar-item-flex {
        padding-top: 0px !important;
        padding-bottom: 0px !important;
        row-gap: 2px !important;
    }
    .event-name-label {
        font-weight: 400 !important;
        font-size: 16px;
    }
    ```

5. Restart Required

    Remember, any modifications necessitate a Thunderbird restart for changes
    to take effect.

For additional guidance, refer to:
 - https://www.userchrome.org/how-create-userchrome-css.html

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-07-10 | Add section about changing calendar font size        |
| 0.1.0   | 2023-07-07 | Initial release                                      |
