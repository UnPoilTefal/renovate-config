# renovate-config

Preset [Renovate](https://docs.renovatebot.com/config-presets/) partagé des repos UnPoilTefal (GitHub) et webdropzone (GitLab). Objectif : un comportement Renovate identique quelle que soit la plateforme — l'uniformisation passe par la config, pas par l'infra.

## Usage

Dans le `renovate.json` de chaque repo :

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>UnPoilTefal/renovate-config"]
}
```

Fonctionne depuis GitHub (app Mend hébergée) **et** depuis GitLab (Renovate CE / renovate-runner) : les presets `github>` sont récupérables des deux côtés (repo public). Le preset est lu sur `main` à chaque scan — un merge ici s'applique partout au scan suivant.

## Ce que le preset impose

| Réglage | Valeur | Pourquoi |
|---------|--------|----------|
| Base | `config:recommended` | défauts Renovate raisonnables ; groupement des seuls monorepos connus — sinon 1 MR par dépendance (blame précis, revert facile) |
| Commits | gitmoji `:arrow_up:` (`:pushpin:` pour pins/digests), semantic commits désactivés | convention gitmoji de tous les repos |
| Majors | `dependencyDashboardApproval` | MR créée uniquement quand on coche la case dans le Dependency Dashboard — zéro bruit non sollicité |
| Stabilité | `minimumReleaseAge: 3 days` | évite les releases cassées/retirées à chaud (les fixes de sécurité contournent le délai) |
| Automerge | aucun | tout passe en revue — sur les repos GitOps un merge = déploiement (ArgoCD selfHeal) |
| Rebase | `rebaseWhen: behind-base-branch` | les projets GitLab sont en merge fast-forward : Renovate maintient ses MRs rebasées donc mergeables |
| Limites | 6 MRs concurrentes, 2/heure | lisser la charge CI sans transformer un retard de merge en embouteillage |
| Divers | dependency dashboard (issue épinglée), timezone Europe/Paris, label `renovate` | visibilité et cohérence |

`prConcurrentLimit` porté de 3 à 6 le 2026-08-09 : sur `ansible/homedropzone`, trois MRs restées ouvertes depuis fin juillet saturaient le plafond, et les cinq mises à jour suivantes sont restées en « Rate-Limited » pendant neuf jours. À 3, le moindre retard de merge suffit à bloquer un repo entier — d'autant que `rebaseWhen: behind-base-branch` ne rattrape rien si Renovate a classé les MRs en « Edited (Blocked) ».

Décisions revues le 2026-07-18 : `group:allNonMajor` retiré (tout-ou-rien au merge, changelogs mélangés — et l'argument runner est tombé depuis que Renovate CE sort les scans de la CI).

## Ce qui reste local aux repos

Les managers spécifiques au contenu du repo — ex. dans HomeDropZoneFlux : managers `kubernetes`/`flux` sur les `*.yaml` et le customManager regex des `targetRevision` annotés `# renovate:`. Le preset ne porte que le comportement commun.

## Ajuster

Modifier `default.json` via MR ici ; le schéma est validé par `$schema`. Les repos peuvent surcharger n'importe quel réglage après le `extends` (le local gagne).
