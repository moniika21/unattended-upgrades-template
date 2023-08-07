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
systemctl edit apt-daily-upgrade.timer
```

And to restart the desired service:

```bash
systemctl restart apt-daily.timer
systemctl restart apt-daily-upgrade.timer
```

Verify that it worked:

```bash
systemctl status apt-daily.timer
systemctl status apt-daily-upgrade.timer
```

> **Warning**
> The `OnCalendar=` with empty value is mandatory to override properly the default value.

## Choose [what to update](https://wiki.debian.org/fr/unattended-upgrades)

```bash
sudo vim /etc/apt/apt.conf.d/50unattended-upgrades
```

## My approach

I may be a slightly diffrent time when this is executed because of [AccuracySec](https://man.archlinux.org/man/systemd.timer.5) to reduce power consumption.
[RandomizedDelaySec](https://man.archlinux.org/man/systemd.timer.5) is rather settled to 0 in order to prevent too much randominess
You can check timestamps with

```bash
systemd-analyze calendar "<expression>"
```

This is my approach on my server:
- backup creation -> 5:00 AM everyday
- backup migration -> 6:00 AM everyday
- apt update -> 07:00 AM on the last saturday of each month
- apt upgrade -> 07:15 AM on the last saturday of each month

If watchtower is installed:

- [docker image update](https://github.com/containrrr/watchtower) -> 07:30 AM on the last saturday of each month (see [docker compose template](https://github.com/moniika21/docker-compose-template/blob/main/watchtower/docker-compose.yaml) for watchtower)

## TODO

Send notif if problem
install a mailX package
Unattended-Upgrade::Mail "";
Unattended-Upgrade::MailReport "only-on-error";
