# Vercel Deployment Workflow – Schnellreferenz

## 🎯 Übersicht deines Workflows

1. **Website lokal entwickeln** (VS Code / Claude Code)
2. **Auf GitHub Business-Account pushen**
3. **Vercel Account für Kunden erstellen**
4. **Mit Vercel CLI deployen** (via Token)
5. **Änderungen verwalten** (Kunden-Account bleibt sauber)

---

## 🔑 Schritt 1: Vercel Token vom Kunden

**Der Kunde macht das einmalig:**

1. Vercel Dashboard öffnen → **Settings** → **Tokens**
2. **Create** → Neuen Token generieren
   - Name: z.B. "Developer Deploy Token" oder "Agentur XYZ"
   - Kopieren und dir geben (NICHT ins GitHub pushen!)

---

## 💻 Schritt 2: Lokal auf deinem Rechner einrichten

### A) Token speichern (einmalig)

**Projekt-Ordner öffnen** (wo deine Website liegt):

```bash
cd ~/Projekte/kunde-website
```

**`.env` Datei erstellen:**

```bash
# Datei erstellen (macOS/Linux) oder manuell in Editor
touch .env
```

**Token in `.env` eintragen:**

```
VERCEL_TOKEN=dein_token_hier_einfuegen
VERCEL_ORG_ID=optional_falls_vorhanden
VERCEL_PROJECT_ID=optional_falls_vorhanden
```

### B) `.env` vor GitHub schützen

**`.gitignore` überprüfen/anpassen:**

```bash
# In deinem Projekt-Root
echo ".env" >> .gitignore
echo ".env.local" >> .gitignore
```

**Überprüfen:**

```bash
cat .gitignore
# Sollte enthalten: .env und .env.local
```

---

## 🚀 Schritt 3: Deployment Befehle

### Development & Testen (lokal)

```bash
# Terminal öffnen im Projekt-Ordner
cd ~/Projekte/kunde-website

# Environment laden (vor jedem Deployment)
source .env

# Vercel CLI installiert? (einmalig)
npm install -g vercel

# Überprüfen, ob alles funktioniert
vercel whoami
# → Sollte Kunden-Account/Team zeigen
```

### Live Deployment

```bash
# Environment laden
source .env

# Deployen (Production)
vercel deploy --prod

# Nur Preview (zum Testen vor Production)
vercel deploy
```

### Andere nützliche Befehle

```bash
source .env

# Status überprüfen
vercel status

# Projekt-Info anzeigen
vercel list

# Logs anschauen
vercel logs
```

---

## 📋 Kompletter Workflow – Tag-zu-Tag

### Neue Website (Kunde)

```bash
# 1. Website lokal entwickeln
cd ~/Projekte/neue-kunde-website
# ... entwickeln in VS Code ...

# 2. GitHub pushen
git add .
git commit -m "Website fertig"
git push origin main

# 3. Token vom Kunden holen und eintragen
echo "VERCEL_TOKEN=token_vom_kunden" > .env
echo ".env" >> .gitignore

# 4. Deployen
source .env
npm install -g vercel  # falls nicht installiert
vercel deploy --prod

# 5. Domain in Vercel mit Custom Domain verbinden
# → Im Vercel Dashboard manuell oder via CLI
```

### Änderung an bestehender Website

```bash
# 1. In dein lokales Repo gehen
cd ~/Projekte/kunde-website

# 2. Änderungen machen in VS Code
# ... Dateien editieren ...

# 3. Lokal testen
npm start  # oder dein test-befehl

# 4. GitHub pushen
git add .
git commit -m "Beschreibung der Änderung"
git push origin main

# 5. Vercel deployen
source .env
vercel deploy --prod

# ✅ Fertig! Kunden-Website aktualisiert
```

---

## ⚠️ Sicherheit – WICHTIG!

### ❌ NIEMALS:

```bash
# Token ins GitHub pushen
git add .env
git push

# Token in Befehlen sichtbar machen
vercel deploy --token=abc123def...

# Token über Slack/Email verschicken (unverschlüsselt)
```

### ✅ IMMER:

```bash
# .env in .gitignore
echo ".env" >> .gitignore

# Token nur lokal speichern
# Nur über sichere Kanäle teilen (Passwort-Manager, verschlüsselte Email)

# Token regelmäßig wechseln (Kunde kann alten Token in Vercel löschen)
```

---

## 🔄 Multiple Kunden verwalten

Für mehrere Kunden-Projekte:

```bash
# Kunde A
cd ~/Projekte/kunde-a-website
export VERCEL_TOKEN=token_kunde_a
vercel deploy --prod

# Kunde B
cd ~/Projekte/kunde-b-website
export VERCEL_TOKEN=token_kunde_b
vercel deploy --prod
```

**Oder pro Projekt `.env` Datei** (empfohlen):

```
~/Projekte/
├── kunde-a-website/
│   ├── .env (TOKEN_A)
│   └── ...
├── kunde-b-website/
│   ├── .env (TOKEN_B)
│   └── ...
```

---

## 🆘 Troubleshooting

### "vercel not found"
```bash
npm install -g vercel
```

### "Token ungültig"
```bash
# Überprüfe ob .env richtig geladen wurde
echo $VERCEL_TOKEN
# Sollte deinen Token anzeigen

# Wenn leer: Token nicht gesetzt
source .env
echo $VERCEL_TOKEN  # Jetzt sollte es angezeigt werden
```

### "Project ID not found"
```bash
# Alternativ ohne Project ID deployen
source .env
vercel deploy --prod
# → Vercel findet Projekt automatisch
```

### Token abgelaufen / Kunde hat ihn gelöscht
```bash
# Neuen Token vom Kunden holen
# .env aktualisieren
echo "VERCEL_TOKEN=neuer_token" > .env
# Erneut deployen
source .env
vercel deploy --prod
```

---

## 📚 Nützliche Links

- [Vercel CLI Dokumentation](https://vercel.com/docs/cli)
- [Vercel Tokens erstellen/verwalten](https://vercel.com/kb/guide/how-do-i-use-a-vercel-api-access-token)
- [Vercel Deploy Befehle](https://vercel.com/docs/cli/deploy)

---

## ✅ Checkliste für neuen Kunden

- [ ] Website entwickelt & getestet
- [ ] Auf GitHub gepusht
- [ ] Vercel Account für Kunden erstellt
- [ ] Token vom Kunden erhalten
- [ ] `.env` Datei mit Token erstellt
- [ ] `.gitignore` hat `.env` eingetragen
- [ ] `vercel deploy --prod` erfolgreich
- [ ] Domain in Vercel konfiguriert
- [ ] Kunde kann Vercel Dashboard sehen & Zahlungsdaten

---

**Zuletzt aktualisiert:** 2026-05-14  
**Dein Vercel Workflow - immer zur Hand! 🚀**
