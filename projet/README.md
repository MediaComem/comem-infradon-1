# Projet SQL : Gestion d'un Hôpital 🏥

## 📌 Description

Ce projet vise à concevoir une base de données relationnelle pour un hôpital en partant d'un fichier Excel brut. Il couvre l'ensemble du cycle de développement d'une base de données :

-   **Analyse des données** et normalisation
-   **Modélisation UML** et conception relationnelle
-   **Implémentation SQL** et intégration des données
-   **Requêtage et optimisation**
-   **Présentation et documentation** des résultats

## 🎯 Objectifs

-   Construire un schéma relationnel optimisé
-   Créer et gérer une base de données SQL
-   Exécuter des requêtes SQL permettant d'extraire des informations pertinentes
-   Identifier et corriger les incohérences des données
-   Optimiser les performances des requêtes

## 📂 Données et structure des données

Vous pouvez accéder aux données dans le dossier `data/hopital_dataset.xlsx`

Les données incluent plusieurs entités principales :

-   👨‍⚕️ **Patients** : Informations personnelles des patients
-   🏥 **Médecins** : Spécialités et affiliations hospitalières
-   📅 **Rendez-vous** : Suivi des consultations entre patients et médecins
-   💊 **Médicaments** : Liste des médicaments prescrits
-   📝 **Prescriptions** : Attribution des médicaments aux patients

## 🤝 Équipe et rôles

Chaque groupe de 4/5 étudiant.e.s doit se répartir les responsabilités comme suit :

1. **Chef de projet** : Coordination, documentation & analyse des besoins clients
2. **Database architect** : Analyse et modélisation UML
3. **Développeur SQL** : Création et gestion de la base de données
4. **Data Engineer** : Rédaction et optimisation des requêtes SQL

## 📦 Livrables attendus

-   **Schéma UML** optimisé
-   **Script SQL** pour la création de la base de données et l'import des données
-   **Requêtes SQL** répondant aux besoins du client
-   **Compte rendus hebdomadaires** pour l'avancée du projet (qui vont construire le rapport final)
-   **Rapport final** détaillant l’ensemble du projet et les différentes étapes du projet (schéma UML, scripts SQL, optimisation etc).
-   **Présentation orale** du projet

## Structure rapport final et présentation

-   Soyez clairs, structurés, concis.
-   Montrez votre progression : ce rapport est aussi une trace de votre apprentissage.
-   Une bonne documentation est valorisée autant que la technique !

### Introduction

-   Présentation rapide de l'équipe (rôles attribués).

-   Objectifs du projet.

-   Présentation générale des données fournies.

### Modélisation

-   Explication de la normalisation à partir du fichier Excel.

-   Schéma UML du modèle relationnel final.

-   Justification des choix (entités, relations, cardinalités).

### Création de la base de données

-   Script SQL de création (tables intermédiaires, clés primaires/étrangères, contraintes).

-   Problèmes rencontrés lors de l’import ou de la structuration.

### Défis & Progrès

Section essentielle pour évaluer votre progression !
Pour chaque challenge rencontré :

-   ✅ Description du défis (ex : doublon patient, prescription orpheline, conflit de rendez-vous…).

-   🧠 Analyse : Comment l’avez-vous détecté ? Quels outils SQL ou techniques utilisées ?

-   🛠️ Solution mise en place (ex : nettoyage, ajout de contraintes, script correctif…).

-   📈 Impact sur la base : ce que ça a changé, ce que vous avez appris.

### Requêtes SQL

-   Pour chaque requête : but, code, résultat, et interprétation.

### Gestion des rôles et sécurité

-   Présentation des utilisateurs créés (GRANT, REVOKE).

-   Explication de la gestion des accès selon les rôles (ex : secrétaire, médecin, admin), ainsi que d'autres rôles qui pourraient être utiles

### Bilan de l'équipe

-   Répartition du travail (qui a fait quoi, retour individuel).

-   Outils utilisés pour collaborer (Git, Excel, Teams...).

-   Retours personnels (difficultés rencontrées, ce que vous avez appris).

### Annexes

-   Script(s) SQL complet

## 📊 Critères d'évaluation

-   **Modélisation UML** (30%)
-   **Qualité du code SQL** (25%)
-   **Requêtes et optimisation** (30%)
-   **Présentation et documentation** (15%)

🚀 **Bon courage à tout le monde !**
