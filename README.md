# Guru SimpleBackup

A WordPress backup and restore plugin that stays small enough to trust: database
and files into one zip, kept on this server and on your own SFTP server, on a
daily, weekly and monthly schedule, restorable in two clicks.

Sold once, from **[guru-host.co.uk](https://client.guru-host.co.uk/store/guru-simplebackup)**
— no subscription, no licence keys, no per-site limits, updates included.
Licence terms are provided with the purchase.

> **This repository holds only the public feature overview and release notes.**
> The plugin's source lives in a private repository and the installable zip is
> only available to customers from their [account page](https://client.guru-host.co.uk/account)
> after purchase — there is nothing to clone or download here.

## What it does

* Full backups: database, uploads, themes and plugins, in one zip file
* Two places to keep them: a folder on this server, and your own SFTP server, with the port and path you choose
* SFTP logs in with a password or an SSH private key (passphrase-protected keys work too) — both stored encrypted
* Daily, weekly and monthly schedules, each with its own time and its own "keep the last N" rule
* Download any backup to your computer, from whichever place holds it
* Restore from your list, from a file you upload through the browser, or from a file you copied onto the server yourself
* Restores All-in-One WP Migration `.wpress` files too, so you can switch without being locked in
* A safety backup is always taken before a restore, so it can be undone
* Moving a site to a new address rewrites the stored links for you
* Skip database tables you do not want backed up — a plugin's 404 log can be larger than the rest of your database put together
* Emails you when a backup fails, or when it was made but one of your destinations could not store it — with the reason, and no more than about once a day for the same problem
* Says plainly when a saved SFTP password or key can no longer be read (for instance after the site's secret keys were changed), instead of failing quietly
* A backup that did not reach every destination is shown as "Completed with warnings", and a failing upload cannot fill your server's disk
* Every screen explains, in ordinary words, what the setting does

## Built for real hosting

Large sites break naive backup plugins because a web server kills long requests.
Everything here runs in short passes that save their position and continue:
exporting the database, packing the archive, uploading it, unpacking it and
importing it. Browser uploads are split into pieces, so a host's upload limit
does not matter. Downloads support resuming.

## Release notes

See [CHANGELOG.md](CHANGELOG.md) for the full version history, or the
[Releases](https://github.com/Guru-host/guru-simplebackup-info/releases) page for the notes of each version.

## Questions

* Product page & purchase: [client.guru-host.co.uk/store/guru-simplebackup](https://client.guru-host.co.uk/store/guru-simplebackup)
* Existing customers: [your account](https://client.guru-host.co.uk/account) for downloads and support
