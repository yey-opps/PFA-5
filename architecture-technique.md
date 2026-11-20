# Architecture Technique — Système de gestion des incidents réseaux avec auto-remédiation

**Projet n°33 — Groupe 3**
**Membres :** Yasmina Rhari, Abdellah Lakli

---

## 1. Vue d'ensemble

```
[Utilisateur / Technicien]
        │
        ▼
[Interface Web (React)]
        │
        ▼
[Backend API (FastAPI / Python)]
        │
        ├──► [Module d'ingestion des logs]
        │         │
        │         ▼
        │    [Parsing & Normalisation Syslog]
        │
        ├──► [Pipeline RAG]
        │         │
        │         ├── Embedding (sentence-transformers)
        │         ├── Base vectorielle (ChromaDB)
        │         └── LLM (Claude API / OpenAI API)
        │
        ├──► [Module Diagnostic]
        │         │
        │         └── Prompt + contexte RAG → Diagnostic + Solution
        │
        └──► [Module Ticketing]
                  │
                  └── Génération ticket → Validation technicien → Création
```

---

## 2. Choix technologiques

| Composant | Technologie | Justification |
|---|---|---|
| **Frontend** | React + Tailwind CSS | Framework populaire, bonne documentation, composants réutilisables |
| **Backend / API** | Python + FastAPI | Léger, performant, écosystème IA/ML riche en Python |
| **LLM** | Claude API (Anthropic) ou OpenAI GPT-4 | Puissant pour le diagnostic en langage naturel |
| **Embeddings** | `all-MiniLM-L6-v2` (sentence-transformers) | Gratuit, léger, performant pour la recherche sémantique |
| **Base vectorielle** | ChromaDB | Open source, simple à installer, suffisant pour le MVP |
| **Base de données** | PostgreSQL | Robuste, stockage des incidents, tickets et historique |
| **Parsing logs** | Python (regex + `syslog-rfc5424-parser`) | Parsing natif des formats syslog standard |
| **Conteneurisation** | Docker + Docker Compose | Déploiement reproductible et isolé |

---

## 3. Description des modules

### 3.1 Module d'ingestion des logs

**Rôle :** Recevoir et parser les logs/messages d'erreur réseau soumis par le technicien.

**Fonctionnement :**
- Le technicien colle un log syslog ou un message d'erreur dans l'interface web
- Le parser extrait les informations clés : appareil source, interface concernée, type d'erreur, sévérité, timestamp
- Les données normalisées sont transmises au pipeline RAG

**Formats supportés :** Syslog (RFC 5424), messages d'erreur Cisco IOS, logs texte brut

### 3.2 Pipeline RAG (Retrieval-Augmented Generation)

**Rôle :** Retrouver la documentation technique pertinente pour enrichir le diagnostic du LLM.

**Fonctionnement :**
1. **Indexation (offline)** : Les documents techniques (guides Cisco, RFCs) sont découpés en chunks, transformés en embeddings via `sentence-transformers`, et stockés dans ChromaDB
2. **Recherche (runtime)** : Le log parsé est transformé en embedding, et les K chunks les plus similaires sont récupérés
3. **Augmentation** : Les chunks récupérés sont injectés dans le prompt envoyé au LLM comme contexte

**Corpus indexé :**
- Documentation Cisco IOS (guides de dépannage)
- RFC réseau (Syslog, OSPF, BGP, etc.)
- Logs d'exemple annotés (Loghub)

### 3.3 Module Diagnostic

**Rôle :** Générer un diagnostic structuré à partir du log et du contexte RAG.

**Fonctionnement :**
- Le LLM reçoit un prompt structuré contenant :
  - Le log parsé (appareil, erreur, contexte)
  - Les documents techniques retrouvés par le RAG
  - Les instructions de format de sortie
- Il génère :
  - **Cause probable** du problème
  - **Niveau de sévérité** estimé
  - **Étapes de résolution** recommandées

### 3.4 Module Ticketing

**Rôle :** Générer automatiquement un ticket de maintenance pré-rempli.

**Fonctionnement :**
- À partir du diagnostic, le système génère un ticket contenant :
  - Titre du problème
  - Description détaillée
  - Priorité (critique / haute / moyenne / basse)
  - Équipement concerné
  - Solution proposée
- Le technicien **revoit et valide** le ticket avant sa création définitive
- Le ticket validé est enregistré dans la base PostgreSQL

---

## 4. Flux de fonctionnement

```
1. SOUMISSION     Technicien colle un log dans l'interface web
                           │
2. PARSING        Backend extrait : appareil, interface, erreur, sévérité
                           │
3. RECHERCHE RAG  Embedding du log → recherche dans ChromaDB → top-K documents
                           │
4. DIAGNOSTIC     LLM reçoit (log parsé + docs RAG + prompt) → génère diagnostic
                           │
5. TICKET         Système génère ticket pré-rempli → technicien valide → création
```

---

## 5. Structure du projet

```
PFA-5/
├── frontend/                   # Application React
│   ├── src/
│   │   ├── components/         # Composants UI
│   │   ├── pages/              # Pages (Dashboard, Soumission, Tickets)
│   │   └── services/           # Appels API
│   └── package.json
│
├── backend/                    # API FastAPI
│   ├── app/
│   │   ├── main.py             # Point d'entrée de l'API
│   │   ├── routers/            # Routes API (incidents, tickets, diagnostic)
│   │   ├── services/           # Logique métier
│   │   │   ├── log_parser.py       # Parsing des logs syslog
│   │   │   ├── rag_pipeline.py     # Pipeline RAG (embedding + recherche)
│   │   │   ├── diagnosis.py        # Génération du diagnostic via LLM
│   │   │   └── ticket_generator.py # Génération des tickets
│   │   └── models/             # Modèles de données (SQLAlchemy)
│   └── requirements.txt
│
├── data/
│   ├── corpus/                 # Documents techniques pour le RAG
│   └── logs_samples/           # Exemples de logs réseau pour les tests
│
├── docker-compose.yml          # Orchestration des services
├── architecture-technique.md   # Ce document
├── reponses-projet-33.md       # Réponses aux questions du jalon
└── README.md
```

---

## 6. APIs exposées

| Méthode | Endpoint | Description |
|---|---|---|
| POST | `/api/incidents` | Soumettre un nouveau log/incident |
| GET | `/api/incidents` | Lister les incidents |
| GET | `/api/incidents/{id}` | Détail d'un incident avec diagnostic |
| POST | `/api/diagnosis` | Lancer un diagnostic RAG sur un log |
| POST | `/api/tickets` | Créer un ticket (après validation technicien) |
| GET | `/api/tickets` | Lister les tickets |
| PUT | `/api/tickets/{id}` | Modifier / valider un ticket |

---

## 7. Diagramme de déploiement (Docker Compose)

```
┌─────────────────────────────────────────────┐
│              Docker Compose                  │
│                                              │
│  ┌──────────┐  ┌──────────┐  ┌───────────┐ │
│  │ Frontend │  │ Backend  │  │ PostgreSQL│ │
│  │ (React)  │  │ (FastAPI)│  │   (DB)    │ │
│  │ :3000    │  │ :8000    │  │ :5432     │ │
│  └────┬─────┘  └────┬─────┘  └─────┬─────┘ │
│       │              │              │        │
│       └──────► API ◄─┘              │        │
│                  │                   │        │
│                  └───────────────────┘        │
│                                              │
│              ┌───────────┐                   │
│              │ ChromaDB  │                   │
│              │ (Vecteurs)│                   │
│              │ :8001     │                   │
│              └───────────┘                   │
└─────────────────────────────────────────────┘
```
