# FRZR: zasady projektu dla agenta

Ten plik zbiera wymagania i zasady, według których agent (Claude Code) rozwijał FRZR od 22.10.2026. Wymagania wprowadzano stopniowo, w kolejnych rozmowach z właścicielką projektu. Przy każdej zasadzie jest data ustalenia, a sekcja „Historia wymagań” pokazuje kolejność. Plik spisano 3.11.2026 na podstawie tych rozmów i historii zmian w repozytorium.

## O projekcie

- FRZR to aplikacja w przeglądarce do prowadzenia diety: zamrażarka z posiłkami w pudełkach (etykiety QR), dziennik kalorii, białka i błonnika oraz waga.
- Może z niej korzystać jedna osoba albo dwie, które jedzą z tej samej zamrażarki (wybór w Ustawieniach).
- Cała aplikacja to jeden plik `index.html`: HTML, CSS i JavaScript bez frameworka i bez kroku budowania.
- Strona działa na GitHub Pages: https://ufosol.github.io/frzr-app/
- Dane są tylko w przeglądarce (localStorage, klucz `mroznik.v1`), z kopią JSON w Ustawieniach. Nie ma serwera.
- Zewnętrzne usługi: Gemini API (liczenie wartości odżywczych, klucz wpisuje użytkownik), Open Food Facts (kody kreskowe), biblioteki html5-qrcode i qrcode-generator z CDN.

## Zasady pracy

- Zmiany tylko w `index.html`. Nowy kod pisany jak otaczający: krótkie komentarze po polsku, te same nazwy i wzorce.
- Przed commitem test w przeglądarce Chromium (Playwright):
  - na kopii danych właścicielki,
  - w trybie jasnym i ciemnym,
  - na szerokości telefonu (390 px),
  - z kontrolą błędów w konsoli.
- Do repozytorium nie trafiają dane osobiste, kopie JSON, klucze API, zrzuty ekranu ani pliki testów. Repozytorium jest publiczne. (22.10)
- Opis commita po polsku: co się zmieniło i dlaczego.
- Po każdej zmianie krótkie podsumowanie dla właścicielki po polsku, bez żargonu, ze zrzutami ekranu, gdy zmiana jest widoczna.

## Teksty w aplikacji

- Wszystko po polsku, poprawnymi formami. Przykład: „jedz więcej”, nie „dodaj jedzenia”. (22.10)
- Nowe teksty nie zakładają płci użytkownika.
- Wyjaśnienia liczb na własnych danych użytkownika, nie wzorem. (22.10)

## Ochrona przed pomyłką (14.11)

- Większe zmiany powstają w osobnej gałęzi, nie w `main`. `main` to wersja, z której korzystają użytkownicy.
- Wpis, który wygląda na pomyłkę albo zastępuje istniejące dane, wymaga drugiego kliknięcia tego samego przycisku. Komunikat mówi, co się nie zgadza. Zmiana pola kasuje pytanie. Bez wyskakujących okien, poza wczytaniem kopii.
- Waga:
  - wartość różniąca się o ponad 4% od ważeń z okolic danego dnia wymaga potwierdzenia,
  - dzień, który ma już wagę, nie jest nadpisywany bez pytania,
  - waga poza 25 do 300 kg i data z przyszłości są odrzucane,
  - po zapisie na inny dzień pole daty wraca na dziś,
  - usunięcie ważenia pyta „Na pewno?”.
- Strona otwarta przez noc przechodzi na nowy dzień, żeby poranne wpisy nie trafiały do wczoraj.
- Jedzenie bez kalorii zapisuje się dopiero po drugim kliknięciu. Wartości ujemne są odrzucane.
- Cele w Ustawieniach są sprawdzane (kalorie 800 do 6000, cel wagi 25 do 300 kg albo puste pole) i potwierdzane komunikatem.
- Wczytanie kopii pyta przed zastąpieniem danych obu osób. Uszkodzony plik zostawia dane bez zmian. Kopia przechodzi te same migracje co dane z przeglądarki (`migrate()`).
- Import z czatu nie zapisuje dat z przyszłości ani wag poza zakresem i wymienia w komunikacie wagi, które zmienił.

## Bezpieczeństwo i zdrowie (zasady nadrzędne)

- Grywalizacja nigdy nie nagradza wagi poniżej celu. Średnia 7 dni poniżej zakresu oznacza (22.10):
  - ostrzeżenie zamiast pochwały,
  - wskazówkę „jedz więcej”,
  - zdanie o rozmowie z lekarzem lub dietetykiem.
- Po osiągnięciu celu nie ma komunikatu o „nowym minimum”. (22.10)
- Brak odznak za zejście poniżej celu.
- Dzień z za małą ilością jedzenia nie może wyglądać jak przejedzenie. Kolory kalorii (27.10):
  - zielony: w celu,
  - pomarańczowy: za dużo,
  - niebieski: za mało.

## Waga

- Cel wagi to zakres cel ±0,5 kg. Średnia w zakresie to „cel osiągnięty”, a nie „prawie”. (24.10)
- Postęp liczy się ze średniej 7 dni (od 3 ważeń w oknie). (22.10)
- Trend to linia dopasowana do wszystkich ważeń z ostatnich 14 dni, w kg na tydzień, od 7 ważeń. Nie porównywać dwóch średnich tygodniowych, bo jedno odstające ważenie odwraca wynik. (27.10)
- Po zmianie trybu postęp liczy się od pierwszego ważenia po zmianie. Serie liczą się z całej historii. (27.10)

## Kalorie i makro

- Pasek białka i błonnika ponad celem jest pełny i zielony. (22.10)
- Porównanie z celem na liczbach widocznych na ekranie (po zaokrągleniu). 1700,4 kcal przy celu 1700 to cel osiągnięty. (26.10)
- Dzień, który jeszcze trwa, nie wchodzi do średnich kalorii. Średnia bieżącego tygodnia jest podpisana „w trakcie” z liczbą dni. (27.10)
- Użytkownik wybiera, co śledzi w seriach i kropkach: same kalorie, kalorie z białkiem, kalorie z błonnikiem albo wszystkie trzy. (22.10)

## Tryby diety

Tryb jest osobny dla każdej osoby (27.10).

| Tryb | Waga | Kalorie w celu | Nadwyżka kcal na „Dziś” |
|---|---|---|---|
| redukcja | pasek w dół do celu | do limitu | pomarańczowa |
| utrzymanie | zakres ±0,5 kg | cel ±10% | pomarańczowa |
| rekompozycja | zakres ±0,5 kg | 85 do 100% celu | pomarańczowa |
| masa | pasek w górę do celu | od 95% celu | nie jest pomarańczowa |

- Rekompozycję dodano jako osobny tryb, bo kalorie właścicielki pasowały do rekompozycji, a do utrzymania były minimalnie za małe. (27.10)
- Cel po złej stronie wagi startowej (np. redukcja do wyższej wagi) daje podpowiedź zamiast pustego paska. (27.10)

## Dwie osoby

- Przełącznik osób na górze strony. (24.10)
- Wspólne: zamrażarka, przepisy, przyprawy, klucz API i baza produktów „Często jem”.
- Osobne: dziennik, waga, odznaki, cele, tryb.
- Baza „Często jem” jest jedna dla obu osób: nazwa i wartości raz, a licznik i data ostatniego dodania osobno (`food.c[osoba]`, `food.l[osoba]`). Produkt dodany przez jedną osobę jest od razu w podpowiedziach drugiej, z licznikiem 0. Poprawka wartości i usunięcie działają u obu. (15.11)
- Ustawienie „Kto korzysta”: jedna osoba albo dwie (`settings.people`). Przy jednej nie ma przełącznika ani imion, a dzielone pudełko schodzi ze stanu w całości, bez resztki czekającej na kogoś, kto nie korzysta z aplikacji. Dane osoby 2 nie są kasowane i wracają po ponownym włączeniu. Nowa osoba zaczyna w trybie jednej osoby. (15.11)
- `meal.share` to część osoby 1. Osoba 2 ma resztę (40% → 60%), a przy 100% też całe pudełko. Domyślne części ustawiane wprost dla aktywnej osoby, nigdy przez odwracanie bieżącej wartości pola. (24.10)
- Dzielone pudełko schodzi ze stanu raz, a reszta czeka na drugą osobę (`meal.wait[osoba]`). Usuwanie wpisów w dowolnej kolejności musi wracać do tego samego stanu. Części obu osób, które razem tworzą pudełko, wracają na stan. (26.10)
- Przełączenie osoby czyści komunikaty i niedokończone wpisy poprzedniej. (24.10)
- Ze stanu schodzi najwyżej tyle pudełek, ile jest. Wpis w dzienniku pamięta, co naprawdę zeszło ze stanu, więc zjedzenie czegoś, czego nie ma, i cofnięcie takiego wpisu nie tworzy pudełek ani resztek. Brak na stanie jest opisany w komunikacie. (14.11)
- Każdy element zamrażarki da się usunąć w oknie „Edytuj” (nie na karcie, żeby karty nie były zaśmiecone), z potwierdzeniem „Na pewno?”. Resztka czekająca tylko na jedną osobę (np. po pudełku, którego już nie ma) znika tylko u niej. Pełne pudełko schodzi ze stanu u obu osób. Posiłek zostaje w bazie, żeby dało się go ugotować ponownie. (13.11)

## Interfejs

- Podpowiedzi w „Dodaj coś spoza zamrażarki” nic nie wpisują same. Wartości z bazy tylko po kliknięciu podpowiedzi, a Enter dodaje to, co wpisane. (22.10)
- Przycisk „Dziś” obok kalendarza, widoczny tylko na innym dniu. (22.10)
- Tabela dni pogrupowana tygodniami, kolumny w grupach „Waga” i „Jedzenie”, ze średnią kcal z 7 dni. (24.10)
- „Wszystkie posiłki” to zwijana lista, na start zwinięta. Jedno danie to jeden wiersz z ostatnią partią, a starsze partie o tej samej nazwie są pod przyciskiem „Partie”. Kolejność do wyboru: ostatnio gotowane, alfabetycznie, najczęściej gotowane. Wyszukiwanie rozwija listę. Na telefonie wiersz jest kartą. (14.11)
- Zakładka „Przepisy”: trzy zwijane sekcje z licznikiem. „Moje produkty” są na start zwinięte, pokazują 15 pozycji i resztę pod przyciskiem, z kolejnością do wyboru: najczęściej dodawane, ostatnio dodawane, alfabetycznie. Wyszukiwanie rozwija sekcje i pokazuje wszystkie trafienia. Na telefonie produkt jest kartą. (15.11)
- Długie listy nie są pokazywane w całości: mają zwijanie, wybór kolejności albo przycisk „pokaż więcej”. (15.11)
- Tabela dni w Wadze pokazuje 45 dni, starsze pod przyciskiem „Pokaż starsze dni”. (14.11)
- Wykresy kalorii, białka i błonnika (30 dni) w zwijanej sekcji pod wykresem wagi (27.10):
  - jedna oś na wykres,
  - przerywana linia celu,
  - kolory jak kropki,
  - podpowiedź po najechaniu,
  - szerokość dopasowana do ekranu.

## Pusty start i przykład

- Nowa osoba zaczyna bez cudzych danych: bez posiłków, wag i celu wagi. (22.10)
- Pod adresem `?demo` aplikacja pokazuje zmyślone dane dwóch osób i niczego nie czyta ani nie zapisuje w przeglądarce. Prawdziwe dane muszą zostać nietknięte. (2.11)
- Na pustej aplikacji karta zachęty z przyciskiem „Pokaż na przykładowych danych”. (2.11)

## Historia wymagań

| Data | Wymaganie |
|---|---|
| 22.10 | Kod w repozytorium bez klucza API. Podpowiedzi bez samowpisywania. Pełny pasek białka ponad celem. Przycisk „Dziś”. |
| 22.10 | Podsumowanie wagi z grywalizacją. Po symulacji: brak nagradzania wagi poniżej celu, „jedz więcej”. Wybór śledzenia makro. Pusty start. Zrozumiały opis wydatku energii. |
| 24.10 | Cel jako zakres ±0,5 kg (błąd „97% do celu”). Tabela dni z tygodniami i średnią kcal. Dwie osoby i dzielone pudełka. Czyszczenie komunikatów przy zmianie osoby. Poprawne domyślne części 40% i 60%. |
| 26.10 | Kalorie równe celowi bez pomarańczowego. Dzielone pudełka bez dublowania przy usuwaniu. |
| 27.10 | Tryby redukcja, utrzymanie, masa. Średnie bez bieżącego dnia. Trend z 14 dni. „Za mało” na niebiesko. Tryb rekompozycja. Wykresy kalorii, białka i błonnika. |
| 2.11 | Przykładowe dane pod `?demo`. |
| 14.11 | Gałąź `porzadki-logiki`: przegląd logiki. Pytanie przed nietypową wagą, nadpisaniem dnia i wczytaniem kopii. Zamrażarka bez pudełek z powietrza. Lista posiłków pogrupowana i zwijana. Sprawdzanie celów i importu z czatu. |
| 15.11 | „Moje produkty” zwijane, z kolejnością i pierwszymi 15 pozycjami. Liczniki i zwijanie przepisów i przypraw. |
| 15.11 | Wspólna baza „Często jem” z osobnymi licznikami. Wybór trybu: jedna albo dwie osoby. |
| 13.11 | Usuwanie z zamrażarki elementów widocznych tylko u jednej osoby (zawieszone resztki dzielonych pudełek). Przycisk przeniesiony z karty do okna „Edytuj”. |
