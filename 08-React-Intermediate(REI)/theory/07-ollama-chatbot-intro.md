# Ollama Chatbot: introduzione e progetto pratico

## 1. Introduzione

In questa lezione vediamo come costruire un piccolo **chatbot con Ollama**.

L'obiettivo è capire come collegare una interfaccia frontend a un modello AI che gira in locale sul nostro computer.

Useremo il concetto di:

- chatbot
- modello AI locale
- API
- richiesta HTTP
- risposta del modello
- frontend React o Next.js
- route backend

---

# 2. Cos'è Ollama?

**Ollama** è uno strumento che permette di eseguire modelli di intelligenza artificiale localmente sul proprio computer.

Questo significa che possiamo usare un modello AI senza dover necessariamente chiamare un servizio esterno online.

Con Ollama possiamo scaricare ed eseguire modelli come:

- Llama
- Mistral
- Gemma
- Qwen
- altri modelli disponibili nella libreria di Ollama

---

# 3. Cosa significa modello locale?

Un modello locale è un modello AI che gira sul nostro computer.

Normalmente, quando usiamo un chatbot online, il messaggio viene inviato a un server esterno.

Con Ollama, invece, possiamo fare una cosa simile ma usando il nostro computer come ambiente di esecuzione.

Schema:

```txt
Utente scrive un messaggio
        ↓
Frontend invia il messaggio
        ↓
Ollama riceve la richiesta
        ↓
Il modello genera una risposta
        ↓
La risposta torna al frontend
        ↓
L'utente vede la risposta nella chat
```

---

# 4. Perché usare Ollama?

Ollama è utile perché permette di sperimentare con modelli AI in modo pratico.

Vantaggi:

- puoi testare modelli AI localmente
- puoi creare chatbot personali
- puoi integrare l'AI in applicazioni web
- puoi imparare come funzionano le API AI
- puoi lavorare su piccoli progetti senza dover creare subito account cloud
- puoi costruire demo, prototipi e tool personali

---

# 5. Cosa stiamo costruendo?

Vogliamo costruire un piccolo chatbot.

Il chatbot deve permettere all'utente di:

1. scrivere un messaggio
2. inviare il messaggio
3. ricevere una risposta da Ollama
4. vedere la conversazione nella pagina

---

# 6. Componenti principali del progetto

Un chatbot con Ollama può essere diviso in tre parti principali:

```txt
Frontend
Backend route
Ollama API
```

## Frontend

Il frontend è la parte visibile all'utente.

Contiene:

- input di testo
- bottone di invio
- lista dei messaggi
- stato della conversazione
- loading state

## Backend route

La backend route riceve il messaggio dal frontend e lo invia a Ollama.

In Next.js può essere una API route.

## Ollama API

Ollama espone una API locale.

Questa API riceve il prompt o i messaggi e restituisce la risposta del modello.

---

# 7. API locale di Ollama

Quando Ollama è in esecuzione, espone una API locale.

Di solito l'indirizzo base è:

```txt
http://localhost:11434/api
```

Uno degli endpoint più usati per una chat è:

```txt
/api/chat
```

Quindi l'URL completo locale è:

```txt
http://localhost:11434/api/chat
```

---

# 8. Endpoint /api/chat

Per creare un chatbot, l'endpoint più adatto è:

```txt
POST /api/chat
```

Questo endpoint riceve una lista di messaggi.

Esempio struttura:

```json
{
  "model": "gemma3",
  "messages": [
    {
      "role": "user",
      "content": "Hello!"
    }
  ]
}
```

---

# 9. Differenza tra /api/generate e /api/chat

Ollama offre diversi endpoint.

Due endpoint importanti sono:

```txt
/api/generate
/api/chat
```

## /api/generate

Si usa per generare una risposta a partire da un prompt singolo.

Esempio:

```json
{
  "model": "gemma3",
  "prompt": "Spiegami React in modo semplice"
}
```

## /api/chat

Si usa per una conversazione.

Riceve un array di messaggi.

Esempio:

```json
{
  "model": "gemma3",
  "messages": [
    {
      "role": "user",
      "content": "Ciao"
    }
  ]
}
```

Per un chatbot, di solito usiamo `/api/chat`.

---

# 10. Cos'è un messaggio nella chat?

Un messaggio è un oggetto che contiene almeno due proprietà:

```json
{
  "role": "user",
  "content": "Ciao"
}
```

## role

`role` indica chi sta parlando.

I ruoli più comuni sono:

- `user`
- `assistant`
- `system`

## content

`content` contiene il testo del messaggio.

---

# 11. Ruoli dei messaggi

## user

Il messaggio scritto dall'utente.

Esempio:

```json
{
  "role": "user",
  "content": "Spiegami Next.js"
}
```

## assistant

La risposta generata dal modello AI.

Esempio:

```json
{
  "role": "assistant",
  "content": "Next.js è un framework basato su React..."
}
```

## system

Un messaggio di istruzioni generali per il modello.

Esempio:

```json
{
  "role": "system",
  "content": "Rispondi sempre in italiano e in modo semplice."
}
```

---

# 12. Struttura dei messaggi in React

Nel frontend possiamo rappresentare i messaggi con un tipo TypeScript.

```ts
type ChatMessage = {
  role: "user" | "assistant" | "system";
  content: string;
};
```

Poi possiamo creare uno state:

```tsx
const [messages, setMessages] = useState<ChatMessage[]>([]);
```

Questo significa:

> `messages` è un array di oggetti `ChatMessage`.

---

# 13. Stato del chatbot

Un chatbot di solito ha bisogno di più stati.

Esempio:

```tsx
const [messages, setMessages] = useState<ChatMessage[]>([]);
const [input, setInput] = useState("");
const [isLoading, setIsLoading] = useState(false);
```

## messages

Contiene la conversazione.

## input

Contiene il testo scritto dall'utente.

## isLoading

Indica se stiamo aspettando la risposta del modello.

---

# 14. Flusso del chatbot

Quando l'utente invia un messaggio, succede questo:

```txt
1. L'utente scrive nell'input
2. Clicca su invia
3. Il messaggio viene aggiunto alla chat
4. Il frontend invia i messaggi alla API route
5. La API route chiama Ollama
6. Ollama genera una risposta
7. La risposta torna al frontend
8. Il messaggio dell'assistente viene aggiunto alla chat
```

---

# 15. Perché usare una route backend?

Potremmo chiamare Ollama direttamente dal frontend, ma spesso è meglio usare una backend route.

Motivi:

- separiamo frontend e logica server
- possiamo controllare meglio gli errori
- possiamo aggiungere validazioni
- possiamo cambiare modello senza modificare la UI
- possiamo proteggere meglio eventuali configurazioni
- in Next.js possiamo creare una API route nello stesso progetto

---

# 16. Struttura progetto Next.js

Esempio struttura:

```txt
app/
  page.tsx
  api/
    chat/
      route.ts
components/
  Chat.tsx
```

## app/page.tsx

La pagina principale.

## components/Chat.tsx

Il componente frontend della chat.

## app/api/chat/route.ts

La route backend che chiama Ollama.

---

# 17. API route in Next.js

In Next.js con App Router, una API route si crea così:

```txt
app/api/chat/route.ts
```

Dentro il file possiamo gestire una richiesta POST.

Esempio base:

```ts
export async function POST(request: Request) {
  const body = await request.json();

  return Response.json({
    message: "Richiesta ricevuta",
    data: body
  });
}
```

---

# 18. API route che chiama Ollama

Esempio:

```ts
export async function POST(request: Request) {
  const body = await request.json();

  const response = await fetch("http://localhost:11434/api/chat", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      model: "gemma3",
      messages: body.messages,
      stream: false
    })
  });

  const data = await response.json();

  return Response.json(data);
}
```

In questo esempio:

- riceviamo i messaggi dal frontend
- li inviamo a Ollama
- riceviamo la risposta
- restituiamo la risposta al frontend

---

# 19. Perché stream: false?

Ollama può restituire risposte in streaming.

Streaming significa che la risposta arriva pezzo per pezzo.

Per iniziare, possiamo disattivare lo streaming usando:

```json
"stream": false
```

Così riceviamo una risposta completa in un solo oggetto.

Questo rende il codice più semplice per una prima versione del chatbot.

---

# 20. Possibile risposta di Ollama

Una risposta semplificata può contenere un oggetto `message`.

Esempio:

```json
{
  "message": {
    "role": "assistant",
    "content": "Ciao! Come posso aiutarti?"
  }
}
```

Nel frontend possiamo prendere:

```ts
data.message.content
```

e aggiungerlo alla chat.

---

# 21. Componente Chat in React / Next.js

Siccome il componente usa `useState` e `onSubmit`, deve essere un Client Component.

Quindi in cima al file scriviamo:

```tsx
"use client";
```

Esempio:

```tsx
"use client";

import { useState } from "react";

type ChatMessage = {
  role: "user" | "assistant" | "system";
  content: string;
};

export default function Chat() {
  const [messages, setMessages] = useState<ChatMessage[]>([]);
  const [input, setInput] = useState("");
  const [isLoading, setIsLoading] = useState(false);

  async function handleSubmit(event: React.FormEvent<HTMLFormElement>) {
    event.preventDefault();

    if (!input.trim()) {
      return;
    }

    const userMessage: ChatMessage = {
      role: "user",
      content: input
    };

    const nextMessages = [...messages, userMessage];

    setMessages(nextMessages);
    setInput("");
    setIsLoading(true);

    try {
      const response = await fetch("/api/chat", {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          messages: nextMessages
        })
      });

      const data = await response.json();

      const assistantMessage: ChatMessage = {
        role: "assistant",
        content: data.message.content
      };

      setMessages([...nextMessages, assistantMessage]);
    } catch (error) {
      console.error(error);

      const errorMessage: ChatMessage = {
        role: "assistant",
        content: "Errore: non riesco a contattare Ollama."
      };

      setMessages([...nextMessages, errorMessage]);
    } finally {
      setIsLoading(false);
    }
  }

  return (
    <div>
      <h1>Ollama Chatbot</h1>

      <div>
        {messages.map((message, index) => (
          <div key={index}>
            <strong>{message.role}: </strong>
            <span>{message.content}</span>
          </div>
        ))}
      </div>

      {isLoading && <p>Sto generando la risposta...</p>}

      <form onSubmit={handleSubmit}>
        <input
          value={input}
          onChange={(event) => setInput(event.target.value)}
          placeholder="Scrivi un messaggio..."
        />

        <button type="submit" disabled={isLoading}>
          Invia
        </button>
      </form>
    </div>
  );
}
```

---

# 22. Usare il componente nella pagina

File:

```txt
app/page.tsx
```

Esempio:

```tsx
import Chat from "@/components/Chat";

export default function HomePage() {
  return (
    <main>
      <Chat />
    </main>
  );
}
```

---

# 23. Comandi utili Ollama

## Verificare che Ollama sia installato

```bash
ollama --version
```

## Scaricare un modello

Esempio:

```bash
ollama pull gemma3
```

## Avviare una chat nel terminale

```bash
ollama run gemma3
```

## Controllare i modelli installati

```bash
ollama list
```

---

# 24. Testare Ollama con curl

Possiamo testare Ollama dal terminale.

Esempio:

```bash
curl http://localhost:11434/api/chat -d '{
  "model": "gemma3",
  "messages": [
    {
      "role": "user",
      "content": "Ciao!"
    }
  ],
  "stream": false
}'
```

Se Ollama funziona, dovremmo ricevere una risposta JSON.

---

# 25. Errori comuni

## Ollama non è avviato

Se ricevi un errore di connessione, probabilmente Ollama non è in esecuzione.

Controlla che il servizio Ollama sia attivo.

---

## Modello non installato

Se usi:

```txt
gemma3
```

ma il modello non è installato, devi prima eseguire:

```bash
ollama pull gemma3
```

---

## Endpoint sbagliato

Per una chat usa:

```txt
/api/chat
```

Per una generazione singola puoi usare:

```txt
/api/generate
```

---

## Dimenticare "stream": false

Se non usi:

```json
"stream": false
```

Ollama potrebbe restituire una risposta streaming.

Questo è utile per chatbot avanzati, ma può complicare la prima versione.

---

# 26. Perché il componente Chat deve essere "use client"?

Il componente `Chat` usa:

- `useState`
- `onChange`
- `onSubmit`
- input controllato
- stato di loading

Quindi deve girare nel browser.

Per questo scriviamo:

```tsx
"use client";
```

in cima al file.

---

# 27. Backend route e Server Side

La route:

```txt
app/api/chat/route.ts
```

gira lato server.

Questo significa che il codice dentro la route non viene eseguito nel browser.

La route può chiamare Ollama usando:

```ts
fetch("http://localhost:11434/api/chat")
```

Questo è codice server-side.

---

# 28. Differenza tra frontend e backend nel progetto

## Frontend

File esempio:

```txt
components/Chat.tsx
```

Responsabilità:

- mostrare messaggi
- gestire input
- gestire click o submit
- mostrare loading
- chiamare `/api/chat`

---

## Backend

File esempio:

```txt
app/api/chat/route.ts
```

Responsabilità:

- ricevere messaggi dal frontend
- chiamare Ollama
- gestire errori
- restituire la risposta al frontend

---

# 29. Versione più sicura della API route

Possiamo aggiungere controlli base.

```ts
export async function POST(request: Request) {
  try {
    const body = await request.json();

    if (!Array.isArray(body.messages)) {
      return Response.json(
        { error: "Messages deve essere un array" },
        { status: 400 }
      );
    }

    const response = await fetch("http://localhost:11434/api/chat", {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        model: "gemma3",
        messages: body.messages,
        stream: false
      })
    });

    if (!response.ok) {
      return Response.json(
        { error: "Errore nella risposta di Ollama" },
        { status: 500 }
      );
    }

    const data = await response.json();

    return Response.json(data);
  } catch (error) {
    console.error(error);

    return Response.json(
      { error: "Errore interno del server" },
      { status: 500 }
    );
  }
}
```

---

# 30. Possibile miglioramento: system message

Possiamo aggiungere un messaggio di sistema.

Esempio:

```ts
const systemMessage = {
  role: "system",
  content: "Rispondi sempre in italiano, in modo semplice e didattico."
};
```

Poi possiamo inviare a Ollama:

```ts
messages: [systemMessage, ...body.messages]
```

Così il chatbot avrà istruzioni di comportamento.

---

# 31. Esempio API route con system message

```ts
export async function POST(request: Request) {
  const body = await request.json();

  const systemMessage = {
    role: "system",
    content: "Rispondi sempre in italiano, in modo semplice e didattico."
  };

  const response = await fetch("http://localhost:11434/api/chat", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      model: "gemma3",
      messages: [systemMessage, ...body.messages],
      stream: false
    })
  });

  const data = await response.json();

  return Response.json(data);
}
```

---

# 32. Miglioramenti possibili del chatbot

Dopo la prima versione, possiamo migliorare il progetto aggiungendo:

- messaggi con stile diverso per user e assistant
- loading spinner
- streaming della risposta
- salvataggio della chat nel localStorage
- scelta del modello
- cancellazione conversazione
- system prompt personalizzato
- cronologia delle conversazioni
- supporto Markdown nelle risposte
- gestione errori più chiara

---

# 33. Mini struttura finale del progetto

```txt
app/
  page.tsx
  api/
    chat/
      route.ts

components/
  Chat.tsx
```

---

# 34. Schema mentale

```txt
Chat.tsx
  ↓
fetch("/api/chat")
  ↓
app/api/chat/route.ts
  ↓
fetch("http://localhost:11434/api/chat")
  ↓
Ollama
  ↓
Risposta AI
  ↓
Frontend aggiorna la chat
```

---

# 35. Concetti chiave

## Ollama

Strumento per eseguire modelli AI localmente.

## Modello

Il sistema AI che genera la risposta.

## API

Interfaccia che permette al nostro codice di comunicare con Ollama.

## Endpoint

URL specifico di una API.

## /api/chat

Endpoint di Ollama per conversazioni.

## messages

Array di messaggi della conversazione.

## role

Indica chi parla: user, assistant o system.

## content

Contiene il testo del messaggio.

## stream

Indica se la risposta arriva pezzo per pezzo oppure tutta insieme.

---

# 36. Schema veloce da copiare

## Frontend

```tsx
const response = await fetch("/api/chat", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    messages: nextMessages
  })
});
```

---

## Backend

```ts
const response = await fetch("http://localhost:11434/api/chat", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    model: "gemma3",
    messages: body.messages,
    stream: false
  })
});
```

---

# 37. Riassunto finale

In questa lezione abbiamo visto come creare un chatbot con Ollama.

Ollama ci permette di eseguire modelli AI localmente.

Il nostro chatbot è composto da:

- un frontend React/Next.js
- una API route backend
- una chiamata alla API locale di Ollama

Il frontend raccoglie il messaggio dell'utente e lo invia a `/api/chat`.

La route backend riceve il messaggio e lo inoltra a Ollama.

Ollama genera una risposta e la restituisce alla route.

La route restituisce la risposta al frontend.

Il frontend aggiorna la conversazione e mostra la risposta all'utente.

Il concetto più importante da ricordare è:

> Il frontend non genera la risposta AI.  
> Il frontend invia il messaggio al backend, il backend chiama Ollama, e Ollama genera la risposta.
