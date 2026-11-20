# Carte Mentale — Projet n°33

**Système de gestion des incidents réseaux avec auto-remédiation**
**Groupe 3 :** Yasmina Rhari, Abdellah Lakli

---

```
                    ┌─────────────────────────────────────┐
                    │   GESTION DES INCIDENTS RÉSEAUX     │
                    │      AVEC AUTO-REMÉDIATION (IA)     │
                    └──────────┬──────────────────────────┘
                               │
         ┌─────────────────────┼─────────────────────┐
         │                     │                     │
         ▼                     ▼                     ▼
   ┌───────────┐      ┌──────────────┐      ┌──────────────┐
   │1. PROBLÈME│      │2. FONCTIONS  │      │3. CHOIX DE   │
   │           │      │  ATTENDUES   │      │   SOLUTION   │
   └─────┬─────┘      └──────┬───────┘      └──────┬───────┘
         │                    │                     │
         │                    │                     │
         ▼                    ▼                     ▼
                                              ┌──────────────┐
                                              │4. PREMIER    │
                                              │   LIVRABLE   │
                                              └──────────────┘
```

---

## 1. Problème

```
                         PROBLÈME
                            │
            ┌───────────────┼───────────────┐
            │               │               │
            ▼               ▼               ▼
    ┌──────────────┐ ┌────────────┐ ┌──────────────────┐
    │  Diagnostic  │ │   MTTR     │ │   Dépendance à   │
    │  lent et     │ │  élevé     │ │   l'expertise     │
    │  manuel      │ │            │ │   humaine         │
    └──────────────┘ └────────────┘ └──────────────────┘
            │               │               │
            ▼               ▼               ▼
    Les techniciens   Temps de        Peu de techniciens
    analysent les     résolution      qualifiés dispo-
    logs à la main    trop long       nibles 24/7
```

**Détail :**
- **Diagnostic lent et manuel** — Les techniciens doivent lire les logs ligne par ligne, consulter la documentation, et croiser les informations manuellement
- **MTTR élevé** — Le temps moyen de résolution (Mean Time To Repair) est trop long, impactant la disponibilité des services
- **Dépendance à l'expertise humaine** — La qualité du diagnostic dépend du technicien disponible, problématique en heures creuses

---

## 2. Fonctionnalités attendues

```
                   FONCTIONNALITÉS ATTENDUES
                            │
         ┌──────────────────┼──────────────────┐
         │                  │                  │
         ▼                  ▼                  ▼
  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
  │   Analyse    │  │  Diagnostic  │  │  Génération  │
  │  auto des    │  │  IA via RAG  │  │  de tickets  │
  │    logs      │  │              │  │              │
  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘
         │                  │                  │
         ▼                  ▼                  ▼
  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
  │  Interface   │  │  Validation  │  │ Historique   │
  │  web simple  │  │    par le    │  │    des       │
  │              │  │  technicien  │  │  incidents   │
  └──────────────┘  └──────────────┘  └──────────────┘
```

**Détail :**

| Fonctionnalité | Description |
|---|---|
| **Analyse auto des logs** | Parsing automatique des logs syslog pour extraire appareil, interface, type d'erreur |
| **Diagnostic IA via RAG** | Le LLM consulte la documentation technique via RAG pour trouver la cause et la solution |
| **Génération de tickets** | Création automatique d'un ticket de maintenance pré-rempli |
| **Interface web simple** | Formulaire pour soumettre un log et afficher le diagnostic |
| **Validation par le technicien** | Le technicien revoit et valide le ticket avant création définitive |
| **Historique des incidents** | Consultation des incidents passés avec leurs diagnostics |

---

## 3. Choix de solution

```
                      CHOIX DE SOLUTION
                            │
         ┌──────────────────┼──────────────────┐
         │                  │                  │
         ▼                  ▼                  ▼
  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
  │     LLM      │  │  RAG Pipeline│  │   Backend    │
  │  Claude API  │  │  ChromaDB +  │  │  FastAPI +   │
  │  (Anthropic) │  │  Embeddings  │  │   React      │
  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘
         │                  │                  │
         ▼                  ▼                  ▼
  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
  │  PostgreSQL  │  │    Docker    │  │   Datasets   │
  │  (base de    │  │   Compose    │  │  WitFoo +    │
  │  données)    │  │  (déploiem.) │  │   Loghub     │
  └──────────────┘  └──────────────┘  └──────────────┘
```

**Détail :**

| Composant | Choix | Justification |
|---|---|---|
| **LLM** | Claude API (Anthropic) | Contexte 200K tokens, idéal pour injecter de la doc technique |
| **RAG Pipeline** | ChromaDB + all-MiniLM-L6-v2 | Gratuit, léger, recherche sémantique efficace |
| **Backend** | FastAPI (Python) | Écosystème IA riche, async natif, Swagger auto |
| **Frontend** | React + Tailwind CSS | Composants réutilisables, design rapide |
| **Base de données** | PostgreSQL | JSONB natif, robuste, ORM SQLAlchemy |
| **Déploiement** | Docker Compose | Reproductible, 4 services orchestrés |
| **Datasets** | WitFoo Precinct6 + Loghub | 2M événements réseau + logs systèmes |

---

## 4. Premier livrable (MVP)

```
                      PREMIER LIVRABLE (MVP)
                            │
         ┌──────────────────┼──────────────────┐
         │                  │                  │
         ▼                  ▼                  ▼
  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
  │  Log syslog  │  │ Interface    │  │   Ticket     │
  │      →       │  │  web + API   │  │  pré-rempli  │
  │  Diagnostic  │  │  REST        │  │  et validé   │
  │   via RAG    │  │              │  │              │
  └──────────────┘  └──────────────┘  └──────────────┘
```

**Détail :**

| Livrable MVP | Description | Critère de réussite |
|---|---|---|
| **Pipeline log → diagnostic** | Soumettre un log syslog → parsing → RAG → diagnostic structuré | 80% de diagnostics pertinents |
| **Interface web + API REST** | Formulaire de soumission + affichage du diagnostic + endpoints API | Fonctionnel de bout en bout |
| **Ticket pré-rempli validé** | Génération auto du ticket, validation par le technicien | 70% validés sans modification majeure |

**Scénario de démonstration :**

```
1. Le technicien colle un log :
   "%LINK-3-UPDOWN: Interface GigabitEthernet0/1, changed state to down"

2. Le système parse → identifie : Routeur, Interface Gi0/1, Link Down

3. Le RAG cherche dans la doc Cisco → trouve les causes possibles

4. Le LLM génère :
   - Cause probable : Câble débranché ou défaillance du port
   - Sévérité : Haute
   - Solution : Vérifier le câblage physique, tester avec un autre port

5. Le ticket est généré → le technicien valide → ticket créé
```

---

## Résumé visuel

```
 PROBLÈME                SOLUTION               LIVRABLE
 ────────               ─────────              ─────────
 Diagnostic    ──►     IA + RAG      ──►     Log → Diagnostic
 lent/manuel           Claude API             automatique

 MTTR élevé    ──►     ChromaDB +    ──►     Interface web
                       Embeddings             simple

 Dépendance    ──►     FastAPI +     ──►     Ticket validé
 expertise             React                  par technicien
```
