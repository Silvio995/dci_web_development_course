# Lezione: ORM e Sequelize

## 1. Che cos'è un ORM?

**ORM** significa **Object-Relational Mapping**.

Un ORM è uno strumento che permette di collegare il mondo della programmazione, per esempio **JavaScript** o **TypeScript**, con il mondo dei database relazionali, come **PostgreSQL**.

I database SQL lavorano con:

- tabelle
- righe
- colonne
- chiavi primarie
- relazioni tra tabelle

In JavaScript e TypeScript, invece, lavoriamo spesso con:

- oggetti
- array
- metodi
- classi
- funzioni asincrone

L'ORM serve proprio a creare un ponte tra questi due mondi.

Invece di scrivere direttamente sempre query SQL, possiamo usare codice JavaScript per creare, leggere, modificare o cancellare dati nel database.

---

## 2. Perché usare un ORM?

Usare un ORM può essere utile perché:

- rende il codice più leggibile per chi lavora con JavaScript o TypeScript;
- riduce la quantità di SQL da scrivere manualmente;
- aiuta a gestire operazioni complesse sul database;
- può proteggere meglio da errori e da attacchi come la **SQL Injection**;
- permette di lavorare con oggetti invece che solo con tabelle e righe;
- è molto usato nello sviluppo backend moderno.

Esempio semplice:

```js
const users = await User.findAll();
```

Questa riga può essere più facile da leggere rispetto alla query SQL equivalente:

```sql
SELECT * FROM users;
```

---

## 3. Differenza tra SQL e ORM

Con SQL scriviamo direttamente il comando per il database.

Con un ORM scriviamo codice JavaScript/TypeScript, e l'ORM lo traduce in SQL.

### Esempio: creare un nuovo utente

Con Sequelize/ORM:

```js
const jane = await User.create({ name: 'Jane' });
```

Traduzione SQL:

```sql
INSERT INTO users (name) VALUES ('Jane');
```

---

### Esempio: trovare tutti gli utenti

Con Sequelize/ORM:

```js
const users = await User.findAll();
```

Traduzione SQL:

```sql
SELECT * FROM users;
```

---

### Esempio: trovare un progetto tramite ID

Con Sequelize/ORM:

```js
const project = await Project.findByPk(123);
```

`findByPk` significa **find by primary key**, cioè trova tramite chiave primaria.

Traduzione SQL:

```sql
SELECT * FROM projects WHERE id = 123;
```

---

## 4. Che cos'è una Primary Key?

Una **Primary Key** è un identificatore unico per ogni riga di una tabella.

Esempio tabella `users`:

| id | name  |
|---:|-------|
| 1  | Jane  |
| 2  | Mario |
| 3  | Anna  |

In questo caso `id` è la primary key.

Ogni utente ha un `id` diverso.

Quando usiamo:

```js
await User.findByPk(2);
```

stiamo dicendo:

> Cerca l'utente con primary key uguale a 2.

SQL equivalente:

```sql
SELECT * FROM users WHERE id = 2;
```

---

## 5. ORM in altri linguaggi

Gli ORM non esistono solo in JavaScript.

Sono molto usati anche in altri linguaggi di programmazione.

| Linguaggio | ORM famoso |
|---|---|
| Java | Hibernate |
| C# | Entity Framework |
| Python | Django ORM |
| PHP | Laravel Eloquent |
| Ruby | Rails Active Record |
| JavaScript/Node.js | Sequelize |

Questo significa che il concetto di ORM è molto importante nello sviluppo backend.

---

## 6. Sequelize

**Sequelize** è un ORM per **Node.js**.

È basato sulle **Promise**, quindi funziona molto bene con `async` e `await`.

Con Sequelize possiamo collegarci a diversi database SQL, tra cui:

- PostgreSQL
- MySQL
- MariaDB
- SQLite
- Microsoft SQL Server
- DB2
- Snowflake

Nel nostro corso useremo Sequelize con **PostgreSQL**.

---

## 7. Pacchetti necessari

Per usare Sequelize con PostgreSQL dobbiamo installare questi pacchetti:

```bash
bun add sequelize pg pg-hstore
```

Significato dei pacchetti:

| Pacchetto | Significato |
|---|---|
| `sequelize` | L'ORM vero e proprio |
| `pg` | Il driver che permette a Node.js di collegarsi a PostgreSQL |
| `pg-hstore` | Pacchetto usato per serializzare alcuni tipi di dati |

---

## 8. Cos'è un driver?

Un **driver** è un pacchetto che permette al nostro codice di comunicare con un database.

Sequelize è l'ORM, ma per parlare davvero con PostgreSQL ha bisogno del pacchetto `pg`.

Possiamo immaginarlo così:

```txt
Codice JavaScript
       ↓
Sequelize ORM
       ↓
Driver pg
       ↓
Database PostgreSQL
```

---

## 9. Configurare Sequelize

Esempio base di connessione a PostgreSQL:

```js
import { Sequelize } from 'sequelize';

const sequelize = new Sequelize('nome_database', 'nome_utente', 'password', {
  host: 'localhost',
  dialect: 'postgres',
});

try {
  await sequelize.authenticate();
  console.log('Connessione al database riuscita!');
} catch (error) {
  console.error('Errore di connessione:', error);
}
```

Spiegazione:

- `nome_database` è il nome del database;
- `nome_utente` è l'utente PostgreSQL;
- `password` è la password dell'utente;
- `host: 'localhost'` significa che il database è sul nostro computer;
- `dialect: 'postgres'` dice a Sequelize che stiamo usando PostgreSQL.

---

## 10. Creare un modello con Sequelize

Un **modello** rappresenta una tabella del database.

Esempio: modello `User`.

```js
import { DataTypes } from 'sequelize';
import { sequelize } from './db.js';

const User = sequelize.define('User', {
  name: {
    type: DataTypes.STRING,
    allowNull: false,
  },
  email: {
    type: DataTypes.STRING,
    allowNull: false,
    unique: true,
  },
});

export default User;
```

Questo modello rappresenta una tabella con colonne simili a queste:

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  email TEXT NOT NULL UNIQUE
);
```

Nota: Sequelize può generare nomi e strutture in modo leggermente diverso in base alla configurazione.

---

## 11. Sincronizzare il modello con il database

Per creare la tabella nel database possiamo usare:

```js
await sequelize.sync();
console.log('Tabelle sincronizzate');
```

Esempio completo:

```js
import { Sequelize, DataTypes } from 'sequelize';

const sequelize = new Sequelize('testdb', 'postgres', 'password', {
  host: 'localhost',
  dialect: 'postgres',
});

const User = sequelize.define('User', {
  name: {
    type: DataTypes.STRING,
    allowNull: false,
  },
  email: {
    type: DataTypes.STRING,
    allowNull: false,
    unique: true,
  },
});

await sequelize.sync();

console.log('Database sincronizzato');
```

---

## 12. Operazioni CRUD con Sequelize

CRUD significa:

- **Create**: creare dati
- **Read**: leggere dati
- **Update**: aggiornare dati
- **Delete**: cancellare dati

---

### CREATE - Creare un utente

```js
const user = await User.create({
  name: 'Jane',
  email: 'jane@example.com',
});

console.log(user.toJSON());
```

SQL equivalente:

```sql
INSERT INTO users (name, email)
VALUES ('Jane', 'jane@example.com');
```

---

### READ - Leggere tutti gli utenti

```js
const users = await User.findAll();

console.log(users);
```

SQL equivalente:

```sql
SELECT * FROM users;
```

---

### READ - Trovare un utente tramite ID

```js
const user = await User.findByPk(1);

console.log(user);
```

SQL equivalente:

```sql
SELECT * FROM users WHERE id = 1;
```

---

### READ - Trovare un utente con una condizione

```js
const user = await User.findOne({
  where: {
    email: 'jane@example.com',
  },
});

console.log(user);
```

SQL equivalente:

```sql
SELECT * FROM users WHERE email = 'jane@example.com' LIMIT 1;
```

---

### UPDATE - Aggiornare un utente

```js
await User.update(
  { name: 'Jane Updated' },
  {
    where: {
      id: 1,
    },
  }
);
```

SQL equivalente:

```sql
UPDATE users
SET name = 'Jane Updated'
WHERE id = 1;
```

---

### DELETE - Cancellare un utente

```js
await User.destroy({
  where: {
    id: 1,
  },
});
```

SQL equivalente:

```sql
DELETE FROM users
WHERE id = 1;
```

---

## 13. Esempio pratico completo

```js
import { Sequelize, DataTypes } from 'sequelize';

const sequelize = new Sequelize('testdb', 'postgres', 'password', {
  host: 'localhost',
  dialect: 'postgres',
});

const User = sequelize.define('User', {
  name: {
    type: DataTypes.STRING,
    allowNull: false,
  },
  email: {
    type: DataTypes.STRING,
    allowNull: false,
    unique: true,
  },
});

await sequelize.authenticate();
console.log('Connessione riuscita');

await sequelize.sync();
console.log('Tabelle pronte');

const jane = await User.create({
  name: 'Jane',
  email: 'jane@example.com',
});

console.log('Utente creato:', jane.toJSON());

const users = await User.findAll();
console.log('Tutti gli utenti:', users.map(user => user.toJSON()));

const userById = await User.findByPk(jane.id);
console.log('Utente trovato tramite ID:', userById.toJSON());

await User.update(
  { name: 'Jane Smith' },
  { where: { id: jane.id } }
);

const updatedUser = await User.findByPk(jane.id);
console.log('Utente aggiornato:', updatedUser.toJSON());

await User.destroy({
  where: { id: jane.id },
});

console.log('Utente cancellato');
```

---

## 14. Promise, async e await

Sequelize è promise-based.

Questo significa che molte operazioni non danno subito il risultato, ma restituiscono una **Promise**.

Per questo usiamo `await`.

Esempio:

```js
const users = await User.findAll();
```

Senza `await`, JavaScript non aspetterebbe il risultato del database.

---

## 15. Relazioni tra tabelle con Sequelize

Un ORM può aiutarci anche a gestire le relazioni tra tabelle.

Esempio:

- un utente può avere molti post;
- un post appartiene a un utente.

In SQL questa è una relazione **one-to-many**.

Con Sequelize possiamo scrivere:

```js
User.hasMany(Post);
Post.belongsTo(User);
```

Significato:

```txt
User 1 ─── molti Post
Post 1 ─── appartiene a 1 User
```

Esempio SQL equivalente semplificato:

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL
);

CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  user_id INT REFERENCES users(id)
);
```

---

## 16. Eager Loading e Lazy Loading

Nella documentazione di Sequelize vengono citati due concetti importanti:

- **Eager Loading**
- **Lazy Loading**

### Eager Loading

Con l'eager loading carichiamo subito anche i dati collegati.

Esempio: caricare utenti e post insieme.

```js
const users = await User.findAll({
  include: Post,
});
```

È simile a fare una query con `JOIN` in SQL.

Esempio SQL semplificato:

```sql
SELECT users.*, posts.*
FROM users
JOIN posts ON posts.user_id = users.id;
```

---

### Lazy Loading

Con il lazy loading carichiamo prima un dato, e poi solo quando serve carichiamo i dati collegati.

```js
const user = await User.findByPk(1);
const posts = await user.getPosts();
```

Prima troviamo l'utente.

Poi, in un secondo momento, chiediamo i suoi post.

---

## 17. Transazioni con Sequelize

La documentazione di Sequelize dice che Sequelize supporta le **transazioni**.

Una transazione permette di eseguire più operazioni come se fossero una sola.

Se una parte fallisce, possiamo annullare tutto.

Esempio:

```js
const transaction = await sequelize.transaction();

try {
  await User.create(
    { name: 'Mario', email: 'mario@example.com' },
    { transaction }
  );

  await User.create(
    { name: 'Anna', email: 'anna@example.com' },
    { transaction }
  );

  await transaction.commit();
  console.log('Transazione completata');
} catch (error) {
  await transaction.rollback();
  console.error('Transazione annullata:', error);
}
```

Questo è utile quando più operazioni devono riuscire tutte insieme.

---

## 18. SQL Injection e ORM

La **SQL Injection** è un tipo di attacco in cui qualcuno prova a inserire codice SQL pericoloso dentro un input.

Esempio pericoloso se scritto male:

```js
const email = "' OR '1'='1";
const query = `SELECT * FROM users WHERE email = '${email}'`;
```

Questo può diventare molto pericoloso.

Con Sequelize, invece, possiamo scrivere:

```js
const user = await User.findOne({
  where: {
    email: userInputEmail,
  },
});
```

L'ORM aiuta a gestire i valori in modo più sicuro.

Attenzione: un ORM non rende automaticamente sicura tutta l'applicazione. Bisogna comunque validare gli input e scrivere codice corretto.

---

## 19. Documentazione data dal professore: riassunto Sequelize

Il testo della documentazione presenta Sequelize come uno strumento ORM per Node.js:

> Sequelize is an easy-to-use and promise-based Node.js ORM tool.

In italiano:

> Sequelize è uno strumento ORM per Node.js, facile da usare e basato sulle Promise.

Secondo la documentazione, Sequelize supporta diversi database:

- PostgreSQL
- MySQL
- MariaDB
- SQLite
- DB2
- Microsoft SQL Server
- Snowflake

La documentazione cita anche diverse funzionalità importanti:

| Funzionalità | Significato |
|---|---|
| Transaction support | Supporto alle transazioni |
| Relations | Gestione delle relazioni tra tabelle |
| Eager loading | Caricamento immediato dei dati collegati |
| Lazy loading | Caricamento dei dati collegati solo quando servono |
| Read replication | Possibilità di leggere dati da repliche del database |
| TypeScript support | Supporto a TypeScript |
| CLI | Strumenti da terminale |

---

## 20. Risorse citate nella documentazione

La documentazione suggerisce alcune risorse utili:

- documentazione ufficiale di Sequelize;
- guida Getting Started per Sequelize 6, versione stabile;
- tabella di compatibilità dei database;
- changelog;
- discussioni della community;
- Slack di Sequelize;
- Stack Overflow;
- CLI;
- guida per TypeScript;
- strumenti per GraphQL;
- strumenti per CockroachDB;
- strumenti per YugabyteDB.

La documentazione cita anche che Sequelize sta cercando nuovi maintainer per aiutare a finalizzare e pubblicare la prossima major version.

Un **maintainer** è una persona che aiuta a mantenere e migliorare un progetto open-source.

---

## 21. Versione stabile: Sequelize 6

La documentazione consiglia di iniziare dalla guida:

```txt
Getting Started guide for Sequelize 6 stable
```

Questo significa che Sequelize 6 è la versione stabile consigliata per iniziare.

Quando impariamo Sequelize, quindi, conviene seguire gli esempi della documentazione della versione 6.

---

## 22. Concetti chiave da ricordare

- SQL lavora con tabelle, righe e colonne.
- JavaScript lavora con oggetti, array e metodi.
- Un ORM collega questi due mondi.
- Sequelize è un ORM per Node.js.
- Sequelize è promise-based, quindi usa spesso `async` e `await`.
- Con Sequelize possiamo fare operazioni CRUD.
- I modelli rappresentano le tabelle.
- Le relazioni tra tabelle possono essere gestite con metodi come `hasMany` e `belongsTo`.
- Sequelize può aiutare a scrivere codice più sicuro e più leggibile.
- PostgreSQL richiede anche il pacchetto `pg`.

---

## 23. Mini esercizi

### Esercizio 1

Spiega con parole semplici cos'è un ORM.

Risposta possibile:

> Un ORM è uno strumento che permette di usare oggetti JavaScript per lavorare con un database SQL, senza scrivere sempre direttamente query SQL.

---

### Esercizio 2

Traduci questo codice Sequelize in SQL:

```js
const users = await User.findAll();
```

Risposta:

```sql
SELECT * FROM users;
```

---

### Esercizio 3

Traduci questo codice Sequelize in SQL:

```js
const user = await User.findByPk(5);
```

Risposta:

```sql
SELECT * FROM users WHERE id = 5;
```

---

### Esercizio 4

Scrivi il comando per installare Sequelize con PostgreSQL usando Bun.

Risposta:

```bash
bun add sequelize pg pg-hstore
```

---

### Esercizio 5

Cosa significa `pk` in `findByPk`?

Risposta:

> `pk` significa primary key, cioè chiave primaria.

---

## 24. Frase riassuntiva finale

Sequelize ci permette di lavorare con un database PostgreSQL usando codice JavaScript moderno, oggetti, Promise e metodi come `create`, `findAll`, `findByPk`, `update` e `destroy`, invece di scrivere sempre manualmente query SQL.
