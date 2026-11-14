# revvv.it — opis projektu i podjęte decyzje

Ten dokument to „pamięć” projektu. Zbiera wszystkie ustalenia z fazy planowania.
Claude: traktuj go jako źródło prawdy o kontekście projektu. Gdy decyzja się zmienia,
zaktualizuj ten plik (i dodaj ADR, jeśli to decyzja architektoniczna).

---

## 1. Cel projektu

- **Cel nadrzędny:** nauka. Właściciel projektu jest frontend developerem (React) i przez
  ten projekt staje się fullstack developerem.
- **Nacisk na naukę:** backend (Node.js / NestJS), bazy danych, CI/CD, infrastruktura,
  cyberbezpieczeństwo, testy (jednostkowe, integracyjne, E2E), praca z Claude Code.
- **Frontend:** dalszy rozwój w mniejszym stopniu — hooki, wzorce, dobre praktyki, Next.js.
- **Proces jak w prawdziwym zespole:** Scrum, tickety w Jirze, gałęzie, PR-y, code review,
  środowiska, release'y. Projekt ma być możliwie bliski realnemu procesowi.
- **Ambicja:** docelowo możliwa aplikacja komercyjna, więc decyzje podejmujemy „na serio”.

## 2. Produkt

**revvv.it** — agregator ofert wypożyczalni aut sportowych i premium (moc od 200 KM).
To platforma pośrednicząca (marketplace), a nie pojedyncza wypożyczalnia.
Cel: zebrać w jednym miejscu jak najwięcej wypożyczalni aut sportowych i ich aut.

### Problem
- Trudno znaleźć wszystkie wypożyczalnie w okolicy, a jeszcze trudniej konkretny model auta.
- Przykład: ktoś rozważa zakup danego modelu i chce się nim najpierw przejechać, ale nie wie,
  gdzie go wypożyczyć — musi szukać na wielu stronach.

### Model działania
- Katalog + leady: pokazujemy oferty i przekazujemy zapytania do wypożyczalni. Od klienta nic nie
  pobieramy (wyjątek do ustalenia: system bonów).
- Zasięg: cała Polska.
- Plany FREE / PRO / PREMIUM różnią się limitem aut (3 / 6 / więcej). Działają od początku:
  przekroczenie limitu → zmiana planu → subskrypcja miesięczna w Stripe (tryb testowy).
- Wypożyczalnie zakładają konta same (wybór typu konta przy rejestracji), admin je weryfikuje
  (NIP, telefon). Do weryfikacji nic z ich oferty nie jest publiczne. Na start testowe konta z seeda;
  przekazanie konta prawdziwej wypożyczalni = zmiana e-maila dostępowego.
  Zdjęcia na start generowane przez AI (nie kopiujemy cudzych).
- To przede wszystkim aplikacja do nauki i testów — decyzje komercyjne weryfikujemy później.
- Wizja, persony i roadmapa: `docs/product/vision.md`, `docs/product/roadmap.md`.

### Grupa docelowa
- Szeroka grupa, nie tylko klienci VIP. Duża część to młodzi ludzie (np. 20–25 lat),
  którzy chcą wynająć mocne auto na weekend albo dać komuś przejażdżkę w prezencie.
- Chodzi o frajdę z jazdy i emocje, a nie o limuzyny z szoferem.
- Płacącymi klientami platformy są wypożyczalnie (subskrypcje).

### Wnioski produktowe
- **Scenariusz prezentowy** to jeden z głównych scenariuszy. W MVP: zapytanie z typem „prezent”.
  System bonów do opracowania (burza mózgów) — wypożyczalnie mają dziś zwykle bony papierowe.
- Cena „od X zł/dzień” obowiązkowa. Marka i model ze słownika (marka → model → generacja).
- Zaufanie do zweryfikowanych wypożyczalni: bez moderacji treści, admin może ukryć ofertę
  lub zablokować konto. Automatyczna moderacja — na sam koniec roadmapy.
- **Wymagania wobec kierowcy** (minimalny wiek, staż prawa jazdy, wysokość kaucji) muszą
  być widoczne od razu i dostępne jako filtry.
- **Mobile first.**
- **Ton komunikacji:** luźny i energiczny. Marketing opieramy na emocjach i frajdzie
  (np. track day), a nie na zachęcaniu do szybkiej jazdy po drogach publicznych.

### Zakres funkcjonalny
1. **Konta:** rejestracja e-mail + hasło oraz przez Google (OAuth/OIDC), łączenie kont,
   role (klient, wypożyczalnia, admin), 2FA dla wypożyczalni. Przy rejestracji wybór typu konta
   (osobiste / wypożyczalnia); wypożyczalnię weryfikuje admin (NIP, telefon).
   Konto osobiste opcjonalne, z historią zapytań.
2. **Wypożyczalnie i auta:** profile z oddziałami, auta ze słownika marek/modeli/generacji,
   parametry (moc, 0–100, napęd, nadwozie, rocznik), cena „od” obowiązkowa, walidacja progu mocy
   (180–200 KM, do ustalenia) na backendzie, minimalny panel admina (weryfikacja wypożyczalni,
   ukrywanie ofert, blokowanie kont).
3. **Zdjęcia:** kompresja na froncie + przetwarzanie w workerze (sharp): warianty rozmiarów,
   WebP/AVIF, usuwanie EXIF (GPS!), limity rozmiaru i liczby pikseli.
4. **Wyszukiwarka:** strona główna = wyszukiwarka; dwa tryby: auta i wypożyczalnie; lokalizacja
   (miejscowość + promień albo województwo), filtry (marka i model na czele), sortowanie, paginacja,
   stan w URL-u, przełącznik lista / mapa (MapLibre + OSM, PostGIS, klastrowanie), geolokalizacja
   z fallbackiem (IP / ręczny wybór miasta), osobne strony SSR/SEO: karta auta, profil wypożyczalni,
   strony modeli i miast.
5. **Plany wypożyczalni:** Free / Pro / Premium z limitem liczby aut, Stripe Billing (subskrypcja
   miesięczna, tryb testowy), obniżenie planu (nadmiarowe auta → szkice, szczegóły do ustalenia).
   Robione PRZED rezerwacjami.
6. **Formularz zapytań** o dostępność (mail do wypożyczalni) — także bez logowania (Turnstile),
   z typem: dla siebie / prezent / jazda testowa.
7. **Maile** z szablonami (React Email), wysyłane przez kolejkę.
8. **Powiadomienia na żywo** przez WebSockets: aktualizacja kalendarza rezerwacji na żywo.
   Komunikacja z wypożyczalnią odbywa się mailowo.
9. **Funkcje AI (Claude API):** wyszukiwanie językiem naturalnym zamieniane na filtry,
   generowanie opisów ofert.
10. **Rezerwacje:** kalendarz, prośba o rezerwację potwierdzana przez wypożyczalnię (bez płatności
    przez revvv), ochrona przed podwójną rezerwacją na poziomie bazy, eksport iCal, weryfikacja klientów.
11. **Bony i kaucje** — model do ustalenia (burza mózgów).
12. **Dodatki:** opinie po wynajmie, zapisane wyszukiwania z alertami, panel statystyk dla
    wypożyczalni, elastyczne cenniki, rozbudowany panel admina, cache, funkcje niszowe
    (wynajem na godziny, dostawa pod adres), automatyczna moderacja treści (na sam koniec).

## 3. Nazewnictwo

| Element | Wartość |
|---|---|
| Marka / domena | revvv.it (rozważane przekierowania: revvv.pl, revvv.me) |
| Organizacja GitHub | `revvvit` (github.com/revvvit) |
| Repozytoria | `revvv-api`, `revvv-web` (publiczne, bez licencji open source) |
| Jira | przestrzeń `revvv.it` (projekt Scrum), klucz `REV` (np. `REV-42`) |
| Gałęzie | `REV-42-krotki-opis` |
| Commity | Conventional Commits po angielsku, np. `feat(cars): add photo upload [REV-42]` |
| Obrazy Docker | `ghcr.io/revvvit/revvv-api` |
| Środowiska | `revvv-int`, `revvv-prd` |

**Języki:** kod, commity, PR-y i komentarze w kodzie — po angielsku. Tickety w Jirze — po polsku.
Interfejs aplikacji — domyślnie PL, przygotowany na EN.

## 4. Stack technologiczny

### Repozytoria
Dwa osobne repozytoria połączone kontraktem **OpenAPI**. Backend jest samodzielną usługą.

### Frontend (`revvv-web`)
- Next.js (App Router) — SSR stron publicznych pod SEO, panel jako część klienta
- Tailwind CSS + shadcn/ui, motyw na zmiennych CSS (light + dark)
- TanStack Query, React Hook Form + Zod
- Klient API generowany z OpenAPI przez **Orval**
- **next-intl** — na start tylko PL, ale żadnych tekstów na sztywno w komponentach
- **Obsługa błędów:** kody błędów z API tłumaczone na komunikaty, toasty (Sonner),
  błędy walidacji przy polach formularza, error boundaries
- MapLibre, Storybook

### Backend (`revvv-api`)
- NestJS + TypeScript
- Swagger / OpenAPI (specyfikacja generowana z kodu)
- **Jednolity format błędów:** Problem Details (RFC 9457) z kodem błędu
  (np. `CAR_LIMIT_REACHED`), nie z gotowym tekstem
- WebSockets (gateway z autoryzacją połączenia, adapter Redis na przyszłość)
- BullMQ (kolejki), React Email (szablony maili — tłumaczenia maili w repo API),
  sharp (zdjęcia), Stripe Billing, Claude API

### Dane
- PostgreSQL + PostGIS
- Drizzle ORM (bliżej SQL-a, typy geometryczne dla PostGIS)
- Redis (kolejki, cache)
- **Seed** wersjonowany w repo API: jedno polecenie czyści bazę, uruchamia migracje i ładuje
  realistyczne dane (ręcznie przygotowane rekordy + Faker ze stałym ziarnem).
  Każda nowa funkcja aktualizuje seed (część Definition of Done).

### Lokalnie
docker-compose: Postgres (PostGIS), Redis, MinIO (S3), Mailpit.

### Środowisko deweloperskie
- Windows + PowerShell; Docker Desktop (backend WSL2) do docker-compose.
- Końce linii LF wymuszone plikiem `.gitattributes` w obu repo (`* text=auto eol=lf`) — CI działa na Linuksie.
- Skrypty w `package.json` muszą działać w PowerShellu i na Linuksie (bez składni tylko bashowej,
  np. `rm -rf`, `VAR=x cmd`).

### Usługi zewnętrzne
Cloudflare R2 (pliki), Resend (maile), Stripe (tryb testowy), Nominatim (geokodowanie),
Doppler lub Infisical (sekrety), Sentry, PostHog.

## 5. Środowiska, CI/CD i release

### Środowiska
- **local** — docker-compose, własna baza
- **INT** — automatyczny deploy po każdym merge'u do `main`, własna baza
- **PRD** — release na koniec sprintu
- Hosting (wstępnie, do potwierdzenia): Vercel Hobby (front), Render Free (API),
  Neon Free (Postgres + PostGIS), Redis Cloud / Upstash (od etapu z kolejkami).
  Bez samodzielnego DevOps na tym etapie.

### CI (GitHub Actions)
Lint, typecheck, testy, E2E, SonarQube Cloud (quality gate), build obrazu Docker → GHCR,
deploy po zielonym pipeline, ręczny workflow do odświeżenia seeda na INT.
Ochrona gałęzi `main`: wymagane zielone checki (bez wymogu akceptacji PR — praca solo).

### Release (koniec sprintu)
- **Trunk-based development:** `main` zawsze gotowy do wdrożenia, krótkie gałęzie,
  niedokończone funkcje za feature flagą.
- Tickety mają przypisaną wersję (fixVersion) w Jirze.
- **Release = promocja przetestowanej wersji z INT na PRD** (ten sam obraz Docker API —
  „build once, deploy many”; front buduje się per środowisko przez `NEXT_PUBLIC_*`).
- **Zamrożenie:** od startu `/release` do wdrożenia na PRD nie mergujemy do `main`.
- **Weryfikacja dwufazowa:**
  1. Przed release'em (INT, bramka go/no-go): porównanie ticketów z wersji w Jirze
     z commitami/PR-ami w `main`, testy E2E i smoke, przeklikanie nowych funkcji przez
     Playwright MCP według kryteriów akceptacji, przegląd Sentry.
  2. Po release'ie (PRD): smoke test i obserwacja Sentry.
  Podstawą bramki są testy deterministyczne; agent to dodatkowa warstwa.
- **Bugi z weryfikacji:** najpierw wyszukanie duplikatów w Jirze, ocena ważności wg
  `docs/product/priorities.md`, propozycja decyzji (krytyczny na INT blokuje release,
  krytyczny na PRD → rollback/hotfix, drobny → backlog). Decyzję podejmuje człowiek.
- Kolejność wdrożenia: migracje (expand/contract, kompatybilne wstecz) → API → front.
- Ręczne zatwierdzenie wdrożenia na PRD w GitHub Actions (environments).
- semantic-release: wersje, changelog, GitHub Releases. Oznaczanie wydań w Sentry.
- Rollback = poprzedni obraz Docker. Hotfix = wersja patch (np. 1.3.1).

## 6. Testy i jakość
- Vitest: testy jednostkowe (logika domenowa) i integracyjne endpointów na prawdziwej bazie
  (Testcontainers)
- Testing Library + MSW na froncie
- Playwright: E2E kluczowych ścieżek (w repo web, z obrazem API z rejestru), axe (a11y)
- k6 (wydajność) — później
- ESLint + Prettier, Lefthook, commitlint, Renovate, SonarQube Cloud, Bruno (testy API ręczne)

## 7. Bezpieczeństwo (przy każdej funkcji)
OWASP Top 10 i ASVS jako checklisty. Kluczowe: autoryzacja i ochrona przed IDOR/BOLA
oraz mass assignment, bezpieczne sesje (httpOnly, Secure, SameSite), Argon2id, rate limiting,
walidacja (Zod), CSP, sanityzacja HTML, bezpieczne uploady (typ po zawartości, limity, EXIF),
weryfikacja podpisów webhooków Stripe, cena liczona zawsze na serwerze, gitleaks, Semgrep/CodeQL,
OWASP ZAP, Cloudflare Turnstile, akcje GitHub przypięte do SHA, RODO (minimalizacja danych,
usuwanie i eksport konta, brak PII w logach), dziennik audytowy.

**Łańcuch dostaw (zależności):**
- **Okres karencji 3 dni:** nowe wersje paczek trafiają do projektu najwcześniej 3 dni po publikacji.
  - pnpm: ustawienie `minimumReleaseAge` (chroni każdą instalację, także ręczne `pnpm add`)
  - Renovate: `minimumReleaseAge: "3 days"` dla aktualizacji wersji
  - Wyjątek: **poprawki bezpieczeństwa wchodzą natychmiast** (czekanie pomaga atakującym)
- Wersje zależności przypięte w lockfile, akcje GitHub przypięte do SHA.
- Nowa zależność tylko po uzasadnieniu (czy jest potrzebna, czy jest utrzymywana, ile ma zależności).
- W GitHubie włączone: Dependabot alerts, Dependabot security updates, Secret Protection
  z Push protection, prywatne zgłaszanie podatności.

## 8. Proces i organizacja
- **Jira:** przestrzeń `revvv.it` (projekt Scrum), klucz `REV`. Połączona z GitHubem
  (GitHub for Atlassian) — gałęzie, commity i PR-y z numerem `REV-x` są widoczne w tickecie.
- **Statusy:** To Do → In Progress → Code Review → QA → Ready for Release → Done

  | Status | Znaczenie |
  |---|---|
  | To Do | zaplanowane w sprincie, nierozpoczęte |
  | In Progress | praca na gałęzi `REV-x-…` |
  | Code Review | otwarty PR, pipeline + review |
  | QA | zmergowane do `main`, automatycznie wdrożone i testowane na INT |
  | Ready for Release | przetestowane na INT, czeka na release |
  | Done | wdrożone na PRD |

- Release musi się odbyć **przed zamknięciem sprintu**.
- Gdy ticket nie przejdzie QA, wraca do In Progress, a poprawka idzie **nowym PR-em**
  (pierwotny jest już zmergowany).
- **Typy:** Epic, Story, Task (etykiety: FE, BE, Design, DevOps, Docs, QA), Bug.
- **Sprinty dwutygodniowe**, estymacja w story pointach, retro, raporty burndown.
- **Wirtualny zespół Scrumowy** (subagenci Claude): Product Owner, Tech Lead, Scrum Master,
  opcjonalnie agent „klienta” generujący realistyczne zgłoszenia. Agenci mają własne zdanie
  i się sprzeciwiają, gdy trzeba. Zapis każdego spotkania (warsztaty, refinement, planning, retro) w `docs/sessions/`.
- **Dokumenty produktowe** (do utworzenia): `docs/product/vision.md`, `roadmap.md`,
  `priorities.md` (zasady priorytetyzacji + macierz ważności błędów).
- **ADR-y** w `docs/adr/`. Figma do designu. Confluence — opcjonalnie.

## 9. Claude w workflow
- CLAUDE.md w obu repo.
- **Styl pracy:** w `revvv-api` styl Learning (Claude tłumaczy i prosi o napisanie kluczowych
  fragmentów), w `revvv-web` zwykły tryb z elementami nauki (Claude implementuje, ale tłumaczy
  nieoczywiste hooki, wzorce i dobre praktyki).
- **Skille** (dodawane stopniowo, gdy coś zrobimy ręcznie 2–3 razy):
  `jira-ticket` (gotowy), `start-ticket`, `adr`, `new-endpoint`, `db-change`, `new-component`,
  `sync-api-client`, `security-review`, `sprint-planning`, `refinement`, `daily`, `retro`,
  `release-notes`, `release`.
- **Serwery MCP:** Atlassian (Jira), Playwright, Sentry, GitHub, Figma (w tym zapis na canvas).
- **Hooki** (np. formatowanie po edycji, blokada edycji `.env`) i **subagenci**
  (np. `security-reviewer`, role Scrumowe).
- **Claude Code w GitHub Actions** (@claude w PR-ach).
- Opcjonalnie: własny serwer MCP z wyszukiwaniem aut.

## 10. Etapy
MVP = etapy 0–3. Szczegóły i cele nauki: `docs/product/roadmap.md`.
0. **Fundamenty:** szkielet obu repo, CI, Sonar, sekrety, obsługa błędów, i18n,
   konfiguracja Claude'a. Cel: front pobiera `/health` z API, oba repo mają zielony pipeline.
1. **Katalog:** rejestracja z wyborem typu konta, weryfikacja wypożyczalni przez admina, 2FA,
   oddziały, auta ze słownikiem modeli, zdjęcia (kolejka + sharp), limit aut planu FREE,
   minimalny panel admina.
2. **Wyszukiwarka i mapa:** wyszukiwanie aut i wypożyczalni, lokalizacja (promień / województwo),
   lista / mapa (PostGIS, klastrowanie), geolokalizacja, strony auta i wypożyczalni (SSR/SEO).
3. **Zapytania i konta klientów:** zapytania (także bez logowania), opcjonalne konto osobiste
   (Google) z historią, maile przez kolejkę (wypożyczalnia, klient, admin).
4. **Plany i billing:** zmiana planu po przekroczeniu limitu, subskrypcja Stripe (tryb testowy), obniżenie planu.
5. **Funkcje AI:** wyszukiwanie językiem naturalnym, opisy ofert.
6. **Rezerwacje:** kalendarz, exclusion constraint, WebSockets (kalendarz na żywo), iCal, weryfikacja klientów.
7. **Bony i kaucje** — model do ustalenia (burza mózgów).
8. **Dodatki.**

## 11. Wygląd (roboczo)
- Interfejs w ~90% neutralny, kolor marki tylko w akcjach i wyróżnieniach. Light + dark mode.
- Kandydaci na paletę (decyzja razem z logo):
  - **A — grafit i papaja:** akcent `#FF8000` (tekst na przycisku ciemny `#18181B`),
    tekst akcentu w jasnym trybie `#C2410C`, tło jasne `#FAFAF9`, tło ciemne `#0E0F11`,
    akcent w ciemnym `#FF8A1F`.
  - **C — grafit i limonka:** akcent `#C8F031` (w jasnym trybie przycisk grafitowy
    `#18181B` z limonkowym tekstem), tekst akcentu w jasnym `#4D7C0F`, tło ciemne `#0D0E0C`.
- Odrzucone: połączenia czerwono-czarne.
- Kolory trzymamy jako tokeny w jednym pliku motywu — zmiana palety to jeden PR.

## 12. Otwarte decyzje
**Przed etapem 1:**
- Logowanie: własna implementacja (sesje w bazie + Arctic + otplib) czy biblioteka
  (np. Better Auth). Rekomendacja: własna, bo to cel nauki.
- **Zdecydowano:** oddziały wypożyczalni w modelu od początku.
- **Zdecydowano:** wypożyczalnie rejestrują się same, admin weryfikuje (NIP, telefon); bez moderacji treści.
- Próg mocy: 180 czy 200 KM.

**Przed etapem 4:** obniżenie planu — kto wybiera auta przechodzące w szkic; czy limit dotyczy
wszystkich aut, czy tylko opublikowanych.

**Przed etapem 7:** system bonów (burza mózgów) i kaucje a zasada „od klienta nic nie pobieramy”.

**Do omówienia (propozycje „tanie teraz, drogie później”):** ceny jako liczby całkowite
w groszach + waluta, czas w UTC (`timestamptz`), walidacja zmiennych środowiskowych przy
starcie, logi strukturalne z identyfikatorem żądania, endpoint `/health`, rate limiting,
ciemny motyw od początku, szablon PR i Definition of Done.

**Później:** termin pokazania MVP, finalny hosting, sprawdzenie znaku towarowego revvv (EUIPO, UPRP, klasy 39 i 35),
logo i finalna paleta, widget rezerwacyjny, wybór automatyzacji (dev, produktowe, operacyjne).
