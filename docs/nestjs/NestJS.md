---
title: NestJs
sidebar_position: 5
---

# NestJs

## 1) Was ist NestJS und warum nutzt man es?

NestJS ist ein **Node.js Framework**, das stark von Angular inspiriert ist:

### Grundidee der Architektur

- **Controller** → nehmen HTTP‑Requests an, liefern Responses
- **Services/Provider** → enthalten Business‑Logik, werden per DI injiziert
- **Module** → bündeln zusammengehörige Features

---

## 2) Module – das Organisations-Backbone

Ein Module ist eine Klasse mit `@Module()`‑Decorator.  
Es definiert:

- **controllers** → HTTP‑Endpoints
- **providers** → Services, Repositories, Helper
- **imports** → andere Module
- **exports** → Provider, die andere Module nutzen dürfen

### Beispiel

```ts
import { Module } from "@nestjs/common";
import { CatsController } from "./cats.controller";
import { CatsService } from "./cats.service";

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class CatsModule {}
```

---

## 3) Controller – Requests annehmen & Antworten liefern

Controller sind für **Input/Output** zuständig:

- Routing
- Validierung/Parsing

### Beispiel

```ts
import { Controller, Get, Post, Body } from "@nestjs/common";
import { CatsService } from "./cats.service";
import { CreateCatDto } from "./dto/create-cat.dto";

@Controller("cats")
export class CatsController {
  constructor(private readonly catsService: CatsService) {}

  @Get()
  findAll() {
    return this.catsService.findAll();
  }

  @Post()
  create(@Body() dto: CreateCatDto) {
    return this.catsService.create(dto);
  }
}
```

### Wichtige Decorators

- `@Controller('prefix')`
- `@Get()`, `@Post()`, `@Patch()`, `@Delete()`
- `@Param()`, `@Query()`, `@Body()`, `@Headers()`, `@Req()`, `@Res()`

---

## 4) Provider & Services – Business-Logik + Dependency Injection

Provider sind DI‑fähige Klassen.  
Services sind die häufigste Provider‑Art.

### Beispiel

```ts
import { Injectable } from "@nestjs/common";

@Injectable()
export class CatsService {
  private readonly cats = [];

  create(cat) {
    this.cats.push(cat);
    return cat;
  }

  findAll() {
    return this.cats;
  }
}
```

### Warum Provider wichtig sind

- testbar (Mocks)
- wiederverwendbar

---

## 5) DTOs – Datenform fürs API-Interface

DTOs definieren die Struktur von Daten, die in die API rein/raus gehen.

```ts
export class CreateCatDto {
  name: string;
  age: number;
}
```

---

## 6) Validation – Global aktivieren (main.ts)

```ts
import { ValidationPipe } from "@nestjs/common";

app.useGlobalPipes(
  new ValidationPipe({
    whitelist: true,
    forbidNonWhitelisted: true,
    transform: true,
  }),
);
```

**Pipes** validieren & transformieren Daten, bevor der Controller läuft.

---

## 7) Exception Handling – Exceptions & Filters

Typische Exceptions:

```ts
throw new BadRequestException();
throw new NotFoundException();
```

**Exception Filters** fangen Fehler ab und formen die Response.

Use-Cases:

- einheitliches Error‑Format
- Logging/Telemetry zentralisieren

---

## 8) Middleware – läuft vor dem Routing

Middleware sitzt früh in der Pipeline.

### Beispiel

```ts
import { Injectable, NestMiddleware } from "@nestjs/common";

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req, res, next) {
    console.log(req.method, req.url);
    next();
  }
}
```

Aktivierung:

```ts
import { Module, NestModule, MiddlewareConsumer } from "@nestjs/common";

@Module({})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer.apply(LoggerMiddleware).forRoutes("cats");
  }
}
```

Use-Cases:

- Request‑Logging
- Header‑Checks
- Express‑Middleware integrieren

---

## 9) Guards – Auth & Authorization

Guards entscheiden, ob ein Request **in den Handler darf**.

Use-Cases:

- JWT Auth
- Rollen (Admin/User)
- Feature Flags

---

## 10) Datenbank & Entities (TypeORM / Prisma)

## TypeORM

Entities definieren Tabellen:

```ts
@Entity()
export class Cat {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;
}
```

Repository‑Injection:

```ts
@InjectRepository(Cat)
```

---

## 11) CLI & Resource Generator

Nest CLI kann automatisch generieren:

```
nest -h
```

<img width="921" height="832" alt="image" src="https://github.com/user-attachments/assets/3071f0b4-f72c-42e6-ab88-6a73c8a0dd5c" />
