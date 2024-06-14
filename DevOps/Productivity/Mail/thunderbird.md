---
title: Thunderbird
author: Christian Külker
date: 2024-06-10
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Mail
description: Thunderbird mail client

---

## Introduction

Thunderbird is primarily recognized as an email client and secondarily
as a [Calendar](../Calendar/thunderbird.md) and this document
focus on the mail client part.

There are basically 2 ways on how to deploy a thunderbird mail configuration:
1) Configure one (master) client, copy `prefs.js` to `user.js`, edit `user.js`
and deploy it with your preferred deployment system (Puppet, Ansible, ...) and
2) Understand the configuration of thunderbird and create one configuration
from scratch inside `user.js`. This second method is described in the following
section. However due to its complexity it might not work and can take a huge
amount of time testing.


## Configure a Mail Account with a Template

Unfortunately preseeding a configuration of a mail account with thunderbird is
unlike mutt unnecessary complex. There are various entities below the master
key `mail` involved: `identies`, `servers`, `smtpservers`, `smtp`, `root`,
`accounts` and others. And they all interact. For now we try to configure the
bare minimum.  For the following we assume that the local server is `server1`.
Be aware that there are configurations where it is `server2`. To mitigate
problems it is advised to make a backup of `pref.js` and close `thunderbird`.

When setting up or understanding a Thunderbird mail account via the `prefs.js`
file, various keys play crucial roles in this configuration:

__server__: This pertains to incoming email servers (like POP3 or IMAP
servers). Each incoming server will have a unique key, e.g.,
`mail.server.server1`. Under this key, various properties like hostname, type
(POP3 or IMAP), username, etc., are stored.

__smtpserver__: This key represents outgoing mail servers (SMTP servers). Each
SMTP server will also have a unique identifier, e.g., `mail.smtpserver.smtp1`.
This will have properties like hostname, port, username, etc.

__smtp__:

- This key usually maps to the default SMTP server or provides global SMTP
settings. It might, for instance, determine which of the `smtpserver` entries
is used by default.

- This refers to the general SMTP settings and also has a key which indicates
the default SMTP server to be used, for example, mail.smtp.defaultserver.

- This is related to `smtpserver` but it's more of a master list that
  identifies which SMTP servers are available. It doesn’t hold the settings
  themselves but references to them.

   Example:
   ```javascript
      user_pref("mail.smtp.defaultserver", "smtp1");
   ```

__root__: This refers to the folder that stores your mail data. The relation
here is that each account will have a root folder where its emails, settings,
etc., are stored.

- This is related to the account structure and it helps in defining the folder
hierarchies, especially for IMAP accounts where the mailbox might be organized
into various sub-folders on the server.

__identity__: Identities relate to the "From" information when you send an
email. An identity contains data like your email address, your name, your
reply-to address, your signature, etc. A user can have multiple identities
(e.g., different email addresses or different names under the same account).
Each identity is associated with both an incoming server (`server`) and an
outgoing server (`smtpserver`).

   Example:
   ```javascript
      user_pref("mail.identity.id1.useremail", "you@example.com")
   ```

6. **account**: This key ties together servers and identities. An account
typically refers to a pair: one incoming server and one or multiple identities.
It can also specify which SMTP server to use.

   Example:
   ```javascript
   user_pref("mail.account.account1.server", "server1");
   user_pref("mail.account.account1.identities", "id1");
   ```

7. **accountmanager**: This key usually lists all the accounts and some global
settings related to accounts. For instance, it might list the default account
to use or all the accounts that have been set up.

   Example:
   ```javascript
   user_pref("mail.accountmanager.defaultaccount", "account1");
   user_pref("mail.accountmanager.accounts", "account1,account2");
   ```

The relation among these keys:

- An `account` links to both a `server` (incoming mail settings) and an
  `identity` (user's email details)
- An `identity` will specify which `smtpserver` (outgoing mail settings) to use
- `accountmanager` manages all `accounts` and some global mail settings.
- Both `server` and `smtpserver` will have their corresponding `root`
  directories, detailing where mail data is stored

By understanding these relationships, one can navigate the `prefs.js` file to
understand or modify the configuration of a Thunderbird mail account. However,
manual changes (in `user.js`) should be done with caution, and backups should
be taken to prevent unwanted modifications.

### Example

Be aware that this is only a configuration snippet of `user.js` that could be
used as an Ansible template and it might or might not work for you. This
example assumes that you have a local `account1` without SMTP server, so that
the next account is `account2` with `id1` and `smtp1`. Please adjust numbers to
your setup. This example assumes you have a hotmail account.  You also have to
replace the following placeholders in jinja2 (Ansible) notation.

Variable         | Format                | Example                     | Remark
---------------- | --------------------- | --------------------------- | ----------------
`{{AID}}`        | Integer               | 2                           | account
`{{IID}}`        | Integer               | 1                           | server (ingress)
`{{EID}}`        | Integer               | 1                           | smtp (egress}
`{{ID}`          | Integer               | 1                           | id
`{{HOME}}`       | "/home/UID"           | /home/bilbo                 |
`{{USER_NAME}}`  |  "firstname.lastname" | bilbo.baggings              |
`{{NAME}}`       | "Firstname Lastname"  | Bilbo Baggings              |
`{{CLIENT}`      | 24 char hash          | 24fb-5f97-88ce-9d34d49b1e18 |
`{{PROFILE_ID}}` | 8 char hash           | a2f9c5e2                    |

Also be aware to set the correct maximal ID number for accounts:
`user_pref("mail.account.lastKey", 3);` is correct if `account3` is used. So in
this case we assume that `{{AID}}` is the last ID.

```javascript
user_pref("mail.ab_remote_content.migrated", 1);
user_pref("mail.account.account{{AID}}.identities", "id{{ID}}}");
user_pref("mail.account.account{{AID}}.server", "server{{SID}}");
user_pref("mail.account.lastKey", {{AID}});
user_pref("mail.accountmanager.accounts", "account1,account{{AID}}");
user_pref("mail.accountmanager.defaultaccount", "account1");
user_pref("mail.accountmanager.localfoldersserver", "server{{SID}}");
user_pref("mail.folder.views.version", 1);
user_pref("mail.identity.id{{ID}}}.archive_folder",
          "imap://{{USER_NAME}}%40hotmail.com@outlook.office365.com/Archives");
user_pref("mail.identity.id{{ID}}}.draft_folder",
          "imap://{{USER_NAME}}%40hotmail.com@outlook.office365.com/Drafts");
user_pref("mail.identity.id{{ID}}}.drafts_folder_picker_mode", "0");
user_pref("mail.identity.id{{ID}}}.fcc_folder",
          "imap://{{USER_NAME}}%40hotmail.com@outlook.office365.com/Sent");
user_pref("mail.identity.id{{ID}}}.fcc_folder_picker_mode", "0");
user_pref("mail.identity.id{{ID}}}.fullName", "{{NAME}}");
user_pref("mail.identity.id{{ID}}}.reply_on_top", 1);
user_pref("mail.identity.id{{ID}}}.sig_bottom", true);
user_pref("mail.identity.id{{ID}}}.smtpServer", "server{{EID}}");
user_pref("mail.identity.id{{ID}}}.stationery_folder",
          "imap://{{USER_NAME}}%40hotmail.com@outlook.office365.com/Templates");
user_pref("mail.identity.id{{ID}}}.tmpl_folder_picker_mode", "0");
user_pref("mail.identity.id{{ID}}}.useremail", "{{USER_NAME}}@hotmail.com");
user_pref("mail.identity.id{{ID}}}.valid", true);
user_pref("mail.imap.chunk_size", 81920);
user_pref("mail.imap.min_chunk_size_threshold", 122880);
user_pref("mail.openMessageBehavior.version", 1);
user_pref("mail.purge_threshhold_mb", 20);
user_pref("mail.purge_threshold_migrated", true);
user_pref("mail.rights.version", 1);
user_pref("mail.root.imap",
          "{{HOME}}/.thunderbird/{{PROFILE_ID}}.default-default/ImapMail");
user_pref("mail.root.imap-rel", "[ProfD]ImapMail");
user_pref("mail.root.none",
          "{{HOME}}/.thunderbird/{{PROFILE_ID}}.default-default/Mail");
user_pref("mail.root.none-rel", "[ProfD]Mail");
user_pref("mail.server.server{{SID}}.check_new_mail", true);
user_pref("mail.server.server{{SID}}.clientid", "{{CLIENT}}");
user_pref("mail.server.server{{SID}}.directory",
"{{HOME}}/.thunderbird/{{PROFILE_ID}}.default-default/ImapMail/outlook.office365.com");
user_pref("mail.server.server{{SID}}.directory-rel",
          "[ProfD]ImapMail/outlook.office365.com");
user_pref("mail.server.server{{SID}}.hostname", "outlook.office365.com");
user_pref("mail.server.server{{SID}}.login_at_startup", true);
user_pref("mail.server.server{{SID}}.max_cached_connections", 5);
user_pref("mail.server.server{{SID}}.name", "{{USER_NAME}}@hotmail.com");
user_pref("mail.server.server{{SID}}.namespace.personal", "\"\"");
user_pref("mail.server.server{{SID}}.port", 993);
user_pref("mail.server.server{{SID}}.serverIDResponse",
"(\"name\" \"Microsoft.Exchange.Imap4.Imap4Server\" \"version\" \"15.20\")");
user_pref("mail.server.server{{SID}}.socketType", 3);
user_pref("mail.server.server{{SID}}.spamActionTargetAccount",
          "imap://{{USER_NAME}}%40hotmail.com@outlook.office365.com");
user_pref("mail.server.server{{SID}}.storeContractID",
          "@mozilla.org/msgstore/berkeleystore;1");
user_pref("mail.server.server{{SID}}.timeout", 29);
user_pref("mail.server.server{{SID}}.trash_folder_name", "Deleted");
user_pref("mail.server.server{{SID}}.type", "imap");
user_pref("mail.server.server{{SID}}.userName", "{{USER_NAME}}@hotmail.com");
user_pref("mail.smtp.defaultserver", "server{{EID}}");
user_pref("mail.smtpserver.server{{EID}}.authMethod", 3);
user_pref("mail.smtpserver.server{{EID}}.description", "Outlook.com (Microsoft)");
user_pref("mail.smtpserver.server{{EID}}.hostname", "smtp.office365.com");
user_pref("mail.smtpserver.server{{EID}}.port", 587);
user_pref("mail.smtpserver.server{{EID}}.try_ssl", 2);
user_pref("mail.smtpserver.server{{EID}}.username", "{{USER_NAME}}@hotmail.com");
user_pref("mail.smtpservers", "{{SMTP}");
user_pref("mail.spam.version", 1);
user_pref("mail.startup.enabledMailCheckOnce", true);
user_pref("mail.ui-rdf.version", 40);
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2024-06-10 | Initial release                                      |
