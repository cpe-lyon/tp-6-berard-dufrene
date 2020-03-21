# Compte rendu TP6
## Administration systèmes
BERARD Lucas

DUFRENE Mélic

### Exercice 1. Disques et partitions

#### 2. Vérifiez que ce nouveau disque dur est bien détecté par le système

```
lsblk
```
*On voit bien notre disk de 5 Go*

#### 3. Partitionnez ce disque en utilisant fdisk : créez une première partition de 2 Go de type Linux (n°83), et une seconde partition de 3 Go en NTFS (n°7)

```
sudo fdisk /dev/sdb
-> n
-> t
```
*effectuer les bonnes config puis faire le "t" avec un hexcode de 7 pour mettre en NTFS la deuxieme partition.*

#### 4. A ce stade, les partitions ont été créées, mais elles n’ont pas été formatées avec leur système de fichiers. A l’aide de la commande mkfs, formatez vos deux partitions.

```
sudo mkfs -t ext4 /dev/sdb1
sudo mkfs -t ntfs /dev/sdb2
```

#### 5. Pourquoi la commande df -T, qui affiche le type de système de fichier des partitions, ne fonctionne-telle pas sur notre disque ?

```
df -T
```
*pas de disque 2 car pas monté au démarrage*

#### 6. Faites en sorte que les deux partitions créées soient montées automatiquement au démarrage de la machine, respectivement dans les points de montage /data et /win

```
mkdir /data
mkdir /winlucas
df -T
```
*Le disque est à présent affiché.*

#### 7. Utilisez la commande mount puis redémarrez votre VM pour valider la configuration

```
sudo mount --target /data /dev/sdb1
sudo mount --target /win /dev/sdb2
shutdown now
```

### Exercice 2. Disques et partitions

#### 1. Commencez par changer l’extension du fichier /etc/default/grub.d/50-curtin-settings.cfg s’il est présent dans votre environnement

*/etc/default/grub.d/50-curtin-settings.cfg contient des configurations de grub. 
Il n'est pas présent sur mon système*

#### 2. Modifiez le fichier /etc/default/grub pour que le menu de GRUB s’affiche pendant 10 secondes passé ce délai, le premier OS du menu doit être lancé automatiquement.

```
GRUB_DEFAULT=0
GRUB_TIMEOUT_STYLE=menu
GRUB_TIMEOUT=10
```

#### 3. Lancez la commande update-grub

*Cette commande permet au système de reconstruire le fichier de configuration final de grub en appelant le script grub-mkconfig.*

#### 4. Redémarrez votre VM pour valider que les changements ont bien été pris en compte

```
sudo reboot
```
On redémarre proprement la VM.

#### 5. On va augmenter la résolution de GRUB et de notre VM. Cherchez sur Internet le ou les paramètres à rajouter au fichier grub.

```
GRUB_GFXPAYLOAD=1024x768
```

#### 6. On va à présent ajouter un fond d’écran. Il existe un paquet en proposant quelques uns : grub2-splashimages.

```
sudo apt install grub2-splashimages
```
*Puis dans le fichier /etc/default/grub on ajoute:*
```
GRUB_BACKGROUND="/usr/share/images/grub/Plasma-lamp.tga"
```

#### 7. Il est également possible de configurer des thèmes. Installez-en un.

```
sudo apt install grub2-themes-*
```
*Puis dans /etc/default/grub on ajoute:*
```
GRUB_THEME="/boot/grub/themes/ubuntu-mate/theme.txt"
```

#### 8. Ajoutez une entrée permettant d’arrêter la machine, et une autre permettant de la redémarrer.

*On modifie le fichier /etc/grub.d/40_custom*
```
menuentry 'Arrêt du système' {
        halt
}
menuentry 'Redémarrage du système' {
        reboot
}
```

#### 9. Configurer GRUB pour que le clavier soit en français

*D'abord, on crée le répertoire layouts puis on copie la configuration du clavier dans un format compréhensible par grub.*
```
sudo mkdir /boot/grub/layouts
sudo grub-kbdcomp -o /boot/grub/layouts/fr.gkb fr
```
*Dans le fichier /etc/grub.d/40_custom, on écrit:*
```
insmod keylayouts
keymap fr
```

*Puis dans le fichier /etc/default/grub:*
```
GRUB_TERMINAL_INPUT=at_keyboard
```

### Exercice 3. Disques et partitions

#### 1. Commencez par installer le paquet build-essential, qui contient tous les outils nécessaires (compilateurs, bibliothèques) à la compilation de programmes en C (entre autres).

```
sudo apt install build-essential
```

#### 2. Créez un fichier hello.c contenant le code suivant :

```
mkdir codeC
vim codeC/hello.c
```


#### 3. Créez également un fichier Makefile :

```
vim codeC/Makefile
```


#### 4. Compilez le module à l’aide de la commande make, puis installez-le à l’aide de la commande make install.

```
make
sudo make install
```


#### 5. Chargez le module ; vérifiez dans le journal du noyau que le message ”La fonction init_module() est appelée” a bien été inscrit, synonyme que le module a été chargé ; confirmez avec la commande lsmod.

```
sudo depmod -a
sudo modprobe hello
journalctl  | tail -5
```
*le message est bien présent.*
```
lsmod | grep hello
```
*Il est present dans la liste.*

#### 6. Utilisez la commande modinfo pour obtenir des informations sur le module hello.ko ; vous devriez notamment voir les informations figurant dans le fichier C.

```
cd /lib/modules/5.3.0-42-generic/kernel/drivers/misc
modinfo hello.ko
->
filename:       /lib/modules/5.3.0-42-generic/kernel/drivers/misc/hello.ko
version:        Version 1.00
description:    Module hello world
author:         John Doe
license:        GPL
srcversion:     4398A2271F215E3A6F58078
depends:
retpoline:      Y
name:           hello
vermagic:       5.3.0-42-generic SMP mod_unload
```


#### 7. Déchargez le module ; vérifiez dans le journal du noyau que le message ”La fonction cleanup_module() est appelée” a bien été inscrit, synonyme que le module a été déchargé ; confirmez avec la commande lsmod.

```
sudo rmmod hello
journalctl | grep cleanup_module
-> mars 21 14:19:15 serveur kernel: [Hello world] - La fonction cleanup_module() est appelée.
```
*le message est bien présent.*
```
lsmod | grep hello
```
*Aucun résultat.*


#### 8. Pour que le module soit chargé automatiquement au démarrage du système, il faut l’inscrire dans le fichier /etc/modules. Essayez, et vérifiez avec la commande lsmod après redémarrage de la machine.

```
sudo vim /etc/modules 
```
*On ecrit uniquement hello à la fin du fichier.*
```
sudo reboot
lsmod | grep hello
-> hello                  16384  0
```
*Il est présent !"





