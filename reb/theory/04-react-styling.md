# React - Styling delle applicazioni e CSS Modules

## 1. Styling nelle applicazioni React

Lo **styling** nelle applicazioni React può essere fatto in molti modi diversi.

Il modo più semplice è usare normale **CSS**, come abbiamo visto nel template iniziale di React.

Per esempio, possiamo avere un file CSS generale:

```css
body {
   font-family: Arial, sans-serif;
}

.button {
   background-color: black;
   color: white;
}
```

E poi importarlo nel nostro progetto React.

---

# CSS normale in React

## 2. Usare CSS classico

In React possiamo usare i file `.css` come in un normale progetto frontend.

Esempio:

```css
.card {
   padding: 1rem;
   border: 1px solid #ddd;
   border-radius: 8px;
}
```

Poi nella componente React usiamo `className`:

```jsx
function Card() {
   return (
      <div className="card">
         <h2>Product Card</h2>
         <p>This is a simple card.</p>
      </div>
   );
}
```

Ricorda: in JSX non si usa `class`, ma si usa `className`.

---

## 3. Importare un file CSS

Possiamo importare un file CSS dentro un file JavaScript o JSX.

Esempio:

```jsx
import "./Card.css";

function Card() {
   return (
      <div className="card">
         <h2>Product Card</h2>
      </div>
   );
}

export default Card;
```

In questo modo gli stili definiti in `Card.css` vengono caricati nella nostra applicazione.

---

# SCSS in React

## 4. Che cos’è SCSS?

Oltre al CSS normale, possiamo usare anche **SCSS**.

SCSS è una versione più potente del CSS.

Permette di usare funzionalità aggiuntive come:

* variabili;
* nesting;
* file parziali;
* mixins;
* funzioni.

SCSS rende il codice CSS più organizzato, soprattutto nei progetti più grandi.

---

## 5. Esempio di SCSS

```scss
$primary-color: #222;
$spacing: 1rem;

.card {
   padding: $spacing;
   color: $primary-color;

   h2 {
      margin-bottom: 0.5rem;
   }
}
```

In questo esempio:

* `$primary-color` è una variabile;
* `$spacing` è una variabile;
* `h2` è scritto dentro `.card`, quindi lo stile è annidato.

Questo si chiama **nesting**.

---

## 6. Usare SCSS in React

Usare SCSS in React è molto semplice.

Di solito basta installare `sass`:

```bash
npm install sass
```

Poi possiamo creare file con estensione `.scss`.

Esempio:

```txt
Card.scss
```

E importarli nella componente:

```jsx
import "./Card.scss";
```

---

# Organizzare gli stili per componente

## 7. Uno stylesheet per componente

Un metodo molto comune per organizzare gli stili in React è usare uno stylesheet per ogni componente.

Per esempio:

```txt
src/
   components/
      Card/
         Card.jsx
         Card.css
      Button/
         Button.jsx
         Button.css
      Header/
         Header.jsx
         Header.css
```

Ogni componente ha il suo file CSS.

---

## 8. Perché usare uno stylesheet per componente?

Questo approccio è utile perché:

* mantiene gli stili organizzati;
* rende il progetto più facile da leggere;
* rende più semplice trovare gli stili di una componente;
* rende più semplice modificare una componente senza cercare in un file CSS enorme;
* aiuta a mantenere il codice più pulito e manutenibile.

Per esempio, se dobbiamo modificare lo stile di `Button`, sappiamo che probabilmente lo troveremo in:

```txt
Button.css
```

---

# Il problema delle collisioni dei nomi delle classi

## 9. Cosa sono le collisioni dei class names?

Anche se usiamo un file CSS per ogni componente, in React il CSS normale resta globale.

Questo significa che una classe definita in un file può influenzare anche elementi di un’altra componente.

Il problema nasce quando due componenti usano lo stesso nome di classe.

---

## 10. Esempio di collisione

Immaginiamo di avere una componente `Article` e una componente `Comment`.

Nel file CSS dell’articolo scriviamo:

```css
.highlight {
   background: yellow;
}
```

Nel file CSS del commento scriviamo:

```css
.highlight {
   background: red;
}
```

Entrambe le componenti usano la classe:

```txt
.highlight
```

Il problema è che il CSS è globale.

Quindi una regola può sovrascrivere l’altra.

Il risultato può essere imprevedibile:

* l’articolo potrebbe diventare rosso invece che giallo;
* il commento potrebbe diventare giallo invece che rosso;
* dipende dall’ordine in cui i CSS vengono caricati.

---

## 11. Perché questo è un problema?

Le collisioni dei nomi delle classi rendono il progetto più difficile da gestire.

Possono causare bug visivi difficili da trovare.

Per esempio, modifichi lo stile di una componente, ma senza volerlo cambi anche lo stile di un’altra componente.

Questo succede perché entrambe usano lo stesso nome di classe.

---

# Soluzione semplice: classe wrapper

## 12. Avvolgere gli stili in una classe specifica

Una possibile soluzione è racchiudere tutto il CSS di una componente dentro una classe specifica.

Esempio per `Article`:

```css
.article .highlight {
   background: yellow;
}
```

Esempio per `Comment`:

```css
.comment .highlight {
   background: red;
}
```

In JSX:

```jsx
function Article() {
   return (
      <article className="article">
         <p className="highlight">Important article text</p>
      </article>
   );
}
```

```jsx
function Comment() {
   return (
      <div className="comment">
         <p className="highlight">Important comment text</p>
      </div>
   );
}
```

In questo modo `.highlight` viene limitata al contesto della componente.

---

## 13. Limite della classe wrapper

La classe wrapper aiuta, ma non risolve completamente il problema.

Bisogna comunque ricordarsi di scrivere sempre selettori specifici.

In progetti grandi può diventare facile dimenticare una classe o creare nomi troppo simili.

Per questo esiste una soluzione più sicura: i **CSS Modules**.

---

# CSS Modules

## 14. Che cosa sono i CSS Modules?

I **CSS Modules** sono un modo per evitare collisioni tra i nomi delle classi CSS.

Permettono di limitare il CSS a una componente specifica.

In pratica, una classe scritta in un CSS Module non diventa globale, ma viene trasformata in un nome unico.

Questo evita che due componenti con lo stesso nome di classe entrino in conflitto.

---

## 15. A cosa servono i CSS Modules?

I CSS Modules servono per:

* evitare collisioni tra nomi di classi;
* limitare lo stile a una componente specifica;
* evitare CSS globale quando non necessario;
* evitare l’uso eccessivo di inline styles;
* rendere gli stili più sicuri e manutenibili.

---

## 16. Come si riconosce un CSS Module?

Un file CSS Module ha questa forma:

```txt
NomeComponente.module.css
```

Esempio:

```txt
Button.module.css
Card.module.css
Article.module.css
```

Se usiamo SCSS, possiamo avere:

```txt
Button.module.scss
Card.module.scss
```

---

## 17. Esempio di CSS Module

File `Button.module.css`:

```css
.button {
   background-color: black;
   color: white;
   padding: 0.75rem 1rem;
   border: none;
   border-radius: 8px;
}
```

File `Button.jsx`:

```jsx
import styles from "./Button.module.css";

function Button() {
   return <button className={styles.button}>Click me</button>;
}

export default Button;
```

---

## 18. Spiegazione dell’esempio

Con CSS normale avremmo scritto:

```jsx
<button className="button">Click me</button>
```

Con CSS Modules invece scriviamo:

```jsx
<button className={styles.button}>Click me</button>
```

Perché importiamo il file CSS come oggetto:

```jsx
import styles from "./Button.module.css";
```

`styles.button` contiene il nome della classe generato automaticamente da React/Vite.

Questo nome sarà unico e non entrerà in conflitto con altre classi `.button` in altre componenti.

---

## 19. Esempio pratico di classi uguali senza conflitto

File `Article.module.css`:

```css
.highlight {
   background: yellow;
}
```

File `Comment.module.css`:

```css
.highlight {
   background: red;
}
```

Anche se entrambe le classi si chiamano `.highlight`, non c’è conflitto.

Questo perché CSS Modules genera nomi unici per ogni componente.

---

## 20. Uso nella componente Article

```jsx
import styles from "./Article.module.css";

function Article() {
   return (
      <article>
         <p className={styles.highlight}>Important article text</p>
      </article>
   );
}

export default Article;
```

---

## 21. Uso nella componente Comment

```jsx
import styles from "./Comment.module.css";

function Comment() {
   return (
      <div>
         <p className={styles.highlight}>Important comment text</p>
      </div>
   );
}

export default Comment;
```

In questo modo:

* `Article` usa il suo `.highlight`;
* `Comment` usa il suo `.highlight`;
* le due classi non si influenzano tra loro.

---

# CSS globale vs CSS Modules

## 22. CSS globale

Il CSS globale è utile per stili generali dell’applicazione.

Esempi:

* reset CSS;
* font globali;
* stile del `body`;
* variabili CSS globali;
* layout base dell’app.

Esempio:

```css
body {
   margin: 0;
   font-family: Arial, sans-serif;
}
```

---

## 23. CSS Modules

I CSS Modules sono utili per gli stili specifici di una componente.

Esempi:

* bottone;
* card;
* navbar;
* form;
* lista prodotti;
* commento;
* articolo.

In generale:

* usa CSS globale per regole generali;
* usa CSS Modules per componenti specifiche.

---

# Inline styles

## 24. Cosa sono gli inline styles?

Gli inline styles sono stili scritti direttamente nell’elemento JSX.

Esempio:

```jsx
function Button() {
   return (
      <button style={{ backgroundColor: "black", color: "white" }}>
         Click me
      </button>
   );
}
```

In React gli inline styles si scrivono come oggetto JavaScript.

Per esempio:

```jsx
style={{ backgroundColor: "black" }}
```

Nota importante: in React le proprietà CSS con trattino diventano camelCase.

CSS normale:

```css
background-color
```

React inline style:

```jsx
backgroundColor
```

---

## 25. Perché evitare troppi inline styles?

Gli inline styles possono essere utili in alcuni casi, ma non sono sempre la scelta migliore.

Sono meno comodi quando:

* ci sono tanti stili;
* vogliamo usare pseudo-classi come `:hover`;
* vogliamo usare media queries;
* vogliamo mantenere separata la struttura dallo stile;
* il componente diventa troppo pieno e difficile da leggere.

Per questo spesso è meglio usare CSS, SCSS o CSS Modules.

---

# Esempio completo con CSS Module

## File `ProductCard.module.css`

```css
.card {
   padding: 1rem;
   border: 1px solid #ddd;
   border-radius: 12px;
}

.title {
   font-size: 1.5rem;
   margin-bottom: 0.5rem;
}

.description {
   color: #555;
}

.button {
   padding: 0.75rem 1rem;
   border: none;
   border-radius: 8px;
   cursor: pointer;
}
```

---

## File `ProductCard.jsx`

```jsx
import styles from "./ProductCard.module.css";

function ProductCard() {
   return (
      <article className={styles.card}>
         <h2 className={styles.title}>Apple iPad</h2>
         <p className={styles.description}>Cool tablet device thing</p>
         <button className={styles.button}>Add to cart</button>
      </article>
   );
}

export default ProductCard;
```

---

## Spiegazione

In questo esempio:

* `styles.card` applica lo stile della card;
* `styles.title` applica lo stile del titolo;
* `styles.description` applica lo stile della descrizione;
* `styles.button` applica lo stile del bottone.

Ogni classe è locale alla componente `ProductCard`.

Questo significa che un’altra componente può avere anche lei una classe `.button`, ma non entrerà in conflitto con questa.

---

# Recap finale

## Concetti principali da ricordare

Lo styling in React può essere fatto in molti modi.

Il modo più semplice è usare normale CSS.

SCSS è una versione più potente del CSS e permette di usare variabili, nesting e altre funzionalità.

Un metodo comune in React è usare uno stylesheet per ogni componente.

Questo aiuta a mantenere il codice organizzato e più facile da modificare.

Il CSS normale però è globale, quindi può causare collisioni tra nomi di classi.

Esempio di collisione:

```css
.highlight {
   background: yellow;
}
```

```css
.highlight {
   background: red;
}
```

Se due componenti usano la stessa classe, una regola può sovrascrivere l’altra.

I CSS Modules risolvono questo problema perché rendono le classi locali alla componente.

Un file CSS Module si chiama così:

```txt
ComponentName.module.css
```

E si importa così:

```jsx
import styles from "./ComponentName.module.css";
```

Poi si usa così:

```jsx
<div className={styles.container}></div>
```

I CSS Modules aiutano a:

* evitare collisioni di classi;
* limitare gli stili a una componente;
* evitare troppo CSS globale;
* evitare troppi inline styles;
* rendere il progetto più manutenibile.

---

# Mini glossario

| Termine              | Significato                                              |
| -------------------- | -------------------------------------------------------- |
| Styling              | Applicazione degli stili grafici                         |
| CSS                  | Linguaggio per definire lo stile delle pagine web        |
| SCSS                 | Versione più potente del CSS                             |
| Stylesheet           | File che contiene regole CSS                             |
| Component            | Parte riutilizzabile dell’interfaccia                    |
| className            | Attributo JSX per assegnare una classe CSS               |
| Class name collision | Conflitto tra nomi di classi uguali                      |
| Global CSS           | CSS applicato globalmente all’applicazione               |
| CSS Modules          | Metodo per rendere le classi CSS locali a una componente |
| Inline styles        | Stili scritti direttamente dentro JSX                    |
| Wrapper class        | Classe contenitore usata per limitare gli stili          |
| Nesting              | Scrivere selettori annidati in SCSS                      |

---

```
