# Hosting & nginx — Guida Completa in Italiano

## Obiettivi della lezione

In questa lezione impariamo:

* cosa è un VPS e come funziona l’hosting
* come installare e configurare nginx
* come servire più applicazioni con un solo web server
* come usare `/etc/hosts`
* come funziona il formato YAML
* come utilizzare Docker Compose
* cosa è SSH e perché la sicurezza dei server è importante

---

# 1. VPS e concetti di Hosting

## Cos’è un VPS?

VPS significa:

```text
Virtual Private Server
```

È uno dei sistemi di hosting più utilizzati.

In un data center esistono grandi server fisici con:

* molte CPU
* molta RAM
* grandi dischi

Queste risorse vengono suddivise in più server virtuali.

Ogni server virtuale è un VPS.

---

## Esempio semplificato

```text
Server fisico
├── VPS 1
├── VPS 2
├── VPS 3
└── VPS 4
```

Ogni VPS funziona come un computer indipendente:

* ha il proprio sistema operativo
* i propri file
* i propri utenti
* i propri processi

---

## Risorse dedicate e condivise

Un VPS può avere:

### Risorse dedicate

Esempio:

* CPU dedicate
* RAM riservata

### Risorse condivise

Esempio:

* CPU condivise con altri VPS

---

## Perché i VPS sono utili?

La maggior parte dei siti web utilizza poche risorse.

Dividere un grande server in più VPS permette:

* risparmio economico
* migliore utilizzo dell’hardware
* maggiore efficienza energetica

Si paga solo ciò che serve.

---

# 2. Cos’è nginx?

## Definizione

nginx (si legge “engine-x”) è un web server.

Un web server:

* riceve richieste HTTP
* restituisce pagine web al browser

---

## Cosa può fare nginx?

nginx può:

* servire file HTML, CSS e immagini
* fare da reverse proxy
* gestire HTTPS
* gestire più applicazioni
* reindirizzare richieste
* distribuire traffico

---

## nginx vs Apache

I due web server storici più famosi sono:

* nginx
* Apache

nginx è noto per:

* alte performance
* basso utilizzo di RAM
* velocità elevata

---

# 3. Installazione di nginx

## Aggiornare i pacchetti

```bash
sudo apt update
```

Aggiorna la lista dei pacchetti disponibili.

---

## Installare nginx

```bash
sudo apt install nginx
```

Installa nginx sul sistema.

---

## Verificare che nginx sia attivo

```bash
sudo systemctl status nginx
```

Se tutto funziona correttamente apparirà:

```text
active (running)
```

---

## Test nel browser

Aprire:

```text
http://localhost
```

Dovrebbe apparire la pagina di benvenuto di nginx.

---

# 4. localhost

`localhost` indica:

```text
questo stesso computer
```

Di solito corrisponde all’indirizzo IP:

```text
127.0.0.1
```

---

# 5. Creare un utente dedicato

## Comando

```bash
sudo useradd -m -s /bin/bash webdev26
```

---

## Spiegazione

### `useradd`

Crea un nuovo utente Linux.

### `-m`

Crea automaticamente la home directory:

```text
/home/webdev26
```

### `-s /bin/bash`

Imposta Bash come shell predefinita.

---

## Perché usare utenti separati?

Nei server reali si evitano operazioni continue come root.

Gli utenti dedicati migliorano:

* sicurezza
* organizzazione
* separazione delle applicazioni

---

# 6. Creare la struttura delle cartelle

## Comandi

```bash
sudo mkdir -p /var/www/app/app1
sudo mkdir -p /var/www/app/app2
```

---

## Struttura finale

```text
/var/www/
└── app/
    ├── app1/
    │   └── index.html
    └── app2/
        └── index.html
```

---

# 7. Permessi Linux

## Cambiare proprietario

```bash
sudo chown -R webdev26:webdev26 /var/www/app
```

### `chown`

Significa:

```text
change owner
```

Cambia proprietario e gruppo.

---

## Modificare permessi

```bash
sudo chmod -R 755 /var/www/app
```

### `chmod`

Significa:

```text
change mode
```

Gestisce i permessi dei file.

---

## Significato di 755

```text
7 = lettura + scrittura + esecuzione
5 = lettura + esecuzione
```

Quindi:

* proprietario → può leggere, scrivere, eseguire
* gruppo → può leggere ed eseguire
* altri → possono leggere ed eseguire

È il setup tipico dei contenuti web.

---

# 8. Creare i file HTML

## app1/index.html

```html
<h1>App 1</h1>
<p>Hello from app1</p>
```

## app2/index.html

```html
<h1>App 2</h1>
<p>Hello from app2</p>
```

---

# 9. Configurazione nginx

## Creare un file di configurazione

```bash
sudo nano /etc/nginx/sites-available/webdev26
```

---

## Configurazione

```nginx
server {
   listen 80;
   server_name localhost;

   location /app1 {
      alias /var/www/app/app1;
      index index.html;
      try_files $uri $uri/ =404;
   }

   location /app2 {
      alias /var/www/app/app2;
      index index.html;
      try_files $uri $uri/ =404;
   }

   location / {
      alias /var/www/html;
      index index.html;
      try_files $uri $uri/ =404;
   }
}
```

---

# 10. Spiegazione della configurazione nginx

## `server`

Definisce un server virtuale.

---

## `listen 80`

Ascolta sulla porta HTTP standard:

```text
80
```

---

## `server_name`

Definisce i domini gestiti:

```nginx
server_name localhost;
```

---

## `location`

Gestisce specifici percorsi URL.

Esempio:

```nginx
location /app1
```

gestisce:

```text
http://localhost/app1
```

---

## `alias`

Indica la cartella reale nel filesystem.

Esempio:

```nginx
alias /var/www/app/app1;
```

---

## `index`

Definisce il file iniziale:

```nginx
index index.html;
```

---

## `try_files`

Controlla se il file esiste.

Se non esiste:

```text
404
```

---

# 11. Attivare la configurazione

## Creare il symlink

```bash
sudo ln -s /etc/nginx/sites-available/webdev26 /etc/nginx/sites-enabled
```

Un symlink è simile a un collegamento di Windows.

---

## Disattivare la configurazione di default

```bash
sudo rm /etc/nginx/sites-enabled/default
```

---

## Testare nginx

```bash
sudo nginx -t
```

Verifica che la configurazione sia corretta.

---

## Ricaricare nginx

```bash
sudo systemctl reload nginx
```

Ricarica la configurazione senza riavviare il server.

---

# 12. /etc/hosts

## Cos’è?

Prima di cercare un dominio su internet, Linux controlla:

```text
/etc/hosts
```

Questa cartella permette di creare DNS locali.

---

## Aggiungere un dominio locale

Aprire:

```bash
sudo nano /etc/hosts
```

Aggiungere:

```text
127.0.0.1 mycoolwebsite26.com
```

---

## Risultato

Ora:

```text
mycoolwebsite26.com
```

punterà al computer locale.

---

# 13. Configurare nginx per il dominio

Modificare:

```nginx
server_name localhost;
```

in:

```nginx
server_name localhost mycoolwebsite26.com;
```

---

## Applicare le modifiche

```bash
sudo nginx -t && sudo systemctl reload nginx
```

---

# 14. SSH

## Cos’è SSH?

SSH permette di collegarsi a server remoti in modo sicuro.

---

## Esempio

```bash
ssh user@server-ip
```

---

## A cosa serve?

Con SSH possiamo:

* amministrare server
* modificare file
* fare deploy
* installare software
* monitorare sistemi

---

# 15. Sicurezza dei server

Un server online deve essere protetto.

Misure importanti:

* password forti
* aggiornamenti
* firewall
* utenti separati
* chiavi SSH

I server pubblici ricevono continuamente tentativi automatici di accesso.

---

# 16. YAML

## Cos’è YAML?

YAML è un formato per file di configurazione.

Usato in:

* Docker Compose
* Kubernetes
* GitHub Actions

---

## Caratteristiche

YAML usa:

* indentazione
* gerarchie
* chiavi e valori

NON usa:

* parentesi
* XML
* JSON

---

## Regole principali

* usare spazi, mai tab
* normalmente 2 spazi per livello

---

## Esempio

```yml
services:
  web:
    image: xyz
```

---

# 17. Docker Compose

## Cos’è?

Docker Compose permette di gestire più container insieme.

---

## Installazione

```bash
sudo apt install docker-compose-plugin
```

---

## Avviare i container

```bash
docker compose up -d
```

---

## Fermare i container

```bash
docker compose down
```

---

# 18. nginx dentro Docker

## Struttura del progetto

```text
nginx-docker/
├── docker-compose.yml
├── nginx.conf
└── www/
    ├── app1/
    │   └── index.html
    └── app2/
        └── index.html
```

---

## Avvio

Dentro la cartella:

```bash
docker compose up -d
```

Docker:

* crea il container nginx
* monta i file locali
* avvia il web server

---

# 19. Pulizia del sistema

## Fermare e disinstallare nginx

```bash
sudo systemctl stop nginx
sudo apt purge nginx nginx-common
sudo apt autoremove
```

---

## Eliminare file e configurazioni

```bash
sudo rm -rf /var/www/app
sudo rm -rf /etc/nginx
```

---

## Eliminare l’utente

```bash
sudo userdel -r webdev26
```

---

## Pulire /etc/hosts

Aprire:

```bash
sudo nano /etc/hosts
```

Rimuovere manualmente la riga aggiunta.

---

# 20. Concetti fondamentali imparati

Questa lezione introduce concetti fondamentali del backend e DevOps:

* hosting
* VPS
* web server
* nginx
* filesystem Linux
* permessi
* networking locale
* DNS locale
* Docker
* YAML
* sicurezza server

Sono competenze essenziali nel lavoro reale di sviluppo backend e infrastruttura.
