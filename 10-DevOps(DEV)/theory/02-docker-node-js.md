# Lezione Docker: lavorare in una Dockerized Node.js App

## 1. Obiettivo della lezione

In questa lezione stiamo imparando come lavorare con una **Node.js App dockerizzata**.

Lo scenario è simile a quello reale in azienda:

> Entro in un team, clono una repository già esistente, trovo un `Dockerfile` e devo riuscire ad avviare l’applicazione nel mio computer usando Docker.

L’obiettivo non è solo “far partire l’app”, ma capire il flusso di lavoro:

1. clonare la repo;
2. entrare nella cartella corretta;
3. costruire una Docker image;
4. avviare un container;
5. testare l’API;
6. capire quando serve ricostruire l’immagine;
7. distinguere Git, Docker image e container.

---

## 2. Concetti fondamentali

### Docker

Docker è uno strumento che permette di eseguire applicazioni dentro ambienti isolati chiamati **container**.

Serve soprattutto per evitare il classico problema:

> “Sul mio computer funziona, sul tuo no.”

Con Docker, il team può definire un ambiente uguale per tutti.

---

### Docker Hub

Docker Hub è un repository pubblico dove si trovano immagini Docker già pronte.

Esempio:

```Dockerfile
FROM node:20
```

Questa istruzione dice a Docker di partire da una immagine già pronta con Node.js 20.

---

### Image

Una **image** è una specie di pacchetto/ricetta dell’applicazione.

Contiene:

- sistema di base;
- runtime, per esempio Node.js;
- dipendenze;
- codice copiato;
- comando di avvio.

Una image non è ancora l’app in esecuzione. È il modello da cui si crea un container.

---

### Container

Un **container** è una istanza in esecuzione di una image.

Esempio:

- image = app preparata;
- container = app accesa.

Comandi collegati:

```bash
docker build
```

crea una image.

```bash
docker run
```

avvia un container.

---

### Dockerfile

Un `Dockerfile` è un file di testo senza estensione che contiene le istruzioni per costruire una image Docker.

Docker legge il Dockerfile riga per riga e crea l’immagine.

---

## 3. Perché Docker si usa con una Node.js App

In una normale app Node.js, senza Docker, ogni sviluppatore dovrebbe installare manualmente:

- Node.js;
- npm;
- le dipendenze;
- eventuali database;
- variabili ambiente;
- configurazioni locali.

Con Docker, invece, il progetto descrive l’ambiente necessario.

Quindi anche se il mio computer ha una versione diversa di Node, l’app dentro Docker può girare con la versione corretta.

Esempio:

```Dockerfile
FROM node:20
```

significa:

> Questa app deve girare in un ambiente con Node.js 20.

---

## 4. Docker serve per modificare il codice?

No, normalmente Docker non è il posto dove si modifica il codice.

Il codice si modifica nella repo locale, per esempio:

```bash
~/projects/game-of-chance-api
```

Docker serve come ambiente di esecuzione e test.

Flusso mentale corretto:

```text
Modifico il codice nella repo locale
↓
Docker esegue il codice in un ambiente isolato
↓
Testo se l’app funziona
↓
Committo con Git quando il lavoro è pronto
```

Il container non sostituisce Git e non sostituisce l’editor.

---

## 5. Git e Docker sono separati

Un commit Git non aggiorna automaticamente un container già attivo.

Git gestisce la cronologia del codice.

Docker gestisce l’ambiente in cui il codice gira.

| Azione | Cosa succede |
|---|---|
| Modifico un file | Cambia il codice locale |
| Faccio `git commit` | Salvo uno snapshot nella cronologia Git |
| Faccio `docker build` | Creo una nuova image con i file attuali |
| Faccio `docker run` | Avvio un container da una image |
| Il container è già attivo | Continua a usare la versione con cui è stato creato |

Frase chiave:

> Git salva il codice. Docker esegue il codice in un ambiente controllato.

---

## 6. Comando per costruire l’immagine

Comando usato:

```bash
sudo docker build -t game-of-chance-api .
```

Scomposizione:

### `sudo`

Esegue il comando con permessi da amministratore.

Nel nostro caso serve perché l’utente Ubuntu non ha ancora il permesso di usare Docker senza `sudo`.

---

### `docker`

Chiama il programma Docker da terminale.

---

### `build`

Dice a Docker:

> Costruisci una nuova image.

Docker cerca un file chiamato `Dockerfile` nella cartella corrente.

---

### `-t game-of-chance-api`

`-t` significa **tag**.

Serve a dare un nome alla image.

In questo caso la image si chiama:

```bash
game-of-chance-api
```

Senza tag, Docker creerebbe comunque una image, ma sarebbe meno comodo richiamarla.

---

### `.`

Il punto finale significa:

> Usa la cartella corrente come build context.

Il build context è l’insieme dei file che Docker può usare per costruire l’immagine.

Per questo è importante essere nella cartella giusta, cioè quella dove si trovano:

```text
Dockerfile
package.json
src/
```

---

### Traduzione completa del comando

```bash
sudo docker build -t game-of-chance-api .
```

significa:

> Docker, con permessi da amministratore, costruisci una image usando il Dockerfile della cartella attuale e chiamala `game-of-chance-api`.

---

## 7. Comando per avviare il container

Comando tipico:

```bash
sudo docker run -p 3000:3000 game-of-chance-api
```

Scomposizione:

### `sudo`

Serve per i permessi Docker.

---

### `docker`

Chiama Docker.

---

### `run`

Dice a Docker:

> Avvia un nuovo container.

---

### `-p 3000:3000`

Serve a collegare le porte.

Struttura:

```bash
-p PORTA_DEL_COMPUTER:PORTA_DEL_CONTAINER
```

Nel nostro caso:

```bash
-p 3000:3000
```

significa:

> Quando apro `localhost:3000` sul mio computer, inoltra la richiesta alla porta `3000` dentro il container.

Questo è necessario perché il container è isolato. Senza port mapping, il browser non riuscirebbe a raggiungere facilmente l’app.

---

### `game-of-chance-api`

È il nome della image da usare per creare il container.

---

### Traduzione completa del comando

```bash
sudo docker run -p 3000:3000 game-of-chance-api
```

significa:

> Docker, avvia un container partendo dalla image `game-of-chance-api` e collega la porta 3000 del mio computer alla porta 3000 del container.

---

## 8. Comandi utili durante la challenge

### Vedere le image disponibili

```bash
sudo docker images
```

Serve a vedere le image create o scaricate.

---

### Vedere i container attivi

```bash
sudo docker ps
```

Mostra solo i container in esecuzione.

---

### Vedere tutti i container, anche quelli fermi

```bash
sudo docker ps -a
```

---

### Fermare un container

```bash
sudo docker stop CONTAINER_ID
```

Esempio:

```bash
sudo docker stop abc123
```

---

### Rimuovere un container fermo

```bash
sudo docker rm CONTAINER_ID
```

---

### Avviare un container in background

```bash
sudo docker run -d -p 3000:3000 game-of-chance-api
```

`-d` significa **detached mode**, cioè il container gira in background e il terminale resta libero.

---

### Vedere i log di un container

```bash
sudo docker logs CONTAINER_ID
```

---

## 9. Dopo una modifica al codice cosa succede?

Se il Dockerfile copia il codice dentro l’immagine con `COPY`, il container non vede automaticamente le modifiche fatte dopo.

Flusso classico:

```bash
sudo docker stop CONTAINER_ID
sudo docker build -t game-of-chance-api .
sudo docker run -p 3000:3000 game-of-chance-api
```

Quindi:

1. fermo il vecchio container;
2. ricostruisco la image;
3. avvio un nuovo container.

---

## 10. Development con volume

In sviluppo reale si usa spesso un **volume**.

Un volume può collegare la cartella locale del progetto a una cartella dentro il container.

Esempio concettuale:

```bash
sudo docker run -p 3000:3000 -v $(pwd):/app game-of-chance-api
```

Questa parte:

```bash
-v $(pwd):/app
```

significa:

> Collega la cartella attuale del mio computer alla cartella `/app` dentro il container.

Così modifico il codice fuori dal container, ma il container può vedere quei file.

Spesso viene usato insieme a:

```bash
npm run dev
```

oppure:

```bash
nodemon
```

per riavviare automaticamente l’app durante lo sviluppo.

---

## 11. Dockerfile: istruzioni più importanti

Esempio base per una Node.js App:

```Dockerfile
FROM node:20

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

---

### `FROM`

Esempio:

```Dockerfile
FROM node:20
```

Indica l’immagine di partenza.

In questo caso stiamo dicendo:

> Parti da un ambiente dove Node.js 20 è già installato.

Un Dockerfile valido normalmente parte da `FROM`.

---

### `WORKDIR`

Esempio:

```Dockerfile
WORKDIR /app
```

Imposta la cartella di lavoro dentro il container.

Le istruzioni successive verranno eseguite dentro `/app`.

Se la cartella non esiste, Docker la crea.

---

### `COPY`

Esempio:

```Dockerfile
COPY package*.json ./
```

Copia file dalla cartella locale al filesystem dell’immagine.

Qui stiamo copiando `package.json` e, se esiste, `package-lock.json`.

Altro esempio:

```Dockerfile
COPY . .
```

Copia tutto il progetto dentro la cartella di lavoro del container.

---

### `RUN`

Esempio:

```Dockerfile
RUN npm install
```

Esegue un comando durante la costruzione della image.

Serve per installare dipendenze o preparare l’ambiente.

Importante:

`RUN` viene eseguito durante il `docker build`, non quando parte il container.

---

### `EXPOSE`

Esempio:

```Dockerfile
EXPOSE 3000
```

Documenta che l’app dentro il container ascolta sulla porta 3000.

Attenzione: `EXPOSE` da solo non pubblica la porta sul computer.

Per collegare davvero la porta serve ancora:

```bash
-p 3000:3000
```

---

### `CMD`

Esempio:

```Dockerfile
CMD ["npm", "start"]
```

Definisce il comando di default da eseguire quando parte il container.

Importante:

`CMD` non viene eseguito durante il build.

`CMD` viene usato quando facciamo `docker run`.

---

## 12. Differenza importante tra RUN e CMD

Questa è una domanda possibile da challenge.

### `RUN`

Esegue comandi durante la costruzione della image.

Esempio:

```Dockerfile
RUN npm install
```

Serve a preparare l’immagine.

---

### `CMD`

Specifica il comando da eseguire quando il container parte.

Esempio:

```Dockerfile
CMD ["npm", "start"]
```

Serve ad avviare l’app.

---

Frase da ricordare:

> `RUN` prepara l’immagine. `CMD` avvia l’app quando parte il container.

---

## 13. Build context

Quando lanciamo:

```bash
docker build -t game-of-chance-api .
```

il punto `.` indica il build context.

Significa che Docker può usare i file della cartella corrente.

Se lancio il comando dalla cartella sbagliata, Docker potrebbe non trovare il Dockerfile oppure potrebbe usare file sbagliati.

Prima di fare build conviene sempre controllare:

```bash
pwd
ls
```

Dovrei essere nella cartella del progetto e vedere il Dockerfile.

---

## 14. Problema dei permessi Docker su Ubuntu

Errore visto:

```text
permission denied while trying to connect to the docker API at unix:///var/run/docker.sock
```

Significa che Docker è installato, ma l’utente corrente non ha il permesso di comunicare con il Docker daemon.

Soluzione temporanea:

```bash
sudo docker ...
```

Soluzione definitiva:

```bash
sudo usermod -aG docker $USER
```

Poi bisogna fare logout/login o riavviare Ubuntu.

Dopo il riavvio si può testare:

```bash
docker ps
```

Se non appare più l’errore, Docker funziona senza `sudo`.

---

## 15. Workflow completo della lezione

### 1. Clonare la repo

```bash
git clone URL_REPOSITORY
```

---

### 2. Entrare nella cartella

```bash
cd ~/projects/nome-repo
```

---

### 3. Controllare i file

```bash
ls
```

Dobbiamo vedere:

```text
Dockerfile
package.json
src/
```

---

### 4. Costruire la image

```bash
sudo docker build -t game-of-chance-api .
```

---

### 5. Avviare il container

```bash
sudo docker run -p 3000:3000 game-of-chance-api
```

---

### 6. Testare l’API

Dal browser:

```text
http://localhost:3000
```

Oppure dal terminale:

```bash
curl http://localhost:3000
```

---

### 7. Dopo modifiche al codice

```bash
sudo docker ps
sudo docker stop CONTAINER_ID
sudo docker build -t game-of-chance-api .
sudo docker run -p 3000:3000 game-of-chance-api
```

---

## 16. Possibili domande della challenge

### Domanda: Cos’è una Docker image?

Risposta:

> Una Docker image è un pacchetto immutabile che contiene tutto il necessario per creare ed eseguire un container: sistema di base, runtime, dipendenze, codice e comando di avvio.

---

### Domanda: Cos’è un container?

Risposta:

> Un container è una istanza in esecuzione di una image. È l’applicazione accesa dentro un ambiente isolato.

---

### Domanda: A cosa serve il Dockerfile?

Risposta:

> Il Dockerfile contiene le istruzioni che Docker usa per costruire una image.

---

### Domanda: Cosa fa `docker build -t game-of-chance-api .`?

Risposta:

> Costruisce una Docker image usando il Dockerfile della cartella corrente e le assegna il nome `game-of-chance-api`.

---

### Domanda: Cosa significa il punto `.` nel comando `docker build`?

Risposta:

> Il punto indica il build context, cioè la cartella corrente da cui Docker può prendere i file necessari per costruire l’immagine.

---

### Domanda: Cosa fa `docker run -p 3000:3000 game-of-chance-api`?

Risposta:

> Avvia un container dalla image `game-of-chance-api` e collega la porta 3000 del computer alla porta 3000 del container.

---

### Domanda: Il container si aggiorna dopo un commit Git?

Risposta:

> No. Git e Docker sono separati. Il commit salva il codice nella cronologia Git, ma il container attivo continua a usare la versione con cui è stato creato.

---

### Domanda: Quando devo rifare `docker build`?

Risposta:

> Devo rifare `docker build` quando modifico file che vengono copiati nell’immagine e voglio che la nuova image contenga quelle modifiche.

---

### Domanda: Differenza tra `RUN` e `CMD`?

Risposta:

> `RUN` viene eseguito durante la costruzione della image. `CMD` definisce il comando che parte quando viene avviato il container.

---

## 17. Schema mentale finale

```text
Repository Git
    |
    | codice locale
    v
Dockerfile
    |
    | docker build
    v
Docker Image
    |
    | docker run
    v
Docker Container
    |
    | -p 3000:3000
    v
Browser / curl su localhost:3000
```

---

## 18. Frasi chiave da memorizzare

- Docker non sostituisce Git.
- Git gestisce il codice.
- Docker gestisce l’ambiente di esecuzione.
- Una image è il pacchetto pronto.
- Un container è l’app in esecuzione.
- `docker build` crea una image.
- `docker run` avvia un container.
- Il punto `.` indica la cartella corrente come build context.
- `-p 3000:3000` collega la porta del computer alla porta del container.
- Dopo modifiche al codice, spesso bisogna ricostruire la image e riavviare il container.
- In sviluppo reale, spesso si usano i volumi per collegare codice locale e container.
