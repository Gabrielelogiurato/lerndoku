---
title: Infosec Einsatz
sidebar_position: 12
---

# Infosec Einsatz

In dieser Lerndoku beschreibe ich meinen Einsatz in Infosec. 
Der Einsatz dauerte ungefähr **20–25 Arbeitstage**. In dieser Zeit habe ich Security‑Grundlagen gelernt, verschiedene Tools ausprobiert und eine reale Applikation sicherheitstechnisch analysiert.

---

## 1. Überblick

Das Ziel des Einsatzes war es, einen Einblick in die Arbeit des Infosec‑Teams zu erhalten.  
Dabei ging es um folgende Themen:

- Security‑Grundlagen
- SOC und AppSec
- TryHackMe‑Lernpfade
- Security‑Tools
- Analyse einer realen Applikation
- Erstellung eines Security‑Reports

---

## 2. Infosec Team

Zu Beginn habe ich das Infosec‑Team kennengelernt.  
Ich hatte ein Meeting mit Marko und eins mit Lars über SOC und AppSec

### SOC

Das **Security Operations Center (SOC)** überwacht Systeme und analysiert Security Alerts.

Typische Aufgaben:

- Logs prüfen
- Alerts analysieren
- Incidents untersuchen
- False Positives erkennen
- Massnahmen einleiten

### AppSec

**Application Security (AppSec)** beschäftigt sich mit der Sicherheit von Applikationen.

Typische Aufgaben:

- Code Reviews
- Dependency Checks
- SAST und DAST
- Security Findings dokumentieren
- Entwickler beraten

---

## 3. Kennengelernte Tools

Während des Einsatzes habe ich verschiedene Security‑Tools verwendet.

| Tool | Zweck |
|------|-------|
| Tenable | Vulnerability Management |
| Microsoft Defender | Endpoint Security |
| Microsoft Sentinel | SIEM / Log‑Analyse |
| TheHive | Incident Response |
| Burp Suite / ZAP | Web Application Testing |
| Semgrep | Static Code Analysis |
| npm audit | Dependency Check |
| Trivy | Container‑ und Dependency‑Scanning |

---

## 4. TryHackMe Lernpfade

Ein grosser Teil des Einsatzes bestand aus TryHackMe‑Kursen.

Bearbeitete Lernpfade:

- Introduction to Cybersecurity
- Pre Security
- Web Fundamentals
- SOC Level 1

Gelernt habe ich unter anderem:

- Netzwerke
- HTTP / HTTPS
- Linux & Windows Grundlagen
- Web Security
- OWASP Top 10
- XSS
- SQL Injection
- Phishing Analyse
- Log Analyse
- Incident Response

---

## 5. Wichtige Security‑Begriffe

### SAST

**Static Application Security Testing**  
→ Quellcode wird analysiert, ohne die Applikation auszuführen.

Beispiele:

- Semgrep
- SpotBugs
- ESLint Security Plugins

### DAST

**Dynamic Application Security Testing**  
→ Eine laufende Applikation wird getestet.

Beispiele:

- Burp Suite
- OWASP ZAP

### SCA

**Software Composition Analysis**  
→ Dependencies werden auf bekannte Schwachstellen geprüft.

Beispiele:

- npm audit
- Dependency‑Check
- Trivy

---

## 6. Security Assessment: NetGrade

Am Ende des Einsatzes habe ich die Applikation **NetGrade** analysiert.

**Repository:**  
https://github.com/kevin-nca/netgrade

NetGrade ist eine Notenverwaltungs‑App basierend auf React, Ionic, Capacitor und TypeScript.

Wichtige Erkenntnisse:

- Kein klassisches Backend
- Keine REST‑API
- Daten werden lokal gespeichert
- Risiken: Dependencies, lokale Daten, Import/Export‑Funktionen

---

## 7. Durchgeführte Testcases

### Testcase 1: Dependency Check

Mit `npm audit` wurden Dependencies geprüft.

**Resultat:**

- mehrere verwundbare Pakete (z.B. minimatch, rollup, tar, qs, xlsx …)
- Test: **FAILED**

**Empfehlung:**

- `npm audit fix`
- kritische Pakete manuell aktualisieren

---

### Testcase 2: SAST

Der Code wurde mit statischen Analyse‑Tools geprüft.

**Resultat:**

- keine kritischen Findings
- Test: **PASSED**

---

### Testcase 3: Semgrep

Semgrep wurde mit mehreren Regelsets ausgeführt:

```bash
semgrep ci
semgrep --config "p/typescript"
semgrep --config "p/react"
semgrep --config "p/xss"
semgrep --config p/security-audi
```

**Resultat:**

- keine blockierenden Findings  
- Test: **PASSED**

---

### Testcase 4: Using AI Agent

Es wurde ein Prompt bereitgestellt, der die Applikation testet. Dieser wurde mit drei verschiedenen KIs getestet:

- Claude
- Cursor
- copilot

---

### Testcase 5: Burp Suite / ZAP

Die Applikation wurde dynamisch getestet.

Geprüft wurden u.a.:

- DOM XSS  
- Client‑side Prototype Pollution  
- DOM Clobbering  

**Resultat:**

- keine Schwachstellen gefunden  
- Test: **PASSED**

---

## 8. Wichtigstes Finding

Das wichtigste Finding waren **verwundbare Dependencies**.

**Risiken:**

- Denial of Service  
- Prototype Pollution  
- Path Traversal  
- unerwartetes Verhalten  

**Empfehlungen:**

- Dependencies aktualisieren  
- `npm audit fix`  
- kritische Pakete manuell prüfen  
- regelmässige Scans einrichten  
- Security Checks in CI/CD integrieren  

---

## 9. Weitere Beobachtung: JSON Import

NetGrade besitzt eine JSON‑Importfunktion.  
Importierte Daten sollten stärker validiert werden.

**Mögliche Risiken:**

- ungültige Daten werden gespeichert  
- bestehende Daten werden überschrieben  
- grosse Dateien verursachen Performance‑Probleme  

**Empfehlungen:**

- striktes JSON‑Schema  
- maximale Dateigrösse definieren  
- unbekannte Felder entfernen  
- Daten vor dem Speichern validieren  

---

## 10. Persönliche Learnings

Während des Einsatzes habe ich gelernt, dass Security nicht nur aus Tools besteht.  
Wichtig ist vor allem, die Resultate richtig zu interpretieren.

Meine wichtigsten Learnings:

- Tool‑Findings sind nicht automatisch echte Findings  
- CVSS hilft bei der Priorisierung  
- SAST, DAST und SCA ergänzen sich  
- Client‑only Apps haben andere Risiken als Backend‑Apps  
- Dokumentation ist wichtig  
- Security ist ein kontinuierlicher Prozess
