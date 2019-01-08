Spécificités liées au format JsonAPI
---

Ces spécificités sont communes à l'ensemble des APIs d'Omega.

# Pagination

Pour des résultats retournant plusieurs éléments, il y a une pagination, par 20.

Le paramètre utilisé est page, exemples :

* page=2, la page 2, de 21 à 40
* page[offset]=21&page[limit]=20

# filter: Filtres, critères de recherche

Pour la partie filter, voici le descriptif et quelques exemples :

* Egalité 'eq'
* Egalité ou null 'eqn'
* Différent 'neq'
* Différent ou null 'neqn'
* Supérieur 'gt'
* Supérieur ou null 'gtn'
* Supérieur ou égal 'gte'
* Supérieur ou égal ou null 'gten'
* Inférieur 'lt'
* Inférieur ou null 'ltn'
* Inférieur ou égal 'lte';
* Inférieur ou égal ou null 'lten';
* Contient 'like';
* Dans 'in';
* Pas dans 'nin';
* Vide 'empty';
* Pas vide 'nempty';
* Entre 'between';
* Commence par 'begin';
* Termine par 'end';

Exemple :

* filter[numpconso][eq]=abcdef&filter[pconso_id][gte]=23
* filter[voie][like]=principale&filter[ville][begin]=met

# filter: Options

Pour l'instant les options suivantes sont disponibles :

* Opérateur par défaut si omis : filter[options][mode]=eq
* Sensibilité à la casse : filter[options][case_sensitive]=false
* Opérateur entre clauses : filter[options][andor]=and
