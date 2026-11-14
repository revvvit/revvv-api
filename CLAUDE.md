# revvv-api

Backend platformy **revvv.it** — agregatora ofert wypożyczalni aut sportowych i premium (od 200 KM).
Pełny kontekst projektu i wszystkie decyzje: `docs/project-brief.md`. Przeczytaj go na początku
pracy nad każdym nowym zadaniem, jeśli nie masz go jeszcze w kontekście.

Frontend jest w osobnym repo: `revvv-web` (zwykle obok, w `../revvv-web`).

## Najważniejsze: to projekt do nauki backendu

Właściciel projektu jest frontend developerem i przez ten projekt uczy się backendu.
W tym repo:
- **Najpierw wyjaśnij, potem implementuj.** Przed kodem opisz podejście, alternatywy
  i powód wyboru. Zadawaj pytania, które pomagają zrozumieć (np. „jak myślisz, gdzie
  powinna być ta walidacja?”).
- **Kluczowe fragmenty pisze właściciel.** Logikę domenową, zapytania SQL, uprawnienia
  i obsługę błędów zostaw do napisania (oznacz miejsca `TODO(human)` z krótką wskazówką),
  chyba że wyraźnie poprosi, żebyś zrobił to sam. Boilerplate, konfiguracje i powtarzalny
  kod możesz pisać samodzielnie.
- **Rób code review jak senior:** wskazuj błędy, ryzyka bezpieczeństwa i lepsze wzorce,
  wyjaśniając dlaczego.
- Rozmawiamy po polsku.

## Środowisko pracy
- Właściciel pracuje na **Windowsie w PowerShellu** — polecenia do uruchomienia podawaj w składni PowerShella.
- `docker compose` przez Docker Desktop (WSL2); konfiguracja może wymagać sprawdzenia przy pierwszym użyciu.
- Końce linii **LF** (`.gitattributes`). Skrypty w `package.json` muszą działać w PowerShellu i w CI (Linux).

## Stack
NestJS + TypeScript (strict), PostgreSQL + PostGIS, Drizzle ORM, Redis + BullMQ,
Swagger/OpenAPI, WebSockets, React Email, sharp, Stripe, Vitest + Testcontainers, pnpm.

## Konwencje
- **Kod, commity, PR-y, komentarze:** po angielsku. **Tickety w Jirze:** po polsku.
- **Gałęzie:** `REV-<numer>-krotki-opis` (np. `REV-42-plan-limits`).
- **Commity:** Conventional Commits z numerem ticketu, np. `feat(cars): add photo upload [REV-42]`.
- **Trunk-based:** krótkie gałęzie z `main`, merge przez PR, `main` zawsze gotowy do wdrożenia.
- **Błędy API:** zawsze format Problem Details (RFC 9457) z polem `code`
  (np. `CAR_LIMIT_REACHED`). Nigdy gotowe teksty dla użytkownika — tłumaczy je frontend.
- **Kontrakt:** każda zmiana endpointu = aktualne dekoratory OpenAPI. Front generuje z tego
  klienta (Orval), więc zmiany łamiące kontrakt trzeba wyraźnie oznaczyć w PR.
- **Pieniądze** jako liczby całkowite w groszach + waluta. **Czas** w UTC (`timestamptz`).
- **Seed:** każda nowa encja/funkcja aktualizuje seed, żeby była od razu widoczna lokalnie i na INT.

## Bezpieczeństwo (zawsze)
- Każdy endpoint sprawdza uprawnienia do konkretnego zasobu (ochrona przed IDOR/BOLA),
  w jednym, scentralizowanym miejscu.
- Walidacja wszystkich danych wejściowych, whitelist pól (brak mass assignment).
- Żadnych sekretów w kodzie ani w repo. Nie edytuj plików `.env*` z prawdziwymi wartościami.
- Żadnych danych osobowych, haseł ani tokenów w logach.
- Cena i limity liczone zawsze na serwerze.

## Zależności (łańcuch dostaw)
- Nowe wersje paczek dopiero **3 dni po publikacji** (pnpm `minimumReleaseAge`). Nie omijaj tego
  ustawienia. Wyjątek: poprawki bezpieczeństwa.
- Zanim dodasz nową zależność, zapytaj i uzasadnij: czy jest potrzebna, czy jest aktywnie
  utrzymywana, ile ciągnie za sobą zależności. Preferuj rozwiązanie bez nowej paczki, jeśli jest proste.
- Instaluj konkretne wersje, nie `latest`. Akcje GitHub przypinaj do SHA.

## Praca z ticketami (Jira, projekt REV)
- Jira: przestrzeń `revvv.it`, klucz `REV`, połączona z GitHubem (GitHub for Atlassian, organizacja `revvvit`).
- Gdy dostajesz numer ticketu (np. `REV-42`), pobierz go przez Atlassian MCP,
  przeczytaj opis i kryteria akceptacji, zaproponuj plan i dopytaj o niejasności przed kodowaniem.
- Do tworzenia ticketów używaj skilla `jira-ticket` (szablony po polsku).
- Statusy: To Do → In Progress → Code Review → QA → Ready for Release → Done (szczegóły: project-brief, sekcja 8).
  Proponuj przejścia: start pracy → In Progress, otwarty PR → Code Review, merge → QA (testy na INT),
  przetestowane na INT → Ready for Release, wdrożone na PRD → Done.
- Nieudane QA: ticket wraca do In Progress, poprawka nowym PR-em z tym samym numerem ticketu.

## Definition of Done
- [ ] Kryteria akceptacji ticketu spełnione
- [ ] Testy jednostkowe dla logiki domenowej, testy integracyjne dla nowych endpointów
- [ ] Lint, typecheck i testy przechodzą
- [ ] OpenAPI aktualne
- [ ] Seed zaktualizowany (jeśli dotyczy)
- [ ] Sprawdzone pod kątem bezpieczeństwa (uprawnienia, walidacja, dane w logach)
- [ ] ADR dodany, jeśli zapadła decyzja architektoniczna

## Polecenia
<!-- Uzupełnić po wygenerowaniu szkieletu (REV: szkielet NestJS). Np.: -->
<!-- pnpm dev | pnpm test | pnpm lint | pnpm db:reset | docker compose up -d -->

## Dokumentacja
- `docs/project-brief.md` — kontekst i decyzje
- `docs/jak-pracujemy.md` — ściąga procesu (refinement, planning, ticket, review, QA, release)
- `docs/adr/` — decyzje architektoniczne
- `docs/product/` — wizja, roadmapa, priorytety, backlog
- `docs/learning/dziennik.md` — dziennik nauki; po otwarciu PR-a przypomnij o wpisie i pomóż go napisać
- `docs/sessions/` — zapis spotkań (warsztaty, refinement, planning, retro); po każdym spotkaniu zaproponuj notatkę wg szablonu z README
