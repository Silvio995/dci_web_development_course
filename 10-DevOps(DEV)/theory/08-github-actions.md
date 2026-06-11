Cos'è GitHub Actions?

GitHub Actions è uno strumento di CI/CD (Continuous Integration / Continuous Deployment) integrato in GitHub.

Serve per automatizzare operazioni come:

Eseguire test automaticamente
Controllare la qualità del codice
Creare build
Pubblicare applicazioni (deploy)

In pratica, ogni volta che succede un evento su GitHub (push, pull request, ecc.), GitHub può eseguire automaticamente delle operazioni.

CI/CD spiegato semplice
Continuous Integration (CI)

Ogni volta che uno sviluppatore invia codice:

GitHub scarica il progetto
Installa le dipendenze
Esegue i test
Controlla se tutto funziona

Se qualcosa è rotto, lo si scopre subito.

Continuous Deployment (CD)

Se i test vanno bene:

l'app può essere pubblicata automaticamente
oppure aggiornata su un server

senza intervento manuale.

Dove si trovano le GitHub Actions?

GitHub cerca sempre i workflow nella cartella:

.github/workflows

Esempio:

my-project/
│
├── src/
├── package.json
└── .github/
    └── workflows/
        └── test.yml
Cos'è un Workflow?

Un workflow è un file YAML (.yml o .yaml) che descrive:

Quando deve partire
Cosa deve fare

Esempio:

on:
  push:
    branches:
      - main

Significa:

Esegui questo workflow quando qualcuno fa push sul branch main.

Webhook vs GitHub Actions
Webhook

GitHub invia solo una notifica.

Esempio:

"Ehi, qualcuno ha fatto un push!"

Poi devi avere un tuo server che riceve la notifica e decide cosa fare.

Analogia

🔔 Campanello di casa

Il campanello dice solo:

"Qualcuno è alla porta."

Ma poi devi andare tu ad aprire.

GitHub Actions

GitHub esegue direttamente le operazioni.

Analogia

🤖 Robot domestico

Gli dici:

Se qualcuno suona
Apri la porta
Ritira il pacco
Mandami un SMS

Il robot fa tutto da solo.

Il blocco uses

Nei file YAML trovi spesso:

uses: actions/checkout@v4

Significa:

Usa un'azione già pronta.

Formato generale:

uses: utente/repository@versione

Esempio:

uses: actions/checkout@v4
actions = account GitHub ufficiale
checkout = repository dell'azione
v4 = versione

Questa action scarica il codice del repository nel computer virtuale che esegue il workflow.

Marketplace delle Actions

Esistono migliaia di actions già pronte.

Esempi:

Installare Node.js
Eseguire test Jest
Fare deploy su Vercel
Fare deploy su Netlify
Pubblicare immagini Docker

Si trovano nel GitHub Marketplace.

Esempio completo

Workflow che esegue i test quando si fa push o una Pull Request:

name: Run Tests

on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 22

      - run: npm install

      - run: npm test

Cosa fa:

Parte con push o pull request su main
Avvia una macchina Ubuntu
Scarica il repository
Installa Node.js
Installa le dipendenze
Esegue i test
Act: eseguire GitHub Actions in locale

Normalmente le Actions girano sui server GitHub.

Con Act puoi provarle sul tuo PC.

Vantaggi:

Test più veloci
Nessun push necessario
Debug immediato
Installazione di Act

Creare la cartella:

mkdir -p ~/bin

Aggiungere il percorso al PATH:

echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc

Ricaricare:

source ~/.bashrc

Scaricare Act:

curl -L https://github.com/nektos/act/releases/download/v0.2.89/act_Linux_x86_64.tar.gz -o act.tar.gz

Estrarre:

tar -xzf act.tar.gz -C ~/bin act

Eliminare il file scaricato:

rm act.tar.gz

Verificare:

act --version

⚠️ Act richiede Docker installato e avviato.

Comandi principali di Act
Elencare i workflow
act -l

Mostra tutti i job disponibili.

Eseguire un job specifico
act -j test

Esegue il job chiamato test.

Cose da ricordare per l'esame o il lavoro

✅ GitHub Actions è uno strumento CI/CD.

✅ I workflow si trovano sempre in:

.github/workflows

✅ I file workflow sono YAML (.yml).

✅ Il blocco on: definisce quando parte il workflow.

✅ Il blocco jobs: definisce cosa deve fare.

✅ uses: serve per utilizzare actions già esistenti.

✅ actions/checkout@v4 scarica il repository.

✅ actions/setup-node@v4 installa Node.js.

✅ Act permette di eseguire le GitHub Actions localmente usando Docker.