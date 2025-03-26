# 📘 Révisions – Infrastructure des données

---

✍️ L'examen se déroulera sur papier : aucune exécution de requête SQL ne sera possible. Il s'agit d'évaluer votre compréhension théorique et votre capacité à analyser des situations.

### 📝 Exercice 1 – Client / Commande

Dessine un diagramme UML pour représenter une entreprise où :

-   Un client peut passer plusieurs commandes
-   Une commande contient plusieurs produits
-   Chaque produit a un nom, un prix
-   Chaque commande a une date

**👉 Bonus** : identifie les cardinalités, les types PostgreSQL et les attributs clés.

### 📝 Exercice 2 – Requêtes simples

À partir de la table `employes(id, nom, poste, salaire, date_embauche)` :

1. Sélectionner tous les employés.
2. Lister les employés embauchés après 2022.
3. Afficher le salaire moyen des développeurs.
4. Trier les employés par salaire décroissant.
5. Supprimer tous les stagiaires.

### 📝 Exercice 3 – Détection d’anomalies

Table de départ :

| client_nom | client_ville | produit | quantite | prix |
| ---------- | ------------ | ------- | -------- | ---- |

-   Quelles sont les **anomalies** potentielles (insertion, mise à jour, suppression) ?
-   Propose une **décomposition en 3NF**.

---

### 📝 Exercice 4 – Jointures & Groupes

Tables :

-   `clients(id, nom)`
-   `commandes(id, client_id, date)`
-   `lignes_commandes(id, commande_id, produit, quantite)`

1. Lister tous les clients avec leurs commandes (même ceux sans commande).
2. Afficher le total de produits commandés par client.
3. Lister les produits les plus commandés (quantité totale).

## ❓ QCM Vrai / Faux (justifiez si Faux)

1. Un index permet d'accélérer les requêtes de type `SELECT`.
2. Un index est automatiquement créé sur une colonne `PRIMARY KEY`.
3. Un index rend toujours toutes les requêtes plus rapides.
4. Les index ralentissent les opérations d’insertion (`INSERT`).
5. Un index de type `GIN` est adapté à la recherche en texte intégral.
6. Le type d’index par défaut dans PostgreSQL est `Hash`.
7. On peut avoir plusieurs index sur une même table.

---

## 🧠 Questions ouvertes

8. **Donne une définition simple d’un index.**  
   _(en une ou deux phrases)_

9. **Dans quels cas est-il pertinent de créer un index ?**  
   _(Donne 2 exemples concrets)_

10. **Cite deux inconvénients à l’utilisation d’index.**

11. **Quelle différence entre un index simple et un index composé ?**  
    Et pourquoi l’ordre des colonnes est-il important dans un index composé ?

---

## 💡 Étude de cas

Soit la table suivante :

```sql
produits(id, nom, description, prix)
```

Réponds aux questions suivantes :

a. Sur quelle(s) colonne(s) serait-il pertinent de créer un index ?  
b. Quel type d’index utiliserais-tu pour faire des recherches par mots-clés dans la colonne `description` ?  
c. Pourquoi ne faut-il pas indexer toutes les colonnes d'une table ?
