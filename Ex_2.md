## BERARD Lucas / DUFRENE Mélic

### Exercice 2

#### 1. /etc/default/grub.d/50-curtin-settings.cfg contient des configurations de grub. Il n'est pas présent sur mon système

#### 2. Modifiez le fichier /etc/default/grub pour que le menu de GRUB s’affiche pendant 10 secondes passé ce délai, le premier OS du menu doit être lancé automatiquement.
```
GRUB_DEFAULT=0
GRUB_TIMEOUT_STYLE=menu
GRUB_TIMEOUT=10
```

#### 3. Lancez la commande update-grub
Cette commande permet au système de reconstruire le fichier de configuration final de grub en appelant le script grub-mkconfig.

#### 4. Redémarrez votre VM pour valider que les changements ont bien été pris en compte
```
sudo reboot
```
On redémarre proprement la VM.

#### 5. On va augmenter la résolution de GRUB et de notre VM. Cherchez sur Internet le ou les paramètres à rajouter au fichier grub.
```
GRUB_GFXPAYLOAD=1024x768
```

#### 6. On va à présent ajouter un fond d’écran. Il existe un paquet en proposant quelques uns : grub2-splashimages (après installation, celles-ci sont disponibles dans /usr/share/images/grub).
```
sudo apt install grub2-splashimages
```
Puis dans le fichier /etc/default/grub on ajoute:
```
GRUB_BACKGROUND="/usr/share/images/grub/Plasma-lamp.tga"
```

#### 7. Il est également possible de configurer des thèmes. On en trouve quelques uns dans les dépôts (grub2-themes-\*). Installez-en un.
```
sudo apt install grub2-themes-*
```
Puis dans /etc/default/grub on ajoute:
```
GRUB_THEME="/boot/grub/themes/ubuntu-mate/theme.txt"
```

#### 8. 8. Ajoutez une entrée permettant d’arrêter la machine, et une autre permettant de la redémarrer.
On modifie le fichier /etc/grub.d/40_custom
```
menuentry 'Arrêt du système' {
        halt
}
menuentry 'Redémarrage du système' {
        reboot
}
```

#### 9. . Configurer GRUB pour que le clavier soit en français
D'abord, on crée le répertoire layouts puis on copie la configuration du clavier dans un format compréhensible par grub.
```
sudo mkdir /boot/grub/layouts
sudo grub-kbdcomp -o /boot/grub/layouts/fr.gkb fr
```
Dans le fichier /etc/grub.d/40_custom, on écrit:
```
insmod keylayouts
keymap fr
```

Puis dans le fichier /etc/default/grub:
```
GRUB_TERMINAL_INPUT=at_keyboard
```
