# React - Routing avanzato con React Router

## 1. Argomenti della lezione

In questa lezione vediamo alcuni concetti più avanzati di routing in React.

Gli argomenti principali sono:

* routing annidato;
* redirect di un utente;
* rotte dinamiche;
* differenza tra `BrowserRouter` e `createBrowserRouter`;
* differenza tra `Navigate` e `useNavigate`.

---

# Ripasso: routing in React

## 2. Che cos’è il routing?

Il **routing** permette di navigare tra diverse viste di una Single Page Application.

In una SPA non carichiamo ogni volta una nuova pagina HTML dal server.

React cambia la vista mostrata in base all’URL.

Per esempio:

```txt
/                -> Home
/dashboard       -> Dashboard
/register        -> Register
/user/1          -> Profilo utente con id 1
```

---

## 3. React Router

Per gestire il routing in React usiamo una libreria esterna:

```txt
react-router-dom
```

React Router permette di:

* creare rotte;
* navigare tra viste;
* usare layout condivisi;
* creare rotte annidate;
* usare parametri dinamici negli URL;
* fare redirect;
* gestire il bottone indietro e avanti del browser.

---

# Due modi per definire le rotte

## 4. BrowserRouter e createBrowserRouter

Con React Router possiamo definire le rotte principalmente in due modi:

1. usando `<BrowserRouter>`;
2. usando `createBrowserRouter`.

Il primo approccio è quello più classico.

Il secondo approccio è più nuovo ed è quello consigliato nelle versioni moderne di React Router.

---

# Primo approccio: BrowserRouter

## 5. Il metodo con BrowserRouter

Il metodo con `<BrowserRouter>` definisce le rotte usando componenti JSX.

Esempio semplificato:

```jsx
import { BrowserRouter } from "react-router-dom";

function main() {
   return (
      <BrowserRouter>
         <App />
      </BrowserRouter>
   );
}
```

In questo caso `<BrowserRouter>` avvolge l’applicazione e abilita il routing.

---

## 6. Esempio con Route annidate usando BrowserRouter

```jsx
import { Routes, Route } from "react-router-dom";

function App() {
   return (
      <Routes>
         <Route path="/" element={<RootLayout />}>
            <Route path="dashboard" element={<Dashboard />} />
            <Route path="register" element={<Register />} />
         </Route>
      </Routes>
   );
}
```

In questo esempio:

* `/` mostra `RootLayout`;
* `/dashboard` mostra `Dashboard` dentro `RootLayout`;
* `/register` mostra `Register` dentro `RootLayout`.

---

## 7. Attenzione alla sintassi

Nelle versioni moderne di React Router si usa:

```jsx
element={<Component />}
```

Non si usa più:

```jsx
component={Component}
```

Quindi la forma corretta è:

```jsx
<Route path="dashboard" element={<Dashboard />} />
```

---

# Routing annidato

## 8. Che cos’è il routing annidato?

Il **routing annidato** significa che una rotta può contenere altre rotte figlie.

Questo è utile quando vogliamo avere un layout comune per più pagine o viste.

Per esempio:

```txt
/
├── dashboard
├── register
└── user/:id
```

La rotta principale può mostrare un layout generale, mentre le rotte figlie mostrano il contenuto specifico.

---

## 9. Esempio di layout condiviso

Immaginiamo di avere una struttura così:

```txt
RootLayout
├── Header
├── Main Content
└── Footer
```

Il layout contiene parti comuni come:

* navbar;
* header;
* footer;
* contenitore principale.

Le pagine figlie cambiano solo il contenuto centrale.

---

## 10. Outlet

Per mostrare il contenuto delle rotte figlie dentro un layout, React Router usa:

```txt
Outlet
```

`Outlet` è il punto in cui verrà renderizzata la rotta figlia.

Esempio:

```jsx
import { Outlet } from "react-router-dom";

function RootLayout() {
   return (
      <>
         <header>
            <nav>Navigation</nav>
         </header>

         <main>
            <Outlet />
         </main>

         <footer>Footer</footer>
      </>
   );
}

export default RootLayout;
```

Se l’utente va su `/dashboard`, `Dashboard` verrà mostrato dentro `<Outlet />`.

Se l’utente va su `/register`, `Register` verrà mostrato dentro `<Outlet />`.

---

# Secondo approccio: createBrowserRouter

## 11. Il nuovo approccio con createBrowserRouter

Il metodo con `createBrowserRouter` definisce le rotte usando un array di oggetti JavaScript.

Esempio:

```jsx
import { createBrowserRouter, RouterProvider } from "react-router-dom";

const router = createBrowserRouter([
   {
      path: "/",
      element: <RootLayout />,
      children: [
         {
            path: "dashboard",
            element: <Dashboard />,
         },
         {
            path: "register",
            element: <Register />,
         },
      ],
   },
]);

function App() {
   return <RouterProvider router={router} />;
}

export default App;
```

---

## 12. Spiegazione dell’esempio

```jsx
const router = createBrowserRouter([...]);
```

Qui creiamo il router.

Dentro l’array definiamo le rotte.

Ogni rotta è un oggetto con proprietà come:

* `path`;
* `element`;
* `children`.

---

## 13. Proprietà path

```jsx
path: "/"
```

Indica il percorso della rotta.

Esempi:

```txt
/
/dashboard
/register
/user/:id
```

---

## 14. Proprietà element

```jsx
element: <RootLayout />
```

Indica quale componente deve essere mostrata per quella rotta.

---

## 15. Proprietà children

```jsx
children: [
   {
      path: "dashboard",
      element: <Dashboard />,
   },
]
```

`children` contiene le rotte figlie.

Queste rotte vengono mostrate dentro l’`Outlet` della rotta padre.

---

## 16. RouterProvider

Con `createBrowserRouter`, non usiamo direttamente `<BrowserRouter>`.

Usiamo invece:

```jsx
<RouterProvider router={router} />
```

`RouterProvider` riceve il router creato con `createBrowserRouter`.

---

# BrowserRouter vs createBrowserRouter

## 17. Differenze principali

| Approccio             | Come definisce le rotte                    | Note                                   |
| --------------------- | ------------------------------------------ | -------------------------------------- |
| `BrowserRouter`       | Con componenti JSX come `Routes` e `Route` | Più classico e più semplice all’inizio |
| `createBrowserRouter` | Con array di oggetti JavaScript            | Più moderno e consigliato              |

---

## 18. Quale approccio usare?

Il primo approccio con `<BrowserRouter>` può sembrare più semplice.

Il secondo approccio con `createBrowserRouter` è più moderno ed è quello raccomandato nelle versioni più recenti di React Router.

Con `createBrowserRouter` è più facile gestire funzionalità avanzate come:

* loader;
* actions;
* error elements;
* routing annidato più strutturato;
* data routing.

---

# Routenparameter / Route Parameters

## 19. Cosa sono i route parameters?

I **route parameters**, cioè parametri di rotta, permettono di rendere dinamica una parte dell’URL.

Sono utili quando abbiamo molte pagine simili, ma con dati diversi.

Per esempio, una pagina profilo utente.

---

## 20. Problema: tanti utenti

Immaginiamo di avere 50.000 utenti.

Ogni utente ha:

* un ID;
* una pagina profilo.

Gli URL potrebbero essere:

```txt
/user/1
/user/2
/user/3
/user/50000
```

Non vogliamo creare 50.000 rotte diverse.

---

## 21. Soluzione sbagliata

Questo approccio non è pratico:

```jsx
<Route path="/user/1" element={<UserProfile1 />} />
<Route path="/user/2" element={<UserProfile2 />} />
<Route path="/user/3" element={<UserProfile3 />} />
```

Sarebbe impossibile da mantenere.

---

## 22. Soluzione corretta: rotta dinamica

Creiamo una sola rotta dinamica:

```jsx
<Route path="/user/:id" element={<UserProfile />} />
```

La parte:

```txt
:id
```

è dinamica.

Significa che può cambiare.

Esempi:

```txt
/user/1
/user/25
/user/300
```

Tutte queste URL useranno la stessa componente:

```txt
UserProfile
```

---

# Route dinamiche con createBrowserRouter

## 23. Esempio con createBrowserRouter

```jsx
const router = createBrowserRouter([
   {
      path: "/",
      element: <RootLayout />,
      children: [
         {
            path: "dashboard",
            element: <Dashboard />,
         },
         {
            path: "register",
            element: <Register />,
         },
         {
            path: "user/:id",
            element: <UserProfile />,
         },
      ],
   },
]);
```

In questo caso:

```txt
/user/1
/user/2
/user/abc
```

mostrano tutte la componente `UserProfile`.

---

# useParams

## 24. Come leggere il parametro dinamico?

Per leggere un parametro dinamico dentro una componente usiamo l’hook:

```txt
useParams
```

Esempio:

```jsx
import { useParams } from "react-router-dom";

function UserProfile() {
   const params = useParams();

   return <h1>User ID: {params.id}</h1>;
}

export default UserProfile;
```

Se l’URL è:

```txt
/user/42
```

allora:

```jsx
params.id
```

sarà:

```txt
42
```

---

## 25. Spiegazione di useParams

La rotta è:

```txt
/user/:id
```

La parte dinamica si chiama:

```txt
id
```

Dentro la componente, `useParams()` restituisce un oggetto.

Esempio:

```js
{
   id: "42"
}
```

Attenzione: i parametri dell’URL sono stringhe.

Quindi `id` sarà una stringa, non un numero.

---

# Redirect di un utente

## 26. Cosa significa redirect?

Fare un **redirect** significa mandare l’utente automaticamente verso un’altra rotta.

Esempio:

* l’utente prova ad aprire `/dashboard`;
* non è loggato;
* lo mandiamo automaticamente a `/login`.

Oppure:

* l’utente apre `/old-page`;
* lo mandiamo a `/new-page`.

---

# Navigate vs useNavigate

## 27. Differenza tra Navigate e useNavigate

In React Router abbiamo due strumenti importanti per fare redirect:

1. `Navigate`
2. `useNavigate`

La differenza principale è:

| Strumento     | Tipo       | Dove si usa                                  |
| ------------- | ---------- | -------------------------------------------- |
| `Navigate`    | Componente | Direttamente nel JSX                         |
| `useNavigate` | Hook       | Dentro la logica JavaScript della componente |

Entrambi servono per reindirizzare l’utente verso una rotta specifica.

---

# Navigate

## 28. Che cos’è Navigate?

`Navigate` è una componente di React Router.

Si usa direttamente nel JSX.

Esempio:

```jsx
import { Navigate } from "react-router-dom";

function ProtectedPage({ isLoggedIn }) {
   if (!isLoggedIn) {
      return <Navigate to="/login" />;
   }

   return <h1>Protected Page</h1>;
}

export default ProtectedPage;
```

---

## 29. Spiegazione di Navigate

Se `isLoggedIn` è `false`, la componente ritorna:

```jsx
<Navigate to="/login" />
```

Questo manda l’utente alla pagina:

```txt
/login
```

Se invece `isLoggedIn` è `true`, viene mostrata la pagina protetta.

---

# useNavigate

## 30. Che cos’è useNavigate?

`useNavigate` è un hook di React Router.

Si usa quando vogliamo reindirizzare l’utente dentro una funzione JavaScript.

Per esempio dopo:

* click su un bottone;
* login completato;
* submit di un form;
* cancellazione di un elemento.

---

## 31. Esempio con useNavigate

```jsx
import { useNavigate } from "react-router-dom";

function LoginForm() {
   const navigate = useNavigate();

   function handleLogin() {
      // logica di login
      navigate("/dashboard");
   }

   return (
      <button onClick={handleLogin}>
         Login
      </button>
   );
}

export default LoginForm;
```

---

## 32. Spiegazione di useNavigate

```jsx
const navigate = useNavigate();
```

Qui otteniamo una funzione chiamata `navigate`.

Poi possiamo usarla così:

```jsx
navigate("/dashboard");
```

Questo manda l’utente alla rotta:

```txt
/dashboard
```

---

# Quando usare Navigate e quando useNavigate?

## 33. Regola pratica

Usa `Navigate` quando il redirect dipende da cosa deve essere renderizzato.

Esempio:

```jsx
if (!isLoggedIn) {
   return <Navigate to="/login" />;
}
```

Usa `useNavigate` quando il redirect avviene dopo un’azione o dentro una funzione.

Esempio:

```jsx
function handleSubmit() {
   navigate("/dashboard");
}
```

---

# Esempio completo con routing avanzato

## 34. App.jsx

```jsx
import {
   createBrowserRouter,
   RouterProvider,
   Outlet,
   Link,
   Navigate,
   useNavigate,
   useParams,
} from "react-router-dom";

function RootLayout() {
   return (
      <>
         <header>
            <nav>
               <Link to="/">Home</Link>{" | "}
               <Link to="/dashboard">Dashboard</Link>{" | "}
               <Link to="/register">Register</Link>{" | "}
               <Link to="/user/42">User 42</Link>
            </nav>
         </header>

         <main>
            <Outlet />
         </main>
      </>
   );
}

function Home() {
   return <h1>Home</h1>;
}

function Dashboard() {
   const isLoggedIn = false;

   if (!isLoggedIn) {
      return <Navigate to="/register" />;
   }

   return <h1>Dashboard</h1>;
}

function Register() {
   const navigate = useNavigate();

   function handleRegister() {
      // logica di registrazione
      navigate("/dashboard");
   }

   return (
      <section>
         <h1>Register</h1>
         <button onClick={handleRegister}>Register and go to dashboard</button>
      </section>
   );
}

function UserProfile() {
   const params = useParams();

   return <h1>User Profile ID: {params.id}</h1>;
}

const router = createBrowserRouter([
   {
      path: "/",
      element: <RootLayout />,
      children: [
         {
            index: true,
            element: <Home />,
         },
         {
            path: "dashboard",
            element: <Dashboard />,
         },
         {
            path: "register",
            element: <Register />,
         },
         {
            path: "user/:id",
            element: <UserProfile />,
         },
      ],
   },
]);

function App() {
   return <RouterProvider router={router} />;
}

export default App;
```

---

## 35. Spiegazione dell’esempio completo

In questo esempio usiamo:

* `createBrowserRouter` per creare il router;
* `RouterProvider` per fornire il router all’app;
* `RootLayout` come layout principale;
* `Outlet` per mostrare le rotte figlie;
* `Link` per navigare senza reload;
* `Navigate` per fare redirect nel JSX;
* `useNavigate` per navigare dentro una funzione;
* `useParams` per leggere il parametro dinamico `id`.

---

# La rotta index

## 36. Che cos’è index: true?

Nel router troviamo:

```jsx
{
   index: true,
   element: <Home />,
}
```

Questa è una **index route**.

Significa che `Home` viene mostrata quando siamo esattamente nella rotta padre:

```txt
/
```

Quindi non scriviamo:

```jsx
path: ""
```

ma usiamo:

```jsx
index: true
```

---

# Recap finale

## Concetti principali da ricordare

React Router permette di definire le rotte in due modi principali:

| Metodo                | Descrizione                            |
| --------------------- | -------------------------------------- |
| `BrowserRouter`       | Approccio classico con JSX             |
| `createBrowserRouter` | Approccio moderno con array di oggetti |

Il metodo `createBrowserRouter` è più nuovo e consigliato.

Le rotte annidate permettono di avere layout condivisi.

Per mostrare il contenuto delle rotte figlie si usa:

```jsx
<Outlet />
```

I parametri dinamici permettono di creare rotte variabili.

Esempio:

```txt
/user/:id
```

Questa rotta può gestire URL come:

```txt
/user/1
/user/2
/user/300
```

Per leggere il parametro si usa:

```jsx
const params = useParams();
```

`Navigate` e `useNavigate` servono entrambi per reindirizzare l’utente.

Differenza:

| Strumento     | Uso                            |
| ------------- | ------------------------------ |
| `Navigate`    | Si usa nel JSX                 |
| `useNavigate` | Si usa nella logica JavaScript |

Esempio con `Navigate`:

```jsx
return <Navigate to="/login" />;
```

Esempio con `useNavigate`:

```jsx
const navigate = useNavigate();
navigate("/dashboard");
```

---

# Mini glossario

| Termine             | Significato                                               |
| ------------------- | --------------------------------------------------------- |
| Routing             | Sistema per navigare tra viste                            |
| Nested Routing      | Routing annidato, rotte dentro altre rotte                |
| BrowserRouter       | Metodo classico per abilitare il routing                  |
| createBrowserRouter | Metodo moderno per creare un router                       |
| RouterProvider      | Componente che fornisce il router all’app                 |
| Route Parameter     | Parametro dinamico dentro l’URL                           |
| Dynamic Route       | Rotta con una parte variabile                             |
| `:id`               | Parametro dinamico chiamato id                            |
| useParams           | Hook per leggere parametri dinamici                       |
| Navigate            | Componente per fare redirect nel JSX                      |
| useNavigate         | Hook per navigare tramite JavaScript                      |
| Outlet              | Punto in cui vengono mostrate le rotte figlie             |
| children            | Rotte figlie dentro una rotta padre                       |
| index route         | Rotta mostrata quando siamo esattamente nella rotta padre |

---


