# API + Backend

## EXPRESS, CRUD e REST – Teoria completa

---

# Backend e gestione dati

Il **core di un backend** è la gestione dei dati.

Un backend serve principalmente a:

* creare dati
* leggere dati
* aggiornare dati
* eliminare dati

Queste operazioni sono chiamate **CRUD**.

---

# CRUD

CRUD significa:

* **C**reate → creare dati
* **R**ead → leggere dati
* **U**pdate → aggiornare dati
* **D**elete → eliminare dati

Queste 4 operazioni sono la base di qualsiasi sistema backend.

---

# HTTP e CRUD

Il protocollo HTTP si adatta perfettamente al modello CRUD.

Ogni operazione viene rappresentata da:

* un metodo HTTP
* un endpoint (URL)
* headers (metadati)
* body (dati inviati)

---

# Struttura di una richiesta HTTP

Una richiesta HTTP è composta da:

* **Method** → cosa vogliamo fare (GET, POST, PUT, DELETE)
* **URL** → su quale risorsa lavoriamo
* **Headers** → informazioni aggiuntive
* **Body** → dati inviati (opzionale)

---

# Mapping CRUD ↔ HTTP

## Operazioni corrette

| CRUD   | HTTP Method |
| ------ | ----------- |
| Create | POST        |
| Read   | GET         |
| Update | PUT / PATCH |
| Delete | DELETE      |

---

# Esempi di API REST

## READ (GET)

### Tutti gli utenti

```
GET /users
```

### Utente specifico

```
GET /users/1
GET /users/klaus
```

---

## CREATE (POST)

```
POST /comments
POST /products
```

📦 Il body contiene i dati da creare:

```json
{
  "text": "Ottimo prodotto",
  "rating": 5
}
```

---

## DELETE

### Eliminare tutti gli utenti

```
DELETE /users
```

### Eliminare un utente specifico

```
DELETE /users/1
```

---

## UPDATE (PUT / PATCH)

### PUT

```
PUT /users/1
```

### PATCH

```
PATCH /users/1
```

---

# Differenza tra PUT e PATCH

## PUT (sostituzione completa)

PUT sostituisce completamente la risorsa esistente.

Esempio iniziale:

```json
{
  "id": 1,
  "name": "John",
  "role": "User"
}
```

### PUT /users/1

Payload:

```json
{
  "id": 1,
  "role": "Admin"
}
```

Risultato teorico:

```json
{
  "id": 1,
  "role": "Admin"
}
```

👉 I campi mancanti vengono rimossi.

---

## PATCH (aggiornamento parziale)

PATCH modifica solo i campi forniti.

Esempio:

```json
{
  "id": 1,
  "name": "John",
  "role": "User"
}
```

### PATCH /users/1

Payload:

```json
{
  "role": "Admin"
}
```

Risultato:

```json
{
  "id": 1,
  "name": "John",
  "role": "Admin"
}
```

---

# Nota importante su PUT e PATCH

In teoria:

* PUT → sostituzione completa
* PATCH → aggiornamento parziale

In pratica:

* le implementazioni reali possono variare
* alcuni backend trattano PUT e PATCH in modo simile

La realtà del software è spesso meno “perfetta” della teoria.

---

# Express e Backend

entity["software","Express","Node.js web framework"]

Express è un framework per Node.js che permette di creare backend in modo semplice.

Con Express possiamo:

* definire endpoint
* gestire richieste HTTP
* leggere body e headers
* inviare risposte

---

# Esempio Express (API base)

```js
const express = require("express");
const app = express();

app.use(express.json());

app.get("/users", (req, res) => {
  res.json([]);
});

app.post("/users", (req, res) => {
  const user = req.body;
  res.json({ message: "User created", user });
});

app.listen(3000);
```

---

# Cos’è REST?

REST significa:

> Representational State Transfer

È un insieme di principi per progettare API web.

---

# Principio di REST

REST definisce un modo standard per usare HTTP per gestire risorse.

Una risorsa può essere:

* utenti
* prodotti
* ordini
* commenti

---

# REST e HTTP

Le REST API usano HTTP in modo coerente:

| Azione            | Endpoint | Metodo      |
| ----------------- | -------- | ----------- |
| leggere utenti    | /users   | GET         |
| creare utente     | /users   | POST        |
| aggiornare utente | /users/1 | PUT / PATCH |
| eliminare utente  | /users/1 | DELETE      |

---

# Importante chiarimento

Nel modello REST corretto:

* GET → leggere dati
* POST → creare dati
* PUT → aggiornare (completo)
* PATCH → aggiornare (parziale)
* DELETE → eliminare dati

❗ Non è corretto invertire questi metodi.

---

# REST è una teoria

REST non è una tecnologia.

È un insieme di regole e linee guida.

Serve per rendere le API:

* consistenti
* prevedibili
* scalabili

---

# Esempio di API REST

```
GET /products
GET /products/10
POST /products
PUT /products/10
DELETE /products/10
```

---

# Concetto di risorsa

In REST tutto è una risorsa.

Esempi:

* /users → collezione di utenti
* /users/1 → singolo utente
* /products → prodotti
* /orders → ordini

---

# Perché REST è utile?

* standardizza le API
* facilita la comunicazione tra sistemi
* rende il backend prevedibile
* semplifica il lavoro dei client

---

# Backend moderno

Un backend moderno usa:

* Express o framework simili
* API REST
* CRUD operations
* JSON come formato dati

---

# Riassunto

* CRUD è la base della gestione dati
* HTTP implementa perfettamente CRUD
* Express aiuta a creare backend facilmente
* REST è uno stile architetturale per API
* GET/POST/PUT/PATCH/DELETE sono i metodi standard
* PUT sostituisce, PATCH modifica parzialmente
* Le API sono insiemi di endpoint

---

# Concetti chiave da ricordare

* Backend = gestione dati
* CRUD = Create, Read, Update, Delete
* REST = stile per progettare API
* HTTP methods = azioni sulle risorse
* Express = framework Node.js per server
* JSON = formato dati standard
