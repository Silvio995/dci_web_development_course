# PRI - Testen: Testing in JavaScript

## Obiettivi della lezione

In questa lezione parliamo di **testing**, cioè dei test nel processo di sviluppo software.

Gli obiettivi principali sono:

- capire perché i test sono importanti
- capire la differenza tra test manuali e test automatici
- conoscere i principali tipi di test:
  - Unit Test
  - Integration Test
  - E2E Test
- capire cosa sono gli Unit Test
- vedere un esempio pratico con Jest
- capire cosa sono le Mock Functions
- capire quando usare i mock

---

# Cos'è il testing?

Il **testing** è una parte fondamentale dello sviluppo software.

Testare significa controllare che il codice funzioni come ci aspettiamo.

Quando scriviamo una funzione, una pagina, una API o una feature, dobbiamo verificare che il comportamento sia corretto.

Esempio:

```js
function sum(a, b) {
  return a + b;
}
```

Se chiamiamo:

```js
sum(2, 3);
```

ci aspettiamo come risultato:

```txt
5
```

Un test serve proprio a controllare che questo risultato sia corretto.

---

# Perché i test sono importanti?

I test sono importanti perché ci aiutano a capire se il codice:

- funziona correttamente
- continua a funzionare anche dopo modifiche future
- non rompe parti già esistenti dell'applicazione
- gestisce anche casi particolari o input non validi

In una grande applicazione ci possono essere:

- centinaia di file
- migliaia di righe di codice
- tante funzioni collegate tra loro
- API
- database
- componenti frontend
- logiche backend

È impossibile testare tutto manualmente dopo ogni modifica.

Per questo usiamo i **test automatici**.

---

# Test manuali e test automatici

## Test manuali

Un test manuale è quando controlliamo qualcosa noi direttamente.

Esempio:

- apro il browser
- provo il login
- inserisco email e password
- clicco sul bottone
- controllo se vengo reindirizzato alla dashboard

Il problema è che questo richiede tempo e può essere facile dimenticare qualcosa.

## Test automatici

Un test automatico è codice che controlla altro codice.

Esempio:

```js
test("somma due numeri", () => {
  expect(sum(2, 3)).toBe(5);
});
```

Il vantaggio è che possiamo eseguire tanti test con un solo comando.

---

# Esempio pratico: comparePassword

Immaginiamo di avere una funzione:

```js
comparePassword(pw, hash)
```

Questa funzione serve a confrontare una password normale con una password criptata.

Per migliorare la sicurezza, decidiamo di cambiare l'algoritmo standard di criptazione delle password.

A questo punto dobbiamo assicurarci che la funzione continui a funzionare.

Dobbiamo testare che:

- funzioni con i vecchi hash
- funzioni con i nuovi hash
- funzioni con input non validi
- non faccia crashare l'app

Esempio di casi da testare:

```txt
password corretta + vecchio hash = true
password sbagliata + vecchio hash = false
password corretta + nuovo hash = true
password sbagliata + nuovo hash = false
input non valido = comportamento sicuro
```

Questo esempio dimostra una cosa importante:

> Non bisogna testare solo il caso felice, ma anche i casi limite e gli errori.

---

# Tipi principali di test

Esistono diversi metodi di testing.

In questa lezione vengono citati tre tipi principali:

1. Unit Test
2. Integration Test
3. E2E Test

---

## 1. Unit Test

Gli **Unit Test** controllano una piccola parte isolata del codice.

Di solito testano una singola funzione.

Esempio:

```js
function pos(number) {
  if (typeof number !== "number") {
    return 0;
  }

  if (number < 0) {
    return number * -1;
  }

  return number;
}
```

Questa funzione prende un numero e restituisce il suo valore positivo.

Esempi:

```txt
pos(-5) => 5
pos(5) => 5
pos(0) => 0
pos("x") => 0
```

Un Unit Test serve a controllare proprio questi casi.

---

## 2. Integration Test

Gli **Integration Test** controllano se più parti del programma funzionano bene insieme.

Esempio:

- una route API
- un controller
- un database
- una funzione di validazione

Un integration test non controlla solo una funzione isolata, ma verifica se più componenti collaborano correttamente.

Esempio pratico:

```txt
POST /login
→ riceve email e password
→ controlla il database
→ confronta la password
→ restituisce un token
```

Questo test controlla l'integrazione tra API, database e logica di login.

---

## 3. E2E Test

Gli **E2E Test**, cioè End-to-End Test, controllano se l'app funziona come un vero utente la userebbe.

Esempio:

- apro la pagina di login
- scrivo email e password
- clicco su Login
- arrivo alla dashboard
- vedo il mio nome utente

Questi test controllano l'applicazione dall'inizio alla fine.

Sono più completi, ma anche più lenti e più complessi.

---

# Confronto tra i tipi di test

| Tipo di test | Cosa testa | Esempio |
|---|---|---|
| Unit Test | Una funzione isolata | `pos(-5)` restituisce `5` |
| Integration Test | Più parti insieme | API + database + controller |
| E2E Test | L'intera app come utente reale | Login completo dal browser |

---

# Unit Test

In questa lezione ci concentriamo soprattutto sugli **Unit Test**.

Gli Unit Test servono a testare parti indipendenti del codice.

Di solito testano:

- singole funzioni
- piccoli moduli
- logiche isolate
- helper functions

Esempio:

```js
import { pos } from "./Math";

test("converte un numero intero negativo in positivo", () => {
  expect(pos(-5)).toBe(5);
});

test("restituisce un numero positivo così com'è", () => {
  expect(pos(5)).toBe(5);
});

test("restituisce 0 quando l'input è 0", () => {
  expect(pos(0)).toBe(0);
});

test("restituisce 0 con input non valido", () => {
  expect(pos("x")).toBe(0);
});
```

---

# Analisi dell'esempio `pos`

Dai test possiamo capire cosa deve fare la funzione `pos`.

La funzione deve:

- ricevere un numero
- se il numero è negativo, restituirlo positivo
- se il numero è già positivo, restituirlo uguale
- se il numero è `0`, restituire `0`
- se l'input non è valido, restituire `0`

Possibile implementazione:

```js
export function pos(value) {
  if (typeof value !== "number") {
    return 0;
  }

  if (value < 0) {
    return value * -1;
  }

  return value;
}
```

---

# Struttura di un test

Un test con Jest di solito ha questa forma:

```js
test("descrizione del comportamento", () => {
  expect(risultato).toBe(valoreAtteso);
});
```

Vediamolo meglio:

```js
test("converte un numero negativo in positivo", () => {
  expect(pos(-5)).toBe(5);
});
```

Significato:

- `test()` definisce un test
- la stringa descrive cosa stiamo testando
- `expect()` contiene il risultato ottenuto
- `.toBe()` contiene il risultato atteso

In italiano possiamo leggerlo così:

> Mi aspetto che `pos(-5)` sia uguale a `5`.

---

# Jest

**Jest** è un framework di testing molto usato per JavaScript.

Un framework di testing ci dà strumenti per:

- scrivere test
- eseguire test
- controllare risultati
- creare mock
- verificare errori
- organizzare suite di test

Sito ufficiale:

```txt
https://jestjs.io
```

---

# Creare un nuovo progetto con Bun

Per iniziare un nuovo progetto possiamo usare Bun.

Comando:

```bash
bun init -y
```

Questo comando crea un nuovo progetto con configurazione base.

Poi installiamo Jest:

```bash
bun add jest
```

---

# Esempio struttura progetto

Una struttura semplice può essere:

```txt
project/
├── Math.js
├── Math.test.js
├── package.json
```

File `Math.js`:

```js
export function pos(value) {
  if (typeof value !== "number") {
    return 0;
  }

  return value < 0 ? value * -1 : value;
}
```

File `Math.test.js`:

```js
import { pos } from "./Math.js";

test("converte un numero intero negativo in positivo", () => {
  expect(pos(-5)).toBe(5);
});

test("restituisce un numero positivo così com'è", () => {
  expect(pos(5)).toBe(5);
});

test("restituisce 0 quando l'input è 0", () => {
  expect(pos(0)).toBe(0);
});

test("restituisce 0 con input non valido", () => {
  expect(pos("x")).toBe(0);
});
```

---

# Comando per eseguire i test

Di solito si configura uno script nel `package.json`.

Esempio:

```json
{
  "scripts": {
    "test": "jest"
  }
}
```

Poi si esegue:

```bash
bun test
```

oppure, se configurato con npm:

```bash
npm test
```

---

# Cosa significa testare bene?

Testare bene non significa scrivere test solo per il caso più semplice.

Bisogna testare:

- caso normale
- caso limite
- input sbagliati
- errori
- valori vuoti
- valori nulli
- comportamento atteso quando qualcosa va male

Esempio con `pos`:

```txt
pos(-5)     caso normale negativo
pos(5)      caso normale positivo
pos(0)      caso limite
pos("x")    input non valido
```

---

# Mock Functions

Una **Mock Function** è una funzione finta controllata dal test.

In pratica Jest sostituisce una funzione reale con una funzione controllabile.

Questo serve quando non vogliamo usare una dipendenza reale.

Esempi di dipendenze reali:

- API esterne
- database
- filesystem
- servizi email
- servizi di pagamento
- moduli esterni

---

# Perché usare i mock?

I mock servono per:

1. isolare la logica
2. osservare le chiamate
3. simulare comportamenti
4. evitare effetti collaterali

---

## 1. Isolare la logica

Quando testiamo una funzione, vogliamo testare solo quella logica.

Non vogliamo che il test dipenda da:

- connessione internet
- database reale
- API esterna
- file reali

Esempio:

```js
async function getUserName(userId, fetchUser) {
  const user = await fetchUser(userId);
  return user.name;
}
```

Nel test possiamo passare una funzione finta:

```js
test("restituisce il nome dell'utente", async () => {
  const fakeFetchUser = jest.fn().mockResolvedValue({
    id: 1,
    name: "Mario",
  });

  const result = await getUserName(1, fakeFetchUser);

  expect(result).toBe("Mario");
});
```

Qui non stiamo chiamando un database reale.

Stiamo simulando il risultato.

---

## 2. Osservare le chiamate

Con i mock possiamo controllare:

- quante volte una funzione è stata chiamata
- con quali argomenti
- cosa ha restituito

Esempio:

```js
test("chiama la funzione con userId 1", async () => {
  const fakeFetchUser = jest.fn().mockResolvedValue({
    id: 1,
    name: "Mario",
  });

  await getUserName(1, fakeFetchUser);

  expect(fakeFetchUser).toHaveBeenCalledTimes(1);
  expect(fakeFetchUser).toHaveBeenCalledWith(1);
});
```

Qui controlliamo che `fakeFetchUser` sia stata chiamata correttamente.

---

## 3. Simulare comportamenti

Un mock può simulare:

- una risposta positiva
- un errore
- un valore vuoto
- un timeout
- un dato mancante

Esempio errore:

```js
test("gestisce errore quando fetchUser fallisce", async () => {
  const fakeFetchUser = jest.fn().mockRejectedValue(new Error("Errore API"));

  await expect(getUserName(1, fakeFetchUser)).rejects.toThrow("Errore API");
});
```

---

## 4. Evitare effetti collaterali

Un effetto collaterale è qualcosa che modifica il mondo esterno.

Esempi:

- inviare una vera email
- scrivere su un database
- fare una chiamata HTTP reale
- creare o cancellare file
- fare un pagamento reale

Nei test vogliamo evitare queste cose.

Per esempio, se testiamo una funzione che invia email, non vogliamo mandare davvero email durante il test.

Usiamo quindi un mock.

---

# Quando usare le Mock Functions?

Dovresti usare mock functions quando:

- vuoi sostituire sistemi esterni
- vuoi evitare API reali
- vuoi evitare accessi reali al database
- vuoi evitare lettura o scrittura di file reali
- vuoi controllare se una funzione è stata chiamata
- vuoi simulare errori
- vuoi testare solo la tua logica

---

# Esempio completo con mock

Immaginiamo questa funzione:

```js
export async function sendWelcomeEmail(user, emailService) {
  if (!user.email) {
    throw new Error("Email mancante");
  }

  await emailService.send(user.email, "Benvenuto!");
  return true;
}
```

Test:

```js
import { sendWelcomeEmail } from "./sendWelcomeEmail.js";

test("invia email di benvenuto", async () => {
  const fakeEmailService = {
    send: jest.fn().mockResolvedValue(true),
  };

  const user = {
    email: "mario@example.com",
  };

  const result = await sendWelcomeEmail(user, fakeEmailService);

  expect(result).toBe(true);
  expect(fakeEmailService.send).toHaveBeenCalledTimes(1);
  expect(fakeEmailService.send).toHaveBeenCalledWith(
    "mario@example.com",
    "Benvenuto!"
  );
});
```

In questo modo:

- non inviamo una vera email
- testiamo solo la logica
- controlliamo che il metodo `send` venga chiamato correttamente

---

# Frasi importanti da ricordare

## Unit Test

> Un Unit Test verifica una piccola parte isolata del codice, di solito una singola funzione.

## Integration Test

> Un Integration Test verifica se più parti del programma funzionano correttamente insieme.

## E2E Test

> Un E2E Test verifica l'applicazione completa dal punto di vista dell'utente.

## Mock Function

> Una Mock Function è una funzione finta usata per sostituire una funzione reale durante un test.

---

# Mini-cheatsheet Jest

## Definire un test

```js
test("descrizione", () => {
  // codice del test
});
```

## Controllare un valore

```js
expect(result).toBe(expectedValue);
```

## Controllare che una funzione sia stata chiamata

```js
expect(mockFunction).toHaveBeenCalled();
```

## Controllare quante volte è stata chiamata

```js
expect(mockFunction).toHaveBeenCalledTimes(1);
```

## Controllare con quali argomenti è stata chiamata

```js
expect(mockFunction).toHaveBeenCalledWith("test");
```

## Creare una mock function

```js
const mockFunction = jest.fn();
```

## Creare una mock function con valore di ritorno

```js
const mockFunction = jest.fn().mockReturnValue("ciao");
```

## Creare una mock function asincrona positiva

```js
const mockFunction = jest.fn().mockResolvedValue({ name: "Mario" });
```

## Creare una mock function asincrona con errore

```js
const mockFunction = jest.fn().mockRejectedValue(new Error("Errore"));
```

---

# Esercizio 1

Crea una funzione `pos`.

La funzione deve:

- convertire un numero negativo in positivo
- restituire un numero positivo senza modificarlo
- restituire `0` se riceve `0`
- restituire `0` se riceve un input non valido

File `Math.js`:

```js
export function pos(value) {
  if (typeof value !== "number") {
    return 0;
  }

  return value < 0 ? value * -1 : value;
}
```

File `Math.test.js`:

```js
import { pos } from "./Math.js";

test("converte un numero negativo in positivo", () => {
  expect(pos(-5)).toBe(5);
});

test("restituisce un numero positivo così com'è", () => {
  expect(pos(5)).toBe(5);
});

test("restituisce 0 se il numero è 0", () => {
  expect(pos(0)).toBe(0);
});

test("restituisce 0 se l'input non è valido", () => {
  expect(pos("x")).toBe(0);
});
```

---

# Esercizio 2

Crea una funzione `isAdult`.

La funzione deve ricevere un'età e restituire:

- `true` se l'età è maggiore o uguale a 18
- `false` se l'età è minore di 18
- `false` se l'input non è valido

Esempio:

```js
export function isAdult(age) {
  if (typeof age !== "number") {
    return false;
  }

  return age >= 18;
}
```

Test:

```js
import { isAdult } from "./isAdult.js";

test("restituisce true se l'età è 18", () => {
  expect(isAdult(18)).toBe(true);
});

test("restituisce true se l'età è maggiore di 18", () => {
  expect(isAdult(25)).toBe(true);
});

test("restituisce false se l'età è minore di 18", () => {
  expect(isAdult(16)).toBe(false);
});

test("restituisce false con input non valido", () => {
  expect(isAdult("ciao")).toBe(false);
});
```

---

# Riassunto finale

Il testing è fondamentale nello sviluppo software perché permette di controllare che il codice funzioni correttamente e continui a funzionare anche dopo modifiche future.

In applicazioni grandi non possiamo testare tutto manualmente ogni volta. Per questo usiamo i test automatici.

I principali tipi di test sono:

- **Unit Test**: testano una piccola parte isolata del codice
- **Integration Test**: testano più parti del programma insieme
- **E2E Test**: testano l'applicazione completa come farebbe un utente reale

In questa lezione ci siamo concentrati sugli Unit Test con Jest.

Abbiamo visto che un test di solito usa:

```js
test()
expect()
toBe()
```

Abbiamo anche visto le **Mock Functions**, cioè funzioni finte usate per sostituire funzioni reali durante i test.

I mock sono utili quando vogliamo evitare:

- chiamate API reali
- accessi al database
- invio di email reali
- lettura o scrittura di file reali
- altri effetti collaterali

La regola principale da ricordare è:

> Un buon test deve essere automatico, chiaro, ripetibile e deve controllare anche i casi limite.