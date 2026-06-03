# Git/GitHub Workflow e Webhooks

## Obiettivi della lezione

In questa lezione abbiamo visto:

* come funziona un workflow Git/GitHub in un team
* la differenza tra branch `main`, `dev` e feature branch
* il ruolo del developer e del project manager/senior developer
* come funziona un Pull Request
* cosa succede quando ci sono merge conflict
* come avviene il passaggio da `dev` a `main`
* cosa sono i webhooks
* differenza tra polling e webhooks
* come usare GitHub Webhooks per automatizzare processi
* come testare webhooks in locale con Smee

---

# Git Workflow: panoramica teorica

Quando si lavora in team, non si dovrebbe lavorare tutti direttamente sul branch principale.

Per evitare confusione, errori e conflitti, si usa una struttura organizzata di branch.

---

# Struttura dei branch

In un tipico team ci sono due branch permanenti:

```txt
main
dev
```

---

## Branch `main`

Il branch `main` rappresenta lo stato di produzione.

Questo significa:

```txt
Quello che si trova in main può andare live.
```

Il codice su `main` dovrebbe essere stabile, testato e pronto per il deploy.

---

## Branch `dev`

Il branch `dev` rappresenta lo stato attuale dello sviluppo.

Qui vengono raccolte le nuove feature prima di arrivare in produzione.

In pratica:

```txt
dev = ambiente dove il lavoro del team viene integrato
main = ambiente stabile per la produzione
```

---

# Feature branch

I feature branch sono branch temporanei.

Vengono creati per lavorare su una singola task o feature.

Esempi:

```txt
feature/add-footer
feature/fix-navbar
```

Questi branch nascono da `dev` e, quando il lavoro è finito, tornano dentro `dev`.

Regola importante:

```txt
I feature branch nascono sempre da dev, non da main.
```

---

# Esempio di struttura

```txt
main
│
└── dev
    │
    ├── feature/add-footer
    ├── feature/fix-navbar
    └── feature/contact-form
```

---

# Ruoli nel workflow

## Developer

Il developer riceve una task o una issue.

Poi:

* crea un feature branch
* implementa la modifica
* fa commit
* fa push
* apre un Pull Request

---

## Project Manager / Senior Developer

Il Project Manager o Senior Developer si occupa di controllare il lavoro.

Di solito:

* fa code review
* controlla i Pull Request
* decide se fare merge
* lascia feedback se servono modifiche
* gestisce il passaggio da `dev` a `main`

---

# Il workflow completo

## 1. Situazione iniziale

Abbiamo due branch:

```txt
main
dev
```

All’inizio possono essere sullo stesso stato.

Poi si deve implementare una nuova feature.

---

# 2. Il developer crea un feature branch

Prima il developer va su `dev`:

```bash
git checkout dev
```

Poi aggiorna `dev` con l’ultima versione remota:

```bash
git pull origin dev
```

Poi crea il feature branch:

```bash
git checkout -b feature/add-footer
```

Questo crea un nuovo branch partendo da `dev`.

---

# 3. Il developer lavora, committa e pusha

Dopo aver scritto il codice:

```bash
git add -A
```

Poi crea il commit:

```bash
git commit -m "feat: add footer component"
```

Poi manda il branch su GitHub:

```bash
git push -u origin feature/add-footer
```

---

# 4. Il developer apre un Pull Request

Il Pull Request viene aperto da:

```txt
feature/add-footer → dev
```

Nel Pull Request si inseriscono:

* titolo
* descrizione
* riferimento alla issue
* reviewer

Esempio descrizione:

```txt
Closes #5
```

Questo significa che, quando il Pull Request viene mergiato, GitHub può chiudere automaticamente la issue numero 5.

---

# 5. Code Review

Il Project Manager o Senior Developer apre il Pull Request e controlla la sezione:

```txt
Files changed
```

Qui può vedere quali file sono stati modificati.

---

# Possibilità A: tutto va bene

Se il codice è corretto:

* il Pull Request viene approvato
* il branch viene mergiato dentro `dev`
* il feature branch può essere eliminato
* la issue collegata viene chiusa automaticamente

---

# Possibilità B: servono modifiche

Se il codice non va bene:

* il reviewer lascia un commento
* il developer modifica il codice
* il developer fa un nuovo commit sullo stesso branch
* il Pull Request si aggiorna automaticamente

Esempio:

```bash
git add -A
git commit -m "fix: update footer spacing"
git push
```

Non serve aprire un nuovo Pull Request.

Il PR già esistente viene aggiornato.

---

# Dopo il merge

Dopo il merge:

```txt
dev contiene la nuova feature.
main non ancora.
```

Questo significa che il lavoro è stato integrato nello sviluppo, ma non è ancora arrivato in produzione.

---

# Lavoro parallelo di più developer

In un team, più developer possono lavorare contemporaneamente.

Per esempio:

```txt
Developer A → feature/add-footer
Developer B → feature/fix-navbar
Developer C → feature/contact-form
```

Questo permette a più persone di lavorare in parallelo senza modificare direttamente `dev` o `main`.

---

# Merge conflict

Un merge conflict può succedere quando due developer modificano la stessa parte dello stesso file.

Git non può sapere automaticamente quale versione è quella corretta.

Per questo chiede al developer di risolvere il conflitto manualmente.

---

# Come risolvere un merge conflict

Prima si aggiorna `dev`:

```bash
git checkout dev
git pull origin dev
```

Poi si torna sul proprio feature branch:

```bash
git checkout feature/fix-navbar
```

Poi si fa il merge di `dev` dentro il feature branch:

```bash
git merge dev
```

Se ci sono conflitti, si risolvono in VSCode.

Poi:

```bash
git add -A
git commit -m "fix: resolve merge conflict"
git push
```

---

# Release: da `dev` a `main`

Quando abbastanza feature sono state raccolte dentro `dev` e tutto è stato testato, si può preparare una release.

A questo punto il Project Manager apre un Pull Request:

```txt
dev → main
```

Questo è il momento del deploy.

Regola:

```txt
Quello che entra in main viene deployato.
```

---

# Schema completo del workflow

```txt
main
 ↑
 │ Pull Request di release
 │
dev
 ↑
 │ Pull Request delle feature
 │
feature/add-footer
```

---

# Webhooks

## Teoria: Polling vs Webhooks

Quando un sistema deve sapere se è successo qualcosa, ci sono due approcci principali:

* polling
* webhooks

---

# Polling

Il polling significa controllare attivamente a intervalli regolari se ci sono aggiornamenti.

Esempio:

```txt
Client: "C'è qualcosa di nuovo?"
Server: "No."

Client: "C'è qualcosa di nuovo?"
Server: "No."

Client: "C'è qualcosa di nuovo?"
Server: "Sì, ecco i dati."
```

Il problema è che il polling può essere lento e inefficiente.

Il client continua a chiedere anche quando non è successo nulla.

---

# Webhooks

I webhooks funzionano in modo diverso.

Il server resta in attesa.

Quando succede un evento, un altro servizio invia automaticamente una richiesta HTTP.

Esempio:

```txt
Server aspetta...

GitHub: "Hey, qualcuno ha fatto push! Ecco i dettagli."

Server: "Grazie, ora eseguo l’azione."
```

Questo è un approccio event-driven, cioè guidato dagli eventi.

È simile a un event listener nel frontend.

---

# Analogia semplice

Polling:

```txt
È come controllare la cassetta della posta ogni 5 minuti.
```

Webhook:

```txt
È come quando il postino suona il campanello.
```

---

# Webhooks su GitHub

Con GitHub Webhooks, possiamo dire a GitHub di inviare una richiesta HTTP quando succede qualcosa in una repository.

Per esempio:

* qualcuno fa push
* viene creato un Pull Request
* viene aperta una issue
* viene chiusa una issue
* viene fatto un merge

---

# Esempi pratici di Webhooks

## Push su repository

Quando qualcuno fa push nel repository:

```txt
GitHub invia una richiesta al tuo server.
Il server fa pull del repo.
Il server esegue il deploy.
```

---

## Nuovo Pull Request

Quando qualcuno apre un Pull Request:

```txt
GitHub avvisa il tuo server.
Il server aggiorna una dashboard interna.
```

---

## Nuova issue

Quando viene creata una issue:

```txt
GitHub avvisa il tuo server.
Il server può inviare un messaggio su Slack.
```

---

# Webhooks e Slack

I webhooks possono essere collegati anche a Slack.

Un team può avere canali separati per:

* notifiche di push
* nuove issue
* nuovi Pull Request
* deploy
* errori

In questo modo il team resta aggiornato senza dover controllare continuamente GitHub.

---

# Webhooks non solo su GitHub

I webhooks non sono solo una funzione di GitHub.

Molti servizi moderni offrono webhooks.

Esempi:

* Stripe
* Slack
* Netlify
* Vercel
* Shopify
* GitHub

Il concetto è sempre simile:

```txt
Quando succede un evento, il servizio invia una richiesta HTTP a un server configurato.
```

---

# Webhook Deployment

Un deployment basato su webhook potrebbe funzionare così:

```txt
1. Tu fai push sul branch main
2. GitHub invia una richiesta HTTP al tuo server
3. Il server valida la richiesta
4. Il server fa pull del repository
5. Il server esegue il deploy
```

---

# Sicurezza nei Webhooks

Un webhook deve essere validato.

Non possiamo fidarci di qualsiasi richiesta HTTP ricevuta.

Il server deve controllare che la richiesta arrivi davvero da GitHub.

Per questo si usa un secret.

Esempio:

```env
WEBHOOK_SECRET=gemeimes-passwort
```

Il secret viene usato per verificare la firma del webhook.

---

# Sviluppo locale di Webhooks

Per ricevere webhooks serve un server pubblicamente raggiungibile.

Il problema è che durante lo sviluppo il nostro server gira spesso in locale, per esempio:

```txt
http://localhost:3000
```

GitHub non può raggiungere direttamente `localhost`, perché è sul nostro computer.

Inoltre, aprire il proprio PC a Internet non è una buona idea per motivi di sicurezza.

---

# Smee

Per testare webhooks in locale possiamo usare:

```txt
https://smee.io
```

Smee crea una URL pubblica.

Quando GitHub manda una richiesta a quella URL, Smee la inoltra al nostro server locale.

Schema:

```txt
GitHub
  ↓
URL pubblica Smee
  ↓
localhost:3000
```

Questo permette di testare webhooks senza esporre direttamente il proprio PC.

---

# Setup demo locale Webhook

## 1. Creare cartella progetto

```bash
mkdir webhook-demo
cd webhook-demo
```

---

## 2. Creare repository GitHub

Nel proprio account GitHub, creare una nuova repository.

---

## 3. Inizializzare progetto Bun

```bash
bun init -y
```

---

## 4. Creare file iniziali

```bash
touch server.ts .env .env.sample
```

---

## 5. Installare pacchetti

```bash
bun add express cors @octokit/webhooks
```

---

## 6. Installare i tipi

```bash
bun add @types/express @types/node -D
```

---

## 7. Aggiungere secret in `.env`

```env
WEBHOOK_SECRET=gemeimes-passwort
```

---

## 8. Installare Smee

```bash
bun add -g smee-client
```

---

## 9. Generare Smee URL

Aprire:

```txt
https://smee.io
```

Cliccare:

```txt
Start a new channel
```

Copiare la URL generata.

---

# Configurare Webhook su GitHub

Nel repository GitHub:

```txt
Settings → Webhooks → Add webhook
```

Poi inserire:

* Payload URL: la URL di Smee
* Content type: `application/json`
* Secret: lo stesso valore di `WEBHOOK_SECRET`
* Events: scegliere gli eventi da ascoltare, per esempio `push`

---

# Avviare Smee

Nel terminale:

```bash
smee -u https://smee.io/2lN8scHMjGuitbs -t http://localhost:3000/api/github/webhooks
```

Spiegazione:

| Parte                                       | Significato                               |
| ------------------------------------------- | ----------------------------------------- |
| `smee`                                      | Avvia il client Smee                      |
| `-u`                                        | URL pubblica generata da Smee             |
| `-t`                                        | Target locale dove inoltrare le richieste |
| `http://localhost:3000/api/github/webhooks` | Endpoint locale del nostro server         |

---

# Endpoint locale webhook

Il server locale deve avere un endpoint che riceve le richieste di GitHub.

Esempio:

```txt
POST /api/github/webhooks
```

GitHub invia gli eventi a questo endpoint tramite Smee.

---

# Documentazione utile

Documentazione GitHub per validare webhook deliveries:

```txt
https://docs.github.com/en/webhooks/using-webhooks/validating-webhook-deliveries#typescript-example
```

Documentazione Octokit Webhooks:

```txt
https://github.com/octokit/webhooks.js
```

---

# Riassunto finale

In un team si usano branch organizzati per mantenere il lavoro pulito.

`main` rappresenta la produzione.

`dev` rappresenta lo sviluppo attivo.

I feature branch sono temporanei e servono per lavorare su singole task.

Il developer lavora sul feature branch, poi apre un Pull Request verso `dev`.

Il Project Manager o Senior Developer fa code review.

Se tutto va bene, il PR viene mergiato.

Quando `dev` è stabile e testato, si apre un Pull Request da `dev` a `main`.

Questo è il momento della release/deploy.

I webhooks permettono a servizi come GitHub di notificare automaticamente un server quando succede un evento.

A differenza del polling, i webhooks non controllano continuamente se ci sono aggiornamenti: reagiscono quando succede qualcosa.

Per testare webhooks in locale si può usare Smee, che crea una URL pubblica e inoltra le richieste al server locale.

---

# Mini schema da ricordare

```txt
main = produzione
```

```txt
dev = sviluppo attivo
```

```txt
feature branch = branch temporaneo per una task
```

```txt
Pull Request = richiesta di integrare codice
```

```txt
Polling = controllo ripetuto
```

```txt
Webhook = notifica automatica quando succede un evento
```

```txt
Smee = ponte tra GitHub e localhost
```

---

# Comandi principali

| Comando                                      | Significato                                |
| -------------------------------------------- | ------------------------------------------ |
| `git checkout dev`                           | Passa al branch dev                        |
| `git pull origin dev`                        | Aggiorna dev dal remoto                    |
| `git checkout -b feature/add-footer`         | Crea un feature branch                     |
| `git add -A`                                 | Aggiunge tutte le modifiche                |
| `git commit -m "feat: add footer component"` | Crea un commit                             |
| `git push -u origin feature/add-footer`      | Pusha il branch remoto                     |
| `git merge dev`                              | Integra dev nel branch corrente            |
| `bun init -y`                                | Inizializza progetto Bun                   |
| `touch server.ts .env .env.sample`           | Crea file iniziali                         |
| `bun add express cors @octokit/webhooks`     | Installa dipendenze                        |
| `bun add @types/express @types/node -D`      | Installa tipi TypeScript                   |
| `bun add -g smee-client`                     | Installa Smee globalmente                  |
| `smee -u URL -t TARGET`                      | Inoltra richieste da Smee al server locale |

---

# Parole chiave

| Termine        | Significato                                     |
| -------------- | ----------------------------------------------- |
| `main`         | Branch di produzione                            |
| `dev`          | Branch di sviluppo                              |
| Feature branch | Branch temporaneo per una task                  |
| Pull Request   | Richiesta di revisione e merge                  |
| Code review    | Controllo del codice prima del merge            |
| Merge conflict | Conflitto tra modifiche incompatibili           |
| Release        | Passaggio del codice stabile verso produzione   |
| Deploy         | Pubblicazione dell’applicazione                 |
| Polling        | Controllo periodico degli aggiornamenti         |
| Webhook        | Richiesta HTTP inviata quando succede un evento |
| Event-driven   | Approccio basato sugli eventi                   |
| Smee           | Tool per testare webhooks in locale             |
| Secret         | Valore usato per validare la richiesta webhook  |
| Octokit        | Libreria ufficiale GitHub per API/webhooks      |
