# 📘 Révisions – Infrastructure des données (Avril 2025)

---

✍️ **L'examen se déroule sur papier** : aucune exécution de requête SQL ne sera possible. Il s'agit d'évaluer votre **compréhension théorique** et votre **capacité à analyser des situations**.

---

### 📝 Exercice 1 – Client / Commande

#### 🔹 Modèle UML

[![Modèle UML](UML.png)](UML.png)

---

### 📝 Exercice 2 – Requêtes SQL

1. Sélectionner tous les employés.

```sql
SELECT * FROM employes;
```

2. Lister les employés embauchés après 2022.

```sql
SELECT *
FROM employes
WHERE date_embauche > '2022-12-31';
```

3. Afficher le salaire moyen des développeurs.

```sql
SELECT AVG(salaire)
FROM employes
WHERE poste = 'développeur';
```

4. Trier les employés par salaire décroissant.

```sql
SELECT *
FROM employes
ORDER BY salaire DESC;
```

5.

```sql
DELETE FROM employes
WHERE poste = 'stagiaire';
```

---

### 📝 Exercice 3 – Détection d’anomalies

**Anomalies potentielles** :

-   Insertion : impossible d’ajouter un nouveau client sans commande
-   Mise à jour : changement du nom du client = modification sur plusieurs lignes
-   Suppression : supprimer une ligne = perte d’infos sur le client ou le produit

**Décomposition en 3NF** :

1. `Clients(client_id, nom, ville)`
2. `Produits(produit_id, nom, prix)`
3. `Commandes(commande_id, client_id)`
4. `LignesCommandes(commande_id, produit_id, quantite)`

---

### 📝 Exercice 4 – Jointures & Agrégats

1. Lister tous les clients avec leurs commandes (même ceux sans commande).

```sql
SELECT c.nom, co.id, co.date
FROM clients c
LEFT JOIN commandes co ON c.id = co.client_id;
```

2. Afficher le total de produits commandés par client.

```sql
SELECT c.nom, SUM(lc.quantite) AS total_produits
FROM clients c
JOIN commandes co ON c.id = co.client_id
JOIN lignes_commandes lc ON co.id = lc.commande_id
GROUP BY c.nom;
```

3. Lister les produits les plus commandés (quantité totale).

```sql
SELECT produit, SUM(quantite) AS total
FROM lignes_commandes
GROUP BY produit
ORDER BY total DESC;
```

---

### ❓ QCM Vrai / Faux

1. ✅ Vrai — Un index accélère les requêtes de type `SELECT`.
2. ✅ Vrai — Une `PRIMARY KEY` crée automatiquement un index.
3. ❌ Faux — Un index n’accélère pas toutes les requêtes.
4. ✅ Vrai — Les index ralentissent les `INSERT`.
5. ✅ Vrai — `GIN` est utilisé pour la recherche plein texte.
6. ❌ Faux — L’index par défaut est de type `BTree`.
7. ✅ Vrai — On peut créer plusieurs index sur une table.

---

### 🧠 Questions ouvertes

**8. Définition d’un index :**
Un index est une structure qui accélère l’accès aux données dans une table.

**9. Cas pertinents pour créer un index :**

-   Colonne utilisée dans une clause WHERE
-   Colonne utilisée dans une jointure

**10. Inconvénients d’un index :**

-   Ralentit les opérations d’écriture (INSERT, UPDATE)
-   Utilise plus d’espace disque

**11. Index simple vs composé :**
Un index simple porte sur une seule colonne.
Un index composé porte sur plusieurs colonnes.
L’ordre est important : seules les requêtes respectant cet ordre bénéficieront de l’index.

---

### 💡 Étude de cas – Table `produits(id, nom, description, prix)`

**a. Colonnes à indexer :**

-   `nom`
-   `prix`

**b. Type d’index pour recherche plein texte sur `description` :**

```sql
CREATE INDEX idx_description_gin
ON produits USING GIN(to_tsvector('french', description));
```

**c. Pourquoi ne pas indexer toutes les colonnes ?**
Indexer toutes les colonnes consomme des ressources et ralentit les écritures. Il faut indexer uniquement les colonnes utiles pour les recherches.
