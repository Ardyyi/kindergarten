# 🚀 Backend Setup - Schritt für Schritt

## 1️⃣ Schnellstart (5 Minuten)

### **A) Supabase Option (EMPFOHLEN - kostenlos)**

#### Schritt 1: Supabase starten
```bash
# Gehe auf: https://supabase.com/
# Click: Start your project
# Registriere dich mit GitHub oder Email
```

#### Schritt 2: Projekt erstellen
```
Name: "kindergarten"
Password: [sicheres Passwort]
Region: europe-west1 (Irland/nah bei Deutschland)
```

#### Schritt 3: SQL ausführen
In Supabase → SQL Editor → Klick auf "+" → Paste diesen Code:

```sql
-- Users Table
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT UNIQUE NOT NULL,
  name TEXT NOT NULL,
  role TEXT NOT NULL CHECK (role IN ('educator', 'parent')),
  kindergarten_id UUID,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Children Table
CREATE TABLE children (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  kindergarten_id UUID NOT NULL,
  name TEXT NOT NULL,
  emoji TEXT DEFAULT '👧',
  birth_date DATE,
  "group" TEXT,
  allergies TEXT,
  contact TEXT,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Events Table
CREATE TABLE events (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  kindergarten_id UUID NOT NULL,
  title TEXT NOT NULL,
  event_date DATE,
  event_time TIME,
  category TEXT DEFAULT 'event',
  description TEXT,
  created_by UUID REFERENCES users(id),
  created_at TIMESTAMP DEFAULT NOW()
);

-- Portfolio Table
CREATE TABLE portfolio_entries (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  child_id UUID NOT NULL REFERENCES children(id) ON DELETE CASCADE,
  emoji TEXT DEFAULT '📸',
  title TEXT NOT NULL,
  description TEXT,
  category TEXT DEFAULT 'sprache',
  image_url TEXT,
  created_by UUID REFERENCES users(id),
  created_at TIMESTAMP DEFAULT NOW()
);

-- Day Reports Table
CREATE TABLE day_reports (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  kindergarten_id UUID NOT NULL,
  "group" TEXT NOT NULL,
  mood TEXT,
  activities TEXT,
  notes TEXT,
  report_date DATE DEFAULT CURRENT_DATE,
  created_by UUID REFERENCES users(id),
  created_at TIMESTAMP DEFAULT NOW()
);

-- Messages Table
CREATE TABLE messages (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  kindergarten_id UUID NOT NULL,
  sender_id UUID NOT NULL REFERENCES users(id),
  recipient_id UUID REFERENCES users(id),
  message TEXT NOT NULL,
  read BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Absences Table
CREATE TABLE absences (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  child_id UUID NOT NULL REFERENCES children(id) ON DELETE CASCADE,
  reported_by UUID REFERENCES users(id),
  reason TEXT,
  from_date DATE NOT NULL,
  to_date DATE NOT NULL,
  note TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Enable RLS
ALTER TABLE children ENABLE ROW LEVEL SECURITY;
ALTER TABLE events ENABLE ROW LEVEL SECURITY;
ALTER TABLE portfolio_entries ENABLE ROW LEVEL SECURITY;
ALTER TABLE day_reports ENABLE ROW LEVEL SECURITY;
ALTER TABLE messages ENABLE ROW LEVEL SECURITY;
ALTER TABLE absences ENABLE ROW LEVEL SECURITY;
```

#### Schritt 4: API Keys kopieren
```
Supabase Dashboard → Settings → API
→ Copy "anon public" Key
→ Copy "service_role" Key (geheim halten!)
```

#### Schritt 5: Frontend Update
In `index.html` oben nach `<script>` hinzufügen:

```javascript
// =================================
// SUPABASE CONFIG
// =================================
const SUPABASE_URL = 'https://YOUR_PROJECT.supabase.co';
const SUPABASE_ANON_KEY = 'eyJ...'; // Deine public key

// Import Supabase (oben in Head, vor </head>):
// <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>

const { createClient } = window.supabase;
const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

// ServerMode aktivieren
app.serverMode = true;
```

#### Schritt 6: Test
```
Öffne: https://ardyyi.github.io/kindergarten/
Login: Name + Rolle
Add Child → sollte in Supabase landen
```

---

## 2️⃣ Node.js Backend (Lokal)

### Setup

```bash
# Ordner erstellen
mkdir kindergarten-backend
cd kindergarten-backend

# npm initialisieren
npm init -y

# Dependencies
npm install express cors dotenv pg
npm install -D nodemon

# .env erstellen
cat > .env << 'EOF'
DATABASE_URL=postgresql://user:password@localhost:5432/kindergarten
PORT=3000
NODE_ENV=development
EOF

# Start Script in package.json:
# "dev": "nodemon server.js"
```

### server.js erstellen

```javascript
// kindergarten-backend/server.js
const express = require('express');
const cors = require('cors');
const { Pool } = require('pg');
require('dotenv').config();

const app = express();
app.use(cors());
app.use(express.json());

// Database Connection
const pool = new Pool({
  connectionString: process.env.DATABASE_URL
});

// ==========================================
// CHILDREN ROUTES
// ==========================================

// Get all children
app.get('/api/children', async (req, res) => {
  try {
    const result = await pool.query('SELECT * FROM children ORDER BY created_at DESC');
    res.json(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).json({ error: err.message });
  }
});

// Add child
app.post('/api/children', async (req, res) => {
  const { name, emoji, birth_date, group, allergies, contact } = req.body;
  
  if (!name || !group) {
    return res.status(400).json({ error: 'Name and group required' });
  }
  
  try {
    const result = await pool.query(
      'INSERT INTO children (name, emoji, birth_date, "group", allergies, contact) VALUES ($1, $2, $3, $4, $5, $6) RETURNING *',
      [name, emoji || '👧', birth_date, group, allergies || '', contact || '']
    );
    res.json(result.rows[0]);
  } catch (err) {
    console.error(err);
    res.status(500).json({ error: err.message });
  }
});

// Delete child
app.delete('/api/children/:id', async (req, res) => {
  try {
    await pool.query('DELETE FROM children WHERE id = $1', [req.params.id]);
    res.json({ success: true });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

// ==========================================
// EVENTS ROUTES
// ==========================================

// Get all events
app.get('/api/events', async (req, res) => {
  try {
    const result = await pool.query(
      'SELECT * FROM events ORDER BY event_date ASC'
    );
    res.json(result.rows);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

// Add event
app.post('/api/events', async (req, res) => {
  const { title, event_date, event_time, category, description } = req.body;
  
  if (!title || !event_date) {
    return res.status(400).json({ error: 'Title and date required' });
  }
  
  try {
    const result = await pool.query(
      'INSERT INTO events (title, event_date, event_time, category, description) VALUES ($1, $2, $3, $4, $5) RETURNING *',
      [title, event_date, event_time || null, category || 'event', description || '']
    );
    res.json(result.rows[0]);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

// Delete event
app.delete('/api/events/:id', async (req, res) => {
  try {
    await pool.query('DELETE FROM events WHERE id = $1', [req.params.id]);
    res.json({ success: true });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

// ==========================================
// PORTFOLIO ROUTES
// ==========================================

// Get portfolio entries
app.get('/api/portfolio', async (req, res) => {
  try {
    const result = await pool.query(
      'SELECT * FROM portfolio_entries ORDER BY created_at DESC'
    );
    res.json(result.rows);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

// Add portfolio entry
app.post('/api/portfolio', async (req, res) => {
  const { child_id, emoji, title, description, category } = req.body;
  
  if (!child_id || !title) {
    return res.status(400).json({ error: 'Child and title required' });
  }
  
  try {
    const result = await pool.query(
      'INSERT INTO portfolio_entries (child_id, emoji, title, description, category) VALUES ($1, $2, $3, $4, $5) RETURNING *',
      [child_id, emoji || '📸', title, description || '', category || 'sprache']
    );
    res.json(result.rows[0]);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

// ==========================================
// MESSAGES ROUTES
// ==========================================

// Get messages
app.get('/api/messages', async (req, res) => {
  try {
    const result = await pool.query(
      'SELECT * FROM messages ORDER BY created_at DESC'
    );
    res.json(result.rows);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

// Send message
app.post('/api/messages', async (req, res) => {
  const { sender_id, recipient_id, message } = req.body;
  
  try {
    const result = await pool.query(
      'INSERT INTO messages (sender_id, recipient_id, message) VALUES ($1, $2, $3) RETURNING *',
      [sender_id, recipient_id || null, message]
    );
    res.json(result.rows[0]);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

// ==========================================
// SERVER START
// ==========================================

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`🌱 KitaKompass Backend running on http://localhost:${PORT}`);
});

// Graceful Shutdown
process.on('SIGTERM', () => {
  console.log('Server shutting down...');
  pool.end();
  process.exit(0);
});
```

### Starten

```bash
npm run dev
# Output: 🌱 KitaKompass Backend running on http://localhost:3000
```

---

## 3️⃣ Database Setup (PostgreSQL lokal)

### macOS
```bash
brew install postgresql@15
brew services start postgresql@15
createdb kindergarten
psql kindergarten < schema.sql
```

### Ubuntu/Linux
```bash
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql
sudo -u postgres createdb kindergarten
sudo -u postgres psql kindergarten < schema.sql
```

### Windows
```
Download: https://www.postgresql.org/download/windows/
Installer ausführen → pgAdmin öffnen
Create Database: kindergarten
```

---

## 4️⃣ Frontend mit Backend verbinden

In `index.html`, am Anfang der `app` Object:

```javascript
const app = {
  user: null,
  serverMode: false, // Umschalten zwischen lokal & server
  apiUrl: 'http://localhost:3000/api', // Backend URL
  
  // ...rest des codes
  
  // Beispiel: addChild mit Server-Sync
  addChild() {
    const emoji = document.getElementById('child-emoji').value || '👧';
    const name = document.getElementById('child-name').value.trim();
    const birth = document.getElementById('child-birth').value;
    const group = document.getElementById('child-group').value.trim();
    const allergies = document.getElementById('child-allergies').value.trim();
    const contact = document.getElementById('child-contact').value.trim();
    
    if (!name || !group) { alert('Name und Gruppe erforderlich'); return; }
    
    const child = {
      emoji, name, birth_date: birth, group, allergies, contact
    };
    
    // 1. Lokal speichern
    child.id = Date.now();
    this.data.children.push(child);
    this.saveData();
    
    // 2. Zum Server senden (wenn serverMode aktiv)
    if (this.serverMode) {
      fetch(`${this.apiUrl}/children`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(child)
      })
      .then(r => r.json())
      .then(serverChild => {
        child.id = serverChild.id; // Echte ID vom Server
        this.saveData();
        console.log('✅ Kind zum Server gesynct');
      })
      .catch(err => {
        console.error('❌ Server Fehler:', err);
        alert('Fehler beim Speichern zum Server');
      });
    }
    
    this.updateChildrenList();
    this.go('s-children');
  }
};
```

---

## 5️⃣ Deployment auf einen Server

### Mit Heroku (kostenlos zum Testen)

```bash
# Heroku CLI installieren
npm install -g heroku

# Login
heroku login

# App erstellen
heroku create kindergarten-api

# PostgreSQL Add-on
heroku addons:create heroku-postgresql:hobby-dev

# Deploy
git push heroku main

# View Logs
heroku logs --tail
```

### Mit DigitalOcean (5$ / Monat)

```bash
# SSH in Server
ssh root@your_ip

# Node installieren
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Code klonen
git clone https://github.com/Ardyyi/kindergarten.git
cd kindergarten-backend

# Dependencies
npm install

# PM2 (Process Manager)
sudo npm install -g pm2
pm2 start server.js
pm2 startup
pm2 save

# Nginx (Reverse Proxy)
sudo apt install nginx
sudo nano /etc/nginx/sites-available/kindergarten

# Paste:
server {
    listen 80;
    server_name api.kindergarten.de;
    
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

sudo systemctl restart nginx

# SSL (Let's Encrypt)
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d api.kindergarten.de
```

---

## 6️⃣ Troubleshooting

| Problem | Lösung |
|---------|--------|
| "CORS error" | Backend CORS Headers checken, `app.use(cors())` hinzufügen |
| "Connection refused" | Backend läuft nicht? `npm run dev` in Terminal |
| "Database connection error" | DATABASE_URL in .env checken |
| "Port 3000 already in use" | PORT ändern oder `lsof -i :3000` + `kill` |
| Änderungen nicht im Frontend | Seite Refresh, Cache leeren |

---

## ✅ Checkliste für Production

- [ ] HTTPS/SSL aktivieren
- [ ] Environment Variables sicher gespeichert (.env in .gitignore)
- [ ] Database Backups einrichten
- [ ] Rate Limiting hinzufügen
- [ ] Input Validation überall
- [ ] Error Logging (Sentry.io)
- [ ] Monitoring (New Relic, DataDog)
- [ ] Datenschutzerklärung
- [ ] Terms of Service
- [ ] Zwei-Faktor-Auth für Admin

---

🎉 **Du bist bereit für den Produktiv-Betrieb!**

Fragen? Fehler? Öffne ein Issue auf GitHub!
