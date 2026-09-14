# Docs — @Voikyrioh/observable

Maj : 2026-09-14. Point d'entrée obligatoire des agents. ARCHITECTURE.md = carte du code.

| Dossier | Contenu | Quand le consulter |
|---|---|---|
| [ARCHITECTURE.md](../ARCHITECTURE.md) | Structure repo + flux principaux | avant tout changement architectural |
| [adr/](./adr/INDEX.md) | décisions (pattern Observable, TypeScript générique) | avant de modifier le pattern ou d'ajouter une dépendance |
| [business-rules/](./business-rules/INDEX.md) | N/A (librairie pure, pas de règles métier) | — |
| [guides/](./guides/usage.md) | API publique : Observable<T>, subscribe, emit, Subscription, exemples | avant d'intégrer dans un projet |
| [bugs/](./bugs/INDEX.md) | fiches FIX:ULID | avant de modifier une zone marquée FIX: |

## Globales (orga-global)

- `J:/Dev/Projects/orga/global/docs/adr/INDEX.md` — ADR globales (conventions de code, librairies)
- `J:/Dev/Projects/orga/global/product-descriptions/packages.md` — fiche écosystème packages
