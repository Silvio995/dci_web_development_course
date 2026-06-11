Cos’è il Cloud Computing

Il Cloud Computing è un modo moderno di far girare le applicazioni online.

Prima si usava:

un server fisico (computer in un data center)
oppure un VPS (server virtuale affittato)

Oggi invece si usa il cloud:

invece di comprare un server, affitti solo le risorse che ti servono quando ti servono.

🏢 Principali provider Cloud

I tre grandi sono:

Amazon Web Services (AWS) → il più grande e completo
Google Cloud (GCP) → cloud di Google
Microsoft Azure → cloud di Microsoft
🧩 Modelli di servizi Cloud
1. IaaS (Infrastructure as a Service)

👉 “Ti do i server, il resto lo fai tu”

server virtuali
storage
rete

Tu gestisci:

sistema operativo
software
sicurezza

Esempio:

AWS EC2

📌 Analogia:

terreno vuoto: tu costruisci tutto da zero

2. PaaS (Platform as a Service)

👉 “Carichi il codice e basta”

niente gestione server
deploy automatico da Git
log disponibili

Esempi:

Heroku
Vercel
Netlify
Render

📌 Analogia:

cucina già pronta: porti solo gli ingredienti (il codice)

3. SaaS (Software as a Service)

👉 “Software già pronto da usare”

Esempi:

Google Docs
Microsoft 365
Salesforce

Per sviluppatori:

spesso si usano API di SaaS nei progetti

Esempio importante del corso:

Cloudinary

📌 Analogia:

app già pronta, la usi senza pensare a come funziona dietro

📦 Containerizzazione

Tecnologia come Docker.

Virtual Machine (VM)
sistema operativo completo
pesante
lento da avviare
Container
leggero
usa lo stesso OS del sistema host
parte in pochi secondi

📌 Analogia:

VM = casa intera
Container = appartamento nello stesso edificio
☁️ Cloud Storage

Servizi per salvare file online:

Esempi:

AWS S3
Google Cloud Storage
Hetzner Storage

Nel corso userete:

Cloudinary (specializzato per immagini e video)

Funzioni:

upload file
download file
eliminazione file via API
🗄️ Database Cloud

Invece di installare un database da soli, si usa un servizio gestito:

Esempi:

AWS RDS
Google Cloud SQL
Azure SQL
MongoDB Atlas

📌 Vantaggio:

backup automatici
aggiornamenti gestiti
scalabilità
⚡ Serverless

👉 “Non gestisci server, solo funzioni”

il codice si attiva solo quando serve
paghi solo quando viene eseguito

Esempi:

AWS Lambda
Google Cloud Functions
Azure Functions
Vercel Functions

Esempio pratico:

upload immagine → funzione crea thumbnail automaticamente

📌 Usato anche in:

Next.js su Vercel
⚠️ Attenzione ai costi

Il cloud può diventare molto costoso se:

traffico improvviso aumenta
auto-scaling non controllato
bot o errori generano richieste infinite

👉 Regole importanti:

sempre impostare un budget
controllare i limiti di spesa
non usare servizi senza controllo dei costi
🧠 In sintesi

Oggi hai imparato che:

il cloud è “affitto di risorse online”
esistono 3 livelli: IaaS, PaaS, SaaS
container = modo moderno di eseguire app leggere
storage e database possono essere completamente gestiti dal cloud
serverless = paghi solo quando il codice gira
attenzione sempre ai costi

# Upload immagini con Express + Multer + Cloudinary

## 📌 Panoramica
Questa lezione mostra come creare un backend con Express per:
- caricare immagini
- salvarle su Cloudinary
- recuperarle tramite API
- eliminarle

Viene usato un array (`imageStore`) come database temporaneo.

---

## 🛠️ Tecnologie usate
- Express → server backend
- Multer → gestione upload file
- Cloudinary → storage immagini cloud
- HTTPS module → recupero immagini dal cloud
- CORS → accesso dal frontend

---

## 📤 Upload immagine (POST `/images`)

### Flusso:
1. Il file viene ricevuto con Multer in memoria
2. Il file è disponibile in `req.file.buffer`
3. Viene creato uno stream verso Cloudinary
4. L’immagine viene caricata nel cloud

### Dati salvati:
```js
{
  public_id,
  secure_url
}