# Lezione: Operazioni CRUD con clausole WHERE in SQL

## Obiettivo della lezione

In questa lezione abbiamo studiato le operazioni fondamentali CRUD in SQL usando PostgreSQL.

CRUD significa:

| Lettera | Operazione | Comando SQL |
|---|---|---|
| C | Create | `INSERT` |
| R | Read | `SELECT` |
| U | Update | `UPDATE` |
| D | Delete | `DELETE` |

Abbiamo visto anche come usare la clausola `WHERE` per filtrare i dati e modificare o cancellare solo alcune righe specifiche.

---

## Connessione al database

Per collegarsi al database da terminale si usa il comando:

```bash
psql -U demo26 -h localhost -d test
```

Significato:

- `psql` avvia PostgreSQL da terminale
- `-U demo26` indica l’utente
- `-h localhost` indica che il database è sul computer locale
- `-d test` indica il nome del database

---

## Creazione della tabella `people`

Abbiamo creato una tabella chiamata `people`.

```sql
CREATE TABLE people (
   id SERIAL PRIMARY KEY,
   name TEXT,
   age INT,
   verified BOOLEAN DEFAULT FALSE,
   created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);
```

### Significato delle colonne

| Colonna | Tipo | Significato |
|---|---|---|
| `id` | `SERIAL PRIMARY KEY` | ID automatico e unico |
| `name` | `TEXT` | Nome della persona |
| `age` | `INT` | Età |
| `verified` | `BOOLEAN` | Indica se la persona è verificata |
| `created_at` | `TIMESTAMPTZ` | Data e ora di creazione |

### Concetti importanti

`SERIAL` crea un numero automatico che aumenta da solo: `1`, `2`, `3`, ecc.

`DEFAULT FALSE` significa che, se non inseriamo un valore, PostgreSQL userà automaticamente `FALSE`.

`DEFAULT CURRENT_TIMESTAMP` significa che, se non inseriamo una data, PostgreSQL userà automaticamente la data e l’orario attuale.

`TIMESTAMPTZ` è un timestamp con fuso orario.

---

# CREATE: inserire dati con INSERT

Per inserire una persona nella tabella:

```sql
INSERT INTO people (name, age, verified)
VALUES ('Bob', 30, TRUE);
```

---

## Inserire più righe insieme

```sql
INSERT INTO people (name, age, verified)
VALUES 
('Alice', 25, DEFAULT),
('Charlie', 35, TRUE),
('Xie', 28, TRUE),
('Ahmad', 41, TRUE),
('Petteri', 19, TRUE);
```

In questo caso inseriamo più persone con un solo comando.

`DEFAULT` significa che PostgreSQL userà il valore predefinito della colonna.

Nel caso di `verified`, il valore predefinito è `FALSE`.

---

## Omettere colonne con valore DEFAULT

Se una colonna ha un valore di default, possiamo anche non scriverla.

```sql
INSERT INTO people (name, age)
VALUES ('Diana', 27);
```

In questo caso `verified` diventerà automaticamente `FALSE`.

---

## Usare RETURNING

`RETURNING` serve per vedere subito cosa è stato creato.

```sql
INSERT INTO people (name, age)
VALUES ('Eva', 31)
RETURNING *;
```

Questo restituisce tutta la nuova riga appena inserita.

Possiamo anche chiedere solo alcune colonne:

```sql
INSERT INTO people (name, age)
VALUES ('Eva', 31)
RETURNING id, name;
```

---

## Inserire una data personalizzata

Esempio: inserire una persona chiamata Zach, di 33 anni, con `created_at` nel 2004.

```sql
INSERT INTO people (name, age, created_at)
VALUES ('Zach', 33, '2004-01-01');
```

Oppure con data e ora:

```sql
INSERT INTO people (name, age, created_at)
VALUES ('Zach', 33, '2004-05-15 10:00:00');
```

Meglio evitare formati troppo generici come:

```sql
'2004'
```

perché possono essere ambigui.

---

# READ: leggere dati con SELECT

Per mostrare tutte le righe della tabella:

```sql
SELECT * FROM people;
```

Il simbolo `*` significa: mostrami tutte le colonne.

---

## Selezionare una sola colonna

```sql
SELECT name FROM people;
```

Questo mostra solo i nomi.

---

## Usare WHERE

La clausola `WHERE` serve per filtrare i risultati.

```sql
SELECT name FROM people WHERE id = 3;
```

Questo mostra il nome della persona con `id = 3`.

---

## Operatori di confronto

```sql
SELECT name FROM people WHERE age > 30;
```

Mostra le persone con età maggiore di 30.

```sql
SELECT name FROM people WHERE age >= 30;
```

Mostra le persone con età maggiore o uguale a 30.

```sql
SELECT name FROM people WHERE age < 40;
```

Mostra le persone con età minore di 40.

```sql
SELECT name FROM people WHERE age <= 40;
```

Mostra le persone con età minore o uguale a 40.

---

## AND

`AND` combina due condizioni. Entrambe devono essere vere.

```sql
SELECT name FROM people 
WHERE age > 30 AND age < 40;
```

Mostra le persone che hanno più di 30 anni e meno di 40.

---

## BETWEEN

`BETWEEN` è una scorciatoia per indicare un intervallo.

```sql
SELECT name FROM people 
WHERE age BETWEEN 31 AND 39;
```

È inclusivo, quindi comprende anche 31 e 39.

---

## OR

`OR` significa che almeno una delle condizioni deve essere vera.

```sql
SELECT name FROM people 
WHERE age > 30 OR age < 40;
```

Attenzione: questa query è molto larga, perché quasi tutte le età potrebbero soddisfare almeno una delle due condizioni.

---

## IN

`IN` controlla se un valore si trova dentro una lista.

```sql
SELECT name FROM people 
WHERE name IN ('Alice', 'Bob', 'Charlie');
```

È molto utile quando vogliamo cercare più valori specifici.

---

## Diverso da

Per dire “diverso da” possiamo usare:

```sql
SELECT name FROM people WHERE age != 30;
```

---

## NOT

`NOT` nega una condizione.

Esempio corretto per trovare persone non verificate:

```sql
SELECT name FROM people WHERE NOT verified;
```

Oppure:

```sql
SELECT name FROM people WHERE verified = FALSE;
```

---

## LIKE

`LIKE` serve per cercare testi usando dei pattern.

### Nomi che iniziano con A

```sql
SELECT name FROM people WHERE name LIKE 'A%';
```

Il simbolo `%` significa “qualsiasi numero di caratteri”.

Quindi `'A%'` significa:

> inizia con A

---

### Nomi che contengono la lettera a

```sql
SELECT name FROM people WHERE name LIKE '%a%';
```

Significa:

> contiene una `a` in qualsiasi posizione

---

### Nomi che finiscono con 1

```sql
SELECT name FROM people WHERE name LIKE '%1';
```

Significa:

> finisce con 1

---

## Underscore `_`

Il simbolo `_` rappresenta esattamente un carattere.

```sql
SELECT name FROM people WHERE name LIKE 'B__';
```

Significa:

> nomi che iniziano con B e hanno esattamente altri due caratteri

Esempio valido:

```text
Bob
```

---

## ILIKE

In PostgreSQL esiste anche `ILIKE`, che ignora maiuscole e minuscole.

```sql
SELECT name FROM people WHERE name ILIKE 'a%';
```

Trova sia:

```text
Alice
alice
ALICE
```

Attenzione: `ILIKE` è specifico di PostgreSQL. Non tutti i database lo supportano.

---

# Condizioni complesse con parentesi

Quando usiamo `AND` e `OR` insieme, dobbiamo fare attenzione.

Esempio:

```sql
SELECT * FROM people
WHERE age < 25 OR age > 40 AND verified;
```

Questa query potrebbe non funzionare come ci aspettiamo, perché `AND` ha priorità su `OR`.

Meglio usare le parentesi:

```sql
SELECT * FROM people
WHERE (age < 25 OR age > 40) AND verified;
```

Significa:

> seleziona le persone che hanno meno di 25 anni oppure più di 40, ma solo se sono verificate.

---

# NULL

## Cos’è NULL?

`NULL` significa:

> nessun valore oppure valore sconosciuto.

Non è uguale a:

- stringa vuota `''`
- numero `0`
- valore booleano `FALSE`

Esempio:

```sql
INSERT INTO people (name, age, verified)
VALUES ('Unbekannt', NULL, NULL);
```

Qui `age` e `verified` non hanno un valore.

---

## Errore comune con NULL

Questa query non funziona correttamente:

```sql
SELECT * FROM people WHERE age = NULL;
```

Per controllare se un valore è `NULL`, bisogna usare:

```sql
SELECT * FROM people WHERE age IS NULL;
```

Per controllare se un valore non è `NULL`:

```sql
SELECT * FROM people WHERE age IS NOT NULL;
```

---

# UPDATE: modificare dati

`UPDATE` serve per modificare righe già esistenti.

Attenzione:

```sql
UPDATE people SET age = 50;
```

Questa query cambia l’età di tutte le persone nella tabella.

Per questo motivo con `UPDATE` bisogna quasi sempre usare `WHERE`.

---

## Modificare una persona specifica

```sql
UPDATE people 
SET age = 30 
WHERE id = 1;
```

Oppure:

```sql
UPDATE people 
SET age = 30 
WHERE name = 'Ricardo';
```

---

## Modificare più colonne insieme

```sql
UPDATE people
SET age = 26, verified = TRUE
WHERE name = 'Alice';
```

Questa query cambia sia `age` sia `verified`.

---

## UPDATE con condizioni più precise

```sql
UPDATE people 
SET verified = TRUE 
WHERE name = 'Alice' AND age > 30;
```

Oppure usando `IN`:

```sql
UPDATE people 
SET verified = TRUE 
WHERE name IN ('Alice', 'Bob', 'Charlie');
```

---

# DELETE: eliminare dati

`DELETE` serve per cancellare righe.

Attenzione:

```sql
DELETE FROM people;
```

Questa query cancella tutte le righe della tabella.

Per questo motivo bisogna usare quasi sempre `WHERE`.

---

## Eliminare per ID

```sql
DELETE FROM people WHERE id = 6;
```

---

## Eliminare per nome

```sql
DELETE FROM people WHERE name = 'Petteri';
```

---

## Eliminare con condizioni

```sql
DELETE FROM people 
WHERE age < 18 OR age > 65;
```

Questa query elimina le persone minorenni o con più di 65 anni.

---

## DELETE con RETURNING

`RETURNING` permette di vedere quali righe sono state eliminate.

Esempio:

```sql
DELETE FROM people
WHERE verified = FALSE
RETURNING *;
```

Questa query elimina tutte le persone non verificate e mostra le righe cancellate.

---

# COUNT()

`COUNT()` è una funzione aggregata.

Serve per contare le righe.

---

## Contare tutte le persone

```sql
SELECT COUNT(*) FROM people;
```

---

## Contare solo le persone verificate

```sql
SELECT COUNT(*) FROM people 
WHERE verified = TRUE;
```

---

## COUNT su una colonna

```sql
SELECT COUNT(age) FROM people;
```

Attenzione:

`COUNT(age)` conta solo le righe in cui `age` non è `NULL`.

Invece:

```sql
SELECT COUNT(*) FROM people;
```

conta tutte le righe.

---

# Eseguire query da un file SQL

Possiamo salvare le query in un file, ad esempio:

```text
queries.sql
```

Poi possiamo eseguire il file dal terminale:

```bash
psql -U demo26 -h localhost -d test -f queries.sql
```

Il flag `-f` significa:

> esegui le query contenute in questo file.

---

## Salvare il risultato in un file

Possiamo anche salvare l’output in un file `.txt`.

```bash
psql -U demo26 -h localhost -d test -f queries.sql > results.txt
```

Questo comando esegue `queries.sql` e salva il risultato dentro `results.txt`.

---

# Esercizi della lezione

## 1. Cancellare tutte le persone non verificate

Richiesta:

> Cancella tutte le persone che non sono verificate e usa `RETURNING` per vedere chi è stato cancellato.

Soluzione:

```sql
DELETE FROM people
WHERE verified = FALSE
RETURNING *;
```

Oppure:

```sql
DELETE FROM people
WHERE NOT verified
RETURNING *;
```

---

## 2. Aggiungere una nuova persona Matti, età 50

```sql
INSERT INTO people (name, age)
VALUES ('Matti', 50);
```

In questo caso `verified` sarà automaticamente `FALSE`, perché ha default `FALSE`.

Se vogliamo vedere subito la riga inserita:

```sql
INSERT INTO people (name, age)
VALUES ('Matti', 50)
RETURNING *;
```

---

## 3. Trovare l’ID di Matti

Se abbiamo usato `RETURNING`, l’ID viene mostrato subito.

Altrimenti possiamo usare:

```sql
SELECT id, name 
FROM people
WHERE name = 'Matti';
```

Se vogliamo essere più precisi:

```sql
SELECT id, name, age
FROM people
WHERE name = 'Matti' AND age = 50;
```

---

## 4. Come ottenere l’ultimo ID inserito in PostgreSQL?

Il metodo migliore è usare `RETURNING`.

Esempio:

```sql
INSERT INTO people (name, age)
VALUES ('Matti', 50)
RETURNING id;
```

Questo restituisce direttamente l’ID appena creato.

---

## 5. Gli ID cancellati vengono riutilizzati?

No, normalmente PostgreSQL non riutilizza gli ID generati con `SERIAL`.

Esempio:

1. Inserisco una persona con ID `6`
2. Cancello la persona con ID `6`
3. Inserisco una nuova persona
4. La nuova persona probabilmente avrà ID `7`, non `6`

Questo è normale.

Le sequenze continuano ad aumentare anche se alcune righe vengono cancellate.

---

# Esempi finali

Inserire due persone:

```sql
INSERT INTO people (name, age) VALUES ('Marc', 72);
INSERT INTO people (name, age) VALUES ('Julia', 29);
```

Mostrare le persone con più di 50 anni:

```sql
SELECT * FROM people WHERE age > 50;
```

Mostrare tutta la tabella:

```sql
SELECT * FROM people;
```

Contare tutte le persone:

```sql
SELECT COUNT(*) FROM people;
```

---

# Riassunto finale

In questa lezione abbiamo imparato a:

- creare una tabella con `CREATE TABLE`
- inserire dati con `INSERT`
- leggere dati con `SELECT`
- filtrare dati con `WHERE`
- usare operatori come `AND`, `OR`, `IN`, `LIKE`, `BETWEEN`
- gestire valori `NULL`
- modificare dati con `UPDATE`
- cancellare dati con `DELETE`
- usare `RETURNING`
- contare righe con `COUNT()`
- eseguire query da un file `.sql`
- capire perché gli ID cancellati non vengono normalmente riutilizzati

---

# Regole importanti da ricordare

## 1. UPDATE e DELETE quasi sempre con WHERE

Senza `WHERE`, rischi di modificare o cancellare tutta la tabella.

Pericoloso:

```sql
UPDATE people SET age = 50;
```

Pericoloso:

```sql
DELETE FROM people;
```

Meglio:

```sql
UPDATE people SET age = 50 WHERE id = 1;
```

```sql
DELETE FROM people WHERE id = 1;
```

---

## 2. Per NULL non usare `=`

Sbagliato:

```sql
SELECT * FROM people WHERE age = NULL;
```

Corretto:

```sql
SELECT * FROM people WHERE age IS NULL;
```

---

## 3. RETURNING è molto utile

```sql
INSERT INTO people (name, age)
VALUES ('Matti', 50)
RETURNING *;
```

```sql
DELETE FROM people
WHERE verified = FALSE
RETURNING *;
```

---

## 4. Gli ID possono avere buchi

Se cancelli una riga, il suo ID normalmente non viene riutilizzato.

Questo è normale e non è un errore.
