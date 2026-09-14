# Guide d'utilisation — @Voikyrioh/observable

API publique complète de la librairie Observable pub/sub.

## Installation

```bash
npm install @Voikyrioh/observable
```

## Classe Observable<T>

Observable générique typé pour un pattern pub/sub simple.

### Constructeur

```typescript
constructor(defaultValue?: T)
```

Crée une instance Observable avec une valeur par défaut optionnelle.

```typescript
// Sans valeur par défaut
const ready = new Observable<boolean>()

// Avec valeur par défaut
const counter = new Observable<number>(0)
```

Si `defaultValue` est fourni, les abonnés immédiatement ajoutés via `subscribe()` le reçoivent d'office.

### subscribe(callback: (v: T) => any): Subscription

S'abonner à l'Observable et recevoir toute émission future.

```typescript
const ready = new Observable<boolean>(false)

const subscription = ready.subscribe((isReady) => {
  console.log('State:', isReady)
})

// Si une valeur existante, callback est invoqué immédiatement
// (ex. console.log('State: false'))
```

**Comportement** :
- Callback invoqué immédiatement si une valeur existe (synchrone)
- Callback invoqué à chaque `emit()` (asynchrone)
- Type-safe : TypeScript valide le type du paramètre

### emit(value: T): void

Émet une nouvelle valeur et notifie tous les abonnés.

```typescript
const ready = new Observable<boolean>(false)
ready.subscribe((state) => console.log('Ready:', state))

ready.emit(true)    // Logs: "Ready: true"
ready.emit(false)   // Logs: "Ready: false"
```

**Comportement** :
- Valeur stockée dans `#value` (état conservé)
- Tous les abonnés notifiés synchroniquement
- Aucune gestion d'erreur : si une callback lance, d'autres callbacks suivantes ne seront pas appelées

## Classe Subscription

Représente un abonnement unsubscribable.

### unsubscribe(): void

Supprime l'abonnement et désinscrire la callback.

```typescript
const ready = new Observable<boolean>()
const sub = ready.subscribe(() => console.log('Updated'))

ready.emit(true)   // Logs: "Updated"

sub.unsubscribe()

ready.emit(false)  // Aucun log
```

### Propriété id

```typescript
readonly id: string
```

UUID unique de la subscription (utilisé en interne).

## Cas d'usage

### 1. État asynchrone (readiness)

```typescript
export class I18nService {
  #ready = new Observable<boolean>(false)

  async loadTranslations() {
    await fetch(...)
    this.#ready.emit(true)
  }

  getReadiness() {
    return this.#ready
  }
}

// Utilisation
const i18n = new I18nService()
i18n.getReadiness().subscribe((isReady) => {
  if (isReady) console.log('Translations loaded')
})
```

### 2. Changement d'état réactif

```typescript
const language = new Observable<string>('en-US')

language.subscribe((lang) => {
  console.log('Language switched to:', lang)
})

language.emit('fr-FR')  // Logs: "Language switched to: fr-FR"
```

### 3. Gestion de ressource avec unsubscribe

```typescript
const observable = new Observable<string>('init')

const sub1 = observable.subscribe(() => console.log('Sub1'))
const sub2 = observable.subscribe(() => console.log('Sub2'))

observable.emit('value')  // Logs: "Sub1", "Sub2"

sub1.unsubscribe()

observable.emit('value2') // Logs: "Sub2" only
```

## Consommateurs recommandés

Actuellement utilisé par :
- `@Voikyrioh/vue-translate` (readiness fichiers traduction)
- Autres projets nécessitant un pub/sub léger

## Notes techniques

- **TypeScript générique** : `Observable<T>` valide les types
- **UUID subscription** : `crypto.randomUUID()` (Node.js natif)
- **Pas d'ordre garanti** : callbacks notifiées dans l'ordre d'ajout (insertion order Map)
- **No error handling** : Observable ne catch pas les erreurs callbacks

## Limitations

1. **Single-value** : seule la dernière émission conservée (pas d'historique)
2. **No error channel** : aucun mécanisme `.error()` ou `.complete()`
3. **Synchronous notification** : pas de batch ou async/await automatique
4. **Memory** : abonnés non unsubscribed = fuite mémoire
