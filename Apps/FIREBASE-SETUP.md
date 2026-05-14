# Firebase Live-Sync Setup für HTML-Apps

Dieses Dokument erklärt, wie man eine HTML-App mit **Firebase Realtime Database** für Live-Synchronisierung zwischen Geräten aufbaut.

## Schnellstart

### 1. Firebase-Projekt erstellen
- Gehe zu https://firebase.google.com → **Console**
- **Neues Projekt** erstellen
- **Realtime Database** aktivieren (Ort: `europe-west1`)
- Modus: **Testmodus** (für Anfang okay)

### 2. Firebase Config kopieren
- ⚙️ **Projekteinstellungen** → **Deine Apps**
- **Web-App hinzufügen** (`</>`)
- `firebaseConfig` Objekt kopieren

### 3. In HTML einbinden
```html
<!-- Firebase SDK (Compat-Version für bessere Kompatibilität) -->
<script src="https://www.gstatic.com/firebasejs/10.7.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.0/firebase-database-compat.js"></script>

<script>
  const firebaseConfig = {
    apiKey: "...",
    authDomain: "...",
    databaseURL: "...",
    projectId: "...",
    storageBucket: "...",
    messagingSenderId: "...",
    appId: "..."
  };

  // Firebase initialisieren
  firebase.initializeApp(firebaseConfig);
  window.db = firebase.database();
  console.log('✅ Firebase ready');
</script>
```

### 4. Daten speichern
```javascript
const Storage = {
  save(data) {
    localStorage.setItem('myapp-data', JSON.stringify(data));
    
    // Zu Firebase speichern (async)
    if (window.db) {
      window.db.ref('data').set(data)
        .then(() => console.log('✅ Saved to Firebase'))
        .catch(err => console.error('❌ Error:', err));
    }
  }
};
```

### 5. Live-Listener setzen
```javascript
if (window.db) {
  window.db.ref('data').on('value', (snapshot) => {
    const remoteData = snapshot.val();
    if (remoteData) {
      // Lokale Daten mit Remote-Daten abgleichen
      const localData = JSON.parse(localStorage.getItem('myapp-data'));
      if (JSON.stringify(localData) !== JSON.stringify(remoteData)) {
        localStorage.setItem('myapp-data', JSON.stringify(remoteData));
        // UI aktualisieren
        renderUI();
      }
    }
  });
}
```

## Wie es funktioniert
1. **Lokal:** Daten werden in `localStorage` gespeichert (funktioniert offline)
2. **Firebase:** Bei Änderung → automatisch zu Firebase hochgeladen
3. **Sync:** Listener hört auf Firebase — wenn andere Geräte ändern → automatisch aktualisiert
4. **Cross-Device:** Mac, iPhone, Browser-Tabs synchronisieren sich **Echtzeit**

## Sicherheit
Für Produktiv-Einsatz in Firebase Console → **Realtime Database → Rules**:
```json
{
  "rules": {
    "data": {
      ".read": true,
      ".write": true
    }
  }
}
```

Später: Mit Authentifizierung absichern

## Tipps
- **localStorage als Fallback**: App funktioniert offline
- **Versionierung**: Gib deinen Daten eine Version (`v1`, `v2`) für Migrationen
- **Konflikt-Handling**: Letzte Änderung gewinnt (einfach) oder Custom-Logic
- **Limits**: Firebase kostenlos: 100 gleichzeitige Verbindungen, 1GB Speicher

## Debugging
- **Konsole öffnen:** F12
- **Logs ansehen:** `🔄 Firebase save`, `✅ Firebase saved`, `🔔 Firebase data received`
- **Firebase Console:** Gehe zu Realtime Database, schau die Struktur an
