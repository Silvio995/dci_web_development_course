# SPA, React e JSX

## 1. SPA - Single Page Application

Una **SPA**, cioè **Single Page Application**, è un tipo di sito web o applicazione web che normalmente utilizza un solo documento HTML principale.

In una SPA, il contenuto viene aggiornato dinamicamente senza dover ricaricare tutta la pagina.

Questo significa che:

- la pagina intera non deve essere ricaricata ogni volta che cambia qualcosa;
- la navigazione da una “pagina” all’altra non richiede un ricaricamento completo;
- elementi come header, footer e menu possono restare sempre gli stessi;
- solo la parte della pagina che cambia viene aggiornata.

Un esempio molto semplice è quello di piattaforme come **Instagram** o **Facebook**.

Quando apri un messaggio privato, non ha senso ricaricare tutta la pagina.  
Quando scrivi un commento sotto un post, non è necessario ricaricare tutto il sito.

Una **Single Page Application** serve proprio a creare esperienze più fluide e veloci per l’utente.

---

## 2. Tutto deve essere una SPA?

No, non tutti i siti devono essere SPA.

Le SPA sono solo uno dei tanti modi per costruire siti web e applicazioni.

Le studiamo perché:

- sono molto richieste nel mercato del lavoro;
- sono un ottimo esempio per imparare concetti moderni di sviluppo web;
- imparare le SPA rende più facile capire anche altri approcci in futuro.

---

# React

## 3. Perché React?

React è una libreria JavaScript molto usata per creare Single Page Applications.

È una scelta molto solida perché:

- è estremamente popolare;
- è ottima per imparare i concetti fondamentali dello sviluppo frontend moderno;
- ha un forte supporto, perché React è sviluppato da Meta;
- ha un ecosistema molto grande;
- esistono tante librerie e framework costruiti intorno a React.

---

## 4. Alternative a React

React non è l’unica tecnologia per creare applicazioni frontend moderne.

Altre alternative sono:

- **AngularJS**, sviluppato da Google;
- **Vue.js**;
- **Svelte**.

Se impari bene una di queste tecnologie, sarà molto più facile imparare anche le altre, perché molti concetti sono simili.

---

## 5. L’ecosistema React

Durante il corso vedremo tre strumenti importanti legati a React:

### React

È la libreria principale, usata per creare interfacce utente.

### Next.js

È un framework basato su React, usato per creare applicazioni full-stack.

Con Next.js possiamo gestire sia il frontend sia alcune parti backend dell’applicazione.

### React Native

È una tecnologia basata su React che permette di creare applicazioni mobili native per Android e iOS.

---

# Come funziona React?

## 6. Il DOM

Prima di capire React, bisogna ricordare cos’è il **DOM**.

Il DOM, cioè **Document Object Model**, è la rappresentazione della struttura HTML della pagina nel browser.

Quando modifichiamo una pagina con JavaScript, normalmente modifichiamo il DOM reale.

React però funziona in modo diverso.

---

## 7. Virtual DOM

React ricrea il DOM in JavaScript.

Questa rappresentazione viene chiamata **Virtual DOM**, cioè DOM virtuale.

Con React:

- non modifichiamo direttamente il DOM reale;
- modifichiamo delle variabili dentro React;
- React aggiorna il Virtual DOM;
- poi React confronta il Virtual DOM con il DOM reale;
- infine aggiorna solo le parti del DOM reale che sono cambiate.

Questo può essere molto più veloce rispetto a modificare direttamente tutto il DOM.

---

## 8. Lo stato dell’applicazione

React controlla lo stato dell’applicazione usando variabili.

Esempi:

```js
formSubmitted = true