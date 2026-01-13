---
title: Testing
sidebar_position: 10
---

# Unit-Testing Guide (NestJS + Jest)

---

# Ziel von Unit-Tests

Ein Unit-Test testet **eine einzelne Klasse (z. B. einen Service)** isoliert.

## ✔️ Was wird getestet

- Business-Logik
- Entscheidungen (`if / else`)
- Daten-Mapping & Transformation
- Aufrufe von Repositories und anderen Services
- Nebenwirkungen (`save`, `delete`, `update`)

## ❌ Was wird **nicht** getestet

- Datenbank
- SQL
- TypeORM intern
- NestJS intern
- HTTP / Controller

> **Merksatz:**  
> Teste **was** dein Code macht – nicht **wie** das Framework funktioniert.

---

# Denkmodell beim Schreiben eines Tests

Bevor du einen Test schreibst, beantworte:

1. Was ist die **Eingabe**?
2. Welche **Abhängigkeiten** werden benutzt?
3. Was muss **nach der Ausführung passiert sein**?
4. Welche **Business-Regeln** gelten?

Wenn du diese Fragen beantworten kannst, weisst du:

- was du mocken musst
- was du im `THEN` prüfen musst

---

# Grundstruktur eines Tests (GIVEN / WHEN / THEN)

Jeder Test folgt diesem Muster:

```ts
it('should ...', async () => {
  // GIVEN – Vorbereitung
  ...

  // WHEN – Aktion
  ...

  // THEN – Erwartung
  ...
});
```

**Bedeutung:**

- **GIVEN:** Mocks, Daten, Setup
- **WHEN:** Methode ausführen
- **THEN:** Verhalten prüfen

---

# Test-Setup mit NestJS (TestingModule)

NestJS nutzt Dependency Injection.  
Damit ein Service getestet werden kann, müssen **alle Constructor-Abhängigkeiten gemockt werden**.

### Beispiel

```ts
beforeEach(async () => {
  const module = await Test.createTestingModule({
    providers: [
      MyService,
      { provide: getRepositoryToken(MyEntity), useValue: myRepoMock },
    ],
  }).compile();

  service = module.get(MyService);
});
```

**Regel:**  
Alles, was im Constructor steht, muss im Test als Provider existieren.

---

# Mocks – Grundlagen

Ein Mock ist ein **kontrollierter Ersatz** für:

- Repositories
- andere Services
- externe Abhängigkeiten

### Beispiel: Repository-Mock

```ts
const repoMock = {
  findOneOrFail: jest.fn(),
  save: jest.fn(),
  delete: jest.fn(),
};
```

---

# Async vs Sync Mocks

| Situation         | Methode               |
| ----------------- | --------------------- |
| async / await     | `mockResolvedValue()` |
| synchron          | `mockReturnValue()`   |
| Fehler simulieren | `mockRejectedValue()` |

### Beispiel

```ts
repo.findOneOrFail.mockResolvedValue(entity);
```

---

# QueryBuilder mocken (TypeORM)

TypeORM nutzt Method-Chaining.  
Jede Methode, die im Service aufgerufen wird, muss im Mock existieren.

### Beispiel

```ts
const qbMock = {
  where: jest.fn().mockReturnThis(),
  andWhere: jest.fn().mockReturnThis(),
  orderBy: jest.fn().mockReturnThis(),
  addOrderBy: jest.fn().mockReturnThis(),
  skip: jest.fn().mockReturnThis(),
  take: jest.fn().mockReturnThis(),
  select: jest.fn().mockReturnThis(),
  addSelect: jest.fn().mockReturnThis(),
  getMany: jest.fn(),
  getCount: jest.fn(),
  getRawOne: jest.fn(),
};

repo.createQueryBuilder.mockReturnValue(qbMock);
```

---

# Assertions – Grundlagen

### Wurde etwas aufgerufen?

```ts
expect(repo.save).toHaveBeenCalled();
```

### Wie oft?

```ts
expect(repo.save).toHaveBeenCalledTimes(1);
```

---

# Robustes Testen

### Strikt (nicht empfohlen)

```ts
expect(repo.save).toHaveBeenCalledWith({ name: "Test" });
```

### Robust (Best Practice)

```ts
expect(repo.save).toHaveBeenCalledWith(
  expect.objectContaining({ name: "Test" }),
);
```

---

# Arrays & gespeicherte Daten prüfen

Zugriff auf gespeicherte Argumente:

```ts
const savedData = repo.save.mock.calls[0][0];
```

- `calls[0]` → erster Aufruf
- `calls[0][0]` → erstes Argument

---

# Private Methoden testen

Falls nötig:

```ts
await (service as any).privateMethod();
```

---

# Typische Testfälle – Checkliste

- Werden Daten geladen?
- Werden Daten gespeichert?
- Werden Daten gelöscht?
- Werden andere Services aufgerufen?
- Werden Felder gemappt oder verändert?
- Gibt es Sonderfälle (`null`, `undefined`)?

---

# Häufige Fehler & Lösungen

### ❌ `... is not a function`

→ Methode fehlt im Mock

### ❌ `toHaveBeenCalledWith` schlägt fehl

→ `objectContaining` / `arrayContaining` verwenden

### ❌ Promise hängt

→ `mockResolvedValue` vergessen

### ❌ Expected `1`, Received `[ ... ]`

→ `save` bekommt ein Objekt oder Array, nicht eine Zahl

---

# Gute Test-Namen

❌ schlecht:

```ts
it("test1");
```

✅ gut:

```ts
it("should save entity with default values");
```

---

# Golden Rules für Tests

1. Ein Test = ein Verhalten
2. Mocks so einfach wie möglich
3. Business-Logik testen, nicht Framework
4. Robust testen statt zu strikt
