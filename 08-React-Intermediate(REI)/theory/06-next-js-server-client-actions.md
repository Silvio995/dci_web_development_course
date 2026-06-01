# Next.js: Fortsetzung II

## Componenti lato server, componenti lato client e Server Actions

## 1. Introduzione

Next.js è un framework basato su React.

Rispetto a React puro, Next.js offre anche funzionalità di backend.

Questo significa che con Next.js possiamo lavorare sia con codice frontend sia con codice backend nello stesso progetto.

Una delle caratteristiche più importanti di Next.js è la distinzione tra:

- componenti lato client
- componenti lato server

In inglese:

- Client Components
- Server Components

---

# 2. Next.js offre funzionalità backend

Next.js non serve solo per costruire interfacce utente.

È un framework che può offrire anche funzionalità backend.

Per esempio, con Next.js possiamo:

- creare pagine frontend
- creare componenti React
- creare API routes
- eseguire codice sul server
- leggere dati dal database
- gestire form
- eseguire Server Actions
- fare rendering lato server

Questo rende Next.js molto potente perché permette di costruire applicazioni full-stack.

---

# 3. Cosa significa full-stack?

Un'applicazione full-stack contiene sia:

- frontend
- backend

## Frontend

Il frontend è la parte visibile all'utente.

Esempi:

- pagine
- bottoni
- form
- menu
- card
- interfaccia grafica

## Backend

Il backend è la parte che lavora dietro le quinte.

Esempi:

- database
- autenticazione
- API
- salvataggio dati
- logica server
- invio email
- gestione ordini

Next.js può aiutare a lavorare con entrambe le parti.

---

# 4. Componenti client e componenti server

Next.js usa due tipi principali di componenti:

```txt
Client Components
Server Components
```

In italiano:

```txt
Componenti lato client
Componenti lato server
```

---

# 5. Componenti lato client

I componenti lato client sono componenti che vengono eseguiti nel browser.

Il browser è il client.

Questi componenti servono quando abbiamo bisogno di interazione diretta con l'utente.

Esempi:

- click su un bottone
- input controllati
- form interattivi
- menu aperti/chiusi
- state locale
- effetti con `useEffect`
- accesso a `window`
- accesso a `localStorage`

---

# 6. Componenti lato server

I componenti lato server sono componenti che vengono eseguiti sul server.

Il codice del componente viene eseguito da Next.js sul server.

Poi il risultato viene trasformato in HTML e inviato al browser.

Quindi il browser riceve già una parte della pagina pronta.

---

# 7. Differenza principale

## Client Component

Un Client Component gira nel browser.

Può usare:

- `useState`
- `useEffect`
- `useReducer`
- `useContext`
- eventi come `onClick`
- `localStorage`
- `window`
- `document`

---

## Server Component

Un Server Component gira sul server.

Può fare operazioni server-side, per esempio:

- leggere dati dal database
- chiamare API server-side
- eseguire codice che non deve stare nel browser
- preparare HTML già pronto da inviare al client

Di default, nell'App Router di Next.js, i componenti sono Server Components.

---

# 8. React tradizionale e caricamento nel browser

Con React puro, quando apriamo una pagina, il browser scarica l'applicazione React.

Questo può includere molti componenti, anche componenti che magari non saranno mostrati subito.

In una Single Page Application tradizionale, gran parte del lavoro viene fatto nel browser.

Il browser deve:

- scaricare JavaScript
- eseguire JavaScript
- costruire l'interfaccia
- gestire lo state
- renderizzare i componenti

---

# 9. Vantaggio dei Server Components

I Server Components possono ridurre il JavaScript iniziale che il browser deve scaricare.

Questo può portare a:

- caricamento iniziale più leggero
- meno codice JavaScript inviato al browser
- rendering più efficiente
- migliore performance iniziale
- possibilità di eseguire codice server direttamente nei componenti

Il vantaggio principale è:

> Non tutto deve essere caricato ed eseguito nel browser.

---

# 10. Come funziona un Server Component?

Quando Next.js deve mostrare un Server Component:

1. Next.js esegue il codice del componente sul server
2. Il componente viene renderizzato sul server
3. Next.js genera il risultato
4. Il risultato viene inviato al client
5. Il browser mostra l'HTML ricevuto

Schema:

```txt
Server Component
        ↓
esecuzione sul server
        ↓
HTML generato
        ↓
HTML inviato al browser
        ↓
pagina visibile all'utente
```

---

# 11. Quando usare un Server Component?

Usiamo un Server Component quando il componente non ha bisogno di interazioni lato browser.

Esempi:

- pagina statica
- lista di dati caricata dal server
- componente che legge dati da database
- contenuto testuale
- layout
- pagina prodotto
- blog post
- dashboard con dati già preparati

Esempio:

```tsx
export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <main>
      <h1>Prodotti</h1>

      {products.map((product) => (
        <p key={product.id}>{product.name}</p>
      ))}
    </main>
  );
}
```

Questo componente può essere un Server Component perché non usa state, effetti o eventi del browser.

---

# 12. Quando usare un Client Component?

Usiamo un Client Component quando il componente deve essere interattivo.

Esempi:

- contatore
- menu dropdown
- form con state
- bottone con `onClick`
- modale
- carousel interattivo
- input con `onChange`
- componenti che usano `useState`
- componenti che usano `useEffect`

Esempio:

```tsx
"use client";

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

---

# 13. La direttiva "use client"

Per trasformare un componente in Client Component, dobbiamo scrivere in cima al file:

```tsx
"use client";
```

Questa istruzione deve essere la prima riga del file.

Esempio:

```tsx
"use client";

import { useState } from "react";

export default function Menu() {
  const [open, setOpen] = useState(false);

  return (
    <button onClick={() => setOpen(!open)}>
      Apri / Chiudi
    </button>
  );
}
```

---

# 14. Quando è necessario "use client"?

È necessario usare `"use client"` quando il componente usa:

- `useState`
- `useEffect`
- `useReducer`
- `useContext` per logica interattiva
- `onClick`
- `onChange`
- `onSubmit`
- `window`
- `document`
- `localStorage`

Esempio:

```tsx
"use client";
```

deve essere aggiunto se usiamo:

```tsx
const [value, setValue] = useState("");
```

Oppure:

```tsx
<button onClick={handleClick}>Click</button>
```

---

# 15. Errore comune: dimenticare "use client"

Se proviamo a usare `useState` in un componente senza `"use client"`, Next.js può dare errore.

Esempio sbagliato:

```tsx
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount(count + 1)}>Click</button>;
}
```

Questo può creare errore perché il componente è considerato Server Component di default.

Esempio corretto:

```tsx
"use client";

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount(count + 1)}>Click</button>;
}
```

---

# 16. Server Actions

Next.js ha anche le **Server Actions**.

Le Server Actions sono funzioni che vengono eseguite sul server.

Sono utili quando vogliamo eseguire logica backend, per esempio:

- salvare dati
- leggere o modificare dati nel database
- gestire un form
- creare un ordine
- aggiornare un profilo
- eliminare un elemento
- eseguire codice che non deve stare nel browser

---

# 17. Cos'è una Server Action?

Una Server Action è una funzione dichiarata con:

```tsx
"use server";
```

Questa direttiva dice a Next.js:

> Questa funzione deve essere eseguita sul server.

Esempio concettuale:

```tsx
async function myAction() {
  "use server";

  console.log("Questa funzione gira sul server");
}
```

---

# 18. Esempio di Server Action

Esempio dalla lezione:

```tsx
<button
  onClick={async function () {
    "use server";
    console.log("THIS RUNS ON THE SERVER");
  }}
>
  Click me
</button>
```

Questo esempio mostra l'idea generale:

- aggiungendo `"use server"` dentro una funzione
- Next.js considera quella funzione una Server Action
- la funzione viene eseguita sul server

Nota importante per gli appunti:

> In pratica, le Server Actions vengono usate soprattutto per gestire form o azioni server-side, non come normale logica di click client-side.

---

# 19. Cosa fa il compilatore di Next.js?

Quando dichiariamo una Server Action, il compilatore di Next.js crea dietro le quinte un endpoint server.

Quando il client invoca quella funzione, il browser non esegue direttamente quel codice.

Invece:

1. il client invia una richiesta al server
2. il server esegue la funzione
3. il server restituisce il risultato

Schema:

```txt
Client
  ↓ richiesta
Server endpoint creato da Next.js
  ↓
Server Action eseguita sul server
  ↓
Risposta al client
```

---

# 20. Perché Server Actions sono utili?

Le Server Actions sono utili perché ci permettono di scrivere funzioni server-side in modo più diretto.

Invece di creare sempre manualmente una API route, possiamo dichiarare una funzione server.

Esempi di utilizzo:

- inviare dati da un form
- salvare un nuovo prodotto
- aggiornare dati utente
- eliminare un elemento
- creare un ordine
- modificare dati nel database

---

# 21. "use server" dentro una funzione

Possiamo usare `"use server"` dentro una funzione.

Esempio:

```tsx
async function createProduct() {
  "use server";

  console.log("Creo un prodotto sul server");
}
```

Questa funzione diventa una Server Action.

---

# 22. "use server" in cima a un file

Possiamo anche scrivere `"use server"` in cima a un file.

Esempio:

```tsx
"use server";

export async function createProduct() {
  console.log("Creo un prodotto");
}

export async function deleteProduct(id: number) {
  console.log("Elimino il prodotto", id);
}
```

Quando `"use server"` è scritto in cima al file, tutti gli export di quel file diventano Server Actions.

Quindi:

```tsx
createProduct()
```

e

```tsx
deleteProduct()
```

sono funzioni server-side.

---

# 23. File dedicato alle Server Actions

Spesso si crea un file dedicato per le Server Actions.

Esempio struttura:

```txt
app/
  actions.ts
```

Oppure:

```txt
src/
  actions/
    product-actions.ts
```

Esempio:

```tsx
"use server";

export async function createProduct(formData: FormData) {
  const name = formData.get("name");

  console.log("Creo prodotto:", name);
}
```

---

# 24. Usare una Server Action con un form

Un caso comune è usare una Server Action con un form.

Esempio:

```tsx
"use server";

export async function createMessage(formData: FormData) {
  const message = formData.get("message");

  console.log("Messaggio ricevuto:", message);
}
```

Poi nel componente:

```tsx
import { createMessage } from "./actions";

export default function ContactPage() {
  return (
    <form action={createMessage}>
      <input name="message" />
      <button type="submit">Invia</button>
    </form>
  );
}
```

Quando il form viene inviato, la funzione `createMessage` viene eseguita sul server.

---

# 25. Client Components e Server Actions

Una Server Action può essere chiamata anche da componenti client, ma bisogna ricordare una cosa importante:

> Il codice della Server Action non gira nel browser.  
> Gira sempre sul server.

Quindi, anche se la chiamiamo da un componente client, Next.js invia una richiesta al server.

---

# 26. "use client" e "use server" a confronto

## "use client"

Si usa per rendere un componente un Client Component.

Esempio:

```tsx
"use client";
```

Serve quando il componente usa:

- state
- effetti
- eventi
- browser API

---

## "use server"

Si usa per dichiarare codice che deve essere eseguito sul server.

Esempio:

```tsx
"use server";
```

Serve per:

- Server Actions
- funzioni backend
- codice server-side

---

# 27. Tabella riassuntiva

| Direttiva | Dove gira il codice? | Quando si usa? |
|---|---|---|
| `"use client"` | Browser | Componenti interattivi |
| `"use server"` | Server | Funzioni server-side / Server Actions |

---

# 28. Esempio pratico: componente client

```tsx
"use client";

import { useState } from "react";

export default function LikeButton() {
  const [likes, setLikes] = useState(0);

  return (
    <button onClick={() => setLikes(likes + 1)}>
      Likes: {likes}
    </button>
  );
}
```

Questo deve essere client-side perché usa:

- `useState`
- `onClick`

---

# 29. Esempio pratico: Server Action

```tsx
"use server";

export async function saveLike() {
  console.log("Salvo il like sul server");
}
```

Questa funzione gira sul server.

Potrebbe essere usata per salvare un dato nel database.

---

# 30. Esempio combinato

File action:

```tsx
"use server";

export async function saveLike() {
  console.log("Like salvato sul server");
}
```

Componente client:

```tsx
"use client";

import { useState } from "react";
import { saveLike } from "./actions";

export default function LikeButton() {
  const [likes, setLikes] = useState(0);

  async function handleClick() {
    setLikes(likes + 1);
    await saveLike();
  }

  return (
    <button onClick={handleClick}>
      Likes: {likes}
    </button>
  );
}
```

Qui succedono due cose:

1. lo state locale `likes` viene aggiornato nel browser
2. `saveLike()` viene eseguita sul server

---

# 31. Attenzione alla sicurezza

Il codice server-side può accedere a cose che non devono stare nel browser.

Per esempio:

- database
- variabili d'ambiente segrete
- token privati
- logica sensibile

Per questo `"use server"` è importante.

Il codice server deve restare sul server.

Non dobbiamo mettere dati segreti nei Client Components.

---

# 32. Regola pratica

## Se il componente deve interagire con l'utente

Usa:

```tsx
"use client";
```

Esempi:

- click
- input
- form controllato
- menu
- state
- effetti

---

## Se la funzione deve lavorare sul server

Usa:

```tsx
"use server";
```

Esempi:

- salvare dati
- leggere dati dal database
- modificare dati
- inviare email
- gestire azioni backend

---

# 33. Schema mentale generale

```txt
Server Component
= renderizzato sul server
= invia HTML al client
= utile per dati e contenuto

Client Component
= renderizzato/interattivo nel browser
= usa useState, useEffect, eventi

Server Action
= funzione che gira sul server
= può essere chiamata dal client tramite richiesta
```

---

# 34. Flusso di una Server Action

```txt
Utente compie un'azione
        ↓
Client Component chiama una Server Action
        ↓
Next.js invia una richiesta al server
        ↓
Il server esegue la funzione
        ↓
La risposta torna al client
        ↓
La UI può aggiornarsi
```

---

# 35. Errori comuni

## 1. Usare useState senza "use client"

Errore:

```tsx
import { useState } from "react";

export default function Component() {
  const [open, setOpen] = useState(false);

  return <button onClick={() => setOpen(!open)}>Toggle</button>;
}
```

Correzione:

```tsx
"use client";

import { useState } from "react";

export default function Component() {
  const [open, setOpen] = useState(false);

  return <button onClick={() => setOpen(!open)}>Toggle</button>;
}
```

---

## 2. Mettere codice segreto in un Client Component

Errore concettuale:

```tsx
"use client";

const secret = process.env.SECRET_KEY;
```

I dati sensibili devono stare lato server, non nel browser.

---

## 3. Pensare che una Server Action giri nel browser

Una Server Action gira sul server.

Anche se viene chiamata da un componente client, il client invia una richiesta al server.

---

# 36. Parole chiave da memorizzare

## Client

Il browser dell'utente.

## Server

Il computer/ambiente dove gira il backend dell'applicazione.

## Client Component

Componente eseguito nel browser.

## Server Component

Componente eseguito sul server.

## Server Action

Funzione eseguita sul server.

## "use client"

Direttiva che rende un componente client-side.

## "use server"

Direttiva che rende una funzione una Server Action oppure rende tutti gli export di un file Server Actions.

---

# 37. Schema veloce da copiare

```tsx
"use client";

import { useState } from "react";

export default function ClientComponent() {
  const [value, setValue] = useState("");

  return (
    <input
      value={value}
      onChange={(event) => setValue(event.target.value)}
    />
  );
}
```

---

```tsx
"use server";

export async function serverAction() {
  console.log("Questa funzione gira sul server");
}
```

---

# 38. Riassunto finale

In questa lezione abbiamo visto che Next.js non è solo frontend.

Next.js è un framework React che offre anche funzionalità backend.

Abbiamo imparato la differenza tra:

- Client Components
- Server Components
- Server Actions

I Client Components girano nel browser e servono per componenti interattivi.

I Server Components girano sul server e possono generare HTML da inviare al client.

Le Server Actions sono funzioni che girano sul server e possono essere chiamate anche dal client.

La direttiva:

```tsx
"use client";
```

serve per rendere un componente interattivo lato browser.

La direttiva:

```tsx
"use server";
```

serve per dichiarare codice che deve essere eseguito sul server.

Il concetto più importante da ricordare è:

> In Next.js non tutto il codice gira nel browser.  
> Alcuni componenti e funzioni possono girare direttamente sul server.
