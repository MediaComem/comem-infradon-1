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

1. Lister tous les animaux et leur espèce
2. Trouver le nombre de zones par parc
3. Afficher les zones avec le nom de leur park
4. Calculer la surface de chaque zone
5. Lister les animaux avec leur dernière position GPS, s'ils en ont une
6. Trouver les animaux sans tracker
7. Trouver les parcs contenant plus de 1 zone
8. Lister les zones où la position d’un animal se trouve en dehors de leur géométrie
9. Trouver les animaux localisés dans une autre zone que la leur
10. Créer un rôle _ecologiste_ avec accès en lecture sur les animaux et zones
11. Créer un rôle _scientifique_ avec accès en écriture sur les tracker
12. Créer un rôle _admin_zones_ avec gestion complète des zones, lecture des parcs
13. Créer une transaction pour insérer un nouvel animal et un tracker associé

---

## Questions Théoriques

1. Quelles sont les propriétés d'une transaction (ACID) ?

-   [ ] Atomicité

-   [ ] Cohérence

-   [ ] Isolation

-   [ ] Durabilité

-   [ ] Disponibilité

2. Quel type d’index est le plus utilisé par défaut dans PostgreSQL ?

-   [ ] B-tree

-   [ ] Hash

-   [ ] GIN

-   [ ] GiST

3. Quelle forme normale élimine les dépendances transitives ?

-   [ ] 1NF

-   [ ] 2NF

-   [ ] 3NF

-   [ ] BCNF

4. Quelle commande donne à un rôle le droit de SELECT sur une table ?

-   [ ] SET RIGHT SELECT ON table TO role;

-   [ ] GRANT SELECT ON table TO role;

-   [ ] ALLOW SELECT TO role;

-   [ ] PERMIT SELECT ON table;

5.  Qu’est-ce que le `REVOKE` permet ?

-   [ ] De créer un rôle

-   [ ] De retirer un droit précédemment accordé

-   [ ] De supprimer un utilisateur

-   [ ] De bloquer les mises à jour

## Questions ouvertes

1. Pourquoi faut-il éviter de stocker plusieurs valeurs dans une même colonne (ex : tableaux, virgules). Donne un exemple et explique comment le corriger
2. À quoi sert la 2e forme normale ? Imagine une table qui contient à la fois le nom de la zone et le nom du parc. Pourquoi pourrait-ce poser problème ?
3. Quelle est la bonne pratique quand une information dépend indirectement d'une clé (via une autre colonne) ? Par exemple, un animal a une espèce, et cette espèce a une classification. Où faut-il stocker la classification ?
4. Quel type d’index est utilisé pour les données géographiques?
