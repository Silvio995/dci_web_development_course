# React Forms - Form in React

## Creazione del progetto con Vite

Per creare un nuovo progetto React con Vite usando il template React, usiamo questo comando:

```bash
npm create vite@latest react-forms -- --template react
```

Dove:

* `npm create vite@latest` crea un nuovo progetto con Vite;
* `react-forms` è il nome della cartella/progetto;
* `-- --template react` indica che vogliamo usare il template React.

Dopo la creazione del progetto:

```bash
cd react-forms
npm install
npm run dev
```

---

# Form in React

## 1. Form normali in HTML

Nei form HTML normali, il valore degli input viene gestito direttamente dagli elementi del DOM.

Per esempio, quando scriviamo dentro un input HTML, il browser gestisce il valore internamente.

Esempio HTML:

```html
<input type="text" />
```

In questo caso il valore dell’input vive dentro l’elemento HTML stesso.

---

## 2. Form in React

In React il funzionamento è diverso.

Il valore degli elementi di input viene salvato dentro variabili di stato, cioè dentro lo **state** della componente.

Questo significa che React controlla il valore dell’input.

Quando un input viene gestito dallo state, si chiama:

```txt
Controlled Component
```

In italiano possiamo tradurlo come:

```txt
Componente controllata
```

---

# Controlled Components

## 3. Cosa sono le componenti controllate?

Le **controlled components** sono elementi di form il cui valore è controllato dallo stato della componente React.

In pratica:

* il valore dell’input viene salvato in una variabile di stato;
* la prop `value` collega l’input allo state;
* l’evento `onChange` aggiorna lo state quando l’utente scrive o modifica il valore.

---

## 4. Struttura di una controlled component

Una controlled component usa normalmente:

### `useState`

Serve per creare una variabile di stato.

```jsx
const [inputValue, setInputValue] = useState("");
```

### `value`

Serve per dire all’input quale valore deve mostrare.

```jsx
value={inputValue}
```

### `onChange`

Serve per aggiornare lo state quando l’utente cambia il valore dell’input.

```jsx
onChange={handleChange}
```

---

## 5. Esempio di input controllato

```jsx
import { useState } from "react";

function ControlledInput() {
   const [inputValue, setInputValue] = useState("");

   function handleChange(event) {
      setInputValue(event.target.value);
   }

   return (
      <div>
         <label>
            Controlled Input:
            <input
               type="text"
               value={inputValue}
               onChange={handleChange}
            />
         </label>
      </div>
   );
}

export default ControlledInput;
```

---

## 6. Spiegazione dell’esempio

```jsx
const [inputValue, setInputValue] = useState("");
```

Qui creiamo una variabile di stato chiamata `inputValue`.

Il valore iniziale è una stringa vuota.

```jsx
function handleChange(event) {
   setInputValue(event.target.value);
}
```

Questa funzione viene eseguita ogni volta che l’utente scrive qualcosa nell’input.

`event.target.value` contiene il valore attuale dell’input.

Con `setInputValue` aggiorniamo lo stato.

```jsx
<input
   type="text"
   value={inputValue}
   onChange={handleChange}
/>
```

Qui l’input è controllato da React.

* `value={inputValue}` mostra il valore salvato nello state;
* `onChange={handleChange}` aggiorna lo state quando l’utente scrive.

---

# Mini esercizio: Radio Buttons

## Pizza Order con Radio Buttons in React

L’obiettivo dell’esercizio è creare una componente React per ordinare una pizza usando radio buttons.

La componente dovrà gestire:

* la grandezza della pizza;
* il metodo di pagamento;
* il prezzo calcolato automaticamente;
* il riepilogo dell’ordine;
* l’invio del form;
* un messaggio finale dopo l’invio.

---

# 1. Setup base

## Crea una componente React

Crea una componente chiamata:

```txt
RadioButtonExample
```

All’interno della componente usa `useState` per gestire:

* la grandezza della pizza;
* il metodo di pagamento;
* lo stato di invio del form.

---

## Stati necessari

```jsx
const [size, setSize] = useState("medium");
const [paymentMethod, setPaymentMethod] = useState("");
const [submitted, setSubmitted] = useState(false);
```

Significato:

| State           | Significato                                 |
| --------------- | ------------------------------------------- |
| `size`          | Contiene la grandezza scelta della pizza    |
| `paymentMethod` | Contiene il metodo di pagamento scelto      |
| `submitted`     | Indica se il form è stato inviato oppure no |

---

# 2. Grandezza della pizza

## Radio Group 1

Crea un gruppo di radio buttons per scegliere la grandezza della pizza.

Le opzioni sono:

* `small`
* `medium`
* `large`

Tutti i radio buttons dello stesso gruppo devono avere lo stesso attributo `name`.

Esempio:

```jsx
name="size"
```

Questo permette al browser di capire che questi radio buttons appartengono allo stesso gruppo.

Quindi l’utente potrà selezionare solo una grandezza alla volta.

---

## Struttura di ogni radio button

Ogni radio button deve avere:

* un `value`;
* un `onChange`;
* una proprietà `checked`.

Esempio:

```jsx
<input
   type="radio"
   name="size"
   value="small"
   checked={size === "small"}
   onChange={(event) => setSize(event.target.value)}
/>
```

---

## Spiegazione

```jsx
value="small"
```

Il valore di questo radio button è `small`.

```jsx
checked={size === "small"}
```

Questo controlla se il radio button deve essere selezionato.

Se il valore dello state `size` è uguale a `"small"`, allora il radio button risulta selezionato.

```jsx
onChange={(event) => setSize(event.target.value)}
```

Quando l’utente seleziona il radio button, aggiorniamo lo state `size`.

---

# 3. Metodo di pagamento

## Radio Group 2

Crea una seconda radio group per scegliere il metodo di pagamento.

Le opzioni sono:

* Carta di credito;
* PayPal;
* Contanti alla consegna.

Anche qui tutti i radio buttons dello stesso gruppo devono avere lo stesso `name`.

Esempio:

```jsx
name="paymentMethod"
```

Per il metodo di pagamento usiamo uno state separato:

```jsx
const [paymentMethod, setPaymentMethod] = useState("");
```

In questo modo React controlla quale metodo di pagamento è stato selezionato.

---

## Esempio radio button pagamento

```jsx
<input
   type="radio"
   name="paymentMethod"
   value="paypal"
   checked={paymentMethod === "paypal"}
   onChange={(event) => setPaymentMethod(event.target.value)}
/>
```

---

# 4. Calcolo del prezzo

## Prezzi per grandezza

Definiamo un oggetto con i prezzi delle pizze:

```jsx
const prices = {
   small: 8,
   medium: 10,
   large: 12,
};
```

Poi calcoliamo il prezzo in base alla grandezza selezionata:

```jsx
const price = prices[size];
```

Se `size` è `"small"`, il prezzo sarà `8`.

Se `size` è `"medium"`, il prezzo sarà `10`.

Se `size` è `"large"`, il prezzo sarà `12`.

Il prezzo si aggiorna automaticamente quando cambia lo state `size`.

---

# 5. Anteprima / Riepilogo ordine

Mostra un riepilogo dell’ordine con:

* grandezza selezionata;
* prezzo calcolato;
* metodo di pagamento selezionato;
* oppure `"Not selected"` se nessun metodo di pagamento è stato scelto.

Esempio:

```jsx
<div>
   <h2>Order Summary</h2>
   <p>Size: {size}</p>
   <p>Price: €{price}</p>
   <p>Payment Method: {paymentMethod || "Not selected"}</p>
</div>
```

---

# 6. Invio del form

## handleSubmit

Crea una funzione `handleSubmit`.

Questa funzione deve:

* usare `preventDefault`;
* mostrare i dati dell’ordine, per esempio con `alert`;
* impostare `submitted` su `true`.

Esempio:

```jsx
function handleSubmit(event) {
   event.preventDefault();

   alert(`Order submitted!
Size: ${size}
Price: €${price}
Payment method: ${paymentMethod}`);

   setSubmitted(true);
}
```

---

## Perché usiamo preventDefault?

Nei form HTML normali, quando premiamo il bottone submit, la pagina viene ricaricata.

In React non vogliamo che la pagina si ricarichi.

Per questo usiamo:

```jsx
event.preventDefault();
```

Così blocchiamo il comportamento standard del form.

---

# 7. Disabilitare il bottone Submit

Il bottone di invio deve essere disabilitato se l’utente non ha scelto un metodo di pagamento.

Esempio:

```jsx
<button type="submit" disabled={!paymentMethod}>
   Submit Order
</button>
```

Significato:

```jsx
disabled={!paymentMethod}
```

Se `paymentMethod` è vuoto, il bottone è disabilitato.

Se l’utente sceglie un metodo di pagamento, il bottone diventa attivo.

---

# 8. Conditional Rendering

## Messaggio dopo l’invio

Dopo l’invio del form, mostra un messaggio di ringraziamento.

Il messaggio deve essere mostrato solo se:

```jsx
submitted === true
```

Esempio:

```jsx
{submitted && <p>Thank you for your order!</p>}
```

Questo si chiama **conditional rendering**.

Significa che un elemento viene mostrato solo se una condizione è vera.

---

# Codice completo dell’esercizio

```jsx
import { useState } from "react";

function RadioButtonExample() {
   const [size, setSize] = useState("medium");
   const [paymentMethod, setPaymentMethod] = useState("");
   const [submitted, setSubmitted] = useState(false);

   const prices = {
      small: 8,
      medium: 10,
      large: 12,
   };

   const price = prices[size];

   function handleSubmit(event) {
      event.preventDefault();

      alert(`Order submitted!
Size: ${size}
Price: €${price}
Payment method: ${paymentMethod}`);

      setSubmitted(true);
   }

   return (
      <form onSubmit={handleSubmit}>
         <h1>Pizza Order</h1>

         <section>
            <h2>Choose pizza size</h2>

            <label>
               <input
                  type="radio"
                  name="size"
                  value="small"
                  checked={size === "small"}
                  onChange={(event) => setSize(event.target.value)}
               />
               Small
            </label>

            <label>
               <input
                  type="radio"
                  name="size"
                  value="medium"
                  checked={size === "medium"}
                  onChange={(event) => setSize(event.target.value)}
               />
               Medium
            </label>

            <label>
               <input
                  type="radio"
                  name="size"
                  value="large"
                  checked={size === "large"}
                  onChange={(event) => setSize(event.target.value)}
               />
               Large
            </label>
         </section>

         <section>
            <h2>Choose payment method</h2>

            <label>
               <input
                  type="radio"
                  name="paymentMethod"
                  value="credit-card"
                  checked={paymentMethod === "credit-card"}
                  onChange={(event) => setPaymentMethod(event.target.value)}
               />
               Credit Card
            </label>

            <label>
               <input
                  type="radio"
                  name="paymentMethod"
                  value="paypal"
                  checked={paymentMethod === "paypal"}
                  onChange={(event) => setPaymentMethod(event.target.value)}
               />
               PayPal
            </label>

            <label>
               <input
                  type="radio"
                  name="paymentMethod"
                  value="cash"
                  checked={paymentMethod === "cash"}
                  onChange={(event) => setPaymentMethod(event.target.value)}
               />
               Cash on delivery
            </label>
         </section>

         <section>
            <h2>Order Summary</h2>
            <p>Size: {size}</p>
            <p>Price: €{price}</p>
            <p>Payment Method: {paymentMethod || "Not selected"}</p>
         </section>

         <button type="submit" disabled={!paymentMethod}>
            Submit Order
         </button>

         {submitted && <p>Thank you for your order!</p>}
      </form>
   );
}

export default RadioButtonExample;
```

---

# Come usare la componente in App.jsx

Nel file `App.jsx` puoi importare e usare la componente così:

```jsx
import RadioButtonExample from "./RadioButtonExample";

function App() {
   return (
      <div>
         <RadioButtonExample />
      </div>
   );
}

export default App;
```

---

# Recap finale

## Cose importanti da ricordare

Nei form HTML normali, il valore degli input viene gestito dal DOM.

In React, invece, il valore degli input viene gestito dallo state.

Quando un input è collegato allo state tramite `value` e `onChange`, si chiama **controlled component**.

Per i radio buttons:

* tutti i radio buttons dello stesso gruppo devono avere lo stesso `name`;
* ogni radio button deve avere un `value`;
* `checked={state === value}` controlla quale opzione è selezionata;
* `onChange` aggiorna lo state.

Per inviare un form in React:

* si usa `onSubmit`;
* si crea una funzione `handleSubmit`;
* si usa `event.preventDefault()` per evitare il reload della pagina.

Il **conditional rendering** permette di mostrare qualcosa solo se una condizione è vera.

Esempio:

```jsx
{submitted && <p>Thank you for your order!</p>}
```

---

# Mini glossario

| Termine               | Significato                                                             |
| --------------------- | ----------------------------------------------------------------------- |
| Form                  | Modulo per inserire dati                                                |
| Input                 | Campo di inserimento                                                    |
| State                 | Stato interno della componente                                          |
| useState              | Hook di React per gestire lo stato                                      |
| Controlled Component  | Componente il cui valore è controllato dallo state                      |
| value                 | Prop che imposta il valore dell’input                                   |
| onChange              | Evento che si attiva quando cambia il valore                            |
| checked               | Prop usata per controllare se un radio button è selezionato             |
| onSubmit              | Evento che si attiva quando il form viene inviato                       |
| preventDefault        | Metodo che blocca il comportamento standard del browser                 |
| Conditional Rendering | Rendering condizionale, mostrare qualcosa solo se una condizione è vera |

---

