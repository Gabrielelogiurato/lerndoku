---
title: XLSX
sidebar_position: 11
---

# XLSX in Netgrade

## Was ist XLSX überhaupt

XLSX ist ein Dateiformat für Tabellenkalkulationen, das von Microsoft verwendet wird.

## Verwendung in NetGrade

In NetGrade verwenden wir XLSX für den Export unserer Schuldaten. Die Bibliothek xlsx ermöglicht es uns, Daten in Excel-Format zu konvertieren und zu speichern.

Ein Beispiel in unserem Code

```
const workbook = XLSX.utils.book_new();

const schoolData = [
  ['School Information'],
  ['Name', data.school.name],
  ['Address', data.school.address],
];

const schoolSheet = XLSX.utils.aoa_to_sheet(schoolData);

XLSX.utils.book_append_sheet(workbook, schoolSheet, 'School');

const xlsxData = XLSX.write(workbook, { type: 'base64', bookType: 'xlsx' });
```

## Wie verwenden wir es in Netgrade

Wir verwenden es um verschiedene Sachen zu Exportieren wie zb:

- Schulinfos
- Fächer und Lehrer (Lehrer noch nicht)
- Prüfungen und Noten
- Und Zusammenfassungen (Kommentare)
