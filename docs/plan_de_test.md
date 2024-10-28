## plan de test
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