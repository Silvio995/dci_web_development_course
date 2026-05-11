# React - Ciclo di vita delle componenti e useEffect

## 1. Il ciclo di vita di una componente React

Il **ciclo di vita** di una componente React descrive i momenti principali in cui una componente nasce, cambia e viene rimossa dalla pagina.

Il ciclo di vita di una componente React può essere diviso in tre fasi principali:

1. **Mounting**
2. **Updating**
3. **Unmounting**

---

# Mounting

## 2. Che cos’è il Mounting?

Il **mounting** è il primo momento in cui una componente viene mostrata nella pagina.

In pratica, è la prima volta in cui la funzione della componente viene eseguita.

Esempio:

```jsx
function Welcome() {
   return <h1>Hello React</h1>;
}
```

Quando React mostra questa componente per la prima volta nella pagina, sta facendo il **mounting** della componente.

---

# Updating

## 3. Che cos’è l’Updating?

L’**updating** avviene quando qualcosa fa eseguire di nuovo la funzione della componente.

Una componente viene aggiornata, per esempio, quando:

* cambia lo **state**;
* cambiano le **props**;
* il componente padre viene renderizzato di nuovo.

Quando una componente viene aggiornata, React esegue di nuovo la funzione della componente e aggiorna l’interfaccia se qualcosa è cambiato.

Esempio:

```jsx
const [count, setCount] = useState(0);
```

Se chiamiamo:

```jsx
setCount(count + 1);
```

React aggiorna lo state e renderizza di nuovo la componente.

---

# Unmounting

## 4. Che cos’è l’Unmounting?

L’**unmounting** avviene quando una componente viene rimossa dalla pagina.

Per esempio, una componente può essere rimossa quando:

* cambiamo pagina;
* una condizione non è più vera;
* un elemento non deve più essere mostrato.

Esempio:

```jsx
{showMessage && <Message />}
```

Se `showMessage` diventa `false`, la componente `Message` viene rimossa dalla pagina.

Questa fase si chiama **unmounting**.

---

# Recuperare dati dentro una componente

## 5. Fetch API

In React, il modo base per recuperare dati da un server è usare la **Fetch API**.

I dati recuperati vengono normalmente salvati nello **state** della componente.

Esempio generale:

```jsx
const [data, setData] = useState("");
```

Quando riceviamo i dati dal server, aggiorniamo lo state:

```jsx
setData(result);
```

Attenzione però: quando lo state viene aggiornato, la componente viene renderizzata di nuovo.

---

# Problema: fetch dentro il corpo della componente

## 6. Esempio sbagliato

```jsx
import React, { useState } from "react";

const FetchData = () => {
   const [data, setData] = useState("");

   fetch("https://jsonplaceholder.typicode.com/posts/1")
      .then((response) => response.json())
      .then((data) => setData(JSON.stringify(data)));

   return <p>{data}</p>;
};

export default FetchData;
```

Questo codice sembra corretto, ma in realtà crea un problema molto importante.

---

## 7. Perché questo codice crea un problema?

Pensiamo al ciclo di vita della componente.

Quando `FetchData` viene renderizzata:

1. la componente viene eseguita;
2. parte una richiesta `fetch`;
3. quando la richiesta termina, viene chiamato `setData`;
4. `setData` aggiorna lo state;
5. l’aggiornamento dello state causa un nuovo render;
6. il nuovo render fa partire di nuovo la `fetch`;
7. la nuova `fetch` aggiorna di nuovo lo state;
8. lo state aggiornato causa un altro render.

Questo processo continua all’infinito.

Si crea quindi una **infinite loop**, cioè un ciclo infinito.

---

## 8. Regola importante

Non bisogna mettere direttamente una richiesta `fetch` nel corpo principale della componente se quella richiesta aggiorna lo state.

Questo perché ogni aggiornamento dello state causa un nuovo render, e ogni nuovo render farebbe partire di nuovo la richiesta.

Per risolvere questo problema usiamo `useEffect`.

---

# useEffect

## 9. Che cos’è useEffect?

`useEffect` è un **React Hook**.

È uno strumento molto potente perché permette di eseguire azioni in una componente in base al suo ciclo di vita.

Con `useEffect` possiamo eseguire codice:

* quando la componente viene mostrata per la prima volta;
* quando cambia una variabile di state;
* quando cambia una prop;
* quando la componente viene rimossa dalla pagina.

---

## 10. Perché useEffect è utile?

`useEffect` permette di controllare quando deve essere eseguita una certa azione.

Per esempio, possiamo dire a React:

> Esegui questa fetch solo la prima volta che la componente viene renderizzata.

In questo modo evitiamo il ciclo infinito.

---

# useEffect eseguito solo al primo render

## 11. Esempio corretto

```jsx
import { useEffect, useState } from "react";

function User({ id }) {
   const [data, setData] = useState("");

   useEffect(() => {
      fetch("https://jsonplaceholder.typicode.com/posts/1")
         .then((response) => response.json())
         .then((data) => setData(JSON.stringify(data)));
   }, []);

   return <p>{data}</p>;
}

export default User;
```

---

## 12. Spiegazione dell’esempio

```jsx
useEffect(() => {
   fetch("https://jsonplaceholder.typicode.com/posts/1")
      .then((response) => response.json())
      .then((data) => setData(JSON.stringify(data)));
}, []);
```

Qui la `fetch` è dentro `useEffect`.

Il secondo argomento di `useEffect` è:

```jsx
[]
```

Questo array vuoto significa:

> Esegui questo effetto solo una volta, quando la componente viene mostrata per la prima volta.

Quindi:

* la componente viene renderizzata;
* `useEffect` esegue la `fetch`;
* la `fetch` aggiorna lo state;
* la componente viene renderizzata di nuovo;
* ma `useEffect` non riparte, perché l’array delle dipendenze è vuoto.

Così evitiamo la infinite loop.

---

# Il secondo argomento di useEffect

## 13. Dependency Array

Il secondo argomento di `useEffect` si chiama:

```txt
Dependency Array
```

In italiano possiamo chiamarlo:

```txt
Array delle dipendenze
```

Questo array controlla quando l’effetto deve essere eseguito.

---

## 14. useEffect con array vuoto

```jsx
useEffect(() => {
   // codice da eseguire
}, []);
```

Significa:

> Esegui il codice solo al primo render della componente.

Questo è utile per:

* recuperare dati iniziali;
* caricare informazioni da un server;
* fare setup iniziale.

---

## 15. useEffect con una dipendenza

```jsx
useEffect(() => {
   fetch(`https://jsonplaceholder.typicode.com/posts/${id}`)
      .then((response) => response.json())
      .then((data) => setData(JSON.stringify(data)));
}, [id]);
```

In questo caso `id` è una dipendenza.

Significa:

> Esegui questo effetto la prima volta e ogni volta che `id` cambia.

Quindi, se cambia il valore di `id`, React esegue di nuovo la `fetch`.

---

## 16. Perché `[id]` è utile?

Immaginiamo che la componente debba mostrare un post diverso in base all’id.

Se `id` cambia da `1` a `2`, vogliamo recuperare i dati del post numero 2.

Per questo scriviamo:

```jsx
}, [id]);
```

Così React sa che l’effetto dipende da `id`.

Quando `id` cambia, l’effetto viene eseguito di nuovo.

---

# Terminologia: Side Effects

## 17. Perché si chiama useEffect?

Il nome `useEffect` deriva dal concetto di:

```txt
Side Effects
```

In italiano:

```txt
Effetti collaterali
```

In programmazione, un effetto collaterale è qualcosa che una funzione fa oltre a restituire un valore.

---

# Funzioni pure

## 18. Che cos’è una funzione pura?

Una **funzione pura** è una funzione che non ha effetti collaterali.

Una funzione pura:

* con lo stesso input restituisce sempre lo stesso output;
* non modifica variabili esterne;
* non legge o scrive file;
* non fa richieste a server;
* si limita a restituire un valore.

Le funzioni pure sono prevedibili e facili da testare.

---

## 19. Esempio di funzione pura

```js
function add(a, b) {
   return a + b;
}

add(1, 5); // restituisce 6
```

Questa è una funzione pura perché:

* riceve `a` e `b`;
* restituisce la somma;
* non modifica nulla fuori dalla funzione;
* con `add(1, 5)` il risultato sarà sempre `6`.

---

# Funzioni con side effects

## 20. Che cos’è una funzione con effetti collaterali?

Una funzione ha effetti collaterali quando fa qualcosa oltre a restituire un valore.

Esempi di effetti collaterali:

* modificare una variabile fuori dalla funzione;
* scrivere qualcosa in un file;
* leggere dati da un file;
* leggere dati da un database;
* recuperare dati da un server;
* modificare il DOM;
* aggiornare lo state.

---

## 21. Esempio di funzione con side effect

```js
let total = 0;

function inc(a) {
   total += a;
}

inc(6); // total ora è 6
inc(6); // total ora è 12
inc(6); // total ora è 18
```

Questa funzione ha un effetto collaterale perché modifica una variabile esterna alla funzione.

La variabile `total` si trova fuori da `inc`, ma `inc` la modifica.

Quindi la funzione non si limita a restituire un valore.

---

## 22. Perché non è una funzione pura?

La funzione `inc` non è pura perché:

* modifica `total`, una variabile esterna;
* il risultato dipende dallo stato precedente di `total`;
* chiamando più volte la funzione con lo stesso valore, il risultato globale cambia.

Esempio:

```js
inc(6); // total = 6
inc(6); // total = 12
inc(6); // total = 18
```

L’input è sempre `6`, ma il valore finale cambia ogni volta.

---

# Esercizio: questa è una funzione pura?

## 23. Codice da analizzare

```js
let name = "Max";

function greet(greeting) {
   return greeting + ", " + name;
}

greet("Hello"); // Hello, Max
greet("Hiya");  // Hiya, Max
```

Domanda:

> Questa è una funzione pura?

---

## 24. Risposta

No, questa funzione non è completamente pura.

Anche se la funzione restituisce un valore e non modifica direttamente una variabile esterna, legge comunque una variabile esterna:

```js
name
```

La funzione `greet` dipende da una variabile che non riceve come parametro.

Questo significa che il risultato può cambiare se cambia il valore di `name`.

Esempio:

```js
let name = "Max";

greet("Hello"); // Hello, Max

name = "Anna";

greet("Hello"); // Hello, Anna
```

La chiamata è sempre:

```js
greet("Hello")
```

Ma il risultato cambia perché la funzione usa una variabile esterna.

Per questo non è una funzione pura.

---

## 25. Versione pura della funzione greet

Per rendere la funzione pura, dobbiamo passare tutto quello che le serve tramite parametri.

Esempio:

```js
function greet(greeting, name) {
   return greeting + ", " + name;
}

greet("Hello", "Max"); // Hello, Max
greet("Hello", "Anna"); // Hello, Anna
```

Questa versione è pura perché:

* non legge variabili esterne;
* usa solo i parametri ricevuti;
* con lo stesso input restituisce sempre lo stesso output.

---

# Collegamento con React

## 26. Perché tutto questo è importante in React?

Le componenti React dovrebbero essere il più possibile prevedibili.

La funzione di una componente dovrebbe descrivere cosa mostrare in base a:

* props;
* state.

Però alcune azioni, come recuperare dati da un server, sono effetti collaterali.

Esempi di side effects in React:

* fare una `fetch`;
* salvare dati nel localStorage;
* modificare il titolo della pagina;
* impostare un timer;
* ascoltare eventi del browser;
* aggiornare dati esterni.

Per gestire queste azioni usiamo `useEffect`.

---

# Esempio completo con useEffect e fetch

```jsx
import { useEffect, useState } from "react";

function Post() {
   const [data, setData] = useState(null);
   const [loading, setLoading] = useState(true);

   useEffect(() => {
      fetch("https://jsonplaceholder.typicode.com/posts/1")
         .then((response) => response.json())
         .then((result) => {
            setData(result);
            setLoading(false);
         });
   }, []);

   if (loading) {
      return <p>Loading...</p>;
   }

   return (
      <article>
         <h2>{data.title}</h2>
         <p>{data.body}</p>
      </article>
   );
}

export default Post;
```

---

## 27. Spiegazione dell’esempio completo

In questo esempio usiamo due state:

```jsx
const [data, setData] = useState(null);
const [loading, setLoading] = useState(true);
```

`data` contiene i dati recuperati dal server.

`loading` indica se i dati stanno ancora caricando.

---

La fetch viene eseguita dentro `useEffect`:

```jsx
useEffect(() => {
   fetch("https://jsonplaceholder.typicode.com/posts/1")
      .then((response) => response.json())
      .then((result) => {
         setData(result);
         setLoading(false);
      });
}, []);
```

L’array vuoto `[]` fa sì che la fetch venga eseguita solo al primo render.

Quando arrivano i dati:

* `setData(result)` salva i dati nello state;
* `setLoading(false)` indica che il caricamento è terminato.

---

# Recap finale

## Concetti principali da ricordare

Il ciclo di vita di una componente React ha tre fasi principali:

| Fase       | Significato                                                      |
| ---------- | ---------------------------------------------------------------- |
| Mounting   | La componente viene mostrata per la prima volta                  |
| Updating   | La componente viene renderizzata di nuovo perché qualcosa cambia |
| Unmounting | La componente viene rimossa dalla pagina                         |

Una `fetch` non deve essere messa direttamente nel corpo della componente se aggiorna lo state.

Se mettiamo una `fetch` direttamente nella componente, possiamo creare una infinite loop.

`useEffect` permette di gestire i side effects in React.

Il secondo argomento di `useEffect` è il dependency array.

Con `[]`, l’effetto viene eseguito solo al primo render.

Con `[id]`, l’effetto viene eseguito al primo render e ogni volta che `id` cambia.

Una funzione pura restituisce sempre lo stesso output con lo stesso input e non usa o modifica dati esterni.

Una funzione con side effects fa qualcosa oltre a restituire un valore.

In React, esempi di side effects sono:

* fetch da server;
* timer;
* localStorage;
* eventi del browser;
* aggiornamenti esterni.

---

# Mini glossario

| Termine             | Significato                                           |
| ------------------- | ----------------------------------------------------- |
| Component Lifecycle | Ciclo di vita di una componente                       |
| Mounting            | Prima visualizzazione della componente                |
| Updating            | Aggiornamento della componente                        |
| Unmounting          | Rimozione della componente dalla pagina               |
| fetch               | Metodo per recuperare dati da un server               |
| State               | Stato interno della componente                        |
| setState            | Funzione che aggiorna lo state                        |
| Render              | Esecuzione della componente per mostrare UI           |
| Re-render           | Nuova esecuzione della componente dopo un cambiamento |
| Infinite Loop       | Ciclo infinito                                        |
| useEffect           | Hook per gestire effetti collaterali                  |
| Dependency Array    | Array che controlla quando useEffect viene eseguito   |
| Side Effect         | Effetto collaterale                                   |
| Pure Function       | Funzione pura                                         |
| Props               | Dati ricevuti da una componente                       |

---

```
