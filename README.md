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
- Configuration Elastic Stack : Brahim, Amine

### Red Team
- Tests de pénétration : Oussama, Mostafa

## Architecture
![Architecture du Projet][]
### Composants

- IDS : Suricata
- SIEM : Elastic Stack (Elasticsearch, Kibana, Logstash, Beats)
- Plateforme de test : Kali Linux

### Configuration Matérielle Requise
- VirtualBox/VMware
- Minimum 16GB RAM sur l'hôte
- 140GB espace disque disponible

### Configuration des VMs
- Firewall/Router : 2 CPU, 2GB RAM
- Suricata IDS : 2 CPU, 4GB RAM
- Elastic Stack : 2 CPU, 8GB RAM
- Kali Linux : 2 CPU, 4GB RAM

## 🎯 Objectifs
1. Détection des intrusions   - Base de signatures   - Analyse comportementale
2. Surveillance continue
   - Acquisition automatique des flux
   - Analyse en temps réel
3. Gestion des incidents
   - Transmission des alertes vers le SIEM
   - Qualification des événements
4. Conformité réglementaire
   - Documentation des procédures   - Traçabilité des actions

## Structure du Projet
```
📁 Project Root
├── 📁 docs/
│ ├── 📄 installation.md
│ ├── 📄 configuration.md
│ └── 📄 tests.md
├── 📁 config/
│ ├── 📁 suricata/
│ └── 📁 elastic/
├── 📁 scripts/
│ ├── 📁 setup/
│ └── 📁 tests/
└── 📁 results/
└── 📁 test-reports/
```

## 📄 Workflow de Documentation

### Conversion Automatique des Documents
Le projet utilise GitHub Actions pour convertir automatiquement les fichiers Markdown en PDF et DOCX.

#### Fonctionnalités
- Conversion automatique lors des push
- Support des images et schémas
- Génération de table des matières
- Formatage professionnel

#### Fichiers Générés
Les documents sont générés dans deux formats :
- PDF : Pour la distribution et l'archivage
- DOCX : Pour l'édition et la collaboration

#### Structure des Documents
```
📁 Project Root
├── 📁 docs/                # Documentation source (Markdown)
│   ├── 📄 *.md            # Fichiers Markdown
│   └── 📁 images/         # Images et schémas
└── 📁 results/            # Documents générés
    ├── 📁 pdf/            # Versions PDF
    └── 📁 word/           # Versions DOCX
```

#### Utilisation
1. Les documents sources sont rédigés en Markdown dans le dossier `docs/`
2. Les images sont placées dans `docs/images/`
3. Lors d'un push, GitHub Actions :   - Convertit automatiquement les documents
   - Génère les versions PDF et DOCX   - Met à disposition les fichiers dans les artifacts

#### Workflow GitHub Actions
```yaml
name: Convert Documentation
on:
  push:
    branches:
      - main
      - develop
      - feature/*  pull_request:
    branches:
      - main
      - develop

jobs:
  convert-documents:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Install Pandoc and LaTeX dependencies
        run: |
          sudo apt-get update
          sudo apt-get install -y \
            pandoc \
            texlive-xetex \
            texlive-fonts-recommended \
            texlive-lang-french \
            texlive-latex-extra \
            lmodern

      - name: Create output directories
        run: |
          mkdir -p results/pdf
          mkdir -p results/word

      - name: Prepare files
        run: |
          mkdir -p temp
          cp docs/*.md temp/
          cp -r docs/images temp/
          for file in temp/*.md; do
            sed -i 's|/docs/images/|images/|g' "$file"
          done

      - name: Convert MD to PDF and DOCX
        working-directory: temp
        run: |
          for file in *.md; do
            if [ -f "$file" ]; then
              filename=$(basename "$file" .md)
              echo "Converting $file..."
              
              # Convert to PDF
              pandoc "$file" \
                -f markdown \
                -t pdf \
                --pdf-engine=xelatex \
                -V geometry:margin=1in \
                -V lang=fr \
                -V documentclass=article \
                -V mainfont="Latin Modern Roman" \
                --standalone \
                --toc \
                -o "../results/pdf/${filename}.pdf"
              
              # Convert to DOCX
              pandoc "$file" \
                -f markdown \
                -t docx \
                --toc \
                -o "../results/word/${filename}.docx"
            fi
          done

      - name: Upload PDF results
        uses: actions/upload-artifact@v3
        with:
          name: pdf-documents
          path: results/pdf/

      - name: Upload WORD results
        uses: actions/upload-artifact@v3
        with:
          name: word-documents
          path: results/word/
```

## 🚀 Installation
Voir [Guide d'installation](docs/installation.md)

## Configuration
Voir [Guide de configuration](docs/configuration.md)

## Tests
Voir [Guide des tests](docs/tests.md)

## Plan de Test
Voir [Plan de test](docs/plan_de_test.md)

## Planning
Voir [Planning](docs/planning.md)

## Livrables

### Documentation Technique
- [ ] Architecture détaillée  - Schémas techniques
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
  - Résultats détaillés  - Analyse des performances
  - Recommandations

### Présentation Finale
- [ ] Support de présentation
  - Slides techniques
  - Démonstration live
  - Scénarios d'attaque

- [ ] Documentation
  - Guide utilisateur
  - Manuel administrateur  - Procédures d'urgence

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
*Dernière mise à jour : 13 Novembre 2024*
