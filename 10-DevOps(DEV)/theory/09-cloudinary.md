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