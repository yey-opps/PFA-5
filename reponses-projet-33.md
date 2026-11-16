# Projet n°33 — Système de gestion des incidents réseaux avec auto-remédiation

**Groupe :** 3  
**Membres :** Yasmina Rhari, Abdellah Lakli

---

## 1. Quel problème métier traitez-vous, pour quel utilisateur et avec quel résultat attendu ?

**Problème métier :** Lorsqu'un incident réseau survient (panne de routeur, switch, perte de connectivité), le diagnostic est lent et dépend de l'expertise du technicien disponible. Les temps de résolution (MTTR) sont élevés, surtout en dehors des heures ouvrées.

**Utilisateur cible :** Les techniciens et administrateurs réseau dans un NOC (Network Operations Center) ou un service informatique d'entreprise.

**Résultat attendu :** Réduire le temps moyen de diagnostic d'un incident réseau grâce à une assistance IA qui analyse automatiquement les logs/messages d'erreur, propose un diagnostic et prépare un ticket de maintenance pré-rempli.

**Critère de réussite mesurable :** L'IA propose un diagnostic pertinent (cause + solution) dans 80% des cas testés, et le ticket généré est validé par le technicien sans modification majeure dans 70% des cas.

---

## 2. Quel périmètre MVP avez-vous retenu et quelles fonctions avez-vous reportées ?

### Fonctions incluses dans le MVP :
- Ingestion et analyse de logs/messages d'erreur réseau (format syslog/texte)
- Pipeline RAG sur documentation technique (manuels Cisco, guides de dépannage)
- Génération automatique d'un diagnostic (cause probable + solution proposée)
- Génération d'un ticket de maintenance pré-rempli
- Interface web simple pour soumettre un incident et voir le diagnostic
- Validation du ticket par le technicien avant création définitive

### Fonctions reportées (hors MVP) :
- Analyse d'images/photos des équipements (vision par IA)
- Visualisation et analyse de schémas/topologies réseau
- Consultation automatique de l'historique des pannes similaires
- Auto-remédiation effective (exécution automatique de commandes correctives)
- Intégration avec des outils de monitoring (Zabbix, Nagios, PRTG)
- Système de notifications/alertes en temps réel

---

## 3. Qu'aviez-vous prévu depuis le dernier point et qu'avez-vous réellement terminé ?

| Tâche | Statut | Preuve |
|---|---|---|
| Analyse des besoins et étude de l'existant | ✅ Terminé | Document d'analyse des besoins rédigé |
| Définition du périmètre MVP | ✅ Terminé | Liste des fonctions validée par le groupe |
| Choix de l'architecture technique | 🔄 En cours | Diagramme d'architecture en cours de finalisation |
| Choix des technologies (LLM, base vectorielle, framework) | 🔄 En cours | Comparatif des solutions en cours |
| Collecte et préparation du corpus documentaire | 🔄 En cours | Identification des sources, collecte non terminée |
| Développement du prototype | ⏳ Non commencé | En attente de la finalisation de l'architecture |

**Résumé :** Le projet est en phase de conception. L'analyse des besoins et la définition du MVP sont terminées. L'architecture technique et le choix des outils sont en cours de finalisation.

---

## 4. Que peut-on exécuter aujourd'hui sans intervention manuelle cachée ?

Aucune démonstration exécutable à ce stade. Le projet est en phase de conception. Le premier prototype fonctionnel (soumission d'un log → diagnostic via RAG) est prévu pour [indiquez votre prochaine échéance].

---

**État général : — la conception avance, le développement n'a pas encore démarré.

---

## 5. Quel dataset ou corpus utilisez-vous exactement et comment y accède-t-on ?

### A. Données de logs réseau (pour la détection et l'analyse d'incidents)

| Dataset | Description | Accès | Licence |
|---|---|---|---|
| **Loghub** | Large collection de logs systèmes incluant des logs de routeurs, switches et équipements réseau (format syslog). Référence dans la recherche en log analytics. | GitHub : https://github.com/logpai/loghub | Libre (recherche) |
| **WitFoo Precinct6** | ~1,96 million d'événements de sécurité réseau sanitisés + 10 442 graphes d'incidents. Formats : syslog brut, XML, JSON, CSV. Issu de monitoring réseau entreprise. | Hugging Face : https://huggingface.co/datasets/witfoo/precinct6-cybersecurity | Apache 2.0 |
| **Rootly AI Labs logs-dataset** | Logs de production réels (accès et erreurs) pour l'entraînement d'IA de gestion d'incidents et SRE. Pas de données synthétiques. | GitHub : https://github.com/rootly-ai-labs/logs-dataset | Open source |

### B. Corpus documentaire technique (pour le pipeline RAG)

| Ressource | Description | Accès | Licence |
|---|---|---|---|
| **Documentation Cisco (publique)** | Guides de dépannage Cisco IOS, command references, configuration guides. Source officielle et complète pour le diagnostic réseau. | https://www.cisco.com/c/en/us/support/index.html | Usage public (vérifier les conditions de redistribution) |
| **RFC réseau** | Documents standards des protocoles réseau. Ex : RFC 5424 (Syslog Protocol), RFC 791 (IP), RFC 2328 (OSPF). | https://datatracker.ietf.org/ | Domaine public |
| **Network Engineering Training Data** | Guide pour assembler des données d'entraînement réseau pour LLM, incluant des sessions de troubleshooting. | https://www.jaredwatkins.com/research/local-llm-finetuning/training-data/network-engineering-data/ | Référence méthodologique |

### C. Format et indexation

- **Format d'ingestion :** Documents PDF et texte, logs au format syslog/texte brut
- **Base vectorielle :** ChromaDB ou FAISS pour l'indexation et la recherche sémantique
- **Chunking :** Découpage par sections logiques (respect de la structure des documents CLI et guides de dépannage)
