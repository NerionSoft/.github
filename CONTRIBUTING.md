# Contribuer à NerionSoft

Merci de contribuer ❤️  
Ce document décrit les règles de contribution (issues, branches, commits, PR, qualité, sécurité).

## 1) Avant de commencer
- Respecte notre [Code of Conduct](./CODE_OF_CONDUCT.md).
- Pour une faille de sécurité, **n’ouvre pas une issue publique** : contacte `security@nerionsoft.com`.

## 2) Comment contribuer
Tu peux :
- signaler un bug (issue “🐞 Bug Report”),
- proposer une feature (issue “✨ Feature Request”),
- améliorer la doc,
- réduire la dette technique,
- améliorer CI/CD.

Utilise les templates d’issue disponibles dans `.github/ISSUE_TEMPLATE/`.

## 3) Workflow Git (branches & PR)
Convention recommandée (si applicable au repo) :
- `main` : stable / prod
- `integration` : staging / pré-prod
- branches de travail : `feat/...`, `fix/...`, `chore/...`, `docs/...`, `refactor/...`

Règles :
- Ouvre une PR vers `integration` pour les changements normaux.
- Les hotfix critiques peuvent partir de `main` (`hotfix/...`) puis être **re-propagés** vers `integration`.
- Une PR = un objectif clair. Évite les PR “fourre-tout”.

## 4) Setup local (Node + pnpm)
Pré-requis :
- Node.js (version indiquée par le repo via `engines`, `.nvmrc`, ou documentation)
- pnpm via Corepack

Commandes :
```bash
corepack enable
pnpm -v
pnpm install
