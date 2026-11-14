# Roadmapa — revvv.it

Etapy bez dat. Każdy ma cel produktowy i cel nauki. **MVP = etapy 0–3**: po nich pokazujemy
produkt pierwszym wypożyczalniom i klientom. Termin pokazania — do ustalenia.

## Etap 0 — Fundamenty
- **Produkt:** brak funkcji dla użytkownika.
- **Nauka:** szkielet obu repozytoriów, CI/CD, Docker, format błędów, i18n, seed, Sonar, sekrety, ogólna wstępna konfiguracja projektu.

## Etap 1 — Katalog: wypożyczalnie i auta
- **Produkt:**
  - Rejestracja (e-mail + hasło) z wyborem typu konta: osobiste albo wypożyczalnia.
    Konto wypożyczalni z 2FA.
  - Weryfikacja wypożyczalni przez admina (NIP, kontakt telefoniczny). Przed weryfikacją
    wypożyczalnia może skonfigurować wszystko — profil, oddziały (adres → współrzędne), auta —
    ale nic nie jest publicznie widoczne.
    - Admin ma w swoim dashboardzie widoczną listę wypożyczalni do weryfikacji
      (powiadomienie mailem o nowej wypożyczalni — w etapie 3, razem z resztą maili).
  - Auto: model ze słownika, parametry, „cena od”, wymagania wobec kierowcy, zdjęcia.
  - Limit aut planu FREE (3) — błąd `CAR_LIMIT_REACHED`.
    Seed: testowe konta wypożyczalni, maksymalnie 3 auta każda (rozszerzamy w etapie 4).
  - Zaufanie do zweryfikowanych wypożyczalni: treści publikują bez moderacji.
    Minimalny panel admina: weryfikacja wypożyczalni, ukrywanie ofert, blokowanie kont.
  - Przekazanie konta prawdziwej wypożyczalni (gdy projekt będzie komercyjny) = zmiana e-maila dostępowego.
- **Nauka:**
  - własne logowanie (sesje, Argon2id, 2FA), role i autoryzacja (IDOR, mass assignment),
  - walidacja NIP (suma kontrolna), opcjonalnie sprawdzenie firmy w API GUS/CEIDG,
  - relacje w Drizzle i migracje, geokodowanie (Nominatim) i typ punktu w PostGIS,
  - upload do S3, BullMQ i worker sharp (warianty, usuwanie EXIF), walidacja progu mocy (180–200 KM, do ustalenia).

## Etap 2 — Wyszukiwarka i mapa
- **Produkt:**
  - Strona główna = wyszukiwarka: filtry od razu na górze, przełącznik lista / mapa, sortowanie.
  - Dwa tryby wyszukiwania:
    - **Szukaj auta:** marka i model, cena, moc, napęd, wymagania, lokalizacja.
    - **Szukaj wypożyczalni:** lokalizacja (np. dla kogoś, kto chce kupić voucher, a nie konkretne auto).
  - Lokalizacja w obu trybach: miejscowość + promień (np. Warszawa + 50 km) albo województwo
    (województwo zapisywane przy geokodowaniu adresu oddziału).
  - Paginacja, stan wyszukiwania (filtry, widok, sortowanie) w URL-u — powrót z karty auta
    przywraca wyniki.
  - Osobne strony (SSR, pod SEO): karta auta, profil wypożyczalni (opis, oddziały na mapie, auta,
    kontakt, informacja „oferuje bony prezentowe”), strony modeli i miast.
  - Mapa z klastrowaniem i geolokalizacja z fallbackiem.
- **Nauka:** zapytania przestrzenne (promień: `ST_DWithin`) i indeksy (GiST), plany zapytań,
  MapLibre, SSR i stan w URL-u w Next.js.

## Etap 3 — Zapytania i konta klientów (koniec MVP)
- **Produkt:**
  - Zapytanie o dostępność bez logowania (Turnstile), z typem: dla siebie / prezent / jazda testowa.
  - Konto osobiste (opcjonalne): logowanie przez Google i historia zapytań. Zapytanie wysłane
    bez konta przypisuje się do konta po rejestracji na ten sam e-mail.
  - Maile do wypożyczalni, klienta i admina (np. nowa wypożyczalnia do weryfikacji).
    Dokładna lista maili — do ustalenia na refinemencie etapu.
- **Nauka:** OAuth/OIDC i łączenie kont, kolejki maili (React Email, Resend), tokeny jednorazowe,
  rate limiting, RODO (minimalizacja danych, usuwanie i eksport konta).

## Etap 4 — Plany i billing (tryb testowy)
- **Produkt:**
  - Przekroczenie limitu → propozycja zmiany planu → subskrypcja miesięczna w Stripe (karty testowe).
  - Plany FREE (do 3 aut), PRO (do 6), PREMIUM (powyżej 6).
  - Seed rozszerzony o wypożyczalnie z planami PRO i PREMIUM (więcej aut).
  - Rezygnacja / obniżenie planu: nadmiarowe auta przechodzą w szkic, z ostrzeżeniem przed zmianą.
    Do uzgodnienia: kto wybiera auta i czy limit dotyczy tylko opublikowanych aut (np. 10 aut, 3 opublikowane).
- **Nauka:** Stripe Billing (tryb testowy), cykl życia subskrypcji (upgrade, downgrade, anulowanie),
  webhooki z weryfikacją podpisu, idempotencja, stan subskrypcji w bazie, feature flagi.

## Etap 5 — Funkcje AI
- **Produkt:**
  - Wyszukiwanie językiem naturalnym zamieniane na filtry.
  - Generowanie opisów ofert (szybsze dodawanie aut przez wypożyczalnie).
- **Nauka:** Claude API, structured output, ochrona przed prompt injection, koszty i limity, testy niedeterministyczne.

## Etap 6 — Rezerwacje
- **Produkt:**
  - Kalendarz dostępności auta.
  - Klient wysyła prośbę o rezerwację, wypożyczalnia ją potwierdza. Bez płatności przez revvv.
  - Aktualizacja kalendarza na żywo (WebSockets), eksport iCal, weryfikacja klientów.
- **Nauka:** exclusion constraint przeciw podwójnej rezerwacji, transakcje i współbieżność,
  WebSockets (autoryzacja połączenia, adapter Redis).

## Etap 7 — Bony i kaucje (MODEL DO USTALENIA)
- **Produkt:** system bonów prezentowych — do opracowania na burzy mózgów (wypożyczalnie mają dziś
  zwykle bony papierowe); kaucje (blokada środków).
- **Nauka:** Stripe Connect lub Payment Intents, rozliczenia z wypożyczalnią.
- Do tego czasu scenariusz prezentowy obsługuje zapytanie z typem „prezent”.

## Etap 8 — Dodatki
- Opinie po wynajmie, zapisane wyszukiwania z alertami, panel statystyk dla wypożyczalni,
  elastyczne cenniki, rozbudowany panel admina, cache, wynajem na godziny, dostawa pod adres.
- Automatyczna moderacja treści (algorytm / AI) — na sam koniec.
- k6, własny serwer MCP z wyszukiwaniem aut.
