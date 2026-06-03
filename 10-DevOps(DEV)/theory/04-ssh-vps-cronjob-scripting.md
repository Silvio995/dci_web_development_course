# SSH, VPS, Git/GitHub Workflow, Cronjob e Scripting

## Lernziele / Obiettivi della lezione

In questa lezione vediamo:

* come collegarsi a un VPS tramite SSH
* il workflow base con Git e GitHub
* cosa sono cronjob e scripting
* come creare uno script Bash
* come eseguire automaticamente uno script con cron

---

# SSH

## Cos’è SSH?

SSH significa:

```txt
Secure Shell
```

SSH è il protocollo standard per collegarsi in modo sicuro a un server remoto.

Con SSH possiamo aprire una shell su un’altra macchina, per esempio un VPS.

Esempio:

```txt
Il mio computer → SSH → Server remoto
```

Una volta connessi, possiamo usare il terminale del server come se fossimo fisicamente su quella macchina.

---

## Caratteristiche principali di SSH

SSH è importante perché:

* la comunicazione è cifrata
* permette di accedere a una shell remota
* viene usato per amministrare server Linux
* può autenticare tramite password
* può autenticare tramite SSH key

Il metodo consigliato è l’autenticazione tramite **SSH key**, perché è più sicuro della password.

---

# VPS

## Cos’è un VPS?

VPS significa:

```txt
Virtual Private Server
```

Un VPS è un server virtuale online.

È come avere un computer Linux remoto sempre acceso, accessibile via Internet.

Su un VPS possiamo:

* installare applicazioni
* avviare server Node.js
* usare Docker
* configurare Nginx
* fare deploy di progetti
* eseguire script automatici
* usare cronjob

---

# SSH Key

## Vedere le SSH key esistenti

Per controllare se abbiamo già delle SSH key sul nostro computer:

```bash
ls -al ~/.ssh
```

La cartella `~/.ssh` contiene normalmente chiavi SSH e file di configurazione.

---

# Creare una SSH key con nome personalizzato

Possiamo creare una nuova SSH key con questo comando:

```bash
ssh-keygen -t ed25519 -C "deine@email.com" -f ~/.ssh/webdev26_key
```

Spiegazione:

| Parte                    | Significato                           |
| ------------------------ | ------------------------------------- |
| `ssh-keygen`             | Comando per creare una chiave SSH     |
| `-t ed25519`             | Tipo di chiave moderna e sicura       |
| `-C "deine@email.com"`   | Commento/etichetta della chiave       |
| `-f ~/.ssh/webdev26_key` | Nome e percorso del file della chiave |

---

## File generati

Il comando genera due file:

```txt
~/.ssh/webdev26_key
~/.ssh/webdev26_key.pub
```

---

## Private key

```txt
~/.ssh/webdev26_key
```

Questa è la chiave privata.

Regola fondamentale:

```txt
La private key non deve mai essere condivisa.
```

La chiave privata rimane sul nostro computer.

---

## Public key

```txt
~/.ssh/webdev26_key.pub
```

Questa è la chiave pubblica.

La public key può essere copiata sul server.

Il server usa la public key per riconoscere il nostro computer.

---

# Copiare la public key

Per vedere e copiare la public key:

```bash
cat ~/.ssh/webdev26_key.pub
```

Il contenuto generato può essere inserito nel server dentro il file:

```txt
authorized_keys
```

---

# Simulare un VPS con Docker

Nel corso usiamo un container Docker per simulare un VPS.

Il container si comporta come un server remoto a cui possiamo collegarci via SSH.

Per avviare questo ambiente usiamo una file:

```txt
docker-compose.yml
```

---

# Installare Docker Compose Plugin

Prima di usare `docker compose`, dobbiamo assicurarci che il plugin sia installato.

Comando:

```bash
sudo apt install docker-compose-plugin
```

---

# Avviare il container

Per avviare il container, dobbiamo trovarci nella cartella dove si trova il file:

```txt
docker-compose.yml
```

Poi eseguiamo:

```bash
docker compose up -d
```

Spiegazione:

| Comando          | Significato                           |
| ---------------- | ------------------------------------- |
| `docker compose` | Usa Docker Compose                    |
| `up`             | Avvia i servizi                       |
| `-d`             | Modalità detached, cioè in background |

---

# Entrare nel container

Dopo aver avviato il container, possiamo entrare dentro il container chiamato `ubuntu-ssh`.

Comando:

```bash
docker exec -it ubuntu-ssh bash
```

Se `bash` non è disponibile, possiamo usare:

```bash
docker exec -it ubuntu-ssh sh
```

Spiegazione:

| Parte         | Significato                           |
| ------------- | ------------------------------------- |
| `docker exec` | Esegue un comando dentro un container |
| `-it`         | Modalità interattiva                  |
| `ubuntu-ssh`  | Nome del container                    |
| `bash`        | Shell da aprire                       |

---

# Inserire la public key nel container

Dentro il container dobbiamo creare la cartella `.ssh` per l’utente root.

```bash
mkdir -p /root/.ssh
chmod 700 /root/.ssh
```

Poi inseriamo la public key nel file `authorized_keys`:

```bash
echo "hier kommt der public key rein" > /root/.ssh/authorized_keys
```

Poi impostiamo i permessi corretti:

```bash
chmod 600 /root/.ssh/authorized_keys
```

---

## Spiegazione dei permessi

```bash
chmod 700 /root/.ssh
```

Significa che solo il proprietario può leggere, scrivere ed entrare nella cartella `.ssh`.

```bash
chmod 600 /root/.ssh/authorized_keys
```

Significa che solo il proprietario può leggere e scrivere il file `authorized_keys`.

SSH richiede permessi sicuri, altrimenti può rifiutare la connessione.

---

# Collegarsi via SSH con una key personalizzata

Per collegarci usando la key creata prima:

```bash
ssh -i ~/.ssh/webdev26_key root@localhost -p 2222
```

Spiegazione:

| Parte                    | Significato            |
| ------------------------ | ---------------------- |
| `ssh`                    | Avvia connessione SSH  |
| `-i ~/.ssh/webdev26_key` | Usa questa private key |
| `root@localhost`         | Utente e host          |
| `-p 2222`                | Porta SSH              |

---

## Perché usiamo `-i`?

Usiamo `-i` perché abbiamo creato una chiave con un nome personalizzato.

Quindi dobbiamo dire esplicitamente a SSH quale key usare.

---

# Login senza password

Se la public key è stata configurata correttamente nel server/container, non serve inserire la password.

Il server riconosce il client tramite la SSH key.

Schema:

```txt
Client ha private key
Server ha public key
      ↓
SSH verifica la corrispondenza
      ↓
Accesso consentito
```

---

# Provare login con password

Possiamo anche provare a forzare il login senza public key.

Comando:

```bash
ssh -o PubkeyAuthentication=no root@localhost -p 2222
```

Spiegazione:

```txt
PubkeyAuthentication=no
```

disattiva temporaneamente l’autenticazione tramite chiave pubblica.

In questo modo proviamo il login tramite password.

---

# Fermare Docker Compose

Quando abbiamo finito, possiamo fermare il container.

Dobbiamo essere nella stessa cartella della file `docker-compose.yml`.

Comando:

```bash
docker compose down
```

Questo ferma e rimuove i container creati da Docker Compose.

---

# Pulizia delle SSH key

Se la key è stata creata solo per esercizio e non serve più, possiamo eliminarla.

```bash
rm ~/.ssh/webdev26_key ~/.ssh/webdev26_key.pub
```

Regola importante:

```txt
Rimuovere le private key dal sistema quando non servono più.
```

---

# Rimuovere una voce da known_hosts

Quando ci colleghiamo a un server via SSH, il server viene registrato nel file `known_hosts`.

Se vogliamo rimuovere la voce relativa a `localhost:2222`, usiamo:

```bash
ssh-keygen -R "[localhost]:2222"
```

Questo è utile quando il server/container viene ricreato e SSH segnala un possibile cambio di fingerprint.

---

# Git/GitHub Workflow

## Perché Git è importante su un VPS?

Quando lavoriamo su server o VPS, spesso usiamo Git per scaricare o aggiornare il codice del progetto.

Workflow tipico:

```txt
Sviluppo in locale
      ↓
Commit
      ↓
Push su GitHub
      ↓
Server/VPS fa pull da GitHub
      ↓
App aggiornata sul server
```

---

## Comandi Git utili

Controllare lo stato:

```bash
git status
```

Aggiungere file:

```bash
git add .
```

Creare un commit:

```bash
git commit -m "Message"
```

Inviare su GitHub:

```bash
git push
```

Scaricare aggiornamenti da GitHub:

```bash
git pull
```

---

# Cronjob

## Cos’è un cronjob?

Un cronjob è un sistema per eseguire comandi o script automaticamente in base a un orario.

Serve per automatizzare attività ripetitive.

Esempi:

* eseguire backup ogni notte
* pulire file temporanei ogni giorno
* inviare report ogni settimana
* controllare servizi ogni minuto
* eseguire script di manutenzione

---

# Scripting

## Cos’è uno shell script?

Uno shell script è una file di testo che contiene comandi da eseguire.

La shell, di solito Bash, legge il file ed esegue i comandi uno dopo l’altro.

Esempio:

```txt
Comando 1
Comando 2
Comando 3
```

---

## Bash scripting

La “lingua” usata spesso negli script Linux è Bash scripting.

Bash scripting è una combinazione di:

* normali comandi Linux
* variabili
* condizioni
* cicli
* funzioni
* piccole logiche di programma

---

# Interpreter

Uno script viene eseguito da un interprete.

Su Ubuntu l’interprete più comune è Bash.

Per dire al sistema che uno script deve essere eseguito con Bash, si usa una riga speciale all’inizio del file.

---

# Shebang

La prima riga di una file `.sh` spesso è:

```bash
#!/bin/bash
```

Questa riga si chiama:

```txt
Shebang
```

Lo shebang dice al sistema:

```txt
Esegui questo script usando Bash.
```

---

# Demo pratica: creare uno script per cron

## 1. Creare la cartella

Creiamo una cartella:

```bash
sudo mkdir -p /home/ubuntu
```

Spiegazione:

| Parte          | Significato                             |
| -------------- | --------------------------------------- |
| `sudo`         | Esegue con privilegi amministrativi     |
| `mkdir`        | Crea una cartella                       |
| `-p`           | Non dà errore se la cartella esiste già |
| `/home/ubuntu` | Percorso della cartella                 |

---

## 2. Creare il file dello script

Creiamo il file:

```bash
sudo touch /home/ubuntu/cron-test.sh
```

---

## 3. Modificare il file con nano

Apriamo il file:

```bash
sudo nano /home/ubuntu/cron-test.sh
```

Inseriamo questo contenuto:

```bash
#!/bin/bash
echo "Cronjob lief um $(date)" >> /home/ubuntu/cron-log.txt
```

---

## Spiegazione dello script

```bash
#!/bin/bash
```

Dice che lo script deve essere eseguito con Bash.

```bash
echo "Cronjob lief um $(date)" >> /home/ubuntu/cron-log.txt
```

Scrive una riga dentro il file `cron-log.txt`.

`$(date)` viene sostituito con la data e ora attuale.

Il simbolo:

```bash
>>
```

aggiunge testo alla fine del file senza cancellare il contenuto precedente.

---

# Rendere lo script eseguibile

Dopo aver creato lo script, dobbiamo renderlo eseguibile.

```bash
sudo chmod +x /home/ubuntu/cron-test.sh
```

Spiegazione:

```txt
chmod +x
```

aggiunge il permesso di esecuzione.

---

# Configurare un cronjob

Per aprire la configurazione dei cronjob dell’utente corrente:

```bash
crontab -e
```

Poi inseriamo questa riga:

```bash
* * * * * /home/ubuntu/cron-test.sh
```

Questa riga significa:

```txt
Esegui lo script ogni minuto.
```

---

# Sintassi di cron

La sintassi di cron è composta da cinque campi temporali.

```txt
* * * * * comando
│ │ │ │ │
│ │ │ │ └── giorno della settimana
│ │ │ └──── mese
│ │ └────── giorno del mese
│ └──────── ora
└────────── minuto
```

---

## Significato dei campi

| Campo                  | Valori | Significato                            |
| ---------------------- | ------ | -------------------------------------- |
| Minuto                 | `0-59` | Minuto dell’ora                        |
| Ora                    | `0-23` | Ora del giorno                         |
| Giorno del mese        | `1-31` | Giorno del mese                        |
| Mese                   | `1-12` | Mese                                   |
| Giorno della settimana | `0-7`  | Giorno della settimana, domenica 0 o 7 |

---

# Esempi di cron

## Ogni minuto

```bash
* * * * * /home/ubuntu/cron-test.sh
```

---

## Ogni 5 minuti

```bash
*/5 * * * * /home/ubuntu/cron-test.sh
```

---

## Ogni giorno alle 03:00

```bash
0 3 * * * /home/ubuntu/cron-test.sh
```

---

## Ogni lunedì alle 09:30

```bash
30 9 * * 1 /home/ubuntu/cron-test.sh
```

---

# Controllare se il cronjob funziona

Dopo qualche minuto, possiamo controllare il file di log:

```bash
cat /home/ubuntu/cron-log.txt
```

Se il cronjob funziona, dovremmo vedere righe simili:

```txt
Cronjob lief um Mon Jun 1 10:20:01 UTC 2026
Cronjob lief um Mon Jun 1 10:21:01 UTC 2026
Cronjob lief um Mon Jun 1 10:22:01 UTC 2026
```

---

# Pulizia finale

Quando non ci serve più il cronjob, dobbiamo rimuoverlo.

Apriamo di nuovo crontab:

```bash
crontab -e
```

Poi cancelliamo la riga:

```bash
* * * * * /home/ubuntu/cron-test.sh
```

Salviamo e usciamo.

---

# Nota importante

Nel testo della lezione compare:

```bash
cronjob -e
```

ma il comando corretto normalmente è:

```bash
crontab -e
```

Con `sudo`, invece, modifichiamo i cronjob dell’utente root:

```bash
sudo crontab -e
```

Quindi:

| Comando           | Effetto                                 |
| ----------------- | --------------------------------------- |
| `crontab -e`      | Modifica i cronjob dell’utente corrente |
| `sudo crontab -e` | Modifica i cronjob di root              |

---

# Riassunto finale

SSH serve per collegarsi in modo sicuro a un server remoto.

Con SSH otteniamo una shell sul server e possiamo amministrarlo da terminale.

L’autenticazione tramite SSH key è più sicura e consigliata rispetto alla password.

Una SSH key è composta da:

* private key, da tenere segreta
* public key, da copiare sul server

Nel corso usiamo un container Docker per simulare un VPS.

Docker Compose permette di avviare il container con:

```bash
docker compose up -d
```

e fermarlo con:

```bash
docker compose down
```

I cronjob servono per eseguire script automaticamente in base a un orario.

Uno shell script è una file con comandi Bash.

Lo shebang:

```bash
#!/bin/bash
```

dice al sistema di eseguire lo script con Bash.

Con:

```bash
crontab -e
```

possiamo configurare cronjob automatici.

---

# Mini schema da ricordare

```txt
SSH = connessione sicura a un server remoto
```

```txt
Private key = resta sul tuo computer, non si condivide mai
```

```txt
Public key = si copia sul server
```

```txt
VPS = server virtuale online
```

```txt
Cronjob = comando/script eseguito automaticamente a orari programmati
```

```txt
Shell script = file con comandi Bash
```

```txt
Shebang = indica quale interprete usare
```

---

# Comandi principali

| Comando                                                   | Significato                           |
| --------------------------------------------------------- | ------------------------------------- |
| `ls -al ~/.ssh`                                           | Mostra le SSH key esistenti           |
| `ssh-keygen -t ed25519 -C "email" -f ~/.ssh/webdev26_key` | Crea una nuova SSH key                |
| `cat ~/.ssh/webdev26_key.pub`                             | Mostra la public key                  |
| `docker compose up -d`                                    | Avvia i container in background       |
| `docker exec -it ubuntu-ssh bash`                         | Entra nel container                   |
| `ssh -i ~/.ssh/webdev26_key root@localhost -p 2222`       | Connessione SSH usando una key        |
| `ssh -o PubkeyAuthentication=no root@localhost -p 2222`   | Connessione SSH senza public key      |
| `docker compose down`                                     | Ferma e rimuove i container           |
| `rm ~/.ssh/webdev26_key ~/.ssh/webdev26_key.pub`          | Elimina le key create per esercizio   |
| `ssh-keygen -R "[localhost]:2222"`                        | Rimuove localhost:2222 da known_hosts |
| `crontab -e`                                              | Apre la configurazione cron           |
| `chmod +x file.sh`                                        | Rende uno script eseguibile           |
| `cat file.txt`                                            | Mostra il contenuto di un file        |

---

# Parole chiave

| Termine           | Significato                                            |
| ----------------- | ------------------------------------------------------ |
| SSH               | Protocollo sicuro per collegarsi a server remoti       |
| Secure Shell      | Nome completo di SSH                                   |
| VPS               | Virtual Private Server                                 |
| Private key       | Chiave privata, da non condividere                     |
| Public key        | Chiave pubblica, da mettere sul server                 |
| `authorized_keys` | File sul server che contiene le public key autorizzate |
| `known_hosts`     | File locale con i server SSH già conosciuti            |
| Docker Compose    | Tool per avviare servizi/container da una file compose |
| Cronjob           | Attività programmata nel tempo                         |
| Crontab           | File/configurazione dei cronjob                        |
| Bash              | Shell/interprete comandi comune su Linux               |
| Shell script      | File contenente comandi da eseguire                    |
| Shebang           | Prima riga che indica l’interprete dello script        |
