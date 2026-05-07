# Documento di studio: Relazioni tra tabelle, LIMIT, OFFSET e ORDER BY in SQL

## 1. Relazioni tra tabelle

Quando lavoriamo con database relazionali, le tabelle possono essere collegate tra loro tramite **relazioni**.

Per capire il tipo di relazione tra due tabelle, dobbiamo sempre guardare **entrambe le direzioni**.

La domanda da fare è:

> Un elemento della tabella A può essere collegato a uno o più elementi della tabella B?

E poi anche il contrario:

> Un elemento della tabella B può essere collegato a uno o più elementi della tabella A?

---

## 2. Tipi principali di relazione

Esistono tre tipi principali di relazione:

1. **One-to-One**
2. **One-to-Many**
3. **Many-to-Many**

---

## 3. Relazione One-to-One

Una relazione **One-to-One** significa che un elemento della prima tabella può essere collegato a un solo elemento della seconda tabella, e viceversa.

### Esempio: Paesi e capitali

```text
Paese - Capitale = ONE
Capitale - Paese = ONE

Tipo: ONE-TO-ONE
```

Esempio concreto:

```text
Italia - Roma
Francia - Parigi
Germania - Berlino
```

Ogni Paese ha una sola capitale e ogni capitale appartiene a un solo Paese.

---

## 4. Relazione One-to-Many

Una relazione **One-to-Many** significa che un elemento della prima tabella può essere collegato a molti elementi della seconda tabella, ma ogni elemento della seconda tabella appartiene a un solo elemento della prima.

### Esempio: User e Posts

Direzione 1:

```text
User A - Post 1
       - Post 2
       - Post 3
```

Un utente può avere molti post.

```text
User → Posts = MANY
```

Direzione 2:

```text
Post 1 - User A
Post 2 - User A
Post 3 - User A
```

Ogni post appartiene a un solo utente.

```text
Post → User = ONE
```

Quindi la relazione è:

```text
ONE-TO-MANY
```

Oppure, vista dall'altra direzione:

```text
MANY-TO-ONE
```

In pratica sono la stessa relazione, solo vista da lati diversi.

---

## 5. Esempio: Madre e figli

```text
Madre - Figli = MANY
Figlio - Madre = ONE

Tipo: ONE-TO-MANY
```

Una madre può avere più figli, ma ogni figlio ha una sola madre biologica.

---

## 6. Relazione Many-to-Many

Una relazione **Many-to-Many** significa che più elementi della prima tabella possono essere collegati a più elementi della seconda tabella.

### Esempio: Studenti e corsi

```text
Studente - Corsi = MANY
Corso - Studenti = MANY

Tipo: MANY-TO-MANY
```

Esempio concreto:

```text
Studente A può frequentare:
- Corso HTML
- Corso JavaScript
- Corso SQL

Corso SQL può avere:
- Studente A
- Studente B
- Studente C
```

In questo caso entrambi i lati possono avere molti collegamenti.

---

## 7. Albero decisionale per capire il tipo di relazione

Per capire il tipo di relazione, possiamo usare questo ragionamento:

### Da A verso B

Domanda:

> A può avere più B?

Se sì, allora da questo lato è **Many**.

Se no, allora da questo lato è **One**.

### Da B verso A

Domanda:

> B può avere più A?

Se sì, allora da questo lato è **Many**.

Se no, allora da questo lato è **One**.

### Combinazioni finali

```text
ONE + ONE = ONE-TO-ONE
ONE + MANY = ONE-TO-MANY
MANY + ONE = ONE-TO-MANY
MANY + MANY = MANY-TO-MANY
```

---

## 8. Dove si mette la Foreign Key?

In una relazione **One-to-Many**, la **Foreign Key** si mette nella tabella che rappresenta il lato **Many**.

Esempio:

```text
Author → Books
```

Un autore può avere molti libri.

Un libro appartiene a un solo autore.

Quindi:

```text
Author = ONE
Books = MANY
```

La Foreign Key va nella tabella `books`, perché `books` è il lato Many.

---

## 9. Esempio SQL: Authors e Books

```sql
CREATE TABLE authors (
    author_id SERIAL PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE books (
    book_id SERIAL PRIMARY KEY,
    title VARCHAR(200),
    author_id INTEGER,
    FOREIGN KEY (author_id) REFERENCES authors(author_id)
);
```

### Spiegazione

La tabella `authors` contiene gli autori.

La tabella `books` contiene i libri.

Dentro `books` c'è la colonna `author_id`, che è una **Foreign Key**.

Questa colonna collega ogni libro a un autore.

La relazione è:

```text
Authors → Books = One-to-Many
```

Oppure:

```text
1:n
```

Un autore può scrivere molti libri, ma ogni libro ha un solo autore.

---

## 10. Errore comune: creare prima la tabella sbagliata

Esempio sbagliato:

```sql
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INTEGER,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE customers (
    customer_id SERIAL PRIMARY KEY,
    name VARCHAR(100)
);
```

### Cosa c'è di sbagliato?

La tabella `orders` contiene una Foreign Key che fa riferimento alla tabella `customers`.

Il problema è che `customers` viene creata dopo.

Quindi, quando PostgreSQL prova a creare `orders`, la tabella `customers` ancora non esiste.

### Soluzione corretta

Bisogna creare prima la tabella `customers` e poi la tabella `orders`.

```sql
CREATE TABLE customers (
    customer_id SERIAL PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INTEGER,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

---

## 11. Relazioni esercizio ecommerce

Nel caso di un piccolo ecommerce, possiamo avere queste relazioni:

```text
Users → Orders
One-to-Many
```

Un utente può fare molti ordini, ma ogni ordine appartiene a un solo utente.

```text
Orders → OrderItems
One-to-Many
```

Un ordine può contenere molti elementi, ma ogni elemento appartiene a un solo ordine.

```text
Products → OrderItems
One-to-Many
```

Un prodotto può apparire in molti elementi d'ordine, ma ogni elemento d'ordine si riferisce a un solo prodotto.

```text
Categories → Products
One-to-Many
```

Una categoria può contenere molti prodotti, ma ogni prodotto appartiene a una categoria.

---

# Parte 2: Limitare i risultati di una query

## 12. Perché limitare i risultati?

In un database reale, una tabella può contenere migliaia o milioni di righe.

Spesso non vogliamo mostrare tutti i dati insieme.

Esempio:

Se cerchi libri su Amazon, non ti vengono mostrati milioni di libri tutti insieme.

Ti viene mostrata una pagina con un numero limitato di risultati.

Questo concetto si chiama **pagination**, cioè paginazione.

---

## 13. LIMIT

`LIMIT` serve per indicare quanti risultati vogliamo mostrare.

Esempio:

```sql
SELECT * FROM clients LIMIT 10;
```

Questa query mostra solo i primi 10 risultati della tabella `clients`.

---

## 14. OFFSET

`OFFSET` serve per saltare un certo numero di risultati.

Esempio:

```sql
SELECT * FROM clients LIMIT 10 OFFSET 10;
```

Questa query:

- salta i primi 10 risultati
- mostra i successivi 10 risultati

Quindi corrisponde alla seconda pagina, se ogni pagina contiene 10 risultati.

---

## 15. Pagination

La paginazione funziona combinando `LIMIT` e `OFFSET`.

Esempio con 10 risultati per pagina:

```sql
-- Pagina 1
SELECT * FROM clients LIMIT 10 OFFSET 0;

-- Pagina 2
SELECT * FROM clients LIMIT 10 OFFSET 10;

-- Pagina 3
SELECT * FROM clients LIMIT 10 OFFSET 20;

-- Pagina 4
SELECT * FROM clients LIMIT 10 OFFSET 30;
```

### Formula generale

```text
OFFSET = (numero pagina - 1) * dimensione pagina
```

Esempio:

Vogliamo la pagina 2 con 3 libri per pagina.

```text
OFFSET = (2 - 1) * 3
OFFSET = 3
```

Quindi dobbiamo usare:

```sql
LIMIT 3 OFFSET 3;
```

---

# Parte 3: Ordinare i risultati

## 16. ORDER BY

`ORDER BY` serve per ordinare i risultati di una query.

Possiamo ordinare per titolo, prezzo, data, nome o qualsiasi altra colonna.

---

## 17. Ordinamento alfabetico

```sql
SELECT * FROM books ORDER BY title;
```

Questa query ordina i libri alfabeticamente per titolo.

Di default, l'ordinamento è crescente, cioè `ASC`.

Quindi questa query:

```sql
SELECT * FROM books ORDER BY title;
```

è equivalente a:

```sql
SELECT * FROM books ORDER BY title ASC;
```

---

## 18. Ordinamento decrescente

```sql
SELECT * FROM books ORDER BY title DESC;
```

`DESC` significa descending, cioè decrescente.

Nel caso del testo, ordina dalla Z alla A.

---

## 19. Ordinare per prezzo

```sql
SELECT * FROM books ORDER BY price ASC;
```

Mostra i libri dal più economico al più costoso.

```sql
SELECT * FROM books ORDER BY price DESC;
```

Mostra i libri dal più costoso al più economico.

---

## 20. Ordinare per più colonne

```sql
SELECT * FROM books
ORDER BY price ASC, published_year DESC;
```

In questo caso SQL ordina prima per `price` in modo crescente.

Se più libri hanno lo stesso prezzo, allora li ordina per `published_year` in modo decrescente.

L'ordine delle colonne dentro `ORDER BY` è importante perché determina la priorità.

---

# Parte 4: Query realistiche

## 21. Query realistica per un webshop di libri

Immaginiamo un webshop che vende libri.

L'utente cerca:

- libri Fantasy
- sotto i 20 euro
- ordinati per data di pubblicazione
- mostrando solo i primi 10 risultati

Query:

```sql
SELECT * FROM books
WHERE genre = 'fantasy'
AND price < 20
ORDER BY publication_date
LIMIT 10;
```

### Spiegazione

```sql
WHERE genre = 'fantasy'
```

Filtra solo i libri Fantasy.

```sql
AND price < 20
```

Prende solo i libri con prezzo minore di 20 euro.

```sql
ORDER BY publication_date
```

Ordina i risultati per data di pubblicazione.

```sql
LIMIT 10
```

Mostra solo i primi 10 risultati.

---

# Parte 5: Tabella books

## 22. Creazione della tabella books

```sql
CREATE TABLE books (
   id SERIAL PRIMARY KEY,
   title TEXT NOT NULL,
   author TEXT NOT NULL,
   genre TEXT,
   price DECIMAL(10, 2),
   publication_date DATE
);
```

### Spiegazione delle colonne

```text
id
```

Identificatore unico del libro. È la Primary Key.

```text
title
```

Titolo del libro. Non può essere vuoto perché ha `NOT NULL`.

```text
author
```

Autore del libro. Anche questo è obbligatorio.

```text
genre
```

Genere del libro, per esempio `fantasy` o `sci-fi`.

```text
price
```

Prezzo del libro. `DECIMAL(10, 2)` significa che può avere due cifre decimali.

```text
publication_date
```

Data di pubblicazione del libro.

---

## 23. Inserimento dei dati

```sql
INSERT INTO books (title, author, genre, price, publication_date) VALUES
    ('The Name of the Wind',      'Patrick Rothfuss', 'fantasy', 18.99, '2007-03-27'),
    ('A Game of Thrones',         'George R.R. Martin', 'fantasy', 9.99, '1996-08-01'),
    ('Dune',                      'Frank Herbert', 'sci-fi', 14.99, '1965-08-01'),
    ('The Hobbit',                'J.R.R. Tolkien', 'fantasy', 12.99, '1937-09-21'),
    ('Neuromancer',               'William Gibson', 'sci-fi', 11.99, '1984-07-01'),
    ('The Way of Kings',          'Brandon Sanderson', 'fantasy', 21.99, '2010-08-31'),
    ('Project Hail Mary',         'Andy Weir', 'sci-fi', 16.99, '2021-05-04'),
    ('The Blade Itself',          'Joe Abercrombie', 'fantasy', 13.99, '2006-05-04'),
    ('Ender''s Game',             'Orson Scott Card', 'sci-fi', 8.99, '1985-01-15'),
    ('The Colour of Magic',       'Terry Pratchett', 'fantasy', 10.99, '1983-11-24'),
    ('Mistborn',                  'Brandon Sanderson', 'fantasy', 15.99, '2006-07-17'),
    ('The Hitchhiker''s Guide',   'Douglas Adams', 'sci-fi', 9.99, '1979-10-12');
```

Nota importante:

Nel titolo `Ender's Game`, l'apostrofo viene scritto così:

```sql
'Ender''s Game'
```

In SQL, per scrivere un apostrofo dentro una stringa, dobbiamo raddoppiarlo.

---

# Parte 6: Esercizi con SELECT

## 24. Mostrare solo i primi 5 libri

```sql
SELECT * FROM books LIMIT 5;
```

Questa query mostra solo i primi 5 libri.

---

## 25. Mostrare i successivi 5 libri

```sql
SELECT * FROM books LIMIT 5 OFFSET 5;
```

Questa query salta i primi 5 libri e mostra i successivi 5.

È come vedere la seconda pagina, con 5 libri per pagina.

---

## 26. Mostrare tutti i libri ordinati per prezzo

```sql
SELECT * FROM books ORDER BY price;
```

Questa query mostra tutti i libri dal più economico al più costoso.

`ASC` è sottinteso.

---

## 27. Mostrare tutti i Fantasy sotto i 20 euro

```sql
SELECT * FROM books
WHERE genre = 'fantasy'
AND price < 20
ORDER BY publication_date;
```

Questa query mostra:

- solo libri Fantasy
- con prezzo minore di 20 euro
- ordinati per data di pubblicazione

---

## 28. Mostrare i 3 libri Sci-Fi più costosi

```sql
SELECT * FROM books
WHERE genre = 'sci-fi'
ORDER BY price DESC
LIMIT 3;
```

Questa query mostra:

- solo libri Sci-Fi
- ordinati dal più costoso al meno costoso
- massimo 3 risultati

---

## 29. Mostrare la seconda pagina dei libri Fantasy

Richiesta:

```text
Mostra la seconda pagina dei libri Fantasy, ordinati alfabeticamente per titolo, con 3 libri per pagina.
```

Query:

```sql
SELECT * FROM books
WHERE genre = 'fantasy'
ORDER BY title ASC
LIMIT 3 OFFSET 3;
```

### Spiegazione

```sql
WHERE genre = 'fantasy'
```

Prende solo i libri Fantasy.

```sql
ORDER BY title ASC
```

Li ordina alfabeticamente per titolo, dalla A alla Z.

```sql
LIMIT 3
```

Mostra solo 3 libri per pagina.

```sql
OFFSET 3
```

Salta i primi 3 libri, cioè la prima pagina.

Formula:

```text
OFFSET = (pagina - 1) * libri per pagina
OFFSET = (2 - 1) * 3
OFFSET = 3
```

---

# Parte 7: Esempio One-to-One con employee_logins

## 30. Tabella employees

```sql
CREATE TABLE employees (
   id SERIAL PRIMARY KEY,
   name TEXT NOT NULL,
   email TEXT NOT NULL UNIQUE,
   job_id INT NOT NULL
   -- FOREIGN KEY (job_id) REFERENCES jobs(id)
);
```

Questa tabella contiene i dipendenti.

Ogni dipendente ha:

- un `id`
- un nome
- una email unica
- un `job_id`

---

## 31. Tabella employee_logins

```sql
CREATE TABLE employee_logins (
   id SERIAL PRIMARY KEY,
   employee_id INT NOT NULL UNIQUE,
   username TEXT NOT NULL UNIQUE,
   password_hash TEXT NOT NULL,
   last_login TIMESTAMP,
   failed_attempts INT DEFAULT 0,
   FOREIGN KEY (employee_id) REFERENCES employees(id) ON DELETE CASCADE
);
```

Questa tabella contiene i dati di login dei dipendenti.

---

## 32. Perché questa relazione è One-to-One?

La colonna importante è questa:

```sql
employee_id INT NOT NULL UNIQUE
```

`employee_id` è una Foreign Key verso `employees(id)`.

La parola `UNIQUE` significa che lo stesso `employee_id` può apparire una sola volta nella tabella `employee_logins`.

Quindi:

- un dipendente può avere un solo login
- un login appartiene a un solo dipendente

La relazione è quindi:

```text
ONE-TO-ONE
```

---

## 33. ON DELETE CASCADE

```sql
FOREIGN KEY (employee_id) REFERENCES employees(id) ON DELETE CASCADE
```

`ON DELETE CASCADE` significa che se un dipendente viene eliminato dalla tabella `employees`, anche il suo login viene eliminato automaticamente dalla tabella `employee_logins`.

Esempio:

```text
Elimino il dipendente Mario Rossi
→ viene eliminato anche il login collegato a Mario Rossi
```

Questo evita dati orfani, cioè dati rimasti senza collegamento valido.

---

# Riepilogo finale

## Relazioni

```text
ONE-TO-ONE
Un elemento A corrisponde a un solo elemento B.
```

Esempio:

```text
Paese - Capitale
Dipendente - Login
```

```text
ONE-TO-MANY
Un elemento A può avere molti elementi B.
```

Esempio:

```text
User - Posts
Author - Books
Customer - Orders
Category - Products
```

```text
MANY-TO-MANY
Molti elementi A possono essere collegati a molti elementi B.
```

Esempio:

```text
Studenti - Corsi
```

---

## SQL importanti

### LIMIT

```sql
SELECT * FROM books LIMIT 5;
```

Mostra solo 5 risultati.

### OFFSET

```sql
SELECT * FROM books LIMIT 5 OFFSET 5;
```

Salta i primi 5 risultati e mostra i successivi 5.

### ORDER BY

```sql
SELECT * FROM books ORDER BY title ASC;
```

Ordina i risultati alfabeticamente.

### WHERE

```sql
SELECT * FROM books WHERE genre = 'fantasy';
```

Filtra i risultati.

### Query completa

```sql
SELECT * FROM books
WHERE genre = 'fantasy'
AND price < 20
ORDER BY publication_date
LIMIT 10;
```

Questa query filtra, ordina e limita i risultati.

---

# Mini schema mentale

Quando devi leggere o scrivere una query, pensa così:

```text
SELECT = cosa voglio vedere?
FROM = da quale tabella?
WHERE = quali condizioni?
ORDER BY = in quale ordine?
LIMIT = quanti risultati?
OFFSET = da quale punto parto?
```

Esempio:

```sql
SELECT *
FROM books
WHERE genre = 'fantasy'
ORDER BY title ASC
LIMIT 3
OFFSET 3;
```

Traduzione:

```text
Mostrami tutti i dati dalla tabella books,
solo dei libri fantasy,
ordinati alfabeticamente per titolo,
mostrando 3 risultati,
saltando i primi 3.
```

