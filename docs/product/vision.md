# Wizja produktu — revvv.it

## Wizja
Każde auto sportowe (powyżej 180-200KM - do ustalenia) do wynajęcia w Polsce — znajdziesz je po marce, modelu i na mapie, w minutę.

## Problem
Trudno znaleźć wszystkie wypożyczalnie aut sportowych, a jeszcze trudniej konkretny model.
Kto chce się nim przejechać dla frajdy, w prezencie albo przed zakupem, musi przeszukać wiele stron.
Nie ma jednak gwarancji, że znajdzie wszystkie dostępne wypożyczalnie i auta.
W drugiej sytuacji - mając chęć wynajęcia auta na weekend, musi wybrać to co znajdzie po różnych wypożyczalniach i poświęcić czas na szukanie.

## Dla kogo
| Segment | Czego potrzebuje |
|---|---|
| Weekendowy entuzjasta (20–30-40 lat) | Mocne auto blisko siebie na kilka dni. Od razu widzi, czy spełnia wymagania (wiek, staż, kaucja) i ile to kosztuje. |
| Kupujący prezent | Przeżycie dla bliskiej osoby bez znajomości aut. Prosty sposób, żeby zapytać o prezent. |
| Przyszły nabywca | Jazda konkretnym modelem przed zakupem. Szuka po marce i modelu w całej Polsce. |
| Wypożyczalnia (docelowo klient płacący) | Więcej zapytań przy minimalnym wysiłku. Dodanie auta zajmuje kilka minut. |

## Model działania
- **Katalog i leady.** revvv.it pokazuje oferty i przekazuje zapytania do wypożyczalni.
  Od klienta nic nie pobieramy (system bonów — do opracowania).
- **Plany wypożyczalni:** FREE / PRO / PREMIUM różnią się limitem aut (wstępnie 3 / 6 / więcej).
  Działają od początku w trybie testowym: przekroczenie limitu → propozycja zmiany planu →
  subskrypcja miesięczna w Stripe (płatności testowe).
- **Zimny start:** na początku sami zakładamy profile wypożyczalni i dodajemy ich auta na podstawie
  publicznych danych. Wypożyczalnia może przejąć profil w przyszłości, jeśli aplikacja wystartuje komercyjnie.
  Odbędzie się to drogą manualną. Ewentualnie, jeśli padnie decyzja że aplikacja będzie komercjalizowana, proces uzupełniania rozpoczniemy od początku.
  Zdjęcia na start generowane przez AI — nie kopiujemy cudzych.
- **Zasięg:** cała Polska od początku.
- **Rejestracja wypożyczalni:** przy zakładaniu konta wybiera się typ (osobiste / wypożyczalnia).
  Wypożyczalnię weryfikuje admin (NIP, telefon); do tego czasu nic z jej oferty nie jest publiczne.

## Zasady produktu
- Mobile first. Ton luźny i energiczny, oparty na frajdzie (track day) i spełnianiu marzeń. Nie promujemy szybkiej jazdy po drogach publicznych, chociaż wiadomo jak będzie...
- Wymagania wobec kierowcy (wiek, staż, kaucja) i cena „od X zł/dzień” są obowiązkowe,
  widoczne od razu i dostępne jako filtry. Wymagania uzupełnia wypożyczalnia indywidualnie.
- Marka i model pochodzą ze słownika (marka → model), a nie z wolnego tekstu,
  bo na tym opiera się wyszukiwanie po modelu.
- Oferty należą do wypożyczalni. Ufamy zweryfikowanym wypożyczalniom — bez moderacji treści;
  admin może ukryć ofertę albo zablokować konto. Automatyczna moderacja — na sam koniec roadmapy.
- Próg mocy: od 180-200 KM.

## Czym nie jesteśmy
Nie jesteśmy wypożyczalnią ani pośrednikiem płatności między klientem a wypożyczalnią.
Nie oferujemy limuzyn z szoferem.

## Dwa cele
To przede wszystkim aplikacja do nauki i testów.
Cel nadrzędny to nauka: każdy temat z briefu ma swoje miejsce w roadmapie.
Ambicja komercyjna decyduje o kolejności i spójności etapów, a nie o wycinaniu zakresu.

## Miary sukcesu (wstępnie)
Liczba wypożyczalni i aut w katalogu, liczba zapytań na auto,
odsetek zapytań od zalogowanych klientów, do uzupełnienia.
