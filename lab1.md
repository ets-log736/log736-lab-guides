# Laboratoire 1 - Temps, synchronisation et causalité

## Objectifs

Ce laboratoire porte sur la synchronisation d'horloges dans un système
distribué.

Vous devrez :

- implémenter les algorithmes de Cristian et de Berkeley;
- mesurer l'effet de la latence, de la dérive et de l'incertitude sur la
  synchronisation;
- concevoir des tests qui vérifient des propriétés du système distribué;
- diagnostiquer une exécution lorsque certains messages ou nœuds sont retardés
  ou indisponibles;
- relier la synchronisation physique aux limites de raisonnement sur la
  causalité.

## Environnement

Le travail est réalisé dans le dépôt GitHub privé fourni à votre équipe.

Les processus communiquent par TCP avec des messages NDJSON, soit un objet JSON
UTF-8 par ligne.

Votre programme doit utiliser l'horloge simulée fournie par le laboratoire. Il
ne doit pas modifier ni utiliser l'horloge système comme source de temps pour
les algorithmes évalués.

Le contrat technique détaillé se trouve dans votre dépôt, notamment dans :

```text
README.md
QUICK-REFERENCE.md
spec/PROTOCOL.md
spec/EVENTS.md
spec/*.schema.json
```

## Partie A - Cristian

Implémentez un serveur de temps et un client utilisant l'algorithme de
Cristian.

Le client doit effectuer plusieurs observations du serveur. Pour chaque
observation, il calcule notamment :

- le RTT;
- une estimation de l'heure du serveur;
- une incertitude associée à l'observation.

Le client doit ensuite choisir comment sélectionner ou combiner ses
observations afin de corriger son horloge logique.

La solution doit terminer proprement lorsqu'une requête expire.

Les scénarios peuvent notamment inclure :

- un RTT variable;
- une réponse très lente;
- une requête sans réponse;
- un délai asymétrique.

## Partie B - Berkeley

Implémentez une ronde de synchronisation Berkeley.

Un nœud est désigné comme dirigeant. L'élection du dirigeant ne fait pas partie
du laboratoire.

Le dirigeant doit :

1. interroger les autres nœuds;
2. recueillir les horloges disponibles;
3. calculer une référence commune;
4. envoyer une correction relative à chaque nœud;
5. corriger également sa propre horloge.

Votre solution doit notamment traiter :

- une valeur aberrante;
- un nœud temporairement inaccessible;
- un message retardé provenant d'une ancienne ronde.

## Tests

Votre suite de tests doit vérifier plusieurs propriétés distinctes. La qualité
des tests est évaluée selon leur capacité à détecter des erreurs réalistes, et
non selon le nombre de tests ou le pourcentage de lignes exécutées.

Elle devrait notamment couvrir :

- Cristian avec différents délais;
- une expiration de requête;
- la correction effective de l'horloge;
- Berkeley avec plusieurs décalages;
- une valeur aberrante;
- un nœud inaccessible;
- un message provenant d'une ancienne ronde.

Les scénarios publics sont fournis dans le dépôt de votre équipe.

Exemples :

```sh
make test SCENARIO=cristian-basic
make test-all
```

Les catégories de perturbations sont publiques, mais les paramètres exacts des
scénarios d'évaluation ne le sont pas.

## Livrables

Le dépôt GitHub de l'équipe doit contenir :

- le code source;
- des scripts `build.sh` et `run.sh` fonctionnels;
- les tests de l'équipe dans `tests/`;
- `DESIGN.md`, maximum 500 mots.

`DESIGN.md` doit expliquer brièvement :

- la sélection des observations de Cristian;
- la gestion d'une valeur aberrante dans Berkeley;
- le comportement lorsqu'un nœud ne répond pas;
- une propriété importante vérifiée par vos tests;
- pourquoi la synchronisation physique ne suffit pas toujours à établir la
  causalité.

## Évaluation

| Élément | Poids |
| --- | ---: |
| Protocole et exécution | 10 % |
| Cristian | 20 % |
| Berkeley | 20 % |
| Robustesse aux perturbations | 15 % |
| Tests de l'équipe | 15 % |
| `DESIGN.md` | 5 % |
| Évaluation dynamique | 15 % |

## Évaluation dynamique

Un scénario non répété à l'avance sera exécuté sur la version officiellement
remise.

Il pourra vous être demandé de :

- prédire un résultat;
- expliquer une trace;
- identifier une erreur;
- retrouver la logique correspondante dans votre code.

Certaines questions sont individuelles.

## Remise et démonstration

Les dates et heures officielles sont publiées dans Moodle.

À l'heure de remise, le SHA Git de la branche par défaut est enregistré comme
version officielle. La démonstration et l'évaluation utilisent cette version
exacte.

Les modifications ultérieures ne font pas partie de la version évaluée. Aucun
tag Git de remise n'est requis.

La période entre la remise et la démonstration est réservée à la validation par
le personnel du cours et ne constitue pas une période de correction du code
remis.
