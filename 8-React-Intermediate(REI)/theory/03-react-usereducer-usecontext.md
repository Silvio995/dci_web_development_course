# React: combinare useReducer e useContext

## 1. Introduzione

In questa lezione vediamo come combinare due strumenti molto importanti di React:

- `useReducer`
- `useContext`

L'obiettivo è capire come creare uno **state condiviso** tra più componenti.

Questa combinazione è molto utile quando vogliamo gestire dati globali dell'applicazione, per esempio:

- carrello della spesa
- utente loggato
- tema chiaro/scuro
- lingua selezionata
- notifiche globali
- dati condivisi tra molte pagine o componenti

---

# 2. Ripasso veloce: useReducer

`useReducer` è un Hook di React usato per gestire stati complessi.

La sua sintassi è:

```tsx
const [state, dispatch] = useReducer(reducer, initialState);
```

`useReducer` riceve:

1. una funzione `reducer`
2. uno stato iniziale, cioè `initialState`

E restituisce:

1. lo stato attuale
2. la funzione `dispatch`

---

## 2.1 A cosa serve dispatch?

`dispatch` serve per inviare una **azione** al reducer.

Esempio:

```tsx
dispatch({ type: "clear_cart" });
```

Questa action dice al reducer cosa vogliamo fare.

Il reducer riceve lo state attuale e l'action, poi restituisce il nuovo state.

---

# 3. Ripasso veloce: Context API

La **React Context API** serve per condividere dati tra componenti senza dover passare props manualmente da un componente all'altro.

Normalmente, per passare dati da un componente padre a un componente figlio, usiamo le props.

Esempio:

```tsx
<App>
  <Header user={user} />
</App>
```

Ma se tanti componenti hanno bisogno dello stesso dato, passare props può diventare scomodo.

Questo problema si chiama spesso:

```txt
prop drilling
```

---

# 4. Cos'è il prop drilling?

Il **prop drilling** succede quando dobbiamo passare una prop attraverso tanti componenti intermedi solo per farla arrivare a un componente più in basso.

Esempio:

```tsx
<App user={user}>
  <Layout user={user}>
    <Header user={user}>
      <UserMenu user={user} />
    </Header>
  </Layout>
</App>
```

Il problema è che magari `Layout` e `Header` non usano davvero `user`, ma devono comunque riceverlo e passarlo avanti.

Con la Context API possiamo evitare questo problema.

---

# 5. A cosa serve la Context API?

La Context API viene spesso usata come una memoria globale per alcuni dati dell'applicazione.

Permette di rendere un valore disponibile in tutto il componente tree.

In altre parole:

> Con Context possiamo fornire un valore una volta sola e poi leggerlo da qualsiasi componente interno.

---

# 6. Cosa può contenere un Context?

Un Context può contenere qualsiasi tipo di valore.

Può contenere:

- una stringa
- un numero
- un booleano
- un oggetto
- un array
- una funzione
- uno state
- una funzione `dispatch`

Esempi:

```tsx
const ThemeContext = createContext("light");
```

Oppure:

```tsx
const UserContext = createContext(null);
```

Oppure:

```tsx
const CartContext = createContext(null);
```

---

# 7. Come funziona un Context?

Per usare un Context servono di solito tre passaggi:

1. creare il Context
2. fornire un valore con il Provider
3. consumare il valore con `useContext`

---

# 8. Creare un Context

Per creare un Context usiamo `createContext`.

Esempio:

```tsx
import { createContext } from "react";

const CartContext = createContext(null);
```

Qui abbiamo creato un Context chiamato `CartContext`.

---

# 9. Fornire un valore con Provider

Ogni Context ha un componente speciale chiamato `Provider`.

Il `Provider` serve per rendere disponibile un valore ai componenti figli.

Esempio:

```tsx
<CartContext.Provider value={cart}>
  <App />
</CartContext.Provider>
```

Tutti i componenti dentro `<App />` potranno accedere al valore `cart`.

---

# 10. Consumare un valore con useContext

Per leggere il valore di un Context usiamo `useContext`.

Esempio:

```tsx
import { useContext } from "react";

const cart = useContext(CartContext);
```

In questo modo il componente può accedere al valore del Context.

---

# 11. Perché combinare useReducer e useContext?

`useReducer` è utile per gestire uno stato complesso.

`useContext` è utile per rendere uno stato disponibile a molti componenti.

Combinandoli possiamo creare uno stato condiviso e gestito in modo ordinato.

In pratica:

- `useReducer` gestisce lo stato e la logica di aggiornamento
- `useContext` permette ai componenti di accedere allo stato
- `dispatch` permette ai componenti di inviare azioni per aggiornare lo stato

---

# 12. Idea principale

Quando combiniamo `useReducer` e `useContext`, il valore del Context di solito contiene due cose:

1. lo stato
2. la funzione `dispatch`

Esempio concettuale:

```tsx
const value = {
  state,
  dispatch
};
```

Poi questo valore viene passato al Provider:

```tsx
<CartContext.Provider value={{ state, dispatch }}>
  {children}
</CartContext.Provider>
```

---

# 13. Cosa possono fare i componenti?

Quando un componente usa questo Context, può fare due cose:

1. leggere lo stato
2. inviare azioni con `dispatch`

Esempio:

```tsx
const { state, dispatch } = useContext(CartContext);
```

Poi può leggere lo stato:

```tsx
state.products
```

E può modificarlo inviando azioni:

```tsx
dispatch({ type: "clear_cart" });
```

---

# 14. Esempio pratico: CartContext

Immaginiamo di voler creare un Context per gestire un carrello.

Il carrello deve permettere di:

- aggiungere prodotti
- rimuovere prodotti
- svuotare il carrello
- leggere il numero di prodotti presenti

---

# 15. Definire i tipi TypeScript

Prima definiamo il tipo `Product`.

```tsx
type Product = {
  id: number;
  name: string;
  price: number;
};
```

Poi definiamo lo state del carrello:

```tsx
type CartState = {
  products: Product[];
};
```

Lo state contiene un array di prodotti.

---

# 16. Definire le azioni

Ora definiamo le possibili action.

```tsx
type CartAction =
  | { type: "add_product"; product: Product }
  | { type: "remove_product"; productId: number }
  | { type: "clear_cart" };
```

Questo è un Union Type.

Significa che `CartAction` può essere una di queste tre azioni.

---

# 17. Stato iniziale

Definiamo lo stato iniziale del carrello.

```tsx
const initialState: CartState = {
  products: []
};
```

All'inizio il carrello è vuoto.

---

# 18. Reducer del carrello

Ora creiamo la funzione reducer.

```tsx
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
```

Il reducer riceve:

- lo state attuale
- l'action

Poi restituisce il nuovo state.

---

# 19. Creare il tipo del Context

Il Context deve contenere:

- `state`
- `dispatch`

Quindi possiamo creare un tipo così:

```tsx
type CartContextValue = {
  state: CartState;
  dispatch: React.Dispatch<CartAction>;
};
```

Qui:

```tsx
React.Dispatch<CartAction>
```

significa che `dispatch` è una funzione che accetta solo azioni di tipo `CartAction`.

---

# 20. Creare il Context

Ora possiamo creare il Context.

```tsx
import { createContext } from "react";

const CartContext = createContext<CartContextValue | null>(null);
```

Usiamo:

```tsx
CartContextValue | null
```

perché all'inizio il Context non ha ancora un valore reale.

---

# 21. Creare il Provider

Il Provider è un componente che contiene `useReducer`.

```tsx
import { createContext, useReducer } from "react";

function CartProvider({ children }: { children: React.ReactNode }) {
  const [state, dispatch] = useReducer(cartReducer, initialState);

  return (
    <CartContext.Provider value={{ state, dispatch }}>
      {children}
    </CartContext.Provider>
  );
}
```

Dentro `CartProvider` usiamo:

```tsx
const [state, dispatch] = useReducer(cartReducer, initialState);
```

Poi passiamo `state` e `dispatch` al Provider:

```tsx
value={{ state, dispatch }}
```

In questo modo tutti i componenti figli potranno accedere al carrello.

---

# 22. Usare il Provider nell'app

Per rendere il carrello disponibile in tutta l'app, avvolgiamo l'app con `CartProvider`.

Esempio:

```tsx
function App() {
  return (
    <CartProvider>
      <Header />
      <ProductList />
      <Cart />
    </CartProvider>
  );
}
```

Ora `Header`, `ProductList` e `Cart` possono usare il Context.

---

# 23. Consumare il Context con useContext

Per leggere il Context usiamo `useContext`.

Esempio:

```tsx
import { useContext } from "react";

function Cart() {
  const context = useContext(CartContext);

  if (!context) {
    return null;
  }

  const { state, dispatch } = context;

  return (
    <div>
      <h2>Carrello</h2>
      <p>Prodotti: {state.products.length}</p>

      <button onClick={() => dispatch({ type: "clear_cart" })}>
        Svuota carrello
      </button>
    </div>
  );
}
```

---

# 24. Perché controlliamo if (!context)?

Abbiamo creato il Context così:

```tsx
const CartContext = createContext<CartContextValue | null>(null);
```

Questo significa che il Context può essere:

- un valore valido
- oppure `null`

Quindi TypeScript ci chiede di controllare che non sia `null` prima di usarlo.

```tsx
if (!context) {
  return null;
}
```

Dopo questo controllo, TypeScript sa che `context` non è più `null`.

---

# 25. Custom Hook per semplificare useContext

Spesso si crea un custom hook per usare il Context in modo più pulito.

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

Così nei componenti non dobbiamo ogni volta scrivere il controllo.

Possiamo fare semplicemente:

```tsx
const { state, dispatch } = useCart();
```

---

# 26. Esempio con ProductList

```tsx
function ProductList() {
  const { dispatch } = useCart();

  const products: Product[] = [
    { id: 1, name: "Laptop", price: 999 },
    { id: 2, name: "Mouse", price: 25 },
    { id: 3, name: "Keyboard", price: 80 }
  ];

  return (
    <div>
      <h2>Prodotti</h2>

      {products.map((product) => (
        <div key={product.id}>
          <h3>{product.name}</h3>
          <p>Prezzo: {product.price} €</p>

          <button
            onClick={() =>
              dispatch({ type: "add_product", product: product })
            }
          >
            Aggiungi al carrello
          </button>
        </div>
      ))}
    </div>
  );
}
```

Qui `ProductList` non riceve props.

Accede direttamente alla funzione `dispatch` tramite il Context.

---

# 27. Esempio con Header

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

Anche `Header` può leggere il numero di prodotti nel carrello senza ricevere props.

---

# 28. Esempio completo

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

function CartProvider({ children }: { children: React.ReactNode }) {
  const [state, dispatch] = useReducer(cartReducer, initialState);

  return (
    <CartContext.Provider value={{ state, dispatch }}>
      {children}
    </CartContext.Provider>
  );
}

function useCart() {
  const context = useContext(CartContext);

  if (!context) {
    throw new Error("useCart deve essere usato dentro CartProvider");
  }

  return context;
}

function Header() {
  const { state } = useCart();

  return (
    <header>
      <h1>Shop</h1>
      <p>Prodotti nel carrello: {state.products.length}</p>
    </header>
  );
}

function ProductList() {
  const { dispatch } = useCart();

  const products: Product[] = [
    { id: 1, name: "Laptop", price: 999 },
    { id: 2, name: "Mouse", price: 25 },
    { id: 3, name: "Keyboard", price: 80 }
  ];

  return (
    <div>
      <h2>Prodotti</h2>

      {products.map((product) => (
        <div key={product.id}>
          <h3>{product.name}</h3>
          <p>Prezzo: {product.price} €</p>

          <button
            onClick={() =>
              dispatch({ type: "add_product", product: product })
            }
          >
            Aggiungi al carrello
          </button>
        </div>
      ))}
    </div>
  );
}

function Cart() {
  const { state, dispatch } = useCart();

  return (
    <div>
      <h2>Carrello</h2>

      {state.products.map((product) => (
        <div key={product.id}>
          <p>{product.name}</p>

          <button
            onClick={() =>
              dispatch({
                type: "remove_product",
                productId: product.id
              })
            }
          >
            Rimuovi
          </button>
        </div>
      ))}

      <button onClick={() => dispatch({ type: "clear_cart" })}>
        Svuota carrello
      </button>
    </div>
  );
}

function App() {
  return (
    <CartProvider>
      <Header />
      <ProductList />
      <Cart />
    </CartProvider>
  );
}

export default App;
```

---

# 29. Flusso completo

Il flusso funziona così:

```txt
1. CartProvider crea lo state con useReducer
2. CartProvider passa state e dispatch al Context
3. I componenti usano useContext per leggere il Context
4. I componenti possono leggere lo state
5. I componenti possono inviare action con dispatch
6. Il reducer riceve l'action
7. Il reducer crea un nuovo state
8. React aggiorna la UI
```

---

# 30. Schema mentale

```txt
useReducer
gestisce lo state complesso
        ↓
Context Provider
rende disponibili state e dispatch
        ↓
useContext
permette ai componenti di leggere state e dispatch
        ↓
Componenti
leggono dati e inviano azioni
```

---

# 31. Differenza tra useReducer da solo e useReducer + useContext

## Solo useReducer

Usiamo `useReducer` dentro un componente.

Lo state è disponibile principalmente in quel componente e nei figli tramite props.

```tsx
const [state, dispatch] = useReducer(reducer, initialState);
```

---

## useReducer + useContext

Usiamo `useReducer` dentro un Provider.

Poi rendiamo `state` e `dispatch` disponibili a tutto il sotto-albero dei componenti.

```tsx
<CartContext.Provider value={{ state, dispatch }}>
  {children}
</CartContext.Provider>
```

In questo modo evitiamo di passare props manualmente.

---

# 32. Quando usare useReducer + useContext?

Questa combinazione è utile quando:

- tanti componenti hanno bisogno dello stesso state
- lo state ha una logica complessa
- vogliamo evitare il prop drilling
- vogliamo centralizzare la logica di aggiornamento
- non vogliamo usare subito librerie esterne come Redux

Esempi pratici:

- carrello ecommerce
- login utente
- tema dell'app
- notifiche
- impostazioni globali
- dati condivisi tra molte pagine

---

# 33. Attenzione: non serve usarlo sempre

Non dobbiamo usare `useReducer + useContext` per ogni cosa.

Se lo state è semplice e serve solo a un componente, `useState` è abbastanza.

Esempio:

```tsx
const [isOpen, setIsOpen] = useState(false);
```

Qui non serve creare Context e Reducer.

---

# 34. Quando scegliere cosa?

## Usa useState

Quando lo state è semplice e locale.

Esempio:

- aprire/chiudere un menu
- salvare il valore di un input
- mostrare/nascondere un messaggio

---

## Usa useReducer

Quando lo state è complesso ma resta dentro un componente o pochi componenti vicini.

Esempio:

- form complesso
- editor
- carrello dentro una pagina

---

## Usa useReducer + useContext

Quando lo state è complesso e deve essere condiviso tra tanti componenti.

Esempio:

- carrello globale
- utente loggato
- tema dell'app
- dati condivisi in molte parti dell'app

---

# 35. Parole chiave da memorizzare

## Context API

Sistema di React per condividere valori tra componenti senza passare props manualmente.

## Provider

Componente che fornisce un valore al Context.

## useContext

Hook che permette a un componente di consumare il valore del Context.

## useReducer

Hook che gestisce state complessi tramite reducer e dispatch.

## reducer

Funzione che riceve state e action e restituisce un nuovo state.

## dispatch

Funzione che invia una action al reducer.

## action

Oggetto che descrive cosa vogliamo fare.

---

# 36. Schema veloce da copiare

```tsx
const Context = createContext(null);

function Provider({ children }) {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <Context.Provider value={{ state, dispatch }}>
      {children}
    </Context.Provider>
  );
}

function Component() {
  const { state, dispatch } = useContext(Context);

  return (
    <button onClick={() => dispatch({ type: "some_action" })}>
      Click
    </button>
  );
}
```

---

# 37. Riassunto finale

In questa lezione abbiamo visto come combinare `useReducer` e `useContext`.

`useReducer` ci aiuta a gestire uno stato complesso.

`useContext` ci aiuta a rendere quello stato disponibile a più componenti.

Quando li combiniamo, il Context di solito contiene:

- lo state
- la funzione dispatch

I componenti possono quindi:

- accedere allo state
- inviare azioni per modificarlo

Il concetto principale è:

> `useReducer` gestisce la logica dello stato.  
> `useContext` distribuisce quello stato ai componenti.

Questa combinazione è molto utile per creare una gestione dello stato semplice ma potente, senza usare subito librerie esterne come Redux.
