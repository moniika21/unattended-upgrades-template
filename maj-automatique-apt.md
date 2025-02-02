# Mise à jour automatique des paquets apt

## Installation de unattended-upgrades

<https://wiki.debian.org/UnattendedUpgrades>

- Installer le paquet `unattended-upgrades`
  - `sudo apt install unattended-upgrades`

## Configuration de auto-upgrades## Installation de unattended-upgrades

[Configurer Postfix pour envoyer des mails avec Gmail](https://www.it-connect.fr/configurer-postfix-pour-envoyer-des-mails-avec-gmail/)￼

- Si on est sur Debian trixie ou testing, alors mettre la ligne du backport de la version précédente de debian

```bash
"origin=Debian,codename=bookworm-backports,label=Debian-Backports";
```

- Ajouter ou remplacer aux bons endroits
  - `Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";`
  - `Unattended-Upgrade::Remove-New-Unused-Dependencies "true";`
  - `Unattended-Upgrade::Remove-Unused-Dependencies "true";`
- Décommenter les lignes qui ont été ajoutées si besoin
- Enregistrer et fermer le fichier
- Installer le paquet de vérification de la puissance électrique
  - `sudo apt install powermgmt-base`

## Modification des timers si nécessaire

> Warning
> The `OnCalendar=` with empty value is mandatory to override properly the default value.
>
### Timer apt-daily.timer

- Vérifier l'état du timer
  - `sudo systemctl status apt-daily.timer`
- Ouvrir le fichier du timer
  - `sudo systemctl edit apt-daily.timer`
- Redémarrer le timer
  - `sudo systemctl restart apt-daily.timer`  

### Timer apt-daily-upgrade.timer

- Vérifier l'état du timer
  - `sudo systemctl status apt-daily-upgrade.timer`
- Ouvrir le fichier du timer
  - `sudo systemctl edit apt-daily-upgrade.timer`
- Redémarrer le timer
  - `sudo systemctl restart apt-daily-upgrade.timer`

## Vérification des mises à jour automatiques

- Simuler une mise à jour sans exécuter de changements réels
  - `sudo unattended-upgrade --dry-run`
- Vérifier le statut des mises à jour automatiques
  - `cat /var/log/unattended-upgrades/unattended-upgrades.log`

## Vérification des timers apt et unattended-upgrades

- Vérifier l'état du timer apt-daily.timer
  - `systemctl status apt-daily.timer`
- Vérifier l'état du timer apt-daily-upgrade.timer
  - `systemctl status apt-daily-upgrade.timer`
- Vérifier l'exécution des timers de manière générale
  - `systemctl list-timers | grep apt`

## Annexes

Parce que Debian utilise `systemd`, il dispose de timers définis pour l'utilisation d'APT. Ces fichiers sont fournis par le paquet `apt`.

### Fichiers concernés

- Utilisé pour les mises à jour (`apt update`) :  
    `/lib/systemd/system/apt-daily.timer`
  - Peut être remplacé par :
        `/etc/systemd/system/apt-daily.timer.d/override.conf`

- Utilisé pour les mises à niveau (`apt upgrade`) :  
    `/lib/systemd/system/apt-daily-upgrade.timer`
  - Peut être remplacé par :  
        `/etc/systemd/system/apt-daily-upgrade.timer.d/override.conf`

- Logs disponibles :
  - `/var/log/dpkg.log`
  - `/var/log/unattended-upgrades/`

### Mon approche

L'exécution peut varier légèrement en raison du paramètre [`AccuracySec`](https://man.archlinux.org/man/systemd.timer.5), qui réduit la consommation d'énergie.  
Le paramètre [`RandomizedDelaySec`](https://man.archlinux.org/man/systemd.timer.5) est généralement défini à `0` pour éviter trop d'aléatoire.

Vous pouvez vérifier les timestamps avec :

```bash
systemd-analyze calendar "<expression>"
```

Exemple :

```bash
# 07:15 AM le dernier samedi de chaque mois
Sat *-*~07/1 07:15:00
```

### Planification sur mon serveur

- Création de sauvegarde → 06:00 AM tous les jours
- Migration des sauvegardes → 06:15 AM tous les jours
- Mises à jour des images Docker (si Watchtower est installé) → 10:00 AM tous les jours
  - Voir [docker-compose template](https://github.com/moniika21/docker-compose-template/blob/main/watchtower/docker-compose.yaml)
- Mise à jour APT (`apt update`) → 07:15 AM tous les jours
- Mise à niveau APT (`apt upgrade`) → 07:30 AM tous les jours

### Envoi d'un mail en cas de problème

```bash
Unattended-Upgrade::Mail "personal-mail@gmail.com";
Unattended-Upgrade::MailReport "only-on-error";
```

[Configurer Postfix pour envoyer des mails avec Gmail](https://www.it-connect.fr/configurer-postfix-pour-envoyer-des-mails-avec-gmail/)
