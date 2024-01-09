
# Arch-Update

## Table des matières

- [Description](#description)
- [Installation](#installation)
- [Utilisation](#usage)
- [Documentation](#documentation)
- [Astuces et conseils](#tips-and-tricks)
- [Contributions](#contributing)

## Description

Un notificateur/vérificateur de mise à jour pour Arch Linux qui vous assiste avec des tâches importantes avant et après la mise à jour. Inclut une icône cliquable (.desktop) facilement intégrable à tout environnement de bureau (DE/WM), dock, barre de statut/démarrage ou menu d'applications.
Support optionnel pour les mises à jour de paquets AUR/Flatpak et les notifications de bureau.

Fonctionnalités :

- Inclut une icône cliquable (.desktop) qui change automatiquement pour agir comme un notificateur/vérificateur de mises à jour. Facile à intégrer avec tout environnement de bureau (DE/WM), dock, barre de statut/de lancement, menu d'applications, etc.
- Vérification automatique et liste de tous les paquets disponibles pour mise à jour (via [checkupdates](https://archlinux.org/packages/extra/x86_64/pacman-contrib/ "paquet pacman-contrib")).
- Propose d'imprimer les dernières nouvelles d'Arch Linux avant d'appliquer les mises à jour (via [curl](https://archlinux.org/packages/core/x86_64/curl/ "paquet curl") et [htmlq](https://archlinux.org/packages/extra/x86_64/htmlq/ "paquet htmlq")).
- Vérification automatique et liste des paquets orphelins et proposition de les supprimer.
- Vérification automatique pour les anciens paquets mis en cache et/ou désinstallés dans le cache de `pacman` et proposition de les supprimer (via [paccache](https://archlinux.org/packages/extra/x86_64/pacman-contrib/ "paquet pacman-contrib")).
- Aide au traitement des fichiers pacnew/pacsave (via [pacdiff](https://archlinux.org/packages/extra/x86_64/pacman-contrib/ "paquet pacman-contrib"), nécessite éventuellement [vim](https://archlinux.org/packages/extra/x86_64/vim/ "paquet vim") comme programme de fusion par défaut [merge program](https://wiki.archlinux.org/title/Pacman/Pacnew_and_Pacsave#pacdiff "programme de fusion pacdiff")).
- Vérification automatique pour les mises à jour du noyau en attente nécessitant un redémarrage pour être appliquées et proposition de le faire s'il y en a une.
- Support pour [sudo](https://archlinux.org/packages/core/x86_64/sudo/ "paquet sudo") et [doas](https://archlinux.org/packages/extra/x86_64/opendoas/ "paquet opendoas").
- Support optionnel pour la mise à jour des paquets AUR (via [yay](https://aur.archlinux.org/packages/yay "paquet AUR yay") ou [paru](https://aur.archlinux.org/packages/paru "paquet AUR paru")).
- Support optionnel pour la mise à jour des paquets Flatpak (via [flatpak](https://archlinux.org/packages/extra/x86_64/flatpak "paquet Flatpak")).
- Support optionnel pour les notifications de bureau (via [libnotify](https://archlinux.org/packages/extra/x86_64/libnotify "paquet libnotify"), voir <https://wiki.archlinux.org/title/Desktop_notifications>).
- 

## Installation

### AUR

Installez le paquet AUR [arch-update](https://aur.archlinux.org/packages/arch-update "paquet AUR arch-update").

### Depuis la Source

Installez les dépendances :

```bash
sudo pacman -S --needed pacman-contrib curl htmlq diffutils
```

Téléchargez l'archive de la [dernière version stable](https://github.com/Antiz96/arch-update/releases/latest) et extrayez-la *(alternativement, vous pouvez cloner ce dépôt via `git`)*.

Pour installer `arch-update`, allez dans le répertoire extrait/cloné et exécutez la commande suivante :

```bash
sudo make install
```

Pour désinstaller `arch-update`, allez dans le répertoire extrait/cloné et exécutez la commande suivante :

```bash
sudo make uninstall
```

## Utilisation

L'utilisation consiste à intégrer [le fichier .desktop](#the-desktop-file) n'importe où (cela peut être votre bureau, votre dock, votre barre de statut/de lancement et/ou votre menu d'applications) et à activer le [timer systemd](#the-systemd-timer).

Voici une petite présentation/revue YouTube de `arch-update` que [Cardiac](https://github.com/Cardiacman13) et moi avons faite sur [sa chaîne YouTube](https://www.youtube.com/@Cardiacman) (**les vidéos là-bas, y compris celle-ci, sont en français**) :

*Avertissement : Les fonctionnalités et le comportement par défaut d'Arch-Update peuvent avoir changé/évolué depuis !*

[![présentation youtube](https://github.com/Antiz96/arch-update/assets/53110319/23af5180-1881-486d-bd5a-3edd48ed1a08)](https://www.youtube.com/watch?v=QkOkX70SEmo)

### Le fichier .desktop

Le fichier .desktop se trouve dans `/usr/share/applications/arch-update.desktop` (ou dans `/usr/local/share/applications/arch-update.desktop` si vous avez installé `arch-update` [depuis la source](#from-source)).  
Son icône changera automatiquement en fonction des différents états (vérification des mises à jour, mises à jour disponibles, installation des mises à jour, à jour).  
Il lancera la série de fonctions pertinentes pour effectuer une mise à jour complète et appropriée lorsqu'on clique dessus (voir le chapitre [Documentation](#documentation)). Il est facile à intégrer avec n'importe quel DE/WM, dock, barre de statut/de lancement ou menu d'applications.

### Le timer systemd

Il y a un service systemd dans `/usr/lib/systemd/user/arch-update.service` (ou dans `/usr/local/lib/systemd/user/arch-update.service` si vous avez installé `arch-update` [depuis la source](#from-source)) qui exécute la fonction `check` lorsqu'il est lancé (voir le chapitre [Documentation](#documentation)).  
Pour le démarrer automatiquement **au démarrage et ensuite une fois par heure**, activez le timer systemd associé (vous pouvez modifier le cycle de vérification automatique selon vos préférences, voir le chapitre [Astuces et conseils - Modifier le cycle de vérification automatique](#modify-the-auto-check-cycle)) :

```bash
systemctl --user enable --now arch-update.timer
```

### Screenshot

Personally, I integrated the .desktop icon in my top bar.  
It is the first icon from the left.

![icon](https://github.com/Antiz96/arch-update/assets/53110319/25f3d2ca-b9d3-4a32-ace3-b0fa785662c2)

When `arch-update` is checking for updates, the icon changes accordingly (the `check` function is automatically triggered at boot and then once every hour if you enabled the [systemd timer](#the-systemd-timer) and can be manually triggered by running the `arch-update -c` command):

![icon-checking](https://github.com/Antiz96/arch-update/assets/53110319/f4c09898-7b21-430f-84be-431a31e25c3f)

If there are new available updates, the icon will show a bell sign and a desktop notification indicating the number of available updates will be sent (requires [libnotify/notify-send](https://archlinux.org/packages/extra/x86_64/libnotify/ "libnotify package")):

![icon-update-available](https://github.com/Antiz96/arch-update/assets/53110319/c1526ce7-5f94-41b8-a8fa-3587b9d00a9d)
![notification](https://github.com/Antiz96/arch-update/assets/53110319/631b8e67-487a-441a-84b4-6cce95223729)

When the icon is clicked, it launches the relevant series of functions to perform a complete and proper update starting by refreshing the list of packages available for updates, print it inside a terminal window and asks for the user's confirmation to proceed with the installation (it can also be launched by running the `arch-update` command, requires [yay](https://aur.archlinux.org/packages/yay "yay") or [paru](https://aur.archlinux.org/packages/paru "paru") for AUR packages update support and [flatpak](https://archlinux.org/packages/extra/x86_64/flatpak/) for Flatpak packages update support):

*The colored output can be disabled with the `NoColor` option in the `arch-update.conf` configuration file.*  
*The versions changes in the packages listing can be hidden with the `NoVersion` option in the `arch-update.conf` configuration file.*  
*See the [arch-update.conf documentation chapter](#arch-update-configuration-file) for more details.*

![listing-packages](https://github.com/Antiz96/arch-update/assets/53110319/43a990c8-ed93-420f-8c46-d50d60bff03f)

Once you gave the confirmation to proceed, `arch-update` offers to print latest Arch Linux news.  
Arch news that have been published within the last 15 days are tagged as `[NEW]`.  
Select which news to read by typing its associated number.  
After your read a news, `arch-update` will once again offers to print latest Arch Linux news, so you can read multiple news at once.  
Simply press "enter" without typing any number to proceed with update:

*The Arch news listing/printing can be skipped with the `NoNews`  option in the `arch-update.conf` configuration file.*  
*Note that using this option will generate a warning message as a reminder that users are expected to regularly check Arch news.*  
*See the [arch-update.conf documentation chapter](#arch-update-configuration-file) for more details.*

![list-news](https://github.com/Antiz96/arch-update/assets/53110319/b6883ec4-8c44-4b97-86d9-4d0a304b748b)

While `arch-update` is performing updates, the icon changes accordingly:

![icon-installing](https://github.com/Antiz96/arch-update/assets/53110319/7c74ce84-7de4-4e09-aa2a-66afad9e61d7)

When the update is over, the icon changes accordingly:

![icon-up-to-date](https://github.com/Antiz96/arch-update/assets/53110319/03f224a5-5fcf-450d-9aa5-bae90e7d2e8a)

`arch-update` will then search for orphan packages/unused Flatpak packages and offers to remove them (if there are):

![orphan-packages](https://github.com/Antiz96/arch-update/assets/53110319/76b795e5-076e-4070-9fe2-73165503011b)

![flatpak-unused-packages](https://github.com/Antiz96/arch-update/assets/53110319/cd4053bb-623e-44c2-8c74-9f87710f4074)

`arch-update` will also search for old and/or uninstalled cached packages and offers to remove them (if there are):

*The default behavior is to keep the last 3 cached versions of installed packages and remove every cached versions of uninstalled packages.*  
*You can modify the number of old packages' versions and uninstalled packages' versions to keep in pacman's cache respectively with the `KeepOldPackages=Num` and `KeepUninstalledPackages=Num` options in the `arch-update.conf` configuration file.*  
*See the [arch-update.conf documentation chapter](#arch-update-configuration-file) for more details.*

![cached-packages](https://github.com/Antiz96/arch-update/assets/53110319/7199bbf1-acd8-49a1-80eb-e9874b94fba6)

Additionally `arch-update` will search for pacnew/pacsave files and offers to process them via `pacdiff` (if there are):

![pacnew-files](https://github.com/Antiz96/arch-update/assets/53110319/5ee627ee-f7b7-4528-bf41-435d3c5892ac)

Finally, `arch-update` will check if there's a pending kernel update requiring a reboot to be applied and offers you to do so (if there is):

![kernel-pending-update](https://github.com/Antiz96/arch-update/assets/53110319/14aef5b2-db32-4296-8a60-bc840c09d457)

## Documentation

### arch-update

```text
An update notifier/applier for Arch Linux that assists you with
important pre/post update tasks.

Run arch-update to perform the main "update" function:
Print the list of packages available for update, then ask for the user's confirmation
to proceed with the installation.
Before performing the update, offer to print the latest Arch Linux news.
Post update, check for orphan/unused packages, old cached packages, pacnew/pacsave files
and pending kernel update and, if there are, offers to process them.

Options:
-c, --check    Check for available updates, send a desktop notification containing the number of available updates (if libnotify is installed)
-h, --help     Display this message and exit
-V, --version  Display version information and exit

Exit Codes:
0  OK
1  Invalid option
2  No privilege method (sudo or doas) is installed
3  Error when changing icon
4  User didn't gave the confirmation to proceed
5  Error when updating the packages
6  Error when calling the reboot command to apply a pending kernel update
```

For more information, see the arch-update(1) man page.  
Certain options can be enabled/disabled or modified via the arch-update.conf configuration file, see the arch-update.conf(5) man page.

### arch-update configuration file

```text
The arch-update.conf file is an optional configuration file for arch-update to enable/disable
or modify certain options within the script.

This configuration file has to be located in "${XDG_CONFIG_HOME}/arch-update/arch-update.conf"
or "${HOME}/.config/arch-update/arch-update.conf".

The supported options are:

- NoColor # Do not colorize output.
- NoVersion # Do not show versions changes for packages when listing pending updates.
- NoNews # Do not print Arch news. Note that using this option will generate a warning message as a reminder that users are expected to regularly check Arch news.
- KeepOldPackages=Num # Number of old packages' versions to keep in pacman's cache. Defaults to 3.
- KeepUninstalledPackages=Num # Number of uninstalled packages' versions to keep in pacman's cache. Defaults to 0.

Options are case sensitive, so capital letters have to be respected.
```

For more information, see the arch-update(5) man page.

## Tips and tricks

### AUR Support

Arch-Update supports AUR packages update when checking and installing updates if **yay** or **paru** is installed:  
See <https://github.com/Jguer/yay> and <https://aur.archlinux.org/packages/yay>  
See <https://github.com/morganamilo/paru> and <https://aur.archlinux.org/packages/paru>

### Flatpak Support

Arch-Update supports Flatpak packages update when checking and installing updates (as well as removing unused Flatpak packages) if **flatpak** is installed:  
See <https://www.flatpak.org/> and <https://archlinux.org/packages/extra/x86_64/flatpak/>

### Desktop notifications Support  

Arch-Update supports desktop notifications when performing the `--check` function if **libnotify (notify-send)** is installed:  
See <https://wiki.archlinux.org/title/Desktop_notifications>

### Modify the auto-check cycle

If you enabled the [systemd.timer](#the-systemd-timer), the `--check` option is automatically launched at boot and then once per hour.

If you want to change the check cycle, run `systemctl --user edit arch-update.timer` to create an override configuration for the timer and input the following in it:

```text
[Timer]
OnUnitActiveSec=10m
```

Time units are `s` for seconds, `m` for minutes, `h` for hours, `d` for days...  
See <https://www.freedesktop.org/software/systemd/man/systemd.time.html> for more details.

## Contributing

You can raise your issues, feedbacks and suggestions in the [issues tab](https://github.com/Antiz96/arch-update/issues).  
[Pull requests](https://github.com/Antiz96/arch-update/pulls) are welcomed as well!
