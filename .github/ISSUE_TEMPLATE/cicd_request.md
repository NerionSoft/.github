---
name: "⚙️ CI/CD Pipeline Request"
about: Demander une modification, création ou amélioration du pipeline CI/CD
title: "[CI/CD] "
labels: ["ci-cd", "devops"]
assignees: ""
---

## 🎯 Objectif
Décrire précisément ce que tu veux modifier dans la pipeline.

## 🧱 Contexte
Pourquoi cette évolution est-elle nécessaire ?  
(Ex : sécurité, performance, build fail dû à pnpm approve-builds, nouvelle étape SonarCloud…)

## 🛠 Détail technique proposé
Décrire :
- nouveaux jobs
- nouveaux workflows
- outils à intégrer (Trivy, GitLeaks, Husky, etc.)
- triggers (`on: push`, `on: pull_request`, schedules…)

## 📈 Impact
- [ ] Build time  
- [ ] Coût  
- [ ] Intégration Vercel  
- [ ] Rotation des secrets  
- [ ] Conformité / sécurité  

## 🔗 Références
Github Actions existantes, PR liées, SOP DevOps…
