# Guide d'Installation
1. Cloner le repository
    ```bash
    git clone https://github.com/mondiar01/IDS-SIEM-Project.git
    cd IDS-SIEM-Project
## Prérequis de l'hote
- VirtualBox 6.1+
- 16GB RAM minimum
- 200GB espace disque
- Connexion Internet stable


## Installation de Suricata sur Ubuntu Server 20.04 (Focal)

### 1. Configuration de l'interface de capture

Un IDS nécessite au moins 2 interfaces réseau :
- L'interface de gestion pour l'administration qui est un "bridge"
  ![Interface de gestion](/docs/images/interface_gestion.png)
- L'interface de capture (sniffing) pour recevoir tout le trafic réseau qui est un "host-only" avec le
mode Promiscuité activé en "Allow all"
  ![Interface de capture](/docs/images/interface_capture.png)

L'interface de capture doit être configurée en mode promiscuous pour recevoir tous les paquets du réseau, pas seulement ceux qui lui sont adressés.

#### 1.1. Remplacement de Netplan

Ubuntu utilise par défaut Netplan qui ne supporte pas le mode promiscuous. Nous allons donc installer les anciens outils réseau.

Notez le nom de vos interfaces :
```bash
ip addr # Notez les noms des interfaces (ex: enp0s3 et enp0s8)
```

Installation des outils nécessaires :
```bash
sudo apt install -y ifupdown
```

#### 1.2. Configuration des interfaces

Créez le fichier de configuration :
```bash
sudo nano /etc/network/interfaces
```

Ajoutez le contenu suivant (remplacez les noms d'interfaces si nécessaire) :
```
#loopback
auto lo
iface lo inet loopback

#interface de gestion
allow-hotplug enp0s3
iface enp0s3 inet dhcp

#interface de capture
allow-hotplug enp0s8
iface enp0s8 inet manual
up ifconfig enp0s8 promisc up
down ifconfig enp0s8 promisc down
```

#### 1.3. Application des changements

Désactivez Netplan et redémarrez :
```bash
sudo service systemd-networkd stop
sudo apt remove -y netplan
sudo apt install net-tools -y
sudo reboot now
```

Après le redémarrage, vérifiez la configuration :
```bash
ip addr
```

### 2. Prérequis

Assurez-vous que votre système est à jour :
```bash
sudo apt update && sudo apt upgrade -y
```

### 3. Ajout du dépôt PPA Suricata

#### 3.1. Ajout du PPA
```bash
sudo add-apt-repository ppa:oisf/suricata-stable
```

#### 3.2. Correction des erreurs de clés GPG
Si vous rencontrez des erreurs de clés GPG comme suit :
```
NO_PUBKEY AC10378CF205C960
NO_PUBKEY D7F87B2966EB736F
```

Exécutez la commande suivante :
```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys AC10378CF205C960 D7F87B2966EB736F
```

#### 3.3. Mise à jour des dépôts
```bash
sudo apt update
```

### 4. Installation de Suricata

```bash
sudo apt install suricata
```

### 5. Configuration post-installation

#### 5.1. Vérification de l'installation
```bash
suricata --version
```

#### 5.2. Configuration du service
```bash
# Activation du démarrage automatique
sudo systemctl enable suricata

# Démarrage du service
sudo systemctl start suricata

# Vérification du statut
sudo systemctl status suricata
```

#### 5.3. Mise à jour des règles
```bash
sudo suricata-update
```


