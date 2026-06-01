# Lezione: Concetti di Database

## Obiettivo della lezione

In questa lezione abbiamo introdotto i concetti fondamentali dei database:

- perché si usano i database
- differenza tra database e file system
- differenza tra SQL e NoSQL
- struttura di una tabella
- concetto di ERD
- installazione e uso base di PostgreSQL
- creazione di utenti, database e tabelle
- prime query SQL

---

# Database e file system

## A cosa servono i database?

I database vengono usati per salvare dati in modo organizzato, efficiente e sicuro.

Esempi di dati:

- utenti
- prodotti
- ordini
- messaggi
- pagamenti
- prenotazioni
- articoli
- commenti

---

## A cosa serve il file system?

Il file system viene usato per salvare file.

Esempi di file:

- immagini
- video
- documenti PDF
- file `.txt`
- file `.docx`
- file di codice

---

## Posso salvare dati dentro i file?

Sì, è possibile salvare dati dentro i file, ma non è sempre la soluzione migliore.

Per esempio, potrei salvare una lista di utenti in un file `.txt` o `.json`.

Il problema è che, quando i dati diventano tanti o devono essere letti e modificati spesso, i file diventano poco pratici.

---

# Perché un database è meglio di un file?

Un database è progettato apposta per gestire dati.

Rispetto ai file, un database è migliore perché:

- è più veloce nel leggere grandi quantità di dati
- permette di cercare dati facilmente
- organizza i dati in modo strutturato
- gestisce meglio tanti utenti collegati nello stesso momento
- protegge meglio dal rischio di perdita dei dati
- permette di controllare gli accessi
- può evitare molti errori
- permette di aggiornare velocemente dati collegati tra loro

---

## Spiegazione semplice

Salvare dati in un file è come scrivere tutto in un quaderno.

Funziona se hai poche informazioni.

Ma se hai migliaia di dati, cercare, modificare e controllare tutto diventa lento e complicato.

Un database invece è come un archivio organizzato con regole precise, indici, tabelle e strumenti per trovare subito ciò che serve.

---

## Mini esercizio

Domanda della lezione:

```text
In termini molto semplici, senza conoscenze precedenti sui database, perché salvare dati in un database è meglio che salvarli in un file?
```

Risposta possibile:

```text
Perché un database è costruito apposta per salvare, cercare, modificare e proteggere i dati. 
Un file può andare bene per poche informazioni, ma diventa lento e difficile da gestire quando i dati aumentano. 
Un database invece permette ricerche veloci, accesso da parte di più utenti, protezione dei dati e meno errori.
```

---

# Tipi di database: SQL e NoSQL

Esistono due grandi categorie di database:

| Tipo | Significato |
|---|---|
| SQL | Structured Query Language |
| NoSQL | Not Only SQL |

---

# Database NoSQL

I database NoSQL sono database non relazionali.

Significa che non usano necessariamente tabelle con righe e colonne come i database SQL.

I dati possono essere salvati in modi diversi.

---

## Esempi di NoSQL

### 1. Chiave-valore

Un database chiave-valore funziona come un grande oggetto JavaScript o un dizionario.

Esempio concettuale:

```js
{
  "username": "CoolZero91",
  "email": "coolZero@example.org"
}
```

Un esempio di database chiave-valore è:

```text
Redis
```

---

### 2. Documenti

Un database a documenti salva dati simili a oggetti dentro collezioni.

È simile a un array di oggetti JavaScript.

Esempio concettuale:

```js
[
  {
    "id": 1,
    "name": "Alice",
    "email": "alice@example.com"
  },
  {
    "id": 2,
    "name": "Bob",
    "email": "bob@example.com"
  }
]
```

Un esempio di database a documenti è:

```text
MongoDB
```

---

# Database SQL

I database SQL sono database relazionali.

Vengono anche chiamati:

```text
RDBMS
```

cioè:

```text
Relational Database Management System
```

In un database SQL, i dati vengono salvati in tabelle strutturate.

Le tabelle sono simili a fogli Excel o Google Sheets.

---

## Esempio di tabelle in un database

Una database per un e-commerce potrebbe avere tabelle come:

- `products`
- `orders`
- `users`
- `payments`
- `categories`

Una database per una scuola potrebbe avere tabelle come:

- `students`
- `teachers`
- `courses`
- `lessons`

---

# Database, tabelle, colonne e righe

## Cos’è un database?

Un database è una raccolta di tabelle.

Esempio:

```text
Database: shop
```

Dentro questo database potrei avere:

```text
users
products
orders
payments
```

---

## Cos’è una tabella?

Una tabella è una struttura che contiene dati dello stesso tipo.

Esempio: tabella `users`.

| id | name | email | password |
|---|---|---|---|
| 42 | CoolZero91 | coolZero@example.org | mypassword |

---

## Cosa sono le colonne?

Le colonne sono i campi della tabella.

Sono come:

- le chiavi di un oggetto JavaScript
- le intestazioni `<th>` di una tabella HTML
- le colonne di Excel

Esempio di colonne per una tabella utenti:

- `id`
- `name`
- `email`
- `password`

---

## Cosa sono le righe?

Le righe sono i singoli record della tabella.

Sono come:

- i singoli oggetti in un array
- le righe `<tr>` di una tabella HTML
- le righe di Excel

Esempio di una riga:

| id | name | email | password |
|---|---|---|---|
| 42 | CoolZero91 | coolZero@example.org | mypassword |

Questa riga rappresenta un singolo utente.

---

# ERD: Entity-Relationship Diagram

## Cos’è un ERD?

ERD significa:

```text
Entity-Relationship Diagram
```

In italiano:

```text
Diagramma Entità-Relazione
```

È un diagramma usato per rappresentare la struttura di un database.

---

## A cosa serve un ERD?

Un ERD serve per visualizzare:

- quali tabelle esistono
- quali colonne hanno le tabelle
- come le tabelle sono collegate tra loro
- quali relazioni esistono tra i dati

---

## Esempio

In un e-commerce potremmo avere:

```text
users
orders
products
```

Relazioni possibili:

- un utente può avere molti ordini
- un ordine appartiene a un utente
- un ordine può contenere molti prodotti

Un ERD aiuta a vedere queste relazioni in modo chiaro.

---

## Strumenti per creare ERD

Durante la lezione è stato indicato:

```text
https://www.drawio.com/
```

Draw.io serve per creare diagrammi, inclusi diagrammi ERD.

---

# Altri tipi di diagrammi

## Flussdiagrammi

I flussdiagrammi servono per visualizzare il flusso di un processo.

Esempio:

```text
Utente clicca login
↓
Il sistema controlla email e password
↓
Se sono corretti, entra
↓
Se sono sbagliati, mostra errore
```

---

## Diagrammi di architettura

I diagrammi di architettura servono per rappresentare la struttura generale di un sistema software.

In questi diagrammi, il database viene spesso rappresentato come un cilindro.

---

# PostgreSQL

## Cos’è PostgreSQL?

PostgreSQL è un RDBMS.

Significa che è un sistema per gestire database relazionali.

PostgreSQL è:

- gratuito
- open-source
- molto potente
- molto usato
- ricco di funzionalità

Spesso viene chiamato anche:

```text
Postgres
```

oppure, parlando del tool da terminale:

```text
psql
```

---

## Differenza tra PostgreSQL, Postgres e psql

| Nome | Significato |
|---|---|
| PostgreSQL | Nome completo del database server |
| Postgres | Nome breve usato spesso per PostgreSQL |
| psql | Strumento da terminale per comunicare con PostgreSQL |

---

# Installazione di PostgreSQL su Ubuntu

Documentazione ufficiale indicata nella lezione:

```text
https://www.postgresql.org/download/linux/ubuntu
```

Comando di installazione:

```bash
sudo apt install postgresql
```

Questo comando installa PostgreSQL e lo avvia come servizio in background.

---

## Controllare lo stato del servizio PostgreSQL

```bash
sudo systemctl status postgresql
```

Questo comando mostra se il servizio PostgreSQL è attivo.

---

# Entrare in psql con l’utente postgres

PostgreSQL crea un utente di default chiamato:

```text
postgres
```

Per entrare in psql con questo utente:

```bash
sudo -u postgres psql
```

---

# Creazione di un utente per esercitarsi

Per semplificare gli esercizi, nella lezione viene creato un utente chiamato:

```text
demo26
```

Questo utente viene creato con permessi molto alti.

Attenzione: questa configurazione è comoda per imparare, ma non è sicura per un progetto reale in produzione.

---

## Creare un utente con permessi da superuser

```sql
CREATE USER demo26 WITH
   SUPERUSER
   CREATEDB
   CREATEROLE
   CONNECTION LIMIT -1;
```

---

## Significato dei permessi

| Permesso | Significato |
|---|---|
| `SUPERUSER` | L’utente ha diritti da amministratore |
| `CREATEDB` | L’utente può creare database |
| `CREATEROLE` | L’utente può creare, modificare o cancellare altri utenti |
| `CONNECTION LIMIT -1` | Nessun limite al numero di connessioni |

---

## Aggiungere una password all’utente

```sql
ALTER USER demo26 WITH PASSWORD 'password';
```

Attenzione: nella lezione viene usata la password `password` solo per semplicità.

In un progetto reale bisogna usare una password sicura.

---

## Creare un database

```sql
CREATE DATABASE test;
```

Questo comando crea un database chiamato:

```text
test
```

---

## Operazioni eseguite

Con questi comandi abbiamo fatto tre cose:

| Comando | Cosa fa |
|---|---|
| `CREATE USER` | Crea un nuovo utente |
| `ALTER USER` | Modifica l’utente, in questo caso aggiunge la password |
| `CREATE DATABASE` | Crea un nuovo database |

---

# Uscire da psql

Per uscire da psql si può usare:

```bash
exit
```

Oppure:

```text
CTRL + D
```

Importante:

`exit` non è un comando SQL, ma un comando di psql.

---

# Collegarsi al database con il nuovo utente

Dopo aver creato l’utente `demo26` e il database `test`, possiamo collegarci così:

```bash
psql -U demo26 -h localhost -d test
```

Significato:

| Parte | Significato |
|---|---|
| `psql` | Avvia il client da terminale |
| `-U demo26` | Usa l’utente `demo26` |
| `-h localhost` | Si collega al server locale |
| `-d test` | Si collega al database `test` |

---

# Comandi utili in psql

## Mostrare utenti e permessi

```sql
\du
```

Questo comando mostra gli utenti del database e i loro permessi.

Attenzione: `\du` è un comando di psql, non SQL puro.

---

## Mostrare la struttura di una tabella

```sql
\d nome_tabella
```

Esempio:

```sql
\d Department
```

Questo comando mostra la struttura della tabella, cioè colonne, tipi e vincoli.

---

# PRIMARY KEY

## Cosa significa PRIMARY KEY?

Una `PRIMARY KEY` è una chiave primaria.

Serve per identificare in modo unico ogni riga di una tabella.

---

## Caratteristiche della PRIMARY KEY

Una `PRIMARY KEY` è:

- unica
- non può essere `NULL`
- viene indicizzata automaticamente
- può esserci una sola primary key per tabella

---

## Spiegazione semplice

Se una tabella contiene utenti, ogni utente deve avere un identificatore unico.

Esempio:

| id | name |
|---|---|
| 1 | Alice |
| 2 | Bob |
| 3 | Charlie |

L’id `1` identifica solo Alice.

L’id `2` identifica solo Bob.

Non possono esserci due utenti con lo stesso id.

---

# Creare una tabella

Durante la lezione è stata creata una tabella chiamata `Department`.

```sql
CREATE TABLE Department (
    deptID      SERIAL PRIMARY KEY,
    name        VARCHAR(100) NOT NULL,
    phone       VARCHAR(20)
);
```

---

## Spiegazione della tabella

| Colonna | Tipo | Significato |
|---|---|---|
| `deptID` | `SERIAL PRIMARY KEY` | ID automatico e unico del dipartimento |
| `name` | `VARCHAR(100) NOT NULL` | Nome del dipartimento, massimo 100 caratteri, obbligatorio |
| `phone` | `VARCHAR(20)` | Numero di telefono, massimo 20 caratteri |

---

## SERIAL PRIMARY KEY

```sql
deptID SERIAL PRIMARY KEY
```

Significa:

- PostgreSQL genera automaticamente un numero
- il valore è unico
- il valore non può essere `NULL`
- la colonna identifica ogni riga

---

## VARCHAR

```sql
VARCHAR(100)
```

Significa testo con massimo 100 caratteri.

```sql
VARCHAR(20)
```

Significa testo con massimo 20 caratteri.

---

## NOT NULL

```sql
name VARCHAR(100) NOT NULL
```

Significa che il campo `name` è obbligatorio.

Non possiamo inserire un dipartimento senza nome.

---

# Inserire dati in una tabella

Per aggiungere un dipartimento:

```sql
INSERT INTO Department (name, phone) 
VALUES ('Human Resources', '+49 40 123456');
```

Questo comando inserisce una nuova riga nella tabella `Department`.

---

# Leggere dati da una tabella

Per visualizzare tutti gli elementi di una tabella:

```sql
SELECT * FROM Department;
```

Formula generale:

```sql
SELECT * FROM nome_tabella;
```

Il simbolo `*` significa:

```text
mostra tutte le colonne
```

---

# Cosa abbiamo imparato con SQL

In questa lezione abbiamo usato SQL per:

- creare un utente
- modificare un utente
- creare un database
- creare una tabella
- inserire dati in una tabella
- leggere dati da una tabella
- controllare utenti e permessi
- controllare la struttura di una tabella

---

# Importante: non devi sapere tutto a memoria

Non è necessario ricordare tutta la sintassi subito.

È normale cercare i comandi quando servono.

Con la pratica, le query più comuni diventeranno automatiche.

L’obiettivo principale è capire i concetti:

- cos’è un database
- cos’è una tabella
- cosa sono righe e colonne
- cos’è una primary key
- come ci si collega a PostgreSQL
- come si creano e leggono dati

---

# Riepilogo comandi principali

## Installare PostgreSQL

```bash
sudo apt install postgresql
```

## Controllare lo stato del servizio

```bash
sudo systemctl status postgresql
```

## Entrare come utente postgres

```bash
sudo -u postgres psql
```

## Creare un utente

```sql
CREATE USER demo26 WITH
   SUPERUSER
   CREATEDB
   CREATEROLE
   CONNECTION LIMIT -1;
```

## Aggiungere password a un utente

```sql
ALTER USER demo26 WITH PASSWORD 'password';
```

## Creare un database

```sql
CREATE DATABASE test;
```

## Uscire da psql

```bash
exit
```

## Collegarsi al database

```bash
psql -U demo26 -h localhost -d test
```

## Vedere utenti e permessi

```sql
\du
```

## Creare una tabella

```sql
CREATE TABLE Department (
    deptID      SERIAL PRIMARY KEY,
    name        VARCHAR(100) NOT NULL,
    phone       VARCHAR(20)
);
```

## Inserire una riga

```sql
INSERT INTO Department (name, phone) 
VALUES ('Human Resources', '+49 40 123456');
```

## Mostrare tutti i dati

```sql
SELECT * FROM Department;
```

## Mostrare la struttura della tabella

```sql
\d Department
```

---

# Riassunto finale

Una database è un sistema ottimizzato per salvare e gestire dati.

Rispetto ai file, un database è più adatto quando i dati diventano tanti, devono essere cercati velocemente o devono essere modificati da più utenti.

I database SQL organizzano i dati in tabelle, composte da colonne e righe.

PostgreSQL è un database relazionale molto usato, gratuito e open-source.

In questa lezione abbiamo imparato a installare PostgreSQL, creare un utente, creare un database, creare una tabella, inserire dati e leggere dati con `SELECT`.

---

# Concetti chiave da ricordare

- Un database serve per salvare dati in modo organizzato.
- Un file può salvare dati, ma non è ottimizzato per ricerche, relazioni e accessi simultanei.
- SQL usa tabelle con righe e colonne.
- NoSQL può usare strutture diverse, come documenti o coppie chiave-valore.
- Un ERD serve per visualizzare la struttura del database.
- PostgreSQL è un RDBMS.
- `psql` è lo strumento da terminale per parlare con PostgreSQL.
- `PRIMARY KEY` identifica in modo unico ogni riga.
- `SERIAL` genera numeri automatici.
- `NOT NULL` rende un campo obbligatorio.
- `SELECT * FROM tabella;` mostra tutti i dati di una tabella.
