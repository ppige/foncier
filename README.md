# Application d'extraction du Foncier

Cette application, bâtie pour fonctionner comme module de l'IDS [geOrchestra](https://github.com/georchestra/georchestra), permet à des utilisateurs authentifiés de commander des extraits de la base de données foncières.

## Pré-requis

Pour fonctionner, l'application nécessite:
 * un LDAP au standard geOrchestra,
 * une base PostgreSQL+PostGIS dans laquelle se trouvent autant de schémas `foncier_XXXX` (où XXXX est un numérique représentant une année) qu'il y a de millésimes de données foncières.

## Installation

Dans un contexte docker:
```
make docker-build
docker-compose up
```
Puis RDV sur [http://localhost:8080/foncier/](http://localhost:8080/foncier/), éventuellement en [chargeant des données de test](samples/README.md).

Notez que le navigateur doit envoyer les headers suivants afin de simuler un proxy de sécurité geOrchestra:
 * sec-username = mon_login
 * sec-roles = ROLE_FONCIER_2014;ROLE_FONCIER_2013;ROLE_FONCIER_2012;ROLE_FONCIER_2011;ROLE_FONCIER_2009
 * sec-org = psc
 * sec-orgname = PSC geOrchestra
 * sec-firstname = mon_prenom
 * sec-lastname = mon_nom
 * sec-email = my_valid_email@provider.com

Cela peut se faire en utilisant par exemple une extension comme "[modheader](https://chrome.google.com/webstore/detail/modheader/idgpnmonknjnojddfkpgkljpfnnfcklj?hl=en)"

## Utilisation

Si la requête entrante est munie d'un header `sec-roles` composé d'une suite de chaînes de caractères du type `ROLE_FONCIER_XXXX`, séparées par des points virgules (eg: `sec-roles = ROLE_FONCIER_2009;ROLE_FONCIER_2011`) alors la page d'accueil présente un formulaire d'extraction permettant de choisir un millésime à extraire (à choisir parmi les années composant `sec-roles`).

Si de plus, la requête est munie d'un header `sec-org` égalant le `cn` d'un organisme du LDAP (ex: `sec-org = psc`), alors l'extraction est autorisée sur l'ensemble des communes de codes INSEE renseignés dans le champ `description` du dit organisme.

Dans le cas contraire, la réponse de l'application est une page indiquant les modalités d'accès aux fichiers fonciers.

## Checking the app

In georchestra LDAP default config, organization psc has its description field listing some commune codes (62053,62267,62565,62685,62696,62862,62562). If you run this compose file, the DB will be loaded with some cadaster sample data covering a wider area. They are published in the foncier_2014 DB schema.
This way, you can check the app is working properly by using the headers as suggested above and requesting the data for year 2014. It should extract cadaster information on the communes listed in the description field.

- Open http://localhost:8080/foncier/
- configure the headers as suggested in main README,
- Try some extraction on year 2014
- check your mail at http://localhost:8081/webmail/
- you should have received an email with the download link. It should allow you to download an archive containing your  extracted data


## License

Licensed under the EUPL V.1.1.

For full details, see [LICENSE.md](LICENSE.md).
