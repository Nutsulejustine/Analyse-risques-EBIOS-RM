# Analyse de risques — Méthode EBIOS RM — NovaPay SARL

Exercice pédagogique GRC : analyse de risques selon la méthode EBIOS Risk Manager (ANSSI), alignée ISO/CEI 27005, menée en continuité de la [PSSI](../01-PSSI/README.md) de NovaPay SARL.

## Sommaire

1. [Cadrage](#1-cadrage)
2. [Atelier 1 — Socle de sécurité](#2-atelier-1--socle-de-sécurité)
3. [Atelier 2 — Sources de risque](#3-atelier-2--sources-de-risque)
4. [Atelier 3 — Scénarios stratégiques](#4-atelier-3--scénarios-stratégiques)
5. [Atelier 4 — Scénarios opérationnels](#5-atelier-4--scénarios-opérationnels)
6. [Atelier 5 — Traitement du risque](#6-atelier-5--traitement-du-risque)
7. [Cartographie des risques (avant/après traitement)](#7-cartographie-des-risques-avantaprès-traitement)

---

## 1. Cadrage

**Objectif.** Identifier, évaluer et hiérarchiser les risques numériques pesant sur NovaPay SARL afin de prioriser les mesures de sécurité.

**Méthode.** EBIOS Risk Manager (EBIOS RM), méthode de l'ANSSI alignée sur l'ISO/CEI 27005, structurée en 5 ateliers successifs.

**Périmètre retenu.** L'application mobile de paiement, le back-office web, l'API de paiement connectée aux opérateurs télécom partenaires, et les données clients associées (identité, numéro de téléphone, historique de transactions, données bancaires).

**Participants (fictifs).** RSSI, DSI, responsable produit, responsable conformité.

**Échelles utilisées.**

| Vraisemblance | Niveau | Gravité | Niveau |
| --- | --- | --- | --- |
| Minime (improbable) | 1 | Négligeable | 1 |
| Significative (possible) | 2 | Limitée | 2 |
| Forte (probable) | 3 | Importante | 3 |
| Maximale (quasi-certaine) | 4 | Critique | 4 |

Niveau de risque = Vraisemblance × Gravité, classé : **1-4 Faible · 5-9 Modéré · 10-12 Élevé · 13-16 Critique**.

## 2. Atelier 1 — Socle de sécurité

### Valeurs métier, biens supports et événements redoutés

| Valeur métier | Bien support | Événement redouté | Impact (DICP) | Gravité |
| --- | --- | --- | --- | --- |
| Service de paiement mobile | API de paiement, serveurs applicatifs | Indisponibilité prolongée du service | Disponibilité | 4 |
| Données clients | Base de données clients | Vol ou fuite de données personnelles/bancaires | Confidentialité | 4 |
| Transactions financières | Module de traitement des paiements | Altération frauduleuse des montants | Intégrité | 4 |
| Accès au back-office | Comptes administrateurs | Usurpation d'un compte à privilèges | Confidentialité / Intégrité | 3 |
| Réputation de l'entreprise | Image de marque, confiance client | Divulgation publique d'un incident de sécurité | Disponibilité / Confidentialité | 3 |
| Conformité réglementaire | Registres de traitement, journaux | Non-conformité constatée lors d'un contrôle | Preuve | 2 |

### Écart par rapport au socle de sécurité (référentiel ISO 27002)

| Mesure du socle | État constaté | Écart |
| --- | --- | --- |
| Gestion des secrets (A.8.24) | Clés d'API stockées en clair dans le code | Écart majeur |
| Séparation des environnements (A.8.31) | Cloisonnement dev/prod partiel | Écart majeur |
| Authentification multifacteur (A.8.5) | Déployée sur le back-office uniquement | Écart mineur |
| Journalisation (A.8.15) | En place mais revue non régulière | Écart mineur |

## 3. Atelier 2 — Sources de risque

| Source de risque (SR) | Objectif visé (OV) | Motivation | Ressources estimées | Priorité |
| --- | --- | --- | --- | --- |
| Cybercriminel organisé | Détourner des fonds via des transactions frauduleuses | Gain financier | Élevées (groupe structuré) | 1 |
| Prestataire tiers compromis | Servir de point d'entrée vers le SI de NovaPay | Involontaire (maillon faible) | Variables | 2 |
| Employé malveillant ou négligent | Divulguer ou altérer des données clients | Vengeance ou erreur | Faibles à moyennes | 3 |
| Concurrent indirect | Nuire à la réputation de NovaPay | Avantage concurrentiel | Moyennes | 4 |
| Script kiddie / attaquant opportuniste | Exploiter une vulnérabilité connue de l'API publique | Défi technique, notoriété | Faibles | 5 |

**Couple SR/OV retenu en priorité** : *Cybercriminel organisé → détourner des fonds via l'API de paiement*, compte tenu de la nature des services proposés par NovaPay et de l'attractivité financière directe du gain potentiel.

## 4. Atelier 3 — Scénarios stratégiques

### Scénario stratégique 1 (retenu) — Fraude sur les transactions via compromission de la chaîne d'approvisionnement

Un cybercriminel organisé cible un prestataire tiers disposant d'un accès à l'environnement de développement de NovaPay, afin d'atteindre l'API de paiement en production.

**Cartographie de l'écosystème impliqué :**

| Partie prenante | Niveau de dépendance | Niveau de confiance accordé | Exposition |
| --- | --- | --- | --- |
| Prestataire de développement externe | Fort | Moyen | Élevée |
| Hébergeur cloud | Fort | Élevé | Moyenne |
| Opérateurs télécom partenaires | Fort | Élevé | Moyenne |
| Établissement bancaire partenaire | Moyen | Élevé | Faible |

**Chaîne d'attaque envisagée :**
1. Compromission du compte d'un développeur du prestataire externe (hameçonnage ciblé).
2. Mouvement latéral vers l'environnement de développement de NovaPay.
3. Récupération de clés d'accès à l'API de production mal cloisonnées.
4. Injection de transactions frauduleuses ou exfiltration de données clients.

### Scénario stratégique 2 — Fraude interne par abus de privilèges

Un collaborateur disposant d'un accès administrateur au back-office détourne des fonds ou exfiltre des données clients en abusant de ses droits, en l'absence de séparation des tâches ou de double contrôle sur les opérations sensibles.

### Scénario stratégique 3 — Indisponibilité provoquée par un prestataire critique

Une attaque par déni de service distribué (DDoS) ou une panne prolongée chez l'hébergeur cloud ou un opérateur télécom partenaire rend le service de paiement indisponible pendant une durée dépassant le seuil de tolérance métier.

## 5. Atelier 4 — Scénarios opérationnels

### Scénario opérationnel détaillé (déroulé du scénario stratégique 1 retenu)

| Étape technique | Vulnérabilité exploitée | Vraisemblance | Gravité | Niveau de risque |
| --- | --- | --- | --- | --- |
| Hameçonnage ciblé d'un employé du prestataire | Absence de MFA chez le prestataire | 3 | 2 | 6 — Modéré |
| Accès à l'environnement de développement | Comptes partagés, cloisonnement insuffisant | 3 | 3 | 9 — Modéré |
| Récupération de clés d'API de production | Secrets stockés en clair dans le code | 2 | 4 | 8 — Modéré |
| Injection de transactions frauduleuses | Absence de contrôle de cohérence côté serveur | 2 | 4 | 8 — Modéré |
| **Scénario global (chaîne complète aboutie)** | — | **2** | **4** | **8 — Modéré*** |

*\* Avant traitement ; voir § 7 pour la réévaluation après mise en œuvre des mesures de l'atelier 5.*

### Autres scénarios opérationnels évalués

| Scénario | Vraisemblance | Gravité | Niveau de risque |
| --- | --- | --- | --- |
| Fraude interne par abus de privilèges (scénario stratégique 2) | 2 | 3 | 6 — Modéré |
| DDoS sur l'API de paiement (scénario stratégique 3) | 3 | 3 | 9 — Modéré |
| Panne prolongée de l'hébergeur cloud (scénario stratégique 3) | 2 | 4 | 8 — Modéré |

## 6. Atelier 5 — Traitement du risque

| Risque | Traitement | Mesure proposée | Responsable | Échéance | Gravité après traitement |
| --- | --- | --- | --- | --- | --- |
| Compromission d'un prestataire | Réduire | Exiger le MFA et un audit de sécurité pour tout prestataire à accès sensible | RSSI | T+1 mois | 1 |
| Cloisonnement dev/production insuffisant | Réduire | Séparer strictement les environnements et les identifiants d'accès | DSI | T+2 mois | 2 |
| Secrets en clair dans le code | Réduire | Déployer un coffre-fort de secrets (vault) et interdire les clés en dur | DSI | T+3 mois | 1 |
| Absence de contrôle côté serveur sur les transactions | Réduire | Ajouter des contrôles de cohérence et des seuils d'alerte anti-fraude | Équipe produit | T+3 mois | 2 |
| Fraude interne par abus de privilèges | Réduire | Séparation des tâches et double validation sur les opérations sensibles | RSSI | T+2 mois | 2 |
| DDoS / panne prestataire critique | Réduire + Transférer | Protection anti-DDoS, clause de continuité de service dans les contrats, bascule multi-zone | DSI | T+3 mois | 2 |
| Atteinte à la réputation en cas d'incident | Accepter (risque résiduel) | Préparer un plan de communication de crise | Direction générale | T+1 mois | 2 |

## 7. Cartographie des risques (avant/après traitement)

| Risque | Niveau avant traitement | Niveau après traitement |
| --- | --- | --- |
| Fraude via compromission de la chaîne d'approvisionnement | 8 — Modéré | 2 — Faible |
| Fraude interne par abus de privilèges | 6 — Modéré | 2 — Faible |
| DDoS sur l'API de paiement | 9 — Modéré | 4 — Faible |
| Panne prolongée de l'hébergeur cloud | 8 — Modéré | 4 — Faible |

**Conclusion.** La mise en œuvre du plan de traitement ramène l'ensemble des risques majeurs identifiés dans la zone Faible à Modérée. Un suivi trimestriel par le comité sécurité permet de vérifier l'application effective du plan et de réévaluer les risques à chaque changement majeur du système d'information, conformément à la [PSSI](../01-PSSI/README.md).

---
*Document rédigé dans un cadre pédagogique (exercice GRC). NovaPay SARL est une entreprise fictive ; aucune donnée réelle n'est concernée.*
