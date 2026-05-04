# Lezione SQL: Transazioni, Normalizzazione, SERIAL, IDENTITY e UUID

Questa lezione tratta alcuni concetti fondamentali di SQL e PostgreSQL:

- Transazioni
- Proprietà ACID
- Normalizzazione dei dati
- Problemi della duplicazione dei dati
- Relazioni tra tabelle con `FOREIGN KEY`
- Differenza tra `SERIAL`, `IDENTITY` e `UUID`
- Esercizi pratici con `INSERT`, `SELECT`, `WHERE`, `NULL`, `LIKE`, `IN`, `AND` e `OR`

---

## 1. Transazioni in SQL

Una **transazione** è una sequenza di comandi SQL che viene eseguita come una sola unità.

Il concetto principale è:

> O tutte le operazioni vengono completate correttamente, oppure nessuna modifica viene salvata.

Questo è molto importante quando dobbiamo fare operazioni delicate, per esempio un trasferimento di denaro tra due conti bancari.

Immagina questa situazione:

1. Togli 50 euro dal conto di Alice.
2. Aggiungi 50 euro al conto di Bob.

Se la prima operazione funziona, ma la seconda fallisce, il database si troverebbe in uno stato sbagliato: Alice perderebbe soldi, ma Bob non li riceverebbe.

Per evitare questo problema si usano le transazioni.

---

## 2. Esempio di tabella per conti bancari

```sql
CREATE TABLE xaccounts (
   id SERIAL PRIMARY KEY,
   name TEXT,
   balance INT
);

INSERT INTO xaccounts (name, balance)
VALUES
   ('Alice', 100),
   ('Bob', 100);
```

Questa tabella contiene due conti:

| id | name  | balance |
|---:|-------|--------:|
| 1  | Alice | 100     |
| 2  | Bob   | 100     |

---

## 3. BEGIN, COMMIT e ROLLBACK

Per iniziare una transazione si usa:

```sql
BEGIN;
```

Per confermare e salvare le modifiche si usa:

```sql
COMMIT;
```

Per annullare le modifiche fatte dentro la transazione si usa:

```sql
ROLLBACK;
```

---

## 4. Esempio di transazione con errore

```sql
BEGIN;

UPDATE xaccounts SET balance = 9999 WHERE id = 1;
UPDATE xaccounts SET failure = 9999 WHERE id = 1;

COMMIT;
```

La prima query funziona, perché la colonna `balance` esiste.

La seconda query fallisce, perché la colonna `failure` non esiste nella tabella `xaccounts`.

La tabella ha solo queste colonne:

```text
id
name
balance
```

Quindi PostgreSQL darà un errore.

In una situazione del genere, invece di confermare con `COMMIT`, bisogna annullare con:

```sql
ROLLBACK;
```

---

## 5. Esempio corretto di transazione

```sql
BEGIN;

UPDATE xaccounts SET balance = 50 WHERE id = 1;
UPDATE xaccounts SET balance = 150 WHERE id = 2;

COMMIT;
```

Questa transazione rappresenta un trasferimento:

- Alice passa da 100 a 50.
- Bob passa da 100 a 150.

Se entrambe le query funzionano, `COMMIT` salva definitivamente le modifiche.

---

## 6. Proprietà ACID

Le transazioni devono rispettare le proprietà **ACID**.

ACID significa:

| Lettera | Termine inglese | Termine italiano | Significato semplice |
|--------|------------------|------------------|----------------------|
| A | Atomicity | Atomicità | Tutto o niente |
| C | Consistency | Consistenza | Il database resta valido |
| I | Isolation | Isolamento | Le transazioni non si disturbano tra loro |
| D | Durability | Durabilità | Dopo il `COMMIT`, i dati restano salvati |

---

## 7. Atomicità

Atomicità significa che una transazione deve essere completata interamente oppure non deve essere eseguita affatto.

Esempio:

```text
Togliere soldi ad Alice
Aggiungere soldi a Bob
```

Le due operazioni devono riuscire insieme.

Se una delle due fallisce, tutto deve essere annullato.

---

## 8. Consistenza

Consistenza significa che il database deve essere in uno stato valido prima e dopo la transazione.

Esempio:

Prima del trasferimento:

| Nome  | Saldo |
|-------|------:|
| Alice | 100   |
| Bob   | 100   |

Totale: 200

Dopo il trasferimento:

| Nome  | Saldo |
|-------|------:|
| Alice | 50    |
| Bob   | 150   |

Totale: 200

Il totale resta corretto. Non devono sparire o comparire soldi per errore.

---

## 9. Isolamento

Isolamento significa che più transazioni possono avvenire nello stesso momento senza interferire tra loro in modo pericoloso.

Per esempio, se due utenti modificano lo stesso conto nello stesso momento, PostgreSQL deve gestire la situazione in modo controllato.

---

## 10. Durabilità

Durabilità significa che, dopo un `COMMIT`, i dati sono salvati in modo permanente.

Anche se il programma si chiude o il server si riavvia, la modifica confermata rimane nel database.

---

# 11. Normalizzazione dei dati

La **normalizzazione** è una tecnica per progettare bene le tabelle di un database.

Serve a:

- evitare dati duplicati;
- mantenere i dati coerenti;
- evitare problemi durante inserimenti, modifiche o cancellazioni;
- rendere il database più facile da mantenere.

In breve:

> La normalizzazione serve a dividere i dati nel posto giusto, evitando di ripetere sempre le stesse informazioni.

---

## 12. Problema senza normalizzazione

Immagina una tabella `employees` fatta così:

```sql
CREATE TABLE employees (
   id SERIAL PRIMARY KEY,
   name TEXT NOT NULL,
   email TEXT NOT NULL UNIQUE,
   job_title TEXT,
   job_description TEXT
);
```

Questa tabella contiene:

- nome del dipendente;
- email;
- titolo del lavoro;
- descrizione del lavoro.

Esempio di inserimento:

```sql
INSERT INTO employees
    (name, email, job_title, job_description)
VALUES
    ('CoolZero91', 'cz@example.org', 'HACKER', 'HAX'),
    ('Rauli', 'rauli@example.org', 'Developer', 'Writes HTML, CSS and JavaScript code for our software projects and products'),
    ('Veera', 'veera@example.org', 'Developer', 'Writes HTML, CSS and JavaScript code for our software projects and products'),
    ('Alpi', 'alpi@example.org', 'Developer', 'Writes HTML, CSS and JavaScript code for our software projects and products'),
    ('Alice', 'alice@example.org', 'Developer', 'Writes HTML, CSS and JavaScript code for our software projects and products'),
    ('Blork', 'blork@example.org', 'Developer', 'Writes HTML, CSS and JavaScript code for our software projects and products'),
    ('Schlep', 'schlep@example.org', 'Developer', 'Writes HTML, CSS and JavaScript code for our software projects and products');
```

Il problema è che la descrizione del lavoro `Developer` viene ripetuta molte volte.

---

## 13. Problemi causati dalla duplicazione

Senza normalizzazione abbiamo diversi problemi.

### 13.1 Ridondanza

La stessa informazione viene salvata più volte.

Esempio:

```text
Developer - Writes HTML, CSS and JavaScript...
Developer - Writes HTML, CSS and JavaScript...
Developer - Writes HTML, CSS and JavaScript...
```

---

### 13.2 Update anomaly

Se la descrizione del lavoro `Developer` cambia, bisogna modificare molte righe.

Esempio:

```sql
UPDATE employees
SET job_description = 'New description'
WHERE job_title = 'Developer';
```

Se dimentichi una riga, il database diventa incoerente.

---

### 13.3 Rischio di inconsistenza

Potresti ritrovarti con descrizioni diverse per lo stesso ruolo:

```text
Developer - Writes HTML, CSS and JavaScript
Developer - Writes software
Developer - Builds applications
```

---

### 13.4 Spreco di memoria

Stai salvando la stessa descrizione tante volte.

---

### 13.5 Rischio di errori di scrittura

Qualcuno potrebbe scrivere male il nome del lavoro:

```text
Developer
Develper
Developper
```

Il database li considera valori diversi.

---

# 14. Soluzione: creare una tabella `jobs`

Per normalizzare, si crea una tabella separata per i lavori.

```sql
CREATE TABLE jobs (
   id SERIAL PRIMARY KEY,
   title TEXT NOT NULL UNIQUE,
   description TEXT NOT NULL
);
```

Poi si inseriscono i lavori una sola volta:

```sql
INSERT INTO jobs (title, description)
VALUES
('Developer', 'Writes HTML, CSS and JavaScript code for our software projects and products'),
('Hacker', 'hax');
```

Ora la descrizione del lavoro non viene più ripetuta in ogni dipendente.

---

## 15. Collegare `employees` e `jobs`

Aggiungiamo una colonna `job_id` alla tabella `employees`:

```sql
ALTER TABLE employees ADD COLUMN job_id INT;
```

Poi creiamo una `FOREIGN KEY`:

```sql
ALTER TABLE employees ADD FOREIGN KEY (job_id) REFERENCES jobs(id);
```

Questo significa:

> Il valore di `employees.job_id` deve corrispondere a un `id` esistente nella tabella `jobs`.

---

## 16. Aggiornare i dati esistenti

Ora assegniamo il lavoro corretto ai dipendenti:

```sql
UPDATE employees SET job_id = 1 WHERE job_title = 'Developer';
UPDATE employees SET job_id = 2 WHERE job_title = 'HACKER';
```

Quindi:

- chi era `Developer` riceve `job_id = 1`;
- chi era `HACKER` riceve `job_id = 2`.

---

## 17. Eliminare le colonne duplicate

Dopo aver spostato i dati del lavoro nella tabella `jobs`, possiamo eliminare le colonne vecchie:

```sql
ALTER TABLE employees DROP COLUMN job_title;
ALTER TABLE employees DROP COLUMN job_description;
```

A questo punto:

- `employees` contiene i dati dei dipendenti;
- `jobs` contiene i dati dei lavori;
- `employees.job_id` collega ogni dipendente al suo lavoro.

---

## 18. Leggere i dati con JOIN

Dopo la normalizzazione, per mostrare insieme dipendente e lavoro serve una `JOIN`.

```sql
SELECT 
   e.name,
   e.email,
   j.title AS job_title,
   j.description AS job_description
FROM employees e
JOIN jobs j ON e.job_id = j.id
ORDER BY e.name;
```

Questa query dice:

> Prendi i dipendenti da `employees`, collegali ai lavori in `jobs`, e mostrami nome, email, titolo del lavoro e descrizione.

---

## 19. Vantaggi della normalizzazione

La soluzione normalizzata ha molti vantaggi:

- la descrizione del lavoro viene salvata una sola volta;
- se cambia una descrizione, bisogna aggiornare una sola riga;
- tutti i dipendenti collegati a quel lavoro vedono automaticamente la nuova descrizione;
- si evitano errori di scrittura;
- il database è più ordinato e facile da mantenere.

Esempio di aggiornamento:

```sql
UPDATE jobs 
SET description = 'Entwickelt moderne Webanwendungen mit React, Vue und Node.js'
WHERE title = 'Developer';
```

Con questa query aggiorniamo la descrizione di `Developer` una sola volta.

---

## 20. Aggiungere un nuovo lavoro

```sql
INSERT INTO jobs (title, description)
VALUES
('Designer', 'beautifuls things');
```

Poi possiamo assegnare il nuovo lavoro a un dipendente:

```sql
UPDATE employees 
SET job_id = 3 
WHERE name = 'Rauli';
```

---

## 21. Trovare tutti i Developer

```sql
SELECT e.name, j.title
FROM employees AS e
JOIN jobs AS j
ON j.id = e.job_id
WHERE j.title = 'Developer';
```

Questa query mostra tutti i dipendenti che hanno il lavoro `Developer`.

---

# 22. SERIAL in PostgreSQL

`SERIAL` è un modo usato in PostgreSQL per creare automaticamente ID numerici progressivi.

Esempio:

```sql
id SERIAL PRIMARY KEY
```

Quando inserisci una nuova riga, PostgreSQL assegna automaticamente un numero:

```text
1, 2, 3, 4, 5...
```

Oggi `SERIAL` è considerato un sistema più vecchio, cioè legacy.

Funziona ancora, ma PostgreSQL mette a disposizione alternative più moderne.

---

# 23. GENERATED AS IDENTITY

Le alternative moderne a `SERIAL` sono:

```sql
GENERATED ALWAYS AS IDENTITY
```

e

```sql
GENERATED BY DEFAULT AS IDENTITY
```

---

## 24. GENERATED ALWAYS AS IDENTITY

Questa versione è più rigida.

PostgreSQL controlla l'ID e normalmente non permette di inserirne uno manualmente.

```sql
CREATE TABLE users_always (
   id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
   name TEXT NOT NULL
);

INSERT INTO users_always (name)
VALUES ('Alice'), ('Bob');
```

Se provi a inserire manualmente un ID:

```sql
INSERT INTO users_always (id, name)
VALUES (100, 'Charlie');
```

PostgreSQL darà errore, perché l'ID è gestito automaticamente.

---

## 25. GENERATED BY DEFAULT AS IDENTITY

Questa versione è più flessibile.

PostgreSQL genera l'ID automaticamente, ma permette anche di inserire un ID manualmente se necessario.

Esempio concettuale:

```sql
CREATE TABLE users_default (
   id INT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
   name TEXT NOT NULL
);
```

---

# 26. UUID in PostgreSQL

Un `UUID` è un identificatore molto lungo e difficilmente prevedibile.

Esempio:

```text
9f3b7c1e-7e3a-4f0c-b85e-1c8d6a2f9a11
```

In PostgreSQL possiamo creare una tabella con UUID così:

```sql
CREATE TABLE users (
   user_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
   user_name TEXT NOT NULL
);

INSERT INTO users (user_name)
VALUES ('Alice'), ('Bob');
```

Qui `gen_random_uuid()` genera automaticamente un UUID per ogni nuovo utente.

---

## 27. Perché UUID può essere meglio di SERIAL in una web app?

`SERIAL` genera ID progressivi e prevedibili:

```text
/users/1
/users/2
/users/3
/orders/10
/orders/11
/orders/12
```

In una web app, se questi ID sono visibili nelle URL o nelle API, un utente potrebbe provare a cambiare il numero e tentare di accedere ad altri dati.

Esempio:

```text
/orders/15
/orders/16
/orders/17
```

Questo può diventare un problema di sicurezza se il backend non controlla correttamente i permessi.

Il nome di questa vulnerabilità è spesso **IDOR**, cioè **Insecure Direct Object Reference**.

---

## 28. UUID non sostituisce l'autorizzazione

È importante capire una cosa:

> UUID non rende automaticamente sicura una route.

Anche con UUID, il backend deve sempre controllare se l'utente loggato ha il permesso di accedere a quella risorsa.

Esempio concettuale:

```ts
const order = await db.order.findUnique({
  where: { id: orderId },
});

if (order.userId !== currentUser.id) {
  return forbidden();
}
```

Quindi il problema non è `SERIAL` da solo.

Il vero problema è usare ID prevedibili senza controlli di autorizzazione.

Risposta corretta:

> `SERIAL` può essere problematico in una web app perché genera ID prevedibili. Se questi ID sono visibili nelle URL o nelle API, un utente potrebbe provare a indovinare altri ID. Tuttavia, anche usando UUID, bisogna sempre controllare i permessi lato backend. UUID riduce il rischio di indovinare gli ID, ma non sostituisce l'autenticazione e l'autorizzazione.

---

## 29. UUID e unione di dati da sistemi diversi

Un altro vantaggio degli UUID è che sono utili quando si devono unire dati provenienti da più database o sistemi diversi.

Con `SERIAL`, due database diversi potrebbero avere entrambi un utente con `id = 1`.

Esempio:

Database A:

| id | name  |
|---:|-------|
| 1  | Alice |

Database B:

| id | name |
|---:|------|
| 1  | Bob  |

Se poi unisci i dati, puoi avere conflitti di ID.

Con UUID questo rischio è molto più basso, perché ogni ID è generato in modo quasi unico a livello globale.

---

# 30. Esercizi pratici

Tabella di partenza:

```sql
CREATE TABLE persons (
    id          SERIAL PRIMARY KEY,
    name        TEXT NOT NULL,
    age         INT,
    verified    BOOLEAN DEFAULT TRUE,
    created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO persons (name, age, verified)
VALUES
    ('Alice', 28, TRUE),
    ('Bob', 35, FALSE),
    ('Charlie', 42, TRUE),
    ('Diana', 19, TRUE),
    ('Eve', 31, FALSE),
    ('Frank', 55, TRUE),
    ('Grace', 23, TRUE),
    ('Henry', 38, FALSE);
```

---

## 31. Aggiungere una nuova persona con nome Ada e età 21

```sql
INSERT INTO persons (name, age)
VALUES ('Ada', 21);
```

Non serve specificare `verified`, perché ha valore di default `TRUE`.

---

## 32. Aggiungere Linus, età 52, verified FALSE

```sql
INSERT INTO persons (name, age, verified)
VALUES ('Linus', 52, FALSE);
```

---

## 33. Aggiungere tre persone contemporaneamente

Persone da aggiungere:

- Cathryn, 41 anni
- Mikkel, 32 anni, verified FALSE
- Rodd, 63 anni

```sql
INSERT INTO persons (name, age, verified)
VALUES
    ('Cathryn', 41, TRUE),
    ('Mikkel', 32, FALSE),
    ('Rodd', 63, TRUE);
```

Oppure, siccome `verified` di default è `TRUE`, si può fare così:

```sql
INSERT INTO persons (name, age, verified)
VALUES
    ('Cathryn', 41, DEFAULT),
    ('Mikkel', 32, FALSE),
    ('Rodd', 63, DEFAULT);
```

---

## 34. Aggiungere una persona e usare RETURNING * per vedere l'ID

```sql
INSERT INTO persons (name, age)
VALUES ('Mario', 30)
RETURNING *;
```

`RETURNING *` mostra subito la riga appena inserita, incluso l'ID generato automaticamente.

---

## 35. Selezionare tutte le persone verificate

```sql
SELECT *
FROM persons
WHERE verified = TRUE;
```

Oppure più breve:

```sql
SELECT *
FROM persons
WHERE verified;
```

---

## 36. Selezionare le persone verificate e più grandi di 30 anni

```sql
SELECT *
FROM persons
WHERE verified = TRUE
AND age > 30;
```

---

## 37. Selezionare persone con età maggiore di 40 oppure minore di 25

```sql
SELECT *
FROM persons
WHERE age > 40
OR age < 25;
```

---

## 38. Selezionare persone il cui nome inizia con A

```sql
SELECT *
FROM persons
WHERE name LIKE 'A%';
```

`A%` significa:

> Il nome deve iniziare con A e poi può avere qualsiasi altro carattere.

---

## 39. Selezionare persone il cui nome contiene una a, maiuscola o minuscola

In PostgreSQL possiamo usare `ILIKE`, che ignora maiuscole e minuscole.

```sql
SELECT *
FROM persons
WHERE name ILIKE '%a%';
```

`%a%` significa:

> La lettera `a` può trovarsi in qualsiasi punto del nome.

---

## 40. Selezionare persone che si chiamano Alice, Bob o Charlie

```sql
SELECT *
FROM persons
WHERE name IN ('Alice', 'Bob', 'Charlie');
```

`IN` serve quando vogliamo controllare se un valore è presente dentro una lista.

---

## 41. Esercizio difficile: persone più grandi di 30 e verificate oppure più giovani di 20

Richiesta:

> Seleziona tutte le persone che sono o più grandi di 30 e verificate, oppure più giovani di 20.

```sql
SELECT *
FROM persons
WHERE (age > 30 AND verified = TRUE)
OR age < 20;
```

Le parentesi sono importanti perché rendono chiara la logica.

---

## 42. Aggiungere una persona Mystery senza età

```sql
INSERT INTO persons (name)
VALUES ('Mystery');
```

In questo caso `age` sarà `NULL`, perché non abbiamo inserito nessun valore.

---

## 43. Selezionare persone senza età

```sql
SELECT *
FROM persons
WHERE age IS NULL;
```

Per controllare i valori `NULL`, non si usa `=`.

Si usa:

```sql
IS NULL
```

---

## 44. Selezionare persone che hanno un'età

```sql
SELECT *
FROM persons
WHERE age IS NOT NULL;
```

---

## 45. Cosa succede con WHERE age = NULL?

Questa query è sbagliata:

```sql
SELECT *
FROM persons
WHERE age = NULL;
```

In SQL, `NULL` significa valore sconosciuto o assente.

Non puoi confrontare `NULL` con `=`.

Quindi questa query non restituisce i risultati attesi.

La forma corretta è:

```sql
SELECT *
FROM persons
WHERE age IS NULL;
```

Oppure, per trovare chi ha un'età:

```sql
SELECT *
FROM persons
WHERE age IS NOT NULL;
```

---

# 46. Riassunto finale

In questa lezione hai studiato:

## Transazioni

Le transazioni servono a eseguire più query come una sola operazione sicura.

Comandi principali:

```sql
BEGIN;
COMMIT;
ROLLBACK;
```

Concetto principale:

> Tutto o niente.

---

## ACID

Una transazione deve essere:

- atomica;
- consistente;
- isolata;
- duratura.

---

## Normalizzazione

La normalizzazione serve a evitare dati duplicati e a organizzare meglio le tabelle.

Prima:

```text
employees contiene anche job_title e job_description
```

Dopo:

```text
employees contiene i dipendenti
jobs contiene i lavori
employees.job_id collega il dipendente al lavoro
```

---

## SERIAL, IDENTITY e UUID

`SERIAL` genera ID numerici progressivi.

`GENERATED AS IDENTITY` è il modo più moderno per generare ID automatici.

`UUID` genera ID lunghi e difficili da indovinare.

Nelle web app, UUID può essere utile perché evita ID prevedibili nelle URL o nelle API, ma non sostituisce mai i controlli di autorizzazione.

---

## Query importanti della lezione

```sql
-- Inserire dati
INSERT INTO persons (name, age)
VALUES ('Ada', 21);

-- Selezionare dati
SELECT * FROM persons;

-- Filtrare con WHERE
SELECT * FROM persons
WHERE age > 30;

-- AND
SELECT * FROM persons
WHERE verified = TRUE AND age > 30;

-- OR
SELECT * FROM persons
WHERE age > 40 OR age < 25;

-- LIKE
SELECT * FROM persons
WHERE name LIKE 'A%';

-- ILIKE
SELECT * FROM persons
WHERE name ILIKE '%a%';

-- IN
SELECT * FROM persons
WHERE name IN ('Alice', 'Bob', 'Charlie');

-- NULL
SELECT * FROM persons
WHERE age IS NULL;

-- NOT NULL
SELECT * FROM persons
WHERE age IS NOT NULL;

-- JOIN
SELECT e.name, j.title
FROM employees e
JOIN jobs j ON e.job_id = j.id;
```

---

# 47. Frase da ricordare

```text
Le transazioni proteggono le operazioni.
La normalizzazione protegge la struttura dei dati.
UUID protegge dalla prevedibilità degli ID, ma non sostituisce i controlli di sicurezza.
```
