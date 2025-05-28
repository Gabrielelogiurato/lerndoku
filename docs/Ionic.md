# Mobile App mit Ionic (React)

## Einführung

### Warum eigentlich Ionic?

Mit Ionic kannst du Apps bauen, die auf fast allem laufen, egal ob auf einem Android-Handy, einem iPhone, im Browser oder sogar auf dem Computer. Und das Beste: Du brauchst dafür nur ganz normale Web-Technologien wie HTML, CSS und JavaScript (oder TypeScript).

Und obwohl du eigentlich eine Web-App entwickelst, kannst du trotzdem Funktionen wie Kamera oder Push-Nachrichten nutzen. Möglich ist das mit einem Tool namens Capacitor.

Mit Ionic entwickelst du Apps für:

- Android-Smartphones und Tablets
- iPhones und iPads
- den Browser (als normale Web-App oder Progressive Web App)
- den Desktop

---

## Beginnen wir mit dem Einrichten von Ionic

### 1. Node.js installieren

Bevor du loslegst, brauchst du Node.js, denn Ionic basiert darauf.

1. Lade dir Node.js von der offiziellen Website herunter:  
   → [nodejs.org](https://nodejs.org/)

2. Installiere es ganz normal – nimm am besten die LTS-Version.

3. Teste danach im Terminal, ob alles klappt:

```bash
node -v
npm -v
```

### 2. Ionic CLI installieren

```bash
npm install -g @ionic/cli
```

### 3. Dein erstes Projekt erstellen (mit React)

```bash
ionic start meine-erste-app blank --type=react
```

- `meine-erste-app`: So wird dein Projekt heissen
- `blank`: Eine leere Vorlage
- `--type=react`: Du nutzt React als Framework

Wenn eine frage wie: „Integrate Capacitor?“ steht dann klickst du auf Ja, wenn du native Funktionen nutzen willst.

### 4. Projekt starten

```bash
cd meine-erste-app
ionic serve
```

### 5. Erster Inhalt: „Hallo Welt!“

Bearbeite diese Datei:

```bash
src/pages/Home.tsx
```

```tsx
import {
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar
} from '@ionic/react';

const Home: React.FC = () => (
  <IonPage>
    <IonHeader>
      <IonToolbar>
        <IonTitle>Meine erste Ionic App</IonTitle>
      </IonToolbar>
    </IonHeader>
    <IonContent className="ion-padding">
      <h1>Hallo Welt!</h1>
    </IonContent>
  </IonPage>
);

export default Home;
```

---

## Die Projektstruktur verstehen

### Wichtige Ordner

- `src/` – Hier steckt der komplette Quellcode deiner App
- `src/pages/` – Die einzelnen Seiten deiner App
- `src/components/` – Eigene, wiederverwendbare Komponenten
- `src/assets/` – Bilder, Icons, Fonts usw.
- `src/theme/` – Styling, z.B. Farben oder Schriftarten
- `node_modules/` – Alle installierten Bibliotheken

### Wichtige Dateien

- `ionic.config.json` – Konfiguration für das Ionic-CLI
- `capacitor.config.ts` – Einstellungen für Capacitor (z.B. App-ID, Name)
- `package.json` – Zeigt dir, welche Abhängigkeiten du installiert hast
- `tsconfig.json` – TypeScript-Konfiguration für das Projekt

---

## Native Plattformen hinzufügen

### Android hinzufügen

```bash
ionic capacitor add android
ionic capacitor open android
```

### iOS hinzufügen (nur auf macOS)

```bash
ionic capacitor add ios
ionic capacitor open ios
```

---

## Nützliche Befehle

| Befehl                        | Beschreibung                                        |
|------------------------------|-----------------------------------------------------|
| `ionic serve`                | Starte die App im Browser                           |
| `ionic build`                | Erstelle ein Build der App                          |
| `ionic capacitor sync`       | Synchronisiere Änderungen mit den nativen Projekten |
| `ionic generate page <name>` | Erstelle eine neue Seite                            |
