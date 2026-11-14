# ADR-0001: Stack technologiczny i architektura

- **Status:** zaakceptowany
- **Data:** 2026-11-05

## Kontekst
revvv.it to agregator ofert wypożyczalni aut sportowych i premium. Projekt ma dwa cele:
zbudować produkt, który mógłby działać komercyjnie, oraz nauczyć właściciela (frontend developera)
backendu, baz danych, CI/CD i bezpieczeństwa w realistycznym procesie zespołowym.

Wymagania, które wpływają na architekturę: SEO stron publicznych, zapytania geograficzne (mapa,
geolokalizacja), relacyjne dane z transakcjami, płatności subskrypcyjne, zadania w tle (maile,
przetwarzanie zdjęć), powiadomienia w czasie rzeczywistym.

## Decyzje

### 1. Dwa repozytoria połączone kontraktem OpenAPI
`revvv-api` i `revvv-web` z osobnymi pipeline'ami i wdrożeniami.
- **Dlaczego:** backend ma być samodzielną usługą z własnym cyklem życia — to lepiej uczy
  pracy backendowej. Kontrakt OpenAPI + generowany klient zastępuje współdzielone typy.
- **Alternatywa:** monorepo (pnpm + Turborepo) ze współdzielonymi schematami Zod.
  Odrzucone, bo zaciera granicę między frontem a backendem.
- **Koszt:** zmiany przekrojowe wymagają dwóch PR-ów; trzeba pilnować kompatybilności API.

### 2. Frontend: Next.js (App Router)
- **Dlaczego:** SSR stron publicznych pod SEO; reszta wiedzy z Reacta przenosi się bez zmian.
- **Alternatywa:** React + Vite (SPA). Odrzucone przez słabe SEO stron ofert.

### 3. Backend: NestJS
- **Dlaczego:** narzucona struktura (moduły, DI, guardy) pomaga osobie uczącej się backendu,
  popularny na polskim rynku, wbudowane wsparcie OpenAPI i WebSockets.
- **Alternatywa:** Fastify / Express. Mniej narzutu, ale architekturę trzeba wymyślać samemu.

### 4. Baza: PostgreSQL + PostGIS, ORM: Drizzle
- **Dlaczego Postgres:** dane relacyjne, transakcje, exclusion constraints (przyszłe rezerwacje),
  PostGIS do mapy i geolokalizacji, JSONB, wyszukiwanie pełnotekstowe.
- **Dlaczego Drizzle:** bliżej SQL-a (cel nauki), typy geometryczne dla PostGIS.
- **Alternatywa:** Prisma. Słabsze wsparcie PostGIS (zapytania geograficzne w surowym SQL-u).

### 5. Redis + BullMQ
Kolejki zadań w tle (maile, zdjęcia, powiadomienia) i cache.

### 6. Jednolity format błędów
Problem Details (RFC 9457) z kodem błędu. Front tłumaczy kody na komunikaty (i18n zostaje na froncie).

### 7. Środowiska: local → INT → PRD
INT wdrażany automatycznie z `main`, PRD przez promocję przetestowanej wersji na koniec sprintu.
Osobne bazy dla każdego środowiska; wspólne dane zapewnia wersjonowany seed.

## Konsekwencje
- Trzeba utrzymywać aktualną specyfikację OpenAPI i regenerować klienta na froncie.
- Nauka NestJS, Drizzle i PostGIS naraz to stroma krzywa — rekompensuje ją tryb Learning w Claude Code.
- Hosting na darmowych planach (wstępnie Vercel, Render, Neon) ma ograniczenia (usypianie API,
  limity bazy), akceptowalne dla INT. Decyzja o hostingu zostanie potwierdzona osobnym ADR-em.
