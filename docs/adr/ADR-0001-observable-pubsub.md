---
id: ADR-0001
titre: Pattern Observable pub/sub générique typé
type: architecture
statut: acceptée
date: 2026-09-14
portee: repo
remplace: —
liens: []
---

# ADR-0001 — Pattern Observable pub/sub générique typé

## Contexte

Plusieurs systèmes (vue-translate, game-resource-api) ont besoin de notifier des abonnés d'un changement d'état asynchrone (fichiers chargés, service prêt). Plutôt que des Pattern spécifiques, une librairie réutilisable générique.

## Décision

Implémenter une classe `Observable<T>` minimaliste qui :
1. Accepte des abonnements via `subscribe(callback: (v: T) => any)`
2. Émet une valeur via `emit(value: T)` notifiant tous les abonnés
3. Retourne une `Subscription` unsubscribable (UUID, pattern décorateur avec closure)
4. TypeScript générique pour type-safety `Observable<ReadyState>`

## Comment l'appliquer

```typescript
// Créer un Observable
const ready = new Observable<boolean>(false)

// S'abonner
const sub = ready.subscribe((isReady) => {
  console.log('Ready:', isReady)
})

// Émettre
ready.emit(true)

// Se désabonner
sub.unsubscribe()
```

## Quand NE PAS l'appliquer / limites

- Pour des événements (click, input) : utiliser DOM events
- Pas de gestion d'erreur : callbacks doivent catch leurs erreurs
- Pas de replay (historique) : seule la dernière valeur est conservée
- Single-threaded (JavaScript) : pas de concurrence réelle

## Alternatives rejetées

- EventEmitter Node : trop lourd pour un pattern simple
- RxJS : surspec, trop de dépendances pour this usage
- Promise : single-value seulement, pas de notification réutilisable

## Conséquences

- Dépendance minimale (`crypto` natif Node)
- API très simple, facilement testable
- Réutilisable par plusieurs projets (vue-translate, etc.)

## Références

- `src/observable.class.ts` — Implémentation Observable + Subscription
- Utilisé par : `@Voikyrioh/vue-translate`
