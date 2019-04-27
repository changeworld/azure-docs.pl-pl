---
title: Dokumentacja wstępnie utworzonych domen
titleSuffix: Azure
description: Dokumentacja ze wstępnie utworzonych domen, które są wstępnie kolekcjami intencje i podmioty z Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: e1e579233a5ad1af1ef8ee84019cd995959d3b2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712620"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Dokumentacja ze wstępnie utworzonych domen aplikacją usługi LUIS
Ta dokumentacja zawiera informacje na temat [ze wstępnie utworzonych domen](luis-how-to-use-prebuilt-domains.md), które są wstępnie utworzonych kolekcji intencje i podmioty, które są oferowane usługi LUIS.

[Domeny niestandardowe](luis-how-to-start-new-app.md), z drugiej strony, rozpoczynać się nie intencje i modeli. Można dodać żadnych wbudowanych domeny intencje i podmioty do modelu niestandardowego.

## <a name="list-of-prebuilt-domains"></a>Lista ze wstępnie utworzonych domen
Usługa LUIS oferuje 20 ze wstępnie utworzonych domen. 

| Wstępnie utworzone domeny | Opis | Obsługiwane języki |
| ---------------- |-----------------------|:------:|
| Kalendarz | Domeny kalendarza zapewnia intencje i podmioty, dodawanie, usuwanie, lub edytowanie terminu, sprawdzanie dostępności uczestników i znajdowanie informacji na temat zdarzenia kalendarza.| en-US<br/> zh-CN |
| Aparat fotograficzny | Domeny aparatu przewiduje intencje i podmioty biorąc zdjęcia, nagrania wideo i wideo emisji do aplikacji.| en-US |
| Komunikacja | Wysyłanie komunikatów i wykonywaniu rozmów telefonicznych.| en-US <br/> zh-CN |
| Rozrywka  | Obsługa zapytań związanych z muzyka, filmy i TV.| en-US |
| Zdarzenia | Pokazuje rezerwacji biletów na koncerty, rozwiązanie, gry sportowe i dokument.| en-US |
| Przydatności | Obsługa żądań związanych z przydatności działania śledzenia.| en-US |
| Gry | Obsługa żądań związanych z gier innych firm, gry dla wielu graczy.| en-US |
| HomeAutomation | Kontrolowanie inteligentne macierzystego urządzeniami, takimi jak światła i urządzeń.| en-US<br/> zh-CN |
| MovieTickets | Rezerwacji bilety do filmów na theater filmu.| en-US |
| Muzyka | Odtwarzanie utworów muzycznych na odtwarzacz muzyczny.| en-US<br/> zh-CN |
| Uwaga | Domeny Uwaga zapewnia intencje i podmioty, związane z tworzeniem, edytowaniem i znajdowanie uwagi.| en-US<br/> zh-CN |
| OnDevice | Domeny OnDevice zapewnia intencje i podmioty, związane z sterowania urządzeniem.| en-US<br/> zh-CN |
| Miejsca  | Obsługa zapytań dotyczących takich firm, instytucje, restauracje, publicznego miejsca do magazynowania i adresów miejsc.| en-US<br/> zh-CN |
| Przypomnienie | Obsługa żądań związanych z tworzeniem, edytowaniem i znajdowanie przypomnienia.| en-US<br/> zh-CN |
| RestaurantReservation | Obsługa żądań do zarządzania rezerwacje restauracji.| en-US<br/> zh-CN |
| Taksówek | Obsługa rezerwacji dla taksówek.| en-US<br/> zh-CN |
| Tłumaczenie | Tłumaczenie tekstu na język docelowy.| en-US<br/> zh-CN |
| TV | Kontrolowanie TV.| en-US |
| Usługi użyteczności publicznej  | Obsługa żądań, które są wspólne w wielu domenach, takich jak "pomoc", "Powtórz", "Rozpocznij ponownie".| en-US |
| Pogoda | Pobieranie pogody i prognozy.| en-US<br/> zh-CN |
| Sieć Web | Przejdź do witryny sieci Web.| en-US<br/> zh-CN |

Aby uzyskać więcej szczegółów w każdej domenie zobacz w kolejnych sekcjach.

## <a name="calendar"></a>Kalendarz 

Domeny kalendarza zapewnia intencje i podmioty, związane z wpisy kalendarza. Intencji kalendarza obejmują dodawanie, usuwanie lub edytowanie terminu, sprawdzanie dostępności i znajdowanie informacji o wpisu kalendarza lub terminu.

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Add | Dodaj nowy element jednorazowe do kalendarza.| Wprowadź termin Lisa o 14: 00, od niedzieli <br/><br/>Chcę zaplanować na spotkanie<br/><br/>Należy skonfigurować na spotkanie|
| CheckAvailability | Znajdź dostępne dla terminu lub spotkania na kalendarz użytkownika lub inną osobę kalendarz.| Gdy jest dostępne w celu spełnienia Jim? <br/><br/>Pokaż przy Karolina jest dostępna jutro<br/><br/>Chris jest bezpłatne w sobotę?|
| Usuwanie | Żądanie usunięcia wpisu kalendarza.| Anuluj mojej termin Karoliny. <br/><br/>Usuń Moje spotkania 9: 00<br/>|
| Edytuj | Żądanie zmiany istniejących spotkań lub wpis w kalendarzu.| Przenieś Moje spotkania 9: 00 do 10: 00.<br/><br/>Chcę zaktualizować Moje harmonogramu.<br/><br/>Ponowne planowanie spotkania za pomocą Ryan.|
| Znajdowanie | Wyświetl Mój kalendarz co tydzień.| Znajdź stomatologowi Przejrzyj terminu. <br/><br/>Pokaż kalendarz<br/>|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Lokalizacja | Lokalizacja elementu kalendarza, spotkania lub terminu. Adresy, miasta i regiony są dobrym przykładem lokalizacji.| 209 Nashville w przypadku treningów <br/><br/>897 pancake DOM<br/><br/>Garażu|
| Podmiot | Tytuł spotkania lub terminu.| Termin dentist firmy <br/><br/>Obiad z Julia<br/><br/>Lekarza|

## <a name="camera"></a>Aparat fotograficzny 
Domeny aparatu zapewnia intencje i podmioty, powiązane z użyciem aparatu fotograficznego. Intencji obejmuje przechwytywanie zdjęcia, selfie, zrzut ekranu lub filmu wideo, a emisja wideo do aplikacji.

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| CapturePhoto| Przechwytywanie zdjęcia.| Zrób zdjęcie<br/><br/>Przechwytywanie|
| CaptureScreenshot | Przechwyć zrzut ekranu.| Wykonaj zrzut ekranu.<br/><br/>Przechwytywanie ekranu.|
| CaptureSelfie | Przechwyć selfie.| Wykonaj selfie <br/><br/>Zrób zdjęcie Me |
| CaptureVideo | Rozpocznij nagrywanie filmu wideo.| Zacznij nagrywanie <br/><br/>Rozpocznij nagrywanie|
| StartBroadcasting| Rozpocznij wideo emisji.| Rozpocznij emisji w serwisie Facebook|
| StopBroadcasting| Zatrzymaj wideo emisji.| Zatrzymaj emisji|
| StopVideoRecording| Zatrzymaj nagrywanie filmu wideo.| Jest wystarczająca<br/><br/>Zatrzymaj nagrywanie|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AppName | Nazwa aplikacji wideo, aby wysyłać.| OneNote<br/><br/>Facebook<br/><br/>Skype|


## <a name="communication"></a>Komunikacja 
Domain komunikacja zapewnia intencje i podmioty, związane z poczty e-mail, wiadomości i połączeń telefonicznych.

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AddContact| Dodaj nowy kontakt do listy kontaktów użytkownika.|Dodaj nowy kontakt <br/><br/>Zapisz tę liczbę i umieść nazwę jako Karoliny|
| AddMore| Dodaj więcej na temat wiadomości e-mail lub tekstu, jako część stopniowy kompozycji e-mail lub SMS.|Wzbogać tekstu <br/><br/>Wzbogać treść wiadomości e-mail|
| Odpowiedź| Odpowiedzi na przychodzące połączenia telefonicznego.|Odebranie połączenia <br/><br/>Podnieś ją|
| AssignContactNickname| Przypisać pseudonim kontaktu.|Zmień Tomasz Tato <br/>Pseudonim Jim edycji<br/>Dodaj pseudonim do Patti Owens|
| CallVoiceMail| Połącz poczty głosowej użytkownika.|Połącz mnie Moje pole pocztę głosową <br/>Poczta głosowa<br/>Wywołaj pocztę głosową|
| CheckIMStatus| Sprawdź stan kontakt w programie Skype.|Stan online przez Jim ustawiono język? <br/>Karolina jest dostępne ze?|
| Potwierdź| Potwierdź akcję.|Yes<br/>OK<br/>W porządku<br/>Potwierdzam, że chcę wysłać tę wiadomość e-mail.<br/>|
| Wybierania| Rozpoczynanie rozmowy telefonicznej.|Jim wywołania<br/>Proszę wybrać 311<br/>|
| FindContact| Znajdowanie informacji kontaktowych według nazwy.|Znajdź numer dla Karoliny<br/>Pokaż numer dla Karoliny<br/>|
| FindSpeedDial| Znajdź numer wybierania szybkość, numer telefonu jest ustawiony na i na odwrót.|Co to jest mój numer wybierania 5?<br/>Czy mają szybkość wybierania zestaw?<br/>Co to jest numer wybierania 941-5555-333?|
| GetForwardingsStatus| Uzyskaj bieżący stan przekazywania połączeń.|Czy włączone jest Moja przesyłanie dalej wywołania?<br/>Informacje o tym, jeśli mój stan połączenia jest włączone czy wyłączone<br/>|
| GoBack| Wróć do poprzedniego kroku.|Wróć do usługi twitter<br/>Przejść wstecz<br/>Przejdź wstecz|
| Zignoruj| Ignoruj przychodzących połączeń.|Nie można znaleźć odpowiedzi<br/>Ignoruj wywołania|
| IgnoreWithMessage| Ignoruj przychodzących połączeń i odpowiedzi z tekstem zamiast tego.|Nie można znaleźć odpowiedzi to wywołanie, ale zamiast tego wysłać wiadomość.<br/>Ignoruj i wysłać ponownie wiadomość.|
| PressKey| Naciśnij przycisk lub numer na klawiaturze numerycznej.|Wybierania star.<br/>Naciśnij klawisz 1 2 3.|
| ReadAloud| Przeczytaj wiadomości lub wiadomości e-mail do użytkownika.|Przeczytaj tekst.<br/>Co ona powiedzieć wiadomości?|
| TurnForwardingOff| Rozpoczynanie rozmowy telefonicznej.|<br/><br/>|
| Wybierz ponownie| Wybierz ponownie lub ponownie wywołaj liczbą.|Wybierz ponownie.<br/>Wybierz ponownie Moje ostatnie wywołanie.|
| Reject| Odrzucanie połączenia przychodzącego.|Odrzuć wywołanie<br/>Nie można odebrać<br/>Nie są dostępne w tej chwili i wywoła ponownie później.|
| SendEmail| Wyślij wiadomość e-mail. Celem tego mają zastosowanie do wiadomości e-mail, ale nie wiadomości SMS.|Adres e-mail do wody Mike: Mike czy obiad w ostatnim tygodniu została świetnie.<br/>Wyślij wiadomość e-mail do Boba<br/>|
| SendMessage| Wyślij wiadomość SMS lub wiadomości błyskawicznej.|Wyślij wiadomość SMS, Chris i Karoliny|
| SetSpeedDial| Ustaw skrót wybierania szybkość dla numeru telefonu kontaktu.|Ustaw szybkiego wybierania, jeden dla Karoliny.<br/>Skonfiguruj szybkie wybieranie dla programu Microsoft Operations Manager.|
| ShowNext| Zobacz następną sekcję, na przykład listę wiadomości SMS lub wiadomości e-mail.|Pokaż następny.<br/>Przejdź do następnej strony.|
| ShowPrevious| Na przykład, zobacz poprzedni element na liście wiadomości SMS lub wiadomości e-mail.|Pokaż poprzedni.<br/>Poprzednie<br/>Przejdź do poprzedniego.|
| StartOver| Rozpoczęcie systemu lub Rozpocznij nową sesję.|Zacznij od nowa<br/>Nowej sesji<br/>restart|
| TurnForwardingOff| Wyłącz funkcję przekazywania połączeń.|Przerwanie przekazywania Moje wywołania<br/>Wyłącz przekazywanie wywołań|
| TurnForwardingOn| Wyłącz telefonu osoby mówiącej.|Przekazywania z 3333 Moje wywołania<br/>Przełącz na wywołanie funkcji przekazywania danych do 3333|
| TurnSpeakerOff| Wyłącz telefonu osoby mówiącej.|Zwiększyły się ze mną osoby mówiącej.<br/>Wyłącz głośnomówiącego.<br/>|
| TurnSpeakerOn| Włącz telefonu osoby mówiącej.|Tryb głośnomówiącego.<br/>Umieść głośnomówiącego na.<br/>|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AudioDeviceType | Typ urządzenia audio (osoby mówiącej, zestaw słuchawkowy, mikrofon itp.).| Prelegent<br/>Bezobsługowego<br/>Bluetooth|
| Kategoria | Kategoria wiadomości lub wiadomości e-mail.| Ważne<br/>Wysoki priorytet|
| ContactAttribute | Atrybut użytkownika skontaktuj się z zapytania o.| Urodziny<br/>Adres<br/>Numer telefonu|
| Nazwa kontaktu | Nazwa odbiorcy kontaktu lub komunikatu.| Karolina<br/>Jim<br/>Chris|
| EmailSubject | Tekst używany jako wiersz tematu wiadomości e-mail.| Odp: interesujących historii|
| Kreska | Wiersz użytkownik chce umożliwia nawiązywanie połączeń lub wysyłać tekst/z poziomu poczty e-mail.| Praca z wiersza<br/>Brytyjski komórki<br/>Skype|
| Komunikat | Komunikat do wysłania jako wiadomość e-mail lub tekstu.| Było wspaniałe spotkanie już dziś. Do zobaczenia wkrótce!|
| MessageType | Nazwa odbiorcy kontaktu lub komunikatu.| Tekst<br/>Email|
| OrderReference | Numer porządkowy lub względną pozycja na liście identyfikacji elementu do pobrania. Na przykład "last" lub "najnowsze" w "Jaki był ostatni komunikat, który została wysłana?"| Ostatnia<br/>Najnowsze|
| SenderName | Nazwa nadawcy.| Patti Owens|

## <a name="entertainment"></a>Rozrywka  
Pokazuje rozrywki, które domeny zapewnia intencje i podmioty, powiązane wyszukiwania filmów, utworów muzycznych, gier i TV.

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Wyszukiwanie| Pokazuje wyszukiwanie filmów, muzyki, aplikacje, gry i TV.|Wyszukaj w sklepie obwódkę.<br/>Wyszukaj awatara.|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| ContentRating | Multimediów zawartości ocenę G lub R filmów.|Film wideo dzieci.<br/>PG sklasyfikowane.|
| Gatunku | Gatunek filmu, gier, aplikacji lub utworu.|Comedies<br/>Dramatów<br/>Zabawnych|
| Słowo kluczowe| Słowa kluczowe do wyszukania ogólnego atrybut określający, nie istnieje w bardziej szczegółowe miejsc nośnika.|Ścieżek dźwiękowych<br/>Rzeka księżyca<br/>Amelia Earhart|
| Język | Język używany w nośników, takich jak prowadzone języka filmu lub utworu.|Francuski<br/>Polski<br/>Koreański|
| MediaFormat | Dodatkowe specjalny techniczne typ, w którym jest sformatowany nośnika.|HD filmy<br/>Filmy 3D<br/>Do pobrania|
| MediaSource | Sklepie lub w portalu marketplace nabycia nośnika.|Netflix<br/>Pierwsze|
| MediaSubTypes| Typów nośnika jest mniejszy niż filmy i gry.|Pokazy<br/>DLC<br/>Przyczepy|
| Przynależność| Kraj, w której utworzono filmu, Pokaż lub utworu.|Francuski<br/>Niemiecki<br/>Koreański|
| Person (Osoba)| Aktor, dyrektor, producenta, muzykiem lub wykonawcy filmu, aplikację, grę lub programu TV.|Madonna<br/>Stanley Kubrick|
| Rola| Rolę odgrywaną przez osobę w przypadku tworzenia nośnika.|Sings<br/>Wskazany przez<br/>Od|
| Stanowisko| Nazwa filmu, aplikacji, gier, telewizyjny lub utworu.|Znajomych<br/>Minecraft|
| Typ| Format typu lub nośnik filmu, aplikacji, gier, telewizyjny lub utworu.|Muzyka<br/>MovieTV <br/>Pokazuje|
| UserRating| Gwiazda użytkownika lub thumbs klasyfikacji.|5 gwiazdek<br/>3 gwiazdek<br/>4 gwiazdek|

## <a name="events"></a>Zdarzenia 
Domena zdarzeń zapewnia intencje i jednostki związane z rezerwacji biletów dla zdarzeń, takich jak Koncerty, rozwiązanie, gry sportowe i dokument przedstawia.

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Book (Książka)| Zakup biletów ze zdarzeniem.|Chcę otrzymywać zakup biletów dla symphony to weekend.|


### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Adres | Lokalizacja zdarzenia lub adres. |Palo Alto<br/>300 112th Ave SE <br/> Seattle |
| Name (Nazwa) | Nazwa zdarzenia.|Szekspir w Park|
| PlaceName| Nazwa lokalizacji zdarzenia.|Louvre<br/>Opera DOM<br/>Broadway|
| PlaceType | Typ lokalizacji wydarzenie odbędzie się w.|Kawiarnia<br/>Teatralną<br/>Biblioteka|
| Typ | Typ zdarzenia.|Koncert<br/>Gry sportowe|

## <a name="fitness"></a>Przydatności 
Domeny przydatności zapewnia intencje i podmioty, związane z przydatności działania śledzenia. Intencji obejmują zapisywania notatki, pozostały czas lub odległości lub zapisywania wyników działania.

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AddNote| Dodaje dodatkowe uwagi dotyczące śledzonych działania.|Trudności Ten przebieg był 6/10<br/>Terenu, których jestem w systemie jest asfaltu.<br/>Używam roweru szybkość 3|
|GetRemaining| Pobiera pozostałego czasu lub odległości dla działania.|Ile czasu do następnego omówienie?<br/>Ile mil pozostałych w danego uruchomienia? Czas, jaki podziału?|
| LogActivity| Zapisz lub zaloguj się wyniki ukończonego działania.|Zapisz mój ostatni przebieg<br/>Zaloguj się Moje przeszukiwania rano sobota<br/>przechowywanie Moje poprzednie toru|
| LogWeight| Zapisz lub dziennika wagi bieżącego użytkownika.|Zapisz moje bieżące wagi<br/>Zaloguj się teraz moje wagi<br/>przechowywanie Moja bieżąca waga treści|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Właściwości ActivityType | Typ działania do śledzenia. |Uruchom polecenie<br/>Marsz<br/>Toru<br/>Cykl |
| Żywność | Typ żywności do śledzenia w przydatności aplikacji. |Banany<br/>Łososia<br/>Wstrząsnąć białka|
| MealType| Typ posiłku do śledzenia w kondycji lub przydatności aplikacji.|Śniadanie<br/>Obiad<br/>Obiad<br/>Supper|
| Miara| Typ miary dla czasu, odległości lub waga do użycia w przydatności lub kondycji aplikacji.|Kilometrów<br/>Miles<br/>Minuty<br/>Kilogramów|
| Liczba | Wartość numeryczną do użytku w przydatności lub kondycji aplikacji.|19<br/>trzy<br/>200<br/>jeden|
| StatType | Typ wartości licznika Statystyka na zagregowane dane do użytku w przydatności lub kondycji aplikacji.|Suma<br/>Średnia<br/>Maksimum<br/>Minimalne|

## <a name="gaming"></a>Gry 
Domeny gier zapewnia intencje i podmioty, powiązane z zarządzaniem gier innych firm, gry dla wielu graczy.

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| InviteParty| Zaproś kontakt do dołączenia do strony gier.|Zaproś tego odtwarzacza do mojej firmy<br/>Przyjdź na mojej firmy<br/>Dołącz do mojego clan|
|LeaveParty| Pobiera pozostałego czasu lub odległości dla działania.|Nazywam się<br/>Czy mogę jestem opuszczenie tej strony, do innego<br/>Jestem było Kończenie działania|
| StartParty| Rozpocznij strony gier w gry dla wielu graczy.|Spróbujmy był start stroną<br/>Uruchom stronę<br/>Firma Microsoft na początek clan zmieniły|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Kontakt| Nazwisko osoby kontaktowej w gry dla wielu graczy.|Karolina<br/>Jim|


## <a name="homeautomation"></a>HomeAutomation 
Domeny HomeAutomation zapewnia intencje i podmioty, związane z kontrolowanie inteligentne macierzystego urządzeniami, takimi jak światła i urządzeń.

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Wyłączanie| Wyłącz, a następnie zamknąć lub odblokowania urządzenia.|Wyłącz światła<br/>Zatrzymaj maker kawy<br/>Drzwi garażowe Zamknij|
|Wlaczac| Włącz urządzenie lub ustawić urządzenia określone ustawienie lub trybie.|Włącz Moje maker kawy<br/>można włączyć na Moje maker kawy?<br/>Ustaw termostat 72 stopni.|


### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Urządzenie | Typ urządzenia, które można włączyć lub wyłączyć.|Twórca kawy<br/>Termostat<br/>światła|
| Operacja | Stan urządzenia.|Blokady<br/>open<br/>włączone<br/>wyłączone|
| Miejsca | Lokalizacja lub miejsca, w której znajduje się urządzenie.|Salon<br/>sypialni<br/>kuchenne|

## <a name="movietickets"></a>MovieTickets 
Domeny MovieTickets zapewnia intencje i podmioty, związane z żądania rezerwacji filmy na theater filmu.

### <a name="examples"></a>Przykłady

|Przykłady|
|--|
|Zarezerwuj dwie bilety kapitana Omar i dwa muszkieterami|
|Anuluj biletów|
|Gdy Omar kapitana jest wyświetlane?|

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Book (Książka) | Zakup biletów filmu.|Zarezerwuj dwie bilety kapitana Omar i dwa muszkieterami<br/>Chcę, aby kupić biletu dla filmu w przyszłości<br/>Chcę bilet kapitana Omar część 2 dalej środa|
|GetShowTime| Uzyskaj showtime filmu.|Gdy Omar kapitana jest wyświetlane?|


### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Adres | Adres theater filmu.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| MovieTitle | Tytuł filmu.|Czas życia Pi<br/>Głód gry<br/>Incepcja|
| PlaceName | Nazwa filmu theater lub kin.|Amir kinowych<br/>Teatralną Aleksandrii<br/>Nowy Jork kinowy|
| PlaceType | Typ lokalizacji film jest wyświetlane w.|kinowych<br/>kinowy<br/>IMAX kinowych|

## <a name="music"></a>Muzyka 
Domeny utworów muzycznych zapewnia intencje i podmioty, związane z odtwarzaniem utworów muzycznych na odtwarzacz muzyczny.

### <a name="examples"></a>Przykłady

|Przykłady|
|--|
|Odtwórz Beethoven|
|Zwiększ wolumin śledzenie|
|Przejdź do następnego utworu|

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| DecreaseVolume | Zmniejszyć wolumin w urządzeniu.|Zmniejsz ścieżki woluminu<br/>wolumin szczegółów|
| IncreaseVolume | Zwiększ wolumin w urządzeniu.|Zwiększ wolumin śledzenie<br/>wolumin w górę|
| Wycisz dźwięk |Wycisz muzyka odtwarzania.|Wycisz utwór<br/>Umieść śledzenie wyciszone<br/>Wycisz utworów muzycznych |
| Wstrzymaj | Wstrzymaj odtwarzanie music.|Wstrzymaj<br/>Wstrzymaj utworów muzycznych<br/>Śledź wstrzymania|
| PlayMusic | Odtwórz utworów muzycznych na urządzeniu.|Odtwórz Kevin Durant<br/>Odtwórz Paradise przez Coldplay<br/>Odtwórz Hello przez Adele|
| Powtarzanie |Powtarzanie odtwarzania music.|Powtórz utwór<br/>Odtwórz zysk toru<br/>Powtórz utworów muzycznych|
| Resume | Wznów muzyka odtwarzania.|Wznów utwór<br/>Uruchom ponownie utworów muzycznych<br/>Wznów działanie|
| SkipBack | Przejdź wstecz ścieżki.|Przejdź do następnego utworu<br/>Odtwarzać utwór dalej|
| SkipForward |Przeskocz do przodu ścieżki.|Odtwórz poprzedniego<br/>Wróć do poprzedniej ścieżki |
| Stop | Zatrzymaj akcji dotyczących muzyki odtwarzania. |Zatrzymaj odtwarzanie tego albumu.|
| Włącz dźwięk | Wyłącz wyciszenie urządzenie do odtwarzania music.| Wyłącz wyciszenie.|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| ArtistName | Aktora, dyrektor, producenta, zapisywania, muzykiem lub wykonawcy skojarzone z nośnika, aby odtworzyć na urządzeniu.|Elvis Presley<br/>Taylora Swift<br/>Adele<br/>Mozart|
| Gatunku | Tego gatunku utworów muzycznych żądanej.|Muzyka kraju<br/>Klasyka Broadway<br/>Moja muzyka klasycznego z okresu Barok odtwarzania|

## <a name="note"></a>Uwaga 
Domeny Uwaga zapewnia intencje i podmioty, związane z tworzeniem, edytowaniem i znajdowanie uwagi.

### <a name="examples"></a>Przykłady

|Przykłady|
|--|
|Dodaj do mojego artykuły spożywcze Uwaga sałaty pomidora chleb kawy|
|Usunięcie pozycji banany z mojej listy połówce|
|Usuń wszystkie elementy z listy urlopu|

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AddToNote | Dodaj informacje do notatki.|Dodaj do mojego artykuły spożywcze Uwaga sałaty pomidora chleb kawy<br/>Dodaj do listy zadań do wykonania<br/>Dodaj cupcakes do mojej aplikacji Wunderlist|
| CheckOffItem | Zaznaczenie elementów z istniejącym notatki.|Usunięcie pozycji banany z mojej listy połówce<br/>Oznacz Tort ser na Moje święta zakupy listy jako gotowe|
| Clear | Wyczyść wszystkie elementy z istniejącym notatki.|Usuń wszystkie elementy z listy urlopu<br/>Wyczyść wszystkie z moich przeczytania|
| Potwierdź | Potwierdź akcję odnoszących się do uwagi.|Ma nic złego przeze mnie<br/>tak<br/>Jestem potwierdzenie utrzymywanie wszystkich elementów na listach|
| Przycisk Utwórz | Tworzenie nowej notatki. | Utwórz listę<br/>Należy pamiętać, aby Przypomnij mi tego Jason znajduje się w mieście pierwszy tydzień maja|
| Usuwanie | Usuń całą notatkę. |Usuwanie listy urlopu <br/>Usuń notatkę Moje artykuły spożywcze|
| DeleteNoteItem | Usuń elementy z istniejącym notatki.| Usuń moduły z listy połówce<br/>Usuń pióra z mojej szkoły zakupy listy|
| ReadAloud | Odczytywanie listy głos.|Plik Readme, pierwszy z nich<br/>Plik Readme szczegóły|
| ShowNext | Zobacz kolejny element na liście notatek.|Pokaż następny<br/>Następna strona<br/>Następne kroki|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AppName | Nazwa aplikacji notatek.|Wunderlist<br/>OneNote|
| Nazwa kontaktu | Nazwa kontaktu w notatki.|Karolina<br/>Jim<br/>Chris|
| Źródło danych | Lokalizacja uwagi.|OneDrive<br/>Dokumenty Google<br/>Mój komputer|
| DataType | Typ pliku lub dokumentu, zwykle związanych z programami konkretnego oprogramowania.|Slajdów<br/>Arkusz kalkulacyjny<br/>Arkusz|
| Tekst | Tekst notatki lub przypomnienia.|Rozciąganie przed zalet<br/>jutro perspektywie.|
| Stanowisko | Tytuł notatki.|artykuły spożywcze<br/>osoby do wywołania<br/>do wykonania|

## <a name="ondevice"></a>OnDevice 
Domeny OnDevice zapewnia intencje i podmioty, związane z sterowania urządzeniem.

### <a name="examples"></a>Przykłady

|Przykłady|
|--|
|Zamknij odtwarzacza wideo|
|Anulowanie odtwarzania|
|Czy można utworzyć ekran jaśniejszy?|


### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AreYouListening | Poproś, jeśli urządzenie nasłuchuje.|jest to na?<br/>Możesz nasłuchują?|
|CloseApplication|Zamknij aplikację urządzenia.|Zamknij odtwarzacza wideo|
|FileBug|Zgłoś usterkę na urządzeniu.|Zgłoś usterkę.<br/>Można zgłosić usterkę dla mnie?<br/>Pozwól mi zgłosić ten błąd|
|GoBack|Poproś przejść jeden krok lub wracasz do poprzedniego kroku.|Przejdź wstecz.<br/>Przejdź do poprzedniego ekranu<br/>Przejdź wstecz Zatrzymaj nasłuchiwania|
|Pomoc| Prosić o pomoc.|Pomoc.<br/>Witamy<br/>Co należy zrobić?<br/>Potrzebuję pomocy| 
|LocateDevice|Zlokalizuj urządzenie.|Można znaleźć Mój telefon<br/>Znajdź firmy tom iphone<br/>Znajdź Mój telefon|
|Zaloguj się|Zaloguj się do usługi przy użyciu urządzenia.|Zaloguj się.<br/>Logowanie do usługi Facebook<br/>Zaloguj się do usługi LinkedIn|
|Wyloguj|Wyloguj się z usługą za pomocą urządzenia.|Wylogowywanie Mój telefon<br/>Zaloguj się do usługi twitter<br/>Wyloguj się|
|MainMenu|Wyświetl menu głównego urządzenia.|Menu Widok.|
|OpenApplication|Otwórz aplikację na urządzeniu.|Otwórz alarmu.<br/>Włącz aparat fotograficzny<br/>Uruchom kalendarza|
|OpenSetting|Otwórz ustawienia na urządzeniu.|Otwórz ustawienia sieciowe.|
|PairDevice|Sparuj urządzenie.|Proszę o pomoc w parowanie sygnał Bluetooth na telefon<br/>Włączanie funkcji bluetooth i Sparuj go z komputera przenośnego<br/>Para Bluetooth sygnał do mojego laptopa|
|Wyłącz zasilanie | Wyłącz urządzenie.|Można zostanie zamknięta moim komputerze<br/>Shutdown<br/>Wyłącz Mój telefon komórkowy|
|QueryBattery|Pobierz informacje o czas pracy baterii.|Pokaż czas pracy baterii.<br/>Co to jest Mój stan baterii<br/>Teraz poziom naładowania baterii, ile?<br/>Pokaż mi baterii|
|QueryWifi|Pobierz informacje o sieci Wi-Fi.|Uzyskaj informacje o sieci Wi-Fi.|
|Ponowne uruchamianie|Należy ponownie uruchomić urządzenie.|Uruchom ponownie.|
|RingDevice| Poproś urządzenia pierścień, aby ją znaleźć, gdy zostaje utracona.|Pierścień Mój telefon.| 
|SetBrightness|Ustaw jasność urządzenia.|Ustawianie jasności średni<br/>Ustawianie jasności wysoki<br/>Ustawianie jasności niski|
|SetupDevice|Zainicjuj konfiguracji urządzenia.|Chcę zainstalować Instalatora systemu operacyjnego<br/>Konfigurowanie.<br/>Czy ustawienia dla mnie|
|ShowAppBar|Pokaż pasek aplikacji.|Pokaż pasek aplikacji<br/>Sprawdź paska aplikacji<br/>Pozwól mi widzieć paska aplikacji|
|ShowContextMenu|Pokaż menu kontekstowe.|Pozwól mi wyświetlić menu kontekstowe<br/>Menu kontekstowe.<br/>Chcę zobaczyć menu kontekstowe|
|Stan uśpienia|Umieść urządzenia w tryb uśpienia.|Idź spać<br/>Stan uśpienia<br/>Mój komputer jest uśpiony|
|SwitchApplication|Łącznik aplikacji na używanie na urządzeniu.|Przełącz do mojego media player.|
|TurnDownBrightness|Zmniejsz jasność urządzenia.|Dim ekranu.|
|TurnOffSetting|Wyłącz ustawienie urządzenia.|Dezaktywowanie funkcji Bluetooth<br/>Wyłącz danych<br/>Odłącz bluetooth|
|TurnOnSetting|Włącz ustawienie urządzenia.|Włączone <br/> Wyłączone|
|TurnUpBrightness|Włącz się jasność urządzenia.|Czy można utworzyć ekran jaśniejszy?|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AppName | Nazwa aplikacji na urządzeniu.|SoundCloud<br/>YouTube|
| BrightnessLevel | Ustaw poziom jasności na urządzeniu.|Procent sto<br/>50<br/>40%|
| Nazwa kontaktu | Nazwa kontaktu na urządzeniu.|Paul<br/>Maksymalna liczba Marlen|
| Typ urządzenia | Typ urządzenia. |Numer telefonu<br/>Kindle<br/>Przenośny|
| Typ nośnika | Typ nośnika obsługiwanych przez urządzenie.|Muzyka<br/>Film<br/>Programy telewizyjne|
| Nazwy | Typ ustawienia lub panelu ustawień, które użytkownik chce, aby edytować.|Sieć Wi-Fi<br/>Sieć bezprzewodowa<br/>Schemat kolorów<br/>Centrum powiadomień|

## <a name="places"></a>Miejsca  
Domena miejsc zapewnia intencji do obsługi zapytań dotyczących takich firm, instytucja, restauracje, publicznego miejsca do magazynowania i adresów miejsc.

### <a name="examples"></a>Przykłady

|Przykłady|
|--|
|Zapisz tę lokalizację do moich ulubionych|
|Jak daleko jest dniem wolnym od pracy Inn?|
|W jakich czasie Safeway zamknąć?|


### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AddFavoritePlace | Dodawanie lokalizacji do listy ulubionych użytkownika.|Zapisz tę lokalizację do moich ulubionych<br/>Dodaj ten adres do moich ulubionych|
|CheckAccident|Zadaj pytanie, czy określony drogach jest awarii.|Na 880 jest awarii?<br/>Pokaż informacje o awarii|
|CheckAreaTraffic|Sprawdź ruch dla ogólnego obszaru lub Kalifornijskiej nie na określonej trasy.|Ruch w Seattle<br/>Co to jest traffic podobnie jak w Seattle?|
|CheckIntoPlace|Zaewidencjonuj w miejscu, za pomocą mediów społecznościowych.|Zaewidencjonuj mnie na Foursquare<br/>Sprawdź w tym miejscu|
|CheckRouteTraffic| Sprawdź ruch określoną trasę określone przez użytkownika.|W jaki sposób ruch do Mashiko?<br/>Pokaż mi ruch do Kirkland<br/>W jaki sposób ruch do Seattle?| 
|Potwierdź|Potwierdź akcję odnoszących się do miejsca.|Upewnij się, Moja Rezerwacja restauracji.|
|Zakończ|Akcja, aby zakończyć zadanie odnoszących się do miejsca.|Zamknij.<br/>Zamknij, zapewniając mnie kierunki|
|FindPlace|Wyszukaj miejsca (biznesowe, instytucja, restauracji, przestrzeń publicznych adresów).|Gdzie znajduje się najbliższy biblioteki?<br/>Znajdź dobre restauracji włoski w widoku górski|
|Getaddress —| Poproś o adres miejsca.|Pokaż adres Guu na Robson ulicy<br/>Co to jest adres najbliższej kawę?| 
|GetDistance|Poproś o odległość do określonego miejsca.|Jak daleko jest dniem wolnym od pracy Inn?<br/>jak daleko jest Bellevue kwadratowych, w tym miejscu<br/>Co to jest odległość Tahoe|
|GetHours|Poproś o godzin pracy dla miejsca.|W jakich czasie Safeway zamknąć?<br/>Ile godzin Home magazynu?<br/>Kawę jest wciąż otwarty?|
|GetMenu|Poproś o elementów menu dla restauracji.|Jest Zucca obsługi dowolnych elementów vegan?<br/>Co to jest w menu w Sizzler<br/>Pokaż menu Applebee firmy|
|GetPhoneNumber| Poproś o numer telefonu w miejscu.|Co to jest numer telefonu w najbliższej kawę?<br/>Zapewniają numer domu Depot| 
|GetPriceRange| Pyta, czy dla zakresu cena miejsca.|Jest Zucca tania?<br/>Jest Cineplex wysokości równej połowie ceny w środy?<br/>Jaki jest koszt obiad cała firma lobster na Sizzler?|
|GetReviews|Poproś o przeglądach miejsce.|Pokaż mi przeglądy Cheesecake fabryki<br/>Przeczytaj recenzje Cineplex w Yelp|
|GetRoute|Poproś o kierunkach w miejscu.|Jak przeprowadzi do Bellevue kwadrat<br/>Pokaż mi to najkrótszy sposób 8 a 59th, w tym miejscu<br/>Poznać mnie drogę do widoku górski urzędu certyfikacji|
|GetStarRating|Poproś o klasyfikacji w formie gwiazdek miejsce.|Jak jest Zucca sklasyfikowane zgodnie z Yelp<br/>Na ile gwiazdek ma prania francuski?<br/>Jest dobra akwarium w Monterrey?|
|GetTransportationSchedule|Pobierz harmonogram magistrali dla miejsca.|Czas, jaki jest dalej magistrali do Centrum?<br/>Pokaż mi autobusów w King County|
|GetTravelTime|Poproś o czas podróży do określonej lokalizacji docelowej.|Jak długo potrwa nawiązać połączenia z siecią San Francisco w tym miejscu<br/>Co to jest czas na Denver z SF|
|MakeCall|W miejscu, należy wykonać połączenie telefoniczne.|Wywołanie programu Microsoft Operations Manager<br/>Chcę wywołać Skype Anna<br/>Jim wywołania|
|MakeReservation|Żądania rezerwacji restauracji lub innych firm.|Zarezerwuj w Zucca dla dwa tonight<br/>Zarezerwuj tabeli na jutro<br/>Tabela 3 w Palo Alto na 8|
|MapQuestions|Żądanie informacji na temat wskazówek lub tego, czy określony drogowej przechodzi do miejsca docelowego.|13 przechodzą przez Centrum?<br/>Czy można wykonać 880 w celu Oakland?|
|Ocena|Pobierz opis klasyfikacji restauracji lub miejsca.|Na ile gwiazdek ma numer identyfikacyjny Contoso?|
|ReadAloud|Odczytywanie listy miejsc na głos.|Plik Readme, pierwszy z nich<br/>Plik Readme szczegóły|
|Selectitem —|Wybierz element z listy wyborów odnoszących się do miejsca lub miejsc.|Wybierz drugą<br/>Zaznacz pierwszą pozycję|
|ShowMap|Pokaż mapę obszaru.|Pokaż mapę dla drugiej<br/>Pokaż mapę<br/>Znajdź San Francisco na mapie|
|ShowNext|Pokaż następny element w serii.|Pokaż następny<br/>Przejdź do następnej strony|
|ShowPrevious|Pokaż poprzedni element w serii.|Pokaż poprzedni<br/>wstecz<br/>Przejdź do poprzedniego|
|StartOver|Ponowne uruchomienie aplikacji lub Rozpocznij nową sesję.|Zacznij od nowa<br/>Nowej sesji<br/>
restart|
|TakesReservations|Zapytaj, czy miejsce akceptuje rezerwacji.|Galerii sztuki akceptować rezerwacje<br/>Czy jest możliwe rezerwację w ogrodzie oliwek

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AbsoluteLocation | Lokalizacja lub adres miejsca.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| Pozwalającego | Celu cech/korzyści miejsce.|dzieci jeść bezpłatnie<br/>waterfront<br/>bezpłatne parkowania|
| Atmosfery | Atmosfery miejsce.|przyjazne dla dzieci<br/>zwykłych restauracji<br/>Sporty|
| Cuisine | Cuisine miejsca. |Śródziemnego<br/>Włoski<br/>Rupia|
| DestinationAddress| Lokalizacja docelowa lub adres.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationPlaceName| Nazwa miejsca docelowego, które jest biznesowych, restauracji, publiczne przyciągania lub instytucji.|park środkowa<br/>Safeway<br/>Walmart|
| DestinationPlaceType | Typ lokalizacji docelowej, która jest lokalnych firmach, restauracji, publiczne przyciągania lub instytucji. |Restauracja<br/>Opera<br/>kinowych|
| Odległość | Odległość w miejscu.|15 mil.<br/>5 miles<br/>10 mil stąd nigdzie|
| MealType | Typ posiłku, takich jak śniadanie lub obiad. |Śniadanie<br/>Obiad<br/>Obiad<br/>Supper|
| OpenStatus | Wskazuje, czy miejsce jest otwarte lub zamknięte.|Otwarty<br/>Zamknięte<br/>Otwieranie|
| PlaceName | Nazwa miejsca.|Fabryka cheesecake|
| PlaceType | Typ miejsce.|Kawiarnia<br/>Teatralną<br/>Biblioteka|
| PreferredRoute | Trasą preferowaną, określone przez użytkownika. | 101 <br/>202 <br/>Trasy 401|
| Product (Produkt) | Produkt, oferowane przez miejsce. | Odzież<br/>Aparaty cyfrowe usługi ASR<br/>Świeży ryb | 
| PublicTransportationRoute | Nazwa trasy transport publiczny, który wyszukuje użytkownika. | Północno-wschodniej korytarza szkolenie<br/>Tras 3 X |
| Ocena | Ocena miejsca. | 5 gwiazdek<br/>3 gwiazdek<br/>4 gwiazdek|
| RouteAvoidanceCriteria | Kryteria unikanie określonej trasy, takich jak unikania awarii, konstrukcje lub drogi | Drogi <br/>Konstrukcje<br/>Trasy 11|
| ServiceProvided | To jest usługa dostarczane przez firmę lub miejsca, takie jak nowe zlecenie, plowing, krajobrazu pozdrowienia z Seattle. | Pracownik<br/>mechanika<br/>plumber|
| TransportationCompany | Nazwa dostawcy transportu.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Typ transportu.|Service Bus<br/>Szkolenie<br/>Jazda samochodem|

## <a name="reminder"></a>Przypomnienie 
Domeny przypomnienie zapewnia intencje i podmioty, do tworzenia, edytowania i znajdowanie przypomnienia.

### <a name="examples"></a>Przykłady

|Przykłady|
|--|
|Zmień moje wywiad na jutro 9: 00|
|Przypomnij mi kupić mleka w mojej sposób kraju|
|Można sprawdzić Jeśli przypomnienie o datę urodzin Christine firmy?|


### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Change| Zmień przypomnienia.|Zmień moje wywiad na jutro 9: 00<br/>Przenieś przypomnienie Moje przypisania do jutro|
| Przycisk Utwórz| Utwórz nowe przypomnienie.|Tworzenie przypomnienia<br/>Przypomnij mi kupić mleka<br/>Chcę Pamiętaj, aby wywołać Rebecca, gdy jestem w domu|
| Usuwanie | Usuń przypomnienie.|Usuń przypomnienie Mój obraz<br/>Usuń to przypomnienie|
| Znajdowanie | Znajdź przypomnienia.|Czy mam przypomnienie o mojej rocznicy?<br/>Można sprawdzić Jeśli przypomnienie o datę urodzin Christine firmy?|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Tekst | Opis przypomnienia.|Podnieś czyszczenia<br/>upuszczanie samochodzie w Centrum usług|

## <a name="restaurantreservation"></a>RestaurantReservation 
Domeny RestaurantReservation zapewnia intencje i podmioty, powiązane z zarządzaniem rezerwacje restauracji.

### <a name="examples"></a>Przykłady

|Przykłady|
|--|
|Zarezerwuj w Zucca dla dwa tonight|
|Zarezerwuj tabeli na firmy BJ na jutro|
|Tabela 3 w Palo Alto na 7|

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Zarezerwuj | Żądania rezerwacji dla restauracji. |Zarezerwuj w Zucca dla dwa tonight<br/>Zarezerwuj tabeli na jutro<br/>Tabela 3 w Palo Alto na 7|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Adres| Lokalizacja zdarzenia lub adres dla rezerwacji.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| Pozwalającego | Atrybut opisujący pozwalającego miejsca.|Wyświetl Ocean<br/>inne niż palenia|
| AppName | Nazwa aplikacji składania rezerwacji.|OtwórzTabelę<br/>Yelp<br/>TripAdvisor|
| Atmosfery | Opis atmosfery restauracji lub innym miejscu.|Romantyczna<br/>zwykłych<br/>dobre dla grup|
| Cuisine | Typ żywności, cuisine lub cuisine przynależność. |Chiński<br/>Włoski<br/>Peso|
| MealType | Typ posiłku skojarzone z rezerwacji.|Śniadanie<br/>Obiad<br/>Obiad<br/>Supper|
| PlaceName | Nazwa lokalnych firmach, restauracji, publiczne przyciągania lub instytucji.|IHOP<br/>Fabryka cheesecake<br/>Louvre|
| PlaceType | Typ lokalnych firmach, restauracji, publiczne przyciągania lub instytucji.|Restauracji<br/>Opera<br/>kinowych|
| Ocena | Ocena miejsca lub restauracji.|5 gwiazdek<br/>3 gwiazdek<br/>4 gwiazdek|

## <a name="taxi"></a>Taksówek 
 
Domeny taksówek zapewnia intencje i podmioty, do tworzenia i zarządzania rezerwacje taksówek.

### <a name="examples"></a>Przykłady

|Przykłady|
|--|
|Pobierz plik cab w 15: 00|
|Jak długo ma czekać na Moje taksówek|
|Anuluj mojej Uber|

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Book (Książka) | Wywołaj taksówek. |Pobierz mnie plik cab<br/>Znajdź taksówek<br/>Zarezerwuj mnie uber x|
| Cancel | Anulowanie akcji dotyczących taksówek rezerwacji.|Anuluj mojej taksówek<br/>Anuluj mojej Uber|
| Śledź | Śledzenie trasy taksówek.|Jak długo ma czekać na Moje taksówek<br/>Gdzie jest Mój Uber?|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Adres| Adres skojarzony z rezerwacji taksówek. |Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationAddress| Lokalizacja docelowa lub adres. |Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationPlaceName | Nazwa miejsca docelowego, które jest lokalnych firmach, restauracji, publiczne przyciągania lub instytucji. |Park środkowa<br/>Safeway<br/>Walmart|
| DestinationPlaceType | Typ lokalizacji docelowej, która jest lokalnych firmach, restauracji, publiczne przyciągania lub instytucji. |Restauracja<br/>Opera<br/>kinowych|
| PlaceName | Nazwa lokalnych firmach, restauracji, publiczne przyciągania lub instytucji. |Park środkowa<br/>Safeway<br/>Walmart|
| PlaceType| Typ miejsca w żądaniu, aby zarezerwować taksówek.|Restauracja<br/>Opera<br/>kinowych|
| TransportationCompany | Nazwa dostawcy transportu.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Typ transportu.|Service Bus<br/>Szkolenie<br/>Jazda samochodem|

## <a name="translate"></a>Tłumaczenie 
Domeny Translate zapewnia intencje i podmioty, związane z tłumaczenie tekstu na język docelowy.

### <a name="examples"></a>Przykłady

|Przykłady|
|--|
|Tłumaczenie na Francuską|
|Tłumaczenie hello na język niemiecki|
|Tłumaczenie to pole następujące zdanie do języka angielskiego|


### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Tłumaczenie| Tłumaczenie tekstu na inny język.|Tłumaczenie na Francuską<br/>Tłumaczenie hello na język niemiecki|


### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| TargetLanguage | Język docelowy tłumaczenia.|Francuski<br/>Niemiecki<br/>Koreański|
| Tekst | Tekst do tłumaczenia.|Witaj, świecie<br/>Dzień dobry<br/>Dobry wieczór|

## <a name="tv"></a>TV 
 
Domeny TV przewiduje intencje i podmioty kontrolowanie TV.

### <a name="examples"></a>Przykłady

|Przykłady|
|--|
|Kanał przełącznika BBC|
|Przewodnik po Telewizyjnych show|
|Obejrzyj National geograficzne|

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| ChangeChannel| Zmień na ekranie Telewizora kanału.|Zmiana kanał CNN<br/>Kanał przełącznika BBC<br/>Przejdź do kanału 4|
| Showguide —| Pokaż przewodnik TV.|Przewodnik po Telewizyjnych show<br/>Co to jest w kanale filmu teraz?<br/>Pokaż Moje listy programów|
| WatchTV| Poproś obejrzeć kanał telewizyjny.|Czy chcesz obserwować kanał firmy Disney<br/>Przejdź do TV.<br/>Obejrzyj National geograficzne|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| ChannelName | Nazwa kanału telewizji.|CNN<br/>BBC<br/>Kanał filmu|

## <a name="utilities"></a>Usługi użyteczności publicznej  
Domena narzędzi zapewnia intencji zadań, które są wspólne dla wielu zadań, takich jak greetings anulowania, potwierdzenie, pomoc, powtórzenia, nawigacja, uruchamianie i zatrzymywanie.

### <a name="examples"></a>Przykłady

|Przykłady|
|--|
|Wróć do usługi Twitter|
|Pomóż|
|Powtórz ostatnie pytanie.|


### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Cancel | Anulowanie akcji.|Anuluj komunikat<br/>Nie chcę już Wyślij wiadomość e-mail|
| Potwierdź | Potwierdź akcję.|tak, już potwierdzam, że<br/>Dobra am I Potwierdzanie<br/>OK am I Potwierdzanie|
| FinishTask | Zakończ zadanie pracy użytkownika.|Wszystkie operacje zostały zakończone<br/>Zakończona<br/>To się robi|
| GoBack | Przejdź wstecz jeden krok lub wracasz do poprzedniego kroku.|Wróć do usługi Twitter<br/>Przejść wstecz<br/>Przejdź wstecz|
| Pomoc | Żądanie w celu uzyskania pomocy.|Pomóż<br/>Otwórz Pomoc<br/>pomoc|
| Powtarzanie | Powtórz akcję.|Powtórz ostatnie pytanie.<br/>Powtórz ostatni utwór|
| ShowNext | Pokaż następny element w serii. |Pokaż następny<br/>Przejdź do następnej strony|
| ShowPrevious | Pokaż poprzedni element w serii.|Pokaż poprzedni|
| StartOver | Ponowne uruchomienie aplikacji lub Rozpocznij nową sesję.|Zacznij od nowa<br/>Nowej sesji<br/>restart|
| Stop | Zatrzymaj akcji.| Zatrzymaj informujący o tym, ten problem<br/>Zamknij się<br/>Zatrzymaj.|

## <a name="weather"></a>Pogoda 
Domeny pogody przewiduje intencje i podmioty wprowadzenie prognoz i raporty o pogodzie.

### <a name="examples"></a>Przykłady

|Przykłady|
|--|
|Pogoda w Londynie we wrześniu|
|Jakie? s 10 dni prognozy?|
|Co to jest średnia temperatura w Indiach we wrześniu?|


### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| GetCondition | Uzyskaj historyczne fakty związane z o pogodzie. |Pogoda w Londynie we wrześniu<br/>Co to jest średnia temperatura w Indiach we wrześniu?|
| GetForecast | Pobierz bieżącą pogodę i Prognozowanie w ciągu kilku następnych dni. |W jaki sposób dane pogody już dzisiaj?<br/>Co to jest 10 dni prognozy?<br/>Jak będzie pogody to weekend?|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Lokalizacja| Lokalizacja bezwzględna dla żądania o pogodzie.|Seattle<br/>Paryż<br/>Palo Alto|

## <a name="web"></a>Sieć Web 
Domeny sieci Web udostępnia intencji do nawigowania do witryny sieci Web.

### <a name="examples"></a>Przykłady

|Przykłady|
|--|
|Przejdź do facebook.com|
|Przejdź do www.twitter.com|
|Przejdź do www.bing.com|

### <a name="intents"></a>Intencje
| Nazwa elementu Intent | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Nawigacja | Żądanie, aby przejść do określonej witryny sieci Web. |Przejdź do facebook.com<br/>Przejdź do www.twitter.com|

