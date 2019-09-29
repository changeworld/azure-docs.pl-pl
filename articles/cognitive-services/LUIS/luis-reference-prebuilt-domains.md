---
title: Wbudowane odwołanie do domeny — LUIS
titleSuffix: Azure Cognitive Services
description: Dokumentacja ze wstępnie utworzonych domen, które są wstępnie kolekcjami intencje i podmioty z Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 7e2b5c5c5d2ca4c0d6ab820866341c5f30082c5f
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672773"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Wstępnie skompilowane odwołanie do domeny dla aplikacji LUIS
Ta dokumentacja zawiera informacje na temat [ze wstępnie utworzonych domen](luis-how-to-use-prebuilt-domains.md), które są wstępnie utworzonych kolekcji intencje i podmioty, które są oferowane usługi LUIS.

[Domeny niestandardowe](luis-how-to-start-new-app.md), z drugiej strony, rozpoczynać się nie intencje i modeli. Można dodać żadnych wbudowanych domeny intencje i podmioty do modelu niestandardowego.

## <a name="supported-domains-across-cultures"></a>Obsługiwane domeny między kulturami

W poniższej tabeli zestawiono aktualnie obsługiwane domeny. Obsługa języka angielskiego jest zwykle większa niż inne. 

| Typ jednostki       | PL-US      | ZH-CN   | DE    | PW     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| [Kalendarz](#calendar)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Zawiadomienia](#communication)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Wiadomość e-mail](#email)           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [HomeAutomation](#homeautomation)           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Uwagi](#notes)      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Nabywc](#places)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [RestaurantReservation](#restaurantreservation)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Czynność](#todo)     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Uruchamiane](#utilities)          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Meteorologiczn](#weather)        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Web](#web)    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Wbudowane domeny nie są **obsługiwane** w programie:

* Francuski (kanadyjski)
* Hindi
* Hiszpański (Meksyk)

## <a name="description-for-luis-prebuilt-domains"></a>Opis dla prekompilowanych domen LUIS
## <a name="calendar"></a>**Kalendarz**
W kalendarzu znajdują się informacje o spotkaniach osobistych i terminach, a nie na zdarzeniach publicznych (na przykład w planach świata, kalendarzu zdarzeń Seattle) ani w kalendarzu ogólnym (na przykład dzień dzisiejszy to, co się stało, gdy jest to dzień robocizny).
### <a name="intents"></a>**Intencji**
Nazwa celu | Opis | Przykłady
---------|----------|---------------
 AcceptEventEntry | Zaakceptuj (n) termin/spotkanie/wydarzenie w kalendarzu. | Zaakceptuj termin. <br> Zaakceptuj zdarzenie <br> Zaakceptuj Dzisiejsze spotkanie.
 Cancel | Anuluj trwającą akcję przez asystenta wirtualnego, na przykład anulując proces tworzenia spotkania. <br> _**Uwaga**: Ten cel obejmuje głównie akcję "Anuluj" w scenariuszu kalendarza. Jeśli potrzebujesz wyrażenia ogólnego dla "Cancel", Skorzystaj z opcji "Anuluj" w domenie **narzędzi** ._ | Po prostu Anuluj zdarzenie. <br> Nie, po prostu anuluję termin.
 ChangeCalendarEntry | Zmień lub ponownie Zaplanuj wpis kalendarza. | Zaplanuj moje 6 godzin termin "jutro do 2" <br> Zmień harmonogram terminu lekarza dla 5 PM <br> Ponownie Zaplanuj obiad z pracownik3 Olson do piątku. <br> Zmień czas zdarzenia.
 CheckAvailability | Znajdź dostępne dla terminu lub spotkania na kalendarz użytkownika lub inną osobę kalendarz. | Gdy jest dostępne w celu spełnienia Jim? <br> Pokaż, gdy Karoliny jest dostępny jutro. <br> Chris jest bezpłatne w sobotę?
 Potwierdź | Potwierdź, czy wykonać operację/akcję na podstawie poprzedniego zamiaru. <br> _**Uwaga**: Ten cel obejmuje głównie akcję "Potwierdź" dla scenariusza kalendarza. Jeśli potrzebujesz bardziej ogólnych wyrażeń w "Confirm", Skorzystaj z opcji "Potwierdź" w domenie **narzędzi** ._| To poprawne, Utwórz spotkanie <br> Tak, Dziękujemy, Połącz się ze spotkaniem.
 ConnectToMeeting | Połącz się ze spotkaniem. | Połącz do 11:00 rozmowy konferencyjnej z Adam. <br> Zaakceptuj połączenie z budżetem.
 ContactMeetingAttendees | Skontaktuj się z uczestnikami spotkania. | Poinformuj o spotkaniu, które jest uruchomione z opóźnieniem do 3:00. <br> Powiadom współpracowników o 8 spotkaniach, które muszą zostać uruchomione o godzinie 8:30.
 CreateCalendarEntry | Dodaj nowy element jednorazowe do kalendarza. | Utwórz spotkanie dotyczące omawiania problemów. <br> Utwórz spotkanie zabc@microsoft.com
 DeleteCalendarEntry | Żądanie usunięcia wpisu kalendarza.| Anuluj mojej termin Karoliny. <br> Usuń mój 9 rano spotkania. <br> Usuń moje wydarzenie.
  FindCalendarEntry | Otwórz aplikację kalendarza lub przeszukaj wpis w kalendarzu. | Znajdź stomatologowi Przejrzyj terminu. <br> Pokaż mój kalendarz. <br> Co znajduje się w moim kalendarzu w czwartek?
 FindCalendarWhen | Sprawdź godzinę, kiedy ma miejsce harmonogram. | Kiedy mogę spełnić wymagania bursztynowe i Susan? <br> Kiedy mam zaplanowaną brunch? 
 FindCalendarWhere | Sprawdź miejsce, w którym ma miejsce harmonogram. | Gdzie są moje terminy jutro? <br>Gdzie jest spotkanie z Stacyem i Michael jutro w przypadku obiadu?
  FindCalendarWho | Sprawdź uczestników, którzy będą uczestniczyć w harmonogramie docelowym. | Chcę, aby w przyszłości wszyscy potwierdzili się na spotkaniu w dniu 2. <br> Czy Jim będzie na następnym spotkaniu pielęgniarek/pielęgniarzy?
 FindCalendarDetail | Sprawdź i Pokaż szczegóły harmonogramu. | Chcę podać mi szczegółowe informacje o spotkaniu, które zaplanowali z moim współpracownikem.
 FindDuration | Sprawdź czas trwania. | Ile czasu potrzeba na pobranie artykułów spożywczych? <br> Jak długo mam korzystać z obiadu?
 FindMeetingRoom | Znajdź dostępne pokoje spotkań. | Co to są moje pokoje? <br> Nowa lokalizacja spotkania, Znajdź ją.
 GoBack | Wróć do ostatniego kroku lub elementu.  <br> _**Uwaga**: Zapoznaj się z domeną **Utilities** , aby uzyskać więcej GoBack ogólnych wyrażenia długości._ | Poprzedni <br> Powrót do ostatniej wiadomości e-mail.
 Reject | Użytkownik odrzuca proponowany asystenta wirtualnego. <br> _**Uwaga**: Zapoznaj się z domeną **Narzędzia** spisu treści, aby uzyskać więcej informacji o wyrażenia długości ogólnej._ | Nie trzeba ustawiać zdarzenia. <br> Mam inne rzeczy, które należy wykonać w tym czasie.
ShowNext | Sprawdź następne zdarzenie. <br> _**Uwaga**: Zapoznaj się z domeną **Utilities** , aby uzyskać więcej ShowNext ogólnych wyrażenia długości._ | Podaj moje następne wydarzenie. <br> Co dalej w kalendarzu?
 ShowPrevious | Sprawdź poprzednie zdarzenie. <br> _**Uwaga**: Zapoznaj się z domeną **Utilities** , aby uzyskać więcej ShowPrevious ogólnych wyrażenia długości._ | Co to jest harmonogram przed tym?
 TimeRemaining | Sprawdź pozostały czas do następnego zdarzenia. | Wyświetlaj, ile czasu mam przed spotkaniem. <br> Wyświetlaj ilość czasu, jaki mam przed rozpoczęciem następnego spotkania.
 
### <a name="entities"></a>**Obiekty**
Nazwa jednostki | Typ jednostki | Opis | Przykłady | Miejsca
-------|-----------------------|-------------|---------|--------
Nazwa kontaktu | personName | Imię i nazwisko osoby kontaktowej lub uczestnika spotkania. | Poznaj z **Betsy**. <br>  Zapoznaj się z **Aubrey** dnia 3 lipca o godzinie 7 | Betsy <br> Aubrey <br> Amy 
DestinationCalendar | ułatwia | Nazwa kalendarza docelowego. | obiad ze swoją usługą MOM wtorek 12 **Personal** <br> Użyj mojego kalendarza **Google** jako mojego kalendarza domyślnego. <br> Aktualizowanie klasy Yoga do Mon wed o godzinie 3 Lista w kalendarzu **osobistym** . | Google <br> Tytuł <br> firmy <br> główną
Duration | datetime | Czas trwania spotkania, terminu lub pozostałego czasu. | Dodaj do spotkania kalendarzowego z obserwują, aby omówić szczegóły Scholarship jutro o godzinie 11 na **20 minut**. <br> Dodaj do kalendarza zdarzenie o godzinie Subway w piątek I będzie jedzenia z Thomasem na **godzinę o godz** . 9 | godzina <br> 2 dni <br> 20 minut 
EndDate | datetime | Data zakończenia spotkania lub terminu. | Dodawanie uzgodnienia kalendarza w korytarzu o niskich, Maria od trzeciego do **Mary piąty** | Jan piąty  
Godzina zakończenia | datetime | Godzina zakończenia spotkania lub terminu. | może być od 2 30 do **trzech** | trzy 
Location | ułatwia | Lokalizacja elementu kalendarza, spotkania lub terminu.  Adresy, miasta i regiony są dobrym przykładem lokalizacji. | Umieść spotkanie w **Fremont** , aby umieścić tablet w Birmy <br> spotkanie Pro Bono w **Edina** | 209 Nashville w przypadku treningów <br> 897 pancake DOM <br> Garażu 
MeetingRoom | ułatwia | Miejsce na spotkanie lub termin. | Dodaj do spotkania kalendarza służbowego z Jake o godzinie 2 w jej **biurze** w tym piątek | jego biuro <br> Pokój konferencyjny <br> Pokój 2
MoveEarlierTimeSpan | datetime | Czas, przez który użytkownik chce przenieść wcześniej spotkanie lub termin. | Przenieś moją datę obiadu w przód o **30 minut**. | 30 minut <br> dwie godziny 
MoveLaterTimeSpan |  datetime | Czas, przez który użytkownik chce później przenieść spotkanie lub termin. | Wypchnij moje spotkanie za pomocą pola Storczykowy z powrotem o **4 godziny**. | 4 godziny <br> 15 minut 
OrderReference | ułatwia | Numer porządkowy lub względną pozycja na liście identyfikacji elementu do pobrania. | Jaki jest mój przyszły termin na przyszłość? | dalej
OriginalEndDate | datetime | Pierwotna Data zakończenia spotkania lub terminu. | Zmiana mojego urlopu kończącego się w **piątek** na poniedziałek | Piątek 
OriginalEndTime | datetime | Pierwotny czas zakończenia spotkania lub terminu. | Nastąpi przejście o **3** do 4 | 3
OriginalStartDate | datetime | Oryginalna Data rozpoczęcia spotkania lub terminu. | Zmień termin " **jutro**" z 10 rano do środy o godzinie 9  | jutro 
OriginalStartTime | datetime | Pierwotny czas rozpoczęcia spotkania lub terminu. | Zmień termin "jutro" z **10 rano** do środy o godzinie 9 | 10 rano
Kontener positionreference | ordinal | Pozycja bezwzględna na liście, identyfikująca element do pobrania. | **Druga** | sekunda <br> Nie. 3 <br> numer 5
RelationshipName obiektu | list | Nazwa relacji kontaktu. | Dodaj obiad o godz. 1:00 z moją **żona** | żona <br> mężem <br> siostrzan 
Gniazdo | ułatwia | Atrybut użytkownik chce zbadać lub edytować. | Zmień **lokalizację** zdarzenia <br> Zmień **czas** na siódmy | location <br> time 
StartDate | datetime | Data rozpoczęcia spotkania lub terminu. | Utwórz spotkanie w **środę** o godzinie 4 | Środa 
Godzina rozpoczęcia | datetime | Godzina rozpoczęcia spotkania lub terminu. | Utwórz spotkanie w środę o godzinie **4** | 4 godziny
Subject | prosty, wzorzec. Ile | Temat, taki jak tytuł spotkania lub terminu. | Jaki czas to spotkanie **przygotowujące stronę** ? | Zwiększ wcięcie <br> Obiad z Julia 
Message | prosty, wzorzec. Ile | Komunikat do wysłania do uczestników. | Ostrzegaj uczestników spotkania o obiadie, który **zostanie późny**. | Zostanie opóźnione

## <a name="communication"></a>**Zawiadomienia**
Żądania wykonania wywołań, wysyłania tekstu/ISP, znajdowania i dodawania kontaktów oraz innych żądań związanych z komunikacją (zwykle wychodzące). _Nazwa kontaktu_ wyrażenia długości nie należy do domeny komunikacji.

### <a name="intents"></a>**Intencji**
Nazwa celu | Opis | Przykłady
---------|----------|---------
AddContact | Dodaj nowy kontakt do listy kontaktów użytkownika. | Dodaj nowy kontakt.  <br>   Zapisz tę liczbę i umieść ją jako Jan.
Addflag | Dodawanie flagi do wiadomości e-mail | Oflaguj tę wiadomość e-mail <br> Dodaj flagę do tej wiadomości e-mail.
AddMore | Dodaj więcej na temat wiadomości e-mail lub tekstu, jako część stopniowy kompozycji e-mail lub SMS. | Dodaj więcej do tekstu.  <br>   Dodaj więcej do treści wiadomości e-mail.
Odpowiedź | Odpowiedzi na przychodzące połączenia telefonicznego. | Odpowiedz na wywołanie.  <br>   Wybierz ją.
AssignContactNickname | Przypisać pseudonim kontaktu. | Zmień Tomasz na tata.  <br>   Edytuj pseudonim Jim. <br>   Dodaj pseudonim do Patti Owens.
Wywołania zwrotnego | Zwróć połączenie telefoniczne. | Wywołanie zwrotne.
CallVoiceMail | Połącz poczty głosowej użytkownika. | Połącz mnie z moim polem poczty głosowej. <br>Poczta głosowa. <br>   Zadzwoń do poczty głosowej.
Cancel | Anuluj operację. | Anuluj. <br>   Zakończ.
CheckIMStatus | Sprawdź stan kontaktu w wiadomości błyskawicznej. | Stan online przez Jim ustawiono język? 
CheckMessages | Wyszukaj nowe wiadomości lub wiadomości e-mail. | Sprawdź moją skrzynkę odbiorczą <br>  Czy mam nową pocztę?
Potwierdź | Potwierdź akcję. | Tak, wyślij ją. <br> Potwierdź, że chcę wysłać tę wiadomość e-mail.
EndCall | Zakończ połączenie telefoniczne. | Rozłącz połączenie. <br> Zakończ.
FindContact | Znajdowanie informacji kontaktowych według nazwy. | Znajdź numer MUM. <br>   Pokaż numer Karoliny.
FinishTask | Zakończ bieżące zadanie. | Gotowe <br> To wszystko.
TurnForwardingOff | Wyłącz funkcję przekazywania połączeń. | Zatrzymaj przekazywanie moich wywołań. <br> Wyłącz przekazywanie połączeń.
TurnForwardingOn | Włącz przekazywanie połączeń. | Przekazywania z 3333 Moje wywołania <br>  Przełącz na przekazywanie połączeń do 3333.
GetForwardingsStatus | Uzyskaj bieżący stan przekazywania połączeń. | Czy włączone jest Moja przesyłanie dalej wywołania? <br>   Powiadom mnie, jeśli moje wywołanie ma stan włączony lub wyłączony.
Getnotifications | Pobierz powiadomienia. | Otwórz moje powiadomienia <br>   Czy mogę sprawdzić moje telefony powiadomienia w serwisie Facebook
GoBack | Wróć do poprzedniego kroku. | Wróć do usługi twitter <br>   Przejść wstecz <br>   Przejdź wstecz
Zignoruj | Ignoruj przychodzących połączeń. | Nie można znaleźć odpowiedzi <br>   Ignoruj wywołania
IgnoreWithMessage | Ignoruj przychodzących połączeń i odpowiedzi z tekstem zamiast tego. | Nie można znaleźć odpowiedzi to wywołanie, ale zamiast tego wysłać wiadomość. <br>   Ignoruj i wysłać ponownie wiadomość.
Wybierania | Rozpoczynanie rozmowy telefonicznej. | Jim wywołania <br>   Wybierz numer 311.
FindSpeedDial | Znajdź numer szybkie, wybieranie numeru, numer telefonu jest ustawiony na i na odwrót. | Co to jest mój numer wybierania 5? <br>   Czy mają szybkość wybierania zestaw? <br>   Co to jest numer wybierania 941-5555-333?
Prześlij dalej | Prześlij dalej wiadomość e-mail | Przekaż tę wiadomość e-mail do Grega.
ReadAloud | Przeczytaj wiadomości lub wiadomości e-mail do użytkownika. | Przeczytaj tekst. <br>   Co ona powiedzieć wiadomości?
PressKey | Naciśnij przycisk lub numer na klawiaturze numerycznej. | Wybierania star. <br>   Naciśnij klawisz 1 2 3.
QueryLastText | Wysyła zapytanie do ostatniego tekstu lub komunikatu. | Kto je utworzył? <br>   Kto niedawno z nich wysyłał wiadomość?
Wybierz ponownie | Wybierz ponownie lub ponownie wywołaj liczbą. | Wybierz ponownie. <br>   Wybierz ponownie Moje ostatnie wywołanie.
Reject | Odrzucanie połączenia przychodzącego. | Odrzuć wywołanie <br>   Nie można odebrać <br>   Nie są dostępne w tej chwili i wywoła ponownie później.
Odpowiedz | Odpowiedz na wiadomość. | Odpowiedz na Lore Hound <br>   Odpowiedz, wpisując jestem w mój sposób
SearchMessages | Przeszukaj komunikaty w pewnych warunkach. | Czy można wyszukiwać moje wiadomości e-mail wysyłane przez Janina?
SendEmail | Wyślij wiadomość e-mail. Celem tego mają zastosowanie do wiadomości e-mail, ale nie wiadomości SMS. | Wyślij wiadomość e-mail do wód Jan: Jan, ten obiad został Splendid. <br>   Wyślij wiadomość e-mail do Roberta.
SendMessage | Wyślij wiadomość SMS lub wiadomości błyskawicznej. | Wyślij wiadomość SMS, Chris i Karoliny <br>   Greg wiadomości w serwisie Facebook <br>   
SetSpeedDial | Ustaw skrót wybierania szybkość dla numeru telefonu kontaktu. | Ustaw szybkiego wybierania, jeden dla Karoliny. <br>   Skonfiguruj szybkie wybieranie dla programu Microsoft Operations Manager.
ShowCurrentNotification | Pokaż bieżące powiadomienia. | Czy istnieją nowe powiadomienia? <br> Pokaż mi powiadomienie.
ShowNext | Zobacz następną sekcję, na przykład listę wiadomości SMS lub wiadomości e-mail. | Pokaż następny. <br>   Przejdź do następnej strony.
ShowPrevious | Na przykład, zobacz poprzedni element na liście wiadomości SMS lub wiadomości e-mail. | Pokaż poprzedni. <br>   Ubiegł. <br>   Przejdź do poprzedniego.
TurnSpeakerOff | Wyłącz telefonu osoby mówiącej. | Zwiększyły się ze mną osoby mówiącej. <br>   Wyłącz głośnomówiącego.
TurnSpeakerOn | Włącz telefonu osoby mówiącej. | Tryb głośnomówiącego. <br>   Umieść głośnomówiącego na.

### <a name="entities"></a>**Obiekty**
Nazwa jednostki | Typ jednostki | Opis | Przykłady | Miejsca
------|-------|----------|--------------|---------------
Attachment | ułatwia | Załącznik, który użytkownik chce wysłać pocztą SMS lub wiadomość e-mail. | Wyślij **plik** pocztą e-mail z programu OneNote. <br> Wyślij mój **dokument** dla gospodarstw domowych do Katie. | file <br> usługę
AudioDeviceType | ułatwia | Typ urządzenia audio (głośnik, słuchawki, mikrofon itp.). | Odpowiedz przy użyciu **rąk bezpłatnych**. <br> Ponownie wybierz numer **telefonu osoby mówiącej**. | mówc <br> bezpłatnie <br> Moduły
Category | ułatwia | Kategoria wiadomości lub wiadomości e-mail musi mieć jasno zdefiniowaną definicję w systemie poczty e-mail, na przykład "nieprzeczytane", "flaga". Opis z nieprawidłowym definicją, na przykład "New" i "ostatnich", nie są kategoriami. | Oznacz wszystkie wiadomości e-mail jako **przeczytane**  <br> Nowa wiadomość e-mail **o wysokim priorytecie** dla Paul | ważne <br> wysoki priorytet <br> odczyt
ContactAttribute | ułatwia | Atrybut kontaktu z informacjami o użytkowniku.| Jakie **urodziny** należy wiedzieć w następnym miesiącu? | dat urodzenia <br> adres <br> Numer telefonu
Nazwa kontaktu | personName  | Nazwa odbiorcy kontaktu lub komunikatu. | Wyślij wiadomość e-mail do **Stevens** | Stevens
Date/Time | datetime | Data i godzina odebrania wiadomości e-mail. | Przeczytaj **dzisiejszą**wiadomość e-mail <br> Kto **dzisiaj**wysłać wiadomość e-mail? <br> kto jest telefonem o godzinie **17:00**? | dzisiaj <br> jutro
DestinationPhone | ułatwia | Użytkownik docelowy chce wywołać lub wysłać tekst do. | Utwórz połączenie z **domem** <br> Wyślij wiadomość tekstową do **strony głównej** | Rodzinny <br> strona główna
EmailAddress | email | Użytkownik adresu e-mail chce wysłać wiadomość lub wykonać zapytanie. | Wyślij wiadomość e-mail doMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | prosty, wzorzec. Ile | Tekst używany jako wiersz tematu wiadomości e-mail. | Utwórz wiadomość e-mail do Davida z podmiotem **Hej**  | Odp: interesujących historii
Klucz | ułatwia | Użytkownik klucza chce naciskać. | Naciśnij klawisz **spacji** . <br> Naciśnij klawisz **9** | Wybierz <br> Star <br> 8
Kreska | ułatwia | Wiersz użytkownik chce użyć do wysłania wiadomości e-mail lub tekstu z programu. | Przeczytaj moją ostatnią wiadomość e-mail z usługi **Hotmail** . <br> Wywołaj Piotr przez **telefon komórkowy**. <br> Wywołaj tato przy użyciu mojej linii **służbowej** .| usługi <br> Skype <br> Brytyjski komórki
SenderName | personName | Nazwa nadawcy. | Odczytaj wiadomość e-mail z firmy **David** <br> Wiadomości e-mail z Chanda | David <br> Chanda
FromRelationshipName | ułatwia | Nazwa relacji nadawcy. | Odczytaj wiadomość od **DAD**. <br> Czy można czytać wszystkie wiadomości SMS z programu **MOM**? | Tato <br> Program 
Message | prosty, wzorzec. Ile |  Komunikat do wysłania jako wiadomość e-mail lub tekstu.  | Wyślij wiadomość e-mail z informacją "**jestem zajęty**". | Jestem zajęty
OrderReference | ułatwia | Numer porządkowy lub względną pozycja na liście identyfikacji elementu do pobrania. | Jaki był **ostatnio** wysłany komunikat? <br> Przeczytaj **najnowszą** wiadomość e-mail Nokia. <br> Odczytuj **nowe** wiadomości tekstowe. | last <br> najnowsza <br> Aktualne <br> najnowszych
Kontener positionreference | prosta, porządkowa | Numer porządkowy lub względną pozycja na liście identyfikacji elementu do pobrania.| Jaki był **pierwszy** wysłany komunikat? <br> **Trzecia** .| Pierwsze <br> trzeciej
phoneNumber | phoneNumber | Użytkownik numeru telefonu chce zadzwonić lub wysłać tekst do. | Wyślij tekst do **4 1 5 6 8 4 5 2 8 4** | 3525214446
RelationshipName obiektu | ułatwia | Nazwa relacji osoby kontaktowej lub odbiorcy wiadomości. | Wyślij wiadomość e-mail do mojego **żona** | żona
SearchTexts | prosta, wzorzec. any | Teksty używane do filtrowania wiadomości e-mail lub wiadomości | Przeszukaj wszystkie wiadomości e-mail zawierające ciąg "**Surface Pro**" | Surface Pro
Szybkie | ułatwia | Szybki numer telefonu. | Wywołanie **3 4 5**. <br> Ustaw **szybkość wybierania numeru**. | 345 <br> 5

## <a name="email"></a>**Wiadomość e-mail**
Poczta e-mail jest poddomeną domeny *komunikacji* . Obejmuje ona głównie żądania wysyłania i odbierania wiadomości za poorednictwem wiadomości e-mail.
### <a name="intents"></a>**Intencji**
Nazwa celu | Opis | Przykłady
---------|----------|---------
AddMore | Dodaj więcej na temat wiadomości e-mail lub tekstu, jako część stopniowy kompozycji e-mail lub SMS. | Dodaj więcej do treści wiadomości e-mail.
Cancel | Anuluj operację. <br> ***Uwaga**: Aby uzyskać więcej **informacji,** zapoznaj się z tematem domena usługi wyrażenia długości. * | Anuluj. Nie wysyłaj go. <br> Zakończ.
CheckMessages | Sprawdź, czy nie są dostępne nowe wiadomości/wiadomości e-mail bez zapytań warunkowych. Jeśli istnieje jakikolwiek warunek, wyrażenia długości należy do zamiaru *SearchMessage* . | Sprawdź moją skrzynkę odbiorczą. <br> Czy mam nowe wiadomości e-mail? 
Potwierdź | Potwierdź trwającą akcję powiązaną z przetwarzaniem wiadomości e-mail. <br> ***Uwaga**: Aby uzyskać więcej informacji, zapoznaj się z domeną **Narzędzia** wyrażenia długości. * | Tak, wyślij ją. <br> Potwierdzam, że chcę wysłać tę wiadomość e-mail.
Usuwanie | Usuń wiadomość e-mail lub pocztę przefiltrowaną przez niektóre warunki. | Umieść wiadomość e-mail w koszu <br> Pusta Skrzynka odbiorcza. <br> Usuń wiadomość e-mail z Jan.
ReadAloud | Przeczytaj wiadomości lub wiadomości e-mail do użytkownika. <br> ***Uwaga**: Zapoznaj się z domeną **Utilities** , aby uzyskać więcej ReadAloud ogólnych wyrażenia długości. *  | Przeczytaj wiadomość e-mail wysłaną przez Mary.
Odpowiedz | Odpowiedz na komunikat z bieżącą wiadomością e-mail. | Utwórz odpowiedź na wiadomość e-mail. <br> Odpowiedz, wpisując "Dziękuję, że bardzo wiele, najlepsze uwagi, Jan".
SearchMessages | Przeszukaj komunikaty według pewnych warunków, w tym nazwy nadawcy, godziny i tematu. | Pokaż mi komunikaty z Nisheen. <br> Czy można wyszukiwać moje wiadomości e-mail zatytułowane "ABC"?
SendEmail | Wyślij wiadomość e-mail. | Wyślij wiadomość e-mail na Jan: Jan, ten obiad został Splendid. <br> Wyślij wiadomość e-mail do Roberta.
ShowNext | Zobacz następne elementy listy wiadomości tekstowych lub wiadomości e-mail. <br> ***Uwaga**: Zapoznaj się z domeną **Utilities** , aby uzyskać więcej ShowNext ogólnych wyrażenia długości. * | Pokaż następny. <br> Przejdź do następnej strony.
ShowPrevious | Pokaż poprzednie elementy listy wiadomości tekstowych lub wiadomości e-mail. <br> ***Uwaga**: Zapoznaj się z domeną **Utilities** , aby uzyskać więcej ShowPrevious ogólnych wyrażenia długości. * | Pokaż poprzedni. <br> Ubiegł. <br> Przejdź do poprzedniego.
Prześlij dalej | Prześlij dalej wiadomość e-mail. | Przekaż tę wiadomość e-mail do Grega.
Addflag | Dodaj flagę do wiadomości e-mail. | Oflaguj tę wiadomość e-mail <br> Dodaj flagę do tej wiadomości e-mail.
QueryLastText | Wyślij zapytanie do ostatniej wiadomości e-mail. | Kto wysłał do mnie wiadomość e-mail? <br> Kto niedawno wysłać wiadomość e-mail?


### <a name="entities"></a>**Obiekty**
Nazwa jednostki | Typ jednostki | Opis | Przykłady | Miejsca
------|-------|----------|--------------|---------------
Attachment | ułatwia | Załącznik, który użytkownik chce wysłać pocztą SMS lub wiadomość e-mail. | Wyślij **plik** pocztą e-mail z programu OneNote. <br> Wyślij mój **dokument** dla gospodarstw domowych do Katie. | file <br> usługę
Nazwa kontaktu | personName  | Nazwa odbiorcy kontaktu lub komunikatu. | Wyślij wiadomość e-mail do **Stevens** | Stevens
Date | datetime | Data odebrania wiadomości e-mail. | Przeczytaj **dzisiejszą**wiadomość e-mail <br> Kto **dzisiaj**wysłać wiadomość e-mail? | dzisiaj
EmailAddress | email | Użytkownik adresu e-mail chce wysłać wiadomość lub wykonać zapytanie. | Wyślij wiadomość e-mail doMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | prosty, wzorzec. Ile | Tekst używany jako wiersz tematu wiadomości e-mail. | Utwórz wiadomość e-mail do Davida z podmiotem **Hej**  | Odp: interesujących historii
SenderName | personName | Nazwa nadawcy. | Odczytaj wiadomość e-mail z firmy **David** <br> Wiadomości e-mail z Chanda | David <br> Chanda
FromRelationshipName | ułatwia | Nazwa relacji nadawcy. | Odczytaj wiadomość od **DAD**. <br> Czy można czytać wszystkie wiadomości SMS z programu **MOM**? | Tato <br> Program 
Message | prosty, wzorzec. Ile |  Komunikat do wysłania jako wiadomość e-mail lub tekstu.  | Wyślij wiadomość e-mail z informacją "**jestem zajęty**". | Jestem zajęty
Category | ułatwia | Kategoria wiadomości lub wiadomości e-mail musi mieć jasno zdefiniowaną definicję w systemie poczty e-mail, na przykład "nieprzeczytane", "flaga". Opis z nieprawidłowym definicją, na przykład "New" i "ostatnich", nie są kategoriami. | Oznacz wszystkie wiadomości e-mail jako **przeczytane**  <br> Nowa wiadomość e-mail **o wysokim priorytecie** dla Paul | ważne <br> wysoki priorytet <br> odczyt
OrderReference | ułatwia | Numer porządkowy lub względną pozycja na liście identyfikacji elementu do pobrania. | Jaki był **ostatnio** wysłany komunikat? <br> Przeczytaj **najnowszą** wiadomość e-mail Nokia. <br> Odczytuj **nowe** wiadomości tekstowe. | last <br> najnowsza <br> Aktualne <br> najnowszych
Kontener positionreference | prosta, porządkowa | Numer porządkowy lub względną pozycja na liście identyfikacji elementu do pobrania.| Jaki był **pierwszy** wysłany komunikat? <br> **Trzecia** .| Pierwsze <br> trzeciej
RelationshipName obiektu | ułatwia | Nazwa relacji osoby kontaktowej lub odbiorcy wiadomości. | Wyślij wiadomość e-mail do mojego **żona** | żona
Time | datetime | Time | Wyślij wiadomość e-mail **Tonight**. | tonight
SearchTexts | prosta, wzorzec. any | Teksty używane do filtrowania wiadomości e-mail lub wiadomości | Przeszukaj wszystkie wiadomości e-mail zawierające ciąg "**Surface Pro**" | Surface Pro 
Kreska | ułatwia | Wiersz użytkownik chce użyć do wysłania wiadomości e-mail z. | Przeczytaj moją ostatnią wiadomość e-mail z usługi **Hotmail** . <br> Wyślij wiadomość e-mail z **konta służbowego**.| usługi <br> konto służbowe 

## <a name="homeautomation"></a>**HomeAutomation**
Domena HomeAutomation zapewnia intencje i jednostki związane z kontrolowaniem inteligentnych urządzeń domowych. Firma Microsoft obsługuje głównie polecenie sterujące związane ze światłami i warunkiem zapowietrznym. Może jednak mieć pewne możliwości uogólnienia na innych urządzeniach elektrycznych.
### <a name="supported-devices-and-properties"></a>**Obsługiwane urządzenia i właściwości**
Urządzenie | properties
-------|---------
Czujnik temperatury | Temperatura
Światło, Lampa | Wyłączone, jasność, kolor
Telewizja, radio | Wyłączone, wolumin
Warunek dotyczący powietrza (A/C), termostat | Zasilanie wyłączone, temperatura, zasilanie
Łopatk | Wyłączone, zasilanie
Możliwości, odtwarzacze DVD, tworzenie lodu itd. | Wyłączone

### <a name="intents"></a>**Intencji**
Nazwa celu | Opis | Przykłady
---------|----------|---------
 Wyłączanie | Użytkownik chce wyłączyć urządzenie lub ustawienia.  | Wyłącz światła. <br> Wyłącz coś. <br> Coś wyłączonego.
 Wlaczac | Włącz urządzenie lub Włącz i ustaw określone ustawienia dla urządzenia. | Włącz światło do 50%. <br> Włącz moją twórcę kawy <br> Czy można włączyć moją twórcę kawy?
 Setdevice | Użytkownik chce ustawić określone ustawienie lub typ urządzenia.  | Ustaw warunek klimatyzacyjny na 26 stopni. <br> Zmień lampki na niebieską. <br> Wywołaj ten sygnalizator jako nightlight.
 QueryState | Użytkownik pyta o stan urządzenia lub ustawienia.  | Na czym jest ustawiony termostat? <br> Czy w systemie jest włączona wartość A/C? <br> Co to jest temperatura Bedroom?
 TurnUp | Zmień ustawienia lub jasność urządzeń. | Rozjaśnij lampki o 75%. <br> Jasność tutaj o 10 procent.  <br> Wypełnianie w pokoju mieszkalnym.
 TurnDown | Wyłączając, ale nie wyłączając urządzenia, w trybie DIMM, temperatura lub jasność świateł. | Przełączaj bibliotekę o 44%. <br> Przyciemniaj światła. <br> Utwórz chłodnicę pokoju.
### <a name="entities"></a>**Obiekty**
Nazwa jednostki | Typ jednostki | Opis | Przykłady
-------|----------|--------------|---------------
DeviceName | List | Tekst zdefiniowany przez użytkownika dla swoich urządzeń. | Niebieski<br> Święta <br> Kasow
DeviceType | List | Obsługiwane urządzenia. | Uliczne <br> Warunek klimatyzacyjny <br> nightlight
Location | ułatwia | Lokalizacja lub pomieszczenie, w której znajduje się urządzenie. | Zlew<br> Downstairs <br> Bedroom
NumericalChange | ułatwia | Kwota, przez którą ustawienie jest zwiększane lub zmniejszane. <br> <br> _Miejsce pojawia się tylko z intencjami turn_up i turn_down._ | 3<br> 50%<br>
OrderReference | ordinal | Celem tego gniazda jest przechwycenie zaznaczenia elementów. Wskazuje pozycję elementu na liście. | Pierwsze<br> 2\.
Kwantyfikator | List | Kwantyfikator wskazuje liczbę wystąpień określonej jednostki. Na przykład "wszystkie", "co" itd. | Wszyscy<br> Co<br> Wszystko
Ustawienie | Proste | Ustawienie, dla którego użytkownik chce ustawić swoje urządzenie, w tym sceny, poziom, intensywność, kolor, tryb, temperatura, stan urządzenia. | Niebieski<br> 72 <br> Odczytu 
Nazwy | List | Ustawienie urządzenia, którego dotyczy użytkownik. | Temperatura<br> 
Data/godzina |  datetime | Czas i czas trwania obsługi urządzenia| 5 minut <br> 3 godziny
Jednostka | List | Aby oznaczyć słowa takie jak "stopnie", "%", "Fahrenheita", "c", każdy termin jednostkowy powinien być oznaczony osobno. | Kąt<br> Percent


## <a name="notes"></a>**Uwagi**
Uwaga domena ułatwia tworzenie notatek i zapisywanie elementów dla użytkowników.
### <a name="intents"></a>**Intencji**
Nazwa celu | Opis | Przykłady
---------|----------|---------
AddToNote | Dodaj informacje do otwartej notatki. | Dodaj do mojej notatki dotyczącej planowania, aby skontaktować się z moim szefem projektu.
Clear | Wyczyść wszystkie elementy z istniejącym notatki. | Usuń wszystkie elementy z notatki dotyczącej urlopu. <br>Wyczyść wszystko w notatce do czytania.
Potwierdź | Potwierdź akcję odnoszących się do uwagi. <br> ***Uwaga**: Ten cel obejmuje głównie akcję "Potwierdź" dla scenariusza notatki. Jeśli potrzebujesz bardziej ogólnych wyrażeń w "Confirm", Skorzystaj z opcji "Potwierdź" w domenie **narzędzi** . * | W tej notatce nie zgadzam się. <br>Potwierdzam zachowywanie wszystkich elementów na listach.
Create | Tworzenie nowej notatki. | Utwórz notatkę. <br>Zwróć uwagę na to, że Jason jest w mieście pierwszego tygodnia od. 
Usuwanie | Usuń całą notatkę. | Usuń moją notatkę dotyczącą urlopu. <br>Usuń moje notatki z artykułów spożywczych.
ReadAloud | Zanotuj głośność. | Zapoznaj się z pierwszą notatką. <br>Przeczytaj szczegóły.
Zamykanie | Zamknij bieżącą notatkę. | Zamknij notatkę. <br>Zamknij bieżącą notatkę.
Otwarty | Otwórz wcześniej istniejącą notatkę. | Otwórz notatkę dotyczącą mojego wywołania. <br>Otwórz notatkę "Planowanie".
RemoveSentence | Usuń zdanie dla notatki. | Usuń ostatnie zdanie. <br>Usuwanie wiórów z notatki w sklepie spożywczym. <br>Usuń pióra z noty dotyczącej zakupów szkolnych
ChangeTitle | Zmień tytuł notatki. | Nazwa ta należy do "planowania".

### <a name="entities"></a>**Obiekty**
Nazwa jednostki | Typ jednostki | Opis | Przykłady 
------- | ------- | ------- | -------
Text | prosty, wzorzec. Ile | Tekst notatki lub przypomnienia. | Rozciąganie przed zalet <br> jutro perspektywie.
Stanowisko | prosty, wzorzec. Ile | Tytuł notatki. | artykuły spożywcze <br> osoby do wywołania <br> do wykonania
CreationDate | datetimeV2 | To miejsce jest przeznaczone, gdy użytkownik pyta o uwagi utworzone w określonym oknie daty/godziny. | 
Kwantyfikator | List | Gdy użytkownik prosi o wykonanie akcji dla elementu "All", "co" lub "any" lub całego tekstu w notatce. | all <br> Ile <br> co
OrderReference | ordinal | Użytkownik chce wykonać akcje z elementami "First", "Last", "Next" itp. Items. | pierwszego <br> last


## <a name="places"></a>**Nabywc**
Miejsca obejmują firmy, instytucje, restauracje, publiczne miejsca i adresy. Domena obsługuje Znajdowanie i uzyskiwanie informacji o miejscu publicznym, takim jak lokalizacja, godziny operacyjne i odległość. 
### <a name="intents"></a>**Intencji**
Nazwa celu | Opis | Przykłady
---------|----------|---------
MakeCall | W miejscu, należy wykonać połączenie telefoniczne. | Applebees na 1000/200 Rojas St i Wywołaj.
FindPlace | Wyszukaj miejsca (biznesowe, instytucja, restauracji, przestrzeń publicznych adresów). Ale nie: <li> Tylko nazwa firmy: Starbucks <li> Tylko nazwa lokalizacji: Seattle/Norwegia <li> Cuisine, żywność lub produkty: Pizza/guacamole/włoski | Gdzie znajduje się najbliższy biblioteki? <br> Starbucks w Seattle. <br> Gdzie znajduje się najbliższy biblioteki? <br> 
Getaddress — | Poproś o adres miejsca. | Pokaż mi adres Guu na Robson ulica. <br> Co to jest adres najbliższej kawę?
GetDistance | Poproszenie o odległość od bieżącej lokalizacji do określonego miejsca. | Jak daleko jest dniem wolnym od pracy Inn?<br> Ile z nich ma być Bellevue kwadrat? <br> Jaka jest odległość do Tahoe?
GetPhoneNumber | Poproś o numer telefonu w miejscu. | Co to jest numer telefonu w najbliższej kawę?<br> Podaj liczbę dla magazynu macierzystego. <br> Numer telefonu do Disneyland.
GetPriceRange | Zażądaj zakresu użycia na mieszkańca w miejscu. | Średnia cena J. Sushi w Seattle. <br> Jest Cineplex wysokości równej połowie ceny w środy? <br> Jaki jest koszt obiad cała firma lobster na Sizzler?
GetStarRating | Poproś o klasyfikacji w formie gwiazdek miejsce. | Jak ocenia się Zucca?<br> Na ile gwiazdek ma prania francuski?<br> Jest dobra akwarium w Monterrey?
GetHours | Poproś o godzin pracy dla miejsca. | W jakich czasie Safeway zamknąć?<br> Ile godzin Home magazynu?<br> Kawę jest wciąż otwarty?
GetReviews | Poproś o przeglądach miejsce. | Pokaż przeglądy dla fabryki Cheesecake. <br> Przeczytaj przeglądy Cineplex. <br> Czy istnieją najnowsze Przeglądy dotyczące usługi Humperdinks?
GetMenu | Poproś o elementów menu dla restauracji. | Jest Zucca obsługi dowolnych elementów vegan? <br> Co znajduje się w menu pod adresem Sizzler? <br> Pokaż menu Applebee mnie.
RatePlace | Oceń miejsce. | 4 gwiazdki Rating for Maxi Pizza in Kimberly. <br> Nadaj Bonefish na TripAdvisor. <br> Utwórz 4 gwiazdki przegląd dla La Hacienda.
AddFavoritePlace | Użytkownik chce dodać lokalizację do listy ulubionych lub MVP. | Zapisz tę lokalizację w moich ulubionych. <br> Dodaj najlepsze kupowanie do moich ulubionych.

### <a name="entities"></a>**Obiekty**
Jednostki LUIS | Typ jednostki | Opis | Przykłady | Przykłady wypowiedź
--------------|-------------|-------------|----------|-------------------
AbsoluteLocation | ułatwia | Lokalizacja lub adres miejsca. | Palo Alto <br> 300 112th Ave SE <br> Seattle | **1020 Middlefield Rd.** w **Palo Alto** <br> Sklepy z nasionami ptaków w **Seattle** <br> Uzyskaj odległość od tego miejsca do **300 112th**.
Pozwalającego | ułatwia | Obiektywne właściwości i korzyści z publicznej lokalizacji. | waterfront <br> bezpłatne parkowania | Kirkland **Waterfront** owoce morza restauracje. <br> Czy istnieje **bezpłatne miejsce parkingowe** w pobliżu?
Cuisine | ułatwia | Typ żywności, cuisine lub cuisine przynależność. | Chiński <br> Włoski <br> Sushi <br> Noodle <br> | Pomóż mi znaleźć w **chińskiej** restauracji. <br> Co to są godziny otwarcia restauracji **Sushi** ? <br> Gdzie jest najbliższa **steaka** ?
Date | datetime | wartość DateTime lub Duration dla daty lokalizacji dostosowanej. | jutro <br> dzisiaj <br> 6\. | Jaki czas działa Aquarium **jutro**? <br> Najbliższy sklep rowerowy otwarty po **południu**
Odległość | wymiar | Odległość do miejsca publicznego od pozycji currenct użytkownika. | 15 mil. <br> 10 kilometrów | Sklep odzieżowy w ciągu **15 mil** <br> Restauracja dla dzieci, która ma tylko **10 kilometrów**
MealType | List | Typ posiłku, takich jak śniadanie lub obiad. | Śniadanie <br> Obiad | Wyszukaj **śniadanie** Greenwood Seattle <br> Znajdź miejsce na **obiad**.
Bliski | List | Opisz miejsce w pobliżu bez względu na lokalizację lub adres. | znajdując <br> w tym obszarze <br> dookoła mnie | Znajdź **najbliższą** indyjskią restauracji. <br> Gdzie jest mój **lokalny** Wetherspoon? <br> Wszystkie **dobre restauracje?**
OpenStatus | List | Wskazuje, czy miejsce jest otwarte lub zamknięte. | open <br> Zamknięte | Jaki czas Yogurt Grunt **zamknięty** dzisiaj? <br> Jakie są godziny **otwarcia** dla Costco?
PlaceName | ułatwia | Nazwa miejsca docelowego, które jest biznesowych, restauracji, publiczne przyciągania lub instytucji. Nazwa miejsca może zawierać element symbol, jeśli jest często używany. | Park środkowa <br> Safeway <br> Walmart| Jaki czas jest otwarty w **Safeway** farmacji? <br> Czy **Walmart** jest otwarty?
PlaceType | ułatwia | Typ lokalizacji docelowej, która jest lokalnych firmach, restauracji, publiczne przyciągania lub instytucji. | Restauracji <br> Opera <br> kinowych | **kina** w Cambridge <br> Czy **w pobliżu** ?
PriceRange | ułatwia | Zakres cenowy produktów lub usług w miejscu. | czynsz <br> koszt ekonomiczny <br> zabiera | Znajdowanie **przystępnej** naprawy urządzenia <br> Co to jest **tanie** miejsce Pizza, które jest teraz otwarte?
Produkt | ułatwia | Produkt, oferowane przez miejsce. | Odzież <br> telewizorach | Gdzie jest najlepsze miejsce do pobrania **żywności**? <br> Znajdź Wschodnie Kilbride szukające **telewizji**.
Ocena | ułatwia | Ocena miejsca. | 5 gwiazdek <br> top <br> Aukcj | Czy istnieją **dobre** miejsca do wychodzenia i Eat jutro <br> **najlepsze** Amsterdam Restauracje <br> Wystaw trzy **pierwsze** sklepy Pizza.


## <a name="restaurantreservation"></a>**RestaurantReservation**
Domena rezerwacji restauracji obsługuje intencje związane z obsługą rezerwacji dla restauracji.
### <a name="intents"></a>**Intencji**
Nazwa celu | Opis | Przykłady
---------|----------|---------
Zarezerwuj | Żądania rezerwacji dla restauracji. | Zarezerwuj w Zucca dla dwóch dla Tonight. <br> Zarezerwuj tabelę na przyszłość. <br> Tabela dla 3 w Palo Alto o 7. <br> Utwórz rezerwację dla 3 przy użyciu Red Homar.
DeleteReservation | Anulowanie lub usuwanie rezerwacji dla restauracji. | Anulowanie rezerwacji o godzinie Zucca jutro. <br> Zapomnij o mojej rezerwacji dla Red Homar o godz. 7:00 następny piątek. <br> Nie potrzebuję rezerwacji dla Zucca, Anuluj ją.
ChangeReservation | Zmień czas, miejsce lub liczbę osób na potrzeby istniejącej rezerwacji w restauracji. | Zmień moją rezerwację na 9 p.m. <br> Zmień moją rezerwację z Zucca na Red Homar. <br> 7 osób zamiast 6 dla rezerwacji w Zucca.
Potwierdź | Potwierdź akcję powiązaną z rezerwacją. <br> ***Uwaga**: Ten cel obejmuje głównie akcję "Confirm" dla scenariusza rezerwacji w restauracji. Jeśli potrzebujesz bardziej ogólnych wyrażeń w "Confirm", Skorzystaj z opcji "Potwierdź" w domenie **narzędzi** . * | Tak, wprowadziliśmy rezerwacje dla Tonight o godzinie 8 w programie makaroner. <br> Tak, po prostu Zarezerwuj ją. <br> Potwierdź rezerwację na pasku Sushi.
FindReservationDetail | Wyświetla szczegółowe informacje o istniejącej rezerwacji. | Znajdź moją rezerwację w witrynie Renaissance San Diego <br> Pokaż wartość rezerwacji jutro. <br> Wyświetl szczegóły mojej rezerwacji dla Zucca.
FindReservationWhere | Sprawdź absolutną lokalizację rezerwacji. | Gdzie jest lokalizacja elementu Zucca w mojej rezerwacji? <br> Pokaż ustawienia regionalne mojego zastrzeżeń w przyszłości.
FindReservationWhen | Sprawdzanie dokładnego czasu rezerwacji | Kiedy jest rezerwacja Zucca na jutro? <br> Czas rezerwacji w kolorze czerwonym.
Reject | Użytkownik odrzuca zaproponowany przez asystenta wirtualnego zarządzanie rezerwacją. <br> ***Zwróć uwagę**,:P dzierżawy odnoszą się do domeny **programów narzędziowych** , aby uzyskać więcej informacji o wyrażenia długości. * | Nie trzeba ustawiać zdarzenia. | Nie, nie chcę zmieniać rezerwacji. <br> Nie, nie należy zaksięgować, ale popełniono pomyłkę.

### <a name="entities"></a>**Obiekty**
Jednostka LUIS | Typ jednostki | Opis | Przykłady
-------|------|---------|-------------------
Adres | ułatwia | Lokalizacja zdarzenia lub adres dla rezerwacji. | Palo Alto<br>300 112th Ave SE<br>Seattle
Atmosfery | List | Opis środowiska w restauracji. | Romantyczna<br>zwykłych<br>dobre dla grup<br>dniach
Cuisine | ułatwia | Typ żywności, cuisine lub cuisine przynależność. | Chiński<br>Włoski<br>Peso<br>Sushi<br>Noodle<br>steak
MealType | List | Typ posiłku skojarzone z rezerwacji. | Śniadanie<br>Obiad<br>Obiad<br>Supper
PlaceName | ułatwia | Nazwa restauracji | Zucca<br>Fabryka cheesecake<br>Red Homar
Ocena | ułatwia | Ocena miejsca lub restauracji. | 5 gwiazdek<br>3 gwiazdek<br>4 — gwiazda
NumberPeople | ułatwia | Liczba osób do rezerwacji | 3<br>sześć
Time | datetime| Czas rezerwacji restauracji | jutro<br>tonight<br>7:00<br>Wigilia Bożego Narodzenia


## <a name="todo"></a>**Czynność**
Domena do _zrobienia_ zawiera typy list zadań, które użytkownicy mogą dodawać, oznaczać i usuwać elementy do wykonania.
### <a name="intents"></a>**Intencji**
Nazwa celu | Opis | Przykłady
---------|----------|---------
Adddo zrobienia | Użytkownik chce dodać elementy zadań dla dowolnego typu listy. |  Przypomnij mi o zakupie mleka. <br> Dodaj element o nazwie "Kup mleko" do listy Moje elementy do wykonania
Potwierdź | Użytkownik chce potwierdzić trwającą akcję. Wypowiedź zawiera jawny sygnał, taki jak "tak", aby potwierdzić operację. <br> <br> ***Uwaga**: Ten cel obejmuje głównie akcję "Potwierdź" w scenariuszu do zrobienia. Jeśli potrzebujesz bardziej ogólnych wyrażeń w "Confirm", Skorzystaj z opcji "Potwierdź" w domenie **narzędzi** . * | Usuń zadanie. <br> Na pewno chcę dodać to zadanie. <br> Tak, chcę to zrobić.
Cancel | Użytkownik chce anulować trwającą akcję.  <br> <br> ***Uwaga**: Ten cel obejmuje głównie akcję "Confirm" dla scenariusza rezerwacji w restauracji. Jeśli potrzebujesz bardziej ogólnych wyrażeń w "Confirm", Skorzystaj z opcji "Potwierdź" w domenie **narzędzi** . * | Nie, zapomnij go. <br> po prostu Anuluj zadanie. <br> Nie, nie dodawaj.
DeleteToDo | Usuń element z listy zadań, odwołując się do jego zamówienia lub Usuń wszystkie elementy do wykonania. | Usuń wszystkie zadania. <br> Pomóż mi usunąć drugi z nich.
MarkToDo | Oznacz zadanie jako zakończone lub gotowe, odwołując się do jego zawartości dotyczącej kolejności lub zadań. | Oznacz zadanie "Kup mleko" jako zakończone. <br> Zakończ odczytywanie zadania. <br> Ukończ wszystko.
ShowNextPage | Lista zostanie podzielona na kilka stron do wyświetlenia. Pokaż elementy listy na następnej stronie dla użytkownika. | Czy można wyświetlić następną stronę na liście zakupów? <br> Co dalej? <br> Co dalej?
ShowPreviousPage | Pokaż elementy listy na poprzedniej stronie użytkownika. | Pokaż poprzedni. <br> Muszę sprawdzić poprzednie zadania.
ShowToDo | Pokaż wszystkie elementy na liście do wykonania. | Pokaż listę moje zakupy. <br> Pokaż moją listę artykułów spożywczych.

### <a name="entities"></a>**Obiekty**
Jednostka LUIS | Typ jednostki | Opis | Przykłady
-------|------|---------|-------------------
ContainsAll | ułatwia | Reprezentuje wszystkie lub dowolne elementy na liście zadań | może pomóc w usunięciu **wszystkich** zadań. <br> Zakończ **wszystko**.
ordinal | ordinal | Porządkowa lub numeryczne odwołanie do elementu. | Oznacz **trzeci** jako zakończony. <br> Usuń **pierwsze** zadanie.
Listtype | ułatwia | Typ listy zadań.  | Dodaj buty do listy **zakupów** .
FoodOfGrocery | List | Wykryj listę elementów żywności | Przypomnij mi o zakupie **mleka**. <br> Dodaj **wołowinę** do listy artykułów spożywczych.
TaskContent | prosta, wzorzec. any | Wykrywa zawartość zadania. | Przypomnij mi o **rozmowie z moją matką** . <br> Dodaj **uroczyste urodziny John** do listy Moje do wykonania


## <a name="utilities"></a>**Uruchamiane**
Domena _narzędzi_ to ogólna domena obejmująca wszystkie wstępnie zbudowane modele _Luis_ , które zawierają typowe intencje i wyrażenia długości w scenariuszach różnicowych.
### <a name="intents"></a>**Intencji**
Nazwa celu | Opis | Przykłady
---------|----------|---------
 Cancel | Anuluj akcję/żądanie/zadanie/usługę. | Anuluj. <br> Nie zapomnij tego pamiętać.
 Potwierdź | Potwierdź akcję. | Oczywiście <br> Tak, <br> Sprawdzenia.
 Reject | Użytkownik odrzuca proponowany asystenta wirtualnego. | Nie
 FinishTask | Zakończ zadanie pracy użytkownika. | Jestem gotowy. <br> To wszystko. <br> Ukończyć.
 GoBack | Przejdź wstecz jeden krok lub wracasz do poprzedniego kroku. | Wróć do kroku. <br> Przejdź wstecz.
 Help | Żądanie w celu uzyskania pomocy. | Pomoc. <br> Otwórz Pomoc.
 Powtarzanie | Powtórz akcję. | Powiedz ponownie.
 ShowNext | Pokaż lub poinformuj następny element (y). | Pokaż następny.
 ShowPrevious | Pokaż poprzedni element w serii. | Pokaż poprzednią.
 StartOver | Ponowne uruchomienie aplikacji lub Rozpocznij nową sesję. | Uruchomieniu.
 Stop | Poinformuj asystenta wirtualnego, aby przestał mówić.  | Przestań powiedzieć to.
 SelectAny | Użytkownik prosi o wybranie dowolnego elementu lub wyników wyświetlanych na ekranie. | Dowolna z nich. <br> Wybierz jeden z nich.
 SelectNone | Użytkownik wybierze żaden z istniejących elementów i wyświetli monit o podanie dodatkowych opcji. | Podaj inne sugestie. <br> Nie ma żadnego z nich.
  Selectitem — | Użytkownik prosi o wybranie elementu lub wyników wyświetlanych na ekranie. | Wybierz trzecią. <br> Wybierz górny prawy.
 Eskaluj | Poproszą o obsługę klienta, aby obsłużyć te problemy. | Czy mogę skontaktować się z osobą?
 ReadAloud | Przeczytaj coś głosowego do użytkownika. | Przeczytaj tę stronę. <br> Przeczytaj na głos.

### <a name="entities"></a>**Obiekty**
Jednostka LUIS | Typ jednostki | Opis | Przykłady
------------|-------------|-------------|---------
ordinal | ordinal | Porządkowa lub numeryczne odwołanie do elementu. | **Drugi** . <br> **Dalej** .
number | number | Liczba elementów, które użytkownik chce | Następne **3** elementy
DirectionalReference | ułatwia | Punkt odniesienia, w którym znajduje się element na ekranie. | Z prawej strony <br> Prawym górnym

## <a name="weather"></a>**Meteorologiczn**
Domena pogody koncentruje się na sprawdzaniu warunków pogodowych i klasyfikatorów z lokalizacją i czasem lub sprawdzaniem czasu według warunków pogodowych.
### <a name="intents"></a>**Intencji**
Nazwa celu | Opis | Przykłady
---------|----------|---------
 CheckWeatherValue | Podawanie informacji o pogodzie lub współczynniku związanym z Pogoda dla lokalizacji na prognozie lub w przeszłych informacjach. <br> Czynniki związane z Pogoda: <li> temperature </li> <li> deszcz/śnieg/opady </li> <li> suche/mokre/wilgotność </li> <li> przeciwmgielnych </li> <li> Indeks UV </li> <li> cale deszczu/śniegu </li> | Jaki jest indeks jakości powietrza w Pekin? <br> Jak dużo opadów jest oczekiwany w Seattle w marcu? <br> Zarejestruj najwyższą temperaturę Hawaje.
 CheckWeatherTime | Podawaj czas prognozowanych lub historycznych czynników związanych z Pogoda dla lokalizacji. | Kiedy spodziewasz się deszczu? <br> Dobry moment na przejście do Kanady <br> Kiedy jest miesiąc okienko w Minnesoty?
 QueryWeather | Zapoznaj się z warunkiem pogodowym lub czynnikami związanymi z Pogoda dla określonej lokalizacji, dla której oczekiwana jest odpowiedź "tak, nie lub może", lub Powiadom o działaniach, które są zależne od warunków pogodowych. | Czy będzie to deszczowe jutro? <br> Czy Sunny dzisiaj? <br> Czy jest zbyt wczesny, aby przejść do Alaska?
 ChangeTemperatureUnit | Zmień jednostkę pogody, w tym c, Kelvin i Fahrenheita. | Konwertuj w c. <br> Czy mogę to zrobić w Kelvin?
 GetWeatherAdvisory | Pobierz klasyfikatory pogody lub alert z określonej lokalizacji. | Czy istnieją klasyfikatory pogodowe w Memphis? <br> Czy istnieją alerty pogodowe dla mojego obszaru?

### <a name="entities"></a>**Obiekty**
Jednostka LUIS | Typ jednostki | Opis | Przykłady
------------|-------------|-------------|---------
Location | Geograficzne | Bezwzględna lub niejawna lokalizacja żądania pogody. | Palo Alto<br>Szanghaj<br>Seattle<br>Delvina<br>
Date/Time   | datetime | Data i godzina trwania zapytania o Pogoda. | Listopad<br>godzinowo<br>Dobry<br>Ten weekend<br>10 dni<br>
AdditionalWeatherCondition | list | Dodatkowy wyraz opisujący Pogoda, taki jak szybkość lub kierunek wiatru. | direction<br>Szybko<br>Pasażerski
Znaczeniu | ułatwia | Opis słów historycznych warunków pogodowych, w tym przeciętnych przypadków przygranicznych Joomla w poprzednim okresie czasu. | zatrudnienia<br>historyczne/historia<br>chyleń<br>najlepszy czas<br>kiedykolwiek nagrane
PrecipitationUnit | wymiar | Opady dla śniegu lub deszczu. | 5 cali<br>6 cm
SuitableFor | ułatwia | Opis działania człowieka w warunkach pogodowych, który jest typowy, gdy użytkownicy wykonują zapytania dotyczące działań, które są zależne od warunków pogodowych. | okładki<br>macierzyst<br>basen
TemperatureUnit |temperature | temperature | 18 c<br>7 Kelvin stopni
WeatherRange | ułatwia | Określony stan temperatury, wiatru i innych warunków pogodowych w danym okresie czasu | maksymalnie<br>Wysokowydajn<br>niska<br>Średnia wysoka<br>Najwyższa
WeatherCondition | ułatwia | Opis warunku pogodowego | Sunny<br>fartuch<br>Opady<br>temperature<br>Krab<br>gorąca
WindDirectionUnit | list | Słowa kierunku wiatru | szerokości<br>Południowe<br>gracz<br>Zachodni<br>północny


## <a name="web"></a>**Web**
Domena sieci Web zapewnia zamiar i jednostki do wyszukiwania w witrynie internetowej.
### <a name="intents"></a>**Intencji**
Nazwa celu | Opis | Przykłady
---------|----------|---------
 Wyszukiwanie w usłudze WebSearch | Żądanie przejścia do określonej witryny sieci Web lub wyszukiwanie w aparacie wyszukiwania. | Wyszukaj powierzchnię w google.com. <br> Znajdowanie wszystkiego najlepszego w sieci Web <br> Przejdź do www.twitter.com.

### <a name="entities"></a>**Obiekty**
Jednostka LUIS | Typ jednostki | Opis | Przykłady
------------|-------------|-------------|---------
SearchEngine | List | Użytkownik aparatu wyszukiwania chce korzystać z programu. | Bing <br> Google
Tekstprzeszukiwany | prosty, wzorzec. Ile | Użytkownik tekstowy chce przeszukać. <br> _Oznacz "znajomych w serwisie Facebook" jako Tekstprzeszukiwany, jeśli witryna internetowa po "w" nie jest aparatem wyszukiwania. Adres URL powinien być również oznakowany jako Tekstprzeszukiwany._ | Film <br> Uczenie głębokie <br> Rejs
Łącze | url | Łącze do witryny sieci Web. | www.twitter.com

