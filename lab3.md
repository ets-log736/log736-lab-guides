# Laboratoire 3 - Réplication, quorums et cohérence éventuelle

## Objectifs

Vous implémenterez un service de compteur répliqué qui privilégie la
disponibilité pendant certaines pannes réseau et qui converge après le
rétablissement des communications.

Le laboratoire met en pratique :

- les quorums de lecture et d'écriture;
- la cohérence éventuelle;
- le read repair;
- l'anti-entropie;
- les CRDT.

La CRDT imposée est un **PN-Counter**.

L'OR-Set add-wins peut être étudié comme exemple en cours, mais ne constitue pas
une piste d'implémentation alternative pour ce laboratoire.

## Processus et transport

Le même conteneur de soumission est lancé plusieurs fois. Chaque instance
reçoit un fichier JSON indiquant son identifiant, ses pairs et les paramètres
`N`, `R` et `W`.

Exigences générales :

- communication TCP + NDJSON;
- un objet JSON UTF-8 par ligne;
- toutes les communications inter-nœuds passent par le routeur fourni par le
  cours;
- les réponses sont corrélées avec `message_id` et `in_reply_to`;
- chaque nœud peut coordonner une lecture ou une écriture client;
- aucun nombre de nœuds, identifiant, port ou paramètre de quorum ne doit être
  codé en dur.

Consultez notamment :

```text
spec/PROTOCOL.md
spec/CRDT.md
spec/QUORUMS.md
spec/CONFIG.md
spec/EVENTS.md
QUICK-REFERENCE.md
```

## PN-Counter

Chaque réplique conserve deux cartes croissantes `P` et `N`.

Une mise à jour positive augmente uniquement `P[self]`.

Une mise à jour négative augmente uniquement `N[self]`.

La valeur est :

```text
value = sum(P) - sum(N)
```

La fusion est effectuée composante par composante :

```text
merge.P[i] = max(local.P[i], remote.P[i])
merge.N[i] = max(local.N[i], remote.N[i])
```

La fusion doit être :

- commutative;
- associative;
- idempotente.

Un état ancien, dupliqué ou reçu dans un ordre différent ne doit jamais faire
perdre une mise à jour déjà connue.

## Quorums N / R / W

`N` est le nombre de répliques du groupe.

`R` est le nombre d'états requis pour réussir une lecture.

`W` est le nombre d'acquittements requis pour réussir une écriture.

```text
1 <= R <= N
1 <= W <= N
```

Le coordinateur local compte comme une réponse pour `R` et comme un
acquittement pour `W`.

### Écriture

Le coordinateur :

1. applique d'abord `delta` à son composant local;
2. propage son état;
3. retourne `status=ok` seulement après `W` acquittements.

Si `W` n'est pas atteint avant le délai, la réponse est
`status=quorum_unavailable`.

La mutation locale n'est pas annulée. Elle peut être propagée plus tard par
anti-entropie. Un échec signalé au client peut donc représenter un résultat
ambigu.

### Lecture

Le coordinateur :

1. obtient `R` états valides;
2. les fusionne;
3. calcule la valeur de l'état fusionné;
4. retourne la réponse au client.

Si `R` n'est pas atteint avant le délai, la réponse est
`status=quorum_unavailable`.

## Réparation et convergence

Après une lecture réussie, le coordinateur effectue un read repair des
répliques obsolètes.

Chaque réplique exécute périodiquement une synchronisation anti-entropie avec un
pair.

Après guérison d'une partition, toutes les répliques doivent converger même
sans nouvelle lecture ou écriture client.

Les Merkle trees et le stockage durable ne sont pas requis.

## Protocole public

Les types de messages obligatoires incluent :

```text
counter_update
counter_update_response
counter_read
counter_read_response
replica_merge
replica_merge_response
replica_read
replica_read_response
sync_request
sync_response
status_request
status_response
```

Les formats JSON détaillés, la représentation du PN-Counter et les événements
stdout obligatoires se trouvent dans le dossier `spec/` du dépôt étudiant.

## Scénarios publics

Le dépôt étudiant fournit quatre scénarios d'intégration :

- `quorum-basic` : `N=3`, `R=2`, `W=2`, plusieurs mises à jour et une lecture;
- `partition-dual-write` : `N=5`, `R=2`, `W=2`, partition `3|2`, écritures des
  deux côtés puis convergence;
- `anti-entropy-heal` : une réplique manque plusieurs mises à jour puis se
  rattrape sans nouveau trafic client;
- `quorum-unavailable` : `N=5`, `W=3`, une partition minoritaire ne peut pas
  annoncer une écriture comme réussie.

Exemples :

```sh
make test SCENARIO=quorum-basic
make test-all
```

L'évaluation utilise également des scénarios non publiés avec d'autres valeurs
de `R` et `W`, partitions, délais, états dupliqués ou anciens et tests de read
repair.

## Livrables

Le dépôt GitHub de l'équipe doit contenir :

- le code source;
- des scripts `build.sh` et `run.sh` fonctionnels;
- les tests de l'équipe dans `tests/`;
- `DESIGN.md`, maximum 500 mots.

## DESIGN.md

Le document doit expliquer brièvement :

- le rôle de `N`, `R` et `W`;
- le comportement lorsque `W` n'est pas atteint;
- pourquoi deux partitions peuvent diverger tout en restant disponibles;
- pourquoi la fusion du PN-Counter converge sous réordonnancement et
  duplication;
- pourquoi l'anti-entropie doit fonctionner sans trafic client;
- l'effet de `R + W > N` sur l'intersection des quorums;
- une garantie de cohérence que votre système ne fournit pas.

## Évaluation

| Élément | Poids |
| --- | ---: |
| Lectures et écritures par quorum | 20 % |
| Correction du PN-Counter | 20 % |
| Anti-entropie et convergence | 15 % |
| Partitions, disponibilité et récupération | 15 % |
| Protocole et environnement d'exécution | 5 % |
| Tests de l'équipe | 5 % |
| `DESIGN.md` | 5 % |
| Évaluation dynamique individuelle | 15 % |

## Évaluation dynamique

Un scénario non vu à l'avance peut présenter :

- une partition;
- des valeurs `N`, `R` et `W`;
- des états PN-Counter divergents;
- une trace d'anti-entropie.

Vous devrez pouvoir :

- prévoir les réponses possibles;
- fusionner des états;
- expliquer la convergence;
- identifier une erreur de protocole ou de quorum.

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
