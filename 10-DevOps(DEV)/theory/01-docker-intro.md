# Riassunto lezione: DevOps e Docker

## 1. Che cosa significa DevOps

La parola **DevOps** nasce dall’unione di due parole:

- **Development** = sviluppo
- **Operations** = gestione operativa / messa in funzione / manutenzione

Noi come sviluppatori conosciamo già la parte di **development**, cioè scrivere codice, creare funzionalità, lavorare su frontend, backend, database e API.

Con DevOps iniziamo invece a capire anche cosa succede **dopo** lo sviluppo:

- come si testa un progetto;
- come si fa il build;
- come si prepara un’app per l’esecuzione;
- come si mette online;
- come si controlla che funzioni;
- come si gestiscono errori, server, log e ambienti.

L’obiettivo della lezione non è diventare subito DevOps Engineer, ma avere una base per capire le discussioni tecniche reali in un team.

---

## 2. Perché un developer deve conoscere DevOps

Anche se un developer non lavora direttamente come DevOps Engineer, deve comunque conoscere i concetti base perché nel lavoro reale spesso dovrà parlare di:

- deploy;
- ambiente di sviluppo;
- ambiente di produzione;
- build;
- container;
- log;
- variabili ambiente;
- problemi di configurazione;
- server;
- porte;
- database;
- errori in produzione.

Un DevOps Engineer di solito ha competenze molto ampie. Non è per forza lo sviluppatore principale dell’app, ma conosce bene:

- sistemi di deployment;
- server management;
- sicurezza;
- infrastruttura;
- requisiti aziendali;
- monitoraggio;
- automazione.

Per questo è utile che anche un junior developer capisca il linguaggio base di DevOps.

---

## 3. Concetti principali di DevOps

### Deployment

Il **deployment** è il processo con cui un’applicazione viene resa disponibile in un certo ambiente.

Può essere:

- ambiente di sviluppo;
- ambiente di test;
- staging;
- produzione.

L’obiettivo è distribuire aggiornamenti in modo affidabile e ripetibile.

Termini collegati:

- **Continuous Delivery**
- **Continuous Deployment**
- **CD**

---

### Monitoring e logging

Il **monitoring** serve a controllare lo stato dell’app e dell’infrastruttura.

Serve a capire:

- l’app è online?
- risponde lentamente?
- ci sono errori?
- il server è sovraccarico?
- il database risponde?

Il **logging** consiste nel raccogliere e leggere i log prodotti dall’app o dai container.

Esempio Docker:

```bash
docker logs nome-container
```

---

### Infrastructure management

L’**infrastructure management** riguarda la gestione dell’infrastruttura necessaria per far girare l’applicazione.

Può includere:

- server;
- database;
- storage;
- reti;
- configurazioni;
- sicurezza;
- scalabilità.

---

### Performance management

Il **performance management** serve ad assicurarsi che l’app rispetti certi livelli di performance.

Esempi:

- tempi di risposta bassi;
- carico gestibile;
- risorse sufficienti;
- database ottimizzato;
- scalabilità quando aumentano gli utenti.

---

### Incident e problem management

Un **incident** è un problema che interrompe o degrada il funzionamento dell’app.

Esempi:

- server offline;
- database non raggiungibile;
- errore nel deploy;
- bug critico in produzione.

Il problem management cerca anche la causa del problema per evitare che si ripeta.

---

## 4. Perché impariamo Docker

Docker è uno degli strumenti più usati nel mondo DevOps e nello sviluppo moderno.

È importante perché:

- è molto richiesto nel lavoro;
- è utile per il curriculum;
- permette di lavorare in ambienti più controllati;
- riduce problemi tra computer diversi;
- aiuta a simulare ambienti reali;
- rende più semplice condividere app e configurazioni.

Il problema classico che Docker aiuta a risolvere è:

> “Sul mio computer funziona.”

Questo problema nasce perché ogni sviluppatore può avere configurazioni diverse:

- sistema operativo diverso;
- versione Node diversa;
- database diverso;
- variabili ambiente diverse;
- dipendenze diverse;
- porte occupate;
- configurazioni locali non uguali alla produzione.

Docker prova a rendere l’ambiente più uguale per tutti.

---

## 5. Che cosa significa containerizzare un’app

**Containerizzare** un’app significa impacchettarla dentro uno o più container.

Un container contiene tutto ciò che serve per far girare l’app:

- sistema minimo;
- runtime, per esempio Node.js;
- librerie;
- dependencies;
- configurazioni;
- codice;
- comandi di avvio.

In inglese si dice spesso:

> Dockerizing an application

cioè preparare l’applicazione per essere eseguita con Docker.

---

## 6. Container vs Virtual Machine

Un container assomiglia a una piccola macchina virtuale, ma è più leggero.

Una macchina virtuale tradizionale ha un sistema operativo completo per ogni VM.

Un container invece:

- condivide il kernel del sistema host;
- usa meno risorse;
- parte più velocemente;
- è più leggero;
- può essere creato, fermato e distrutto più facilmente.

Quindi Docker è utile perché permette di avere ambienti isolati senza il peso di una VM completa.

---

## 7. Image e Container

### Image

Una **image** è una specie di modello o template.

Contiene le istruzioni e i file necessari per creare un container.

Possiamo immaginarla come una classe in programmazione.

### Container

Un **container** è una istanza in esecuzione di una image.

Possiamo immaginarlo come un oggetto creato da una classe.

Schema:

```text
Image = modello
Container = istanza in esecuzione
```

Esempio:

```text
postgres image
       ↓
container some-postgres
```

Un container ha:

- un ID;
- un nome;
- uno stato;
- eventuali porte esposte;
- log;
- filesystem interno.

---

## 8. Come funziona Docker a livello base

Il flusso base è:

```text
Image
  ↓ docker run
Container
```

Esempio:

```bash
docker run hello-world
```

Cosa succede:

1. Docker controlla se l’immagine `hello-world` esiste localmente.
2. Se non esiste, la scarica da Docker Hub.
3. Crea un container da quell’immagine.
4. Esegue il programma.
5. Il container termina subito.

---

## 9. Docker Hub

**Docker Hub** è un repository online di immagini Docker.

Da lì Docker può scaricare immagini già pronte, per esempio:

- `hello-world`
- `postgres`
- `ubuntu`
- `node`
- `httpd`

Quando lanciamo:

```bash
docker run postgres
```

se l’immagine non esiste nel computer, Docker la scarica automaticamente da Docker Hub.

---

## 10. Dockerfile

Un **Dockerfile** è una file di definizione usato per creare una propria immagine Docker.

Serve quando vogliamo costruire un’immagine personalizzata con:

- il nostro codice;
- le nostre dipendenze;
- la nostra configurazione;
- il nostro comando di avvio.

Flusso:

```text
Dockerfile
   ↓ docker build
Image
   ↓ docker run
Container
```

---

## 11. Docker Compose e Kubernetes

### Docker Compose

Molti progetti reali non hanno un solo container.

Esempio:

- frontend;
- backend Node.js;
- PostgreSQL;
- Elasticsearch;
- Redis.

Avviare tutto manualmente sarebbe scomodo.

**Docker Compose** serve a orchestrare più container con un solo comando.

Esempio concettuale:

```bash
docker compose up
```

---

### Kubernetes

Per progetti molto grandi, soprattutto in produzione, ci possono essere centinaia o migliaia di container.

In quel caso si usa spesso **Kubernetes**.

Kubernetes serve a gestire grandi ambienti containerizzati.

Può:

- creare container quando servono;
- distruggere container;
- aggiornare container;
- scalare servizi;
- gestire deployment complessi.

È molto potente, ma anche più complesso.

---

## 12. Comandi Docker base

### Test iniziale

```bash
docker run hello-world
```

Serve per verificare che Docker funzioni.

---

### Vedere i container attivi

```bash
docker ps
```

Mostra solo i container in esecuzione.

---

### Vedere tutti i container

```bash
docker ps -a
```

Mostra anche i container fermati.

---

### Vedere le immagini

```bash
docker images
```

Mostra le immagini presenti nel computer.

---

### Aiuto su un comando

```bash
docker help ps
```

Mostra le opzioni disponibili per `docker ps`.

---

## 13. Esempio con PostgreSQL

Durante la lezione è stato usato questo comando:

```bash
docker run --name some-postgres -e POSTGRES_PASSWORD=mypass123 -d postgres
```

Scomposizione:

### `docker run`

Avvia un nuovo container.

---

### `--name some-postgres`

Dà un nome fisso al container.

In questo modo possiamo riferirci al container usando il nome:

```bash
some-postgres
```

invece dell’ID generato automaticamente.

---

### `-e POSTGRES_PASSWORD=mypass123`

Imposta una variabile ambiente dentro il container.

Nel caso dell’immagine `postgres`, questa variabile viene letta all’avvio e serve per impostare la password dell’utente `postgres`.

---

### `-d`

Significa **detached mode**.

Il container gira in background e il terminale resta libero.

Senza `-d`, il terminale rimane occupato dai log del container.

---

### `postgres`

È il nome dell’immagine da usare.

Se l’immagine non è presente localmente, Docker la scarica da Docker Hub.

---

## 14. Entrare dentro un container

Per aprire una shell dentro un container attivo:

```bash
docker exec -it some-postgres bash
```

Scomposizione:

### `docker exec`

Esegue un comando dentro un container già in esecuzione.

---

### `-it`

Significa:

- `i` = interactive;
- `t` = terminal.

Serve per poter interagire con la shell.

---

### `some-postgres`

È il nome del container.

---

### `bash`

È il comando da eseguire dentro il container.

Per uscire:

```bash
CTRL + D
```

oppure:

```bash
exit
```

---

## 15. Usare psql dentro il container

Per entrare in PostgreSQL come utente `postgres`:

```bash
docker exec -it some-postgres psql -U postgres
```

Se invece usiamo un utente che non esiste:

```bash
docker exec -it some-postgres psql -U thisfails
```

il comando fallisce, perché quell’utente non è stato creato.

---

## 16. Vedere i log di un container

```bash
docker logs some-postgres
```

Questo comando mostra i log prodotti dal container.

È molto utile per capire:

- se il container è partito;
- se ci sono errori;
- se il servizio è pronto;
- se l’app sta ricevendo richieste.

---

## 17. Fermare e riavviare un container

### Fermare

```bash
docker stop some-postgres
```

Ferma il container.

---

### Riavviare

```bash
docker start some-postgres
```

Riavvia un container già esistente.

Attenzione:

`docker start` non crea un nuovo container. Riavvia un container già creato.

---

## 18. Eliminare container e image

### Eliminare un container

Prima bisogna fermarlo:

```bash
docker stop some-postgres
```

Poi eliminarlo:

```bash
docker rm some-postgres
```

---

### Eliminare una image

```bash
docker rmi postgres
```

Oppure con il nome della propria image.

Regola importante:

> Un’immagine non può essere eliminata se esiste ancora un container basato su quella image, anche se il container è fermo.

Quindi prima:

```bash
docker rm nome-container
```

poi:

```bash
docker rmi nome-image
```

---

## 19. Il problema delle porte

All’inizio il container PostgreSQL era chiuso verso l’esterno.

Questo significa che il database girava dentro il container, ma dal computer host non era raggiungibile direttamente.

Per renderlo raggiungibile bisogna pubblicare una porta.

Sintassi:

```bash
-p PORTA_HOST:PORTA_CONTAINER
```

Esempio:

```bash
-p 54320:5432
```

Significa:

```text
localhost:54320 sul mio computer
        ↓
porta 5432 dentro il container
```

---

## 20. Errore porta già occupata

Comando che può dare errore:

```bash
docker run --name some-postgres -e POSTGRES_PASSWORD=mypass123 -d -p 5432:5432 postgres
```

Errore:

```text
failed to bind host port 0.0.0.0:5432/tcp: address already in use
```

Significa:

> La porta 5432 del computer è già usata da un altro servizio.

Nel nostro caso probabilmente PostgreSQL era già installato sul laptop e stava usando la porta `5432`.

Soluzione:

Usare una porta diversa sul computer host:

```bash
docker run --name some-postgres -e POSTGRES_PASSWORD=mypass123 -d -p 54320:5432 postgres
```

Qui la porta interna del container resta `5432`, ma sul computer usiamo `54320`.

---

## 21. Collegarsi a PostgreSQL da fuori il container

Dopo aver pubblicato la porta:

```bash
docker run --name some-postgres -e POSTGRES_PASSWORD=mypass123 -d -p 54320:5432 postgres
```

possiamo collegarci da fuori con:

```bash
psql -h localhost -p 54320 -U postgres
```

Scomposizione:

- `-h localhost` = connettiti al computer locale;
- `-p 54320` = usa la porta pubblicata;
- `-U postgres` = accedi come utente postgres.

---

## 22. Capire il flag `-d`

Senza `-d`:

```bash
docker run --name some-postgres -e POSTGRES_PASSWORD=mypass123 postgres
```

il container parte, ma i log rimangono attaccati al terminale.

Il terminale sembra “bloccato”, ma in realtà sta mostrando l’output del container.

Con `-d`:

```bash
docker run --name some-postgres -e POSTGRES_PASSWORD=mypass123 -d postgres
```

il container gira in background.

Frase da ricordare:

> `-d` significa detached mode: container attivo in background.

---

## 23. Esempio con Webserver Apache/httpd

Comando usato:

```bash
docker run --name mein-webserver -d -p 9004:80 httpd
```

Scomposizione:

- `docker run` = crea e avvia un container;
- `--name mein-webserver` = dà un nome al container;
- `-d` = lo avvia in background;
- `-p 9004:80` = collega la porta 9004 del computer alla porta 80 del container;
- `httpd` = immagine Apache.

Poi nel browser:

```text
http://localhost:9004
```

Risultato:

```text
It works!
```

Questa è la pagina standard di Apache.

---

## 24. Modificare un file dentro il container Apache

Per entrare nel container:

```bash
docker exec -it mein-webserver sh
```

Poi per modificare la pagina iniziale:

```bash
echo "<h1>Hallo aus dem Container!</h1>" > /usr/local/apache2/htdocs/index.html
```

Poi ricaricando il browser su:

```text
http://localhost:9004
```

la pagina mostra:

```text
Hallo aus dem Container!
```

Questo dimostra che possiamo entrare in un container e modificare file interni.

Attenzione però:

> Le modifiche fatte direttamente dentro un container possono andare perse se il container viene eliminato.

Nel lavoro reale è meglio modificare il codice nella repo o usare volumi.

---

## 25. Schema mentale generale

```text
Docker Hub
   ↓ docker pull automatico
Image
   ↓ docker run
Container
   ↓ docker exec / logs / stop / start
Servizio in esecuzione
   ↓ -p HOST:CONTAINER
Accesso da localhost
```

---

## 26. Frasi chiave da memorizzare

- DevOps unisce sviluppo e gestione operativa.
- Il deployment serve a rendere disponibile un’app in un ambiente.
- Monitoring e logging servono a controllare app, errori e stato del sistema.
- Docker aiuta a evitare il problema “sul mio computer funziona”.
- Una image è il modello.
- Un container è l’istanza in esecuzione.
- `docker run` crea e avvia un container da una image.
- `docker ps` mostra i container attivi.
- `docker ps -a` mostra tutti i container.
- `docker images` mostra le immagini.
- `docker exec -it` esegue comandi dentro un container attivo.
- `docker logs` mostra i log.
- `docker stop` ferma un container.
- `docker start` riavvia un container esistente.
- `docker rm` elimina un container.
- `docker rmi` elimina una image.
- `-d` avvia il container in background.
- `-p HOST:CONTAINER` pubblica una porta.
- Se una porta è già occupata, bisogna usare una porta host diversa.
- Docker Compose serve a gestire più container.
- Kubernetes serve a orchestrare grandi ambienti containerizzati.

---

## 27. Mini tabella dei comandi

| Comando | Significato |
|---|---|
| `docker run hello-world` | Test iniziale di Docker |
| `docker ps` | Mostra container attivi |
| `docker ps -a` | Mostra tutti i container |
| `docker images` | Mostra immagini disponibili |
| `docker run --name nome image` | Avvia container con nome |
| `docker exec -it nome bash` | Entra nel container con bash |
| `docker exec -it nome sh` | Entra nel container con sh |
| `docker logs nome` | Mostra log |
| `docker stop nome` | Ferma container |
| `docker start nome` | Riavvia container |
| `docker rm nome` | Elimina container |
| `docker rmi image` | Elimina image |
| `-d` | Detached mode / background |
| `-e KEY=value` | Variabile ambiente |
| `-p 9004:80` | Porta host 9004 verso porta container 80 |
| `-p 54320:5432` | Porta host 54320 verso porta container 5432 |

---

## 28. Possibili domande da challenge

### Che cos’è DevOps?

DevOps è l’unione tra sviluppo software e gestione operativa. Include concetti come build, test, deployment, monitoring, logging e gestione dell’infrastruttura.

---

### Perché usiamo Docker?

Per creare ambienti isolati, riproducibili e più simili tra sviluppatori, test e produzione.

---

### Che differenza c’è tra image e container?

Una image è il modello. Un container è un’istanza in esecuzione di quella image.

---

### Cosa fa `docker run hello-world`?

Scarica l’immagine `hello-world` se non esiste, crea un container, esegue il programma e poi il container termina.

---

### Cosa fa `docker ps`?

Mostra i container attualmente in esecuzione.

---

### Cosa fa `docker ps -a`?

Mostra tutti i container, anche quelli fermati.

---

### A cosa serve `docker exec -it`?

Serve a eseguire un comando dentro un container già attivo, spesso per aprire una shell interattiva.

---

### A cosa serve `-p 54320:5432`?

Collega la porta `54320` del computer alla porta `5432` dentro il container.

---

### Perché `-p 5432:5432` può dare errore?

Perché la porta `5432` del computer può essere già occupata da un PostgreSQL locale.

---

### Che cosa significa `-d`?

Significa detached mode: il container gira in background e il terminale resta libero.

---

### A cosa serve Docker Compose?

Serve a gestire più container collegati tra loro con un solo comando.

---

### A cosa serve Kubernetes?

Serve a orchestrare grandi quantità di container in produzione.
