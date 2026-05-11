# React - Stilizzare componenti con Tailwind CSS

## 1. Styling dei componenti con Tailwind

In React possiamo stilizzare i componenti in diversi modi:

* CSS normale;
* SCSS;
* CSS Modules;
* inline styles;
* CSS Frameworks.

In questa lezione ci concentriamo su **Tailwind CSS**.

Tailwind è un **CSS Framework** molto usato per creare interfacce moderne in modo veloce.

---

# CSS Frameworks

## 2. Che cos’è un CSS Framework?

Un **CSS Framework** è come una libreria di classi CSS già pronte.

Invece di scrivere tutto il CSS da zero, possiamo usare classi già esistenti.

In pratica:

* non scriviamo sempre nuove regole CSS;
* scegliamo classi già pronte;
* combiniamo queste classi per ottenere lo stile desiderato.

---

## 3. Esempio di classi in un CSS Framework

Una libreria CSS potrebbe contenere classi come queste:

```css
.p-1 {
   padding: .2rem;
}

.p-2 {
   padding: .35rem;
}

.p-3 {
   padding: .5rem;
}

.infobox {
   color: var(--theme-color-info);
   border: .1rem solid var(--theme-color-info);
   background-color: var(--theme-color-info-light);
}
```

Invece di scrivere ogni volta:

```css
padding: .5rem;
```

possiamo usare una classe già pronta, per esempio:

```html
<div class="p-3"></div>
```

In React useremo `className`:

```jsx
<div className="p-3"></div>
```

---

## 4. Come si usa un CSS Framework?

Per usare un CSS Framework bisogna normalmente seguire il suo sistema.

Ogni framework ha un modo specifico di lavorare.

Di solito un framework si aspetta che le classi vengano usate in un certo modo.

Per questo è importante leggere la documentazione e capire la logica del framework.

---

## 5. Cosa può includere un CSS Framework?

Un CSS Framework può includere diverse cose, per esempio:

* un sistema di colori;
* un reset stylesheet integrato;
* un sistema di griglia;
* layout basati su Flexbox o CSS Grid;
* regole di tipografia;
* icone;
* classi per spacing, bordi, ombre e dimensioni;
* componenti già pronti.

Alcuni framework includono anche piccoli helper JavaScript.

Questi helper possono servire per funzionalità più complesse, come:

* popup;
* carousel;
* tooltip;
* menu dropdown;
* modali.

---

# Tailwind CSS

## 6. Che cos’è Tailwind?

**Tailwind CSS** è un CSS Framework.

È basato su classi utility, cioè piccole classi che fanno una cosa specifica.

Per esempio:

```css
.relative {
   position: relative;
}

.z-10 {
   z-index: 10;
}
```

In Tailwind, molte classi hanno nomi che ricordano direttamente proprietà CSS.

Questo rende Tailwind molto utile per chi conosce già le basi del CSS.

---

## 7. Tailwind vs Bootstrap

Tailwind non è il framework CSS più usato in assoluto.

Il framework CSS più conosciuto e usato storicamente è **Bootstrap**.

Però Tailwind sta diventando sempre più popolare.

Tailwind viene spesso considerato più moderno e più flessibile rispetto a Bootstrap.

Bootstrap offre molti componenti già pronti.

Tailwind invece offre soprattutto classi utility per costruire componenti personalizzati.

---

## 8. Perché imparare Tailwind?

Imparare Tailwind è utile perché:

* è molto richiesto in molti progetti moderni;
* permette di creare interfacce velocemente;
* aiuta a scrivere meno CSS personalizzato;
* usa nomi di classi vicini al CSS reale;
* rende più semplice imparare altri framework come Bootstrap;
* si integra molto bene con React.

Se conosci Tailwind, imparare Bootstrap sarà più semplice, perché molti concetti di layout, spacing e responsive design sono simili.

---

# Utility-first CSS

## 9. Che significa utility-first?

Tailwind è un framework **utility-first**.

Questo significa che usa tante piccole classi, ognuna con una responsabilità precisa.

Per esempio:

```jsx
<div className="p-4 bg-white rounded-lg shadow">
   Content
</div>
```

In questo esempio:

| Classe       | Significato                |
| ------------ | -------------------------- |
| `p-4`        | Aggiunge padding           |
| `bg-white`   | Imposta lo sfondo bianco   |
| `rounded-lg` | Aggiunge bordi arrotondati |
| `shadow`     | Aggiunge un’ombra          |

Invece di creare una classe CSS personalizzata, combiniamo classi già pronte.

---

## 10. Esempio con CSS normale

Con CSS normale potremmo scrivere:

```css
.card {
   padding: 1rem;
   background-color: white;
   border-radius: 0.5rem;
   box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}
```

E poi in React:

```jsx
<div className="card">Content</div>
```

---

## 11. Esempio con Tailwind

Con Tailwind possiamo scrivere direttamente:

```jsx
<div className="p-4 bg-white rounded-lg shadow">
   Content
</div>
```

In questo modo non abbiamo bisogno di creare una classe `.card` separata.

---

# Installazione di Tailwind in React con Vite

## 12. Installare Tailwind

Nel progetto React creato con Vite, entra nella cartella del progetto.

Esempio:

```bash
cd nome-progetto
```

Poi installa Tailwind con:

```bash
npm install tailwindcss @tailwindcss/vite
```

---

## 13. Modificare `vite.config.js`

Dopo l’installazione, bisogna modificare il file:

```txt
vite.config.js
```

Il file deve importare Tailwind e aggiungerlo ai plugin di Vite.

Esempio:

```js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
   plugins: [react(), tailwindcss()],
});
```

---

## 14. Importare Tailwind in `index.css`

Nel file:

```txt
index.css
```

aggiungi questa riga:

```css
@import "tailwindcss";
```

Questa riga importa Tailwind nel progetto.

---

## 15. Struttura finale essenziale

Dopo l’installazione, i file principali saranno:

```txt
src/
   main.jsx
   App.jsx
   index.css
vite.config.js
package.json
```

Nel file `index.css` ci sarà:

```css
@import "tailwindcss";
```

Nel file `vite.config.js` ci sarà il plugin Tailwind:

```js
plugins: [react(), tailwindcss()]
```

---

# Primo esempio con Tailwind in React

## 16. Componente semplice

```jsx
function App() {
   return (
      <main className="min-h-screen bg-gray-100 p-8">
         <section className="max-w-xl mx-auto bg-white rounded-xl shadow p-6">
            <h1 className="text-3xl font-bold mb-4">
               Hello Tailwind
            </h1>
            <p className="text-gray-600">
               This component is styled with Tailwind CSS.
            </p>
         </section>
      </main>
   );
}

export default App;
```

---

## 17. Spiegazione delle classi

| Classe          | Significato                              |
| --------------- | ---------------------------------------- |
| `min-h-screen`  | Altezza minima pari a tutta la schermata |
| `bg-gray-100`   | Sfondo grigio chiaro                     |
| `p-8`           | Padding grande                           |
| `max-w-xl`      | Larghezza massima                        |
| `mx-auto`       | Centra orizzontalmente                   |
| `bg-white`      | Sfondo bianco                            |
| `rounded-xl`    | Bordi molto arrotondati                  |
| `shadow`        | Ombra                                    |
| `text-3xl`      | Testo grande                             |
| `font-bold`     | Testo in grassetto                       |
| `mb-4`          | Margine sotto                            |
| `text-gray-600` | Testo grigio                             |

---

# Stati come hover e focus

## 18. Hover e focus in Tailwind

Tailwind permette di gestire stati come `hover` e `focus` direttamente nelle classi.

Esempio:

```jsx
<button className="bg-black text-white px-4 py-2 rounded hover:bg-gray-800 focus:outline-none">
   Click me
</button>
```

---

## 19. Spiegazione

| Classe               | Significato                                                     |
| -------------------- | --------------------------------------------------------------- |
| `bg-black`           | Sfondo nero                                                     |
| `text-white`         | Testo bianco                                                    |
| `px-4`               | Padding orizzontale                                             |
| `py-2`               | Padding verticale                                               |
| `rounded`            | Bordi arrotondati                                               |
| `hover:bg-gray-800`  | Quando passo sopra con il mouse, lo sfondo diventa grigio scuro |
| `focus:outline-none` | Rimuove l’outline di default al focus                           |

La parte importante è:

```txt
hover:bg-gray-800
```

Significa:

> Applica `bg-gray-800` solo quando l’utente passa con il mouse sopra l’elemento.

---

# Responsive Design con Tailwind

## 20. Responsive design

Tailwind permette di gestire il responsive design con prefissi come:

```txt
sm:
md:
lg:
xl:
2xl:
```

Questi prefissi indicano breakpoint diversi.

Esempio:

```jsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
   <div>Card 1</div>
   <div>Card 2</div>
   <div>Card 3</div>
</div>
```

---

## 21. Spiegazione responsive

| Classe           | Significato                              |
| ---------------- | ---------------------------------------- |
| `grid`           | Usa CSS Grid                             |
| `grid-cols-1`    | Una colonna di default                   |
| `md:grid-cols-2` | Da schermi medi in poi usa due colonne   |
| `lg:grid-cols-3` | Da schermi grandi in poi usa tre colonne |
| `gap-4`          | Spazio tra gli elementi                  |

Quindi:

* su mobile: 1 colonna;
* su tablet: 2 colonne;
* su desktop: 3 colonne.

---

# Dark Mode con Tailwind

## 22. Dark mode

Tailwind supporta anche la **dark mode**.

Possiamo usare il prefisso:

```txt
dark:
```

Esempio:

```jsx
<div className="bg-white text-black dark:bg-black dark:text-white">
   Dark mode example
</div>
```

---

## 23. Spiegazione dark mode

| Classe            | Significato                       |
| ----------------- | --------------------------------- |
| `bg-white`        | Sfondo bianco in modalità normale |
| `text-black`      | Testo nero in modalità normale    |
| `dark:bg-black`   | Sfondo nero in dark mode          |
| `dark:text-white` | Testo bianco in dark mode         |

Questo permette di cambiare stile in base alla modalità scura.

---

# Documentazione ufficiale

## 24. Guida Tailwind con Vite

La documentazione ufficiale per installare Tailwind con Vite è:

```txt
https://tailwindcss.com/docs/guides/vite
```

Questa guida spiega come configurare Tailwind in un progetto Vite.

---

## 25. Concetti base da leggere

Per prendere familiarità con Tailwind, è utile leggere queste sezioni della documentazione:

### Utility-first

```txt
https://tailwindcss.com/docs/utility-first
```

Spiega la filosofia principale di Tailwind: usare classi utility invece di scrivere CSS personalizzato per ogni componente.

---

### Hover, focus e altri stati

```txt
https://tailwindcss.com/docs/hover-focus-and-other-states
```

Spiega come applicare stili in base agli stati degli elementi, per esempio hover, focus, active e disabled.

---

### Responsive design

```txt
https://tailwindcss.com/docs/responsive-design
```

Spiega come creare layout responsive usando prefissi come `sm:`, `md:`, `lg:`.

---

### Dark mode

```txt
https://tailwindcss.com/docs/dark-mode
```

Spiega come gestire la modalità scura con Tailwind.

---

# Risorse extra

## 26. shadcn/ui

Una risorsa molto interessante è:

```txt
https://ui.shadcn.com
```

**shadcn/ui** è una raccolta di componenti moderni costruiti con Tailwind.

È molto usata nei progetti React moderni.

Non è una classica libreria di componenti già installata e chiusa.

Di solito copi i componenti nel tuo progetto e li personalizzi.

---

## 27. Tailwind UI

Un’altra risorsa è:

```txt
https://tailwindui.com/components
```

Tailwind UI è una raccolta di componenti costruiti con Tailwind.

Alcuni componenti sono gratuiti, altri sono a pagamento.

Può essere utile per vedere esempi professionali di layout e interfacce.

---

# Esempio completo: Card con Tailwind

## 28. Product Card

```jsx
function ProductCard() {
   return (
      <article className="max-w-sm rounded-2xl bg-white p-6 shadow">
         <h2 className="text-2xl font-bold text-gray-900">
            Apple iPad
         </h2>

         <p className="mt-2 text-gray-600">
            Cool tablet device thing.
         </p>

         <button className="mt-4 rounded-lg bg-black px-4 py-2 text-white hover:bg-gray-800">
            Add to cart
         </button>
      </article>
   );
}

export default ProductCard;
```

---

## 29. Spiegazione della card

| Classe              | Significato                          |
| ------------------- | ------------------------------------ |
| `max-w-sm`          | Larghezza massima piccola            |
| `rounded-2xl`       | Bordi molto arrotondati              |
| `bg-white`          | Sfondo bianco                        |
| `p-6`               | Padding interno                      |
| `shadow`            | Ombra                                |
| `text-2xl`          | Titolo grande                        |
| `font-bold`         | Testo in grassetto                   |
| `text-gray-900`     | Testo grigio molto scuro             |
| `mt-2`              | Margine sopra piccolo                |
| `text-gray-600`     | Testo grigio medio                   |
| `mt-4`              | Margine sopra più grande             |
| `rounded-lg`        | Bordi arrotondati                    |
| `px-4`              | Padding orizzontale                  |
| `py-2`              | Padding verticale                    |
| `hover:bg-gray-800` | Cambia colore al passaggio del mouse |

---

# Vantaggi di Tailwind

## 30. Perché Tailwind è utile?

Tailwind è utile perché:

* permette di scrivere meno CSS personalizzato;
* velocizza la creazione di interfacce;
* mantiene gli stili vicini al JSX;
* riduce il rischio di collisioni tra classi;
* rende facile creare layout responsive;
* rende semplice gestire hover, focus e dark mode;
* è molto usato in progetti React moderni.

---

# Possibili svantaggi di Tailwind

## 31. Cosa può essere difficile all’inizio?

All’inizio Tailwind può sembrare strano perché ci sono molte classi dentro il JSX.

Esempio:

```jsx
<div className="max-w-sm rounded-2xl bg-white p-6 shadow text-gray-900">
   Content
</div>
```

Può sembrare più lungo rispetto a scrivere una classe CSS unica.

Però, con la pratica, diventa molto veloce e leggibile.

---

# Recap finale

## Concetti principali da ricordare

Tailwind CSS è un CSS Framework.

Un CSS Framework è una libreria di classi già pronte.

Invece di scrivere sempre CSS personalizzato, usiamo classi già disponibili.

Tailwind è un framework utility-first.

Questo significa che usa tante piccole classi, ognuna con una funzione specifica.

Esempi:

```txt
p-4
bg-white
rounded-lg
shadow
text-gray-600
```

Per installare Tailwind in React con Vite:

```bash
npm install tailwindcss @tailwindcss/vite
```

Poi si modifica `vite.config.js`:

```js
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
   plugins: [react(), tailwindcss()],
});
```

E si importa Tailwind in `index.css`:

```css
@import "tailwindcss";
```

Tailwind permette di gestire facilmente:

* spacing;
* colori;
* layout;
* hover;
* focus;
* responsive design;
* dark mode.

Risorse importanti:

```txt
https://tailwindcss.com/docs/guides/vite
https://tailwindcss.com/docs/utility-first
https://tailwindcss.com/docs/hover-focus-and-other-states
https://tailwindcss.com/docs/responsive-design
https://tailwindcss.com/docs/dark-mode
https://ui.shadcn.com
https://tailwindui.com/components
```

---

# Mini glossario

| Termine           | Significato                                                          |
| ----------------- | -------------------------------------------------------------------- |
| Tailwind CSS      | Framework CSS utility-first                                          |
| CSS Framework     | Libreria di classi CSS già pronte                                    |
| Utility class     | Classe piccola con una funzione specifica                            |
| Utility-first     | Approccio basato su classi utility                                   |
| Bootstrap         | Framework CSS molto popolare                                         |
| Vite              | Build tool e development server moderno                              |
| Plugin            | Estensione che aggiunge funzionalità                                 |
| Responsive design | Design che si adatta a schermi diversi                               |
| Breakpoint        | Punto in cui il layout cambia in base alla larghezza dello schermo   |
| Hover             | Stato quando il mouse passa sopra un elemento                        |
| Focus             | Stato quando un elemento è selezionato o attivo                      |
| Dark mode         | Modalità scura dell’interfaccia                                      |
| shadcn/ui         | Raccolta di componenti moderni basati su Tailwind                    |
| Tailwind UI       | Raccolta di componenti Tailwind, alcuni gratuiti e altri a pagamento |

---

```
