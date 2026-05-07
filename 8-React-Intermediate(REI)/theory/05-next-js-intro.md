# Next.js: Introduzione

## 1. Cos'è Next.js?

**Next.js** è un framework basato su React.

React è una libreria JavaScript che ci permette di costruire interfacce utente tramite componenti.

Next.js parte da React e aggiunge molte funzionalità utili per costruire applicazioni web complete.

Possiamo dire che:

> React ci aiuta a costruire la UI.  
> Next.js ci aiuta a costruire un'intera applicazione web.

---

# 2. React vs Next.js

## React

React è una libreria per costruire interfacce utente.

Con React possiamo creare:

- componenti
- pagine
- interazioni
- state
- form
- UI dinamiche

Però React da solo non include tutto quello che serve per una web app completa.

Per esempio, con React puro dobbiamo spesso configurare separatamente:

- routing
- server-side rendering
- ottimizzazione immagini
- API routes
- gestione del rendering lato server
- struttura del progetto

---

## Next.js

Next.js è un framework che usa React e aggiunge strumenti già pronti.

Con Next.js possiamo gestire:

- pagine
- routing
- layout
- rendering lato server
- rendering lato client
- API routes
- ottimizzazione immagini
- caricamento dati
- SEO
- build di produzione

In pratica, Next.js ci dà una struttura più completa rispetto a React puro.

---

# 3. Perché usare Next.js?

Next.js viene usato perché semplifica la creazione di applicazioni moderne.

I vantaggi principali sono:

- routing già integrato
- buona struttura del progetto
- migliore SEO rispetto a una Single Page Application classica
- supporto al rendering lato server
- possibilità di creare API routes
- ottimizzazione delle immagini
- caricamento dati più organizzato
- possibilità di creare siti statici e dinamici

---

# 4. Cos'è un framework?

Un framework è uno strumento che ci dà una struttura e delle regole per costruire applicazioni.

React è una libreria.

Next.js è un framework.

La differenza principale è:

- una libreria ti dà strumenti da usare liberamente
- un framework ti dà anche una struttura da seguire

Con Next.js molte decisioni sono già organizzate.

Per esempio, il routing è basato sui file.

---

# 5. Routing in Next.js

Una delle caratteristiche più importanti di Next.js è il routing automatico.

In React puro, spesso usiamo una libreria come `react-router-dom`.

In Next.js, invece, le rotte vengono create in base ai file e alle cartelle.

Questo si chiama:

```txt
file-based routing
```

Significa che la struttura delle cartelle determina gli URL dell'applicazione.

---

# 6. App Router

Nelle versioni moderne di Next.js si usa spesso la cartella:

```txt
app/
```

Questa cartella contiene le pagine e i layout dell'applicazione.

Esempio:

```txt
app/
  page.tsx
  about/
    page.tsx
  products/
    page.tsx
```

Questa struttura crea queste rotte:

```txt
/           -> app/page.tsx
/about      -> app/about/page.tsx
/products   -> app/products/page.tsx
```

---

# 7. Il file page.tsx

In Next.js, un file chiamato:

```txt
page.tsx
```

rappresenta una pagina.

Esempio:

```tsx
export default function HomePage() {
  return (
    <main>
      <h1>Homepage</h1>
      <p>Benvenuto nella mia applicazione Next.js.</p>
    </main>
  );
}
```

Questo file, se si trova in:

```txt
app/page.tsx
```

corrisponde alla homepage:

```txt
/
```

---

# 8. Creare una nuova pagina

Per creare una pagina About, possiamo creare questa struttura:

```txt
app/
  about/
    page.tsx
```

Dentro `app/about/page.tsx`:

```tsx
export default function AboutPage() {
  return (
    <main>
      <h1>About</h1>
      <p>Questa è la pagina About.</p>
    </main>
  );
}
```

Questa pagina sarà visibile all'indirizzo:

```txt
/about
```

---

# 9. Layout in Next.js

Next.js permette di creare layout condivisi.

Un layout è una struttura comune che viene usata da più pagine.

Per esempio:

- header
- navbar
- footer
- sidebar
- struttura principale della pagina

Il file principale del layout si chiama:

```txt
layout.tsx
```

Esempio:

```tsx
export default function RootLayout({
  children
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="it">
      <body>
        <header>
          <nav>Navbar</nav>
        </header>

        {children}

        <footer>
          Footer
        </footer>
      </body>
    </html>
  );
}
```

In questo esempio, `children` rappresenta il contenuto della pagina corrente.

---

# 10. Cos'è children?

`children` è una prop speciale di React.

Rappresenta il contenuto che viene inserito dentro un componente.

Esempio:

```tsx
<Layout>
  <HomePage />
</Layout>
```

Dentro il componente `Layout`, `children` sarà:

```tsx
<HomePage />
```

In Next.js, `children` viene usato spesso nei layout.

---

# 11. Componenti in Next.js

Next.js usa componenti React.

Possiamo creare componenti riutilizzabili in una cartella come:

```txt
components/
```

Esempio struttura:

```txt
app/
  page.tsx
components/
  Header.tsx
  Footer.tsx
  ProductCard.tsx
```

Esempio componente:

```tsx
type ProductCardProps = {
  name: string;
  price: number;
};

export default function ProductCard({ name, price }: ProductCardProps) {
  return (
    <div>
      <h2>{name}</h2>
      <p>{price} €</p>
    </div>
  );
}
```

Uso nella pagina:

```tsx
import ProductCard from "@/components/ProductCard";

export default function ProductsPage() {
  return (
    <main>
      <h1>Prodotti</h1>

      <ProductCard name="Laptop" price={999} />
    </main>
  );
}
```

---

# 12. Import alias con @

In molti progetti Next.js si usa `@` come scorciatoia per la cartella `src` oppure per la root del progetto.

Esempio:

```tsx
import Header from "@/components/Header";
```

Questo è più pulito rispetto a:

```tsx
import Header from "../../components/Header";
```

L'alias `@` dipende dalla configurazione del progetto.

---

# 13. Struttura base di un progetto Next.js

Una struttura possibile è:

```txt
my-next-app/
  app/
    layout.tsx
    page.tsx
    about/
      page.tsx
  components/
    Header.tsx
    Footer.tsx
  public/
    images/
  package.json
  tsconfig.json
  next.config.js
```

---

# 14. La cartella public

La cartella `public` contiene file statici.

Per esempio:

- immagini
- icone
- file scaricabili
- favicon

Esempio:

```txt
public/
  logo.png
```

Possiamo usare l'immagine così:

```tsx
<img src="/logo.png" alt="Logo" />
```

Il percorso parte direttamente dalla root pubblica.

---

# 15. Link tra pagine

In Next.js possiamo navigare tra pagine usando il componente `Link`.

Esempio:

```tsx
import Link from "next/link";

export default function Header() {
  return (
    <nav>
      <Link href="/">Home</Link>
      <Link href="/about">About</Link>
      <Link href="/products">Products</Link>
    </nav>
  );
}
```

`Link` è preferibile rispetto al normale tag `<a>` per la navigazione interna.

---

# 16. Immagini in Next.js

Next.js offre anche un componente speciale per le immagini:

```tsx
import Image from "next/image";
```

Esempio:

```tsx
import Image from "next/image";

export default function Logo() {
  return (
    <Image
      src="/logo.png"
      alt="Logo"
      width={200}
      height={100}
    />
  );
}
```

Il componente `Image` aiuta a ottimizzare le immagini.

---

# 17. Server Components e Client Components

Nel nuovo sistema di Next.js con App Router, i componenti sono Server Components di default.

Questo significa che vengono renderizzati sul server.

Se un componente deve usare interazioni lato client, come:

- `useState`
- `useEffect`
- eventi come `onClick`
- accesso al browser
- localStorage

allora deve essere un Client Component.

Per indicarlo, scriviamo in cima al file:

```tsx
"use client";
```

---

# 18. Esempio di Client Component

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

Senza `"use client"`, questo componente non potrebbe usare `useState`.

---

# 19. Quando serve "use client"?

Serve quando il componente usa:

- `useState`
- `useEffect`
- `useReducer`
- `useContext` con stato interattivo
- eventi come `onClick`, `onChange`, `onSubmit`
- `localStorage`
- `window`
- `document`

Esempio:

```tsx
"use client";
```

deve essere la prima riga del file.

---

# 20. Server Component

Un Server Component è utile quando il componente non ha interazioni dirette con l'utente.

Esempio:

```tsx
export default function AboutPage() {
  return (
    <main>
      <h1>About</h1>
      <p>Questa pagina non usa state o eventi.</p>
    </main>
  );
}
```

Qui non serve `"use client"`.

---

# 21. Caricamento dei dati

Next.js permette di caricare dati in modo diverso rispetto a React puro.

Possiamo caricare dati:

- lato server
- lato client
- da API esterne
- dal database
- da file statici

Esempio semplice in un Server Component:

```tsx
async function getProducts() {
  const response = await fetch("https://example.com/api/products");
  const products = await response.json();

  return products;
}

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

---

# 22. API Routes

Next.js permette anche di creare endpoint API dentro il progetto.

Con App Router possiamo creare una route API così:

```txt
app/
  api/
    hello/
      route.ts
```

Esempio:

```ts
export async function GET() {
  return Response.json({
    message: "Ciao da Next.js"
  });
}
```

Questa API sarà disponibile a:

```txt
/api/hello
```

---

# 23. Metodo GET e POST

Nelle API routes possiamo gestire diversi metodi HTTP.

Esempio GET:

```ts
export async function GET() {
  return Response.json({ message: "Richiesta GET" });
}
```

Esempio POST:

```ts
export async function POST(request: Request) {
  const body = await request.json();

  return Response.json({
    message: "Richiesta POST ricevuta",
    data: body
  });
}
```

---

# 24. SEO

Next.js è molto usato anche perché aiuta con la SEO.

SEO significa:

```txt
Search Engine Optimization
```

In italiano:

```txt
ottimizzazione per i motori di ricerca
```

Un sito con buona SEO è più facile da leggere per Google e altri motori di ricerca.

Next.js aiuta perché può generare pagine già pronte sul server, invece di aspettare che tutto venga creato solo nel browser.

---

# 25. Metadata

In Next.js possiamo definire i metadata di una pagina.

Esempio:

```tsx
export const metadata = {
  title: "Homepage",
  description: "Questa è la homepage della mia app Next.js"
};
```

I metadata sono importanti per:

- titolo della pagina
- descrizione
- SEO
- anteprime sui social

---

# 26. Differenza tra sito statico e dinamico

## Sito statico

Un sito statico ha contenuti che cambiano raramente.

Esempi:

- portfolio
- landing page
- blog semplice
- pagina informazioni

---

## Sito dinamico

Un sito dinamico ha contenuti che cambiano spesso o dipendono dall'utente.

Esempi:

- ecommerce
- dashboard
- area utente
- social network
- pannello admin

Next.js può essere usato sia per siti statici sia per siti dinamici.

---

# 27. Comandi principali

Per creare o lavorare su un progetto Next.js, di solito usiamo NPM.

## Installare le dipendenze

```bash
npm install
```

## Avviare il progetto in sviluppo

```bash
npm run dev
```

## Creare la build di produzione

```bash
npm run build
```

## Avviare la versione di produzione

```bash
npm start
```

---

# 28. File importanti

## package.json

Contiene:

- dipendenze
- script
- informazioni sul progetto

Esempio script:

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  }
}
```

---

## next.config.js

Contiene configurazioni di Next.js.

Esempio:

```js
const nextConfig = {};

module.exports = nextConfig;
```

---

## tsconfig.json

Contiene configurazioni di TypeScript.

---

# 29. Errori comuni all'inizio

## Dimenticare "use client"

Se usi `useState`, `useEffect` o `onClick` e ricevi un errore, probabilmente hai dimenticato:

```tsx
"use client";
```

---

## Mettere page.tsx nel posto sbagliato

Ricorda:

```txt
app/about/page.tsx
```

crea:

```txt
/about
```

---

## Usare <a> invece di Link per navigazione interna

Per navigare tra pagine interne è meglio usare:

```tsx
<Link href="/about">About</Link>
```

---

## Dimenticare export default

Una pagina deve esportare un componente di default.

Esempio:

```tsx
export default function HomePage() {
  return <h1>Home</h1>;
}
```

---

# 30. Mini esempio completo

Struttura:

```txt
app/
  layout.tsx
  page.tsx
  about/
    page.tsx
components/
  Header.tsx
```

---

## app/layout.tsx

```tsx
import Header from "@/components/Header";

export default function RootLayout({
  children
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="it">
      <body>
        <Header />
        {children}
      </body>
    </html>
  );
}
```

---

## components/Header.tsx

```tsx
import Link from "next/link";

export default function Header() {
  return (
    <header>
      <nav>
        <Link href="/">Home</Link>
        <Link href="/about">About</Link>
      </nav>
    </header>
  );
}
```

---

## app/page.tsx

```tsx
export default function HomePage() {
  return (
    <main>
      <h1>Homepage</h1>
      <p>Benvenuto nella mia prima app Next.js.</p>
    </main>
  );
}
```

---

## app/about/page.tsx

```tsx
export default function AboutPage() {
  return (
    <main>
      <h1>About</h1>
      <p>Questa è la pagina About.</p>
    </main>
  );
}
```

---

# 31. Schema mentale

```txt
Next.js
= React + strumenti extra
= routing automatico
= layout
= pagine
= server rendering
= API routes
= SEO migliore
```

---

# 32. Schema App Router

```txt
app/
  layout.tsx      -> layout principale
  page.tsx        -> homepage /
  about/
    page.tsx      -> /about
  products/
    page.tsx      -> /products
  api/
    hello/
      route.ts    -> /api/hello
```

---

# 33. Quando usare Next.js?

Next.js è utile quando vogliamo creare:

- siti web moderni
- portfolio
- blog
- ecommerce
- dashboard
- applicazioni full-stack
- siti con SEO importante
- progetti React più strutturati

---

# 34. Parole chiave da memorizzare

## Framework

Strumento che dà struttura e regole per costruire applicazioni.

## Routing

Sistema che collega URL e pagine.

## File-based routing

Routing basato su file e cartelle.

## App Router

Sistema moderno di routing di Next.js basato sulla cartella `app`.

## Page

File che rappresenta una pagina.

## Layout

Struttura condivisa tra più pagine.

## Server Component

Componente renderizzato sul server.

## Client Component

Componente che può usare interazioni lato browser.

## API Route

Endpoint backend creato dentro Next.js.

## SEO

Ottimizzazione per i motori di ricerca.

---

# 35. Riassunto finale

Next.js è un framework basato su React.

React ci permette di costruire componenti e interfacce utente.

Next.js aggiunge strumenti per creare applicazioni web complete.

Con Next.js possiamo gestire:

- pagine
- routing
- layout
- componenti
- immagini
- metadata
- API routes
- caricamento dati
- rendering lato server
- SEO

Il concetto più importante da ricordare è:

> Next.js è React con una struttura più completa per costruire applicazioni web moderne.

Per iniziare, la cosa fondamentale è capire la struttura della cartella `app`:

```txt
app/page.tsx
```

crea la homepage:

```txt
/
```

mentre:

```txt
app/about/page.tsx
```

crea la pagina:

```txt
/about
```

Next.js rende il routing più semplice perché le pagine vengono create direttamente dalla struttura delle cartelle.
