# React: State Management e useReducer

## 1. Cos'è lo State Management?

In React, lo **state** rappresenta i dati che possono cambiare durante l'utilizzo dell'applicazione.

Esempi di state:

- un menu aperto o chiuso
- il testo scritto in un input
- un carrello della spesa
- una lista di prodotti
- i dati di un utente
- lo stato di un form

Quando un'applicazione diventa più grande, gestire lo state può diventare più complesso.

Per questo esistono diversi modi per gestire lo state.

---

# 2. Diversi approcci per gestire lo state

In React possiamo gestire lo state in diversi modi.

I più comuni sono:

- `useState`
- `useReducer`
- Context API
- librerie esterne come Redux, Zustand, MobX

Esistono anche librerie NPM create apposta per gestire lo stato globale di un'applicazione.

Una delle più famose è **Redux**.

Redux viene usato soprattutto in applicazioni grandi, dove tanti componenti devono condividere e modificare lo stesso stato.

---

# 3. Cosa impariamo oggi?

Oggi impariamo una nuova strategia di State Management:

```ts
useReducer()
```

`useReducer` è un Hook di React.

È una alternativa a `useState`.

---

# 4. useReducer Hook

`useReducer()` è un Hook che serve per gestire stati più complessi.

È simile, come idea generale, a `Array.prototype.reduce()`, ma in React viene usato per aggiornare lo stato di un componente.

Possiamo pensarlo così:

> `useReducer` prende lo stato attuale, riceve un'azione e produce un nuovo stato.

---

# 5. useReducer è un'alternativa a useState

`useReducer()` è un'alternativa a:

```ts
useState()
```

Ma questo non significa che `useState` non si usa più.

`useState` rimane perfetto per stati semplici.

Esempi di stati semplici:

```ts
const [menuOpen, setMenuOpen] = useState(false);
```

```ts
const [count, setCount] = useState(0);
```

```ts
const [name, setName] = useState("");
```

In questi casi `useState` è chiaro, semplice e adatto.

---

# 6. Quando usare useReducer?

`useReducer` è più adatto quando lo stato è complesso.

Esempi di stati complessi:

- carrello della spesa
- form complessi
- profili utente
- liste con molte azioni possibili
- stati con più proprietà collegate tra loro
- logiche dove il nuovo stato dipende dal tipo di azione eseguita

Esempio pratico:

Un carrello può avere tante azioni:

- aggiungere un prodotto
- rimuovere un prodotto
- svuotare il carrello
- aggiornare la quantità
- calcolare il totale

In questi casi `useReducer` può essere più ordinato di tanti `useState`.

---

# 7. Confronto tra useState e useReducer

## useState

```ts
const [state, setState] = useState(initialState);
```

Con `useState` riceviamo:

1. il valore dello state
2. una funzione per aggiornarlo

Esempio:

```ts
const [count, setCount] = useState(0);
```

Per aggiornare lo stato:

```ts
setCount(count + 1);
```

---

## useReducer

```ts
const [state, dispatch] = useReducer(reducer, initialState);
```

Con `useReducer` riceviamo:

1. il valore dello state
2. una funzione chiamata `dispatch`

La funzione `dispatch` serve per inviare un'azione.

---

# 8. Sintassi di useReducer

```ts
const [state, dispatch] = useReducer(reducer, initialState);
```

`useReducer()` prende due argomenti:

1. una funzione `reducer`
2. uno stato iniziale, cioè `initialState`

---

# 9. Cosa restituisce useReducer?

`useReducer` restituisce un array con due elementi:

```ts
[state, dispatch]
```

Dove:

- `state` è lo stato attuale
- `dispatch` è la funzione che usiamo per inviare azioni

---

# 10. Differenza principale tra useState e useReducer

## Con useState

Con `useState` aggiorniamo lo stato direttamente usando `setState`.

Esempio:

```ts
setCount(count + 1);
```

Oppure:

```ts
setMenuOpen(true);
```

---

## Con useReducer

Con `useReducer` non aggiorniamo lo stato direttamente.

Invece inviamo una **azione** usando `dispatch`.

Esempio:

```ts
dispatch({ type: "clear_cart" });
```

La funzione `dispatch` manda questa azione alla funzione `reducer`.

Poi sarà la funzione `reducer` a decidere come aggiornare lo stato.

---

# 11. Cosa sono le Actions?

Le **actions** sono oggetti che descrivono cosa vogliamo fare.

In italiano possiamo chiamarle **azioni**.

Un'azione è un oggetto JavaScript.

Esempio:

```ts
{ type: "clear_cart" }
```

Questa azione significa:

> Voglio svuotare il carrello.

---

# 12. Azioni per un carrello

Se il nostro `useReducer` serve per gestire un carrello, potremmo avere azioni come:

- aggiungere un prodotto al carrello
- rimuovere un prodotto dal carrello
- svuotare il carrello

Esempi:

```ts
{ type: "clear_cart" }
```

```ts
{ type: "remove_product", productId: 1 }
```

```ts
{ 
  type: "add_product", 
  product: {
    id: 1,
    price: 42,
    name: "laptop"
  }
}
```

---

# 13. La proprietà type

Ogni action di solito ha una proprietà chiamata:

```ts
type
```

La proprietà `type` descrive il tipo di azione.

Esempi:

```ts
type: "clear_cart"
```

```ts
type: "remove_product"
```

```ts
type: "add_product"
```

Il reducer controllerà il valore di `type` per capire cosa deve fare.

---

# 14. Dispatch

`dispatch` è la funzione che invia una action al reducer.

Esempio:

```tsx
<button onClick={() => dispatch({ type: "clear_cart" })}>
  Svuota carrello
</button>
```

Quando l'utente clicca il bottone, viene inviata questa action:

```ts
{ type: "clear_cart" }
```

Il reducer riceve l'action e aggiorna lo stato.

---

# 15. Esempio con lista di prodotti

Immaginiamo di avere una lista di prodotti.

Per ogni prodotto vogliamo mostrare:

- nome
- descrizione
- prezzo
- bottone per aggiungerlo al carrello

Esempio:

```tsx
<div className="product-cards">
  {products.map((prod) => (
    <div className="product-card" key={prod.id}>
      <h2>{prod.name}</h2>
      <p>{prod.description}</p>
      <p>{prod.price}</p>

      <button onClick={() => dispatch({ type: "add_product", product: prod })}>
        In den Warenkorb
      </button>
    </div>
  ))}
</div>
```

In italiano il testo del bottone può essere:

```tsx
<button onClick={() => dispatch({ type: "add_product", product: prod })}>
  Aggiungi al carrello
</button>
```

Quando clicchiamo il bottone, inviamo questa action:

```ts
{
  type: "add_product",
  product: prod
}
```

---

# 16. Cosa fa la funzione reducer?

La funzione `reducer` è il cuore di `useReducer`.

È una funzione che riceve:

1. lo stato attuale
2. l'action inviata con `dispatch`

Poi restituisce il nuovo stato.

Sintassi generale:

```ts
function reducer(state, action) {
  // controlla il tipo di action
  // restituisce un nuovo state
}
```

---

# 17. Regola importante del reducer

Il reducer non deve modificare direttamente lo stato originale.

Deve creare e restituire un nuovo stato.

In React è importante non modificare direttamente lo state.

Quindi non facciamo questo:

```ts
state.products.push(action.product);
return state;
```

Questo è sbagliato perché modifica direttamente l'array originale.

Facciamo invece:

```ts
return {
  ...state,
  products: [...state.products, action.product]
};
```

Questo crea un nuovo oggetto state e un nuovo array products.

---

# 18. Esempio completo di reducer per un carrello

```ts
function reducer(state, action) {
  if (action.type === "add_product") {
    return {
      ...state,
      products: [...state.products, action.product]
    };
  }

  if (action.type === "remove_product") {
    return {
      ...state,
      products: state.products.filter(
        (product) => product.id !== action.productId
      )
    };
  }

  if (action.type === "clear_cart") {
    return {
      ...state,
      products: []
    };
  }

  console.log("Azione sconosciuta", action);
  return state;
}
```

---

# 19. Spiegazione del reducer riga per riga

## Aggiungere un prodotto

```ts
if (action.type === "add_product") {
```

Qui controlliamo se l'azione è di tipo `"add_product"`.

Se è così, vogliamo aggiungere un prodotto al carrello.

```ts
return {
  ...state,
  products: [...state.products, action.product]
};
```

Con `...state` copiamo tutto lo stato precedente.

Con:

```ts
products: [...state.products, action.product]
```

creiamo un nuovo array che contiene:

- tutti i prodotti precedenti
- il nuovo prodotto

---

## Rimuovere un prodotto

```ts
if (action.type === "remove_product") {
```

Qui controlliamo se l'azione è di tipo `"remove_product"`.

```ts
products: state.products.filter(
  (product) => product.id !== action.productId
)
```

Con `filter()` creiamo un nuovo array eliminando il prodotto con quell'id.

Se `product.id` è diverso da `action.productId`, il prodotto resta nel carrello.

Se è uguale, viene rimosso.

---

## Svuotare il carrello

```ts
if (action.type === "clear_cart") {
```

Qui controlliamo se l'azione è di tipo `"clear_cart"`.

```ts
return {
  ...state,
  products: []
};
```

Restituiamo un nuovo stato dove `products` è un array vuoto.

---

## Azione sconosciuta

```ts
console.log("Azione sconosciuta", action);
return state;
```

Se l'action non corrisponde a nessun tipo conosciuto, stampiamo un messaggio in console e restituiamo lo stato attuale senza modificarlo.

---

# 20. Stato iniziale del carrello

Prima di usare il reducer, ci serve uno stato iniziale.

Esempio:

```ts
const initialState = {
  products: []
};
```

Questo significa che all'inizio il carrello è vuoto.

Poi possiamo usare `useReducer`:

```ts
const [cart, dispatch] = useReducer(reducer, initialState);
```

Qui:

- `cart` è lo stato attuale del carrello
- `dispatch` è la funzione per inviare azioni
- `reducer` è la funzione che aggiorna lo stato
- `initialState` è lo stato iniziale

---

# 21. Esempio completo con useReducer

```tsx
import { useReducer } from "react";

const initialState = {
  products: []
};

function reducer(state, action) {
  if (action.type === "add_product") {
    return {
      ...state,
      products: [...state.products, action.product]
    };
  }

  if (action.type === "remove_product") {
    return {
      ...state,
      products: state.products.filter(
        (product) => product.id !== action.productId
      )
    };
  }

  if (action.type === "clear_cart") {
    return {
      ...state,
      products: []
    };
  }

  console.log("Azione sconosciuta", action);
  return state;
}

function CartExample() {
  const [cart, dispatch] = useReducer(reducer, initialState);

  const product = {
    id: 1,
    name: "Laptop",
    price: 42
  };

  return (
    <div>
      <h1>Carrello</h1>

      <button onClick={() => dispatch({ type: "add_product", product })}>
        Aggiungi prodotto
      </button>

      <button onClick={() => dispatch({ type: "remove_product", productId: 1 })}>
        Rimuovi prodotto
      </button>

      <button onClick={() => dispatch({ type: "clear_cart" })}>
        Svuota carrello
      </button>

      <p>Prodotti nel carrello: {cart.products.length}</p>
    </div>
  );
}

export default CartExample;
```

---

# 22. useReducer con TypeScript

Con TypeScript possiamo tipizzare:

- il prodotto
- lo stato del carrello
- le azioni
- la funzione reducer

---

## Tipo Product

```ts
type Product = {
  id: number;
  name: string;
  price: number;
};
```

---

## Tipo dello State

```ts
type CartState = {
  products: Product[];
};
```

Questo significa che lo state del carrello contiene una proprietà `products`, che è un array di prodotti.

---

## Tipo delle Actions

```ts
type CartAction =
  | { type: "add_product"; product: Product }
  | { type: "remove_product"; productId: number }
  | { type: "clear_cart" };
```

Qui usiamo un Union Type.

Questo significa che `CartAction` può essere una di queste tre azioni:

1. aggiungere un prodotto
2. rimuovere un prodotto
3. svuotare il carrello

---

# 23. Reducer tipizzato con TypeScript

```ts
function reducer(state: CartState, action: CartAction): CartState {
  if (action.type === "add_product") {
    return {
      ...state,
      products: [...state.products, action.product]
    };
  }

  if (action.type === "remove_product") {
    return {
      ...state,
      products: state.products.filter(
        (product) => product.id !== action.productId
      )
    };
  }

  if (action.type === "clear_cart") {
    return {
      ...state,
      products: []
    };
  }

  return state;
}
```

In questo modo TypeScript sa:

- che `state` deve essere di tipo `CartState`
- che `action` deve essere di tipo `CartAction`
- che la funzione deve restituire un nuovo `CartState`

---

# 24. Esempio completo tipizzato

```tsx
import { useReducer } from "react";

type Product = {
  id: number;
  name: string;
  price: number;
};

type CartState = {
  products: Product[];
};

type CartAction =
  | { type: "add_product"; product: Product }
  | { type: "remove_product"; productId: number }
  | { type: "clear_cart" };

const initialState: CartState = {
  products: []
};

function reducer(state: CartState, action: CartAction): CartState {
  if (action.type === "add_product") {
    return {
      ...state,
      products: [...state.products, action.product]
    };
  }

  if (action.type === "remove_product") {
    return {
      ...state,
      products: state.products.filter(
        (product) => product.id !== action.productId
      )
    };
  }

  if (action.type === "clear_cart") {
    return {
      ...state,
      products: []
    };
  }

  return state;
}

function CartExample() {
  const [cart, dispatch] = useReducer(reducer, initialState);

  const product: Product = {
    id: 1,
    name: "Laptop",
    price: 42
  };

  return (
    <div>
      <h1>Carrello</h1>

      <button onClick={() => dispatch({ type: "add_product", product })}>
        Aggiungi prodotto
      </button>

      <button onClick={() => dispatch({ type: "remove_product", productId: 1 })}>
        Rimuovi prodotto
      </button>

      <button onClick={() => dispatch({ type: "clear_cart" })}>
        Svuota carrello
      </button>

      <p>Prodotti nel carrello: {cart.products.length}</p>
    </div>
  );
}

export default CartExample;
```

---

# 25. Perché useReducer è utile?

`useReducer` è utile perché rende più ordinata la logica di aggiornamento dello stato.

Invece di avere tante funzioni separate e tanti `setState`, possiamo concentrare la logica in una sola funzione reducer.

Questo è utile quando ci sono molte azioni possibili.

---

# 26. Quando usare useState e quando usare useReducer?

## Usa useState quando:

- lo stato è semplice
- hai una singola variabile
- la logica di aggiornamento è breve
- il componente è piccolo

Esempi:

```ts
const [isOpen, setIsOpen] = useState(false);
```

```ts
const [name, setName] = useState("");
```

```ts
const [count, setCount] = useState(0);
```

---

## Usa useReducer quando:

- lo stato è complesso
- ci sono tante azioni diverse
- lo stato è un oggetto con più proprietà
- vuoi separare la logica di aggiornamento dal JSX
- vuoi rendere il codice più organizzato

Esempi:

- carrello
- form complesso
- profilo utente
- dashboard
- editor con molte impostazioni

---

# 27. Schema mentale

```txt
Utente clicca un bottone
        ↓
dispatch(action)
        ↓
reducer(state, action)
        ↓
nuovo state
        ↓
React aggiorna la UI
```

---

# 28. Schema veloce da ricordare

## useState

```ts
const [state, setState] = useState(initialState);
```

Con `useState` aggiorni direttamente lo stato:

```ts
setState(newValue);
```

---

## useReducer

```ts
const [state, dispatch] = useReducer(reducer, initialState);
```

Con `useReducer` invii un'azione:

```ts
dispatch({ type: "clear_cart" });
```

Il reducer riceve:

```ts
function reducer(state, action) {
  return newState;
}
```

---

# 29. Parole chiave da memorizzare

## State

I dati che cambiano nel componente.

## Reducer

Funzione che riceve stato e azione, poi restituisce un nuovo stato.

## Action

Oggetto che descrive cosa vogliamo fare.

## Dispatch

Funzione che invia un'action al reducer.

## Initial State

Lo stato iniziale.

---

# 30. Riassunto finale

In questa lezione abbiamo imparato una nuova strategia per gestire lo state in React: `useReducer`.

`useReducer` è un'alternativa a `useState`, ma non lo sostituisce completamente.

`useState` è perfetto per stati semplici.

`useReducer` è più adatto quando abbiamo stati complessi e tante azioni possibili.

Abbiamo visto che:

```ts
const [state, dispatch] = useReducer(reducer, initialState);
```

`useReducer` riceve:

- una funzione reducer
- uno stato iniziale

E restituisce:

- lo state attuale
- una funzione dispatch

Con `dispatch` inviamo delle azioni.

Il reducer riceve l'azione, controlla il suo `type` e restituisce il nuovo stato.

Il concetto più importante da ricordare è:

> Con `useState` aggiorniamo direttamente lo stato.  
> Con `useReducer` inviamo un'azione e il reducer decide come aggiornare lo stato.

