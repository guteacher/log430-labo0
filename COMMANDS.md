# Annexe 1 : commandes `lxc` utiles

```bash
# Montrer la liste de VMs
lxc list

# Créer une nouvelle VM avec Ubuntu 22.04
lxc launch ubuntu:22.04 <nom-remote>:<nom-vm> --vm

# Arrêter une VM
lxc stop <nom-vm>

# Supprimer une VM
lxc delete <nom-vm>

# Démarrer une VM
lxc start <nom-vm>

# Voir les logs d'une VM
lxc console <nom-vm> --show-log

# Copier un fichier vers la VM
lxc file push fichier.txt <nom-vm>/root/

# Copier un fichier depuis la VM
lxc file pull <nom-vm>/root/fichier.txt ./fichier.txt
```

# Annexe 2 : autres commandes utiles
```bash
# Vérifier l'utilisation du CPU et les processus en cours
top     

# Vérifier l'utilisation de la RAM
free -h  

# Vérifier l'espace disque disponible
df -h     

# Vérifier l'IP de votre VM et les interfaces réseau disponibles
ip addr show

# Vérifier l'IP de votre VM et les interfaces réseau disponibles (alternative)
ifconfig
```
