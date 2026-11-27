# Cahier des Charges

## Système de Gestion des Incidents Réseaux avec Auto-Remédiation

**Projet n°33 — Groupe 3**
**Membres :** Yasmina Rhari, Abdellah Lakli
**Date :** Novembre 2026

---

## Table des matières

1. [Contexte et présentation du projet](#1-contexte-et-présentation-du-projet)
2. [Objectifs du projet](#2-objectifs-du-projet)
3. [Périmètre du projet](#3-périmètre-du-projet)
4. [Analyse des besoins](#4-analyse-des-besoins)
5. [Spécifications fonctionnelles](#5-spécifications-fonctionnelles)
6. [Spécifications techniques](#6-spécifications-techniques)
7. [Architecture du système](#7-architecture-du-système)
8. [Types d'anomalies traitées](#8-types-danomalies-traitées)
9. [Données utilisées](#9-données-utilisées)
10. [Contraintes](#10-contraintes)
11. [Planning prévisionnel](#11-planning-prévisionnel)
12. [Livrables](#12-livrables)
13. [Critères de réussite](#13-critères-de-réussite)

---

## 1. Contexte et présentation du projet

### 1.1 Contexte

Les réseaux informatiques d'entreprise sont de plus en plus complexes et critiques. Lorsqu'un incident réseau survient (panne de routeur, défaillance d'un switch, perte de connectivité), le diagnostic repose principalement sur l'expertise humaine du technicien disponible. Ce processus est lent, sujet à l'erreur, et particulièrement problématique en dehors des heures ouvrées.

Les centres d'opérations réseau (NOC) reçoivent un volume important de logs et d'alertes, rendant le tri et l'analyse manuels inefficaces. Le temps moyen de résolution (MTTR — Mean Time To Repair) reste élevé, impactant directement la disponibilité des services.

### 1.2 Problématique

**Comment réduire le temps de diagnostic et de résolution des incidents réseaux en assistant les techniciens par une intelligence artificielle capable d'analyser les logs, de consulter la documentation technique et de proposer des solutions adaptées ?**

### 1.3 Solution proposée

Développer un système intelligent basé sur l'IA qui :
- Analyse automatiquement les logs et messages d'erreur réseau
- Utilise le RAG (Retrieval-Augmented Generation) pour consulter la documentation technique
- Génère un diagnostic structuré avec cause probable et solution
- Prépare un ticket de maintenance pré-rempli, validé par le technicien

---

## 2. Objectifs du projet

### 2.1 Objectif principal

Concevoir et développer un prototype fonctionnel d'un système de gestion des incidents réseaux assisté par IA, capable de diagnostiquer automatiquement les pannes à partir de logs syslog et de proposer des solutions basées sur la documentation technique.

### 2.2 Objectifs spécifiques

| N° | Objectif | Indicateur |
|---|---|---|
| O1 | Réduire le temps de diagnostic des incidents | MTTR réduit par rapport au diagnostic manuel |
| O2 | Proposer des diagnostics pertinents via RAG | 80% de diagnostics jugés pertinents par un technicien |
| O3 | Automatiser la création de tickets de maintenance | 70% des tickets validés sans modification majeure |
| O4 | Rendre le système accessible via une interface web | Interface fonctionnelle et intuitive |

---

## 3. Périmètre du projet

### 3.1 Fonctionnalités incluses (MVP)

| ID | Fonctionnalité | Priorité |
|---|---|---|
| F1 | Ingestion et parsing de logs réseau (format syslog/texte) | Haute |
| F2 | Pipeline RAG sur documentation technique (Cisco, RFCs) | Haute |
| F3 | Génération automatique de diagnostic (cause + solution) | Haute |
| F4 | Génération de ticket de maintenance pré-rempli | Haute |
| F5 | Interface web pour soumettre un incident et voir le diagnostic | Haute |
| F6 | Validation du ticket par le technicien avant création | Moyenne |
| F7 | Historique des incidents et diagnostics | Moyenne |

### 3.2 Fonctionnalités exclues (hors MVP)

| ID | Fonctionnalité | Raison de l'exclusion |
|---|---|---|
| H1 | Analyse d'images/photos des équipements | Pas de dataset d'images disponible |
| H2 | Visualisation de topologies réseau | Complexité trop élevée pour le MVP |
| H3 | Auto-remédiation effective (exécution de commandes) | Risque de sécurité, nécessite un environnement contrôlé |
| H4 | Intégration avec outils de monitoring (Zabbix, Nagios) | Hors périmètre temporel |
| H5 | Alertes et notifications en temps réel | Nécessite une infrastructure de streaming |
| H6 | Consultation automatique de l'historique des pannes similaires | Phase 2 |

---

## 4. Analyse des besoins

### 4.1 Utilisateurs cibles

| Utilisateur | Rôle | Besoins |
|---|---|---|
| **Technicien réseau** | Utilisateur principal | Soumettre un log, obtenir un diagnostic rapide, valider un ticket |
| **Administrateur réseau** | Superviseur | Consulter l'historique des incidents, suivre les tendances |
| **Responsable NOC** | Décideur | Tableau de bord des incidents, métriques de performance |

### 4.2 Besoins fonctionnels

**BF1 — Soumission d'incident**
- L'utilisateur peut coller un log syslog ou un message d'erreur dans un formulaire
- Le système accepte le texte brut (copier-coller depuis un terminal)
- Formats supportés : syslog RFC 5424, messages Cisco IOS, logs texte brut

**BF2 — Diagnostic automatique**
- Le système analyse le log soumis et identifie l'équipement, l'interface et le type d'erreur
- Il recherche dans la documentation technique via RAG les causes possibles
- Il génère un diagnostic structuré : cause probable, sévérité, étapes de résolution

**BF3 — Génération de ticket**
- Le système crée un ticket pré-rempli avec : titre, description, priorité, équipement, solution proposée
- Le technicien peut modifier le ticket avant validation
- Le ticket validé est enregistré en base de données

**BF4 — Consultation de l'historique**
- L'utilisateur peut consulter les incidents passés
- Chaque incident est lié à son diagnostic et son ticket

### 4.3 Besoins non fonctionnels

| ID | Besoin | Critère |
|---|---|---|
| BNF1 | **Performance** | Diagnostic généré en moins de 30 secondes |
| BNF2 | **Disponibilité** | Application accessible 24/7 (déploiement Docker) |
| BNF3 | **Ergonomie** | Interface intuitive, utilisable sans formation |
| BNF4 | **Sécurité** | Données des logs non exposées publiquement |
| BNF5 | **Maintenabilité** | Code modulaire, documentation technique à jour |
| BNF6 | **Extensibilité** | Architecture permettant l'ajout de nouveaux modules |

---

## 5. Spécifications fonctionnelles

### 5.1 Cas d'utilisation principaux

**CU1 — Soumettre un incident**
- **Acteur :** Technicien réseau
- **Précondition :** Le technicien est connecté à l'interface web
- **Scénario principal :**
  1. Le technicien colle un log dans le formulaire de soumission
  2. Le système parse le log et extrait les informations clés
  3. Le système lance le pipeline RAG
  4. Le système affiche le diagnostic (cause, sévérité, solution)
  5. Le système propose un ticket pré-rempli
- **Scénario alternatif :** Si le log est mal formaté, le système affiche un message d'erreur

**CU2 — Valider un ticket**
- **Acteur :** Technicien réseau
- **Précondition :** Un diagnostic a été généré
- **Scénario principal :**
  1. Le technicien consulte le ticket proposé
  2. Il modifie si nécessaire (titre, priorité, description)
  3. Il valide le ticket
  4. Le ticket est enregistré en base de données

**CU3 — Consulter l'historique**
- **Acteur :** Technicien / Administrateur
- **Précondition :** Des incidents existent en base
- **Scénario principal :**
  1. L'utilisateur accède à la page d'historique
  2. Il voit la liste des incidents avec date, équipement, sévérité
  3. Il peut cliquer sur un incident pour voir le détail (log, diagnostic, ticket)

### 5.2 Règles métier

| Règle | Description |
|---|---|
| RM1 | Un ticket ne peut être créé qu'après validation par un technicien |
| RM2 | Chaque incident a un seul diagnostic associé |
| RM3 | La priorité du ticket est suggérée par l'IA mais modifiable par le technicien |
| RM4 | Les logs soumis sont conservés dans leur format original |

---

## 6. Spécifications techniques

### 6.1 Stack technologique

| Composant | Technologie | Version |
|---|---|---|
| **Frontend** | React + Tailwind CSS | React 18+ |
| **Backend** | Python + FastAPI | Python 3.11+, FastAPI 0.100+ |
| **LLM** | Claude API (Anthropic) | claude-sonnet-4-20250514 |
| **Embeddings** | all-MiniLM-L6-v2 (sentence-transformers) | — |
| **Base vectorielle** | ChromaDB | 0.4+ |
| **Base de données** | PostgreSQL | 15+ |
| **ORM** | SQLAlchemy | 2.0+ |
| **Conteneurisation** | Docker + Docker Compose | — |
| **Versioning** | Git + GitHub | — |

### 6.2 APIs

| Méthode | Endpoint | Description |
|---|---|---|
| POST | `/api/incidents` | Soumettre un nouveau log/incident |
| GET | `/api/incidents` | Lister les incidents |
| GET | `/api/incidents/{id}` | Détail d'un incident avec diagnostic |
| POST | `/api/diagnosis` | Lancer un diagnostic RAG sur un log |
| POST | `/api/tickets` | Créer un ticket (après validation) |
| GET | `/api/tickets` | Lister les tickets |
| PUT | `/api/tickets/{id}` | Modifier / valider un ticket |

### 6.3 Modèle de données

**Table `incidents`**

| Champ | Type | Description |
|---|---|---|
| id | UUID | Identifiant unique |
| raw_log | TEXT | Log brut soumis par le technicien |
| device | VARCHAR | Équipement détecté |
| interface | VARCHAR | Interface concernée |
| error_type | VARCHAR | Type d'erreur identifié |
| severity | VARCHAR | Sévérité (critical, high, medium, low) |
| created_at | TIMESTAMP | Date de soumission |

**Table `diagnostics`**

| Champ | Type | Description |
|---|---|---|
| id | UUID | Identifiant unique |
| incident_id | UUID (FK) | Référence à l'incident |
| cause | TEXT | Cause probable identifiée |
| solution | TEXT | Solution proposée |
| rag_sources | JSONB | Documents utilisés par le RAG |
| confidence | FLOAT | Score de confiance |
| created_at | TIMESTAMP | Date de génération |

**Table `tickets`**

| Champ | Type | Description |
|---|---|---|
| id | UUID | Identifiant unique |
| diagnostic_id | UUID (FK) | Référence au diagnostic |
| title | VARCHAR | Titre du ticket |
| description | TEXT | Description détaillée |
| priority | VARCHAR | Priorité (critical, high, medium, low) |
| device | VARCHAR | Équipement concerné |
| proposed_solution | TEXT | Solution proposée par l'IA |
| status | VARCHAR | draft / validated / closed |
| validated_by | VARCHAR | Technicien validateur |
| validated_at | TIMESTAMP | Date de validation |
| created_at | TIMESTAMP | Date de création |

---

## 7. Architecture du système

### 7.1 Architecture globale

```
┌─────────────────────────────────────────────────────────┐
│                     FRONTEND (React)                     │
│  ┌──────────┐  ┌──────────────┐  ┌───────────────────┐  │
│  │ Dashboard │  │ Soumission   │  │ Gestion Tickets   │  │
│  │           │  │ d'incident   │  │                   │  │
│  └──────────┘  └──────────────┘  └───────────────────┘  │
└─────────────────────────┬───────────────────────────────┘
                          │ HTTP REST (JSON)
┌─────────────────────────┴───────────────────────────────┐
│                    BACKEND (FastAPI)                      │
│                                                          │
│  ┌────────────┐  ┌──────────────┐  ┌─────────────────┐  │
│  │ Log Parser │  │ RAG Pipeline │  │ Ticket Generator│  │
│  │            │  │              │  │                 │  │
│  │ - Regex    │  │ - Embedding  │  │ - Génération    │  │
│  │ - Syslog   │  │ - Recherche  │  │ - Validation    │  │
│  │ - Extract  │  │ - LLM Call   │  │ - Stockage      │  │
│  └────────────┘  └──────┬───────┘  └─────────────────┘  │
│                         │                                │
└──────┬──────────────────┼────────────────────┬───────────┘
       │                  │                    │
       ▼                  ▼                    ▼
┌──────────────┐  ┌──────────────┐    ┌──────────────┐
│  ChromaDB    │  │  Claude API  │    │ PostgreSQL   │
│  (Vecteurs)  │  │  (Anthropic) │    │ (Données)    │
│              │  │              │    │              │
│ Docs Cisco   │  │ Diagnostic   │    │ Incidents    │
│ RFCs         │  │ NLP          │    │ Diagnostics  │
│ Guides       │  │              │    │ Tickets      │
└──────────────┘  └──────────────┘    └──────────────┘
```

### 7.2 Pipeline RAG détaillé

```
                    ┌─────────────────────┐
                    │  PHASE OFFLINE      │
                    │  (Indexation)       │
                    │                     │
                    │  Docs Cisco ──┐     │
                    │  RFCs ────────┼──► Chunking ──► Embedding ──► ChromaDB │
                    │  Guides ─────┘     │
                    └─────────────────────┘

                    ┌─────────────────────┐
                    │  PHASE RUNTIME      │
                    │  (Diagnostic)      │
                    │                     │
                    │  Log soumis         │
                    │      │              │
                    │      ▼              │
                    │  Embedding          │
                    │      │              │
                    │      ▼              │
                    │  Recherche ChromaDB │
                    │  (top-K documents)  │
                    │      │              │
                    │      ▼              │
                    │  Prompt = Log       │
                    │  + Documents RAG    │
                    │  + Instructions     │
                    │      │              │
                    │      ▼              │
                    │  Claude API ──► Diagnostic │
                    └─────────────────────┘
```

---

## 8. Types d'anomalies traitées

### 8.1 Par couche OSI

| Couche | Anomalies | Approche IA |
|---|---|---|
| **Couche 1 — Physique** | Câble débranché, perte de signal, surchauffe, panne matérielle | Classification de logs (NLP) |
| **Couche 2 — Liaison** | Boucle STP, MAC flooding, erreurs CRC, tempête broadcast, VLAN mal configuré | Détection de patterns (NLP) + RAG |
| **Couche 3 — Réseau** | Route manquante, flapping d'interface, conflit IP, DDoS, boucle de routage | NLP + RAG + Anomaly detection (futur) |
| **Couche 4 — Transport** | SYN flood, timeout TCP, port scan, retransmissions | Classification + RAG |
| **Couche 7 — Application** | DNS tunneling, erreurs HTTP 5xx, brute-force SSH, exfiltration | NLP + RAG |

### 8.2 Approches IA utilisées

| Technique | Rôle dans le MVP | Phase |
|---|---|---|
| **NLP / LLM** | Comprendre les logs, générer le diagnostic | MVP |
| **RAG** | Chercher les solutions dans la documentation | MVP |
| **Anomaly Detection** | Détecter les comportements anormaux (trafic) | Phase 2 |
| **Séries temporelles** | Prédire les pannes | Phase 2 |
| **Classification supervisée** | Catégoriser automatiquement les incidents | Phase 2 |
| **Analyse de graphes** | Modéliser la topologie et la propagation | Phase 3 |

---

## 9. Données utilisées

### 9.1 Logs réseau (détection et analyse)

| Dataset | Description | Taille | Format | Licence |
|---|---|---|---|---|
| **WitFoo Precinct6** | Événements de sécurité réseau sanitisés (IP, ports, protocoles, labels) | 2M événements, 8.41 Go | Parquet, NDJSON | Apache 2.0 |
| **Loghub** | Collection de logs systèmes (Linux, OpenSSH, Apache) | 2000+ lignes/dataset | .log, .csv | Libre (recherche) |
| **Rootly AI Labs** | Logs de production réels (accès et erreurs) | 43 000+ lignes | .log, .csv | Open source |

### 9.2 Corpus documentaire (RAG)

| Ressource | Description | Format | Accès |
|---|---|---|---|
| **Documentation Cisco** | Guides de dépannage IOS, command references | HTML/PDF | cisco.com (public) |
| **RFCs** | Standards protocoles réseau (Syslog, OSPF, BGP) | Texte | ietf.org (public) |

### 9.3 Indexation

- **Découpage :** Chunking par sections logiques (respect de la structure CLI)
- **Embeddings :** all-MiniLM-L6-v2 (384 dimensions)
- **Stockage :** ChromaDB avec métadonnées (source, type de document, section)

---

## 10. Contraintes

### 10.1 Contraintes techniques

| Contrainte | Impact |
|---|---|
| L'API Claude est payante | Budget limité, optimiser le nombre d'appels |
| ChromaDB est mono-nœud | Suffisant pour le MVP, à remplacer pour la production |
| Pas de logs de routeurs/switches dans les datasets publics | Utiliser les logs systèmes + documentation Cisco |
| Pas de dataset d'images d'équipements | Analyse d'images reportée hors MVP |

### 10.2 Contraintes organisationnelles

| Contrainte | Impact |
|---|---|
| Équipe de 2 personnes | Périmètre MVP réduit |
| Délai académique | Priorisation stricte des fonctionnalités |
| Projet de fin d'année (PFA) | Livrables académiques requis |

### 10.3 Contraintes de sécurité

| Contrainte | Mesure |
|---|---|
| Les logs peuvent contenir des données sensibles (IP internes) | Sanitisation avant stockage |
| L'API LLM envoie des données au cloud | Ne pas envoyer de données de production réelles |
| Accès à l'application | Authentification requise (phase 2) |

---

## 11. Planning prévisionnel

| Phase | Tâches | Durée estimée |
|---|---|---|
| **Phase 1 — Conception** | Cahier des charges, architecture, choix technologiques | 2 semaines |
| **Phase 2 — Données** | Collecte corpus, indexation ChromaDB, pipeline RAG | 2 semaines |
| **Phase 3 — Backend** | API FastAPI, log parser, module diagnostic, ticketing | 3 semaines |
| **Phase 4 — Frontend** | Interface React (soumission, diagnostic, tickets) | 2 semaines |
| **Phase 5 — Intégration** | Tests bout en bout, Docker Compose | 1 semaine |
| **Phase 6 — Évaluation** | Tests de qualité du diagnostic, ajustements | 1 semaine |
| **Phase 7 — Rédaction** | Rapport final, présentation | 1 semaine |

---

## 12. Livrables

| N° | Livrable | Format |
|---|---|---|
| L1 | Cahier des charges (ce document) | Markdown / PDF |
| L2 | Code source du projet | GitHub (yey-opps/PFA-5) |
| L3 | Prototype fonctionnel déployable | Docker Compose |
| L4 | Documentation technique (architecture, API) | Markdown |
| L5 | Rapport de projet | PDF |
| L6 | Présentation orale | Slides |

---

## 13. Critères de réussite

| Critère | Seuil de réussite | Méthode de mesure |
|---|---|---|
| Pertinence du diagnostic | ≥ 80% jugés pertinents | Évaluation par un technicien sur 50 logs de test |
| Qualité des tickets générés | ≥ 70% validés sans modification majeure | Comparaison ticket IA vs ticket attendu |
| Temps de réponse | < 30 secondes pour un diagnostic | Mesure du temps API |
| Couverture du corpus RAG | ≥ 50 documents techniques indexés | Comptage dans ChromaDB |
| Fonctionnement bout en bout | Scénario complet exécutable | Démonstration live |
