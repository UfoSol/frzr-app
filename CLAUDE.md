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
- W „Gotuję” zapis do zamrażarki, zapis przepisu bazowego i zapis w oknie „Edytuj” przepisu nie przepuszczają niepełnych wierszy: wiersz bez nazwy albo bez gramów blokuje zapis (komunikat wymienia wiersze). Wiersz z samymi zerami kcal, białka i błonnika (np. woda) wymaga drugiego kliknięcia tego samego przycisku. Przyprawy i „do smaku” mogą mieć 0 kcal bez pytania, wiersze z sumami nie są blokowane. (15.11)
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
- Najczęstszy podział pudełka to ustawienie przy dwóch osobach (`settings.split`: 30/70, 40/60, 50/50, 60/40, 70/30 albo całe pudełko, domyślnie 40/60). Przyciski części w „Gotuję” i w zamrażarce pokazują ten podział (dla aktywnej osoby), 50% i 100%, bez powtórzeń, więc przy 50/50 albo 100% są dwa przyciski zamiast trzech. Przy jednej osobie ustawienia nie widać, a przyciski to 50% i 100%. (15.11)
- Przełączenie osoby czyści komunikaty i niedokończone wpisy poprzedniej. (24.10)
- Ze stanu schodzi najwyżej tyle pudełek, ile jest. Wpis w dzienniku pamięta, co naprawdę zeszło ze stanu, więc zjedzenie czegoś, czego nie ma, i cofnięcie takiego wpisu nie tworzy pudełek ani resztek. Brak na stanie jest opisany w komunikacie. (14.11)
- Każdy element zamrażarki da się usunąć w oknie „Edytuj” (nie na karcie, żeby karty nie były zaśmiecone), z potwierdzeniem „Na pewno?”. Resztka czekająca tylko na jedną osobę (np. po pudełku, którego już nie ma) znika tylko u niej. Pełne pudełko schodzi ze stanu u obu osób. Posiłek zostaje w bazie, żeby dało się go ugotować ponownie. (13.11)

## Interfejs

- Podpowiedzi w „Dodaj coś spoza zamrażarki” nic nie wpisują same. Wartości z bazy tylko po kliknięciu podpowiedzi, a Enter dodaje to, co wpisane. (22.10)
- Przycisk „Dziś” obok kalendarza, widoczny tylko na innym dniu. (22.10)
- Skaner na „Dziś” („Skanuj kod”, 16.11) czyta dwa rodzaje kodów: QR z etykiety pudełka („zjem pudełko”) i kod kreskowy produktu EAN/UPC („zjem czekoladę”). Kamera: tylna, wyższa rozdzielczość, natywny detektor kodów (jeśli jest), ostrość ciągła, suwak przybliżenia (jeśli kamera go ma), dotknięcie obrazu łapie ostrość, pole skanowania prostokątne. Pudełko jest zdejmowane ze stanu jak dotąd. Kod kreskowy: dane z Open Food Facts (10 s limitu), karta z nazwą, wartościami na 100 g, gramami do wyboru (domyślnie porcja z bazy albo 100 g) i przyciskiem „Dodaj do dziś”; produkt bez kalorii w bazie nie tworzy wpisu, a dodany trafia też do „Często jem”. Skaner jest wstrzymany, gdy karta produktu jest otwarta. Udany skan (pudełko albo „Dodaj do dziś” przy produkcie) od razu zamyka okno skanera i zatrzymuje kamerę, a potwierdzenie jest pod przyciskiem „Skanuj kod” na „Dziś” (znika po 12 s), żeby nie dało się skanować w nieskończoność i nie patrzeć na komunikat; przy błędzie (nieznany kod, brak w bazie, brak kalorii) okno zostaje otwarte i można spróbować jeszcze raz. Kod wpisywany ręcznie („Z kodu kreskowego” przy dodawaniu spoza zamrażarki) zostaje.
- Kod QR na etykiecie niesie identyfikator pudełka i dane jednej porcji (`MROZNIK:id|kcal|białko|błonnik|część osoby 1|nazwa`, do 62 bajtów, polskie litery w UTF-8), bo dane FRZR są zapisane osobno w przeglądarce każdego urządzenia. Gdy pudełka nie ma w przeglądarce (np. telefon bez danych z komputera), skan dopisuje porcję z danych w kodzie (część osoby jak zwykle), bez ruszania stanu zamrażarki, a komunikat to mówi. Stare etykiety (samo id) bez pudełka dają wyjaśnienie, że dane są na innym urządzeniu. Kod na etykiecie ma do 18 mm, żeby przy gęstszym kodzie moduły nie były za małe. Nie ma synchronizacji danych między urządzeniami. (16.11)
- Tabela dni pogrupowana tygodniami, kolumny w grupach „Waga” i „Jedzenie”, ze średnią kcal z 7 dni. (24.10)
- „Wszystkie posiłki” to zwijana lista, na start zwinięta. Jedno danie to jeden wiersz z ostatnią partią, a starsze partie o tej samej nazwie rozwija zielona strzałka po prawej (bez przycisku z napisem, tylko gdy partii jest więcej niż jedna). Kolejność do wyboru: ostatnio gotowane, alfabetycznie, najczęściej gotowane. Wyszukiwanie rozwija listę. Na telefonie wiersz jest kartą. (14.11)
- Zakładka „Przepisy”: trzy zwijane sekcje z licznikiem. „Moje produkty” są na start zwinięte, pokazują 15 pozycji i resztę pod przyciskiem, z kolejnością do wyboru: najczęściej dodawane, ostatnio dodawane, alfabetycznie. Wyszukiwanie rozwija sekcje i pokazuje wszystkie trafienia. Na telefonie produkt jest kartą. (15.11)
- Przepisy i przyprawy na liście mają „Podgląd” zamiast „Edytuj”. Podgląd przepisu to okno ze wszystkimi składnikami (gramy, kcal, białko, błonnik) i sumami. Dopiero w oknie jest „Edytuj”: wtedy można zmieniać nazwę, gramy, wartości na 100 g, liczbę pudełek i część, dopisywać składniki (znany składnik uzupełnia wartości) i je usuwać, z przeliczaniem na żywo. Zapis nie nadpisuje innego przepisu o tej samej nazwie, a odrzucenie niezapisanych zmian wymaga drugiego kliknięcia. Zmiana dotyczy przepisu bazowego, nie pudełek w zamrażarce. „Więcej opcji w Gotuję” otwiera pełny edytor z Gemini. Podgląd przyprawy pokazuje skład i przypisany przepis (bez kalorii), a „Edytuj” zmienia nazwę, skład i przypisanie. (15.11)
- Liczenie składników (15.11): przycisk „Policz” (zamiast „Policz z Gemini”) szuka po kolei w Twoich przyprawach (0 kcal), składnikach z Twoich przepisów, zapamiętanych wynikach Gemini i wbudowanej bazie popularnych produktów (`FOODDB_TXT`, wartości na 100 g, miary domowe przeliczane na gramy, odmiana polskich słów). Gemini dostaje tylko to, czego nie znaleziono, a bez klucza brakujące pozycje są wymienione w komunikacie. Suche przyprawy i sól liczą się jako 0 kcal, także w poleceniu dla Gemini (`RULES`).
- Wyniki Gemini zapisują się w `S.calcCache` (do 300 pozycji, po przekroczeniu znikają najdawniej użyte), żeby nie pytać drugi raz. W Ustawieniach jest przycisk czyszczący całą tę pamięć naraz (drugie kliknięcie potwierdza), bez wybierania pojedynczych pozycji.
- Liczenie z Gemini ma wskaźnik pracy: kółko, etap, sekundy, pasek, przycisk „Anuluj” i podpowiedź po 12 s. Każda próba ma limit czasu (45 s z wyszukiwaniem, 30 s bez), a zmiana osoby przerywa liczenie. W „Opisowo” przycisk „Dodaj” wpisuje kalorie podane w tekście bez żadnego liczenia. Przy „Dodaj” na „Dziś” białko i błonnik najpierw z bazy (w proporcji do wpisanych kcal), potem z Gemini.
- Długie listy nie są pokazywane w całości: mają zwijanie, wybór kolejności albo przycisk „pokaż więcej”. (15.11)
- Przyprawy nie mają wartości odżywczych i nie liczy się ich kalorii (15.11). Przyprawa to nazwa, skład (lista nazw, tylko do podglądu) i przypisane przepisy na danie, czyli warianty (`recipeIds`, np. ten sam łosoś z masłem raz z ryżem, raz z ziemniakami). Przypisane przepisy służą do szybszego wypełniania tabeli przy gotowaniu. Gotuję nie ma trybu „Z przyprawy” (przepisy przyprawy wczytuje się w trybie „Z przepisu”, gdzie przepisy z przyprawą mają ikonkę, albo z podglądu przyprawy). Nowy przepis dla przyprawy zaczyna się z formularza „Samą przyprawę” (przycisk „Zapisz i dodaj nowy przepis”: pusta tabela, a przepis zapisany dołącza do przyprawy jako kolejny wariant). Lista przypraw w Przepisach nie ma przycisku „Gotuję” (dublowałby „Gotuję” przy przepisie i tryb „Z przyprawy”), jest tylko „Podgląd” i ×. Podgląd przyprawy ma „Gotuję z tym przepisem” przy każdym wariancie, ale nie ma przycisku „Nowy przepis dla tej przyprawy” (był mylący i niepotrzebny). Formularz „Samą przyprawę” ma „Zapisz i dodaj nowy przepis”. Warianty wybiera się kafelkami z × i listą „Dodaj wariant…” (w formularzu i w Edytuj). Przypisanie przy zapisie przepisu bazowego dodaje wariant i niczego nie zastępuje. Usunięcie przepisu odpina go od przyprawy. Starsze przyprawy z jednym `recipeId` migrują do listy. Starsze przyprawy z gramami i kaloriami migrują do tego kształtu, a ich linie w przepisach zostają bez kalorii.
- Magazyn przypraw i etykiety przypraw (gałąź `przyprawy`, 16.11):
  - przyprawa ma licznik przygotowanych sztuk (`sp.stock`, nie schodzi poniżej 0): kolumna „Mam” na liście „Moje przyprawy” z − i +, ten sam licznik w podglądzie przyprawy; formularz „Samą przyprawę” w Gotuję ma wybór „Którą przyprawę przygotowujesz” (nowa albo istniejąca), „Ile sztuk przygotowano” (domyślnie 1) oraz przyciski „Zapisz przyprawę” / „Dodaj do magazynu” i „Zapisz i podgląd etykiety” / „Dodaj do magazynu i podgląd etykiety”, które zapisują i otwierają okno z podglądem (tyle etykiet, ile sztuk, co najmniej 1), a drukuje się dopiero stamtąd, żeby można było poprawić skład i obrazek; zużycie przyprawy przy gotowaniu nie zdejmuje jej automatycznie ze stanu (do ustalenia),
  - etykieta przyprawy to wysoki obraz (domyślnie 50 × 120 mm, `settings.spiceLabel`) jak wzór właścicielki: u góry ramka z nazwą dania (nazwa wybranego przepisu przyprawy, wielkimi literami, Impact albo gruby Arial) i obrazkiem, u dołu „SKŁADNIKI” i lista składników dania, obrócone o 180° (etykieta jest składana); na etykiecie nigdy nie ma składu samej przyprawy (`sp.comp`), tylko składniki dania przypisanego do przyprawy; przyprawa bez dania ma tytuł z nazwy przyprawy i dolna część etykiety jest pusta; ten sam obraz idzie do druku przez okno przeglądarki (obraz w rozmiarze etykiety, jedna na stronę) i na M02S (z przycinaniem i odstępami jak etykiety dań),
  - obrazek: ikona z listy (miska, garnek, papryczka, słoik, liść, łyżka), „Bez obrazka” albo własny wgrany (zmniejszony, w odcieniach szarości, zapisany w przeglądarce razem z kopią danych, na etykiecie rastrowany metodą Floyda-Steinberga); wybór i wgrywanie są w oknie etykiet przypraw, przy każdej przyprawie; przy kilku przepisach (wariantach) wybiera się, który jest na etykiecie, domyślnie pierwszy,
  - przyciski etykiety: „Wydrukuj etykietę przyprawy” w podglądzie przyprawy (nie w Edytuj), „Etykieta” przy przyprawie na liście, tryb zaznaczania „Drukuj etykiety przypraw” (pola wyboru, „Zaznacz wszystko”, „Dalej”, „Anuluj”) i okno z liczbą etykiet dla każdej przyprawy (domyślnie tyle, ile w magazynie, co najmniej 1), z tym samym wyborem „Drukuj przez” i trybu wielu etykiet co przy daniach,
  - przepis do istniejącej przyprawy (także takiej bez przepisu) dokłada się bezpośrednio, bez Edytuj: przycisk „Dodaj przepis” (przy kilku „+ przepis”) w kolumnie „Warianty (przepisy)” na liście, „Dodaj przepis do tej przyprawy” w formularzu po wyborze przyprawy (w wyborze przyprawy bez przepisu mają dopisek „(bez przepisu)”), „Dodaj przepis” w podglądzie przyprawy i w oknie etykiet przy przyprawie bez dania; otwiera okno „Przepisy przyprawy” z kafelkami i listą „Dodaj przepis…”, z przyciskami „Zapisz”, „Nowy przepis” (przechodzi do Gotuję z przypisaniem do tej przyprawy) i „Anuluj”; zapis zmienia tylko przypisane przepisy (magazyn, skład i obrazek bez zmian); filtr „tylko bez przepisu” nad listą przypraw ułatwia znalezienie przypraw do uzupełnienia i działa też przy „Zaznacz wszystko”,
  - składniki mają formę „150 g ryżu jaśminowego”, „2 łyżki oleju”, „2 szalotki” (sztuki bez odmiany), „sól do smaku”: nazwa idzie do dopełniacza po jednostce miary (słownik popularnych składników i proste reguły, `spiceIngLine`); odmiana jest przybliżeniem, więc w oknie etykiet każda przyprawa ma zwijane pole „Skład na etykiecie” (jedna pozycja w wierszu) do ręcznej poprawki, zapamiętywanej osobno dla każdego dania (`sp.labelText[idPrzepisu]`), z przyciskiem „Przywróć automatyczny”; poprawka zapisuje się na stałe od razu, a okno mówi to wprost („Zapisano na stałe”, nagłówek „poprawiony ręcznie, zapisany na stałe”); puste pole to etykieta bez składników,
  - tytuł etykiety ma własne pole w oknie etykiet (`sp.labelTitle[idPrzepisu]`, zapis na stałe, „Przywróć nazwę dania”): enter w dowolnym miejscu oddziela duży napis (przed enterem) od mniejszego (po enterze, ok. połowa wielkości, może się zawinąć), tak jak na etykiecie „Łosoś / z masłem”; słowa dużego napisu nie są łamane; lista składników ma większe pismo, większą kropkę punktora i większy odstęp od napisu „SKŁADNIKI”,
  - przed drukiem etykiety bez składników (przyprawa bez przypisanego dania albo danie bez składników) okno informuje o tym i wymaga drugiego kliknięcia „Drukuj”; zmiana czegokolwiek w oknie kasuje zgodę.
- Zakładka „Gotuję” ma na górze wybór „Przygotowuję: Danie / Samą przyprawę”. „Samą przyprawę” to osobny, prosty formularz (nazwa, skład, przypisany przepis, zapis) bez tabeli, pudełek i zamrażarki.
- Zakładka „Gotuję” to trzy ponumerowane kroki (15.11):
  - krok 1 „Składniki” z przełącznikiem trybu: opisowo (tekst i Gemini albo wklejone sumy), tabela (wiersze ręcznie, bez przycisku kodu kreskowego, który tam nie pasował), z przepisu (lista i wyszukiwarka przepisów bazowych); trybu „Z przyprawy” nie ma, bo dublował „Z przepisu” i podgląd przyprawy,
  - tryb pokazuje tylko swoje pola, a wyliczenie jest jedno, więc tryby można łączyć w jednym daniu,
  - krok 2 „Danie i wyliczenie”: nazwa, pudełka, moja część, tabela z sumami, „Zacznij od nowa”, mnożnik porcji: pole, w które można wpisać dowolną liczbę (np. 1,24, od 0,1 do 20, Enter stosuje), z subtelnymi strzałkami skaczącymi po wygodnych wartościach (×0,5 do ×10), i przyciskiem „Powiększ porcje ×N” (albo „Zmniejsz porcje”), który mnoży gramy; zamiast długiego opisu przy „Ile razy więcej?” jest mała ikonka „i” (biała na zielonym kółku, 10 px, z większym obszarem dotyku), której kliknięcie pokazuje objaśnienie („Użyj, jeśli chcesz przeskalować wielkość przygotowanego jedzenia…”), np. do ugotowania podwójnej porcji z przepisu; liczba pudełek zostaje bez zmian, a komunikat o tym mówi,
  - krok 3 „Zapis”: zamrażarka na pierwszym planie, przepis bazowy (z przypisaniem do przyprawy) w zwijanej sekcji,
  - wybrany tryb jest zapamiętany (`settings.cookMode`), komunikat pojawia się w kroku, którego dotyczy.
- Praca na przepisie w „Gotuję” (15.11):
  - krok 2 ma stały przycisk „+ Dodaj wiersz” w każdym trybie (bez przechodzenia do „Tabela”), kursor od razu w nazwie,
  - przy pracy na przepisie (edycja, wczytany przepis, nowy przepis dla przyprawy) u góry kroku 2 jest pasek z opisem sytuacji i przyciskiem „Zapisz zmiany w przepisie” (albo „Zapisz przepis”) oraz „Zakończ i zacznij od nowa”, a komunikat zapisu pojawia się w pasku; zmiana nazwy przed zapisem tworzy nowy przepis, czyli kolejny wariant przyprawy, zapis edytowanego przepisu pod tą samą nazwą nadpisuje go bez pytania, a komunikat mówi tylko „Zapisano zmiany w przepisie” (bez dopisków w rodzaju „przyprawa już go ma”, które brzmiały jak odmowa),
  - po wczytaniu przepisu pole „Opisowo” jest puste, a opis przepisu trzyma szkic (`draft.text`) i dopisuje do niego to, co dodano z pola, więc „Policz” nie dubluje składników, a zapisany posiłek i przepis zachowują opis,
  - wczytanie przepisu przewija do kroku 2,
  - kolumna „Ilość” w tabeli pokazuje zawsze aktualne gramy (także dla ręcznie dodanego składnika, po zmianie gramów i po skalowaniu), a miara domowa, np. „1 łyżeczka”, zostaje; zapis do zamrażarki i przepisu zapisuje aktualną ilość,
  - nazwa składnika w tabeli i w oknie „Edytuj” uzupełnia kcal, białko i błonnik na 100 g najpierw z Twoich przepisów, potem z wbudowanej bazy (np. ryż, kasza bulgur), bez przeskakiwania kursora; uzupełnienie automatyczne zmienia się razem z nazwą, a ręcznie poprawione wartości zostają. Lista podpowiedzi nazw zawiera produkty z bazy.
- Etykiety (15.11):
  - karty w zamrażarce nie mają własnego przycisku etykiet, jest jeden przycisk „Drukuj etykiety”,
  - krok 1: tryb zaznaczania jak w galerii zdjęć, dotknięcie karty zaznacza, pasek z licznikiem, „Zaznacz wszystko”, „Dalej”, „Anuluj”,
  - krok 2: okno z liczbą etykiet dla każdego posiłku (domyślnie tyle, ile pudełek), podglądem i przyciskiem „Drukuj”,
  - wszystkie etykiety idą w jednym wydruku, jedna etykieta na stronę,
  - drukarka etykiet ma 50 mm szerokości: rozmiar etykiety to ustawienie (`settings.label`, domyślnie 50 × 30 mm) zmieniane w oknie druku,
  - „Dodaj i drukuj etykiety” przy nowym posiłku drukuje od razu, w tym samym formacie.
- Druk na Phomemo M02S przez Bluetooth (16.11, gałąź `drukarka-m02s`):
  - integracja aplikacji przez Web Serial (Chrome lub Edge na komputerze), na wirtualnym porcie COM sparowanej drukarki, a nie sterownik systemowy: drukarka nie pojawia się w oknie „Drukuj”; bez Web Serial wszystko działa przez okno przeglądarki,
  - ustawienie „Etykiety drukuj przez” (`settings.printer`: okno przeglądarki albo M02S) jest w Ustawieniach, w karcie „Drukarka etykiet”, i w oknie etykiet („Drukuj przez”); obejmuje też „Dodaj i drukuj etykiety”. Przyciski: „Połącz z M02S” (wybór portu, trzeba wskazać port wychodzący, np. „M02S 'JL_SPP' (COM4)”, numer COM jest inny na każdym komputerze), „Drukuj próbną etykietę” (z kodem QR do sprawdzenia skanerem), „Drukuj N etykiet” w oknie etykiet,
  - sekwencja i opóźnienia odtwarzają sprawdzony skrypt Pythona (`M02S`): ESC @, 1 s, 5 pustych wierszy i bloki rastrowe po 255 wierszy, GS V 1, 5 s, ESC d (domyślnie 3), 2 s, bez resetu; po teście na sprzęcie (marginesy były ok. 2 razy za duże) ostatni blok ma tylko prawdziwe wiersze (bez dopełnienia do 255), a tryb „dokładnie jak biblioteka” (`fill255`) zostaje tylko do testu zgodności bajtów; port 9600, 8N1, bez sterowania przepływem (jak domyślne otwarcie w bibliotece); bajty są zgodne co do bajta z biblioteką phomemo_m02s (MIT, tekst licencji na końcu `index.html`); nie zakładać, że 115200 sprawdzono, ani nie twierdzić, że reset był przyczyną,
  - etykieta idzie jako obraz 576 punktów szerokości w proporcjach ustawionego rozmiaru, te same treści co w oknie drukowania; kod QR rysowany modułami o całkowitej liczbie punktów bez wygładzania; w oknie etykiet podgląd pokazuje dokładnie czarno-biały obraz dla drukarki, a napis nad nim to samo „Podgląd”; opis trybu wielu etykiet (czas jednej etykiety albo czekanie na kliknięcie) jest pod małą ikonką „i” przy „Wiele etykiet”, jak przy mnożniku porcji; nie wyliczać z tego DPI ani fizycznej szerokości papieru,
  - marginesy: puste pasy u góry i u dołu etykiety (wyśrodkowanie treści) są przycinane (`m02sFinal`), a odstępy ustawia się w Ustawieniach w mm, przybliżonych (ok. 12 punktów na mm, bez twierdzenia o dokładnym DPI): przed etykietą (domyślnie 2), po etykiecie (domyślnie 6) i przesuw papieru na końcu w liniach (domyślnie 3, komenda ESC d); dla 50 × 30 mm pusty papier przed treścią spadł ze 103 do 29 wierszy, a po niej z 262 do 72; odstęp po etykiecie nie może być za mały, bo papier musi wyjechać poza krawędź do odrywania,
  - tryb wielu etykiet (`settings.m02s.flow`, w Ustawieniach i w oknie etykiet): „po kolei, z przerwą” (domyślny: etykiety idą same, z opóźnieniami sekwencji) albo „po każdej czekaj na kliknięcie” (po każdej etykiecie oprócz ostatniej pasek pokazuje „Następna etykieta” i „Zakończ”, port zostaje otwarty na czas czekania),
  - kolejka: etykiety idą kolejno, nigdy równolegle, bez automatycznych ponowień; po błędzie albo przekroczeniu limitu zapisu (10 s) kolejka staje, a komunikat podaje, ile wysłano, i ostrzega przed duplikatami; „Zatrzymaj po tej etykiecie” przerywa kolejkę; komunikat końcowy mówi tylko, że wysyłanie się skończyło, a nie że papier wyszedł,
  - długie etykiety (powyżej 510 wierszy, np. etykieta przyprawy 50 × 120 mm, ok. 105 KB): między blokami po 255 wierszy przerwa (ustawienie `settings.m02s.block`, domyślnie 3 s, w Ustawieniach „Przerwa między częściami długiej etykiety”), a po ostatnim bloku czekanie dłuższe o dwie takie przerwy, zanim port się zamknie (1 s nie wystarczył: wyszło ok. 80% etykiety, brakowało końca), bo na drukarce wydruk urywał się „ćwiartkami” (podejrzenie: przepełniony bufor, port bez sterowania przepływem); bajty bez zmian, krótkie etykiety bez przerw; to hipoteza, niesprawdzona na sprzęcie, a strona w tle może spowalniać czasomierze, więc na czas druku zostawić kartę na wierzchu,
  - nie testowano na prawdziwej drukarce (tylko atrapa portu); ewentualne skrócenie opóźnień i dopełnienia za etykietą dopiero po sprawdzeniu na sprzęcie.
- Wyłączony przycisk (np. „Powiększ porcje” przy ×1) ma zwykły kursor-strzałkę i przygaszony wygląd, bez klepsydry, bo z perspektywy użytkownika wyglądała jak błąd; prace w toku pokazuje osobny wskaźnik z kółkiem. (16.11)
- Strona nie skacze na boki: pasek przewijania zawsze zajmuje miejsce (`scrollbar-gutter: stable`), bo przełączenie Danie / Samą przyprawę zmienia wysokość i pasek pojawiał się i znikał. (15.11)
- Na liście przepisów (Przepisy i wyniki „Z przepisu”) przyprawę oznacza tylko zielona wektorowa ikonka papryczki chilli z trzema kuleczkami pieprzu (przepis ją ma albo nie), bez nazwy przyprawy, bo ważne jest samo posiadanie. (15.11)
- Tabela dni w Wadze pokazuje 45 dni, starsze pod przyciskiem „Pokaż starsze dni”. (14.11)
- Wykresy kalorii, białka i błonnika (30 dni) w zwijanej sekcji pod wykresem wagi (27.10):
  - jedna oś na wykres,
  - przerywana linia celu,
  - kolory jak kropki,
  - podpowiedź po najechaniu,
  - szerokość dopasowana do ekranu.

## Pusty start i przykład

- Karta „Pierwszy raz w FRZR?” znika, gdy pojawią się dane (także po wczytaniu kopii albo pierwszym wpisie), bez odświeżania strony. (15.11)
- Nowa osoba zaczyna bez cudzych danych: bez posiłków, wag i celu wagi. (22.10)
- Pod adresem `?demo` aplikacja pokazuje zmyślone dane dwóch osób i niczego nie czyta ani nie zapisuje w przeglądarce. Prawdziwe dane muszą zostać nietknięte. (2.11)
- Na pustej aplikacji karta zachęty z przyciskiem „Pokaż na przykładowych danych”. (2.11)
- „Wyjdź z przykładu” wraca na tę samą stronę bez `?demo` (adres liczony z bieżącego, także dla pliku z dysku o dowolnej nazwie), a nie do katalogu. (15.11)

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
| 15.11 | Etykiety: zaznaczanie wielu posiłków i jeden wydruk, format pod drukarkę etykiet 50 mm. |
| 15.11 | „Gotuję” w trzech krokach z wyborem trybu: opisowo, tabela, z przyprawy, z przepisu. |
| 15.11 | „Gotuję”: osobny formularz samej przyprawy. „Wszystkie posiłki”: zielona strzałka zamiast przycisku „Partie”. |
| 15.11 | Przepisy i przyprawy: „Podgląd” ze składnikami, a „Edytuj” dopiero w oknie podglądu. |
| 15.11 | Przyprawy bez kalorii: nazwa, skład do podglądu i przypisany przepis, którym wypełnia się tabelę przy gotowaniu. |
| 15.11 | Gemini za wolny: wbudowana baza produktów, „Policz” zamiast „Policz z Gemini”, wskaźnik pracy z anulowaniem, „Dodaj” dla tekstu z kaloriami, pamięć wyników Gemini z czyszczeniem w Ustawieniach, przyprawy suche jako 0 kcal. |
| 15.11 | Przycisk 50% przy części pudełka i ustawienie najczęstszego podziału (przy 50/50 albo 100% dwa przyciski zamiast trzech). |
| 15.11 | Przyprawa ma kilka wariantów dania (kilka przepisów). W „Z przyprawy” lista przepisów z opcją „Nowy”, nowy przepis też z formularza przyprawy i z podglądu. Naprawa skakania strony na boki w Gotuję. |
| 15.11 | Niepełny przepis (wiersz bez nazwy, bez gramów albo bez wartości) nie zapisuje się po cichu: blokada albo drugie kliknięcie. |
| 15.11 | Zamiast pola „Przeskaluj gramy ×” strzałki góra/dół i przycisk „Powiększ porcje ×N”. |
| 15.11 | Przegląd logiki dodawania przepisów: pasek zapisu przy pracy na przepisie, „+ Dodaj wiersz” w każdym trybie, brak dublowania składników po wczytaniu przepisu, podpowiadanie wartości z bazy w tabeli i w Edytuj. |
| 15.11 | Bez przycisku „Gotuję” na liście przypraw. Na liście przepisów ikonka przyprawy zamiast jej nazwy. |
| 15.11 | Kolumna „Ilość” zawsze zgodna z gramami. Mnożnik porcji jako pole do wpisania (np. 1,24) z subtelnymi strzałkami. |
| 15.11 | Bez trybu „Z przyprawy” w Gotuję. Przy mnożniku porcji ikonka „i” z objaśnieniem zamiast długiego opisu. |
| 15.11 | Ikonka przyprawy: zielona papryczka chilli z trzema kuleczkami pieprzu (wektor). Ikonka „i” przy mnożniku o połowę mniejsza. |
| 16.11 | Z podglądu przyprawy usunięty przycisk „Nowy przepis dla tej przyprawy”. |
| 16.11 | Druk etykiet bezpośrednio na Phomemo M02S przez Bluetooth (Web Serial), gałąź `drukarka-m02s`: połączenie z portem, próbna etykieta, drukowanie zaznaczonych etykiet kolejką. |
| 16.11 | Po teście na drukarce: marginesy M02S o połowę mniejsze (przycinanie pustych brzegów, ustawialne odstępy) i tryb „po każdej etykiecie czekaj na kliknięcie” obok trybu z przerwą. |
| 16.11 | Skaner: lepsza ostrość i rozdzielczość, czytanie także kodów kreskowych produktów (zjem czekoladę), QR na etykiecie z danymi porcji (działa na telefonie bez danych), bez przycisku kodu kreskowego w Gotuję → Tabela. |
| 16.11 | Skaner zamyka się po udanym skanie, a potwierdzenie jest na stronie „Dziś”. |
| 16.11 | Bez klepsydry nad wyłączonymi przyciskami. |
| 16.11 | Magazyn przypraw (licznik przygotowanych sztuk) i etykiety przypraw jak wzór: ramka z nazwą dania i obrazkiem, skład obrócony o 180°; druk z formularza przygotowania, z podglądu, z listy i po zaznaczeniu wielu. |
| 16.11 | Etykieta przyprawy: tylko składniki dania („150 g ryżu jaśminowego”) z ręczną poprawką, pytanie przed drukiem bez składników, w formularzu „Zapisz i podgląd etykiety” zamiast druku. |
| 16.11 | Dodawanie przepisu do istniejącej przyprawy bez przepisu wprost z listy, formularza, podglądu i okna etykiet, filtr „tylko bez przepisu”. |
| 16.11 | Ręcznie poprawiony skład etykiety przyprawy ma być zapisany na zaś: zapis jest automatyczny, okno pokazuje potwierdzenie. |
| 16.11 | Etykieta przyprawy: tytuł z enterem (duży napis i mniejszy pod nim), większe pismo i kropki listy, większy odstęp od „SKŁADNIKI”. Do bazy właścicielki dopisano 4 dania i przyprawy z obrazkami z jej etykiet (kopia danych poza repozytorium). |
| 16.11 | Etykieta przyprawy na M02S urywała się po 1/4 (bufor): przerwy 1 s między blokami długich etykiet. |
| 16.11 | Po teście: przerwa między częściami długiej etykiety 3 s, ustawiana w Ustawieniach, i dłuższe czekanie na końcu (brakowało ok. 20%). |
| 13.11 | Usuwanie z zamrażarki elementów widocznych tylko u jednej osoby (zawieszone resztki dzielonych pudełek). Przycisk przeniesiony z karty do okna „Edytuj”. |
