# React - Gestione dello stato e Context API

## 1. Che cos’è la gestione dello stato?

La **gestione dello stato** riguarda il modo in cui una applicazione salva, modifica e condivide i dati che possono cambiare nel tempo.

In React questi dati vengono spesso chiamati:

```txt
state
```

In italiano possiamo tradurlo come:

```txt
stato
```

Lo stato contiene informazioni modificabili dell’applicazione.

Esempi:

* il numero di un contatore;
* l’utente attualmente loggato;
* il contenuto di un carrello;
* il tema chiaro/scuro;
* la lingua selezionata;
* il valore scritto dentro un input.

---

# Ripasso: State

## 2. Che cos’era lo state?

Lo **state** è l’insieme delle variabili che contengono informazioni modificabili di una componente o di una applicazione.

Quando lo state cambia, React renderizza di nuovo la componente interessata.

Esempio:

```jsx
const [count, setCount] = useState(0);
```

In questo esempio:

* `count` è la variabile di stato;
* `setCount` è la funzione che modifica lo stato;
* `0` è il valore iniziale.

---

# Ripasso: Props

## 3. Che cosa sono le props?

Le **props** servono per passare dati da una componente a un’altra.

Di solito le props passano dati da una componente padre a una componente figlia.

Esempio:

```jsx
function Welcome({ name }) {
   return <p>Hello, {name}</p>;
}

function App() {
   return <Welcome name="Maria" />;
}
```

In questo esempio:

* `App` passa la prop `name` a `Welcome`;
* `Welcome` riceve `name` e la usa nel JSX.

---

# Stato locale

## 4. Che cos’è lo stato locale?

Lo **stato locale** riguarda dati specifici di una singola componente.

Questi dati vengono gestiti dentro quella componente.

Lo stato locale può anche essere passato ai componenti figli tramite props.

Lo stato locale viene modificato usando le funzioni setter di `useState`.

---

## 5. Esempio di stato locale

```jsx
import { useState } from "react";

const Counter = () => {
   const [count, setCount] = useState(0);

   return (
      <div>
         <p>Contatore: {count}</p>
         <button onClick={() => setCount(count + 1)}>
            Aggiungi uno
         </button>
      </div>
   );
};

export default Counter;
```

---

## 6. Spiegazione dell’esempio

```jsx
const [count, setCount] = useState(0);
```

Qui creiamo uno stato locale chiamato `count`.

Questo stato appartiene alla componente `Counter`.

---

```jsx
<button onClick={() => setCount(count + 1)}>
   Aggiungi uno
</button>
```

Quando l’utente clicca sul bottone, chiamiamo `setCount`.

`setCount` aggiorna lo stato.

Quando lo stato cambia, React renderizza di nuovo la componente e mostra il nuovo valore.

---

## 7. Quando usare lo stato locale?

Usiamo lo stato locale quando i dati servono solo a una componente o a pochi componenti figli vicini.

Esempi:

* apertura o chiusura di un menu;
* valore di un input;
* stato di un bottone;
* contatore;
* messaggio di errore dentro un form;
* tab selezionata in una componente.

---

# Stato globale

## 8. Che cos’è lo stato globale?

Lo **stato globale** contiene dati condivisi da molte componenti.

Si chiama globale perché normalmente deve essere accessibile da tante parti dell’applicazione.

Esempi di stato globale:

* utente attualmente loggato;
* carrello ecommerce;
* lingua selezionata;
* tema chiaro/scuro;
* dati di autenticazione;
* preferenze dell’utente.

---

## 9. Stato globale e variabili globali

Lo stato globale è simile all’idea di una variabile globale, ma gestito in modo più controllato.

In teoria potremmo rendere ogni variabile di stato globale.

Però non è una buona idea.

Se tutto diventa globale, l’applicazione diventa difficile da capire, modificare e mantenere.

---

## 10. Problema: passare dati dalla root a tutti i discendenti

Una possibile soluzione sarebbe tenere lo stato nella componente principale e passarlo a tutti i componenti figli tramite props.

Per esempio:

```txt
App -> ComponentA -> ComponentB -> ComponentC -> Target
```

Ma questo diventa complicato quando:

* abbiamo tante variabili globali;
* abbiamo molte componenti;
* le componenti sono profondamente annidate;
* alcune componenti intermedie non usano quei dati, ma devono comunque passarli avanti.

Questo problema si chiama **Prop Drilling**.

---

# Prop Drilling

## 11. Che cos’è il Prop Drilling?

Il **Prop Drilling** avviene quando passiamo dati tramite props attraverso tanti livelli di componenti, anche se molte componenti intermedie non usano quei dati.

In pratica:

* una componente in alto ha un dato;
* una componente molto in basso ha bisogno di quel dato;
* tutte le componenti in mezzo devono ricevere e ripassare la prop;
* anche se non la usano.

---

## 12. Perché il Prop Drilling è un problema?

Il Prop Drilling è problematico perché:

* rende il codice più difficile da leggere;
* richiede molta scrittura ripetitiva;
* rende le componenti intermedie dipendenti da dati che non usano;
* rende difficile modificare la struttura dell’app;
* aumenta il rischio di errori;
* rende il progetto meno manutenibile.

---

## 13. Esempio semplice di Prop Drilling

```jsx
function App() {
   const data = "something";

   return <MiddleComponentA data={data} />;
}

const MiddleComponentA = ({ data }) => {
   return <MiddleComponentB data={data} />;
};

const MiddleComponentB = ({ data }) => {
   return <Target data={data} />;
};

const Target = ({ data }) => {
   return <p>{data}</p>;
};
```

---

## 14. Spiegazione dell’esempio

In questo esempio, il dato `data` parte da `App` e deve arrivare a `Target`.

Il percorso è:

```txt
App -> MiddleComponentA -> MiddleComponentB -> Target
```

Il problema è che:

* `MiddleComponentA` non usa davvero `data`;
* `MiddleComponentB` non usa davvero `data`;
* entrambe devono comunque ricevere e passare avanti la prop.

Questo è Prop Drilling.

---

# Esempio più realistico

## 15. Utente loggato passato all’Header

```jsx
const App = () => {
   const user = getUser();

   return (
      <main>
         <Header user={user} />
         <Content />
         <Footer />
      </main>
   );
};

const Header = ({ user }) => {
   return (
      <header>
         <Logo />
         <Navigation />
         <UserDetails user={user} />
      </header>
   );
};

const UserDetails = ({ user }) => {
   return (
      <div>
         <p>Benvenuto {user.name}</p>
         <button>Logout</button>
      </div>
   );
};
```

---

## 16. Spiegazione dell’esempio realistico

In questo esempio, `App` recupera l’utente:

```jsx
const user = getUser();
```

Poi passa `user` a `Header`:

```jsx
<Header user={user} />
```

Ma `Header` non usa direttamente `user`.

Lo passa solo a `UserDetails`:

```jsx
<UserDetails user={user} />
```

Quindi `Header` deve occuparsi di una prop che in realtà non gli serve.

Questo è un esempio di Prop Drilling.

---

# Context API

## 17. Che cos’è la Context API?

La **Context API** è una funzionalità di React che serve per gestire stato condiviso.

Permette di condividere dati con molte componenti senza doverli passare manualmente tramite props a ogni livello.

In pratica, la Context API aiuta a evitare il Prop Drilling.

---

## 18. Perché usare la Context API?

Usiamo la Context API quando un dato deve essere accessibile da molte componenti in punti diversi dell’applicazione.

Con Context possiamo mettere il dato in un punto alto dell’albero dei componenti e renderlo disponibile ai componenti discendenti.

Così le componenti intermedie non devono più passare props che non usano.

---

# Provider e Consumer

## 19. I due elementi principali della Context API

La Context API ha due concetti principali:

1. **Provider**
2. **Consumer**

---

# Provider

## 20. Che cos’è un Provider?

Il **Provider** contiene la variabile di stato condivisa.

Il Provider rende questo stato disponibile a tutte le componenti annidate dentro di lui.

In pratica:

```txt
Provider = componente che fornisce il dato
```

Tutte le componenti dentro il Provider possono accedere al dato, se lo richiedono.

---

## 21. Consumer

Un **Consumer** è qualsiasi componente che legge il valore fornito dal Provider.

In pratica:

```txt
Consumer = componente che consuma/usa il dato
```

Una componente diventa Consumer quando accede al context.

Nelle versioni moderne di React, spesso si usa l’hook:

```txt
useContext
```

---

# Esempio base di Context API

## 22. Creare un context

```jsx
import { createContext } from "react";

const UserContext = createContext(null);

export default UserContext;
```

Qui creiamo un context chiamato `UserContext`.

---

## 23. Usare un Provider

```jsx
import UserContext from "./UserContext";

function App() {
   const user = { name: "Maria" };

   return (
      <UserContext.Provider value={user}>
         <Header />
         <Content />
         <Footer />
      </UserContext.Provider>
   );
}

export default App;
```

In questo esempio, il Provider rende `user` disponibile a tutti i componenti dentro di lui.

Quindi `Header`, `Content`, `Footer` e i loro figli possono accedere a `user`.

---

## 24. Consumare il context con useContext

```jsx
import { useContext } from "react";
import UserContext from "./UserContext";

function UserDetails() {
   const user = useContext(UserContext);

   return <p>Benvenuto {user.name}</p>;
}

export default UserDetails;
```

`UserDetails` può leggere `user` direttamente dal context.

Non serve più passarlo manualmente tramite `Header`.

---

# Esempio prima e dopo

## 25. Prima: con Prop Drilling

```jsx
function App() {
   const user = { name: "Maria" };

   return <Header user={user} />;
}

function Header({ user }) {
   return <UserDetails user={user} />;
}

function UserDetails({ user }) {
   return <p>Benvenuto {user.name}</p>;
}
```

Qui `Header` riceve `user` anche se non lo usa direttamente.

---

## 26. Dopo: con Context API

```jsx
function App() {
   const user = { name: "Maria" };

   return (
      <UserContext.Provider value={user}>
         <Header />
      </UserContext.Provider>
   );
}

function Header() {
   return <UserDetails />;
}

function UserDetails() {
   const user = useContext(UserContext);

   return <p>Benvenuto {user.name}</p>;
}
```

Qui `Header` non deve più ricevere né passare la prop `user`.

`UserDetails` legge direttamente il dato dal context.

---

# Casi d’uso tipici della Context API

## 27. Quando usare Context?

La Context API è utile per dati condivisi da molte componenti.

Casi tipici:

* carrello ecommerce;
* lingua selezionata;
* tema chiaro/scuro;
* dettagli dell’utente attualmente loggato;
* stato di autenticazione;
* impostazioni globali dell’app.

---

## 28. Esempio: carrello ecommerce

Un carrello può essere usato in molte parti dell’app:

* icona carrello nella navbar;
* pagina carrello;
* pulsante “Add to cart” nei prodotti;
* riepilogo checkout.

In questo caso può avere senso usare uno stato globale tramite Context API.

---

## 29. Esempio: tema chiaro/scuro

Il tema dell’app viene usato da tante componenti.

Per esempio:

* layout;
* bottoni;
* card;
* navbar;
* footer.

Con Context API possiamo salvare il tema globale:

```txt
light / dark
```

E permettere a molte componenti di leggerlo.

---

# Quando NON usare Context?

## 30. Context non serve sempre

Non bisogna usare Context per ogni singolo stato.

Se uno stato serve solo a una componente, è meglio usare stato locale con `useState`.

Esempio:

* input di un form;
* apertura di una singola modale;
* contatore dentro una componente;
* tab selezionata in una piccola sezione.

In questi casi, il context sarebbe troppo pesante o non necessario.

---

# Recap finale

## Concetti principali da ricordare

Lo **state** contiene dati modificabili di una componente o di una applicazione.

Le **props** servono per passare dati tra componenti.

Lo **stato locale** è interno a una componente.

Esempio:

```jsx
const [count, setCount] = useState(0);
```

Lo **stato globale** è condiviso da molte componenti.

Esempi di stato globale:

* utente loggato;
* carrello;
* tema;
* lingua.

Il **Prop Drilling** avviene quando passiamo props attraverso molte componenti intermedie che non usano quei dati.

Il Prop Drilling rende il codice:

* più difficile da mantenere;
* più lungo;
* più fragile;
* meno leggibile.

La **Context API** permette di condividere dati con molte componenti senza passare props manualmente a ogni livello.

La Context API ha due concetti principali:

| Concetto | Significato                              |
| -------- | ---------------------------------------- |
| Provider | Fornisce il dato alle componenti figlie  |
| Consumer | Componente che legge il dato dal context |

Nelle versioni moderne di React, per leggere il context si usa spesso:

```jsx
useContext
```

Context API è utile per:

* carrello;
* tema chiaro/scuro;
* lingua;
* utente loggato;
* stato globale condiviso.

---

# Mini glossario

| Termine         | Significato                                   |
| --------------- | --------------------------------------------- |
| State           | Dati modificabili di una componente o app     |
| Props           | Dati passati tra componenti                   |
| Local State     | Stato locale interno a una componente         |
| Global State    | Stato condiviso da molte componenti           |
| useState        | Hook per creare stato locale                  |
| Setter Function | Funzione che aggiorna lo state                |
| Prop Drilling   | Passaggio di props attraverso molti livelli   |
| Context API     | Funzionalità React per condividere stato      |
| Provider        | Componente che fornisce dati tramite context  |
| Consumer        | Componente che legge dati dal context         |
| useContext      | Hook per leggere un context                   |
| Component Tree  | Albero delle componenti                       |
| Logged User     | Utente attualmente autenticato                |
| Theme           | Tema grafico dell’app, per esempio light/dark |

---


