# Jak pracujemy — ściąga

Jeden czat = jedna czynność. Decyzje podejmujesz Ty, Claude proponuje i wykonuje.
Teksty w cudzysłowie wpisujesz do Claude'a (później część z nich zastąpią skille, np. `/start-ticket`).

---

## 1. Refinement — przygotowanie ticketów

**Kiedy:** przed planningiem albo w połowie sprintu.

1. Nowy czat: „Refinement z PO i Tech Leadem — tickety bez estymacji w backlogu.”
2. PO sprawdza opisy i kryteria akceptacji, Tech Lead dzieli za duże tickety.
3. Estymacja (planning poker): **najpierw Ty podajesz story pointy**, potem agenci. Różnice omawiacie.
4. Zatwierdzasz → Claude wpisuje zmiany i punkty do Jiry i zapisuje notatkę w `docs/sessions/refinement/`.

## 2. Planning — start sprintu

**Kiedy:** pierwszy dzień sprintu.

1. Nowy czat: „Planning Sprintu N. Mam ok. X godzin w tym sprincie.”
2. PO proponuje cel sprintu i tickety, Tech Lead sprawdza, czy to się zmieści.
3. Zatwierdzasz zakres → Claude zapisuje notatkę w `docs/sessions/planning/`
   i przypisuje tickety do sprintu.
4. **Ty w Jirze:** Backlog → **Start sprint** → czas trwania 2 tygodnie, wpisz cel.

## 3. Praca nad ticketem

1. Nowy czat: „Weź REV-12.”
2. Claude czyta ticket, przesuwa go do **In Progress**, tworzy gałąź i proponuje plan.
3. Robicie zadanie (w `revvv-api` kluczowe fragmenty piszesz Ty).
4. „Otwórz PR” → ticket idzie do **Code Review**.
5. Dopisz 3–4 zdania do `docs/learning/dziennik.md` (Claude przypomni i pomoże).

## 4. Code review

1. **Nowy czat** (świeże spojrzenie): „Zrób review PR-a dla REV-12.”
2. Poprawki: wróć do czatu ticketu albo poproś o nie w tym czacie.
3. CI zielone → **Ty robisz merge** na GitHubie.
4. Ticket idzie do **QA** (Claude przesuwa go na Twoją prośbę).

## 5. QA na INT

1. Nowy czat: „QA REV-12 na INT.”
2. Claude przeklikuje kryteria akceptacji na INT.
3. Działa → **Ready for Release**. Nie działa → **In Progress**, poprawka nowym PR-em.

## 6. Daily (opcjonalnie, 2 minuty)

„Daily: co jest w toku, co blokuje, czy zdążymy przed końcem sprintu?”

## 7. Koniec sprintu

1. Nowy czat: „Release Sprintu N.”
   - Claude sprawdza bramkę go/no-go (tickety, testy, Sentry).
   - **Ty zatwierdzasz** wdrożenie na PRD w GitHub Actions.
   - Tickety → **Done**.
2. Nowy czat: „Review i retro Sprintu N.” → co się udało, co poprawić, quiz z dziennika nauki, notatka w `docs/sessions/retro/`.
3. **Ty w Jirze:** **Complete sprint** → niedokończone tickety wracają do backlogu albo do kolejnego sprintu.
4. Od razu planning kolejnego sprintu (krok 2).

## Coś nie działa?

„Zgłoś buga: <co się stało, gdzie>.” → Claude szuka duplikatów i proponuje ticket.

---

> Kroki 5 i 7 zadziałają w pełni, gdy będą środowiska INT i PRD.
