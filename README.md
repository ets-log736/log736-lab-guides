# LOG736 - Guides des laboratoires

Ce dépôt public contient les guides officiels des laboratoires du cours
**LOG736 - Fondements des systèmes distribués**.

Ces documents présentent les objectifs, les exigences, les livrables et les
critères d'évaluation des laboratoires. Ils sont indépendants d'une session
particulière.

## Laboratoires

| Laboratoire | Guide |
| --- | --- |
| Laboratoire 1 - Temps, synchronisation et causalité | [Consulter le guide](lab1.md) |
| Laboratoire 2 - Consensus et réplication avec Raft | [Consulter le guide](lab2.md) |
| Laboratoire 3 - Réplication, quorums et cohérence éventuelle | [Consulter le guide](lab3.md) |

## Où se trouvent les autres informations?

**Moodle** demeure la référence administrative pour :

- les dates et heures de remise;
- les dates de démonstration;
- les dérogations;
- les notes et la rétroaction.

Le **dépôt GitHub privé de votre équipe** contient :

- le squelette de départ;
- le `Makefile`;
- `build.sh` et `run.sh`;
- les fichiers `spec/` détaillant le contrat technique;
- les scénarios publics de développement;
- les fichiers à compléter comme `DESIGN.md` et, lorsque requis, `PAXOS.md`.

Ce dépôt public ne contient ni solutions de référence, ni scénarios cachés, ni
logique d'évaluation privée.

## Remise GitHub

La version évaluée est la version Git enregistrée à l'heure officielle de
remise publiée dans Moodle.

À cette heure, le dépôt de l'équipe est fermé en écriture et le SHA Git de la
branche par défaut est enregistré comme version officielle. Les modifications
effectuées après cette heure ne font pas partie de la version évaluée.

Aucun tag Git de remise n'est requis.

La période entre la remise et la démonstration est utilisée par le personnel du
cours pour construire les soumissions, vérifier l'infrastructure et préparer
l'évaluation. Elle ne constitue pas une période de correction du code remis.

## Environnement général

Les laboratoires utilisent GitHub et Podman. Le dépôt étudiant est configuré
automatiquement avec les images de cours correspondant à la session.

Pour afficher la configuration active dans votre dépôt étudiant :

```sh
make course-info
```

Les langages pris en charge sont :

- C/C++;
- Go;
- Java;
- Python;
- Node.js.

Le `Makefile` fourni constitue l'interface recommandée pour construire et
tester la soumission.

## Utilisation de l'IA générative

L'utilisation d'outils d'IA générative est permise, mais elle n'est ni
obligatoire ni évaluée en tant que telle. Aucun journal d'utilisation de l'IA
n'est demandé.

Chaque membre de l'équipe demeure responsable du travail remis et doit pouvoir
expliquer les choix de conception, le comportement du système, les traces
observées et le code pendant l'évaluation dynamique.

Les règles habituelles de l'ÉTS concernant l'intégrité académique et
l'attribution des sources demeurent applicables.
