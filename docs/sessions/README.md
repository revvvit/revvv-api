# Sesje — zapis spotkań

Ślad po wirtualnych spotkaniach (właściciel + Claude + subagenci: PO, Tech Lead, Scrum Master).
Każde spotkanie = jeden plik. Claude zapisuje go na koniec spotkania, właściciel akceptuje.

| Katalog | Co tu trafia |
|---|---|
| `workshops/` | warsztaty produktowe, burze mózgów, przeglądy dokumentów |
| `refinement/` | przygotowanie ticketów, estymacja |
| `planning/` | planowanie sprintu: cel, zakres, pojemność |
| `retro/` | review i retrospektywa sprintu |

Po 2–3 notatkach zapisanych ręcznie zamieniamy ten proces w skill `session-notes`.

**Nazwa pliku:** `RRRR-MM-DD-temat.md`, np. `2026-11-14-sprint-0.md`.

## Szablon

```markdown
# <Rodzaj spotkania> — <temat>

- **Data:** RRRR-MM-DD
- **Uczestnicy:** właściciel, Claude, <subagenci>
- **Cel:** <jedno zdanie>

## Decyzje
- ...

## Otwarte kwestie
- ...

## Zmiany i tickety
- <zmienione pliki, utworzone / zmienione tickety REV-x>
```
