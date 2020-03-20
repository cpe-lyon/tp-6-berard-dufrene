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



### Exercice 3. Disques et partitions





