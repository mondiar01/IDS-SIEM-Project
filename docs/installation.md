# Guide d'Installation

## Prérequis
- VirtualBox 6.1+
- 16GB RAM minimum
- 50GB espace disque
- Connexion Internet stable

## Installation de l'environnement
1. Installation de VirtualBox
2. Configuration du réseau
3. Création des VMs

## Installation de Suricata

### Introduction

Suricata est un outil open source de détection de menaces réseau. Il utilise des règles et des signatures pour détecter les menaces dans le trafic réseau. Ce guide explique comment installer et configurer Suricata sur Ubuntu 18.04.

### Installation

Il existe deux méthodes pour installer Suricata sur Ubuntu 18.04 :

1. Installation à partir des sources
2. Installation à partir du dépôt PPA

#### 1. Installation à partir des sources

Cette méthode permet d'obtenir la version la plus récente et stable de Suricata.

##### Étape 1 : Installer les dépendances
```bash
sudo apt -y install libpcre3 libpcre3-dbg libpcre3-dev build-essential autoconf automake libtool libpcap-dev libnet1-dev libyaml-0-2 libyaml-dev zlib1g zlib1g-dev libcap-ng-dev libcap-ng0 make libmagic-dev libjansson-dev libjansson4 pkg-config libnspr4-dev libnss3-dev liblz4-dev rustc cargo python-pip python3-distutils
```

Pour la fonctionnalité IPS :
```bash
sudo apt -y install libnetfilter-queue-dev libnetfilter-queue1 libnfnetlink-dev libnfnetlink0
```

##### Étape 2 : Télécharger et extraire Suricata
```bash
wget https://www.openinfosecfoundation.org/download/suricata-6.0.5.tar.gz
tar xzf suricata-6.0.5.tar.gz
cd suricata-6.0.5
```

##### Étape 3 : Configurer, compiler et installer
```bash
./configure --enable-nfqueue --prefix=/usr --sysconfdir=/etc --localstatedir=/var
make
make install-full
```

#### 2. Installation à partir du dépôt PPA

Cette méthode est plus simple mais peut ne pas fournir la toute dernière version.

```bash
sudo add-apt-repository ppa:oisf/suricata-stable
sudo apt update
sudo apt install suricata jq
```

### Configuration

1. Ouvrez le fichier de configuration :
```bash
sudo vim /etc/suricata/suricata.yaml
```

2. Configurez votre réseau interne et externe :
```yaml
HOME_NET: "[10.0.2.0/24]"
EXTERNAL_NET: "!$HOME_NET"
```

3. Définissez l'interface réseau à surveiller :
```yaml
af-packet:
  - interface: enp0s3
```

4. Sauvegardez et fermez le fichier.

### Installation des règles

Les règles Suricata sont généralement placées dans le répertoire `/etc/suricata/rules/`.

Pour lister les règles par défaut :
```bash
ls -1 /etc/suricata/rules/
```

### Vérification de l'installation

Pour vérifier la version et les options de compilation de Suricata :
```bash
sudo suricata --build-info
```

### Conclusion

Suricata est maintenant installé et configuré sur votre système Ubuntu 18.04. N'oubliez pas de mettre à jour régulièrement les règles et de surveiller les logs pour une protection efficace de votre réseau.

## Installation d'Elastic Stack
[Instructions à venir]