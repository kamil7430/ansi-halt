## Obiekty

* **Wiele miast**, z których każde podzielone jest na **strefy geograficzne**.
* **Wielu kierowców** przypisanych do miasta, gdzie każdy znajduje się w jednym ze stanów:
* Dostępny,
* W trakcie przejazdu,
* Przerwa.


* **Wiele pojazdów** przypisanych do kierowców, dzielących się na kategorie:
* Standard (4 miejsca),
* Premium (4 miejsca, wyższy standard, wyższa ocena kierowcy),
* Van (6 miejsc).


* **Cennik bazowy** uwzględniający strefę geograficzną, dystans oraz kategorię pojazdu.

---

## Reguły dotyczące wyceny i dopasowania

### Nie do złamania (Sztywne)

* Cena przejazdu jest zależna od odległości między punktem A i B, wybranej kategorii pojazdu (Standard / Premium / Van) oraz aktualnego mnożnika popytu.
* Pojazdy kategorii Premium i Van mają wyższą stawkę bazową niż kategoria Standard.
* W trybie planowanym system musi bezwzględnie zagwarantować dostępność kierowcy o wyznaczonej godzinie (rezerwacja zasobów z wyprzedzeniem).

### Miękkie

* System dąży do przydzielenia najbliższego kierowcy w linii prostej/czasowej do punktu A (w trybie natychmiastowym).
* W trybie "W trakcie przejazdu" kierowca może otrzymywać kolejne oferty (podwieszanie zleceń), aby zminimalizować czas przestoju, o ile zakończenie obecnego kursu koreluje z nowym punktem startowym.

### Rozważone, odrzucone

* Dynamiczny overbooking (przydzielanie tego samego kierowcy do dwóch nakładających się rezerwacji planowanych).
* Zmiana ceny gwarantowanej w trakcie trwania okna rezerwacji (2 minuty).

---

## Reguły dotyczące rezerwacji i płatności

### Sztywne

* Użytkownik widzi cenę gwarantowaną przez **dokładnie 2 minuty**. Po tym czasie system musi przeliczyć cenę na nowo, uwzględniając aktualny mnożnik popytu.
* Kliknięcie "Zamawiam" wymaga natychmiastowej i udanej pre-autoryzacji środków na karcie pasażera. Brak środków uniemożliwia uruchomienie procesu poszukiwania kierowcy.
* Kierowca ma **dokładnie 15 sekund** na akceptację lub odrzucenie oferty przejazdu.

### Miękkie

* Po przyjeździe kierowcy na punkt A, system daje pasażerowi darmowy czas na wejście do pojazdu wynoszący **2 minuty**. Po tym czasie naliczana jest opłata za oczekiwanie (soft limit – kierowca decyduje, jak długo realnie poczeka przed ewentualnym anulowaniem).
* Koordynaty GPS z aplikacji kierowcy powinny być wysyłane **co 3 sekundy**, jednak chwilowe opóźnienia sieciowe nie powodują natychmiastowego wylogowania kierowcy z systemu.

### Rozważone, odrzucone

* Limitowanie liczby przejazdów per użytkownik w godzinach szczytu (prime time).
* Możliwość negocjowania ceny przez pasażera wewnątrz aplikacji.

---

## Procesy

### 1. Wycena i zlecenie przejazdu (Tryb natychmiastowy)

1. Pasażer otwiera aplikację i podaje punkt A oraz punkt B.
2. System oblicza dystans oraz pobiera aktualny mnożnik popytu (np. ze względu na warunki pogodowe lub godziny szczytu).
3. System proponuje gwarantowaną cenę ważną przez 2 minuty oraz estymowany czas dojazdu.
4. Pasażer wybiera kategorię pojazdu (Standard/Premium/Van) i klika "Zamawiam".
5. System wykonuje pre-autoryzację środków na karcie płatniczej pasażera.
6. System wysyła zdarzenie o rezerwacji przejazdu do kolejki zleceń.
7. System znajduje najbliższego dostępnego kierowcę spełniającego kryteria i wysyła mu ofertę.
8. Kierowca akceptuje ofertę w ciągu 15 sekund.
9. System wysyła pasażerowi powiadomienie z danymi auta (numer rejestracyjny, model) oraz czasem dojazdu.

#### Przypadki błędów:

* Jeśli miną 2 minuty przed kliknięciem "Zamawiam", konfiguracja ceny wygasa i system wymusza odświeżenie oferty.
* Jeśli pre-autoryzacja karty w pkt. 5 zakończy się niepowodzeniem, proces jest przerywany błędem ("Brak środków").
* Jeśli kierowca nie zareaguje w ciągu 15 sekund (lub odrzuci ofertę), system natychmiast wraca do pkt. 7 i szuka kolejnego kierowcy.

---

### 2. Realizacja i zakończenie przejazdu

1. Kierowca jedzie do punktu A (pasażer śledzi trasę na żywo).
2. Kierowca dojeżdża na miejsce i klika w aplikacji "Jestem na miejscu".
3. Pasażer wsiada do pojazdu.
4. Kierowca klika "Ruszam".
5. [Opcjonalnie] W trakcie jazdy do punktu B kierowca otrzymuje i akceptuje kolejną ofertę (kolejkowanie zleceń).
6. Kierowca dociera do punktu B i klika "Zakończ kurs".
7. System finalizuje pobranie zablokowanych środków z karty pasażera.
8. Pasażer ma możliwość wystawienia recenzji (review) kierowcy.
9. Kierowca ma możliwość wystawienia recenzji (review) pasażerowi.

#### Przypadki błędów:

* Jeśli pasażer nie pojawi się w aucie po 2 minutach od kliknięcia "Jestem na miejscu", system zaczyna doliczać opłatę za oczekiwanie według cennika.

#### Dodatkowe funkcje:

* W trakcie kroku 5 (jazda do punktu B), kierowca może zmienić swój status na "Przerwa" po zakończeniu bieżącego kursu – wtedy system nie będzie podsuwał mu kolejnych zleceń w trakcie jazdy.

---

### 3. Rezerwacja przejazdu (Tryb planowany)

1. Pasażer otwiera aplikację, podaje punkt A, punkt B oraz planowaną datę i godzinę podstawienia.
2. System oblicza bazową stawkę i wyświetla cenę dla wybranej kategorii pojazdu.
3. Pasażer klika "Zarezerwuj przejazd".
4. System wykonuje pre-autoryzację (lub pełne pobranie środków w zależności od polityki firmy) w celu zabezpieczenia rezerwacji.
5. System blokuje dostępność jednego z kierowców w danej strefie w wybranym oknie czasowym, gwarantując realizację.
6. System wysyła pasażerowi potwierdzenie rezerwacji planowanej.

#### Przypadki błędów:

* Jeśli system nie jest w stanie zagwarantować wolnego pojazdu danej kategorii na wybraną godzinę (np. wszystkie zasoby są już zarezerwowane), rezerwacja nie jest otwierana, a pasażer otrzymuje komunikat o braku dostępności.
