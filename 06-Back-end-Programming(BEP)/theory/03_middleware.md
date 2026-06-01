# Express Middleware

## Teoria completa

---

# Cos’è Express?

entity["software","Express","Node.js web framework"] è un framework minimale per Node.js che fornisce principalmente:

* routing
* gestione delle richieste HTTP
* middleware system

Express non ha molte funzionalità “di default”: la maggior parte della logica viene costruita tramite middleware.

---

# Cos’è una Middleware?

Una **middleware function** è una funzione che viene eseguita durante il ciclo di una richiesta HTTP.

In Express, ogni richiesta passa attraverso una serie di middleware.

---

# Concetto fondamentale

> In Express, una applicazione è una sequenza di chiamate a middleware.

Questo significa che ogni request:

* entra nel server
* attraversa una catena di middleware
* viene processata o bloccata
* produce una response

---

# Middleware = funzione con 3 parametri

Ogni middleware riceve:

```js
(req, res, next)
```

## Significato

### req (request)

Rappresenta la richiesta HTTP ricevuta dal client.

Contiene:

* headers
* body
* params
* query

---

### res (response)

Rappresenta la risposta che verrà inviata al client.

Serve per:

* inviare dati
* inviare JSON
* terminare la risposta

---

### next

È una funzione che serve per passare alla middleware successiva.

```js
next()
```

---

# Come funziona il flusso middleware

Una richiesta HTTP attraversa le middleware:

```
Request → Middleware 1 → Middleware 2 → Middleware 3 → Response
```

Se una middleware NON chiama `next()`, il flusso si interrompe.

---

# Esempio semplice

```js
app.use((req, res, next) => {
  console.log("Prima middleware");
  next();
});

app.use((req, res, next) => {
  console.log("Seconda middleware");
  next();
});
```

Output:

```
Prima middleware
Seconda middleware
```

---

# Middleware = tutto in Express

In Express, praticamente tutto può essere considerato middleware:

## Esempio route handler

```js
app.get("/posts", (req, res) => {
  res.send("Hello");
});
```

Questa funzione è tecnicamente una middleware.

---

# Differenza pratica

## Middleware “generale”

```js
app.use((req, res, next) => {})
```

## Route middleware

```js
app.get("/", (req, res) => {})
```

Entrambi sono middleware, ma con scopi diversi.

---

# app.use()

`app.use()` registra una middleware globale.

Esempio:

```js
app.use(express.json());
```

Questa middleware viene eseguita per TUTTE le richieste.

---

# express.json()

```js
app.use(express.json());
```

Questa è una middleware fondamentale.

---

# Cosa fa express.json()?

## 1. Controlla il Content-Type

Verifica se la richiesta contiene:

```
Content-Type: application/json
```

---

## 2. Legge il body della richiesta

Il body arriva come stream di dati (non come oggetto).

---

## 3. Converte il body in stringa

Il flusso di dati viene trasformato in testo.

---

## 4. Parsing JSON

La stringa viene convertita in oggetto JavaScript:

```js
req.body = JSON.parse(bodyString);
```

---

## 5. Aggiunge req.body

Prima di questa middleware:

```js
req.body // undefined
```

Dopo:

```js
req.body // oggetto JSON
```

---

# Perché serve express.json()?

Perché HTTP non invia oggetti JavaScript.

Invia solo testo o stream di byte.

---

# Senza express.json()

```js
app.post("/data", (req, res) => {
  console.log(req.body);
});
```

Output:

```
undefined
```

---

# Con express.json()

```js
app.use(express.json());
```

Ora:

```js
req.body = { ...dati JSON ... }
```

---

# Middleware chain (ordine importante)

Le middleware vengono eseguite:

➡️ dall’alto verso il basso

---

# Se next() non viene chiamato

```js
app.use((req, res, next) => {
  console.log("Blocco qui");
  // next() non chiamato
});
```

👉 Le richieste successive NON vengono eseguite.

---

# Tipi di middleware

## 1. Application-level

```js
app.use()
```

---

## 2. Route-level

```js
app.get()
```

---

## 3. Built-in middleware

```js
express.json()
express.static()
```

---

## 4. Error middleware

```js
app.use((err, req, res, next) => {})
```

---

# Dove si usano le middleware?

Spesso vengono organizzate in cartelle:

```
/middlewares
  auth.js
  logger.js
  validate.js
```

---

# Esempi pratici di middleware

## Logger

```js
app.use((req, res, next) => {
  console.log(req.method, req.url);
  next();
});
```

---

## Auth middleware

```js
app.use((req, res, next) => {
  if(!req.user) {
    return res.status(401).send("Unauthorized");
  }
  next();
});
```

---

# Middleware e ordine

L’ordine è fondamentale:

```js
app.use(auth);
app.use(logger);
app.use(routes);
```

Se invertito, cambia il comportamento dell’app.

---

# Riassunto

* Express è basato su middleware
* Ogni request passa attraverso una catena di middleware
* Middleware hanno (req, res, next)
* next() passa al prossimo step
* express.json() converte JSON → oggetto JS
* req.body non esiste senza parsing
* ordine middleware è fondamentale

---

# Concetti chiave

* Middleware = funzione nel ciclo request/response
* next() = passaggio alla prossima middleware
* express.json() = parser JSON del body
* req.body = dati della richiesta
* ordine = comportamento dell’app
