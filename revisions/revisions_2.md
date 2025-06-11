# Exercice de préparation

Vous travaillez sur un système de **gestion de parcs naturels**. Le modèle inclut :

-   Des **parcs** : park(id, name, area)
-   Des **zones géographiques**: zone(id, park_id, name, geom)
-   Des **animaux**: animal(id, species, name, birthdate)
-   Des **trackers GPS**: tracker(id, animal_id, last_position, last_seen)

Aves les contraines suivantes:

-   Un parc contient plusieurs zones (1-n)

-   Une zone contient plusieurs animaux (1-n)

-   Un animal a éventuellement un tracker GPS (0-1)

Les tables ont la structure suivante:

```SQL
CREATE TABLE park (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    area NUMERIC -- en km²
);

CREATE TABLE zone (
    id SERIAL PRIMARY KEY,
    park_id INTEGER REFERENCES Park(id),
    name VARCHAR(100),
    geom GEOMETRY(POLYGON)
);

CREATE TABLE animal (
    id SERIAL PRIMARY KEY,
    species VARCHAR(100),
    name VARCHAR(50),
    birthdate DATE,
    zone_id INTEGER REFERENCES zone(id)
);

CREATE TABLE tracker (
    id SERIAL PRIMARY KEY,
    animal_id INTEGER UNIQUE REFERENCES animal(id),
    last_position GEOMETRY(POINT),
    last_seen TIMESTAMP
);
```

### 1. Lister tous les animaux et leur espèce

```SQL
SELECT name, species FROM animal;
```

### 2. Trouver le nombre de zones par parc

```SQL
SELECT park_id, COUNT(*) AS zone_count
FROM zone
GROUP BY park_id;
```

> ⚠️ Erreur fréquente : oublier le `GROUP BY` (!!!), ce qui provoque une erreur d’agrégation.

### 3. Afficher les zones avec le nom de leur park

```SQL
SELECT z.id AS zone_id, z.name AS zone_name, p.name AS park_name
FROM zone z
INNER JOIN park p ON z.park_id = p.id;
```

### 4. Calculer la surface de chaque zone

```SQL
SELECT id, name, ST_Area(geom) AS surface
FROM zone;
```

### 5. Lister les animaux avec leur dernière position GPS, s'ils en ont une

```SQL
SELECT a.id, a.name, t.last_position, t.last_seen
FROM animal a
LEFT JOIN tracker t ON a.id = t.animal_id;
```

### 6. Trouver les animaux sans trackers

```SQL
SELECT a.*
FROM animal a
LEFT JOIN tracker t ON a.id = t.animal_id
WHERE t.id IS NULL;
```

> ⚠️ Erreur fréquente : utiliser `INNER JOIN` ici exclurait justement les animaux sans tracker.

### 7. Trouver les parcs contenant plus de 1 zone

```SQL
SELECT park_id
FROM zone
GROUP BY park_id
HAVING COUNT(*) > 1;
```

> ⚠️ `HAVING` filtre les résultats agrégés (comme un `WHERE`, mais après le `GROUP BY`).

### 8. Lister les zones où la position d’un animal se trouve en dehors de leur géométrie

```SQL
SELECT a.id AS animal_id, a.name, z.id AS zone_id, z.name AS zone_name
FROM animal a
INNER JOIN zone z ON a.zone_id = z.id
INNER JOIN tracker t ON a.id = t.animal_id
WHERE NOT ST_Contains(z.geom, t.last_position);
```

> ⚠️ `ST_Contains(geomA, geomB)` vérifie si le polygone geomA contient le point ou la géométrie geomB. Ici, on vérifie si la zone de l’animal contient la dernière position GPS. `NOT ST_Contains(...)` inverse le test : on récupère les animaux en dehors de leur propre zone. L'ordre des paramètres compte ! ST_Contains(zone, point) et non l’inverse. ✅ À retenir : `ST_Contains(A, B)` = "est-ce que A englobe B ?". `NOT ST_Contains(...)` = "est-ce que B est en-dehors de A ?"

### 9. Trouver les animaux localisés dans une autre zone que la leur

```SQL
SELECT a.id AS animal_id, a.name, z1.id AS declared_zone, z2.id AS located_zone
FROM animal a
INNER JOIN tracker t ON a.id = t.animal_id --- on ne prend que les animaux avec un tracker
INNER JOIN zone z1 ON a.zone_id = z1.id --- la zone déclarée de l'animal
INNER JOIN zone z2 ON ST_Contains(z2.geom, t.last_position) --- la zone où se trouve le tracker
WHERE z1.id <> z2.id; --- on compare les deux zones et on filtre pour ne garder que les cas où l’animal est dans une autre zone que la sienne.
```

### 10. Créer un rôle _ecologiste_ avec accès en lecture sur les animaux et zones

```SQL
CREATE ROLE ecologiste;
GRANT CONNECT ON DATABASE your_database TO ecologiste;
GRANT USAGE ON SCHEMA public TO ecologiste;
GRANT SELECT ON animal, zone TO ecologiste;
```

### 11. Créer un rôle _scientifique_ avec accès en écriture sur les tracker

```SQL
CREATE ROLE scientifique;
GRANT CONNECT ON DATABASE your_database TO scientifique;
GRANT USAGE ON SCHEMA public TO scientifique;
GRANT SELECT, INSERT, UPDATE, DELETE ON tracker TO scientifique;
```

### 12. Créer un rôle _admin_zones_ avec gestion complète des zones, lecture des parcs

```SQL
CREATE ROLE admin_zones;
GRANT CONNECT ON DATABASE your_database TO admin_zones;
GRANT USAGE ON SCHEMA public TO admin_zones;
GRANT SELECT ON park TO admin_zones;
GRANT SELECT, INSERT, UPDATE, DELETE ON zone TO admin_zones;
```

### 13. Créer une transaction pour insérer un nouvel animal et un tracker associé

```SQL
BEGIN;

WITH new_animal AS (
    INSERT INTO animal (species, name, birthdate, zone_id)
    VALUES ('Faon', 'Bambi', '2025-06-09', 3)
    RETURNING id
)
INSERT INTO tracker (animal_id, last_position, last_seen)
SELECT id, ST_GeomFromText('POINT(1 1)'), NOW()
FROM new_animal;

COMMIT;

```

> ⚠️ Cette transaction garantit que l’animal et son tracker sont créés ensemble.

### Résumé pratique

| Type de JOIN        | Schéma SQL                        | Ce qu’il fait                                                             | Quand l’utiliser                                                                                        |
| ------------------- | --------------------------------- | ------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| **INNER JOIN**      | `A JOIN B ON A.id = B.a_id`       | Ne retourne que les lignes **avec correspondance des deux côtés**         | Quand vous avez besoin **uniquement** des données liées. Exemple : animaux avec trackers                |
| **LEFT JOIN**       | `A LEFT JOIN B ON A.id = B.a_id`  | Retourne **toutes les lignes de A**, et les lignes de B si elles existent | Quand vous voulez **toutes les lignes de gauche**, même sans correspondance (ex : animaux sans tracker) |
| **RIGHT JOIN**      | `A RIGHT JOIN B ON A.id = B.a_id` | Comme `LEFT JOIN`, mais retourne **toutes les lignes de B**               | Rare. À utiliser si vous voulez **toutes les lignes de droite** même sans correspondance                |
| **FULL OUTER JOIN** | `A FULL JOIN B ON A.id = B.a_id`  | Toutes les lignes de A **et** B, même sans correspondance                 | Pour identifier **les absences des deux côtés** (par exemple : animaux ou trackers orphelins)           |
| **CROSS JOIN**      | `A CROSS JOIN B`                  | Produit cartésien : toutes les **combinaisons possibles**                 | Très rarement utile. À éviter sauf si vous avez une bonne raison (ex : simulation, tests combinatoires) |
| **SELF JOIN**       | `A JOIN A AS B ON A.x = B.y`      | Joint une table **avec elle-même**                                        | Comparer deux lignes d’une même table (ex : proximité entre animaux)                                    |
| **NATURAL JOIN**    | `A NATURAL JOIN B`                | Joint automatiquement sur les **colonnes de même nom**                    | À éviter en pratique (risque d’ambiguïté et erreurs silencieuses)                                       |

---

## Questions Théoriques

1. Quelles sont les propriétés d'une transaction (ACID) ?

-   [x] Atomicité : _tout ou rien_

-   [x] Cohérence : _état valide avant et après_

-   [x] Isolation : _pas d'interférence entre différentes transactions_

-   [x] Durabilité: _la transaction, une fois validée (COMMIT), persiste même en cas de panne_

-   [ ] Disponibilité: _concept relié à [CAP](https://budibase.com/blog/data/cap-vs-acid/) (Consistency, Availability, Partition Tolerance)_

2. Quel type d’index est le plus utilisé par défaut dans PostgreSQL ?

-   [x] B-tree: _C’est l’index par défaut en PostgreSQL, utilisé pour l’égalité, les plages (<, >, BETWEEN...)._

-   [ ] Hash : _Utilisé pour les égalités simples, mais moins courant et moins efficace._

-   [ ] GIN : _Utilisé pour les types de données complexes comme les tableaux, JSONB, texte intégral etc._

-   [ ] GiST : _Utilisé pour les types géométriques, les données spatiales et les types de données complexes._

3. Quelle forme normale élimine les dépendances transitives ?

-   [ ] 1NF : _1ère forme normale, élimine les valeurs multiples dans une colonne._

-   [ ] 2NF : _2ème forme normale, élimine les dépendances partielles._

-   [x] 3NF : _3ème forme normale, élimine les dépendances transitives._

-   [ ] BCNF : _4ème forme normale, stricte, élimine les dépendances fonctionnelles._

4. Quelle commande donne à un rôle le droit de SELECT sur une table ?

-   [ ] SET RIGHT SELECT ON table TO role; _Cette syntaxe n'existe pas._

-   [x] GRANT SELECT ON table TO role; _C'est la syntaxe correcte pour accorder un droit de SELECT à un rôle sur une table._

-   [ ] ALLOW SELECT TO role; _Cette syntaxe n'existe pas._

-   [ ] PERMIT SELECT ON table; _Cette syntaxe n'existe pas._

5.  Qu’est-ce que le `REVOKE` permet ?

-   [ ] De créer un rôle _La commande `CREATE ROLE` permet de créer un rôle._

-   [x] De retirer un droit précédemment accordé _Le `REVOKE` permet de retirer des droits précédemment accordés à un rôle._

-   [ ] De supprimer un utilisateur _La commande `DROP USER` permet de supprimer un utilisateur._

-   [ ] De bloquer les mises à jour _Le `REVOKE` ne bloque pas les mises à jour, il retire des droits._

## Questions ouvertes

### 1. Pourquoi faut-il éviter de stocker plusieurs valeurs dans une même colonne (ex : tableaux, virgules). Donne un exemple et explique comment le corriger

Parce que cela viole la 1ère forme normale (1NF) : chaque cellule d’une table relationnelle doit contenir une seule valeur atomique, pas une liste.

**Exemple incorrect** : Colonne "species" contient : 'lynx, loup, renard'

Pourquoi c'est faux :

-   On ne peut pas filtrer proprement (ex : WHERE species = 'lynx' ne fonctionne pas).

-   Impossible d’indexer efficacement.

-   Risque d’incohérences (ordre des valeurs, orthographe...).

-   Plus difficile de compter, filtrer ou joindre.

**Bonne pratique** :
Créer une table relationnelle :

```SQL
CREATE TABLE animal_species (
    animal_id INTEGER REFERENCES animal(id),
    species VARCHAR(100)
);

```

> ❌ Faux piège à éviter : "_C’est plus simple à stocker ainsi._" Oui, mais ce n’est pas relationnel et vous perdez tous les avantages de SQL.

### 2. À quoi sert la 2e forme normale ? Imagine une table qui contient à la fois le nom de la zone et le nom du parc. Pourquoi pourrait-ce poser problème ?

**Bonne réponse** :
Elle supprime les dépendances partielles : quand une colonne dépend d’une partie de la clé primaire (dans les cas de clé composite).

Exemple concret :

Table : zone(id, name, park_id, park_name)

park_name dépend uniquement de park_id, pas de toute la clé (si elle était composite) → cela viole la 2NF.

Problème :

-   Redondance : le nom du parc est dupliqué.

-   Incohérences possibles si une ligne est mise à jour mais pas les autres.

**Bonne modélisation** :

-   Supprimer `park_name` de zone.

-   Conserver `park_name` dans une table park, et joindre quand nécessaire.

> ❌ Faux piège à éviter : "_C’est pratique pour éviter les jointures._" Oui, mais ça entraîne des anomalies de mise à jour et de suppression.

### 3. Quelle est la bonne pratique quand une information dépend indirectement d'une clé (via une autre colonne) ? Par exemple, un animal a une espèce, et cette espèce a une classification. Où faut-il stocker la classification ?

Exemple : Un animal a une espèce, et chaque espèce a une classification (ex. : carnivore, herbivore...).

**Mauvaise pratique** : stocker la classification dans la table animal.

Pourquoi c’est faux :

-   Redondance : tous les animaux de la même espèce ont la même classification.

-   Risque d’erreur si une classification est mise à jour pour un animal mais pas les autres.

-   Cela viole la 3e forme normale (3NF) car classification dépend transitivement de la clé (via species).

**Bonne pratique** :

Créer une table species :

```SQL
CREATE TABLE species (
    name VARCHAR PRIMARY KEY,
    classification VARCHAR
);

```

Et faire de `animal.species` une clé étrangère vers `species.name`.

> ❌ Faux piège à éviter : "_C’est plus simple de tout garder dans une seule table._"- C’est une simplification dangereuse qui crée de la duplication inutile.

### 4. Quel type d’index est utilisé pour les données géographiques?

**Bonne réponse** :
Le type d’index est GiST (Generalized Search Tree).

**Pourquoi GiST?**

-   Il permet de gérer des objets géométriques : points, polygones, lignes, etc.

-   Utilisé pour les fonctions spatiales comme ST_Contains, ST_Within, etc.

-   Compatible avec PostgreSQL via PostGIS.

Exemple :

```SQL
CREATE INDEX ON zone USING GIST (geom);
```

> ❌ Faux pièges : B-tree : il ne fonctionne pas sur les types GEOMETRY. Hash : limité à l’égalité simple. GIN : adapté à des contenus comme tableaux, JSONB ou texte intégral, pas les géométries.
