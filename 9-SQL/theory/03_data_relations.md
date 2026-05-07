# Relazioni tra tabelle in PostgreSQL

*Traduzione e riassunto in italiano con esempi SQL*

## 1. Concetto base

PostgreSQL è un **RDBMS**, cioè un **Relational Database Management System**. Questo significa che gestisce dati organizzati in tabelle e permette di collegare tra loro più tabelle tramite relazioni.

Nell’esempio della clinica veterinaria si vogliono progettare tre tabelle:

- `clients`: contiene i clienti
- `pets`: contiene gli animali domestici
- `appointments`: contiene gli appuntamenti

Le relazioni principali sono:

- Un cliente può avere molti animali.
- Un cliente può avere molti appuntamenti.
- Ogni appuntamento appartiene a un cliente.
- Ogni appuntamento è collegato a un animale.

---

## 2. Chiave primaria e chiave esterna

**Primary Key**, cioè **chiave primaria**, è l’identificatore univoco di ogni riga. Non può essere `NULL` e spesso viene definita con `SERIAL`.

**Foreign Key**, cioè **chiave esterna**, è un riferimento alla chiave primaria di un’altra tabella. Serve a garantire che i collegamenti tra tabelle siano validi.

---

## 3. Creazione delle tabelle

Di seguito la versione tradotta dell’esempio SQL per creare le tabelle della clinica veterinaria.

```sql
-- 1. Tabella clienti
CREATE TABLE clients (
   id SERIAL PRIMARY KEY,
   name TEXT NOT NULL,
   email TEXT
);

-- 2. Tabella animali (relazione 1:N con clients)
CREATE TABLE pets (
   id SERIAL PRIMARY KEY,
   name TEXT NOT NULL,
   species TEXT,
   client_id INT REFERENCES clients(id)
);

-- 3. Tabella appuntamenti
CREATE TABLE appointments (
   id SERIAL PRIMARY KEY,
   date TIMESTAMPTZ NOT NULL,
   client_id INT REFERENCES clients(id),
   pet_id INT REFERENCES pets(id)
);
```

---

## 4. Perché la Foreign Key è utile

Se provi a inserire un animale con un `client_id` inesistente, PostgreSQL blocca l’operazione per evitare dati errati.

```sql
INSERT INTO pets (name, client_id) VALUES ('Luna', 999);
```

Questo comando fallisce perché non esiste alcun cliente con `id = 999`.

In questo modo PostgreSQL protegge la coerenza del database.

---

## 5. Inserimento dei dati

```sql
INSERT INTO clients (name, email) VALUES
('Alice Weber', 'alice@example.com'),
('Bob Schneider', 'bob@example.com'),
('Clara Hoffmann', 'clara@example.com'),
('David Kim', 'david@example.com');

INSERT INTO pets (name, species, client_id) VALUES
('Veera', 'Cat', 2),
('Bello', 'Dog', 1),
('Minka', 'Cat', 1),
('Rocky', 'Dog', 2),
('Luna', 'Rabbit', 3);

INSERT INTO appointments (date, client_id, pet_id) VALUES
('2024-01-15', 2, 1),
('2024-01-20', 1, 2),
('2024-01-20', 1, 3),
('2024-01-20', 2, 4),
('2024-01-20', 3, 5);
```

---

## 6. Eliminare e ricreare le tabelle con CASCADE

Se si vogliono cancellare tutte le tabelle collegate, si può usare `CASCADE`. Questo rimuove anche gli oggetti che dipendono da esse.

```sql
DROP TABLE IF EXISTS appointments, pets, clients CASCADE;
```

Ricreando le tabelle con `ON DELETE CASCADE`, quando elimini un cliente vengono eliminati automaticamente anche gli animali e gli appuntamenti collegati.

```sql
CREATE TABLE clients (
   id SERIAL PRIMARY KEY,
   name TEXT NOT NULL,
   email TEXT
);

CREATE TABLE pets (
   id SERIAL PRIMARY KEY,
   name TEXT NOT NULL,
   species TEXT,
   client_id INT REFERENCES clients(id) ON DELETE CASCADE
);

CREATE TABLE appointments (
   id SERIAL PRIMARY KEY,
   date TIMESTAMPTZ NOT NULL,
   client_id INT REFERENCES clients(id) ON DELETE CASCADE,
   pet_id INT REFERENCES pets(id) ON DELETE CASCADE
);
```

---

## 7. JOIN: unire i dati di più tabelle

Le `JOIN` servono per combinare righe provenienti da due o più tabelle in un unico risultato.

I tipi citati nell’esempio sono:

- `INNER JOIN`
- `LEFT JOIN`
- `RIGHT JOIN`
- `FULL OUTER JOIN`

---

## 8. INNER JOIN

`INNER JOIN` mostra solo le righe che hanno una corrispondenza in entrambe le tabelle.

```sql
SELECT
   c.name AS client_name,
   p.name AS pet_name
FROM clients c
INNER JOIN pets p ON c.id = p.client_id;
```

Spiegazione semplice:

- Si parte dalla tabella `clients`.
- Per ogni cliente si cercano gli animali che hanno lo stesso `client_id`.
- Vengono mostrati solo i clienti che possiedono almeno un animale.

Per questo motivo un cliente senza animali non compare nel risultato.

Esempio con appuntamenti:

```sql
SELECT
   c.name AS client,
   p.name AS pet,
   a.date AS appointment
FROM clients c
JOIN pets p ON c.id = p.client_id
JOIN appointments a ON p.id = a.pet_id;
```

---

## 9. LEFT JOIN

`LEFT JOIN` mostra tutte le righe della tabella di sinistra e solo le righe corrispondenti della tabella di destra. Se non c’è corrispondenza, PostgreSQL restituisce `NULL` per le colonne mancanti.

```sql
SELECT
   c.name AS client,
   p.name AS pet
FROM clients c
LEFT JOIN pets p ON c.id = p.client_id;
```

Con `LEFT JOIN`:

- Tutti i clienti vengono mostrati.
- Se un cliente non ha animali, il nome dell’animale sarà `NULL`.

---

## 10. Tipi di relazioni tra tabelle

Le relazioni tra tabelle si possono ritrovare anche nei social network o in altre applicazioni.

### One-to-One

**Uno a uno**.

Esempio: un nome utente appartiene a un solo utente.

### One-to-Many

**Uno a molti**.

Esempi:

- Un utente può avere molti post.
- Un post può avere molti like.

### Many-to-Many

**Molti a molti**.

Esempio: un utente può avere molti post preferiti e ogni post può essere preferito da molti utenti.

---

## 11. Riassunto finale

PostgreSQL usa le relazioni tra tabelle per mantenere i dati collegati in modo corretto.

La **Primary Key** identifica ogni riga.

La **Foreign Key** crea il collegamento con un’altra tabella.

Le **JOIN** permettono di leggere insieme i dati collegati.

Questi concetti sono fondamentali per progettare database ordinati, sicuri e facili da interrogare.
