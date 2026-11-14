# Warsztat — przegląd roadmapy i backlog Sprintu 0

- **Data:** 2026-11-14
- **Uczestnicy:** właściciel, Claude
- **Cel:** przejrzeć roadmapę po warsztacie z PO i przenieść backlog Sprintu 0 do Jiry.

## Decyzje
- **Rejestracja:** przy zakładaniu konta wybór typu: osobiste / wypożyczalnia. Wypożyczalnię weryfikuje
  admin (NIP, telefon); do tego czasu może wszystko skonfigurować, ale nic nie jest publiczne.
  Admin ma listę wypożyczalni do weryfikacji w panelu.
- Profile zakładane przez admina i „przejmij profil” — usunięte. Na start testowe konta z seeda;
  przekazanie konta prawdziwej wypożyczalni = zmiana e-maila.
- **Bez moderacji treści** — ufamy zweryfikowanym wypożyczalniom; admin może ukryć ofertę lub
  zablokować konto. Automatyczna moderacja na sam koniec roadmapy.
- **Wyszukiwanie:** strona główna = wyszukiwarka (filtry na górze, lista / mapa, sortowanie).
  Dwa tryby: auta i wypożyczalnie. W obu lokalizacja: miejscowość + promień albo województwo
  (wersja prosta: województwo zapisywane przy geokodowaniu).
- Karta auta i profil wypożyczalni jako osobne strony (SSR, SEO), nie modal.
- Maile (także do admina o nowej wypożyczalni) w etapie 3; dokładna lista na refinemencie etapu.
- Etapy doprecyzowujemy na bieżąco — refinement przed każdym etapem.
- Ślad ze spotkań zapisujemy w `docs/sessions/`.
- Sprint 0: zaczynamy od frontendu (REV-17, szkielet Next.js).

## Otwarte kwestie
- Próg mocy: 180 czy 200 KM.
- `priorities.md` (REV-7) — do zrobienia w trakcie sprintu.

## Zmiany i tickety
- Jira: epik REV-5 „Fundamenty projektu”, tickety REV-6…REV-33 (ticket #n z backlogu = REV-(n+5)).
  Nowe: REV-32 (konfiguracja Claude Code), REV-33 (środowisko PRD i pierwszy release).
- Zaktualizowane: `roadmap.md`, `vision.md`, `project-brief.md`, `sprint-0-backlog.md`.
- Utworzone: `docs/jak-pracujemy.md`, `docs/sessions/`.
