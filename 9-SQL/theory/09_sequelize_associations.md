# Sequelize Modellbeziehungen

## Obiettivo della lezione

In questa lezione abbiamo studiato come collegare più tabelle tra loro usando **Sequelize Associations**.

In un database reale, i dati non sono quasi mai tutti dentro una sola tabella.  
Per esempio, possiamo avere:

- una tabella `Ships`
- una tabella `Captains`
- una tabella `Sailors`
- una tabella `Certificates`

Queste tabelle devono essere collegate tra loro attraverso le **relazioni**.

---

# 1. Fremdschlüssel / Foreign Key

Un **Fremdschlüssel**, cioè una **foreign key**, è una colonna dentro una tabella che punta al **primary key** di un'altra tabella.

## Esempio semplice

```txt
Captain
id | name
1  | Jack Sparrow

Ship
id | name        | captainId
1  | Black Pearl | 1
```

In questo esempio:

- `Captain.id` è il primary key
- `Ship.captainId` è la foreign key
- `captainId` collega la nave al suo capitano

Quindi possiamo dire:

```txt
La nave Black Pearl appartiene al capitano Jack Sparrow.
```

---

# 2. Perché servono le relazioni?

Le relazioni servono per collegare dati che appartengono a tabelle diverse.

Esempio:

```txt
Un User può avere molti Post.
Un Post appartiene a un User.
```

Oppure:

```txt
Una Ship può avere molti Sailors.
Un Sailor appartiene a una Ship.
```

In Sequelize queste relazioni si chiamano **associations**.

---

# 3. Tipi di relazioni

Esistono tre tipi principali di relazioni:

1. **One-to-One** / Eins-zu-Eins
2. **One-to-Many** / Eins-zu-Viele
3. **Many-to-Many** / Viele-zu-Viele

---

# 4. One-to-One / Eins-zu-Eins

## Significato

Una relazione **one-to-one** significa:

```txt
Una cosa è collegata a una sola altra cosa.
```

## Esempio generale

```txt
Un User ha un Profile.
Un Profile appartiene a un User.
```

## Esempio della lezione

```txt
Ship - Captain
```

Possiamo leggerla così:

```txt
Una Ship ha un Captain.
Un Captain comanda una Ship.
```

---

## Codice Sequelize

```js
Captain.hasOne(Ship);
Ship.belongsTo(Captain);
```

## Dove va la foreign key?

Con questa associazione, la foreign key viene messa dentro `Ship`.

```txt
Ship
id | name        | captainId
1  | Black Pearl | 1
```

## Regola da ricordare

```txt
belongsTo = il modello che chiama belongsTo riceve la foreign key.
```

Quindi:

```js
Ship.belongsTo(Captain);
```

significa:

```txt
Ship riceve captainId.
```

---

# 5. One-to-Many / Eins-zu-Viele

## Significato

Una relazione **one-to-many** significa:

```txt
Una cosa può avere molte altre cose.
Ogni elemento figlio appartiene a un solo elemento padre.
```

## Esempio generale

```txt
Un User ha molti Posts.
Un Post appartiene a un User.
```

## Esempio della lezione

```txt
Ship - Sailor
```

Possiamo leggerla così:

```txt
Una Ship ha molti Sailors.
Un Sailor appartiene a una Ship.
```

---

## Codice Sequelize

```js
Ship.hasMany(Sailor);
Sailor.belongsTo(Ship);
```

## Dove va la foreign key?

La foreign key viene messa nella tabella `Sailor`.

```txt
Sailor
id | name  | shipId
1  | Mario | 1
2  | Luigi | 1
```

Questo significa:

```txt
Mario e Luigi appartengono alla Ship con id 1.
```

## Regola da ricordare

```txt
hasMany = il modello target riceve la foreign key.
```

Quindi:

```js
Ship.hasMany(Sailor);
```

significa:

```txt
Sailor riceve shipId.
```

---

# 6. Many-to-Many / Viele-zu-Viele

## Significato

Una relazione **many-to-many** significa:

```txt
Molti elementi possono essere collegati a molti altri elementi.
```

## Esempio generale

```txt
Un User può avere molte Roles.
Una Role può appartenere a molti Users.
```

## Esempio della lezione

```txt
Sailor - Certificate
```

Possiamo leggerla così:

```txt
Un Sailor può avere molti Certificates.
Un Certificate può appartenere a molti Sailors.
```

---

## Perché serve una join table?

In una relazione many-to-many non basta una sola foreign key.

Serve una tabella intermedia chiamata:

```txt
join table
junction table
through table
tabella ponte
```

Questa tabella contiene due foreign keys.

---

## Codice Sequelize

```js
Sailor.belongsToMany(Certificate, {
  through: "SailorCertificates",
});

Certificate.belongsToMany(Sailor, {
  through: "SailorCertificates",
});
```

## Tabella creata

```txt
SailorCertificates
sailorId | certificateId
1        | 1
1        | 2
2        | 1
```

## Esempio completo

```txt
Sailor
id | name
1  | Mario
2  | Luigi

Certificate
id | title
1  | Navigation
2  | Safety

SailorCertificates
sailorId | certificateId
1        | 1
1        | 2
2        | 1
```

Questo significa:

```txt
Mario ha i certificati Navigation e Safety.
Luigi ha il certificato Navigation.
```

---

# 7. Correzione importante dagli appunti

Negli appunti c'era scritto:

```txt
A.belongsTo(B) - Join-Tabelle wird erzeugt
```

Questa frase non è corretta.

La forma corretta è:

```js
A.belongsToMany(B, { through: "JoinTableName" });
```

Quindi:

```txt
belongsToMany = crea o usa una join table.
```

---

# 8. I quattro metodi Sequelize da ricordare

## `belongsTo`

```js
A.belongsTo(B);
```

Significa:

```txt
A appartiene a B.
```

La foreign key viene messa dentro `A`.

Esempio:

```js
Ship.belongsTo(Captain);
```

Risultato:

```txt
Ship riceve captainId.
```

---

## `hasOne`

```js
A.hasOne(B);
```

Significa:

```txt
A ha un B.
```

La foreign key viene messa dentro `B`.

Esempio:

```js
Captain.hasOne(Ship);
```

Risultato:

```txt
Ship riceve captainId.
```

---

## `hasMany`

```js
A.hasMany(B);
```

Significa:

```txt
A ha molti B.
```

La foreign key viene messa dentro `B`.

Esempio:

```js
Ship.hasMany(Sailor);
```

Risultato:

```txt
Sailor riceve shipId.
```

---

## `belongsToMany`

```js
A.belongsToMany(B, {
  through: "JoinTable",
});
```

Significa:

```txt
A è collegato a molti B.
B è collegato a molti A.
Serve una tabella ponte.
```

Esempio:

```js
Sailor.belongsToMany(Certificate, {
  through: "SailorCertificates",
});

Certificate.belongsToMany(Sailor, {
  through: "SailorCertificates",
});
```

Risultato:

```txt
Viene creata la tabella SailorCertificates.
```

---

# 9. Demo della lezione

La demo usa questi modelli:

```txt
Ship
Captain
Sailor
Certificate
```

Le relazioni sono:

```txt
Ship - Captain = one-to-one
Ship - Sailor = one-to-many
Sailor - Certificate = many-to-many
```

---

# 10. Schema completo della demo

```txt
Captain
id | name | skillLevel

Ship
id | name | crewCapacity | amountOfSails | captainId

Sailor
id | name | shipId

Certificate
id | title

SailorCertificates
sailorId | certificateId
```

---

# 11. Codice completo delle associazioni

```js
// One-to-One
Captain.hasOne(Ship);
Ship.belongsTo(Captain);

// One-to-Many
Ship.hasMany(Sailor);
Sailor.belongsTo(Ship);

// Many-to-Many
Sailor.belongsToMany(Certificate, {
  through: "SailorCertificates",
});

Certificate.belongsToMany(Sailor, {
  through: "SailorCertificates",
});
```

---

# 12. Eager Loading vs Lazy Loading

Quando abbiamo relazioni tra modelli, possiamo recuperare i dati collegati in due modi:

1. **Lazy Loading**
2. **Eager Loading**

---

## Lazy Loading

Lazy Loading significa:

```txt
Prima carico il dato principale.
Poi carico il dato collegato solo se mi serve.
```

## Esempio

```js
const ship = await Ship.findByPk(1);

const captain = await ship.getCaptain();
```

In questo caso Sequelize fa due passaggi:

```txt
1. Cerca la Ship con id 1.
2. Cerca il Captain collegato a quella Ship.
```

## Quando usarlo?

Usalo quando non sai ancora se ti servirà il dato collegato.

---

## Eager Loading

Eager Loading significa:

```txt
Carico subito il dato principale insieme ai dati collegati.
```

## Esempio

```js
const ship = await Ship.findByPk(1, {
  include: Captain,
});
```

In questo caso Sequelize recupera subito:

```txt
La Ship
Il Captain collegato
```

## Quando usarlo?

Usalo quando sai già che ti serviranno anche i dati collegati.

---

# 13. Esempi pratici con la demo

## Recuperare una Ship con il suo Captain

```js
const ship = await Ship.findByPk(1, {
  include: Captain,
});

console.log(ship);
```

---

## Recuperare una Ship con tutti i Sailors

```js
const ship = await Ship.findByPk(1, {
  include: Sailor,
});

console.log(ship);
```

---

## Recuperare un Sailor con tutti i Certificates

```js
const sailor = await Sailor.findByPk(1, {
  include: Certificate,
});

console.log(sailor);
```

---

# 14. Installazione pacchetti

Per usare Sequelize con PostgreSQL servono questi pacchetti:

```bash
bun add sequelize pg pg-hstore
```

## Significato dei pacchetti

```txt
sequelize  = ORM per lavorare con il database usando JavaScript
pg         = driver PostgreSQL
pg-hstore  = pacchetto richiesto per compatibilità con PostgreSQL
```

---

# 15. Mini glossario tedesco / italiano

| Tedesco | Italiano | Significato |
|---|---|---|
| Fremdschlüssel | Foreign key | Colonna che punta al primary key di un'altra tabella |
| Primärschlüssel | Primary key | Identificatore unico di una riga |
| Beziehung | Relazione | Collegamento tra tabelle |
| Eins-zu-Eins | One-to-One | Uno a uno |
| Eins-zu-Viele | One-to-Many | Uno a molti |
| Viele-zu-Viele | Many-to-Many | Molti a molti |
| Join-Tabelle | Join table | Tabella ponte |
| Modell | Model | Rappresentazione JS di una tabella |
| Tabelle | Table | Tabella del database |

---

# 16. Schema veloce da memorizzare

```txt
SEQUELIZE ASSOCIATIONS

1. One-to-One
Captain.hasOne(Ship)
Ship.belongsTo(Captain)

Risultato:
Ship riceve captainId


2. One-to-Many
Ship.hasMany(Sailor)
Sailor.belongsTo(Ship)

Risultato:
Sailor riceve shipId


3. Many-to-Many
Sailor.belongsToMany(Certificate, { through: "SailorCertificates" })
Certificate.belongsToMany(Sailor, { through: "SailorCertificates" })

Risultato:
Viene creata la join table SailorCertificates
```

---

# 17. Regola finale super importante

```txt
belongsTo:
la tabella che chiama belongsTo riceve la foreign key.

hasOne:
la tabella target riceve la foreign key.

hasMany:
la tabella target riceve la foreign key.

belongsToMany:
serve una join table.
```

---

# 18. Frase facile da ricordare

```txt
belongsTo = io appartengo a qualcuno, quindi io tengo la foreign key.

hasOne / hasMany = io possiedo qualcosa, quindi l'altra tabella tiene la foreign key.

belongsToMany = tanti con tanti, quindi serve una tabella ponte.
```

---

# 19. Codice finale completo

```js
import { DataTypes } from "sequelize";
import sequelize from "./lib/db.js";

const Ship = sequelize.define("Ship", {
  name: DataTypes.STRING,
  crewCapacity: DataTypes.INTEGER,
  amountOfSails: DataTypes.INTEGER,
});

const Captain = sequelize.define("Captain", {
  name: DataTypes.STRING,
  skillLevel: DataTypes.INTEGER,
});

const Sailor = sequelize.define("Sailor", {
  name: DataTypes.STRING,
});

const Certificate = sequelize.define("Certificate", {
  title: DataTypes.STRING,
});

// One-to-One
Captain.hasOne(Ship);
Ship.belongsTo(Captain);

// One-to-Many
Ship.hasMany(Sailor);
Sailor.belongsTo(Ship);

// Many-to-Many
Sailor.belongsToMany(Certificate, {
  through: "SailorCertificates",
});

Certificate.belongsToMany(Sailor, {
  through: "SailorCertificates",
});

await sequelize.sync({ force: true });
```

---

# 20. Riassunto finale

In questa lezione abbiamo imparato che:

- le tabelle possono essere collegate tra loro
- il collegamento avviene tramite foreign keys
- Sequelize permette di definire queste relazioni con metodi semplici
- `belongsTo`, `hasOne`, `hasMany` e `belongsToMany` sono i metodi principali
- per una relazione many-to-many serve una join table
- con `include` possiamo usare l'eager loading
- con metodi come `getCaptain()` possiamo usare il lazy loading

Questa è una delle parti più importanti di Sequelize, perché permette di lavorare con database relazionali in modo molto più realistico.
