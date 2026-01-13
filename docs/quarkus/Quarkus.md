# ÜK – Lerndokumentation

In dieser Dokumentation werden die wichtigsten Konzepte und Erweiterungen beschrieben, die im ÜK anhand der **Punchclock-Applikation** umgesetzt wurden.  
Die Applikation basiert auf **Quarkus** und verwendet moderne Backend-Technologien wie **REST-APIs**, **JPA/Hibernate**, **DTOs**, **JWT-Authentifizierung**, **Dependency Injection**, **Datenbankmigrationen** und **Testing**.

**Repository:**  
https://github.com/ralf-boltshauser/punchclock

---

# 1. Projektüberblick

Punchclock ist eine Zeiterfassungs-Applikation, mit der Benutzer Arbeitszeiten erfassen können.  
Ein Zeiteintrag (Entry) besteht unter anderem aus einem Check-in- und Check-out-Zeitpunkt und ist einem Benutzer zugeordnet.

Die Applikation ist als REST-Backend umgesetzt und folgt einer serviceorientierten Architektur.

---

# 2. CRUD-Erweiterungen (Entries)

Die bestehende Punchclock-Applikation wurde um zusätzliche CRUD-Funktionen erweitert.

### Umgesetzte Funktionen

- Erstellen von Entries mit korrekter Employee-Relation
- Bearbeiten von bestehenden Entries
- Löschen von Entries

### Beispiel: Delete-Endpoint

```
@DELETE
@Path("/{id}")
@Transactional
public void deleteEntry(@PathParam("id") Long id) {
    Entry entry = Entry.findById(id);
    if (entry != null) {
        entry.delete();
    }
}
```

**Erklärung:**

- `@DELETE` definiert die HTTP-Methode
- `@Path("/{id}")` liest die ID aus der URL
- `@Transactional` stellt sicher, dass die Datenbankoperation atomar ausgeführt wird

---

# 3. DTOs (Data Transfer Objects)

### Warum DTOs?

Entity-Klassen direkt in der API zu verwenden ist problematisch:

- interne Daten könnten preisgegeben werden
- Fremdschlüssel machen die API unnötig kompliziert
- API-Modell und Datenbank-Modell haben unterschiedliche Anforderungen

**Lösung:**  
DTOs kapseln die Daten, die über die API gesendet oder empfangen werden.

### Beispiel DTO

```
public class EntryDTO {
    public LocalDate date;
    public LocalTime startTime;
    public LocalTime endTime;
    public Long employeeId;
}
```

### Mapping DTO → Entity

```
@Transactional
public Entry createEntry(EntryDTO dto) {
    Entry entry = new Entry();
    entry.date = dto.date;
    entry.startTime = dto.startTime;
    entry.endTime = dto.endTime;
    entry.employee = Employee.findById(dto.employeeId);
    entry.persist();
    return entry;
}
```

---

# 4. Dependency Injection (@Inject)

Dependency Injection (DI) bedeutet, dass Abhängigkeiten nicht manuell erstellt, sondern vom Framework automatisch bereitgestellt werden.

Quarkus verwendet dafür **CDI (Contexts and Dependency Injection)**.

### Beispiel Controller mit Injection

```
@Path("/entries")
public class EntryController {

    @Inject
    EntryService entryService;

    @POST
    public Entry create(EntryDTO dto) {
        return entryService.createEntry(dto);
    }
}
```

### Vorteil von Dependency Injection

- bessere Testbarkeit

---

# 5. Transaktionen (@Transactional)

Die Annotation `@Transactional` fasst mehrere Datenbankoperationen zu einer Transaktion zusammen.

### Zweck

- Alle Änderungen werden gemeinsam gespeichert
- Bei einem Fehler werden alle Änderungen zurückgerollt

### Beispiel

```
@Transactional
public void updateEntry(Long id, EntryDTO dto) {
    Entry entry = Entry.findById(id);
    entry.startTime = dto.startTime;
    entry.endTime = dto.endTime;
}
```

---

# 6. API-Planung

Vor der Implementierung wurden die Endpunkte geplant und dokumentiert.

### Beispiel Endpunkte

| Endpunkt       | Methode  | Beschreibung       |
|----------------|----------| ------------------ |
| `/tasks`       | POST     | Aufgabe erstellen  |
| `/tasks/{id}`  | PUT      | Aufgabe bearbeiten |
| `/tasks/{id}`  | DELETE   | Aufgabe löschen    |


---

# 7. Datenmodell-Erweiterung (Category & Tag)

Das Datenmodell wurde um Kategorien und Tags erweitert.

### Category

- Titel
- One-to-Many Beziehung zu Entry

### Tag

- Titel
- Many-to-Many Beziehung zu Entry

### Beispiel Entity

```
@Entity
public class Category extends PanacheEntity {
    public String title;
}
```

---

# 8. Testdaten

Für eine schnelle Entwicklung werden Testdaten automatisch beim Start der Applikation geladen.

```
@ApplicationScoped
public class DataLoader {

    @Transactional
    void onStart(@Observes StartupEvent event) {
        Category c = new Category();
        c.title = "Work";
        c.persist();
    }
}
```

---

# 9. Statistik-Endpunkt (Time Summary)

Der Endpunkt `GET /statistics/time-summaries` berechnet die gesamte erfasste Zeit pro Tag.

```
@ApplicationScoped
public class TimeSummaryService {

    public Map<LocalDate, Duration> calculateSummaryPerDay(List<Entry> entries) {
        Map<LocalDate, Duration> result = new HashMap<>();
        for (Entry e : entries) {
            Duration d = Duration.between(e.startTime, e.endTime);
            result.merge(e.date, d, Duration::plus);
        }
        return result;
    }
}
```

---

# 10. JWT-Authentifizierung

Die API wurde mit JWT-Authentifizierung abgesichert.

### Funktionen

- Token-Generierung mit RSA-Schlüsselpaar
- Rollenbasierte Zugriffskontrolle

```
@GET
@Path("/secure")
@RolesAllowed("Admin")
public String secureData() {
    return "Protected Data";
}
```

---

# 11. Testing

Es wurden verschiedene Testarten eingesetzt:

- Unit-Tests
- Integrationstests
- End-to-End-Tests

### Beispiel

Ein Entry wird über die API erstellt und anschliessend aus der Datenbank gelesen.

---

# 12. ACID-Eigenschaften

ACID beschreibt vier grundlegende Eigenschaften von Datenbanksystemen.

- **Atomicity** – Eine Transaktion wird entweder vollständig oder gar nicht ausgeführt.
- **Consistency** – Die Datenbank bleibt vor und nach jeder Transaktion gültig.
- **Isolation** – Gleichzeitige Transaktionen beeinflussen sich nicht.
- **Durability** – Gespeicherte Daten bleiben dauerhaft erhalten.

Präzise Erklärung findet man unter diesem Link (vom Ük): https://youtu.be/ZHq5f7eZPwA

---

# 13. Entry-Entität

Die Entry-Entität repräsentiert einen Zeiteintrag in der Datenbank.

Eigenes Diagramm:

<img width="630" height="541" alt="image" src="https://github.com/user-attachments/assets/2b7c3827-1f5d-41e1-b765-5683d07a0347" />

```
@Entity
@Table(name = "entry")
public class Entry {
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
@Column(nullable = false)
private LocalDateTime checkIn;
private LocalDateTime checkOut;
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "employee_id", nullable = false)
private Employee employee;
}
```

# 15. Sequenzdiagramme

In diesem Abschnitt werden die erstellten Sequenzdiagramme dokumentiert, welche die Abläufe und Interaktionen zwischen den Komponenten der Punchclock‑Applikation visualisieren.

<img width="324" height="511" alt="image" src="https://github.com/user-attachments/assets/026fe38b-c684-44f5-ad67-23fb12a0d303" />
<img width="361" height="279" alt="image" src="https://github.com/user-attachments/assets/ebd9deae-d50f-44be-b082-2c9810204194" />
<img width="378" height="343" alt="image" src="https://github.com/user-attachments/assets/d11d7cd3-17a2-4540-bb70-916eec6f57a1" />

# 16. Klassendiagramm

<img width="352" height="343" alt="image" src="https://github.com/user-attachments/assets/f4761696-3382-4967-9d76-d67906a3aa94" />
