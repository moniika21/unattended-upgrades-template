# Template for systemd timers

## [Unattended upgrades](https://wiki.debian.org/UnattendedUpgrades)

Because Debian is using the `systemd` system, it has timers defined for APT use, these files are provided by the apt package.

The relevant files are:

Used for `downloads`: /lib/systemd/system/`apt-daily.timer`

- gets overridden by /etc/systemd/system/apt-daily.timer.d/`override.conf`
  
Used for `upgrades`: /lib/systemd/system/`apt-daily-upgrade.timer`

- gets overridden by /etc/systemd/system/apt-daily-upgrade.timer.d/`override.conf`

You can also rename these override file by whatever you want till it finish by `.conf`

This is my update approach on my server:

- apt update -> 06:00 AM everyday
- apt upgrade -> 06:30 AM everyday
- [docker image update](https://github.com/containrrr/watchtower) -> 07:00 AM everyday (see [docker compose template](https://github.com/moniika21/docker-compose-template/blob/main/watchtower/docker-compose.yaml) for watchtower)

## TODO

Change udpdate to one time per week
and send notif if problem
