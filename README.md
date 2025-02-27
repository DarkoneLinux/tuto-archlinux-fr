# 🐧 ARCHITECT TUTO 📜


## Table des Matières
1. [Tutoriel](#installation)
2. [Jeux](#gaming)
3. [Optimisation](#optimization)
4. [Dépannage](#troubleshooting)
5. [Communauté](#community)

#### Installation <a name="installation"/>

> [!IMPORTANT]
> Suivez les étapes avec minutie

<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [ Tutoriel Arch Linux Partie 1 : Archinstall ](https://www.youtube.com/watch?v=JE6VwNHLcyk)

#### 1. Configurer le clavier en français

```sh
loadkeys fr
```

#### 2. Configurer votre Wi-Fi

```sh
iwctl
```
    
Puis (remplacez VOTRE-NOM-WIFI par le nom de votre wifi)

```sh
station wlan0 connect VOTRE-NOM-WIFI (SSID)
```

Entrez votre mot de passe wifi puis tapez `quit` pour quitter iwctl.

#### 3. Archinstall

```sh
archinstall                 # pour lancer le script d'aide à l'installation pour arch linux
```

**/!\ Le menu archinstall est sujet à changement avec les mises à jour du script /!\\**
    
### Post-installation

<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [ Tutoriel Arch Linux Partie 2 : Post Installation ](https://youtu.be/FEFhC46BkXo?si=Gi-6BOhqENLoh5Ak)

#### 1. Optimiser pacman

Cette [modification](https://wiki.archlinux.org/title/Pacman#Enabling_parallel_downloads) permet la parallélisation des téléchargements de paquets.

```sh
sudo nano /etc/pacman.conf
```

Décommentez (retirez le **#** des lignes suivantes) :
   
```
#Options diverses
#UseSyslog
Color <-
#NoProgressBar
#CheckSpace
VerbosePkgLists <- 
ParallelDownloads = 5 <-
```

#### 2. **Installation d'un AUR helper**

Les AUR helpers sont des outils pratiques pour gérer l'installation et la mise à jour des logiciels sur les systèmes basés sur Arch Linux.
Yay et paru facilitent particulièrement l'utilisation du dépôt AUR, un dépôt géré par la communauté qui étend considérablement la bibliothèque de logiciels disponible. Cela inclut la compilation de ces programmes à partir de leur source, à moins que "-bin" ne soit spécifié à la fin de leur nom.
**/!\ Soyez prudent /!\ Comme les paquets dans l'AUR sont fournis par la communauté, n'installez pas tout et n'importe quoi !**

Vous pouvez choisir entre **YAY** ou **Paru**

[Yay](https://github.com/Jguer/yay)
   
```sh
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin
makepkg -si
```

Ajout du support pour les mises à jour des paquets git. (Normalement, cela ne doit être fait qu'une seule fois)

```sh
yay -Y --gendb
yay -Y --devel --save
```

[Paru](https://github.com/Morganamilo/paru)

```sh
sudo pacman -S --needed base-devel
git clone https://aur.archlinux.org/paru-bin.git
cd paru-bin
makepkg -si
```

Ajout du support pour les mises à jour des paquets git. (Normalement, cela ne doit être fait qu'une seule fois)

```sh
paru --gendb
```

#### 3. Alias de maintenance :

<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [ Tutoriel Arch Linux Partie 4 : Maintenance ](https://www.youtube.com/watch?v=Z7POSK2jnII)

Cette modification vous permet de simplement taper « update-arch » dans un terminal pour mettre à jour le système, « clean-arch » pour le nettoyer, ou « fix-key » en cas d'erreur avec les clés gpg.

```sh
nano ~/.bashrc
```
  
Ajoutez chacune de ces lignes à la fin du fichier :

pour yay :

```sh
alias update-arch='yay && flatpak update'
```

```sh
alias clean-arch='yay -Sc && yay -Yc && flatpak remove --unused'
```

pour Paru :

```sh
alias update-arch='paru && flatpak update'
```

```sh
alias clean-arch='paru -Sc && paru -c && flatpak remove --unused'
```

Pour tous : 

```sh
alias update-mirrors='sudo reflector --verbose --score 100 --latest 20 --fastest 5 --sort rate --save /etc/pacman.d/mirrorlist '
```

```sh
alias fix-key='sudo rm /var/lib/pacman/sync/* && sudo rm -rf /etc/pacman.d/gnupg/* && sudo pacman-key --init && sudo pacman-key --populate && sudo pacman -Sy --noconfirm archlinux-keyring && sudo pacman --noconfirm -Su'
```
   
Redémarrez le terminal.

#### 4. Compilation multithread des paquets AUR :

```sh
nano /etc/makepkg.conf
```

Pour utiliser tous les threads, ajoutez :

```sh
MAKEFLAGS="-j$(nproc)"
```

Ou si, par exemple, vous souhaitez utiliser 6 threads :

```sh
MAKEFLAGS="-j6"
```

Remplacez le 6 par le nombre de threads que vous souhaitez utiliser. Il est conseillé d'avoir 2 Go de RAM par cœur utilisé.

### SUPPORT MATÉRIEL

#### NVIDIA

> [!IMPORTANT]
> Rester sur X11 au moins jusqu'à la sortie de KDE 6

#### 1. Installer les composants de base :

```sh
sudo pacman -S --needed nvidia-dkms nvidia-utils lib32-nvidia-utils nvidia-settings vulkan-icd-loader lib32-vulkan-icd-loader
```

Si vous avez un  <img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [PC portable Intel / Nvidia](https://youtu.be/GhsP6btpiiw?si=ibWw_dQdty8_Q0jm) :

```sh
sudo pacman -S --needed intel-media-driver intel-gmmlib onevpl-intel-gpu nvidia-prime
```

#### 2. Activer nvidia-drm modeset=1 :

Ce paramètre permet de lancer le module Nvidia au démarrage.

```sh
sudo nano /etc/modprobe.d/nvidia.conf
```

Ajouter:

`options nvidia-drm modeset=1 fbdev=1`

Sauvegarder.
   
#### 3. Charger les modules Nvidia en priorité au lancement d'Arch :

> [!WARNING]
> Cette étape est destinée aux utilisateurs avancés :star: :
> **Optionnel**, à ne faire que **si on remarque des problèmes au boot.**
> **Augmente drastiquement la taille de l'initramfs empéchant d'installer plus de 1 kernel si on a laissé les options par défaut de archinstall !**

   
```sh
sudo nano /etc/mkinitcpio.conf
```

Modifiez la ligne MODULES=() pour :

```sh
MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)
```

Si utilisation de btrfs :

```sh
MODULES=(btrfs nvidia nvidia_modeset nvidia_uvm nvidia_drm)
```

#### 4. Débloquer Wayland Si vous etes sur Gnome:

```sh
sudo ln -s /dev/null /etc/udev/rules.d/61-gdm.rules
```

#### 5. Reconstruction de l'initramfs :

Comme nous avons déjà installé les pilotes à l'étape 1, donc avant de configurer le hook, nous devons déclencher manuellement la reconstruction de l'initramfs :

```sh
sudo mkinitcpio -P
```

#### AMD (ne pas faire si Nvidia)

Installer les composants de base :

```sh
sudo pacman -S --needed mesa lib32-mesa vulkan-radeon llimineib32-vulkan-radeon vulkan-icd-loader lib32-vulkan-icd-loader
```

#### INTEL (ne pas faire si Nvidia)

Installer les composants de base :

```sh
sudo pacman -S --needed mesa lib32-mesa vulkan-intel lib32-vulkan-intel vulkan-icd-loader lib32-vulkan-icd-loader intel-media-driver
```

#### Imprimantes
- Essentiels

```sh
sudo pacman -S --needed ghostscript gsfonts cups cups-filters cups-pdf system-config-printer avahi
sudo systemctl enable --now avahi-daemon cups
```

```sh
sudo systemctl disable systemd-resolved
```

- Pilotes

```sh
sudo pacman -S --needed foomatic-db-engine foomatic-db foomatic-db-ppds foomatic-db-nonfree foomatic-db-nonfree-ppds gutenprint foomatic-db-gutenprint-ppds 
```

- Imprimantes HP

```sh
yay -S --needed python-pyqt5 hplip
```

- Imprimantes Epson

```sh
yay -S --needed epson-inkjet-printer-escpr epson-inkjet-printer-escpr2 epson-inkjet-printer-201601w epson-inkjet-printer-n10-nx127
```

#### Bluetooth

La seconde commande ci-dessous demande à systemd de démarrer immédiatement le service bluetooth, et aussi de l'activer à chaque démarrage.

```sh
yay -S --needed bluez bluez-utils bluez-plugins
sudo systemctl enable --now  bluetooth.service
```

#### [PipeWire](https://pipewire.org/) (son)

**/!\ Dites oui à tout pour écraser tout avec les nouveaux paquets. /!\**

```sh
sudo pacman -S --needed pipewire lib32-pipewire pipewire-pulse pipewire-alsa pipewire-jack wireplumber alsa-utils alsa-firmware alsa-tools sof-firmware
```

### LOGICIEL DE BASE

#### Composants de base

Ici, vous trouverez des codecs, utilitaires, polices, pilotes :

```sh
yay -S --needed gstreamer-vaapi gst-plugins-bad gst-plugins-base gst-plugins-ugly gst-plugin-pipewire gstreamer-vaapi gst-plugins-good gst-libav gstreamer downgrade  libva-mesa-driver lib32-libva-mesa-driver mesa-vdpau lib32-mesa-vdpau rebuild-detector xdg-desktop-portal-gtk xdg-desktop-portal neofetch power-profiles-daemon lib32-pipewire hunspell hunspell-fr p7zip unrar ttf-liberation noto-fonts noto-fonts-emoji adobe-source-code-pro-fonts otf-font-awesome ttf-meslo-nerd ttf-droid duf btop  ntfs-3g fuse2fs exfatprogs fuse2 fuse3 bash-completion man-db man-pages
```

#### Logiciels divers

```sh
yay -S libreoffice-fresh libreoffice-fresh-fr vlc discord gimp obs-studio gnome-disk-utility visual-studio-code-bin openrgb-bin spotify
```

#### Logiciels KDE

Voici divers logiciels pour graphisme, vidéo (édition, support de codec), utilitaires d'interface graphique, etc.

```sh
sudo pacman -S --needed xdg-desktop-portal-kde okular print-manager kdenlive gwenview spectacle partitionmanager ffmpegthumbs qt6-wayland kdeplasma-addons powerdevil kcalc plasma-systemmonitor qt6-multimedia qt6-multimedia-gstreamer qt6-multimedia-ffmpeg kwalletmanager
```

#### Pare-feu
La configuration par défaut peut bloquer l'accès aux imprimantes et autres appareils sur votre réseau local.
Voici un petit lien pour vous aider : https://www.dsfc.net/infra/securite/configurer-firewalld/

```sh
sudo pacman -S --needed --noconfirm firewalld python-pyqt5 python-capng
sudo systemctl enable --now firewalld.service
firewall-applet &
```

#### Reflector pour la mise à jour automatique des miroirs

```sh
yay -S reflector-simple
```

Une commande pour générer une liste de miroirs, à faire une fois après la première installation et à répéter si vous voyagez, ou changez de pays, ou si vous trouvez le téléchargement des paquets trop lent, ou si vous rencontrez une erreur vous indiquant qu'un miroir est hors service :

```sh
sudo reflector --verbose --score 100 --latest 20 --fastest 5 --sort rate --save /etc/pacman.d/mirrorlist
```

#### Arch Update

<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [Arch-Update  : Notifie les updates de Arch et aide aux tâches importantes avant et après l'update.](https://youtu.be/QkOkX70SEmo?si=EwB-rSTV5dMNbv5D)

- [arch-update](https://github.com/Antiz96/arch-update)

Arch Update est un notificateur/aplicateur de mise à jour pour Arch Linux qui vous assiste dans les tâches importantes avant et après la mise à jour et qui inclut une icône cliquable (.desktop) pouvant être facilement intégrée à n'importe quel environnement de bureau/gestionnaire de fenêtres, dock, barre de statut/lançage ou menu d'application.
Support optionnel pour les mises à jour des paquets AUR/Flatpak et les notifications de bureau.

```sh
yay -S arch-update
systemctl --user enable --now arch-update.timer
```

#### Timeshift

- [Timeshift](https://github.com/linuxmint/timeshift) est un utilitaire Linux open source pour créer des sauvegardes de tout votre système.

**/!\ ATTENTION : par défaut, c'est uniquement le système qui est sauvegardé, pas votre dossier utilisateur (le /home/) ! /!\\**

```sh
sudo pacman -S timeshift
```

- Évitez timeshift et btrfs sur Arch, J’ai déjà eu de la [casse](https://github.com/linuxmint/timeshift).

*“BTRFS snapshots are supported only on BTRFS systems having an Ubuntu-type subvolume layout ”*

- Pour bénéficier des sauvegardes automatiques, vous aurez besoin de démarrer cronie. (facultatif) 

```sh
sudo systemctl enable --now cronie
```

### Grub BTRFS

<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [Setup Grub BTRFS sur Arch Linux.](https://youtu.be/EyhSUBwjPUY?si=cQ0TuGI76_M1TzTp)

Permet de prendre des snapshots à chaque update et de booter dessus à partir de grub.

Déjà comme son nom l'indique il faut avoir choisi grub comme bootloader et btrfs en file system.

On installe Timeshift comme vu à l'étape précedante. puis,

```sh
yay -S timeshift-autosnap grub-btrfs
```

On active le service brtfsd

```sh
sudo systemctl enable --now grub-btrfsd
```

On édite le service : 

```sh
sudo systemctl edit --full grub-btrfsd
```

On remplace `ExecStart=/usr/bin/grub-btrfsd --syslog /.snapshots` par `ExecStart=/usr/bin/grub-btrfsd --syslog --timeshift-auto`

crtl + x pour sauvegarder.

Enfin on lance une fois Timeshift, je conseille de laisser tout par défaut.

#### Fish

[Fish](https://fishshell.com/) est un shell en ligne de commande conçu pour être interactif et convivial. Voir aussi [ArchWiki](https://wiki.archlinux.org/title/fish) sur le sujet. Il remplace le shell par défaut, bash.

- Installez fish.
    
```sh
sudo pacman -S fish                             # 1. installez fish
chsh -s /usr/bin/fish                           # 2. Définissez-le par défaut.
fish                                            # 3. Lancez fish ou redémarrez et il sera par défaut.
fish_update_completions                         # 4. Mettez à jour les complétions.
set -U fish_greeting                            # 5. Supprimez le message de bienvenue.
sudo nano ~/.config/fish/config.fish            # 6. Créez un alias comme pour bash au début de ce tutoriel.
```
- Ajoutez ensuite les alias suivants entre if et end :

pour yay :

```sh
alias update-arch='yay && flatpak update'
```

```sh
alias clean-arch='yay -Sc && yay -Yc && flatpak remove --unused'
```

pour Paru :

```sh
alias update-arch='paru && flatpak update'
```

```sh
alias clean-arch='paru -Sc && paru -c && flatpak remove --unused'
```

pour tous : 

```sh
alias update-mirrors='sudo reflector --verbose --score 100 --latest 20 --fastest 5 --sort rate --save /etc/pacman.d/mirrorlist'
```

```sh
alias fix-key='sudo rm /var/lib/pacman/sync/* && sudo rm -rf /etc/pacman.d/gnupg/* && sudo pacman-key --init && sudo pacman-key --populate && sudo pacman -Sy --noconfirm archlinux-keyring && sudo pacman --noconfirm -Su'
```

- ***Redémarrez sauf si fait à l'étape 3***, les alias de tout type ne fonctionnent qu'après le redémarrage du terminal.

## <img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/game-console.png" width="30" height="30"> **Améliorez votre Expérience de Jeu** <a name="gaming"/>

### Steam
Notez que les pilotes AMD ou Nvidia doivent être installés au préalable comme mentionné dans la section [SUPPORT MATÉRIEL](#HARDWARE-SUPPORT).

```sh
sudo pacman -S steam
```

### Lutris

Lutris est un gestionnaire de jeux FOSS (Free, Open Source) pour les systèmes d'exploitation basés sur Linux.
Lutris permet de rechercher un jeu ou une plateforme (Ubisoft Connect, EA Store, GOG, Battlenet, etc.) et propose un script d'installation qui configurera ce qui est nécessaire pour que votre choix fonctionne avec Wine ou Proton.

```sh
sudo pacman -S lutris wine-staging
```

Vidéo supplémentaire :

<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [Configuration de Lutris pour ordinateur portable Intel/Nvidia](https://www.youtube.com/watch?v=Am3pgTXiUAA)

### Support avancé de manettes

Pilote Linux avancé pour manettes sans fil Xbox 360|One|S|X ([xpadneo](https://github.com/atar-axis/xpadneo)) ([xone](https://github.com/medusalix/xone))

```sh
yay -S xpadneo-dkms-git xone-dkms-git
```

Pilote Linux avancé pour manettes PS5

```sh
yay -S dualsensectl-git
```

### Affichage des performances en jeu

[MangoHud](https://wiki.archlinux.org/title/MangoHud) est une superposition Vulkan et OpenGL qui permet de surveiller les performances du système dans les applications et d'enregistrer des métriques pour le benchmarking.
C'est l'outil dont vous avez besoin si vous voulez voir vos FPS en jeu, votre charge CPU ou GPU, etc. Ou même enregistrer ces résultats dans un fichier.
Ici, nous installons GOverlay qui est une interface graphique pour configurer MangoHud.

```sh
sudo pacman -S goverlay
```

### Amélioration de la compatibilité des jeux Windows

Nous augmentons la valeur par défaut de cette variable, permettant le stockage de plus de "zones de mappage mémoire". La valeur par défaut est très basse. L'objectif est d'améliorer la compatibilité avec les jeux Windows via Wine ou Steam (Voir [ProtonDB](https://www.protondb.com/)), sachant que certains jeux mal optimisés ont tendance à atteindre rapidement cette limite, ce qui peut entraîner un crash.

<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [Gaming LINUX supprimer les crashs / augmenter la compatibilité](https://youtu.be/sr4RgshrUYY)

- Ajouter dans :

```sh
sudo nano /etc/sysctl.d/99-sysctl.conf
``` 

la ligne suivante:

`
vm.max_map_count=2147483642
`

## <img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/speed.png" width="30" height="30"> **BONUS** : <a name="optimization"/>

### [Kernel TKG](https://github.com/Frogging-Family/linux-tkg)

> [!WARNING]
> Cette étape est destinée aux utilisateurs avancés :star:

[KERNEL TKG](https://github.com/Frogging-Family) est un noyau hautement personnalisable qui fournit une sélection de correctifs et d'ajustements pour améliorer les performances de bureau et de jeu.

Vidéo associée :
<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [Kernel TKG sur Arch + Améliorer ses perfs](https://youtu.be/43yYIWMnDJA)

```sh
git clone https://github.com/Frogging-Family/linux-tkg.git
cd linux-tkg
makepkg -si
```

### [MESA-TKG](https://github.com/Frogging-Family/mesa-git)

> [!WARNING]
> Cette étape est destinée aux utilisateurs avancés :star:

Comme le noyau TKG, mais pour Mesa, une version patchée pour ajouter quelques correctifs et optimisations.
Très utile pour les joueurs AMD, sans intérêt pour les joueurs Nvidia.

```sh
git clone https://github.com/Frogging-Family/mesa-git.git
cd mesa-git
makepkg -si
```

Dites oui à tout pour tout écraser avec les nouveaux paquets.

### [NVIDIA-ALL](https://github.com/Frogging-Family/nvidia-all)

> [!WARNING]
> Cette étape est destinée aux utilisateurs avancés :star:

Nvidia-all est une intégration du pilote nvidia par TkG. Il comprend des patchs de support pour les nouveaux noyaux. Il vous permet de sélectionner la version du pilote que vous souhaitez installer, qu'il s'agisse de la dernière version officielle, d'une version bêta, de la version Vulkan, etc.

<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [Vous utilisez Arch et Nvidia, regardez ça !](https://youtu.be/T0laE8gPtfY)

```sh
git clone https://github.com/Frogging-Family/nvidia-all.git
cd nvidia-all
makepkg -si
```

Dites oui à tout pour tout écraser avec les nouveaux paquets.

### Installation [Flatpak](https://wiki.archlinux.org/title/Flatpak)

Anciennement connu sous le nom de xdg-app, il s'agit d'un utilitaire de déploiement de logiciels et de gestion de paquets pour Linux. Il est promu comme offrant un environnement "sandbox" dans lequel les utilisateurs peuvent exécuter des logiciels isolément du reste du système.

<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [MangoHUD, Goverlay, Steam, Lutris FLATPAK!](https://www.youtube.com/watch?v=1dha2UDSF4M)

```sh
sudo pacman -S flatpak flatpak-kcm
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

## Dépannage <a name="troubleshooting"/>
<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [Arch Linux Partie 3 les problèmes les plus communs.](https://youtu.be/vbOOQsYyPfc?si=wA2W8bOG1gtpfmnZ)

<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [Arch Linux Partie 4 Maintenance / mise à jour](https://youtu.be/Z7POSK2jnII?si=SNwagGGJXRVkYPdc)
 
<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [Arch Linux Partie 5 Arch-Chroot](https://youtu.be/iandJSjePiA?si=7uI8JZ-VxAVOsPTh)

- Pour de l'aide, visitez le Discord GLF (fr/en) : [Discord GLF](http://discord.gg/EP3Jm8YMvj)

## Sources <a name="community"/>

Sources et liens utiles :
- [ArchWiki](https://wiki.archlinux.org/)
<img src="https://github.com/Cardiacman13/tuto-archlinux-fr/blob/main/assets/images/Cardiac-icon.png" width="30" height="30"> [Fonctionnement du WIKI d'Arch.](https://www.youtube.com/watch?v=TQ3A9l2N5lI)
- [Site GLF](https://www.gaminglinux.fr/)
- [Discord GLF](http://discord.gg/EP3Jm8YMvj)
- [Ma chaîne Youtube](https://www.youtube.com/@Cardiacman)

## 🙏 Remerciements

- L'équipe d'[Arch Linux](https://archlinux.org/) pour leur travail remarquable.
- La communauté Arch Linux pour leur documentation exceptionnelle.
- Les mainteneurs de l'AUR pour leur travail acharné.
- Les développeurs des paquets utilisés dans ce projet. Mention spéciale à :
- [Frogging Family](https://github.com/Frogging-Family)
- [OpenRGB](https://github.com/CalcProgrammer1/OpenRGB)
- Merci au [Discord GLF](https://discord.gg/6t4REDETJd) pour les nombreux tests et retours.
