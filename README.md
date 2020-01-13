Documentation des APIs Omega
---

# Présentation

Omega est une application dédiée aux structures de distribution de l’eau et d’assainissement. Afin de communiquer avec d'éventuels partenaires des APIs ont été implémentées. La documentation des APIs est libre, mais leur usage devra être validé par nos équipe techniques (prendre contact avec votre correspondant habituel pour cela). [JVS-Mairistem](https://www.jvs-mairistem.fr/approche/omega).

# Techniquement

* Format des échanges : Json-Api, spécifications décrites [ici](https://jsonapi.org/format/1.0/)
* Echanges soumis à un contrat préalable d'utilisation avec JVS-Mairistem.
* [Spécificités Json-Api](./jsonapi.md) liées à Omega.

**Vous trouverez certains exemples d'utilisation sous divers langages sur [github](https://github.com/jvs-groupe)**

# Authentification

## Partenaires

Pour les partenaires une authentification basée sur les spécifications [Hawk](https://github.com/hueniverse/hawk) a été mise en place. Nous fournirons par client un accès via clef ainsi qu'un identifiant et une clef de sécurisation pour le protocole Hawk. Ces clefs et identifiants ne devront pas être accessible par les utilisateurs finaux et devront rester secrets.

# Limitations

Les APIs ne sont pour l'instant accessible qu'en lecture. Pour modifier des données un système de "demandes" a été mis en place afin que le service des eaux soit le responsable du contrôle et de la validation des demandes de changements de données. Il sera bien entendu possible de suive l'évolution de ces demandes. Le service des eaux se réserve le droit de refuser ou rejeter une demande de modification non conforme.

# Schéma général de circulation

Afin de garder une cohérence dans le temps pour les APIs et utiliser des technologies récentes et sécurisées tous les appels passeront forcément par OmegaWEB, application en SASS.

![](./images/Diapositive1.png)

![](./images/Diapositive2.png)

# APIs

Les APIs ont pour but principal de mettre à disposition les données liées aux points de consommation et contrats; restreint à une population en accord avec le partenaire.

## Fonctionnement

**Le fonctinnement décrit ci-dessous est lié au format Json-Api**

Chaque objet "métier" aura un équivalent en modèle dans l'API. Par défaut sans rien préciser l'API va retourner les données jugées principales de cet objet. Par exemple pour une liste de contrats on aura les données du contrat, son adresse et l'occupant. Pour un seul contrat on aura en plus les relevés et les factures. Ceci étant bien entendu paramétrable à la demande pour optimiser au mieux les flux échangés avec OmegaWEB, ça ne sert à rien de retourner des données volumineuses non exploitées.

Pour rappel le lien de la [partie technique](./jsonapi.md) de l'API pour réaliser ces filtres, inclusions, ...

## Tester l'accès, maîtriser les "Headers"

A ce niveau aucune en-tête ApiId n'est nécessaire, mais il est conseillé de l'ajouter. En cas de code retour 415, vérifiez que vous avez bien renseigné l'en-tête Accept, donc au minimum :

```
  ApiId: key@name
  Accept: application/vnd.api+json
```

### Test simple

Le service web ci-dessous doit renvoyer une réponse 204 sans contenu. Si l'accès est soumis à restriction IP, il faudra nous communiquer les IPs ou masques à ajouter dans notre whitelist pour cet accès.

* Endpoint : /api/v1/partner/test

### Test 404 avec une réponse au format Json-Api

* Endpoint : /api/v1/partner/test-404
* Résultat attendu :

```
{
    "errors": [
        {
            "links": {
                "about": "http://jsonapi.org/format"
            },
            "code": "404",
            "title": "Not Found!"
        }
    ]
}
```

Pour le résultat on peut donc soit utiliser le code HTTP ou le code retourné dans l'object json errors en cas d'erreur.

### Test de la sécurisation

* Endpoint : /api/v1/partner/test-secured

Une en-tête Authorizarion est obligatoire, différente en fonction du type de sécurité implémentée.

## Les énumérations

Les énumérations contiennent l'ensemble des codifications de l'application, par exemple les civilités, diamètres de compteur, ... Ces donnés ne varient pas énormément et peuvent être mises en cache pour une durée limitée afin de garder des performances correctes.

![](./images/Diapositive3.png)

Informations sur l'API :

* Recherche :
    * Endpoint : /api/v1/partner/enum
    * Méthode : GET
    * Paramètres : selon format json-API
    * Cette API n'est pas paginée par défaut
    * Include :
        * ligenum : lignes de l'énumération (par défaut On)

## Les adresses

Les adresses sont découpées en plusieurs APIs afin de simplifer les lectures.

![](./images/Diapositive4.png)

* Recherche de commune :
    * Endpoint : /api/v1/partner/commune
    * Méthode : GET
    * Paramètres : selon format json-API
    * Cette API est paginée par 20
    * Include :
        * voie (par défaut Off)
* Lecture d'une commune :
    * Endpoint : /api/v1/partner/commune/:organism_id
    * Méthode : GET
    * Paramètres : identifiant, et selon format json-API
        * Include :
            * voie (par défaut On)
* Recherche de voie :
    * Endpoint : /api/v1/partner/voie
    * Méthode : GET
    * Paramètres : selon format json-API
    * Cette API est paginée par 20
    * Include :
        * commune (par défaut On)
* Lecture d'une voie :
    * Endpoint : /api/v1/partner/voie/:voie_id
    * Méthode : GET
    * Paramètres : selon format json-API
    * Include :
        * commune (par défaut On)
* Recherche des adresses de desserte :
    * Endpoint : /api/v1/partner/pdessadr
    * Méthode : GET
    * Paramètres : selon format json-API
    * Cette API est paginée par 20
    * Include :
        * voie (par défaut On)
        * Commune (par défaut On)
* Lecture d'une adresse de desserte :
    * Endpoint : /api/v1/partner/pdessadr/:pdessadr_id
    * Méthode : GET
    * Paramètres : identifiant, et selon format json-API
    * Include :
        * voie (par défaut On)
        * Commune (par défaut On)

## Le Point de consommation

Pour faire simple, un point de consommation peut être assimilé à un compteur. Voici le modèle général de l'API :

![](./images/Diapositive5.png)

Informations sur l'API :

* Recherche :
    * Endpoint : /api/v1/partner/pconso
    * Méthode : GET
    * Paramètres : selon format json-API
    * Cette API est paginée par défaut par 20.
    * Include :
        * pdessadr (par défaut On)
        * voie (par défaut On)
        * commune (par défaut On)
        * contratactif (par défaut On)
        * compteur (par défaut On)
        * occupant (par défaut On)
        * redevable (par défaut On)
        * propriétaire (par défaut On)
* Lecture d'enregistrement
    * Endpoint : /api/v1/partner/pconso/:pconso_id
    * Méthode : GET
    * Paramètres : L'identifiant, et selon format json-API
    * Include :
        * pdessadr (par défaut On)
        * voie (par défaut On)
        * commune (par défaut On)
        * contratactif (par défaut On)
        * compteur (par défaut On)
        * occupant (par défaut On)
        * redevable (par défaut On)
        * propriétaire (par défaut On)
