# 🌱 KitaKompass - Kindergarten Management System

Ein modernes Verwaltungssystem für Kindergärten mit Rollen-basiertem Zugang für Erzieher und Eltern.

**Status:** Phase 1-3 vorbereitet | localStorage + Backend-ready

---

## 📱 **BEDIENUNGSANLEITUNG**

### **1️⃣ LOGIN**

**Startbildschirm:**
- **Name eingeben** - Dein Name (z.B. "Anna Schmidt")
- **Rolle wählen:**
  - 🧑‍🏫 **Erzieher/in** - Vollzugriff, verwaltet Daten
  - 👨‍👩‍👧 **Eltern** - Eingeschränkter Zugang, sieht nur sein Kind

```
Demo: Name "Demo" + beliebige Rolle
→ Alle Daten landen in localStorage (Browserspeicher)
→ Persisten über Seite neuladen
```

---

### **🧑‍🏫 ERZIEHER - Kompletter Workflow**

#### **1. Kinderprofile erstellen**
```
Startseite → [Kinderprofile] 
→ [Kind hinzufügen]
  • Foto: 👧 oder 👦 (Emoji)
  • Name: "Ben Müller"
  • Geburtsdatum: 2020-03-15
  • Gruppe: "Sonnenhase" (oder "Mondtier", etc.)
  • Allergien: "Nussallergie, laktosefrei"
  • Elternkontakt: "+49 123 456789"

Speichern → Kind erscheint in der Liste
Tippen auf Kind → Detail mit Edit/Löschen
```

**Warum Emoji-Fotos?** 
- Schneller als Upload
- Keine Dateigrößen-Probleme
- Datenschutz (kein echtes Foto lokal)
- Später: echte Bilder möglich mit Backend

#### **2. Kalender verwalten**
```
Startseite → [Kalender]
→ [+] Button (oben rechts) → Neuer Termin
  • Titel: "Sommerfest"
  • Datum: 2026-07-15
  • Uhrzeit: 14:00 (optional)
  • Kategorie: 
    - Veranstaltung (für Events)
    - Feiertag (Urlaub, Feiertage)
    - Elternabend (Treffen)
    - Sonstiges
  • Beschreibung: "Im Hof, ab 14 Uhr"

Speichern → Eltern sehen es sofort in ihrem Kalender
```

#### **3. Portfolio schreiben**
```
Startseite → [Portfolio]
→ [+] Button → Neuer Eintrag
  • Kind: "Ben Müller" wählen
  • Foto: 🎨 (zeigt Aktivitäts-Art)
  • Titel: "Erstes Bild gemalt"
  • Beschreibung: "Ben hat heute mit Acrylfarben experimentiert..."
  • Kategorie:
    - Sprache (Kommunikation, Wortschatz)
    - Motorik (Bewegung, Koordination)
    - Sozial (Gruppenfähigkeit, Freundschaften)
    - Kreativ (Kunstwerke, Fantasie)
    - Kognitiv (Problemlösen, Lernen)

Speichern → Eintrag ist da + Eltern sehen es
```

#### **4. Tagesrückblick (täglich senden)**
```
Startseite → [Tagesrückblick]
  • Gruppe: "Sonnenhase" wählen
  • Stimmung: 😊 (sad, neutral, happy, very-happy)
  • Aktivitäten: "Morgenkreis, Malen, Spielplatz, Mittagessen"
  • Besonderheiten: "Max hat heute so gut gekletteri, Luisa war müde"

Speichern → An alle Eltern der Gruppe sichtbar
```

#### **5. Mit Eltern chatten**
```
Startseite → [Nachrichten]
  • Liste aller Nachrichten (Konversation)
  • Unten: Input-Feld
  • Text eingeben + [Senden]
  
💡 Später: Pro Kind/Parent separate Chats
```

#### **6. Statistiken anschauen**
```
Startseite → [Berichte]
  • Kinder: 5
  • Portfolio-Einträge: 23
  • Nachrichten: 15
  • Termine: 3
  
💡 Export kommt mit Backend
```

---

### **👨‍👩‍👧 ELTERN - Was sie sehen**

#### **Mein Kind**
```
Startseite → [Mein Kind]
  • Profil: Name, Alter, Gruppe
  • Allergien: Was das Kind hat
  
💡 Später: Foto vom echten Kind (mit Datenschutz)
```

#### **Kalender**
```
Startseite → [Kalender]
  • Termine von Erzieher (lesend)
  • Sommerfest, Elternabend, Urlaub, etc.
  • Nicht bearbeitbar (Schutz vor Versehentlichem)
```

#### **Portfolio des Kindes**
```
Startseite → [Portfolio]
  • Alle Aktivitäten/Werke ihres Kindes
  • Fotos mit Beschreibung
  • Kategorisiert (Sprache, Motorik, etc.)
```

#### **Tagesberichte**
```
Startseite → [Tagesberichte]
  • Tägliche Updates vom Erzieher
  • "Heute: 😊 sehr gute Stimmung"
  • Was gemacht wurde
  • Besonderheiten
```

#### **Abwesenheit melden**
```
Startseite → [Abwesenheit]
  • Grund: Krank, Urlaub, Arzt, Sonstiges
  • Von: 2026-07-01
  • Bis: 2026-07-05
  • Notiz: "Fieber 38.5°C"
  
Melden → Erzieher sieht es
```

#### **Chat**
```
Startseite → [Nachrichten]
  • Live-Chat mit Erzieher
  • Fragen stellen
  • Fotos teilen (später)
```

---

## 🔧 **TECHNISCHE ARCHITEKTUR**

### **Phase 1 (Aktuell) - localStorage Only**
```
Browser (iPhone Safari)
    ↓
[index.html]
    ├── localStorage (Browser Speicher)
    │   ├── kitakompass-user
    │   ├── kitakompass-data
    │   └── (max ~5-10MB)
    │
    └── Tabler Icons (CDN)
```

**Vorteile:**
- ✅ Funktioniert offline
- ✅ Keine Server-Kosten
- ✅ Datenschutz (alles lokal)
- ✅ Schnell

**Nachteile:**
- ❌ Daten nur auf 1 Gerät
- ❌ Kein Teilen zwischen Erzieher & Eltern
- ❌ Bei App-Update Daten weg
- ❌ Kein Backup

---

### **Phase 2/3 (Mit Server)**

#### **Minimal-Backend (Node.js + Supabase)**

```javascript
// Backend Architecture
Browser (iPhone)
    ↓
[index.html mit API-Calls]
    ↓
[Backend Server (Node.js)]
    ├── POST /api/children
    ├── GET /api/children
    ├── POST /api/messages
    ├── GET /api/messages
    └── etc.
    ↓
[Database (Supabase/PostgreSQL)]
    ├── users
    ├── children
    ├── events
    ├── portfolio
    ├── messages
    └── absences
    ↓
[File Storage]
    ├── fotos/
    ├── portfolio/
    └── documents/
```

---

## 🚀 **SETUP FÜR BACKEND (Phase 3)**

### **Option A: Supabase (Empfohlen - kostenlos)**

#### **1. Supabase Account erstellen**
```
supabase.com → Sign up (kostenlos)
→ New Project
→ Name: "kindergarten"
→ Password: sichere Passwort
→ Region: Europe (Germany wenn möglich)
```

#### **2. Datenbank-Schema erstellen**

In Supabase SQL Editor:

```sql
-- Nutzer
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT auth.uid(),
  email TEXT UNIQUE NOT NULL,
  name TEXT NOT NULL,
  role TEXT CHECK (role IN ('educator', 'parent')) NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Kinder
CREATE TABLE children (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  educator_id UUID REFERENCES users(id) ON DELETE CASCADE,
  name TEXT NOT NULL,
  emoji TEXT,
  birth_date DATE,
  "group" TEXT,
  allergies TEXT,
  contact TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Portfolio
CREATE TABLE portfolio_entries (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  child_id UUID REFERENCES children(id) ON DELETE CASCADE,
  educator_id UUID REFERENCES users(id),
  emoji TEXT,
  title TEXT NOT NULL,
  description TEXT,
  category TEXT,
  photo_url TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Events
CREATE TABLE events (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  educator_id UUID REFERENCES users(id),
  title TEXT NOT NULL,
  event_date DATE,
  event_time TIME,
  category TEXT,
  description TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Messages
CREATE TABLE messages (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  sender_id UUID REFERENCES users(id),
  recipient_id UUID REFERENCES users(id),
  message TEXT NOT NULL,
  read BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Day Reports
CREATE TABLE day_reports (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  educator_id UUID REFERENCES users(id),
  "group" TEXT NOT NULL,
  mood TEXT,
  activities TEXT,
  notes TEXT,
  report_date DATE DEFAULT CURRENT_DATE,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Absences
CREATE TABLE absences (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  parent_id UUID REFERENCES users(id),
  child_id UUID REFERENCES children(id),
  reason TEXT,
  from_date DATE,
  to_date DATE,
  note TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);
```

#### **3. Row Level Security (RLS) aktivieren**

```sql
-- Nur Erzieher sieht seine Kinder
ALTER TABLE children ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Educators see own children"
  ON children FOR SELECT
  USING (educator_id = auth.uid());

-- Nur User sieht seine Nachrichten
ALTER TABLE messages ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users see own messages"
  ON messages FOR SELECT
  USING (sender_id = auth.uid() OR recipient_id = auth.uid());
```

#### **4. API Keys holen**
```
Supabase Dashboard → Settings → API
→ anon (public)
→ service_role (secret)

In index.html:
const SUPABASE_URL = "https://xxx.supabase.co"
const SUPABASE_KEY = "eyJ..."
```

---

### **Option B: Einfener Node.js Server (Lokal/VPS)**

#### **1. Backend-Struktur aufsetzen**

```bash
mkdir kindergarten-backend
cd kindergarten-backend

npm init -y
npm install express cors dotenv supabase
npm install -D nodemon

# Folder structure:
kindergarten-backend/
├── server.js
├── routes/
│   ├── children.js
│   ├── portfolio.js
│   ├── messages.js
│   └── events.js
├── middleware/
│   └── auth.js
├── .env
└── package.json
```

#### **2. server.js**

```javascript
const express = require('express');
const cors = require('cors');
const { createClient } = require('@supabase/supabase-js');
require('dotenv').config();

const app = express();
app.use(cors());
app.use(express.json());

const supabase = createClient(
  process.env.SUPABASE_URL,
  process.env.SUPABASE_SERVICE_KEY
);

// Example: Get all children
app.get('/api/children', async (req, res) => {
  const { data, error } = await supabase
    .from('children')
    .select('*')
    .eq('educator_id', req.user.id);
  
  if (error) return res.status(400).json(error);
  res.json(data);
});

// Example: Create child
app.post('/api/children', async (req, res) => {
  const { name, emoji, group, allergies, contact } = req.body;
  
  const { data, error } = await supabase
    .from('children')
    .insert([{
      educator_id: req.user.id,
      name, emoji, group, allergies, contact
    }])
    .select();
  
  if (error) return res.status(400).json(error);
  res.json(data[0]);
});

// Similar routes for:
// POST /api/messages
// GET /api/messages
// POST /api/portfolio
// GET /api/portfolio
// etc.

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

#### **3. .env Datei**

```env
SUPABASE_URL=https://xxx.supabase.co
SUPABASE_SERVICE_KEY=eyJ...
PORT=3000
```

#### **4. Starten**

```bash
npm run dev   # mit nodemon
# oder
node server.js
```

---

## 🔗 **Frontend mit Backend verbinden**

### **In index.html anpassen:**

```javascript
// Oben in der App hinzufügen:
const API_URL = 'http://localhost:3000/api'; // oder https://deine-domain.com/api

// Example: addChild mit API
addChild() {
  const name = document.getElementById('child-name').value.trim();
  // ...
  
  // Lokal speichern (während Test):
  this.data.children.push({ id: Date.now(), name, ... });
  
  // Optional: Zum Backend senden
  if (this.serverMode) {
    fetch(`${API_URL}/children`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name, emoji, group, allergies, contact })
    })
    .then(r => r.json())
    .then(child => {
      console.log('Child saved to server:', child);
    })
    .catch(err => console.error(err));
  }
}

// Load Modus umschalten:
// localStorage: Offline-First
// Server: Nur Backend
```

---

## 📋 **Deployment**

### **Option 1: GitHub Pages (nur localStorage)**
```bash
cd kindergarten
git add .
git commit -m "Update"
git push
# Auto-deployed auf https://ardyyi.github.io/kindergarten/
```

### **Option 2: Vercel (mit Backend)**
```bash
npm install -g vercel
vercel
# Auto-deployed
```

### **Option 3: Eigener Server (VPS)**
```bash
# SSH in Server
ssh user@123.456.789.0

# Code hochladen
git clone https://github.com/Ardyyi/kindergarten.git

# Backend starten
cd kindergarten-backend
pm2 start server.js --name "kita"

# Reverse Proxy (nginx)
sudo nano /etc/nginx/sites-available/kindergarten
# → proxy_pass http://localhost:3000;

sudo systemctl restart nginx
```

---

## 🔐 **Sicherheit für Produktion**

```javascript
// Wichtig vor Live-Nutzung:

✅ HTTPS aktivieren (Let's Encrypt gratis)
✅ Passwords hashen (bcrypt)
✅ JWT Tokens für Auth
✅ Rate Limiting
✅ Input Validation
✅ CORS richtig konfigurieren
✅ RLS in Datenbank
✅ Regelmäßige Backups
✅ Datenschutzerklärung (GDPR)
✅ Eltern-Benachrichtigungen verschlüsseln
```

---

## 🐛 **Häufige Probleme**

| Problem | Lösung |
|---------|--------|
| Daten weg nach Hard-Refresh | → localStorage leert sich nicht, check Browser Console |
| App auf iPhone nicht aktualisiert | → Hard Refresh: Safari → oben Reload + halten |
| Backend antwortet nicht | → Check CORS, API URL, Server läuft? |
| Fehler beim Upload | → localStorage ist voll? Größenlimit ~5MB |
| Zwei Nutzer sehen unterschiedliche Daten | → Ohne Server normal! Jeder hat lokale Kopie |

---

## 📞 **Support & Roadmap**

### **Jetzt funktioniert:**
- ✅ Login mit Rollen
- ✅ Kinderprofile
- ✅ Kalender
- ✅ Portfolio
- ✅ Tagesrückblick
- ✅ Chat (lokal)
- ✅ Statistiken

### **Nächste Schritte (Phase 3):**
- 🔄 Backend-Integration (Supabase)
- 📤 Datei-Upload (Fotos, PDFs)
- 🔔 Push-Benachrichtigungen
- 👥 Multi-User Sync
- 📊 Erweiterte Reports
- 🌐 Video-Chat (Jitsi)
- 📄 PDF-Export

---

## 📄 **Lizenz**

Für private Nutzung kostenlos. Kommerziell: [Support kontaktieren]

---

**Fragen?** Issues auf GitHub oder Email: [deine-email]
