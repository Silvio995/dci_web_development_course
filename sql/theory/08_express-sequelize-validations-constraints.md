# Lezione DCI — Express, Sequelize, Product API, Validations & Constraints

## Obiettivo della lezione

In questa sessione abbiamo costruito una piccola API backend con:

- **Express** per creare il server;
- **TypeScript** per avere tipi più chiari;
- **Sequelize** per comunicare con il database;
- un modello `Product` per rappresentare una tabella di prodotti;
- rotte API per leggere e creare prodotti;
- introduzione a **validations** e **constraints** in Sequelize.

---

## File principale: `server.ts`

Il file `server.ts` contiene il server Express e le rotte principali.

```ts
import express from "express";
import type { Request, Response, NextFunction } from "express";
import db from "./lib/db.ts";
import Product from "./models/Product.ts";

const app = express();
app.use(express.json());
```

### Cosa succede qui?

```ts
const app = express();
```

Crea l'applicazione Express.

```ts
app.use(express.json());
```

Permette al server di leggere il body delle richieste in formato JSON.

Questo è fondamentale per le richieste `POST`, perché senza questo middleware `req.body` potrebbe essere vuoto.

---

## Connessione al database

```ts
await db.authenticate();
await db.sync({ force: true });
```

### `db.authenticate()`

Controlla che la connessione al database funzioni.

In pratica Sequelize prova a connettersi al database.

### `db.sync({ force: true })`

Sincronizza i modelli Sequelize con le tabelle del database.

Con:

```ts
force: true
```

Sequelize cancella e ricrea le tabelle ogni volta.

### Attenzione

`force: true` va bene per esercizi e studio, ma non va usato in produzione perché cancella i dati.

---

## Inserimento iniziale dei dati

```ts
await Product.bulkCreate([
   { name: "Laptop", price: 999.99, category: "Electronics" },
   { name: "Headphones", price: 79.99, category: "Electronics" },
   { name: "Coffee Mug", price: 12.99, category: "Kitchen" },
   { name: "Desk Chair", price: 299.99, category: "Furniture" },
   { name: "Wedding dress", price: 399.99, category: "Clothing" },
]);
```

`bulkCreate` serve per creare più record contemporaneamente.

In questo caso stiamo inserendo cinque prodotti iniziali nel database.

---

# Rotte API

## 1. Ottenere un prodotto tramite ID

```ts
app.get("/api/products/:id", async (req: Request, res: Response) => {
   try {
      console.log("params", req.params);

      const product = await Product.findByPk(req.params.id as string);

      if (!product) {
         return res.status(404).json({ msg: "Product not found" });
      }

      console.log("Direkter Zugriff", product.name);
      console.log("Zugriff über getDataValue", product.getDataValue("name"));

      res.json(product);
   } catch (error) {
      console.log(error);
      res.status(500).json({ msg: "Server error" });
   }
});
```

### Endpoint

```txt
GET /api/products/:id
```

Esempio:

```txt
GET /api/products/1
```

### Cosa significa `:id`?

`id` è un **route parameter**.

Se visiti:

```txt
/api/products/3
```

Express salva il valore `3` dentro:

```ts
req.params.id
```

---

## `findByPk`

```ts
const product = await Product.findByPk(req.params.id as string);
```

`findByPk` significa:

> Find by Primary Key

Cerca un record usando la sua chiave primaria, di solito `id`.

---

## Controllo se il prodotto non esiste

```ts
if (!product) {
   return res.status(404).json({ msg: "Product not found" });
}
```

Se il prodotto non viene trovato, Sequelize restituisce `null`.

In quel caso mandiamo una risposta:

```txt
404 Not Found
```

---

## Accesso ai dati del modello

```ts
console.log("Direkter Zugriff", product.name);
console.log("Zugriff über getDataValue", product.getDataValue("name"));
```

Hai visto due modi per accedere ai dati:

```ts
product.name
```

oppure:

```ts
product.getDataValue("name")
```

Entrambi permettono di leggere il valore del campo `name`.

---

# 2. Ottenere tutti i prodotti

```ts
app.get("/api/products", async (req: Request, res: Response) => {
   const where: any = {};

   if (req.query.category) {
      where.category = req.query.category;
   }

   const products = await Product.findAll({ where });
   res.json(products);
});
```

### Endpoint

```txt
GET /api/products
```

Restituisce tutti i prodotti.

---

## Query parameter

Hai aggiunto anche un filtro per categoria:

```txt
GET /api/products?category=Electronics
```

La parte:

```txt
?category=Electronics
```

è una **query string**.

Express la legge con:

```ts
req.query.category
```

Se la categoria esiste, viene costruito un oggetto `where`.

```ts
const where: any = {};

if (req.query.category) {
   where.category = req.query.category;
}
```

Poi Sequelize cerca i prodotti con quel filtro:

```ts
const products = await Product.findAll({ where });
```

---

## Differenza tra params e query

### Params

```txt
/api/products/1
```

Si legge con:

```ts
req.params.id
```

Serve per identificare una risorsa specifica.

### Query

```txt
/api/products?category=Electronics
```

Si legge con:

```ts
req.query.category
```

Serve per filtrare, cercare, ordinare o modificare il risultato.

---

# 3. Creare un nuovo prodotto

Nel codice iniziale avevi:

```ts
app.post("/api/products", async (req: Request, res: Response)=>{
   try {
      const product = await Product.create(req.body);
      res.status(201)
   } catch (error) {
      console.log(error);
      res.status(500).json({ msg: "Server error" });
   }
})
```

Questa rotta crea un nuovo prodotto, ma ha un piccolo problema.

Qui:

```ts
res.status(201)
```

stai impostando lo status `201`, ma non stai inviando una risposta al client.

La versione corretta è:

```ts
app.post("/api/products", async (req: Request, res: Response) => {
   try {
      const product = await Product.create(req.body);
      res.status(201).json(product);
   } catch (error) {
      console.log(error);
      res.status(500).json({ msg: "Server error" });
   }
});
```

### Endpoint

```txt
POST /api/products
```

### Body JSON di esempio

```json
{
  "name": "Table",
  "price": 149.99,
  "category": "Furniture"
}
```

### `Product.create`

```ts
const product = await Product.create(req.body);
```

Crea un nuovo record nella tabella `Product`.

### Status 201

```txt
201 Created
```

Significa che una nuova risorsa è stata creata con successo.

---

# CRUD

CRUD significa:

| Lettera | Operazione | Significato | Nel tuo codice |
|---|---|---|---|
| C | Create | creare dati | `POST /api/products` |
| R | Read | leggere dati | `GET /api/products`, `GET /api/products/:id` |
| U | Update | aggiornare dati | non ancora presente |
| D | Delete | cancellare dati | non ancora presente |

Per ora hai implementato:

```txt
Create + Read
```

Mancano ancora:

```txt
Update + Delete
```

---

# Validations & Constraints in Sequelize

La documentazione ufficiale Sequelize distingue tra:

- **Validations**
- **Constraints**

Sono due concetti simili, ma non uguali.

---

## Validations

Le **validations** sono controlli fatti da Sequelize nel codice JavaScript/TypeScript.

Esempio:

```ts
validate: {
   notEmpty: true,
   min: 0
}
```

Se una validation fallisce, Sequelize blocca l'operazione prima di mandare la query SQL al database.

Quindi:

```ts
Product.create(...)
```

può fallire già a livello JavaScript, senza arrivare al database.

---

## Constraints

Le **constraints** sono regole definite nel database SQL.

Esempio:

```ts
allowNull: false
unique: true
```

Oppure, a livello SQL:

```sql
NOT NULL
UNIQUE
```

Se una constraint fallisce, la query arriva al database, ma il database la rifiuta.

Sequelize poi riceve l'errore e lo trasforma in un errore JavaScript, per esempio:

```txt
SequelizeUniqueConstraintError
```

---

## Differenza semplice

| Concetto | Dove viene controllato? | La query SQL parte? | Esempio |
|---|---|---|---|
| Validation | in Sequelize / JavaScript | no, se fallisce | `notEmpty`, `isEmail`, `min`, `max` |
| Constraint | nel database SQL | sì | `unique`, `NOT NULL` |

---

# Esempio applicato al modello Product

Immaginiamo di voler migliorare il modello `Product`.

Un prodotto dovrebbe avere:

- un nome obbligatorio;
- un nome non vuoto;
- un prezzo obbligatorio;
- un prezzo maggiore o uguale a 0;
- una categoria obbligatoria;
- una categoria non vuota.

Esempio possibile:

```ts
import { DataTypes, Model } from "sequelize";
import db from "../lib/db.ts";

class Product extends Model {
   declare id: number;
   declare name: string;
   declare price: number;
   declare category: string;
}

Product.init(
   {
      id: {
         type: DataTypes.INTEGER,
         autoIncrement: true,
         primaryKey: true,
      },
      name: {
         type: DataTypes.STRING,
         allowNull: false,
         validate: {
            notNull: {
               msg: "Product name is required",
            },
            notEmpty: {
               msg: "Product name cannot be empty",
            },
            len: {
               args: [2, 100],
               msg: "Product name must be between 2 and 100 characters",
            },
         },
      },
      price: {
         type: DataTypes.FLOAT,
         allowNull: false,
         validate: {
            notNull: {
               msg: "Product price is required",
            },
            min: {
               args: [0],
               msg: "Product price must be greater than or equal to 0",
            },
         },
      },
      category: {
         type: DataTypes.STRING,
         allowNull: false,
         validate: {
            notNull: {
               msg: "Product category is required",
            },
            notEmpty: {
               msg: "Product category cannot be empty",
            },
         },
      },
   },
   {
      sequelize: db,
      modelName: "Product",
   }
);

export default Product;
```

---

## Spiegazione delle validations usate

### `allowNull: false`

```ts
allowNull: false
```

Dice che il campo non può essere `null`.

È speciale perché in Sequelize funziona sia come validation sia come constraint SQL.

Cioè:

- Sequelize può bloccare il valore prima della query;
- il database crea anche una constraint `NOT NULL`.

---

### `notEmpty`

```ts
notEmpty: {
   msg: "Product name cannot be empty",
}
```

Evita stringhe vuote.

Senza questa validation, un valore come questo potrebbe passare:

```json
{
  "name": "",
  "price": 10,
  "category": "Electronics"
}
```

---

### `len`

```ts
len: {
   args: [2, 100],
   msg: "Product name must be between 2 and 100 characters",
}
```

Controlla la lunghezza della stringa.

In questo esempio il nome deve avere almeno 2 caratteri e massimo 100.

---

### `min`

```ts
min: {
   args: [0],
   msg: "Product price must be greater than or equal to 0",
}
```

Controlla che il prezzo non sia negativo.

Esempio non valido:

```json
{
  "name": "Laptop",
  "price": -50,
  "category": "Electronics"
}
```

---

# Gestire gli errori di validazione nella rotta POST

Se aggiungi validations al modello, la rotta `POST` dovrebbe gestire meglio gli errori.

Versione migliorata:

```ts
app.post("/api/products", async (req: Request, res: Response) => {
   try {
      const product = await Product.create(req.body);
      res.status(201).json(product);
   } catch (error: any) {
      console.log(error);

      if (error.name === "SequelizeValidationError") {
         return res.status(400).json({
            msg: "Validation error",
            errors: error.errors.map((err: any) => err.message),
         });
      }

      res.status(500).json({ msg: "Server error" });
   }
});
```

---

## Perché usare status 400?

```txt
400 Bad Request
```

Significa:

> Il client ha mandato dati sbagliati.

Esempio:

```json
{
  "name": "",
  "price": -20,
  "category": ""
}
```

In questo caso non è un vero errore del server.

È un errore nei dati inviati dall'utente/client.

---

# Esempi di test con Thunder Client, Postman o REST Client

## 1. Creare un prodotto valido

```http
POST http://localhost:3000/api/products
Content-Type: application/json

{
  "name": "Bookshelf",
  "price": 89.99,
  "category": "Furniture"
}
```

Risposta attesa:

```txt
201 Created
```

---

## 2. Creare un prodotto senza nome

```http
POST http://localhost:3000/api/products
Content-Type: application/json

{
  "price": 89.99,
  "category": "Furniture"
}
```

Risposta attesa:

```txt
400 Bad Request
```

Possibile risposta:

```json
{
  "msg": "Validation error",
  "errors": ["Product name is required"]
}
```

---

## 3. Creare un prodotto con prezzo negativo

```http
POST http://localhost:3000/api/products
Content-Type: application/json

{
  "name": "Broken Item",
  "price": -10,
  "category": "Other"
}
```

Risposta attesa:

```txt
400 Bad Request
```

Possibile risposta:

```json
{
  "msg": "Validation error",
  "errors": ["Product price must be greater than or equal to 0"]
}
```

---

## 4. Leggere tutti i prodotti

```http
GET http://localhost:3000/api/products
```

---

## 5. Filtrare prodotti per categoria

```http
GET http://localhost:3000/api/products?category=Electronics
```

---

## 6. Leggere un prodotto tramite ID

```http
GET http://localhost:3000/api/products/1
```

---

# Possibile versione finale migliorata del `server.ts`

```ts
import express from "express";
import type { Request, Response } from "express";
import db from "./lib/db.ts";
import Product from "./models/Product.ts";

const app = express();

app.use(express.json());

await db.authenticate();
await db.sync({ force: true });

await Product.bulkCreate([
   { name: "Laptop", price: 999.99, category: "Electronics" },
   { name: "Headphones", price: 79.99, category: "Electronics" },
   { name: "Coffee Mug", price: 12.99, category: "Kitchen" },
   { name: "Desk Chair", price: 299.99, category: "Furniture" },
   { name: "Wedding dress", price: 399.99, category: "Clothing" },
]);

app.get("/api/products/:id", async (req: Request, res: Response) => {
   try {
      const product = await Product.findByPk(req.params.id);

      if (!product) {
         return res.status(404).json({ msg: "Product not found" });
      }

      res.json(product);
   } catch (error) {
      console.log(error);
      res.status(500).json({ msg: "Server error" });
   }
});

app.get("/api/products", async (req: Request, res: Response) => {
   try {
      const where: any = {};

      if (req.query.category) {
         where.category = req.query.category;
      }

      const products = await Product.findAll({ where });
      res.json(products);
   } catch (error) {
      console.log(error);
      res.status(500).json({ msg: "Server error" });
   }
});

app.post("/api/products", async (req: Request, res: Response) => {
   try {
      const product = await Product.create(req.body);
      res.status(201).json(product);
   } catch (error: any) {
      console.log(error);

      if (error.name === "SequelizeValidationError") {
         return res.status(400).json({
            msg: "Validation error",
            errors: error.errors.map((err: any) => err.message),
         });
      }

      res.status(500).json({ msg: "Server error" });
   }
});

app.listen(3000, () => {
   console.log("Server hört auf Port 3000!");
});
```

---

# Glossario

## API

Un insieme di endpoint che permettono a client e server di comunicare.

## Endpoint

Una rotta specifica del server.

Esempio:

```txt
GET /api/products
```

## Request

La richiesta inviata dal client al server.

## Response

La risposta inviata dal server al client.

## Body

I dati inviati nella richiesta, spesso in formato JSON.

## Params

Valori dinamici nella rotta.

Esempio:

```txt
/api/products/:id
```

## Query params

Valori dopo il punto interrogativo nell'URL.

Esempio:

```txt
/api/products?category=Electronics
```

## Model

Una rappresentazione JavaScript/TypeScript di una tabella del database.

## Validation

Controllo fatto da Sequelize prima di inviare la query SQL.

## Constraint

Regola del database SQL, come `UNIQUE` o `NOT NULL`.

## `findAll`

Trova più record.

## `findByPk`

Trova un record tramite primary key.

## `create`

Crea un nuovo record.

## `bulkCreate`

Crea più record contemporaneamente.

---

# Riassunto finale

In questa lezione hai imparato a costruire una prima API backend con Express e Sequelize.

Hai creato un server, collegato un database, inserito dati iniziali e creato rotte per:

- leggere tutti i prodotti;
- filtrare prodotti tramite query parameter;
- leggere un prodotto tramite ID;
- creare un nuovo prodotto con `POST`.

In più hai introdotto il concetto di **validations** e **constraints**.

Le validations servono per controllare i dati nel codice prima che arrivino al database.

Le constraints servono per proteggere i dati direttamente nel database.

In un progetto reale si usano entrambe, perché aiutano a rendere l'app più sicura, stabile e coerente.
