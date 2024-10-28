# Projet IDS/SIEM - Suricata avec Elastic Stack

## 📋 Description du Projet
Mise en œuvre d'une solution de détection d'intrusion (IDS) Suricata couplée à un SIEM Elastic Stack pour une société industrielle utilisant des robots contrôlés à distance.

Le projet vise à :
- Détecter les tentatives d'intrusion
- Assurer une surveillance continue du réseau
- Qualifier et analyser les événements de sécurité
- Maintenir la conformité réglementaire

## 👥 Équipe
### Blue Team
- Chef de Projet : Modou Ndiar DIA
- Installation Suricata : Anthony, Leslie
- Configuration Elastic Stack : Brahim

### Red Team
- Tests de pénétration : Oussama, Amine, Mostafa

## 🏗️ Architecture

### Composants
![architecture de l'infra](architecture.jpg)
- IDS : Suricata
- SIEM : Elastic Stack (Elasticsearch, Kibana, Logstash, Beats)
- Plateforme de test : Kali Linux

### Configuration Matérielle Requise
- VirtualBox/VMware
- Minimum 16GB RAM sur l'hôte
- 50GB espace disque disponible

### Configuration des VMs
- Firewall/Router : 2 CPU, 2GB RAM
- Suricata IDS : 4 CPU, 4GB RAM
- Elastic Stack : 4 CPU, 8GB RAM
- Kali Linux : 2 CPU, 4GB RAM

## 🎯 Objectifs
1. Détection des intrusions
   - Base de signatures
   - Analyse comportementale
2. Surveillance continue
   - Acquisition automatique des flux
   - Analyse en temps réel
3. Gestion des incidents
   - Transmission des alertes vers le SIEM
   - Qualification des événements
4. Conformité réglementaire
   - Documentation des procédures
   - Traçabilité des actions

## 📁 Structure du Projet
/
├── docs/
│ ├── installation.md # Guide d'installation
│ ├── configuration.md # Guide de configuration
│ └── tests.md # Procédures de test
├── config/
│ ├── suricata/ # Configurations Suricata
│ └── elastic/ # Configurations Elastic Stack
├── scripts/
│ ├── setup/ # Scripts d'installation
│ └── tests/ # Scripts de test
└── results/
└── test-reports/ # Rapports de tests

## 🚀 Installation
1. Cloner le repository
    ```bash
    git clone https://github.com/mondiar01/IDS-SIEM-Project.git
    cd IDS-SIEM-Project

## Plan de Test

### Phase 1 : Tests Basiques
- [x] Test de connectivité réseau
  - Vérification des connexions entre les VMs
  - Test de la topologie réseau
  - Validation des règles de firewall

- [x] Capture et analyse de trafic
  - Configuration des interfaces de capture
  - Vérification des capacités de capture
  - Test de performances

- [x] Transmission des logs
  - Configuration des agents Beats
  - Validation du flux vers Elasticsearch
  - Test de la rétention des données

### Phase 2 : Tests d'Intrusion
- [ ] Scan de ports
  - Utilisation de Nmap
  - Détection des services exposés
  - Identification des vulnérabilités

- [ ] Tests de brute force
  - Attaques SSH
  - Test des mécanismes de détection
  - Validation des alertes

- [ ] Injection SQL
  - Test des applications web
  - Validation des signatures IDS
  - Analyse des faux positifs

- [ ] Tests d'exploitation
  - Utilisation de Metasploit
  - Test des CVE connues
  - Validation de la détection

### Phase 3 : Validation
- [ ] Détection des attaques
  - Vérification des règles Suricata
  - Test des signatures personnalisées
  - Optimisation des performances

- [ ] Analyse des logs
  - Validation du format des logs
  - Test des dashboards Kibana
  - Vérification des métriques

- [ ] Validation des alertes
  - Test du système de notification
  - Validation du workflow d'incidents
  - Documentation des cas d'usage

## Planning

### Semaine 1 : Setup Infrastructure
- [ ] Installation des machines virtuelles
  - Configuration VirtualBox
  - Déploiement des OS
  - Configuration réseau de base

- [ ] Configuration réseau
  - Mise en place des VLANs
  - Configuration du routage
  - Tests de connectivité

### Semaine 2 : Déploiement IDS
- [ ] Installation Suricata
  - Compilation et installation
  - Configuration initiale
  - Tests de base

- [ ] Configuration des règles
  - Import des règles ET/Pro
  - Création de règles personnalisées
  - Optimisation des performances

### Semaine 3 : SIEM
- [ ] Installation Elastic Stack
  - Déploiement Elasticsearch
  - Configuration Kibana
  - Setup Logstash

- [ ] Configuration dashboards
  - Création des visualisations
  - Configuration des alertes
  - Test des rapports

### Semaine 4 : Phase de Tests
- [ ] Tests d'intrusion
  - Exécution du plan de test
  - Documentation des résultats
  - Ajustements configuration

- [ ] Validation finale
  - Tests de charge
  - Validation des alertes
  - Documentation finale

## Livrables

### Documentation Technique
- [ ] Architecture détaillée
  - Schémas techniques
  - Documentation réseau
  - Configurations détaillées

- [ ] Procédures d'installation
  - Guide pas à pas
  - Scripts d'automatisation
  - Documentation de maintenance

### Documentation des Tests
- [ ] Plan de test complet
  - Scénarios de test
  - Critères de validation
  - Métriques de performance

- [ ] Rapports de tests
  - Résultats détaillés
  - Analyse des performances
  - Recommandations

### Présentation Finale
- [ ] Support de présentation
  - Slides techniques
  - Démonstration live
  - Scénarios d'attaque

- [ ] Documentation
  - Guide utilisateur
  - Manuel administrateur
  - Procédures d'urgence

## Sécurité et Bonnes Pratiques

### Sécurité
- Ne jamais commiter de credentials
- Utiliser des variables d'environnement
- Chiffrer les données sensibles

### Documentation
- Mettre à jour la documentation après chaque changement
- Documenter les incidents et solutions
- Maintenir un changelog

## Ressources et Documentation

### Documentation Officielle
- [Suricata Documentation](https://suricata.readthedocs.io/)
- [Elastic Stack Documentation](https://www.elastic.co/guide/index.html)
- [Kali Linux Tools](https://tools.kali.org/)

## Contact

### Support et Assistance
- Chef de Projet : modou-ndiar.dia@efrei.net
- Support Technique : support.technique@projet-ids.com

## Licence
Ce projet est sous licence MIT.

---
*Dernière mise à jour : 28 Octobre 2024*