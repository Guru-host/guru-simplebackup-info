# Changelog

All notable changes to Guru SimpleBackup are documented here. This mirrors the
`== Changelog ==` section of the plugin's own `readme.txt`.

## 0.7.1

* Fixed: a backup that stopped halfway for no reason the log could give — "Stopped unexpectedly and was not resumed", even though the server and its scheduler were working — is now carried on. The cause on the sites it was found on was that the scheduled event that continues a backup after each step was lost between two steps, so a job that had saved its progress cleanly was left with nothing to run it and was given up on fifteen minutes later. The plugin now notices within a few minutes, continues from the progress it saved, and says "Resumed after the next pass went missing" in the log. It does this at most five times for one backup, and never for a backup whose step was cut off part way (a killed process), which is still marked as failed as before — carrying on from the middle of such a step could put the same data in the archive twice. A restore is handled the same way.
* Fixed: a backup whose upload was cut off — the server ran out of time or memory, or PHP was restarted — left its finished archive in the backup folder. The Backups list called it "Did not finish", nothing counted the file, and nothing ever removed it: about 1 GB more on the disk for every night that happened, on large sites roughly one night in three. Such an archive now follows the same rules as any other. If nothing else holds the backup it is kept as the copy on this server and only the newest two are kept, as for any backup that was not uploaded; if it did reach this server's folder it counts towards the schedule's keep count like a completed backup and ages out with it. The failure is still shown, and you are still emailed.
* Fixed: backups that stopped part way in earlier versions left half-written zips and ".zip.entries" index files in the backup folder, and finished archives that were never listed as copies. They are now cleared up: after every backup, when a backup stops, when this version is installed, and by `wp simple-backup prune`, which says which files it kept and which it removed and how much space that gave back. Only files that can never be restored from are deleted, only once they have been left alone for six hours, and never a file that a backup in the list or a running backup accounts for.
* Fixed: when a backup was stopped or killed, the working index of the archive being written (5-10 MB on a large site) was left behind next to the half-written zip.

## 0.7.0

* "Email me when a backup fails" now actually sends an email. Until now the box was saved and shown but nothing ever read it, so an upload that failed every night could go unnoticed for weeks. You are now emailed — to the address under Destinations, or the site administrator's — when a backup cannot be finished, and when it is made but one of your destinations could not store it. The message names the site, the backup, the destination and the reason, and links to the Backups tab. The same problem is reported at most about once a day, so a nightly failure reminds you without flooding you, and if WordPress cannot send the email the plugin says so on its own screens and in the log.
* Fixed: after the site's secret keys (AUTH_KEY, SECURE_AUTH_SALT) are changed — which a security clean-up does — a saved SFTP password or private key can no longer be read, and every upload failed with a message that pointed at the wrong thing. The plugin now says exactly that: a notice on every one of its screens, a red row at the top of Status & Help, a hint under the field on the Destinations tab, a warning as each backup starts, and a mention in the failure email — with what to do (enter it again and save).
* A backup that did not reach every destination no longer ends with "Backup complete". The log says "Backup finished, but 1 destination failed: …", and the Backups list shows "Completed with warnings" with the reason beside it, without opening the log. The backup itself is still a complete, usable archive, and the plugin's completion hook still fires.
* Fixed: when "keep a copy on this server" was off and the upload failed, each night's archive was left in the backup folder with nothing recording it, and when its row aged out it was removed from the list but never from the disk — one site collected 58 GB this way. A backup that no destination stored is now kept as a copy you can see, download and restore, only the newest two scheduled ones are kept so a failing upload cannot fill the disk, and deleting any backup removes its file from the server. Status & Help says how many backups are on this server and nowhere else. Manual backups are never removed automatically. Archives left behind by earlier versions appear in the list after the upgrade.
* Fixed: a backup that was stopped or killed before its archive was finished no longer leaves the half-written zip behind.
* New hook for developers: `wpsb_destination_failed( $destination, $record, $reason )`, and the `wpsb_keep_unstored_backups` filter for how many unstored scheduled backups are kept.

## 0.6.0

* Database tables can now be left out of a backup. A plugin's 404 log, a security audit trail or a scan cache can be bigger than the whole rest of your database put together, and nobody restores a backup to get their 404 log back — the Schedule tab now takes a list of table names to skip, with the common ones offered as one-click suggestions. Empty to begin with, so nothing changes until you ask for it, and the tables WordPress cannot work without are refused outright. Excluded tables are not in the backup and a restore cannot bring them back, which the screen says plainly.
* While the database is being exported, the progress bar now moves in step with how much data has actually been written, and the message names the table it is working on — "table 41 of 161 — wp_redirection_404 (591,500 of 813,627 rows)". Before, one enormous table could hold the percentage still for twenty minutes and there was no way to tell hard work from a hang. The log also points out any single table that is a quarter of the database or more.
* The free space check now reserves what the backup needs at its busiest moment rather than what the finished zip comes to. Halfway through, the uncompressed database export and the archive being packed are both on disk at once, and the old figure counted neither properly. The log now spells out what it has reserved and whether the file total was measured or merely assumed.
* Fixed: a backup killed part way through — by a fatal error, an out of memory or a restarted container — left a job marked "running" that refused every later backup until an hourly clean-up happened to run. On a quiet site, or one with WordPress' own scheduler switched off, that could be for ever, with no way out except editing the database by hand. Stale jobs are now cleared the moment a backup or restore is started, the clean-up runs every five minutes instead of hourly, and `wp simple-backup cancel` clears a stuck job deliberately.
* Fixed: since a crashed run no longer blocks the next one, its leftover working files (the uncompressed database export, a partial archive) used to be left behind for good instead of being cleaned up. They are now removed as soon as the job is cleared.
* A backup that never finished now says so in the list, with the reason beside it, instead of sitting there as "running" for ever.
* Fixed: pressing Stop while a restore was taking its safety backup stopped the safety backup instead of the restore — and the restore then carried on without a real way back. Stop now stops the restore itself, and a restore refuses to continue unless its safety backup actually completed.
* `wp simple-backup run` accepts `--exclude-tables` to leave tables out of a single run without changing your settings.

## 0.5.2

* Fixed: a job already being worked on by one request (the browser's own polling, or wp-cron ticking it in the background) could be picked up again by another overlapping request at the same time, occasionally doubling up a pass and, in rare cases, throwing a harmless warning into the log. The job lock now uses an atomic claim that only one request can ever win.

## 0.5.1

* Fixed: when "keep a copy on this server" was switched off, the local copy of every backup was silently kept anyway instead of being removed once another destination held it — it now stays only until at least one other destination confirms it has the backup, and is never removed if every other destination fails.

## 0.5.0

* The plugin now announces new versions by itself: WordPress shows its ordinary update notice when Guru-host publishes one, and the Status tab gains a "Check for updates now" button. Updates are fetched from your account — nothing downloads automatically.

## 0.4.0

* The plugin is now Guru SimpleBackup, with the slug and text domain the WordPress.org directory requires. Nothing else changes: settings, backups and schedules carry over untouched.

## 0.3.0

* A quick note can be attached to any backup — type it next to "Back up now", or add one to an existing backup from the list — and it is shown when choosing what to restore.

## 0.2.0

* Restores All-in-One WP Migration `.wpress` files: upload one or scan for it on the server, and it goes through the same pipeline as the plugin's own backups — safety backup first, imported beside the live site, links updated automatically.
* SSH keys protected by a passphrase now work for SFTP: enter the passphrase next to the key, both stored encrypted.

## 0.1.1

* A restore no longer locks itself out behind WordPress' maintenance page: visitors see the plugin's own holding page while the admin screens, the login page and the background work all keep running.
* The database is imported into temporary tables and swapped in at the end, so the site stays up for the whole restore and the swap itself takes moments.

## 0.1.0

* First release.
