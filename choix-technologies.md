# Choix des technologies — Projet n°33

**Système de gestion des incidents réseaux avec auto-remédiation**
**Groupe 3 :** Yasmina Rhari, Abdellah Lakli

---

## 1. LLM (Large Language Model)

Le LLM est le moteur central du système : il analyse les logs, génère le diagnostic et propose des solutions.

### Comparatif

| Critère | Claude API (Anthropic) | OpenAI GPT-4 | LLaMA 3 (Meta, local) | Mistral (local) |
|---|---|---|---|---|
| **Performance** | Excellente | Excellente | Bonne | Bonne |
| **Coût** | Payant (API) | Payant (API) | Gratuit | Gratuit |
| **Facilité d'intégration** | Simple (API REST) | Simple (API REST) | Complexe (GPU requis) | Complexe (GPU requis) |
| **Confidentialité** | Données envoyées au cloud | Données envoyées au cloud | Données locales | Données locales |
| **Taille contexte** | 200K tokens | 128K tokens | 8K-128K tokens | 32K-128K tokens |
| **Qualité diagnostic technique** | Très bonne | Très bonne | Correcte | Correcte |

### Choix retenu : **Claude API (Anthropic)**

**Justification :**
- Fenêtre de contexte très large (200K tokens), idéale pour injecter beaucoup de documentation technique via le RAG
- Excellente compréhension du langage technique réseau
- Intégration simple via API REST en Python
- Alternative envisagée : OpenAI GPT-4 (en cas de besoin de fallback)

---

## 2. Modèle d'embeddings

Le modèle d'embeddings transforme les textes (logs et documents) en vecteurs numériques pour la recherche sémantique dans le pipeline RAG.

### Comparatif

| Critère | all-MiniLM-L6-v2 | text-embedding-3-small (OpenAI) | BGE-large (BAAI) | CamemBERT |
|---|---|---|---|---|
| **Coût** | Gratuit | Payant (API) | Gratuit | Gratuit |
| **Taille vecteur** | 384 dimensions | 1536 dimensions | 1024 dimensions | 768 dimensions |
| **Vitesse** | Très rapide | Rapide | Lent | Moyen |
| **Qualité** | Bonne | Très bonne | Très bonne | Bonne (français) |
| **Exécution locale** | Oui | Non (API) | Oui | Oui |
| **Langue** | Multilingue | Multilingue | Multilingue | Français |

### Choix retenu : **all-MiniLM-L6-v2** (sentence-transformers)

**Justification :**
- Gratuit et exécutable localement (pas de dépendance API)
- Léger et rapide, suffisant pour le volume de notre corpus
- Bonne qualité de recherche sémantique pour des documents techniques
- Largement utilisé et documenté dans la communauté RAG
- Bibliothèque Python `sentence-transformers` facile à intégrer

---

## 3. Base de données vectorielle

La base vectorielle stocke les embeddings des documents techniques et permet la recherche par similarité.

### Comparatif

| Critère | ChromaDB | FAISS (Meta) | Pinecone | Weaviate |
|---|---|---|---|---|
| **Coût** | Gratuit | Gratuit | Payant (cloud) | Gratuit (self-hosted) |
| **Installation** | Très simple (`pip install`) | Simple | Cloud managé | Complexe (Docker) |
| **Persistance** | Oui | Manuelle | Oui | Oui |
| **Scalabilité** | Petite/moyenne échelle | Grande échelle | Grande échelle | Grande échelle |
| **API Python** | Native | Native | SDK | SDK |
| **Filtrage métadonnées** | Oui | Non | Oui | Oui |

### Choix retenu : **ChromaDB**

**Justification :**
- Installation en une commande (`pip install chromadb`)
- API Python native, intégration directe avec `sentence-transformers`
- Persistance intégrée (les données survivent au redémarrage)
- Filtrage par métadonnées (filtrer par type de document, source, etc.)
- Parfaitement adapté à la taille de notre corpus (quelques milliers de documents)
- Pas besoin de la scalabilité de FAISS ou Pinecone pour un MVP

---

## 4. Framework Backend

Le backend expose l'API REST et orchestre les modules (parsing, RAG, diagnostic, ticketing).

### Comparatif

| Critère | FastAPI | Django REST | Flask | Express.js (Node) |
|---|---|---|---|---|
| **Langage** | Python | Python | Python | JavaScript |
| **Performance** | Très rapide (async) | Moyenne | Moyenne | Rapide |
| **Documentation auto** | Oui (Swagger intégré) | Plugin | Plugin | Non |
| **Écosystème IA/ML** | Natif Python | Natif Python | Natif Python | Limité |
| **Courbe d'apprentissage** | Facile | Moyenne | Très facile | Facile |
| **Validation données** | Pydantic (intégré) | Serializers | Manuelle | Manuelle |

### Choix retenu : **FastAPI**

**Justification :**
- Python natif → accès direct à toutes les bibliothèques IA/ML (sentence-transformers, chromadb, anthropic SDK)
- Support async natif, idéal pour les appels API au LLM
- Documentation Swagger générée automatiquement (utile pour le développement et la présentation)
- Validation des données avec Pydantic intégrée
- Plus moderne et performant que Flask, moins lourd que Django

---

## 5. Framework Frontend

L'interface web permet au technicien de soumettre des logs et de consulter les diagnostics.

### Comparatif

| Critère | React | Vue.js | Streamlit | HTML/CSS simple |
|---|---|---|---|---|
| **Interactivité** | Très bonne | Très bonne | Basique | Limitée |
| **Écosystème** | Très large | Large | Limité (data science) | Aucun |
| **Courbe d'apprentissage** | Moyenne | Facile | Très facile | Très facile |
| **Adapté production** | Oui | Oui | Non | Non |
| **Composants UI** | Nombreux | Nombreux | Pré-faits | Aucun |

### Choix retenu : **React + Tailwind CSS**

**Justification :**
- Framework le plus utilisé, grande communauté et documentation
- Composants réutilisables pour le dashboard, les formulaires et les tickets
- Tailwind CSS pour un design rapide et cohérent
- Adapté pour une application en production
- Alternative pour le MVP : Streamlit (si le temps manque pour développer le frontend React)

---

## 6. Base de données relationnelle

Stockage des incidents, tickets, utilisateurs et historique.

### Comparatif

| Critère | PostgreSQL | MySQL | SQLite | MongoDB |
|---|---|---|---|---|
| **Type** | Relationnel | Relationnel | Relationnel (fichier) | Document (NoSQL) |
| **Performance** | Excellente | Bonne | Limitée | Bonne |
| **JSON natif** | Oui (JSONB) | Limité | Non | Natif |
| **Scalabilité** | Très bonne | Bonne | Mono-fichier | Très bonne |
| **Docker** | Simple | Simple | Pas nécessaire | Simple |

### Choix retenu : **PostgreSQL**

**Justification :**
- Support JSONB natif, utile pour stocker les diagnostics structurés et les logs parsés
- Robuste et fiable en production
- Fonctionne facilement dans Docker Compose
- ORM Python mature (SQLAlchemy)

---

## 7. Conteneurisation et déploiement

| Outil | Rôle |
|---|---|
| **Docker** | Conteneurisation de chaque service |
| **Docker Compose** | Orchestration des 4 services (frontend, backend, PostgreSQL, ChromaDB) |
| **Git + GitHub** | Gestion de version et collaboration |

---

## 8. Résumé des choix

| Composant | Technologie retenue |
|---|---|
| LLM | Claude API (Anthropic) |
| Embeddings | all-MiniLM-L6-v2 (sentence-transformers) |
| Base vectorielle | ChromaDB |
| Backend | Python + FastAPI |
| Frontend | React + Tailwind CSS |
| Base de données | PostgreSQL |
| ORM | SQLAlchemy |
| Conteneurisation | Docker + Docker Compose |
| Versioning | Git + GitHub |

---

## 9. Schéma des dépendances

```
Frontend (React)
    │
    ▼ HTTP REST
Backend (FastAPI)
    │
    ├──► ChromaDB (recherche vectorielle)
    │       │
    │       └── sentence-transformers (embeddings)
    │
    ├──► Claude API (génération diagnostic)
    │
    └──► PostgreSQL (incidents, tickets, historique)
```
