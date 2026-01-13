---
title: Blob
sidebar_position: 3
---

# Was ist ein Blob

Ein Blob (Binary Large Object) ist ein Datentyp, der binäre Daten wie Dateien, Bilder oder andere Medien speichert. In JavaScript repräsentiert ein Blob-Objekt unveränderliche, rohe Daten.

## Verwendung in NetGrade

In Netgrade verwenden wir Blobs für den Export von Daten in verschiedenen Formaten wie zb XLSX,CSV,JSON. Dann können wir die Daten direkt im BRowser herunterladen.

Hier ein Beispiel Code:

```
if (options.format === 'xlsx') {
  const base64Data = content;
  const binaryString = atob(base64Data);
  const bytes = new Uint8Array(binaryString.length);
  for (let i = 0; i < binaryString.length; i++) {
    bytes[i] = binaryString.charCodeAt(i);
  }
  return new Blob([bytes], {
    type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
  });
} else {
  return new Blob([content], {
    type: options.format === 'json' ? 'application/json' : 'text/csv',
  });
}
```

## Anwendungsfall in Netgrade

Wir verwenden Blobs für:

- Export von Schuldaten in verschiedenen Formaten
- Direkten Download im Browser
- Keine temporäre Dateispeicherung nötig

## Beispiel für Download

```
const blob = new Blob([data], { type: 'application/json' });

const url = window.URL.createObjectURL(blob);

const a = document.createElement('a');
a.href = url;
a.download = `netgrade-export-${new Date().toISOString()}.${format}`;
document.body.appendChild(a);
a.click();

document.body.removeChild(a);
window.URL.revokeObjectURL(url);

```

Es hat auch SIcherheitsaspekte wie Z.B:

- Keine direkte Dateisystem-Zugriffe
- Kontrollierte Download-Umgebung
