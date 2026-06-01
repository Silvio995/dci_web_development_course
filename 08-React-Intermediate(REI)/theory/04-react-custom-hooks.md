# React: Custom Hooks

## 1. Introduzione

In questa lezione vediamo cosa sono i **Custom Hooks** in React.

Un Custom Hook è una funzione personalizzata che ci permette di riutilizzare logica React in più componenti.

In pratica, invece di ripetere lo stesso codice in tanti componenti, possiamo spostarlo dentro una funzione speciale.

Questa funzione può usare altri Hook di React, come:

- `useState`
- `useEffect`
- `useReducer`
- `useContext`

---

# 2. Cos'è un Hook?

Un Hook è una funzione speciale di React.

Gli Hook ci permettono di usare funzionalità di React dentro i componenti funzionali.

Esempi di Hook già pronti:

```tsx
useState()
```

```tsx
useEffect()
```

```tsx
useReducer()
```

```tsx
useContext()
```

---

# 3. Cos'è un Custom Hook?

Un **Custom Hook** è un Hook creato da noi.

Serve per racchiudere una logica che vogliamo riutilizzare.

Esempio:

```tsx
function useCart() {
  // logica del carrello
}
```

Il nome di un Custom Hook deve iniziare sempre con:

```tsx
use
```

Esempi corretti:

```tsx
useCart()
```

```tsx
useUser()
```

```tsx
useTheme()
```

```tsx
useLocalStorage()
```

Esempi sbagliati:

```tsx
cart()
```

```tsx
getUser()
```

```tsx
themeHook()
```

---

# 4. Regola importante: il nome deve iniziare con use

React riconosce un Custom Hook dal fatto che il nome inizia con `use`.

Questa regola è importante perché React deve sapere che quella funzione può usare altri Hook.

Esempio corretto:

```tsx
function useCounter() {
  const [count, setCount] = useState(0);

  return { count, setCount };
}
```

Esempio sbagliato:

```tsx
function counter() {
  const [count, setCount] = useState(0);

  return { count, setCount };
}
```

Anche se il codice può sembrare simile, il secondo esempio non segue le regole di React.

---

# 5. Perché usare i Custom Hooks?

I Custom Hooks servono per:

- evitare codice ripetuto
- rendere i componenti più puliti
- separare la logica dalla UI
- riutilizzare la stessa logica in più componenti
- organizzare meglio il codice
- semplificare l'uso di Context e Reducer

---

# 6. Problema: codice ripetuto nei componenti

Immaginiamo di avere più componenti che devono usare lo stesso Context.

Esempio:

```tsx
const context = useContext(CartContext);

if (!context) {
  throw new Error("CartContext non trovato");
}

const { state, dispatch } = context;
```

Se dobbiamo scrivere questo codice in tanti componenti, diventa ripetitivo.

Con un Custom Hook possiamo scriverlo una sola volta.

---

# 7. Custom Hook con useContext

Nel caso del carrello, possiamo creare un Custom Hook chiamato:

```tsx
useCart()
```

Questo Hook userà `useContext` internamente.

Esempio:

```tsx
function useCart() {
  const context = useContext(CartContext);

  if (!context) {
    throw new Error("useCart deve essere usato dentro CartProvider");
  }

  return context;
}
```

Ora nei componenti possiamo scrivere semplicemente:

```tsx
const { state, dispatch } = useCart();
```

Invece di ripetere ogni volta `useContext` e il controllo su `null`.

---

# 8. Perché controlliamo il context?

Quando creiamo un Context con TypeScript, spesso scriviamo:

```tsx
const CartContext = createContext<CartContextValue | null>(null);
```

Questo significa che il Context può essere:

- un valore valido
- oppure `null`

Il valore sarà `null` se proviamo a usare il Context fuori dal suo Provider.

Per questo nel Custom Hook facciamo un controllo:

```tsx
if (!context) {
  throw new Error("useCart deve essere usato dentro CartProvider");
}
```

Questo ci aiuta a trovare subito l'errore.

---

# 9. Esempio completo: Context + Custom Hook

```tsx
import { createContext, useContext, useReducer } from "react";

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

type CartContextValue = {
  state: CartState;
  dispatch: React.Dispatch<CartAction>;
};

const initialState: CartState = {
  products: []
};

const CartContext = createContext<CartContextValue | null>(null);

function cartReducer(state: CartState, action: CartAction): CartState {
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

export function CartProvider({ children }: { children: React.ReactNode }) {
  const [state, dispatch] = useReducer(cartReducer, initialState);

  return (
    <CartContext.Provider value={{ state, dispatch }}>
      {children}
    </CartContext.Provider>
  );
}

export function useCart() {
  const context = useContext(CartContext);

  if (!context) {
    throw new Error("useCart deve essere usato dentro CartProvider");
  }

  return context;
}
```

---

# 10. Usare il Custom Hook nei componenti

Dopo aver creato `useCart`, possiamo usarlo nei componenti.

Esempio:

```tsx
function Header() {
  const { state } = useCart();

  return (
    <header>
      <h1>Shop</h1>
      <p>Prodotti nel carrello: {state.products.length}</p>
    </header>
  );
}
```

Qui `Header` legge lo stato del carrello senza ricevere props.

---

# 11. Usare dispatch con il Custom Hook

Esempio:

```tsx
function ClearCartButton() {
  const { dispatch } = useCart();

  return (
    <button onClick={() => dispatch({ type: "clear_cart" })}>
      Svuota carrello
    </button>
  );
}
```

Il componente usa `dispatch` tramite il Custom Hook.

Non deve sapere direttamente come è stato creato il Context.

---

# 12. Custom Hook per separare ancora meglio la logica

Possiamo anche creare funzioni più leggibili dentro il Custom Hook.

Invece di far usare sempre `dispatch` direttamente ai componenti, possiamo creare funzioni come:

- `addProduct`
- `removeProduct`
- `clearCart`

Esempio:

```tsx
export function useCart() {
  const context = useContext(CartContext);

  if (!context) {
    throw new Error("useCart deve essere usato dentro CartProvider");
  }

  const { state, dispatch } = context;

  function addProduct(product: Product) {
    dispatch({ type: "add_product", product });
  }

  function removeProduct(productId: number) {
    dispatch({ type: "remove_product", productId });
  }

  function clearCart() {
    dispatch({ type: "clear_cart" });
  }

  return {
    state,
    dispatch,
    addProduct,
    removeProduct,
    clearCart
  };
}
```

Ora nei componenti possiamo scrivere:

```tsx
const { addProduct } = useCart();
```

e poi:

```tsx
<button onClick={() => addProduct(product)}>
  Aggiungi al carrello
</button>
```

Questo rende il componente più leggibile.

---

# 13. Esempio con funzioni già pronte

```tsx
function ProductCard({ product }: { product: Product }) {
  const { addProduct } = useCart();

  return (
    <div>
      <h3>{product.name}</h3>
      <p>{product.price} €</p>

      <button onClick={() => addProduct(product)}>
        Aggiungi al carrello
      </button>
    </div>
  );
}
```

Il componente non deve più scrivere:

```tsx
dispatch({ type: "add_product", product });
```

Scrive solo:

```tsx
addProduct(product);
```

Questa è una forma più pulita.

---

# 14. Custom Hook con useState

Un Custom Hook non serve solo per Context.

Possiamo creare Custom Hooks anche con `useState`.

Esempio semplice: un contatore.

```tsx
import { useState } from "react";

function useCounter() {
  const [count, setCount] = useState(0);

  function increment() {
    setCount((prev) => prev + 1);
  }

  function decrement() {
    setCount((prev) => prev - 1);
  }

  function reset() {
    setCount(0);
  }

  return {
    count,
    increment,
    decrement,
    reset
  };
}
```

Ora possiamo usare questo Hook in un componente.

```tsx
function Counter() {
  const { count, increment, decrement, reset } = useCounter();

  return (
    <div>
      <p>Count: {count}</p>

      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

---

# 15. Custom Hook con useEffect

Possiamo creare un Custom Hook anche per logiche legate a `useEffect`.

Esempio: leggere la larghezza della finestra.

```tsx
import { useEffect, useState } from "react";

function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    function handleResize() {
      setWidth(window.innerWidth);
    }

    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return width;
}
```

Uso:

```tsx
function App() {
  const width = useWindowWidth();

  return <p>Larghezza finestra: {width}</p>;
}
```

---

# 16. Custom Hook con localStorage

Un esempio molto utile è un Custom Hook per salvare dati nel `localStorage`.

```tsx
import { useState } from "react";

function useLocalStorage(key: string, initialValue: string) {
  const [value, setValue] = useState(() => {
    const savedValue = localStorage.getItem(key);

    if (savedValue !== null) {
      return savedValue;
    }

    return initialValue;
  });

  function updateValue(newValue: string) {
    setValue(newValue);
    localStorage.setItem(key, newValue);
  }

  return [value, updateValue] as const;
}
```

Uso:

```tsx
function Editor() {
  const [html, setHtml] = useLocalStorage("html", "");

  return (
    <textarea
      value={html}
      onChange={(event) => setHtml(event.target.value)}
    />
  );
}
```

---

# 17. Perché `as const`?

Nel Custom Hook:

```tsx
return [value, updateValue] as const;
```

`as const` aiuta TypeScript a capire che stiamo restituendo una tupla, cioè un array con una struttura precisa.

Senza `as const`, TypeScript potrebbe interpretare il ritorno in modo troppo generico.

Con `as const`, TypeScript capisce meglio che:

- il primo valore è `value`
- il secondo valore è `updateValue`

È simile al ritorno di `useState`.

---

# 18. Regole dei Custom Hooks

I Custom Hooks devono rispettare le stesse regole degli Hook di React.

## Regola 1

Devono essere chiamati solo dentro:

- componenti React
- altri Custom Hooks

Non vanno chiamati dentro funzioni normali qualsiasi.

---

## Regola 2

Devono essere chiamati sempre nello stesso ordine.

Non vanno messi dentro:

- `if`
- `for`
- `while`
- funzioni annidate

Esempio sbagliato:

```tsx
if (isLoggedIn) {
  const { state } = useCart();
}
```

Esempio corretto:

```tsx
const { state } = useCart();

if (!isLoggedIn) {
  return null;
}
```

---

# 19. Dove mettere i Custom Hooks nel progetto?

Di solito i Custom Hooks si mettono in una cartella dedicata.

Esempio:

```txt
src/
  hooks/
    useCart.ts
    useCounter.ts
    useLocalStorage.ts
```

Oppure, se il Custom Hook è legato a un Context specifico:

```txt
src/
  contexts/
    CartContext.tsx
```

Dentro `CartContext.tsx` possiamo mettere:

- Context
- Provider
- reducer
- Custom Hook `useCart`

---

# 20. Vantaggi dei Custom Hooks

I Custom Hooks rendono il codice:

- più ordinato
- più leggibile
- più riutilizzabile
- più facile da testare
- più facile da mantenere

Inoltre aiutano a separare:

- la logica
- dalla parte visiva del componente

---

# 21. Prima e dopo

## Senza Custom Hook

```tsx
function Cart() {
  const context = useContext(CartContext);

  if (!context) {
    throw new Error("CartContext non trovato");
  }

  const { state, dispatch } = context;

  return (
    <button onClick={() => dispatch({ type: "clear_cart" })}>
      Svuota carrello
    </button>
  );
}
```

---

## Con Custom Hook

```tsx
function Cart() {
  const { clearCart } = useCart();

  return (
    <button onClick={clearCart}>
      Svuota carrello
    </button>
  );
}
```

Il secondo codice è molto più pulito.

---

# 22. Custom Hook non è uno store globale

Un Custom Hook da solo non crea automaticamente uno stato globale.

Esempio:

```tsx
function useCounter() {
  const [count, setCount] = useState(0);

  return { count, setCount };
}
```

Se usiamo `useCounter()` in due componenti diversi, ogni componente avrà il suo stato separato.

Per condividere uno stato globale, dobbiamo usare anche Context, oppure una libreria esterna.

---

# 23. Custom Hook + Context = stato condiviso

Quando il Custom Hook usa `useContext`, allora può leggere uno stato condiviso.

Esempio:

```tsx
function useCart() {
  const context = useContext(CartContext);
  return context;
}
```

In questo caso tutti i componenti che usano `useCart()` leggono lo stesso Context.

---

# 24. Schema mentale

```txt
Custom Hook
= funzione riutilizzabile
= contiene logica React
= può usare altri Hook
= deve iniziare con use
```

---

# 25. Schema con Context

```txt
CartProvider
crea lo state
        ↓
CartContext
contiene state e dispatch
        ↓
useCart
legge il Context
        ↓
Componenti
usano useCart senza ripetere codice
```

---

# 26. Schema veloce da copiare

```tsx
function useSomething() {
  const context = useContext(SomeContext);

  if (!context) {
    throw new Error("useSomething deve essere usato dentro SomeProvider");
  }

  return context;
}
```

Uso:

```tsx
function Component() {
  const value = useSomething();

  return <div>{/* JSX */}</div>;
}
```

---

# 27. Frasi importanti da ricordare

Un Custom Hook è una funzione personalizzata che usa Hook di React.

Il nome deve iniziare con `use`.

Serve per riutilizzare logica tra componenti.

Un Custom Hook può usare `useContext` per semplificare l'accesso a un Context.

Un Custom Hook può restituire dati, funzioni o entrambi.

---

# 28. Quando creare un Custom Hook?

Crea un Custom Hook quando:

- ripeti la stessa logica in più componenti
- vuoi rendere un componente più pulito
- vuoi separare logica e UI
- usi spesso lo stesso Context
- vuoi creare funzioni più leggibili per modificare lo state

---

# 29. Quando non serve un Custom Hook?

Non serve creare un Custom Hook per logiche molto piccole usate una sola volta.

Esempio:

```tsx
const [isOpen, setIsOpen] = useState(false);
```

Se questo stato serve solo a un componente, non è necessario spostarlo in un Custom Hook.

---

# 30. Riassunto finale

In questa lezione abbiamo imparato cosa sono i Custom Hooks.

Un Custom Hook è una funzione creata da noi che permette di riutilizzare logica React.

Il suo nome deve sempre iniziare con `use`.

Abbiamo visto che un Custom Hook può usare altri Hook, come:

- `useState`
- `useEffect`
- `useReducer`
- `useContext`

Nel caso di `useReducer + useContext`, un Custom Hook è molto utile perché ci permette di semplificare l'accesso al Context.

Invece di scrivere ogni volta:

```tsx
const context = useContext(CartContext);
```

possiamo scrivere:

```tsx
const { state, dispatch } = useCart();
```

Oppure, se prepariamo funzioni specifiche:

```tsx
const { addProduct, removeProduct, clearCart } = useCart();
```

Il concetto più importante da ricordare è:

> I Custom Hooks servono per riutilizzare logica e rendere i componenti più puliti.
