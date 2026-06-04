# V-labs patch stack

This fork ([V-labs/gbrain](https://github.com/V-labs/gbrain)) tracks upstream
[garrytan/gbrain](https://github.com/garrytan/gbrain) with a small rebased
patch stack on the `vlabs` branch.

## Conventions

- `master` mirrors `upstream/master` — never commit there.
- `vlabs` = `master` + this patch stack. **The authoritative list of
  divergences is always** `git log --oneline upstream/master..vlabs`.
- 1 commit = 1 logical patch, subject prefixed `[vlabs]`, updating this file
  in the same commit.
- Upstream-first: every patch gets a PR opened against upstream the day it
  enters the stack. When merged upstream, the commit becomes empty at the
  next rebase and is dropped; update the table below.
- Trunk-tracking cycle: `git fetch upstream` → read
  `git log vlabs..upstream/master` → ff `master` → `git rebase upstream/master vlabs`
  → `bun test` → tag `vlabs-v<upstream-version>-<n>` → deploy the tag.
- Prod runs **tags only**, never a floating branch.

## Patches

| Patch | Fichiers | Pourquoi | Upstream | Statut |
|---|---|---|---|---|
| `[vlabs] fix(jobs): backlinks worker defaults to check, not fix` | `src/commands/jobs.ts` | Les jobs `backlinks` à payload vide (chaînes sync→embed→backlinks enqueues par le serveur après ingestion) faisaient `fix` par défaut et réécrivaient les pages du vault avec des bullets "Referenced in" auto-générés (incident 2026-06-04 : 129 fichiers pollués). Contredit l'intention documentée dans `src/core/cycle.ts:758`. | [PR #1853](https://github.com/garrytan/gbrain/pull/1853) (2026-06-04, depuis `fix/backlinks-default-check` basée v0.42.25.0) | PR ouverte |

| `feat(auth): enforce bound_slug_prefixes on the READ side` | `src/core/{operations,oauth-provider,types}.ts`, `src/core/search/{sql-ranking,hybrid}.ts`, les 2 engines, `src/core/think/{gather,index}.ts`, `src/commands/auth.ts`, tests | ACL par dossier racine du vault : un client OAuth lié à `clients/*,people/*` ne voit QUE ces préfixes sur toutes les surfaces de lecture (search/query/get_page/list/graph/backlinks/timeline/facts/takes/think). Débloque la Phase 2 (ouverture gbrain à l'équipe) sans découper le vault ni casser les wikilinks. Colonne v85 existante, NULL = non restreint. | PR à ouvrir | local |

## Historique des rebases

| Date | Upstream | Tag | Notes |
|---|---|---|---|
| 2026-06-04 | f09f9177 (v0.42.10.0) | vlabs-v0.42.10.0-1 | Création du fork + patch backlinks |
