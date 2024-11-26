


# Configuration de Suricata sur Ubuntu 20.04

## 1. Configuration du fichier principal

Édition du fichier de configuration principal :

```bash
sudo nano /etc/suricata/suricata.yaml
```

Les modifications essentielles à effectuer sont :

1. **Configuration du réseau `HOME_NET`**:  Il est crucial de définir correctement votre réseau local pour éviter de déclencher des alertes sur le trafic légitime.

```yaml
vars:
  address-groups:
    HOME_NET: "[192.168.0.0/16,10.0.0.0/8,172.16.0.0/12]"
    # Ajoutez vos plages IP supplémentaires si nécessaire
```

2. **Activation des logs EVE (Extensible Event Format)**:  Ces logs fournissent des informations détaillées sur les événements de sécurité détectés par Suricata.

```yaml
outputs:
  - eve-log:
      enabled: yes
      filetype: regular
      filename: eve.json
```

```yaml
af-packet:
  - interface: enp0s8  # Remplacez enp0s8 par votre interface de capture
    cluster-id: 99
    cluster-type: cluster_flow
    defrag: yes
```

4. **Désactivation de la validation des checksums**:  Dans certains environnements virtuels, la validation des checksums peut causer des faux positifs.  Si vous rencontrez ce problème, désactivez-la.

```yaml
stream:
  checksum-validation: no
```


## 2. Configuration du service sur Ubuntu 20.04

Sur Ubuntu 20.04, le fichier de service est déjà créé lors de l'installation.  Nous allons le modifier pour nos besoins. **Assurez-vous de remplacer `enp0s8` par votre interface réseau si différent.**

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
Environment=SURICATA_INTERFACE=enp0s8 # Remplacez enp0s8 si nécessaire
ExecStartPre=/usr/bin/suricata-update
ExecStartPre=/usr/bin/suricata -T -c /etc/suricata/suricata.yaml
ExecStart=/usr/bin/suricata $SURICATA_OPTIONS -i $SURICATA_INTERFACE
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

## 3. Application des modifications

```bash
# Rechargement du daemon systemd
sudo systemctl daemon-reload

# Redémarrage du service Suricata
sudo systemctl restart suricata

# Vérification du statut
sudo systemctl status suricata

# Vérification des logs pour s'assurer qu'il n'y a pas d'erreurs
sudo tail -f /var/log/suricata/suricata.log
sudo journalctl -u suricata --since "10 minutes ago" # Alternative pour voir les logs récents
```

## 4. Vérification de la configuration

```bash
# Test de la configuration
sudo suricata -T -c /etc/suricata/suricata.yaml

# Vérification que l'interface est bien en mode promiscuous
ip addr show enp0s8 # Remplacez enp0s8 si nécessaire
```

Si tout fonctionne correctement, vous devriez voir le service en état "active (running)" et aucune erreur dans les logs.  L'interface en mode promiscuous affichera `<PROMISC>` dans sa configuration.



# Configuration de Zeek - Système de Détection d'Intrusion (IDS)

## Introduction
Zeek (anciennement Bro) est un puissant outil d'analyse de trafic réseau qui fonctionne en complément de Suricata. Contrairement à Suricata qui se concentre sur la détection basée sur les signatures, Zeek fournit une analyse approfondie des métadonnées du trafic réseau. Il génère des logs détaillés sur tout, des enregistrements de connexion aux détails de la couche application, permettant une analyse forensique approfondie.

## 1. Prérequis

```bash
# Installation des dépendances
sudo apt install -y git cmake make gcc g++ flex bison libpcap-dev libssl-dev python-dev swig zlib1g-dev
```

## 2. Installation

```bash
# Cloner le dépôt Zeek avec ses sous-modules
sudo git clone --recursive https://github.com/zeek/zeek
cd zeek
./configure
make
sudo make install
```

## 3. Configuration du PATH

```bash
# Ajouter Zeek au PATH sécurisé
sudo visudo
# Ajouter la ligne suivante dans Secure_Path :
:/usr/local/zeek/bin
```

## 4. Configuration des fichiers principaux

### Configuration des réseaux surveillés
```bash
sudo nano /usr/local/zeek/etc/networks.cfg
# Ajouter vos plages IP si nécessaire
```

### Configuration du nœud Zeek
```bash
sudo nano /usr/local/zeek/etc/node.cfg
# Modifier la ligne interface pour correspondre à votre interface de capture :
interface=enp0s8  # Remplacer par votre interface
```

### Configuration de ZeekControl
```bash
sudo nano /usr/local/zeek/etc/zeekctl.cfg

# Modifications recommandées :
LogRotationInterval = 3600
LogExpireInterval = 48hr
StatsInterval = 24hr
LogDir = /var/log/zeek/logs
```

## 5. Configuration du format de sortie JSON

```bash
sudo nano /usr/local/zeek/share/zeek/site/local.zeek

# Ajouter les lignes suivantes :
# Output to JSON
@load policy/tuning/json-logs.zeek
```

## 6. Configuration de la rotation des logs

```bash
# Ajouter une tâche cron pour la rotation des logs
crontab -e

# Ajouter la ligne :
*/5 * * * * /usr/local/zeek/bin/zeekctl cron
```

## 7. Démarrage et gestion

```bash
# Déployer Zeek
sudo zeekctl deploy

# Vérifier le statut
sudo zeekctl status

# Arrêter Zeek
sudo zeekctl stop

# Redémarrer Zeek
sudo zeekctl restart
```

## 8. Vérification des logs

```bash
# Les logs sont stockés dans :
cd /var/log/zeek/logs/current/

# Visualiser les logs en temps réel :
tail -f /var/log/zeek/logs/current/conn.log
```

## Notes importantes

- Zeek fonctionne en mode passif, analysant le trafic réseau sans interférer
- Les logs sont au format JSON pour faciliter l'intégration avec Elastic Stack
- La rotation des logs est configurée pour préserver l'espace disque
- L'interface de capture doit être en mode promiscuous
- Les logs Zeek fournissent des informations détaillées sur le trafic réseau, complémentaires aux alertes Suricata
