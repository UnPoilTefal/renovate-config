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
| Base | `config:recommended` | défauts Renovate raisonnables |
| Commits | gitmoji `:arrow_up:` (`:pushpin:` pour pins/digests), semantic commits désactivés | convention gitmoji de tous les repos |
| Groupement | `group:allNonMajor` | 1 seule MR pour les minor/patch → moins de bruit et de pipelines (runner GitLab mono-job) |
| Rebase | `rebaseWhen: behind-base-branch` | les projets GitLab sont en merge fast-forward : Renovate maintient ses MRs rebasées donc mergeables |
| Limites | 3 MRs concurrentes, 2/heure | lisser la charge CI |
| Divers | dependency dashboard (issue épinglée), timezone Europe/Paris, label `renovate` | visibilité et cohérence |

## Ce qui reste local aux repos

Les managers spécifiques au contenu du repo — ex. dans HomeDropZoneFlux : managers `kubernetes`/`flux` sur les `*.yaml` et le customManager regex des `targetRevision` annotés `# renovate:`. Le preset ne porte que le comportement commun.

## Ajuster

Modifier `default.json` via MR ici ; le schéma est validé par `$schema`. Les repos peuvent surcharger n'importe quel réglage après le `extends` (le local gagne).
