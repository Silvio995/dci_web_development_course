# React e TypeScript: costruire un piccolo Code Editor nel browser

## 1. Promemoria importante

Stiamo iniziando ad affrontare argomenti sempre più complessi.

È normale non capire tutto subito.  
La cosa più importante è **non arrendersi** e continuare a provare.

Quando hai tempo extra, torna alle basi e fai esercizio.  
Le basi sono fondamentali perché ti aiutano a capire meglio anche gli argomenti avanzati.

Concentrati anche sui temi che ti piacciono di più.  
La sera o nel weekend prova a costruire piccoli progetti con le tecnologie che ti interessano.

Più costruisci, più impari.

Gli argomenti che studiamo sono importanti, ma la cosa più importante è continuare ad andare avanti.

> Questo percorso è una maratona, non uno sprint.

Lavora il più possibile, ma mantieni equilibrio.  
Ricordati di riposare, dormire abbastanza e prenderti cura della tua salute fisica e mentale.

---

# 2. Argomento della lezione

## React e TypeScript

In questa lezione lavoriamo con:

- React
- TypeScript
- State
- Props
- localStorage
- Tipizzazione dei dati
- Union Types
- Generics

L'obiettivo pratico della lezione è costruire un piccolo **code editor nel browser**, simile a CodePen.

---

# 3. Progetto della lezione: mini Code Editor

## Cosa vogliamo costruire?

Vogliamo creare una piccola applicazione in cui l'utente può scrivere:

- HTML
- CSS

e vedere subito una **live preview**, cioè un'anteprima aggiornata nel browser.

In pratica, l'utente scrive codice e vede immediatamente il risultato.

---

## Funzionalità principali

Il nostro editor dovrà permettere di:

1. Scrivere codice HTML
2. Scrivere codice CSS
3. Visualizzare il risultato in tempo reale
4. Salvare i dati nel browser usando `localStorage`
5. Usare TypeScript per tipizzare correttamente props e state

---

# 4. Cos'è localStorage?

`localStorage` è una memoria del browser.

Serve per salvare dati direttamente nel browser dell'utente.

Questo significa che, se l'utente scrive del codice nell'editor, possiamo salvarlo nel browser e recuperarlo anche dopo un refresh della pagina.

Esempio:

```ts
localStorage.setItem("html", htmlCode);
```

Con questa istruzione salviamo il valore di `htmlCode` dentro localStorage con la chiave `"html"`.

Per recuperare il valore:

```ts
const savedHtml = localStorage.getItem("html");
```

---

# 5. TypeScript in React

TypeScript ci aiuta a scrivere codice più sicuro.

Con TypeScript possiamo specificare il tipo dei dati che usiamo.

Per esempio:

```ts
const name: string = "Maria";
const age: number = 30;
const isOpen: boolean = true;
```

In React, TypeScript è molto utile soprattutto per:

- tipizzare lo state
- tipizzare le props
- tipizzare oggetti complessi
- evitare errori quando passiamo dati tra componenti

---

# 6. useState e inferenza dei tipi

In React usiamo `useState` per gestire lo stato di un componente.

Esempio:

```ts
const [menuOpen, setMenuOpen] = useState(false);
```

In questo caso TypeScript capisce automaticamente che `menuOpen` è un booleano.

Questo perché il valore iniziale è `false`.

Quindi TypeScript interpreta il codice così:

```ts
menuOpen: boolean
```

In questo caso non abbiamo bisogno di scrivere il tipo esplicitamente.

---

# 7. Quando TypeScript capisce il tipo da solo

TypeScript riesce a dedurre il tipo quando il valore iniziale è chiaro.

Esempi:

```ts
const [count, setCount] = useState(0);
```

Qui TypeScript capisce che `count` è un numero.

```ts
const [name, setName] = useState("");
```

Qui TypeScript capisce che `name` è una stringa.

```ts
const [isLoading, setIsLoading] = useState(false);
```

Qui TypeScript capisce che `isLoading` è un booleano.

---

# 8. Quando dobbiamo scrivere il tipo esplicitamente

A volte TypeScript non può capire da solo quale sarà il tipo corretto dello state.

Questo succede spesso quando il valore iniziale è `null`.

Esempio:

```ts
const [user, setUser] = useState(null);
```

In questo caso TypeScript capisce solo che `user` è `null`.

Però noi magari vogliamo dire che `user` può essere:

- un oggetto `User`
- oppure `null`

Allora dobbiamo scrivere il tipo in modo esplicito.

---

# 9. Esempio con tipo User

Prima definiamo il tipo `User`:

```ts
type User = {
  id: string;
  name: string;
};
```

Poi usiamo questo tipo dentro `useState`:

```ts
const [user, setUser] = useState<User | null>(null);
```

Questo significa:

> La variabile `user` può essere un oggetto di tipo `User` oppure può essere `null`.

---

# 10. Union Type

Il simbolo `|` in TypeScript significa “oppure”.

Questo si chiama **Union Type**.

Esempio:

```ts
string | number
```

Significa:

> Questo valore può essere una stringa oppure un numero.

Esempio completo:

```ts
type User = {
  id: string | number;
  name: string;
};
```

In questo caso la proprietà `id` può essere:

- una stringa
- oppure un numero

---

# 11. Esempi validi e non validi

```ts
type User = {
  id: string | number;
  name: string;
};
```

Esempio valido:

```ts
const user1 = {
  id: 12345,
  name: "Veera"
};
```

È valido perché `id` è un numero.

Esempio valido:

```ts
const user2 = {
  id: "rama1",
  name: "Rauli"
};
```

È valido perché `id` è una stringa.

Esempio non valido:

```ts
const user3 = {
  id: true,
  name: "Alpuli"
};
```

Non è valido perché `id` non può essere un booleano.

Il tipo `id` accetta solo:

```ts
string | number
```

Quindi `true` non è permesso.

---

# 12. useState con Union Type

Esempio:

```ts
const [user, setUser] = useState<User | null>(null);
```

Qui stiamo usando un Union Type perché `user` può avere due stati diversi:

1. All'inizio può essere `null`
2. Dopo il login o dopo il caricamento dei dati può diventare un oggetto `User`

Esempio:

```ts
setUser({
  id: "abc123",
  name: "Maria"
});
```

Ora `user` non è più `null`, ma contiene un oggetto.

---

# 13. Controllare se user è null

Quando usiamo un valore che può essere `null`, dobbiamo controllarlo prima.

Esempio:

```tsx
if (user === null) {
  return <p>Nessun utente trovato</p>;
}
```

Oppure:

```tsx
return <p>Ciao {user.name}</p>;
```

Questo funziona solo se TypeScript è sicuro che `user` non sia `null`.

Esempio completo:

```tsx
if (!user) {
  return <p>Loading...</p>;
}

return <p>Ciao {user.name}</p>;
```

---

# 14. Generics

Nel codice:

```ts
useState<User | null>(null);
```

la parte dentro le parentesi angolari è questa:

```ts
<User | null>
```

Questa sintassi si chiama **Generic**.

I Generics permettono di specificare il tipo che una funzione deve usare.

In questo caso stiamo dicendo a `useState`:

> Questo state deve contenere un valore di tipo `User` oppure `null`.

---

# 15. Generics spiegati in modo semplice

Un Generic è come un'informazione aggiuntiva che passiamo a una funzione per dirle quale tipo deve usare.

Esempio:

```ts
useState<string>("");
```

Significa:

> Questo state sarà una stringa.

Esempio:

```ts
useState<number>(0);
```

Significa:

> Questo state sarà un numero.

Esempio:

```ts
useState<boolean>(false);
```

Significa:

> Questo state sarà un booleano.

Esempio:

```ts
useState<User | null>(null);
```

Significa:

> Questo state sarà un oggetto `User` oppure `null`.

---

# 16. Quando usare i Generics con useState?

Non sempre dobbiamo scrivere il Generic.

Possiamo evitarlo quando TypeScript riesce a capire il tipo da solo.

Esempio:

```ts
const [count, setCount] = useState(0);
```

Qui non serve scrivere:

```ts
useState<number>(0);
```

perché TypeScript capisce già che `count` è un numero.

Ma dobbiamo usare il Generic quando il tipo non è chiaro.

Esempio importante:

```ts
const [user, setUser] = useState<User | null>(null);
```

Qui serve perché il valore iniziale è `null`, ma in futuro vogliamo salvare anche un oggetto `User`.

---

# 17. Tipizzare le Props

In React, le props sono dati che passiamo da un componente a un altro.

Esempio:

```tsx
type ButtonProps = {
  text: string;
  disabled: boolean;
};
```

Poi usiamo il tipo nel componente:

```tsx
function Button({ text, disabled }: ButtonProps) {
  return <button disabled={disabled}>{text}</button>;
}
```

In questo modo TypeScript sa che:

- `text` deve essere una stringa
- `disabled` deve essere un booleano

Se passiamo un valore sbagliato, TypeScript ci segnala l'errore.

---

# 18. Esempio collegato al Code Editor

Nel nostro piccolo editor potremmo avere uno state per il codice HTML:

```ts
const [html, setHtml] = useState("");
```

TypeScript capisce che `html` è una stringa.

Poi uno state per il CSS:

```ts
const [css, setCss] = useState("");
```

Anche qui TypeScript capisce che `css` è una stringa.

Se vogliamo salvare il codice nel localStorage, possiamo fare:

```ts
localStorage.setItem("html", html);
localStorage.setItem("css", css);
```

E per recuperarlo:

```ts
const savedHtml = localStorage.getItem("html");
const savedCss = localStorage.getItem("css");
```

Attenzione: `localStorage.getItem()` può restituire una stringa oppure `null`.

Quindi il tipo è:

```ts
string | null
```

Questo è un altro esempio di Union Type.

---

# 19. Perché localStorage.getItem può essere null?

Perché la chiave che cerchiamo potrebbe non esistere.

Esempio:

```ts
const savedHtml = localStorage.getItem("html");
```

Se `"html"` non esiste nel localStorage, il risultato sarà:

```ts
null
```

Per questo dobbiamo gestire il caso in cui il valore sia nullo.

Esempio:

```ts
const savedHtml = localStorage.getItem("html") || "";
```

In questo modo, se non esiste nessun valore salvato, usiamo una stringa vuota.

---

# 20. Mini esempio completo

```tsx
import { useState } from "react";

function CodeEditor() {
  const [html, setHtml] = useState("");
  const [css, setCss] = useState("");

  function saveCode() {
    localStorage.setItem("html", html);
    localStorage.setItem("css", css);
  }

  return (
    <div>
      <h1>Mini Code Editor</h1>

      <textarea
        value={html}
        onChange={(event) => setHtml(event.target.value)}
        placeholder="Scrivi HTML"
      />

      <textarea
        value={css}
        onChange={(event) => setCss(event.target.value)}
        placeholder="Scrivi CSS"
      />

      <button onClick={saveCode}>Salva</button>
    </div>
  );
}

export default CodeEditor;
```

---

# 21. Concetti chiave della lezione

## React

React ci permette di costruire interfacce utente usando componenti.

## TypeScript

TypeScript aggiunge i tipi a JavaScript e ci aiuta a scrivere codice più sicuro.

## useState

`useState` serve per gestire dati che cambiano nel componente.

## Inferenza dei tipi

TypeScript spesso capisce il tipo automaticamente dal valore iniziale.

## Union Type

Un Union Type permette a una variabile di avere più tipi possibili.

Esempio:

```ts
string | number
```

## Generic

Un Generic permette di specificare il tipo che una funzione deve usare.

Esempio:

```ts
useState<User | null>(null);
```

## localStorage

`localStorage` permette di salvare dati nel browser.

---

# 22. Schema veloce da memorizzare

```ts
const [menuOpen, setMenuOpen] = useState(false);
```

TypeScript capisce:

```ts
menuOpen: boolean
```

---

```ts
const [count, setCount] = useState(0);
```

TypeScript capisce:

```ts
count: number
```

---

```ts
const [name, setName] = useState("");
```

TypeScript capisce:

```ts
name: string
```

---

```ts
const [user, setUser] = useState<User | null>(null);
```

Qui dobbiamo specificare il tipo perché il valore iniziale è `null`.

---

# 23. Frase importante da ricordare

Quando il valore iniziale è chiaro, TypeScript può dedurre il tipo da solo.

Quando il valore iniziale è `null`, spesso dobbiamo specificare il tipo usando un Generic.

Esempio:

```ts
useState<User | null>(null);
```

---

# 24. Riassunto finale

In questa lezione abbiamo visto come usare React insieme a TypeScript per costruire un piccolo editor di codice nel browser.

Abbiamo imparato che TypeScript può dedurre automaticamente alcuni tipi, ma in alcuni casi dobbiamo specificarli noi.

Abbiamo studiato:

- `useState`
- inferenza dei tipi
- Union Types
- Generics
- Props
- State
- localStorage

Il progetto pratico è un mini Code Editor in cui l'utente può scrivere HTML e CSS, vedere una preview live e salvare il codice nel browser.

La cosa più importante è continuare a praticare.

Non serve capire tutto al primo colpo.  
Più progetti costruisci, più i concetti diventano chiari.
