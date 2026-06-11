# 🧪 React Tests con Cypress

## 📌 Cos’è Cypress

Cypress è un framework di testing per applicazioni web.

È più potente di Jest perché permette di testare l’app direttamente nel browser simulando l’utente reale.

---

## 🧪 Tipi di test

### 1. Unit / Component Tests

Testano singole parti dell’app:

* componenti React
* funzioni
* logica isolata

---

### 2. Visual Testing

Controlla che la UI sia corretta:

* layout
* colori
* struttura della pagina
* elementi visivi

---

### 3. End-to-End Testing (E2E)

Simula il comportamento reale dell’utente:

Esempio:

1. apertura sito
2. login
3. click
4. navigazione
5. azioni complete

---

### 4. Integration Testing

Verifica che più parti dell’app lavorino insieme:

* UI
* API
* backend
* database

---

## ⚙️ Come funziona Cypress

* Cypress gira nello stesso browser dell’app
* può leggere il DOM
* può intercettare richieste HTTP
* può leggere lo stato dell’app
* simula azioni utente (click, input, scroll)

---

## ⏳ Asincronia

Cypress è asincrono ma:

* NON usa `await`
* NON usa `.then()` nella maggior parte dei casi

Cypress gestisce una **queue interna di comandi**:

```js
cy.get("button")
cy.click()
```

---

## ⏰ Time Travel & Debug

Cypress permette di:

* vedere ogni step del test
* tornare indietro nel tempo
* ispezionare il DOM in ogni fase
* controllare richieste di rete

---

## 🔍 Selettori

### CSS selectors base

```js
cy.get("#id")              // ID
cy.get(".class")           // classe
cy.get("[data-test='x']")  // attributo
cy.get("form#id .btn")     // combinazioni
```

---

## ⭐ Best Practice Cypress

Usare attributi dedicati ai test:

```html
<button data-cy="submit-btn">Send</button>
```

```js
cy.get("[data-cy='submit-btn']").click()
```

### ✔ Perché?

* le classi cambiano spesso (CSS refactor)
* gli ID possono cambiare
* React/Next.js modifica il DOM
* `data-cy` è stabile e solo per test

---

## 🌐 API Testing (Intercept)

Cypress può simulare API:

```js
cy.intercept("POST", "/posts", {
  statusCode: 201
}).as("submit")
```

✔ utile per:

* evitare backend reale
* rendere i test veloci
* simulare risposte server

---

## 🚀 Uso principale di Cypress

Cypress è perfetto per:

* login/logout
* form e input
* navigazione
* flussi completi utente
* test errori
* simulazione API

---

## 📦 Installazione

```bash
npm install cypress -D
```

oppure

```bash
bun add cypress -D
```

---

## ▶️ Avvio Cypress

```bash
npx cypress open
```

oppure

```bash
bunx cypress open
```

---

## 🧪 Esecuzione test da terminale

Aggiungere in package.json:

```json
"test:e2e": "cypress run"
```

E poi:

```bash
npm run test:e2e
```

---

## 🧠 TypeScript support

In `tsconfig.json`:

```json
{
  "compilerOptions": {
    "types": ["cypress"]
  }
}
```

---

## 📁 Struttura progetto

```
cypress/
  e2e/
    login.cy.js
    contact.cy.js
    navigation.cy.js
```

✔ Un file = una funzionalità

---

## 🔄 Cypress vs Jest

| Jest        | Cypress        |
| ----------- | -------------- |
| test codice | test browser   |
| unit test   | E2E test       |
| veloce      | più realistico |
| no UI       | UI reale       |

---

## 🧠 Riassunto

Cypress è un framework che:

* testa l’app nel browser reale
* simula utenti veri
* permette debug visivo
* intercetta API
* supporta E2E e integration test

---
