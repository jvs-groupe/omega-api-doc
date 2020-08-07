Interface de paiement
---

Le but est de mettre à disposition un service web permettant à des partenaires d'accéder aux factures pouvant être payées. L'interface sera réalisée avec le portail abonné qui permettra d'afficher ces paiements et de centraliser les transactions. Il y aura ainsi un seul paramétrage identique pour tous les services de paiement.

# Service web de recherche des factures

C'est un service web restful avec un résultat au format application/vnd.api+json.

La documentation complète est accessible [ici](https://jsonapi.org/format/)

## Tester l'accès, maîtriser les "Headers"

Certaines en-têtes sont obligatoires pour pouvoir appeler le serveur :

```
  ApiId: "key"@"name"
  Accept: application/vnd.api+json
```

En cas d'appel authentifié l'en-tête ci-dessous est également obligatoire :

```
  Authorization: Bearer "token"
```

**Vous pouvez-rencontrer des erreurs 415 ou 409 en cas de non respect des règles.**

### Test simple

Le service web ci-dessous doit renvoyer une réponse 204 sans contenu. Si l'accès est soumis à restriction IP, il faudra nous communiquer les IPs ou masques à ajouter dans notre whitelist pour cet accès, erreur 409 sinon.

* Endpoint : /api/v1/partner/test

* Exemple d'appel :

```
curl \
  --location \
  --request GET 'https://omegaweb-pp.jvsonline.fr/api/v1/partner/test' \
  --header 'Accept: application/vnd.api+json' \
  --header 'ApiId: 71c94f39384ead6aa975edb07ca0c75e@partner-xxxx'
```

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

* Exemple :

```
curl \
  --location \
  --request GET 'https://omegaweb-pp.jvsonline.fr/api/v1/partner/test-secured' \
  --header 'Accept: application/vnd.api+json' \
  --header 'ApiId: 71c94f39384ead6aa975edb07ca0c75e@partner-xxxx' \
  --header 'Authorization: Bearer A1Z2E3R4T5Y6U7I8O9P0'
```

## Appeler le service web des factures

Il existe 2 manières d'accéder aux factures, via le service web générique et via un service web appliquant certaines règles de filtrage automatiques.

Dans les 2 cas, en cas de réussite ou échec, l'en-tête links.related.total contiendra le nombe de lignes au total, et links.related.count le nombre de lignes retournées. Il est en effet possible de limiter le nombre de résultats à retourner pour gérer une pagination, ...

### Service web générique

Ce service web est ouvert et ne pose aucune restriction quant aux factures à retourner. C'est à l'appelant de correctement poser les filtres en fonction de ses besoins.

Il est également possible de limiter ou étendre les informations retournées grâce aux champs fields et include (cf. spécifications jsonapi). Il suit les normes JsonApi.

* Endpoint : /api/v1/partner/facture
* Exemple d'appel :

```
curl \
  --location \
  --request GET 'https://omegaweb-pp.jvsonline.fr/api/v1/partner/facture?page[limit]=1&filter[or][contfact.contrat.numcontrat][eq]=343025705' \
  --header 'Accept: application/vnd.api+json' \
  --header 'ApiId: 71c94f39384ead6aa975edb07ca0c75e@partner-xxxx' \
  --header 'Authorization: Bearer A1Z2E3R4T5Y6U7I8O9P0'
```

* Résultat retourné :

```
{
    "jsonapi": {
        "version": "1.0",
        "meta": []
    },
    "meta": {
        "copyright": "Copyright 2018 Jvs-Mairistem",
        "authors": [
            "Jérôme KLAM<jerome.klam@jvs.fr>"
        ]
    },
    "links": {
        "related": {
            "href": "/",
            "meta": {
                "total": 1,
                "count": 1
            }
        }
    },
    "data": [
        {
            "type": "Partner_Facture",
            "id": "46243",
            "attributes": {
                "facture_id": "46243",
                "contfact_id": "48403",
                "organism_id": "2",
                "lotfact_id": "461",
                "exercice": "2009",
                "sessexer": "FR",
                "perannee": "2",
                "datefact": "2009-10-20 00:00:00",
                "enum_typfac": "REEL",
                "enum_natfac": "REEL",
                "typefact": "Reelle",
                "recouvrable": "1",
                "prefixnofact": null,
                "nofacture": "034105",
                "budget": "TOUT",
                "factgroupe": "0",
                "consofac": "23",
                "monnaie_id": "2",
                "codemon": "EUR",
                "ht": "73.42",
                "tva": "4.04",
                "ttc": "77.46",
                "ttcestime": "0",
                "solde": "0",
                "avance": "0",
                "mensualites": "0",
                "nap": "77.46",
                "avoir": "0",
                "datech": "2009-11-03 00:00:00",
                "daterecl": null,
                "dateimp": null,
                "preleve": "0",
                "annulee": "0",
                "dateann": null,
                "hpec_id": null,
                "sensbudgdif": "0",
                "avoirisee": "0",
                "nofactnum": "34105",
                "factlienav_id": "0",
                "a_prelever": "0"
            },
            "relationships": {
                "contfact": {
                    "data": [
                        {
                            "type": "Partner_Contfact",
                            "id": "48403"
                        }
                    ]
                }
            }
        }
    ],
    "included": [
        {
            "type": "Partner_Personne",
            "id": "66888",
            "attributes": {
                "personne_id": "66888",
                "enum_civ": "36",
                "nompers": "XXXXX",
                "prenpers": "XXXXX",
                "cpltnom": null,
                "adrpers1": null,
                "adrpers2": "XXX RUE XXX",
                "adrpers3": null,
                "cppers": "30100",
                "villepers": "ALES",
                "tel": null,
                "fax": null,
                "mail": null,
                "numtva": null,
                "telmobile": null,
                "teltravail": null,
                "numerosiret": null,
                "actif": "1",
                "enum_ctier": "CTI01",
                "enum_natju": "NJU01",
                "enum_paysr": null
            }
        },
        {
            "type": "Partner_Contrat",
            "id": "21556",
            "attributes": {
                "contrat_id": "21556",
                "pconso_id": "21556",
                "numcontrat": "343025705",
                "actif": "1",
                "suspendu": "0",
                "enum_tcon": null,
                "enum_natcont": null,
                "enum_stcont": null,
                "datedeb": "1994-01-01 00:00:00",
                "datefin": null,
                "natabo_id": "11496675",
                "principal": "1"
            },
            "relationships": {
                "redevable": {
                    "data": {
                        "type": "Partner_Personne",
                        "id": "66888"
                    }
                }
            }
        },
        {
            "type": "Partner_Contfact",
            "id": "48403",
            "attributes": {
                "facture_id": "46243",
                "contrat_id": "21556"
            },
            "relationships": {
                "contrat": {
                    "data": {
                        "type": "Partner_Contrat",
                        "id": "21556"
                    }
                }
            }
        }
    ]
}
```

### Service web spécifique

Ce service web est limité dans les critères et va également poser des restrictions et filtrages fixes sur les factures à retourner. Il est destiné aux partenaires pour récupérer la dernière facture payable le plus simplement possible. Seul le format JsonApi est reconnu, les paramètres, filtres, include, fields ne sont pas gérés à ce niveau.

* Endpoint : /api/v1/partner/facture/pour-paiement/:numcontrat

```
curl \
  --location --request GET 'https://omegaweb-pp.jvsonline.fr/api/v1/partner/facture/pour-paiement/343025705?page[limit]=1' \
  --header 'Accept: application/vnd.api+json' \
  --header 'ApiId: 71c94f39384ead6aa975edb07ca0c75e@partner-xxxx' \
  --header 'Authorization: Bearer A1Z2E3R4T5Y6U7I8O9P0'
```

```
{
    "jsonapi": {
        "version": "1.0",
        "meta": []
    },
    "meta": {
        "copyright": "Copyright 2018 Jvs-Mairistem",
        "authors": [
            "Jérôme KLAM<jerome.klam@jvs.fr>"
        ]
    },
    "links": {
        "related": {
            "href": "/",
            "meta": {
                "total": 1,
                "count": 1
            }
        }
    },
    "data": [
        {
            "type": "Partner_FactureCondensee",
            "id": "9837802",
            "attributes": {
                "facture_id": "9837802",
                "contfact_id": "9837803",
                "organism_id": "2",
                "exercice": "2019",
                "sessexer": "FR",
                "perannee": "2",
                "datefact": "2019-09-23 00:00:00",
                "datefactfr": "23092019",
                "enum_typfac": "REEL",
                "enum_natfac": "REEL",
                "typefact": "Reelle",
                "recouvrable": "1",
                "prefixnofact": null,
                "nofacture": "033150",
                "budget": "TOUT",
                "factgroupe": "0",
                "consofac": "53",
                "codemon": "EUR",
                "ht": "188.95",
                "tva": "14.91",
                "ttc": "203.86",
                "ttcestime": "0",
                "solde": "0",
                "avance": "0",
                "mensualites": "0",
                "nap": "203.86",
                "nap_cents": 20386,
                "avoir": "0",
                "datech": "2019-10-14 00:00:00",
                "daterecl": null,
                "dateimp": null,
                "preleve": "0",
                "annulee": "0",
                "dateann": null,
                "avoirisee": "0",
                "nofactnum": "33150",
                "a_prelever": "0",
                "numcontrat": "343025705",
                "nompers": "XXXXX",
                "prenpers": "XXXXX",
                "mail": null,
                "tel": null,
                "tel_mobile": null,
                "vad": "123456svi"
            }
        }
    ]
}
```

Il est également possible de limiter les champs retournés en précisant le paramètre fields, ex :

```
  curl \
    --location  \
    --request GET 'https://omegaweb-pp.jvsonline.fr/api/v1/partner/facture/pour-paiement/343025705?page[limit]=1&fields[Partner_FactureCondensee]=facture_id,datefactfr,numcontrat,nap_cents,vad' \
    --header 'Accept: application/vnd.api+json' \
    --header 'ApiId: 71c94f39384ead6aa975edb07ca0c75e@partner-nextalk-ales' \
    --header 'Authorization: Bearer A1Z2E3R4T5Y6U7I8O9P0'
```

#### Description des champs

* facture_id : Identifiant interne unique de la facture
* contfact_id : Identifiant interne du lien entre la facture et le contrat
* organism_id : Identifiant de l'organisme de facturation
* exercice : Exercice de la facture
* sessexer : Session dans l'exercice
* perannee : Période dans l'année
* datefact : Date de la facture au format YYYY-MM-DD HH:MI:SS
* datefactfr : Date de la facture au format DDMMYYYY
* enum_typfac : Code du type de facture
* enum_natfac : Nature de la facture
* typefact : Libellé du type de facture
* recouvrable : 1 si recouvrable
* prefixnofact : Préfixe du numéro de facture
* nofacture : Numéro de facture unique dans l'année, alphanumérique
* budget : Code du budget
* factgroupe : 1 pour une facture groupée
* consofac : Consommation facturée en m3
* codemon : Code de la mannaie, EUR par défaut
* ht : Total ht en Euros
* tva : Total tva en Euros
* ttc : Total Toutes Taxes en Euros
* ttcestime : 1 pour indiquer une estimation
* solde : 1 pour indiquer une facture soldée
* avance : 1 pour indiquer une avance
* mensualites : Nombre de mensualiatées
* nap : Net à payer en Euros
* nap_cents : Net à payer en centimes d'Euros
* avoir : 1 pour indiquer un avoir
* datech : Date d'échéance au format YYYY-MM-DD HH:MI:SS
* daterecl : Date de réclamation au format YYYY-MM-DD HH:MI:SS
* dateimp : Date d'impression au formaty YYYY-MM-DD HH:MI:SS
* preleve : 1 pour indiquer une facture prélevée
* annulee : 1 pour indiquer une facture annulée
* dateann : Date d'annulation au format YYYY-MM-DD HH:MI:SS
* avoirisee : 1 pour indiquer une facture avoirisée
* nofactnum : Numéro de facture dans l'année, numérique
* a_prelever : 1 pour indiquer une facture à prélever
* numcontrat : Numéro du contrat
* nompers : Nom du redevable
* prenpers : Prénom du redevable
* mail : Mail du redevable
* tel : Téléphone du redevable
* tel_mobile : Téléphone (mobile) du redevable
* vad : Numéro de contrat vad
* debiteur : 1 si débiteur, 0 sinon
* crediteur : 1 si créditeur, 0 sinon

#### Paiements en plusieurs fois

Les champs si dessous sont disponible en plus :

* p_nf_possible : 0, 1, 2 ou 3 pour le paiement en plusieurs fois, maximum 3.
* echeance1 : Première échéance en euros avec cents
* echeance1_cents : Première échéance en cents
* echeance2 : Autres échéances en euros avec cents
* echeance2_cents : Autres échéances en cents
* echeance2 : Autres échéances en euros et cents
* vad_recur1 : Montant en euros avec cents de la première échéance
* vad_recur1_cents : Montant en cents de la première échéance
* vad_recur1_datefr : Date de la première échéance au format DDMMYYYY
* vad_recur2 : Montant en euros avec cents de la seconde échéance
* vad_recur2_cents : Montant en cents de la seconde échéance
* vad_recur2_datefr : Date de la seconde échéance au format DDMMYYYY
* vad_recur3 : Montant en euros avec cents de la troisième échéance
* vad_recur3_cents : Montant en cents de la troisième échéance
* vad_recur3_datefr : Date de la troisième échéance au format DDMMYYYY

On utilise :

* le nombre de jours pour calculer la date d'échance à partir de la date de facture.
* Le jour pour les prochains prélèvements

La première échéance est celle qui permet de réguler les écarts de centimes.
Elle sera donc d'un montant égal ou supérieur aux prochaines, qui elles, auront le même montant, précisé dans echeance2

Si un paiement est demandé le 2, et qu'il ne reste que 34 jours avant l'échéance, le paiement en 2 fois ne sera pas possible.
En effet le 10 du mois prochain, les 34 jours auront été dépassés.