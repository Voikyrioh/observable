# Architecture — @Voikyrioh/observable

Stack : TypeScript, Node.js ≥22 · Style : pub/sub pattern · Entrée : `index.ts`
Maj : 2026-09-14

## Vue d'ensemble

Librairie Observable minimaliste pour un pattern pub/sub event-driven. Gère des abonnements typées avec `subscribe(callback)` retournant des `Subscription` unsubscribables. Utilisée notamment par `vue-translate` pour notifier de la readiness des ressources.

## Carte

```
index.ts                           → Export Observable class
src/
└── observable.class.ts            → Observable<T> + Subscription (pub/sub générique)
```

## Flux principaux

- **Création** : `new Observable<Type>(defaultValue?)` — initialise avec valeur par défaut (optionnel)
- **Abonnement** : `observable.subscribe(callback)` → retourne `Subscription` ; callback invoke immédiatement si valeur existante
- **Émission** : `observable.emit(value)` → met à jour `#value`, notifie tous les abonnés
- **Désabonnement** : `subscription.unsubscribe()` → supprime la callback de la liste

## Conventions locales

- Observable est entièrement typé générique `Observable<T>`
- Chaque abonnement reçoit un UUID unique (`crypto.randomUUID()`)
- Aucune gestion d'erreur du côté Observable (callbacks doivent catch)

## Commandes

- Type checking : `npm run ci` (tsc)

## Où chercher

| Je cherche… | Dossier / fichier |
|---|---|
| la classe Observable | `src/observable.class.ts` |
| Subscription | `src/observable.class.ts` class `Subscription` |
| exemples d'usage | `docs/guides/usage.md` |
