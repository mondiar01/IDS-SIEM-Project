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
  ![Interface de gestion](/images/interface_gestion.png)
- L'interface de capture (sniffing) pour recevoir tout le trafic réseau qui est un "host-only" avec le
mode Promiscuité activé en "Allow all"
  ![Interface de capture](/images/interface_capture.png)

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


## Configuration de Suricata sur Ubuntu 20.04

### 1. Configuration du fichier principal
Édition du fichier de configuration principal :
```bash
sudo nano /etc/suricata/suricata.yaml
```

Les modifications essentielles à effectuer sont :

1. Configuration du réseau HOME_NET :
```yaml
vars:
  address-groups:
    HOME_NET: "[192.168.0.0/16,10.0.0.0/8,172.16.0.0/12]"
    # Ajoutez vos plages IP supplémentaires si nécessaire
```

2. Activation des logs EVE :
```yaml
outputs:
  - eve-log:
      enabled: yes
      filetype: regular
      filename: eve.json
```

3. Configuration de l'interface de capture :
- Utilisez Ctrl + \ pour ouvrir la recherche
- Recherchez `af-packet`:
```yaml
af-packet:
  - interface: enp0s8  # Remplacez eth0 par votre interface de capture
    cluster-id: 99
    cluster-type: cluster_flow
    defrag: yes
```

4. Désactivation de la validation des checksums :
```yaml
stream:
  checksum-validation: no
```

### 2. Configuration du service sur Ubuntu 20.04

Sur Ubuntu 20.04, le fichier de service est déjà créé lors de l'installation. Nous allons le modifier pour nos besoins :
```bash
sudo nano /lib/systemd/system/suricata.service
```

Remplacez le contenu par :
```ini
[Unit]
Description=Suricata IDS/IDP daemon
After=network.target network-online.target
Requires=network-online.target
Documentation=man:suricata(8) man:suricatasc(8)
Documentation=https://suricata-ids.org/docs/

[Service]
Type=simple
Environment=SURICATA_OPTIONS="-c /etc/suricata/suricata.yaml --af-packet"
Environment=SURICATA_INTERFACE=enp0s8
ExecStartPre=/usr/bin/suricata-update
ExecStartPre=/usr/bin/suricata -T -c /etc/suricata/suricata.yaml
ExecStart=/usr/bin/suricata $SURICATA_OPTIONS -i $SURICATA_INTERFACE
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

### 3. Application des modifications

```bash
# Rechargement du daemon systemd
sudo systemctl daemon-reload

# Redémarrage du service Suricata
sudo systemctl restart suricata

# Vérification du statut
sudo systemctl status suricata

# Vérification des logs pour s'assurer qu'il n'y a pas d'erreurs
sudo tail -f /var/log/suricata/suricata.log
```

### 4. Vérification de la configuration

```bash
# Test de la configuration
sudo suricata -T -c /etc/suricata/suricata.yaml

# Vérification que l'interface est bien en mode promiscuous
ip addr show enp0s8
```

Si tout fonctionne correctement, vous devriez voir le service en état "active (running)" et aucune erreur dans les logs.