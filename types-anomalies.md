# Types d'anomalies réseau par couche — Approches IA et Data Science

**Projet n°33 — Groupe 3**
**Membres :** Yasmina Rhari, Abdellah Lakli

---

## Couche 1 — Physique

| Anomalie | Données utilisées | Approche IA / Data Science | Algorithmes |
|---|---|---|---|
| Câble débranché / endommagé | Logs `%LINK-3-UPDOWN: Interface down` | Classification de logs (NLP) | LLM, TF-IDF |
| Perte de signal optique | Logs SFP/transceiver, métriques SNMP | Détection de seuil + séries temporelles | ARIMA, Prophet |
| Surchauffe d'équipement | Capteurs température (SNMP) | Anomaly detection | Isolation Forest |
| Panne matérielle (alimentation, ventilateur) | Logs syslog, traps SNMP | Classification supervisée | Random Forest, SVM |

---

## Couche 2 — Liaison de données

| Anomalie | Données utilisées | Approche IA / Data Science | Algorithmes |
|---|---|---|---|
| Boucle STP (Spanning Tree) | Logs STP, topology change notifications | Détection de patterns récurrents (NLP) | LLM, regex patterns |
| MAC flooding | Tables MAC, compteurs de trames | Clustering | K-Means, DBSCAN |
| Erreurs CRC / trames corrompues | Compteurs d'interface (SNMP) | Séries temporelles | LSTM, Prophet |
| Tempête de broadcast | Compteurs broadcast/multicast | Détection d'anomalies (seuil dynamique) | Z-Score, IQR |
| VLAN mal configuré | Logs de configuration, show vlan | RAG sur documentation + NLP | LLM + ChromaDB |

---

## Couche 3 — Réseau

| Anomalie | Données utilisées | Approche IA / Data Science | Algorithmes |
|---|---|---|---|
| Route manquante / boucle de routage | Logs OSPF/BGP, table de routage | Analyse de graphes + NLP sur logs | GNN, NetworkX |
| Flapping d'interface (up/down répétitif) | Logs syslog `%LINK-UPDOWN` | Détection de fréquence (séries temporelles) | FFT, LSTM |
| Conflit d'adresses IP | Logs ARP, DHCP | Règles + détection de doublons | Règles logiques |
| Attaque DDoS | NetFlow, compteurs paquets/sec | Anomaly detection | Autoencoder, Isolation Forest |
| Fragmentation excessive | Compteurs IP fragments | Détection de seuil statistique | Z-Score |

---

## Couche 4 — Transport

| Anomalie | Données utilisées | Approche IA / Data Science | Algorithmes |
|---|---|---|---|
| SYN flood | NetFlow, logs firewall | Classification supervisée | Random Forest, XGBoost |
| Connexions TCP timeout | Logs applicatifs, NetFlow | Séries temporelles | ARIMA, LSTM |
| Port scan | Logs firewall, NetFlow (dst_port) | Clustering + règles MITRE ATT&CK | DBSCAN, règles |
| Retransmissions excessives | Compteurs TCP retransmit | Détection de seuil dynamique | Moyenne mobile |

---

## Couche 7 — Application

| Anomalie | Données utilisées | Approche IA / Data Science | Algorithmes |
|---|---|---|---|
| DNS tunneling | Logs DNS, taille requêtes | Classification (longueur anormale) | Random Forest, regex |
| Erreurs HTTP 5xx en masse | Logs serveur web | Séries temporelles + alerting | Prophet, ARIMA |
| Tentatives brute-force SSH | Logs auth/syslog | NLP sur logs + comptage par IP | LLM, compteur seuil |
| Exfiltration de données | NetFlow (volume sortant) | Anomaly detection | Autoencoder, Isolation Forest |
| Configuration incorrecte | Fichiers config, logs erreurs | RAG (recherche dans la doc technique) | LLM + ChromaDB |

---

## Résumé des approches IA / Data Science

| Technique | Utilisée pour | Algorithmes | Phase projet |
|---|---|---|---|
| **NLP / Classification de logs** | Comprendre les messages d'erreur syslog | LLM (Claude), TF-IDF, BERT | MVP |
| **RAG (Retrieval-Augmented Generation)** | Trouver la solution dans la doc technique | Embeddings + ChromaDB + LLM | MVP |
| **Anomaly Detection** | Détecter les comportements anormaux (trafic, erreurs) | Isolation Forest, Autoencoder, DBSCAN | Phase 2 |
| **Séries temporelles** | Prédire les pannes, détecter les pics | LSTM, Prophet, ARIMA | Phase 2 |
| **Classification supervisée** | Catégoriser les incidents (type, sévérité) | Random Forest, XGBoost, SVM | Phase 2 |
| **Analyse de graphes** | Topologie réseau, propagation d'incidents | GNN, NetworkX | Phase 3 |

---

## Ce qui rentre dans le MVP

Le MVP se concentre sur **NLP + RAG** :

1. Le technicien soumet un **log syslog** (toutes couches)
2. Le **NLP** (LLM) parse et comprend l'erreur
3. Le **RAG** cherche la solution dans la documentation
4. Le LLM génère un **diagnostic + ticket**

Les autres techniques (anomaly detection, séries temporelles, graphes) sont planifiées pour les **versions futures**.
