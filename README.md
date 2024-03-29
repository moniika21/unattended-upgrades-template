# Template for systemd timers

## [Unattended upgrades](https://wiki.debian.org/UnattendedUpgrades)

Because Debian is using the `systemd` system, it has timers defined for APT use, these files are provided by the apt package.

The relevant files are:

Used for `updates` (apt update): `/lib/systemd/system/apt-daily.timer`

- gets overridden by `/etc/systemd/system/apt-daily.timer.d/override.conf`
  
Used for `upgrades` (apt upgrade): `/lib/systemd/system/apt-daily-upgrade.timer`

- gets overridden by `/etc/systemd/system/apt-daily-upgrade.timer.d/override.conf`

The best way to modify these files is to use embeded edit systemctl way:

```bash
systemctl edit apt-daily.timer
```

```bash
systemctl edit apt-daily-upgrade.timer
```

And to restart the desired service:

```bash
systemctl restart apt-daily.timer
```

```bash
systemctl restart apt-daily-upgrade.timer
```

Verify that it worked:

```bash
systemctl status apt-daily.timer
```

```bash
systemctl status apt-daily-upgrade.timer
```

> **Warning**
> The `OnCalendar=` with empty value is mandatory to override properly the default value.

## Choose [what to update](https://wiki.debian.org/fr/unattended-upgrades)

```bash
sudo vim /etc/apt/apt.conf.d/50unattended-upgrades
```
## Logs

And there is always `/var/log/dpkg.log`, or the files in `/var/log/unattended-upgrades/`.

## My approach

I may be a slightly diffrent time when this is executed because of [AccuracySec](https://man.archlinux.org/man/systemd.timer.5) to reduce power consumption.
[RandomizedDelaySec](https://man.archlinux.org/man/systemd.timer.5) is rather settled to 0 in order to prevent too much randominess
You can check timestamps with

```bash
systemd-analyze calendar "<expression>"
```
Example:

Sat *-*~07/1 07:15:00 -> 07:15 AM on the last saturday of each month


This is my approach on my server:
- backup creation -> 6:00 AM everyday
- backup migration -> 6:00 AM everyday
  
- If watchtower is installed:
- [docker image update](https://github.com/containrrr/watchtower) -> 07:30 AM on the last saturday of each month (see [docker compose template](https://github.com/moniika21/docker-compose-template/blob/main/watchtower/docker-compose.yaml) for watchtower)
  
- apt update -> 07:15 AM on the last saturday of each month
- apt upgrade -> 07:30 AM on the last saturday of each month



## Send mail if problem

Unattended-Upgrade::Mail "personal-mail@gmail.com";
Unattended-Upgrade::MailReport "only-on-error";

https://www.it-connect.fr/configurer-postfix-pour-envoyer-des-mails-avec-gmail/
