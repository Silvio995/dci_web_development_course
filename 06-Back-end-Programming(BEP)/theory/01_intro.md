# Server HTTP, Express e Debugging

## Teoria completa della lezione

---

# Cos’è un Server?

Un server è un programma che:

* ascolta su una porta di rete (port)
* riceve richieste (request)
* invia risposte (response)

Nel nostro caso parliamo di **HTTP server**, cioè server che comunicano tramite il protocollo HTTP usato dal web.

---

# Come funziona un HTTP Server?

Un server HTTP resta in esecuzione e aspetta richieste da parte di un client (browser, app, fetch, curl).

Quando arriva una richiesta:

1. la analizza
2. esegue una logica
3. restituisce una risposta

La risposta può essere:

* HTML (pagina web)
* JSON (API)
* testo semplice

---

# Struttura di una richiesta HTTP (POST)

Esempio tipico di richiesta POST:

```
POST /bestellung HTTP/1.1
Host: mein-shop.de
User-Agent: Chrome/90.0
Content-Type: application/json

{"produkt": "Laptop", "anzahl": 1}
```

---

# Spiegazione della struttura

## 1. Start line

```
POST /bestellung HTTP/1.1
```

Contiene:

* metodo HTTP (POST)
* percorso (endpoint)
* versione HTTP

---

## 2. Headers (intestazioni)

```
Host: mein-shop.de
User-Agent: Chrome/90.0
Content-Type: application/json
```

Gli header contengono informazioni aggiuntive sulla richiesta:

* Host → dominio del server
* User-Agent → browser o client
* Content-Type → formato dei dati

---

## 3. Body

```
{"produkt": "Laptop", "anzahl": 1}
```

Contiene i dati inviati al server.

Nel caso di POST, i dati vengono inviati nel body della richiesta.

---

# Come risponde il server?

Il server risponde con una HTTP Response che può contenere:

* HTML
* JSON
* testo

Esempio:

```
HTTP/1.1 200 OK
Content-Type: application/json

{"status": "ok"}
```

---

# Flusso completo richiesta/risposta

1. Il client (browser o fetch) invia una richiesta HTTP
2. Il server riceve la richiesta su una porta
3. Il server elabora i dati
4. Il server invia una risposta
5. Il client riceve e mostra il risultato

---

# Debugging (Exkurs)

Il debugging serve per analizzare il comportamento del programma mentre è in esecuzione.

---

# Strumenti di debugging (VS Code)

## Breakpoints

Un breakpoint interrompe l’esecuzione del codice in un punto preciso.

Serve per:

* vedere lo stato del programma
* capire cosa succede passo dopo passo

---

## Watch variables

Permette di osservare variabili durante l’esecuzione:

* valori attuali
* cambiamenti nel tempo

---

## Perché è utile?

Aiuta a:

* trovare bug
* capire logica del codice
* analizzare flussi complessi

---

# Express Server

Express è un framework JavaScript (Node.js) che permette di creare server HTTP in modo semplice.

---

# Installazione Express

```bash
npm i express
```

---

# Cosa permette di fare Express?

* creare server HTTP facilmente
* gestire richieste (GET, POST, ecc.)
* gestire routing (endpoint)
* inviare risposte

---

# Concetto di API

Un’API è una interfaccia che permette la comunicazione tra sistemi.

---

# Esempi di API

## 1. API del browser

Permette a JavaScript di accedere a funzionalità come:

* webcam
* microfono
* storage

---

## 2. API del sistema operativo

Permette ai programmi di:

* leggere file
* accedere alla memoria
* usare rete

---

## 3. API web (server)

Esempio:

[https://jsonplaceholder.typicode.com](https://jsonplaceholder.typicode.com)

Fornisce dati tramite richieste HTTP.

---

# Cos’è una Web API?

Una Web API è una collezione di endpoint su un server che i client possono chiamare.

Esempio:

* /users
* /products
* /orders

---

# Tipi di API

* GET → leggere dati
* POST → creare dati
* PUT → aggiornare dati
* DELETE → eliminare dati

---

# POST Requests

Di default il browser invia richieste GET quando visiti una pagina.

Per inviare POST bisogna usare strumenti specifici.

---

# Come testare POST requests

## 1. curl (terminal)

```bash
curl localhost:3000/reviews -X POST --data "movie=Matrix&review=***"
```

curl è uno strumento da terminale per inviare richieste HTTP.

---

## 2. Thunder Client

Estensione di VS Code per testare API.

Permette di:

* inviare GET / POST
* vedere risposte
* testare endpoint facilmente

---

# Express Server base

Esempio tipico:

```js
const express = require("express");
const app = express();

app.get("/", (req, res) => {
  res.send("Hello World");
});

app.listen(3000, () => {
  console.log("Server running on port 3000");
});
```

---

# Come funziona Express?

## 1. Creazione server

```js
const app = express();
```

## 2. Definizione endpoint

```js
app.get("/", (req, res) => {})
```

## 3. Avvio server

```js
app.listen(3000)
```

---

# Endpoint API

Un endpoint è un URL del server che esegue una funzione.

Esempio:

```
GET /api/products
POST /api/orders
```

---

# Server e porte

Un server ascolta su una porta specifica:

* 3000 (sviluppo)
* 80 (HTTP)
* 443 (HTTPS)

---

# Concetto importante

Il server:

* non si chiude
* resta in ascolto continuo
* risponde alle richieste quando arrivano

---

# Sintesi del flusso API

1. Client invia request
2. Server riceve request
3. Server esegue logica
4. Server invia response
5. Client riceve dati

---

# Riassunto finale

* Un server ascolta su una porta e risponde alle richieste
* HTTP è il protocollo di comunicazione web
* Express semplifica la creazione di server
* Le API sono collezioni di endpoint
* POST invia dati nel body della richiesta
* curl e Thunder Client servono per testare API
* Debugging aiuta a capire il codice in esecuzione
* Breakpoint e watch variables sono strumenti fondamentali

---

# Concetti chiave da ricordare

* Server = programma che risponde a richieste
* HTTP = protocollo web
* Request = richiesta del client
* Response = risposta del server
* Express = framework per server Node.js
* API = insieme di endpoint
* POST = invio dati al server
* Debugger = strumento per analisi runtime
