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

## My approach

This is my update approach on my server:

- apt update -> 06:00 AM on Saturday
- apt upgrade -> 06:30 AM on Saturday
- [docker image update](https://github.com/containrrr/watchtower) -> 07:00 AM on Saturday (see [docker compose template](https://github.com/moniika21/docker-compose-template/blob/main/watchtower/docker-compose.yaml) for watchtower)

## TODO

Send notif if problem
install a mailX package
Unattended-Upgrade::Mail "evancutaia21@gmail.com";
Unattended-Upgrade::MailReport "only-on-error";
