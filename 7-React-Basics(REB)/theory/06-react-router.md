# React - Routing e React Router

## 1. Che cos’è il routing?

Il **routing** è il sistema che permette di navigare tra diverse viste di un’applicazione.

In una normale pagina web, quando clicchiamo su un link, il browser carica una nuova pagina HTML.

In una **SPA**, cioè **Single Page Application**, invece, non viene ricaricata tutta la pagina.

JavaScript controlla cosa deve essere mostrato nella pagina.

---

# Ricordo: SPA vs sito HTML/CSS normale

## 2. Cosa distingue una SPA da un sito tradizionale?

Una SPA si distingue da una normale pagina HTML/CSS perché:

* non ricarica tutta la pagina quando si cambia sezione;
* usa un singolo file HTML principale;
* JavaScript controlla gli elementi mostrati nella pagina;
* il contenuto cambia dinamicamente;
* l’esperienza utente è più fluida.

SPA significa:

```txt
Single Page Application
```

In italiano:

```txt
Applicazione a pagina singola
```

---

## 3. Esempio pratico di SPA

Immagina un’applicazione con queste sezioni:

* Home;
* Prodotti;
* Contatti;
* Profilo utente.

In un sito tradizionale, ogni sezione potrebbe essere una pagina HTML diversa.

In una SPA, invece, queste non sono vere pagine separate.

Sono **viste** diverse gestite da JavaScript.

---

# Il problema del bottone indietro

## 4. Il bottone “indietro” nei siti tradizionali

Nei siti web tradizionali, il bottone **indietro** del browser funziona molto bene.

Per esempio:

1. visiti la homepage;
2. clicchi su una pagina prodotti;
3. clicchi sul bottone indietro;
4. il browser torna alla homepage.

Questo succede perché il browser ha caricato pagine diverse e le ha salvate nella sua cronologia.

---

## 5. Il problema nelle SPA

In una SPA, invece, la pagina HTML è una sola.

Quando cambiamo vista, spesso non stiamo realmente caricando una nuova pagina dal server.

Stiamo solo cambiando il contenuto mostrato da JavaScript.

Per questo, senza un sistema di routing, il bottone indietro potrebbe non funzionare come ci aspettiamo.

Però possiamo farlo funzionare usando la **History API**.

---

# History API

## 6. Che cos’è la History API?

La **History API** è una funzionalità del browser che permette a JavaScript di controllare la cronologia di navigazione.

La cronologia include:

* bottone indietro;
* bottone avanti;
* URL visitati;
* stato della navigazione.

JavaScript può usare questa API per modificare la cronologia e gestire la navigazione dentro una SPA.

---

## 7. JavaScript e la barra degli indirizzi

JavaScript può controllare parzialmente la barra degli indirizzi.

Per esempio, può cambiare l’URL da:

```txt
https://www.example.com/
```

a:

```txt
https://www.example.com/products
```

senza ricaricare completamente la pagina.

Però ci sono limiti di sicurezza.

Per esempio, JavaScript non può cambiare l’URL da:

```txt
https://evil-hacker.com
```

a:

```txt
https://mybank.com
```

Questo sarebbe pericoloso, quindi il browser non lo permette.

---

# Esempio di sito web tradizionale

## 8. Navigazione normale su internet

Immaginiamo di visitare questo sito:

```txt
https://www.iana.org
```

Quando apriamo questa pagina:

1. il browser manda una richiesta `GET` al server;
2. il server riceve e processa la richiesta;
3. il server restituisce un file HTML, per esempio `index.html`;
4. il browser mostra la pagina.

---

## 9. Navigare verso un’altra pagina

Ora immaginiamo di cliccare su **Domain Names**.

L’URL diventa:

```txt
https://www.iana.org/domains
```

A questo punto:

1. il browser manda una nuova richiesta `GET`;
2. il server processa la richiesta;
3. il server restituisce una nuova pagina, per esempio `domains.html`;
4. il browser mostra la nuova pagina.

---

## 10. Due pagine separate

In questo esempio abbiamo due pagine separate:

```txt
https://www.iana.org
```

```txt
https://www.iana.org/domains
```

Il browser manda una richiesta separata per ogni pagina.

Il server risponde con una pagina diversa.

Questa è la navigazione classica dei siti web tradizionali.

---

# React e SPA: viste invece di pagine

## 11. Pagine vs viste

In React e nelle SPA in generale, spesso non pensiamo più in termini di pagine HTML separate.

Pensiamo invece in termini di **views**, cioè viste.

Esempi:

* Home View;
* Products View;
* Users View;
* Contact View;
* Login View.

In italiano possiamo dire:

```txt
viste
```

Una vista è una parte dell’applicazione che viene mostrata in base alla navigazione.

---

## 12. Esempio di viste in React

Possiamo avere componenti come:

```txt
Home.jsx
Products.jsx
Users.jsx
Contact.jsx
```

Questi componenti rappresentano diverse viste dell’applicazione.

Quando l’utente va su:

```txt
/products
```

React dovrebbe mostrare la vista `Products`.

Quando l’utente va su:

```txt
/contact
```

React dovrebbe mostrare la vista `Contact`.

---

# History API e React

## 13. Routing in React

In React e nelle SPA, il controllo della navigazione e della cronologia viene chiamato:

```txt
Routing
```

Il routing permette di passare da una vista all’altra senza ricaricare tutta la pagina.

Esempio:

```txt
Home -> Contact -> Products
```

L’applicazione cambia contenuto, ma resta dentro la stessa pagina HTML principale.

---

## 14. Il routing è incluso in React?

No, il routing non è incluso nel core di React.

React si occupa principalmente di costruire interfacce utente attraverso componenti.

Il routing viene gestito tramite librerie esterne.

Una delle librerie più usate per il routing in React è:

```txt
react-router-dom
```

---

## 15. Perché il routing non è nel core di React?

Uno dei motivi è che React non viene usato solo per il web.

React viene usato anche per creare applicazioni mobile native tramite:

```txt
React Native
```

Le app mobile non hanno una barra URL come i siti web.

Quindi il routing web non ha senso come funzionalità base del core di React.

Il core di React rimane comune sia per React sul web sia per React Native.

---

# Cosa fa il routing?

## 16. Navigare tra viste

Il routing si occupa di gestire il passaggio tra diverse viste.

Esempi:

```txt
Home -> Contacts
Contacts -> Products
Products -> User Profile
```

Quando l’utente clicca su un link, React mostra la vista corretta senza ricaricare tutta la pagina.

---

## 17. Gestire avanti e indietro

Il routing si occupa anche di far funzionare i bottoni del browser:

* indietro;
* avanti.

Così l’utente può navigare nella SPA in modo naturale, come in un sito tradizionale.

---

## 18. Caricare una vista da un URL specifico

Il routing permette anche di caricare direttamente una vista in base all’URL.

Per esempio, se l’utente visita direttamente:

```txt
https://www.example.com/products
```

React deve mostrare subito la vista dei prodotti.

Non deve necessariamente partire sempre dalla Home.

---

# React Router

## 19. Che cos’è React Router?

**React Router** è una libreria per gestire il routing nelle applicazioni React.

La libreria che useremo è:

```txt
react-router-dom
```

React Router permette agli utenti di navigare da una vista all’altra senza ricaricare completamente la pagina.

Questo rende l’applicazione più veloce e fluida.

---

## 20. Perché usare React Router?

Usiamo React Router perché:

* permette di navigare tra viste diverse;
* evita il ricaricamento completo della pagina;
* aggiorna solo il contenuto necessario;
* gestisce URL diversi;
* permette l’uso del bottone indietro e avanti;
* rende una SPA più simile a un sito completo;
* permette di mostrare una vista specifica quando si visita direttamente un URL.

---

# Installare React Router

## 21. Installazione

Dentro il progetto React, possiamo installare React Router con:

```bash
npm install react-router-dom
```

Dopo l’installazione possiamo usare le sue componenti per creare le rotte.

---

# Concetti base di React Router

## 22. BrowserRouter

`BrowserRouter` è il contenitore principale del sistema di routing.

Di solito avvolge tutta l’applicazione.

Esempio:

```jsx
import { BrowserRouter } from "react-router-dom";

function App() {
   return (
      <BrowserRouter>
         {/* routes here */}
      </BrowserRouter>
   );
}

export default App;
```

---

## 23. Routes e Route

`Routes` contiene l’elenco delle rotte.

`Route` definisce quale componente mostrare per un certo percorso.

Esempio:

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";

function App() {
   return (
      <BrowserRouter>
         <Routes>
            <Route path="/" element={<Home />} />
            <Route path="/products" element={<Products />} />
            <Route path="/contact" element={<Contact />} />
         </Routes>
      </BrowserRouter>
   );
}
```

In questo esempio:

| Path        | Componente mostrata |
| ----------- | ------------------- |
| `/`         | `Home`              |
| `/products` | `Products`          |
| `/contact`  | `Contact`           |

---

## 24. Link

In una SPA non dovremmo usare sempre il normale tag `<a>` per navigare tra viste interne.

Con React Router usiamo spesso:

```txt
Link
```

Esempio:

```jsx
import { Link } from "react-router-dom";

function Navbar() {
   return (
      <nav>
         <Link to="/">Home</Link>
         <Link to="/products">Products</Link>
         <Link to="/contact">Contact</Link>
      </nav>
   );
}
```

`Link` cambia l’URL e mostra la vista corretta senza ricaricare tutta la pagina.

---

# Esempio completo

## 25. Componenti delle viste

```jsx
function Home() {
   return <h1>Home View</h1>;
}

function Products() {
   return <h1>Products View</h1>;
}

function Contact() {
   return <h1>Contact View</h1>;
}
```

---

## 26. Navbar

```jsx
import { Link } from "react-router-dom";

function Navbar() {
   return (
      <nav>
         <Link to="/">Home</Link>
         <Link to="/products">Products</Link>
         <Link to="/contact">Contact</Link>
      </nav>
   );
}

export default Navbar;
```

---

## 27. App.jsx con React Router

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
import Navbar from "./Navbar";

function Home() {
   return <h1>Home View</h1>;
}

function Products() {
   return <h1>Products View</h1>;
}

function Contact() {
   return <h1>Contact View</h1>;
}

function App() {
   return (
      <BrowserRouter>
         <Navbar />

         <Routes>
            <Route path="/" element={<Home />} />
            <Route path="/products" element={<Products />} />
            <Route path="/contact" element={<Contact />} />
         </Routes>
      </BrowserRouter>
   );
}

export default App;
```

---

# Spiegazione dell’esempio completo

## 28. BrowserRouter

```jsx
<BrowserRouter>
   ...
</BrowserRouter>
```

Avvolge l’applicazione e abilita il routing.

---

## 29. Navbar

```jsx
<Navbar />
```

La Navbar resta visibile mentre cambiano le viste.

Questo è tipico di una SPA: alcune parti dell’interfaccia restano uguali, mentre il contenuto principale cambia.

---

## 30. Routes

```jsx
<Routes>
   <Route path="/" element={<Home />} />
   <Route path="/products" element={<Products />} />
   <Route path="/contact" element={<Contact />} />
</Routes>
```

Qui definiamo quali componenti devono essere mostrate in base all’URL.

---

## 31. Route

Ogni `Route` ha almeno due proprietà importanti:

| Prop      | Significato                  |
| --------- | ---------------------------- |
| `path`    | URL/percorso da intercettare |
| `element` | Componente da mostrare       |

Esempio:

```jsx
<Route path="/products" element={<Products />} />
```

Significa:

> Quando l’URL è `/products`, mostra la componente `Products`.

---

# Link vs tag a

## 32. Differenza tra `<a>` e `<Link>`

In HTML normale usiamo:

```html
<a href="/products">Products</a>
```

Questo può causare il ricaricamento completo della pagina.

In React Router usiamo:

```jsx
<Link to="/products">Products</Link>
```

`Link` aggiorna l’URL e cambia vista senza ricaricare tutta la pagina.

---

# Recap finale

## Concetti principali da ricordare

Una SPA usa un solo file HTML principale.

JavaScript controlla gli elementi mostrati nella pagina.

In una SPA non navighiamo sempre tra vere pagine HTML separate.

Spesso navighiamo tra **viste**.

Nei siti tradizionali, ogni pagina può causare una nuova richiesta `GET` al server.

In una SPA, il contenuto cambia dinamicamente senza ricaricare tutta la pagina.

La **History API** permette a JavaScript di controllare la cronologia del browser.

Il routing permette di:

* navigare tra viste;
* far funzionare i bottoni indietro e avanti;
* mostrare una vista specifica in base all’URL;
* evitare reload completi della pagina.

Il routing non è incluso nel core di React.

Per il routing in React usiamo una libreria esterna.

La libreria più comune è:

```txt
react-router-dom
```

Per installarla:

```bash
npm install react-router-dom
```

Componenti principali:

| Componente      | Funzione                          |
| --------------- | --------------------------------- |
| `BrowserRouter` | Abilita il routing nell’app       |
| `Routes`        | Contiene tutte le rotte           |
| `Route`         | Definisce una singola rotta       |
| `Link`          | Permette di navigare senza reload |

---

# Mini glossario

| Termine          | Significato                                        |
| ---------------- | -------------------------------------------------- |
| Routing          | Sistema di navigazione tra viste                   |
| SPA              | Single Page Application                            |
| View             | Vista mostrata da React                            |
| Page             | Pagina HTML tradizionale                           |
| History API      | API del browser per gestire la cronologia          |
| Back Button      | Bottone indietro del browser                       |
| Forward Button   | Bottone avanti del browser                         |
| GET Request      | Richiesta HTTP per ottenere una risorsa            |
| URL              | Indirizzo della pagina o vista                     |
| React Router     | Libreria per il routing in React                   |
| react-router-dom | Pacchetto React Router per applicazioni web        |
| BrowserRouter    | Componente che abilita il routing                  |
| Routes           | Contenitore delle rotte                            |
| Route            | Singola rotta URL-componente                       |
| Link             | Componente per navigare senza ricaricare la pagina |

---


```
