# Backlog Sprintu 0 — Fundamenty projektu

**Epik:** Fundamenty projektu ([REV-5](https://revvvit.atlassian.net/browse/REV-5))
**W Jirze:** ticket #n z tego pliku = `REV-(n+5)`, np. #1 = REV-6, #28 = REV-33. Źródłem prawdy jest teraz Jira.
**Cel sprintu:** front pobiera `/health` z API i wyświetla status, oba repozytoria mają zielony pipeline CI.

Priorytety:
- **Must** — potrzebne do celu sprintu
- **Should** — ważne, ale sprint może się bez tego zakończyć
- **Could** — jeśli starczy czasu, inaczej przechodzi do kolejnego sprintu

Estymacja: do ustalenia na refinemencie (planning poker z Claude'em).
Ten zakres prawdopodobnie przekracza jeden sprint — podział ustalamy na planowaniu.

---

## Organizacja i narzędzia

### 1. Weryfikacja integracji Jiry z GitHubem
- **Typ:** Task · **Etykieta:** DevOps · **Priorytet:** Must
- **Opis:** Integracja GitHub for Atlassian, serwer Atlassian MCP i skill `jira-ticket` są już
  skonfigurowane. Zostało sprawdzić całość na pierwszej gałęzi, commicie i PR-ze.
- **Kryteria akceptacji:**
  - Gałąź, commit i PR z numerem `REV-x` pojawiają się w panelu Development ticketu w Jirze
  - (zrobione) Claude Code pobiera ticket po numerze i potrafi utworzyć nowy
  - (zrobione) Skill `jira-ticket` zainstalowany w `~/.claude/skills/`

### 2. Dokumenty produktowe
- **Typ:** Task · **Etykieta:** Docs · **Priorytet:** Should
- **Opis:** Utworzyć `docs/product/vision.md`, `roadmap.md` i `priorities.md`
  (zasady priorytetyzacji + macierz ważności błędów) na podstawie `docs/project-brief.md`.
- **Kryteria akceptacji:**
  - Trzy dokumenty istnieją i są spójne z opisem projektu
    (`vision.md` i `roadmap.md` — zrobione na warsztacie z PO; zostało `priorities.md`)
  - Macierz ważności błędów definiuje co najmniej poziomy: krytyczny, wysoki, średni, niski

## revvv-api

### 3. Szkielet aplikacji NestJS
- **Typ:** Task · **Etykieta:** BE · **Priorytet:** Must
- **Opis:** Wygenerować projekt NestJS (TypeScript strict, pnpm) w repo `revvv-api`,
  nie nadpisując istniejących `CLAUDE.md` i `docs/`.
- **Kryteria akceptacji:**
  - `pnpm dev` uruchamia API lokalnie
  - TypeScript w trybie strict
  - pnpm skonfigurowany z `minimumReleaseAge` = 3 dni (nowe wersje paczek instalują się
    najwcześniej 3 dni po publikacji)
  - Sekcja „Polecenia” w CLAUDE.md uzupełniona
  - `.gitattributes` wymusza końce linii LF (`* text=auto eol=lf`)

### 4. Lokalne środowisko w docker-compose
- **Typ:** Task · **Etykieta:** DevOps · **Priorytet:** Must
- **Opis:** docker-compose z PostgreSQL (z PostGIS), Redisem, MinIO i Mailpitem.
- **Kryteria akceptacji:**
  - Jedno polecenie uruchamia wszystkie usługi
  - Plik `.env.example` opisuje wszystkie potrzebne zmienne (bez prawdziwych sekretów)
  - Instrukcja uruchomienia w README

### 5. Konfiguracja Drizzle, pierwsza migracja i szkielet seeda
- **Typ:** Task · **Etykieta:** BE · **Priorytet:** Must
- **Opis:** Podłączyć Drizzle do Postgresa, przygotować migrację włączającą rozszerzenie PostGIS
  i szkielet seeda. Polecenie `db:reset` czyści bazę, uruchamia migracje i ładuje seed.
- **Kryteria akceptacji:**
  - `pnpm db:reset` działa na świeżej bazie
  - Rozszerzenie PostGIS jest aktywne
  - Seed używa Fakera ze stałym ziarnem (powtarzalne dane)

### 6. Walidacja zmiennych środowiskowych przy starcie
- **Typ:** Task · **Etykieta:** BE · **Priorytet:** Should
- **Opis:** Aplikacja przy starcie sprawdza obecność i poprawność zmiennych środowiskowych
  i zatrzymuje się z czytelnym komunikatem, jeśli czegoś brakuje.
- **Kryteria akceptacji:**
  - Brak wymaganej zmiennej zatrzymuje start z komunikatem wskazującym, której brakuje
  - Test jednostkowy walidacji

### 7. Endpoint `/health`
- **Typ:** Story · **Etykieta:** BE · **Priorytet:** Must
- **Opis:** Jako operator chcę sprawdzić, czy API i jego zależności działają, żeby hosting
  i monitoring mogły wykryć awarię.
- **Kryteria akceptacji:**
  - `GET /health` zwraca status API, bazy danych i Redisa
  - Gdy baza jest niedostępna, endpoint zwraca błąd z informacją, która zależność nie działa
  - Endpoint opisany w OpenAPI
  - Test integracyjny

### 8. Jednolity format błędów (Problem Details)
- **Typ:** Task · **Etykieta:** BE · **Priorytet:** Must
- **Opis:** Globalny filtr wyjątków zwracający każdy błąd w formacie Problem Details (RFC 9457)
  z polem `code`. Błędy walidacji zawierają listę pól z kodami.
- **Kryteria akceptacji:**
  - Każdy błąd (w tym nieobsłużony) ma ten sam format
  - Błąd 500 nie ujawnia stack trace ani szczegółów wewnętrznych
  - Katalog kodów błędów w jednym miejscu
  - Testy jednostkowe filtra

### 9. Logi strukturalne z identyfikatorem żądania
- **Typ:** Task · **Etykieta:** BE · **Priorytet:** Should
- **Opis:** Logi w formacie JSON (pino). Każde żądanie dostaje identyfikator widoczny
  w logach i w odpowiedzi błędu.
- **Kryteria akceptacji:**
  - Każdy wpis logu zawiera identyfikator żądania
  - Odpowiedź błędu zawiera ten sam identyfikator
  - W logach nie ma haseł, tokenów ani danych osobowych

### 10. Specyfikacja OpenAPI i Swagger
- **Typ:** Task · **Etykieta:** BE · **Priorytet:** Must
- **Opis:** Generowanie specyfikacji OpenAPI z kodu, Swagger UI lokalnie, eksport `openapi.json`
  do użycia przez frontend.
- **Kryteria akceptacji:**
  - Swagger UI dostępny lokalnie
  - `openapi.json` generowany poleceniem i w CI
  - Format błędów (Problem Details) opisany w specyfikacji

### 11. Pipeline CI dla API
- **Typ:** Task · **Etykieta:** DevOps · **Priorytet:** Must
- **Opis:** GitHub Actions: lint, typecheck, testy, build obrazu Dockera i publikacja do
  `ghcr.io/revvvit/revvv-api` po merge'u do `main`.
- **Kryteria akceptacji:**
  - Pipeline uruchamia się dla każdego PR-a
  - Obraz Dockera trafia do GHCR po merge'u do `main`
  - Akcje przypięte do konkretnych wersji (SHA)

## revvv-web

### 12. Szkielet aplikacji Next.js z motywem
- **Typ:** Task · **Etykieta:** FE · **Priorytet:** Must
- **Opis:** Projekt Next.js (App Router, TypeScript strict, pnpm), Tailwind, shadcn/ui
  i motyw na tokenach z trybem jasnym i ciemnym (paleta robocza z project-brief).
- **Kryteria akceptacji:**
  - `pnpm dev` uruchamia aplikację
  - Przełączanie trybu jasny/ciemny działa
  - Kolory wyłącznie przez tokeny motywu
  - pnpm skonfigurowany z `minimumReleaseAge` = 3 dni
  - `.gitattributes` wymusza końce linii LF (`* text=auto eol=lf`)

### 13. Tłumaczenia (next-intl)
- **Typ:** Task · **Etykieta:** FE · **Priorytet:** Must
- **Opis:** Konfiguracja next-intl z PL jako językiem domyślnym i strukturą gotową na EN.
- **Kryteria akceptacji:**
  - Wszystkie teksty pochodzą z plików tłumaczeń
  - Dodanie EN wymaga tylko nowego pliku tłumaczeń

### 14. Klient API z OpenAPI (Orval)
- **Typ:** Task · **Etykieta:** FE · **Priorytet:** Must
- **Opis:** Generowanie typowanego klienta i hooków TanStack Query z `openapi.json` backendu.
- **Kryteria akceptacji:**
  - Jedno polecenie generuje klienta
  - Wygenerowany hook do `/health` jest gotowy do użycia

### 15. Obsługa błędów i toasty
- **Typ:** Task · **Etykieta:** FE · **Priorytet:** Must
- **Opis:** Mapowanie kodów błędów z API na komunikaty (next-intl), toasty (Sonner),
  globalna obsługa w TanStack Query, error boundary.
- **Kryteria akceptacji:**
  - Błąd API wyświetla przetłumaczony komunikat w toaście
  - Nieznany kod błędu wyświetla komunikat ogólny
  - Awaria komponentu pokazuje widok błędu zamiast białej strony

### 16. Strona startowa ze statusem API
- **Typ:** Story · **Etykieta:** FE · **Priorytet:** Must
- **Opis:** Jako developer chcę zobaczyć na stronie głównej status API, żeby potwierdzić,
  że front i backend są poprawnie połączone.
- **Kryteria akceptacji:**
  - Strona pokazuje status API pobrany z `/health`
  - Gdy API nie działa, wyświetla się komunikat błędu (toast)
  - Działa w trybie jasnym i ciemnym

### 17. Storybook
- **Typ:** Task · **Etykieta:** FE · **Priorytet:** Should
- **Opis:** Konfiguracja Storybooka z motywem (jasny/ciemny) i pierwszym komponentem.
- **Kryteria akceptacji:**
  - Storybook uruchamia się lokalnie
  - Przełącznik trybu jasny/ciemny w Storybooku

### 18. Pierwszy test E2E (Playwright)
- **Typ:** Task · **Etykieta:** QA · **Priorytet:** Should
- **Opis:** Konfiguracja Playwrighta i test smoke: strona główna ładuje się i pokazuje status API.
- **Kryteria akceptacji:**
  - Test przechodzi lokalnie przy uruchomionym API
  - Instrukcja uruchomienia w README

### 19. Pipeline CI dla frontu
- **Typ:** Task · **Etykieta:** DevOps · **Priorytet:** Must
- **Opis:** GitHub Actions: lint, typecheck, testy jednostkowe i build dla każdego PR-a.
- **Kryteria akceptacji:**
  - Pipeline uruchamia się dla każdego PR-a
  - Akcje przypięte do konkretnych wersji (SHA)

## Oba repozytoria

### 20. Jakość kodu: ESLint, Prettier, Lefthook, commitlint
- **Typ:** Task · **Etykieta:** DevOps · **Priorytet:** Must
- **Opis:** Wspólne zasady formatowania i lintowania, hooki przed commitem, walidacja
  wiadomości commitów (Conventional Commits z numerem ticketu).
- **Kryteria akceptacji:**
  - Commit z błędem formatowania albo złą wiadomością jest blokowany lokalnie
  - Te same sprawdzenia działają w CI

### 21. Ochrona gałęzi `main`
- **Typ:** Task · **Etykieta:** DevOps · **Priorytet:** Should
- **Opis:** W obu repo: merge do `main` tylko przez PR, wymagane zielone checki
  (bez wymogu akceptacji — praca solo).
- **Kryteria akceptacji:**
  - Bezpośredni push do `main` jest zablokowany
  - PR z czerwonym pipeline nie może zostać zmergowany

### 22. Szablon PR i Definition of Done
- **Typ:** Task · **Etykieta:** Docs · **Priorytet:** Should
- **Opis:** Szablon PR w obu repo z checklistą Definition of Done z CLAUDE.md.
- **Kryteria akceptacji:**
  - Nowy PR automatycznie zawiera szablon

### 23. SonarQube Cloud
- **Typ:** Task · **Etykieta:** DevOps · **Priorytet:** Could
- **Opis:** Analiza jakości i pokrycia testami w obu repo, quality gate w PR-ach.
- **Kryteria akceptacji:**
  - Wynik analizy widoczny w każdym PR-ze

### 24. Zarządzanie sekretami (Doppler lub Infisical)
- **Typ:** Task · **Etykieta:** DevOps · **Priorytet:** Could
- **Opis:** Wybór narzędzia (ADR), konfiguracja środowisk local i INT, podpięcie pod CI.
- **Kryteria akceptacji:**
  - Zmienne środowiskowe pobierane z narzędzia lokalnie i w CI
  - Brak plików `.env` z prawdziwymi wartościami w repo

### 25. Renovate
- **Typ:** Task · **Etykieta:** DevOps · **Priorytet:** Could
- **Opis:** Automatyczne PR-y z aktualizacjami zależności w obu repo.
- **Kryteria akceptacji:**
  - Renovate tworzy PR-y z aktualizacjami, grupując drobne zmiany
  - `minimumReleaseAge: "3 days"` dla aktualizacji wersji
  - Poprawki bezpieczeństwa bez okresu karencji (natychmiast)
  - Ustawienia pnpm i Renovate są spójne (ten sam okres karencji)

### 26. Środowisko INT
- **Typ:** Task · **Etykieta:** DevOps · **Priorytet:** Could
- **Opis:** Potwierdzenie wyboru hostingu (ADR) i automatyczny deploy obu aplikacji na INT
  po merge'u do `main`, z osobną bazą i migracjami w pipeline.
- **Kryteria akceptacji:**
  - Merge do `main` wdraża aplikację na INT
  - Strona startowa na INT pokazuje status API z INT

## Claude Code

### 27. Konfiguracja Claude Code (agenci, hooki, start-ticket)
- **Typ:** Task · **Etykieta:** DevOps · **Priorytet:** Should
- **Opis:** Minimalna konfiguracja pracy z Claude Code: subagenci Product Owner i Tech Lead
  (`~/.claude/agents/`), skill `start-ticket` (pobranie ticketu z Jiry, gałąź, plan, status In Progress)
  oraz hooki w obu repo: formatowanie po edycji i blokada edycji plików `.env*`.
  Kolejne skille dodajemy, gdy coś zrobimy ręcznie 2–3 razy.
- **Kryteria akceptacji:**
  - Agenci PO i Tech Lead dostępni we wszystkich sesjach, z opisem roli i ograniczonymi narzędziami
  - `/start-ticket REV-x` tworzy gałąź `REV-x-krotki-opis` i przesuwa ticket do In Progress
  - Edycja `.env*` przez Claude'a jest blokowana, edytowane pliki są formatowane automatycznie
  - Hooki zapisane w `.claude/settings.json` w repo (wersjonowane)

## Środowiska

### 28. Środowisko PRD i pierwszy release
- **Typ:** Task · **Etykieta:** DevOps · **Priorytet:** Could
- **Opis:** Środowisko produkcyjne obu aplikacji i pierwszy release przez promocję wersji z INT
  (ten sam obraz Dockera API). Bez PRD żaden ticket nie może przejść do Done. Zależy od #26 (INT).
- **Kryteria akceptacji:**
  - Hosting PRD opisany w tym samym ADR co INT, osobna baza danych
  - Wdrożenie na PRD wymaga ręcznego zatwierdzenia w GitHub Actions (environments)
  - Pierwsza wersja oznaczona tagiem (np. `v0.1.0`) i wdrożona przed końcem sprintu
  - Smoke test: strona startowa na PRD pokazuje status API z PRD
