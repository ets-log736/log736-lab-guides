# Laboratoire 2 - Consensus et réplication avec Raft

## Objectifs

Vous implémenterez un service répliqué tolérant aux pannes avec Raft.

Le service maintient un entier et applique les commandes :

```text
SET x
ADD x
SUB x
MULT x
```

Vous devrez démontrer :

- l'élection d'un leader;
- la réplication et la validation du journal;
- le changement de leader après une panne;
- la réconciliation après une partition réseau.

Paxos reste au programme, mais aucune seconde implémentation de consensus n'est
demandée. Vous analyserez plutôt des traces afin de raisonner sur la sûreté et
la vivacité du consensus.

## Processus et transport

Le même conteneur de soumission est lancé plusieurs fois. Chaque instance
reçoit un fichier JSON indiquant son identifiant, ses pairs, les paramètres
temporels et le chemin de stockage stable.

Le contrat exact est fourni dans le dépôt GitHub de votre équipe.

Exigences générales :

- communication TCP + NDJSON;
- un objet JSON UTF-8 par ligne;
- toutes les communications inter-nœuds passent par le routeur fourni par le
  cours;
- les réponses sont corrélées avec `message_id` et `in_reply_to`;
- une réponse ne doit pas dépendre de la même connexion TCP que la requête;
- aucun nom de nœud, port, nombre de nœuds ou délai ne doit être codé en dur.

Consultez notamment :

```text
spec/PROTOCOL.md
spec/RAFT.md
spec/CONFIG.md
spec/EVENTS.md
QUICK-REFERENCE.md
```

## Machine à états

La valeur initiale du service est `0`.

Une réponse client avec `status=ok` ne peut être envoyée qu'après la validation
de l'entrée par une majorité et son application à la machine à états.

## Comportement Raft requis

Votre implémentation doit notamment fournir :

- les rôles follower, candidate et leader;
- les transitions correctes lors des changements de terme;
- une minuterie d'élection aléatoire dans l'intervalle fourni;
- des heartbeats périodiques du leader;
- `RequestVote` avec vérification du terme, du vote déjà accordé et de la
  fraîcheur du journal;
- `AppendEntries` avec vérification de `prev_log_index` et `prev_log_term`;
- la suppression des conflits et le rattrapage des followers;
- le commit par majorité;
- la règle Raft selon laquelle le leader ne fait avancer directement
  `commitIndex` par comptage majoritaire que pour une entrée de son terme
  courant;
- l'application des entrées confirmées exactement une fois et dans l'ordre;
- le retour au rôle follower lors de la réception d'un terme supérieur;
- le stockage durable de `currentTerm`, `votedFor` et du journal dans le chemin
  fourni par la configuration.

Les indices du journal commencent à `1`, avec une entrée virtuelle d'index `0`
et de terme `0`.

## Protocole public

Les types de messages obligatoires incluent :

```text
request_vote
request_vote_response
append_entries
append_entries_response
client_command
client_response
status_request
status_response
```

Les formats JSON détaillés et les événements stdout obligatoires se trouvent
dans le dossier `spec/` du dépôt étudiant.

## Scénarios publics

Le dépôt étudiant fournit quatre scénarios d'intégration :

- `raft-election-basic` : élection sans panne;
- `raft-replication-basic` : plusieurs commandes et convergence;
- `raft-leader-failover` : arrêt du premier leader puis nouvelle élection;
- `raft-partition-recovery` : follower isolé temporairement puis
  réconciliation.

Exemples :

```sh
make test SCENARIO=raft-election-basic
make test-all
```

L'évaluation utilise également des scénarios non publiés avec d'autres
temporisations, pertes de messages, partitions, redémarrages et ordres de
commandes.

## Paxos - analyse

Complétez `PAXOS.md`, maximum 400 mots.

Vous devrez notamment expliquer :

- une trace de préemption répétée pouvant empêcher la progression;
- la règle de sélection d'une valeur après un quorum de promesses contenant des
  valeurs déjà acceptées.

Une trace Paxos différente peut être utilisée pendant l'évaluation dynamique.

## Livrables

Le dépôt GitHub de l'équipe doit contenir :

- le code source;
- des scripts `build.sh` et `run.sh` fonctionnels;
- les tests de l'équipe dans `tests/`;
- `DESIGN.md`, maximum 700 mots;
- `PAXOS.md`, maximum 400 mots.

## Évaluation

| Élément | Poids |
| --- | ---: |
| Élection et transitions de rôle | 15 % |
| Réplication et validation du journal | 20 % |
| Pannes, failover et réconciliation | 20 % |
| Robustesse et stockage stable | 10 % |
| Tests de l'équipe | 10 % |
| Analyse Paxos | 5 % |
| `DESIGN.md` | 5 % |
| Évaluation dynamique individuelle | 15 % |

## Évaluation dynamique

L'équipe reçoit un scénario non vu à l'avance.

Vous devrez pouvoir :

- prévoir le comportement du cluster;
- interpréter une trace;
- expliquer une décision de vote ou de commit;
- relier un comportement incorrect à votre code.

Des questions individuelles peuvent porter sur Raft ou Paxos.

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
