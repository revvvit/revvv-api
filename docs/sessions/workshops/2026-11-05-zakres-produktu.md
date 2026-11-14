# Warsztat — zakres produktu

- **Data:** 2026-11-05
- **Uczestnicy:** właściciel, Claude, subagent Product Owner
- **Cel:** doprecyzować, czym jest revvv.it, dla kogo, i ułożyć kolejność etapów.

## Decyzje
- Model: katalog + leady. Od klienta nic nie pobieramy; docelowo płacą wypożyczalnie.
- Projekt jest przede wszystkim do nauki — nie wycinamy tematów do nauki, ustalamy kolejność.
- Zasięg: cała Polska od początku.
- Plany FREE / PRO / PREMIUM różnią się limitem aut (3 / 6 / więcej). Limit FREE działa od etapu 1,
  płatności testowe w Stripe (subskrypcja miesięczna) w etapie 4. Rekomendacja PO (plany różnicowane
  widocznością zamiast limitu) — odrzucona.
- Mapa zostaje w MVP (filtr marki/modelu + mapa) — rekomendacja PO, by ją przesunąć, odrzucona.
- Konto klienta opcjonalne (zapytanie także bez logowania), z historią zapytań.
- Cena „od X zł/dzień” obowiązkowa; marka i model ze słownika.
- WebSockets: aktualizacja kalendarza rezerwacji na żywo (podwójną rezerwację blokuje constraint w bazie).
- Zdjęcia na start generowane przez AI — nie kopiujemy cudzych.

## Otwarte kwestie
- System bonów prezentowych — do burzy mózgów (etap 7); kaucje razem z nim.
- Obniżenie planu: kto wybiera auta przechodzące w szkic, czy limit dotyczy tylko opublikowanych aut.
- Termin pokazania MVP.

## Zmiany i tickety
- Utworzone: `docs/product/vision.md`, `docs/product/roadmap.md`.
- Zaktualizowane: `docs/project-brief.md` (sekcje 2, 10, 12).
