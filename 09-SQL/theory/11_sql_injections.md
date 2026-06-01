# SQL Injection (SQLi)
## Teoria completa e spiegazione

---

# Cos’è una SQL Injection?

Una SQL Injection (SQLi) è una vulnerabilità di sicurezza in cui un attaccante riesce a inserire codice SQL malevolo dentro una query del database.

Questo succede quando un’applicazione costruisce query SQL concatenando direttamente input dell’utente dentro una stringa SQL.

Il database non riesce più a distinguere tra:

- codice SQL scritto dallo sviluppatore
- dati inseriti dall’utente

Di conseguenza, l’input dell’utente viene interpretato come codice SQL reale.

---

# Perché SQL Injection è importante?

SQL Injection è una delle vulnerabilità più famose e pericolose del web moderno ed è presente da anni nella OWASP Top 10.

Una SQL Injection riuscita può permettere:

- furto di dati sensibili
- bypass dell’autenticazione
- modifica dei dati
- cancellazione di tabelle
- accesso amministrativo
- esecuzione di query arbitrarie
- in alcuni casi persino esecuzione di codice sul server

---

# Dati che possono essere rubati

Un attaccante potrebbe leggere:

- username e password
- email
- dati personali
- numeri di carta di credito
- token di autenticazione
- dati bancari
- dati aziendali

---

# Il problema fondamentale

Il problema nasce quando l’applicazione costruisce query SQL usando concatenazione di stringhe.

Esempio pericoloso:

```js
const sql = `SELECT * FROM users WHERE name = '${username}' AND password = '${password}'`;