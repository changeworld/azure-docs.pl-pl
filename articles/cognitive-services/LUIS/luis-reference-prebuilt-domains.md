---
title: Wbudowane domeny odwołanie - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dokumentacja dotycząca wbudowane domen, które są wbudowane kolekcje intencje i jednostek z języka opis inteligentnego usług (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 14c53bd25913922a0cd7cc438ad0fbe8b4663dd1
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061968"
---
# <a name="prebuilt-domain-reference"></a>Dokumentacja wstępnie utworzonych domen
Ta dokumentacja zawiera informacje na temat [wbudowane domen](luis-how-to-use-prebuilt-domains.md), które są wbudowane kolekcje intencje i jednostek, które oferuje LUIS.

[Domen niestandardowych](create-new-app.md), natomiast uruchomić bez intencje i modeli. Można dodać żadnych opcji wbudowane domeny i jednostki do modelu niestandardowych.

## <a name="list-of-prebuilt-domains"></a>Listy domen wbudowane
LUIS oferuje 20 wbudowane domen. 

| Wbudowane domeny | Opis | Obsługiwane języki |
| ---------------- |-----------------------|:------:|
| Kalendarz | Domena kalendarza zapewnia zamiar i jednostek, dodawanie, usuwanie, lub edycji termin, sprawdzanie dostępności uczestników i znajdowanie informacji na temat zdarzenia kalendarza.| pl-PL<br/> zh-CN |
| Aparat fotograficzny | Domeny aparatu zapewnia intencje i jednostek pobierania obrazów, nagrywanie wideo i emisji wideo do aplikacji.| pl-PL |
| Komunikacja | Wysyłanie wiadomości i rozmowy telefoniczne.| pl-PL <br/> zh-CN |
| Rozrywka  | Obsługa zapytań dotyczących muzyka, filmy i telewizji.| pl-PL |
| Zdarzenia | Rezerwacji biletów dla Koncerty, festiwale, gier oraz informacji sportowych i Komedia zawiera.| pl-PL |
| Przydatności | Obsługiwanie żądań związanych z śledzenie działań przydatności.| pl-PL |
| Gry | Obsługiwanie żądań związanych z gier strona gry dla wielu graczy.| pl-PL |
| HomeAutomation | Kontrolowanie inteligentne macierzystego urządzeniami, takimi jak świateł i urządzeń.| pl-PL<br/> zh-CN |
| MovieTickets | Rezerwacji biletów na wyświetlanie filmów w kina filmu.| pl-PL |
| Muzyka | Odtwarzanie utworów muzycznych na odtwarzacz muzyczny.| pl-PL<br/> zh-CN |
| Uwaga | Domena Uwaga zapewnia intencje i jednostek związane z tworzeniem, edytowaniem i znajdowanie uwagi.| pl-PL<br/> zh-CN |
| OnDevice | Domena OnDevice zapewnia intencje i obiektami powiązana do sterowania urządzeniem.| pl-PL<br/> zh-CN |
| Miejsca  | Obsługa zapytań związanych z takich miejsc, jak firmach, instytucji restauracji, publiczny spacje i adresów.| pl-PL<br/> zh-CN |
| Przypomnienie | Obsługa żądania związane z tworzeniem, edytowaniem i znajdowanie przypomnienia.| pl-PL<br/> zh-CN |
| RestaurantReservation | Obsługa żądań do zarządzania zastrzeżenia restauracji.| pl-PL<br/> zh-CN |
| Taksówki | Obsługa rezerwacji dla taksówki.| pl-PL<br/> zh-CN |
| Tłumaczenie | Tłumaczenie tekstu do języka docelowego.| pl-PL<br/> zh-CN |
| TV | Kontrolowanie telewizorach.| pl-PL |
| Usługi użyteczności publicznej  | Obsługę żądań, które są wspólne w wielu domenach, takich jak "pomoc", "Powtórz", "zacząć od nowa."| pl-PL |
| Pogody | Pobieranie pogody i prognoz.| pl-PL<br/> zh-CN |
| Sieć Web | Przejście do witryny sieci Web.| pl-PL<br/> zh-CN |

Aby uzyskać więcej szczegółów na każdej domeny Zobacz w kolejnych sekcjach.

## <a name="calendar"></a>Kalendarz 

Domena kalendarza zapewnia intencje i obiektów związanych z pozycji kalendarza. Opcje kalendarza obejmują dodawanie, usuwanie lub edytowanie terminu sprawdzanie dostępności i znajdowanie informacji o wpis kalendarza lub terminu.

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Add | Dodaj nowy element jednorazowego w kalendarzu.| Wprowadź termin z Ewa w niedzielę godzinie 2 <br/><br/>Warto zaplanować spotkanie<br/><br/>Należy skonfigurować spotkanie|
| CheckAvailability | Znaleźć dostępności terminu lub spotkania w kalendarzu użytkownika lub kalendarza innej osoby.| Gdy jest dostępne w celu spełnienia Jim? <br/><br/>Pokaż, gdy jutro Karolina jest dostępna<br/><br/>Krzysztof jest bezpłatne w sobotę?|
| Usuwanie | Żądanie usunięcia wpisu kalendarza.| Anuluj mojej terminu z Karolina. <br/><br/>Usuń spotkania 9 am<br/>|
| Edytuj | Żądanie zmiany istniejącego spotkania lub wpis kalendarza.| Przenieś spotkania 9 am 10 am.<br/><br/>Czy chcesz zaktualizować harmonogramu.<br/><br/>Reschdule spotkania z Ryan.|
| Znajdowanie | Wyświetlanie kalendarza co tydzień.| Znajdź stomatologowi Przejrzyj terminu. <br/><br/>Pokaż mój kalendarz<br/>|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Lokalizacja | Lokalizacja elementu kalendarza, spotkania lub terminu. Adresy, miast i regiony są dobrym przykładem lokalizacji.| Siłowni 209 Nashville <br/><br/>897 pancake DOM<br/><br/>Garaż|
| Temat | Tytuł spotkania lub terminu.| Termin w dentist <br/><br/>Obiad z Julia<br/><br/>Lekarza|

## <a name="camera"></a>Aparat fotograficzny 
Domena aparatu zapewnia intencje i jednostki powiązane z użyciem aparatu. Intencje obejmuje przechwytywanie fotografii, selfie, zrzut ekranu lub wideo i Emisja wideo do aplikacji.

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| CapturePhoto| Przechwyć zdjęcie.| Zrób zdjęcie<br/><br/>przechwytywania|
| CaptureScreenshot | Przechwyć zrzut ekranu.| Wykonać zrzutu ekranu.<br/><br/>Przechwytywanie ekranu.|
| CaptureSelfie | Przechwyć selfie.| Podejmij selfie <br/><br/>Tworzenie obrazu do mnie |
| CaptureVideo | Rozpocznij nagrywanie wideo.| Zacznij nagrywanie <br/><br/>Rozpoczęcie rejestrowania|
| StartBroadcasting| Uruchom wideo emisji.| Emisji Start dla usługi Facebook|
| StopBroadcasting| Zatrzymaj wideo emisji.| Zatrzymaj emisji|
| StopVideoRecording| Zatrzymaj nagrywanie wideo.| Jest wystarczająca<br/><br/>Zatrzymaj rejestrowanie|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AppName | Nazwa aplikacji w celu emisji wideo.| OneNote<br/><br/>Facebook<br/><br/>Skype|


## <a name="communication"></a>Komunikacja 
Domena komunikacji zapewnia intencje i obiektów związanych z poczty e-mail, wiadomości i połączeń telefonicznych.

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AddContact| Dodaj nowy kontakt do listy kontaktów użytkownika.|Dodaj nowy kontakt <br/><br/>Zapisz ten numer i umieszcza nazwę jako Karoliny|
| AddMore| Dodaj więcej na temat wiadomości e-mail lub tekst, jako elementu złożenia stopniowy tekst lub wiadomości e-mail.|Dodaj więcej do tekstu <br/><br/>Dodaj więcej na temat treść wiadomości e-mail|
| Odpowiedź| Odpowiedz przychodzących połączeń telefonicznych.|odpowiedzi na wywołanie <br/><br/>Podnieś ją|
| AssignContactNickname| Przypisz pseudonim do kontaktu.|Zmień Isaac Tato <br/>Edytuj Jim pseudonim<br/>Dodaj do Patti Owens pseudonim|
| CallVoiceMail| Połącz użytkownika poczty głosowej.|Połączenie okno Mój pocztę głosową <br/>Poczta głosowa<br/>Wywołanie pocztę głosową|
| CheckIMStatus| Sprawdź stan kontaktu w programie Skype.|Stan online na Jim ustawiono optymalizacji? <br/>Karolina jest dostępne rozmawiać z?|
| Potwierdź| Potwierdź akcję.|Yes<br/>OK<br/>W porządku<br/>Potwierdzam, że chcę wysłać tę wiadomość e-mail.<br/>|
| telefoniczny| Nawiązywanie połączeń telefonicznych.|Wywołanie Jim<br/>Należy wybrać 311<br/>|
| FindContact| Znajdowanie informacji kontaktowych według nazwy.|Znajdź numer dla Karoliny<br/>Pokaż numer dla Karoliny<br/>|
| FindSpeedDial| Znajdź numer szybkie, wybieranie numeru, numer telefonu jest ustawiony na i na odwrót.|Co to jest mój numer wybierania 5?<br/>Należy wybrać zestaw szybkości?<br/>Co to jest numer wybierania 941-5555-333?|
| GetForwardingsStatus| Pobierz bieżący stan przekazywania połączeń.|Czy włączone jest Mój przekazywania połączeń?<br/>Powiedz mi, jeżeli Mój stan wywołania ma wartość<br/>|
| GoBack| Wróć do poprzedniego kroku.|Wróć do usługi twitter<br/>Przejść wstecz<br/>Wstecz|
| Zignoruj| Ignoruj połączenia przychodzącego.|Nie można znaleźć odpowiedzi<br/>Ignoruj wywołania|
| IgnoreWithMessage| Ignoruj połączenia przychodzącego i zamiast tego odpowiedzi z tekstem.|Nie można znaleźć odpowiedzi tego wywołania, ale zamiast tego Wyślij wiadomość.<br/>Ignoruj i Wyślij ponownie tekstu.|
| PressKey| Naciśnij przycisk lub numer na klawiaturze.|Wybierania gwiazdkę.<br/>Naciśnij klawisz 1 2 3.|
| ReadAloud| Przeczytaj komunikat lub wiadomości e-mail do użytkownika.|Przeczytaj tekst.<br/>Co ona powiedzieć w komunikacie?|
| TurnForwardingOff| Nawiązywanie połączeń telefonicznych.|<br/><br/>|
| Wybierz ponownie| Wybierz ponownie lub zadzwoń liczbą ponownie.|Wybierz ponownie.<br/>Wybierz ponownie moje ostatnim wywołaniu.|
| Reject| Odrzucanie połączenia przychodzącego.|Odrzuć wywołanie<br/>Nie można odebrać<br/>Nie jest dostępny w tej chwili i nawiąże połączenie ponownie później.|
| SendEmail| Wyślij wiadomość e-mail. Celem tego stosuje się do poczty e-mail, ale nie wiadomości SMS.|Wiadomości e-mail, aby Jan wody: Mike'owi, czy świetnie się obiad ostatniego tygodnia.<br/>Wyślij wiadomość e-mail do niego<br/>|
| SendMessage| Wyślij wiadomość SMS lub wiadomości błyskawicznej.|Wyślij tekst Krzysztof i Karoliny|
| SetSpeedDial| Ustaw skrót wybierania szybkości dla numeru telefonu kontaktu.|Ustaw szybkiego wybierania, jeden dla Karoliny.<br/>Konfigurowanie szybkiego wybierania dla programu Microsoft Operations Manager.|
| ShowNext| Na przykład, zobacz następny element na liście wiadomości SMS lub wiadomości e-mail.|Pokaż kolejnego.<br/>Przejdź do następnej strony.|
| ShowPrevious| Na przykład, zobacz poprzedni element na liście wiadomości SMS lub wiadomości e-mail.|Pokaż poprzedni.<br/>Poprzednie<br/>Przejdź do poprzedniej.|
| StartOver| Zacznij od nowa system lub uruchomić nową sesję.|Zacznij od nowa<br/>Nowa sesja<br/>restart|
| TurnForwardingOff| Wyłącz funkcję przekazywania połączeń.|przerwanie przekazywania Moje rozmowy<br/>Wyłącz przekazywania połączeń|
| TurnForwardingOn| Wyłącz telefonu osoby mówiącej.|Moje wywołań 3333 przekazywania<br/>Przełącz na wywołanie funkcji przekazywania danych do 3333|
| TurnSpeakerOff| Wyłącz telefonu osoby mówiącej.|Chcę wyłączyć prelegenta.<br/>Wyłącz głośnomówiącego.<br/>|
| TurnSpeakerOn| Włącz telefonu osoby mówiącej.|Tryb głośnomówiącego.<br/>Umieść głośnomówiącego.<br/>|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AudioDeviceType | Typ urządzenia audio (prelegenta, słuchawki, mikrofon itp.).| Prelegent<br/>Bezobsługowego<br/>Bluetooth|
| Kategoria | Kategoria wiadomości lub wiadomości e-mail.| Ważne<br/>Wysoki priorytet|
| ContactAttribute | Atrybut kontaktu użytkownik zapytania o.| Dat urodzenia<br/>Adres<br/>Numer telefonu|
| Nazwa kontaktu | Nazwa adresata kontakt lub wiadomość.| Karolina<br/>Jim<br/>Krzysztof|
| EmailSubject | Tekst używany jako wiersz tematu wiadomości e-mail.| Odp: interesujące wątku|
| Kreska | Wiersz użytkownik chce używany do nawiązania połączenia lub wysyłania tekstu/e-mail z.| Praca z wiersza<br/>Brytyjskie komórki<br/>Skype|
| Komunikat | Komunikat do wysłania wiadomości e-mail lub tekst.| Było bardzo spotkanie dzisiaj. Zobacz możesz ponownie wkrótce!|
| MessageType | Nazwa adresata kontakt lub wiadomość.| Tekst<br/>Email|
| OrderReference | {Numer porządkowy lub względny pozycja na liście identyfikacji elementu do pobrania. Na przykład "ostatni" lub "najnowsze" w "Jaki był ostatni komunikat wysłany?"| Ostatnia<br/>Najnowsze|
| SenderName | Nazwa nadawcy.| Patti Owens|

## <a name="entertainment"></a>Rozrywka  
Pokazuje rozrywki, domeny zapewnia intencje i obiektów związanych z wyszukiwanie filmów, muzyki, gier i TV.

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Wyszukiwanie| Pokazuje Wyszukaj filmów, muzyki, aplikacje, gier i telewizji.|Wyszukaj w sklepie Halo.<br/>Wyszukiwanie awatara.|

### <a name="entities"></a>Jednostki
| Nazwy podmiotów | Opis | Przykłady |
| ---------------- |-----------------------|----|
| ContentRating | Nośnik zawartości ocenę G lub R filmów.|Wideo dzieci.<br/>PG sklasyfikowane.|
| Genre | Genre film, gier, aplikacji lub utworu.|Comedies<br/>Dramatów<br/>Zabawne|
| Słowo kluczowe| Słowa kluczowe do wyszukania ogólne określenie atrybutu nie istnieje w bardziej szczegółowe miejsc nośnika.|Ścieżek dźwiękowych<br/>Rzeka księżyca<br/>Amelia Earhart|
| Język | Nośnik zawartości ocenę G lub R filmów.|Francuski<br/>Polski<br/>Koreański|
| MediaFormat | Dodatkowe specjalne techniczne typu, w którym sformatowaniu nośnika.|HD filmy<br/>Filmy 3D<br/>Do pobrania|
| MediaSource | W magazynie lub marketplace w celu uzyskania nośnika.|Netflix<br/>Pierwsze|
| MediaSubTypes| Mniejsze niż filmy i gry typów nośników.|Pokazy<br/>DLC<br/>Przyczep|
| Przynależność| Kraju, w którym utworzono filmu, Pokaż lub utworu.|Francuski<br/>Niemiecki<br/>Koreański|
| Person (Osoba)| Aktora, dyrektor, producenta, muzykiem lub wykonawcy film, aplikacji, gry lub programu TV.|Madonna<br/>Stanley Kubrick|
| Rola| Rolę odgrywaną przez osobę w przypadku tworzenia nośnika.|Sings<br/>Postępując<br/>Od|
| Stanowisko| Nazwa filmu, aplikacji, gier, programu TV lub utworu.|Znajomych<br/>Minecraft|
| Typ| Format typu lub nośnik film, aplikacji, gier, programu TV lub utworu.|Muzyka<br/>MovieTV <br/>Pokazuje|
| UserRating| Gwiazdę użytkownika lub Kciuki klasyfikacji.|5 gwiazdek<br/>3 gwiazdki<br/>4 gwiazdki|

## <a name="events"></a>Zdarzenia 
Domena zdarzenia zapewnia intencje i jednostek związanych z rezerwacji biletów dla zdarzeń, takich jak Koncerty, festiwale, gier oraz informacji sportowych i Komedia pokazuje.

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Podręcznik| Kup bilety ze zdarzeniem.|Chcę kupić biletu dla symphony tego weekend.|


### <a name="entities"></a>Jednostki
| Nazwy podmiotów | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Adres | Lokalizacja zdarzeń lub adres. |Palo Alto<br/>300 112th Ave. SE <br/> Seattle |
| Name (Nazwa) | Nazwa zdarzenia.|Szekspir w wstrzymywanie|
| PlaceName| Nazwa lokalizacji zdarzenia.|Louvre<br/>Opera DOM<br/>Broadway|
| PlaceType | Typ lokalizacji zdarzenie będzie przechowywać w formacie.|Cafe<br/>Wojennych<br/>Biblioteka|
| Typ | Typ zdarzenia.|Porozumieniu<br/>Gry sportowych|

## <a name="fitness"></a>Przydatności 
Domena przydatności zapewnia lokalizacji docelowych i związanych z śledzenie działań przydatności jednostek. Profile zawierają zapisywanie notatek, pozostały czas lub odległość lub zapisywanie wyników działania.

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AddNote| Dodaje dodatkowe uwagi do śledzonych działania.|Trudności w tym przebiegu zostało 6: 10<br/>Terenu użytkownik w systemie jest asfalt<br/>Używam roweru szybkości 3|
|GetRemaining| Pobiera pozostały czas lub odległość dla działania.|Czas, jaki do następnego laptop?<br/>Ile mil pozostają w przebiegu moich? Czas, jaki podziału?|
| LogActivity| Zapisz lub rejestrować wyniki ukończonego działania.|Zapisz moje ostatniego uruchomienia<br/>Zaloguj się Moje przeszukiwania rano sobota<br/>przechowywanie Moje poprzedniej pływackiej|
| LogWeight| Zapisz lub logowania wagi bieżącego użytkownika.|Zapisz moje bieżące wagi<br/>Zaloguj się teraz moje wagi<br/>przechowywanie Moje bieżące wagi treści|

### <a name="entities"></a>Jednostki
| Nazwy podmiotów | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Właściwość ActivityType | Typ działania do śledzenia. |Uruchom polecenie<br/>Przeszukiwania<br/>Pływackiej<br/>Cykl |
| Żywność | Typ żywności do śledzenia w przydatności aplikacji. |Bananów<br/>Łososia<br/>Potrząsania powoduje białka|
| MealType| Typ zawierają najważniejsze nowości do śledzenia w kondycji lub przydatności aplikacji.|Śniadanie<br/>Obiad<br/>Obiad<br/>Supper|
| Miara| Typ wartości czasu, odległości lub wagi do użycia w aplikacji przydatności lub kondycji.|Kilometrach<br/>Miles<br/>Minuty<br/>Kg|
| Liczba | Wartość numeryczną do użycia w aplikacji przydatności lub kondycji.|19<br/>trzy<br/>200<br/>jeden|
| StatType | Typ statystyki na dane zagregowane do użycia w aplikacji przydatności lub kondycji.|Suma<br/>Średnia<br/>Maksimum<br/>Minimalne|

## <a name="gaming"></a>Gry 
Domena gier zapewnia intencje i powiązane z zarządzaniem gier strona gry dla wielu graczy jednostek.

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| InviteParty| Zapraszanie kontaktu sprzęgać strona gier.|Zaproś ten odtwarzacz do mojej firmy<br/>Dostarczane do mojej firmy<br/>Dołącz do mojego clan|
|LeaveParty| Pobiera pozostały czas lub odległość dla działania.|Chcę się<br/>Jestem I opuszczenie tej strony, do innego<br/>Jestem I Kończenie działania|
| StartParty| Uruchom strona gier gry dla wielu graczy.|Załóżmy był start stroną<br/>Uruchom stroną<br/>należy Rozpoczniemy clan zmieniły|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Kontakt| Nazwisko osoby kontaktowej w gry dla wielu graczy.|Karolina<br/>Jim|


## <a name="homeautomation"></a>HomeAutomation 
Domena HomeAutomation zapewnia intencje i obiektów związanych z kontrolowanie inteligentne macierzystego urządzeniami, takimi jak świateł i urządzeń.

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Wyłączanie| Wyłącz, zamknij lub odblokowania urządzenia.|Wyłącz światła<br/>Zatrzymaj maker kawy<br/>Zamknij garaż drzwi|
|Wlaczac| Włącz na urządzeniu lub ustawić urządzeń do danego ustawienia lub tryb.|Włącz Moje maker kawy<br/>można włączyć na mój maker kawy?<br/>Ustaw termostat 72 stopni.|


### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Urządzenie | Typ urządzenia, które mogą być włączone lub wyłączone.|maker kawy<br/>Termostat<br/>świateł|
| Operacja | Stan urządzenia.|blokady<br/>otwórz<br/>włączone<br/>wyłączone|
| Miejsca | Lokalizacja lub miejsca, w których urządzenie jest w.|Salon<br/>sypialnię<br/>kuchni|

## <a name="movietickets"></a>MovieTickets 
Domena MovieTickets zapewnia intencje i jednostki powiązane z rezerwacji biletów na wyświetlanie filmów w kina filmu.

### <a name="examples"></a>Przykłady
```
Book me two tickets for Captain Omar and the two Musketeers
Cancel tickets
When is Captain Omar showing?
```

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Podręcznik | Zakup filmu biletów.|Skoroszyt dwóch biletów Omar kapitana i dwa muszkieterami<br/>Chcę kupić biletu dla filmu w przyszłości<br/>Chcę biletu Captian Omar część 2 dalej środa|
|GetShowTime| Pobierz showtime filmu.|Gdy jest pokazywany Omar kapitana|


### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Adres | Adres kina filmu.|Palo Alto<br/>300 112th Ave. SE<br/>Seattle|
| MovieTitle | Tytuł filmu.|Czas życia Pi<br/>Głód gry<br/>Incepcja|
| PlaceName | Nazwa kina filmu lub kin.|Amir kinowych<br/>Wojennych Aleksandrii<br/>Kina Nowym Jorku|
| PlaceType | Typ lokalizacji filmu jest wyświetlany w.|kinowych<br/>kina<br/>IMAX kinowych|

## <a name="music"></a>Muzyka 
Domena utworów muzycznych zapewnia intencje i obiektów związanych z odtwarzanie utworów muzycznych na odtwarzacz muzyczny.

### <a name="examples"></a>Przykłady
```
play Beethoven
Increase track volume
Skip to the next song
```

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| DecreaseVolume | Zmniejsz głośność urządzenia.|zmniejszyć wolumin ścieżki<br/>wolumin w dół|
| IncreaseVolume | Zwiększ wielkość urządzenia.|Zwiększ wolumin ścieżki<br/>wolumin w górę|
| Wyciszanie |Wyciszanie odtwarzania utworów muzycznych.|Wyciszenia utworu<br/>Śledź na wyciszanie PUT<br/>Wyciszenia utworów muzycznych |
| Wstrzymaj | Zatrzymaj odtwarzanie utworów muzycznych.|Wstrzymaj<br/>Wstrzymaj utworów muzycznych<br/>Śledź Wstrzymaj|
| PlayMusic | Odtwarzanie muzyki na urządzeniu.|Odtwórz Kevina Durant<br/>Odtwórz Paradise przez Coldplay<br/>Odtwórz Hello przez Adele|
| Powtarzanie |Powtórz odtwarzania utworów muzycznych.|Powtórz utworu<br/>Odtwórz korzyści ścieżki<br/>Powtórz utworów muzycznych|
| Resume | Wznawia odtwarzanie utworów muzycznych.|Wznów utworu<br/>Uruchom ponownie utworów muzycznych<br/>Wznów działanie|
| SkipBack | Przejdź wstecz ścieżki.|Przejdź do następnego utworu<br/>Odtwarzanie utworu dalej|
| SkipForward |Przejdź do przodu ścieżki.|Odtwarzanie poprzedniego<br/>Wróć do poprzedniej ścieżki |
| Stop | Zatrzymaj akcji odnoszących się do odtwarzania utworów muzycznych. |Zatrzymaj odtwarzanie tego albumu.|
| Włącz | Włącz urządzenie odtwarzania utworów muzycznych.| Włącz.|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| ArtistName | Aktora, dyrektor, producenta, moduł zapisujący, muzykiem lub wykonawcy skojarzone z nośnika, aby odtworzyć na urządzeniu.|Elvis Presley<br/>Taylora Swift<br/>Adele<br/>Mozart|
| Genre | Genre muzyki żądanej.|Muzyka kraju<br/>Klasyka Broadway<br/>Odtwórz muzyka klasycznego z Barok okresu.|

## <a name="note"></a>Uwaga 
Domena Uwaga zapewnia intencje i jednostek związane z tworzeniem, edytowaniem i znajdowanie uwagi.

### <a name="examples"></a>Przykłady
```
Add to my groceries note lettuce tomato bread coffee
Check off bananas from my grocery list
Remove all items from my vacation list
```

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AddToNote | Dodawanie informacji do notatki.|Dodaj do moich artykuły spożywcze Uwaga sałaty pomidora chleb kawy<br/>Dodaj do listy todo<br/>Dodaj do mojego Wunderlist babeczki|
| CheckOffItem | Zaznaczenie elementów z istniejącym notatki.|Zaznacz bananów z listy spożywczych<br/>Oznacz ciasto ser na mój przedświątecznych zakupów listy jako gotowe|
| Clear | Wyczyść wszystkie elementy z istniejącym notatki.|Usuń wszystkie elementy z listy urlopu<br/>Wyczyść wszystkie z listy odczytu|
| Potwierdź | Potwierdzenie akcji dotyczących notatki.|Ma nic złego przeze mnie<br/>tak<br/>Jestem potwierdzenie przechowywanie wszystkich elementów na listach|
| Przycisk Utwórz | Tworzenie nowej notatki. | Utwórz listę<br/>Należy pamiętać, aby Przypomnij tego Jason w mieście pierwszym tygodniu maja|
| Usuwanie | Usuń całą notatkę. |Usuń z listy urlopu <br/>Usuń Moje Uwaga artykuły spożywcze|
| DeleteNoteItem | Usunięcie elementów z istniejącym notatki.| Usuń z listy spożywczych moduły<br/>Usuwanie pióra z mojej szkole zakupy listy|
| ReadAloud | Odczytywanie listy głos.|Plik Readme pierwsza z nich<br/>Plik Readme szczegóły|
| ShowNext | Zobacz następny element na liście notatek.|Pokaż następny<br/>Następna strona<br/>Następne kroki|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AppName | Nazwa aplikacji notatek.|Wunderlist<br/>OneNote|
| Nazwa kontaktu | Nazwa kontaktu w notatki.|Karolina<br/>Jim<br/>Krzysztof|
| Źródło danych | Lokalizacja uwagi.|OneDrive<br/>Dokumentacja usługi Google<br/>komputer|
| DataType | Typ pliku lub dokument, zwykle skojarzony z określonym programów.|Slajdów<br/>Arkusz kalkulacyjny<br/>Arkusz|
| Tekst | Tekst uwagi lub przypomnienia.|Rozciąganie przed przejście<br/>jutro długoterminowym.|
| Stanowisko | Tytuł notatki.|artykuły spożywcze<br/>osoby do wywołania<br/>zadania do wykonania|

## <a name="ondevice"></a>OnDevice 
Domena OnDevice zapewnia intencje i obiektami powiązana do sterowania urządzeniem.

### <a name="examples"></a>Przykłady
```
Close video player
Cancel playback
Can you make the screen brighter?
```

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AreYouListening | Zapytaj, czy urządzenie jest nasłuchiwanie.|jest to na?<br/>Możesz nasłuchują?|
|CloseApplication|Zamknij aplikację dla urządzeń.|Zamknij odtwarzacza wideo|
|FileBug|Błąd pliku na urządzeniu.|Sprawdź plik usterki<br/>Można pliku usterki dla mnie?<br/>Chcę zgłosić ten błąd|
|GoBack|Zapytaj wrócić do poprzedniej strony jeden krok lub powrócić do poprzedniego kroku.|Należy wrócić<br/>Przejdź do poprzedniego ekranu<br/>Przejdź wstecz Zatrzymano nasłuchiwanie|
|Pomoc| Prosić o pomoc.|Sprawdź pomoc<br/>Witamy<br/>Co należy zrobić?<br/>Potrzebuję pomocy| 
|LocateDevice|Znajdź urządzenia.|Możesz znaleźć Mój telefon<br/>Znajdź w Tomasz iphone<br/>Znajdź Mój telefon|
|Logowanie|Zaloguj się do usługi przy użyciu urządzenia.|Logowania należy<br/>Logowanie do usługi Facebook<br/>Zaloguj się do LinkedIn|
|Wyloguj|Wyloguj się z usługą za pomocą urządzenia.|Wyloguj Mój telefon<br/>Zaloguj się do usługi twitter<br/>Wyloguj się|
|MainMenu —|Wyświetlić menu głównego urządzenia.|Menu Widok.|
|OpenApplication|Otwórz istniejącą aplikację na urządzeniu.|Proszę Otwórz alarmu<br/>Włącz używanie aparatu<br/>Uruchamianie kalendarza|
|OpenSetting|Otwórz ustawienia na urządzeniu.|Otwórz ustawienia sieciowe.|
|PairDevice|Para urządzenia.|Proszę o pomoc w parowanie sygnał Bluetooth na telefon<br/>Włączanie funkcji bluetooth i Sparuj go z komputera przenośnego<br/>Sygnał Bluetooth par laptopie|
|Wyłącz zasilanie | Wyłącz urządzenie.|Możesz wyłączyć komputer<br/>Zamknij<br/>Wyłącz Mój telefon komórkowy|
|QueryBattery|Pobiera informacje o czas pracy baterii.|Pokaż czas pracy baterii.<br/>Co to jest Mój stan baterii<br/>Ile pracy baterii pozostało teraz?<br/>Pokaż baterii|
|QueryWifi|Pobiera informacje o sieci Wi-Fi.|Pobierz informacje o sieci Wi-Fi.|
|Ponowne uruchamianie|Należy ponownie uruchomić urządzenie.|Uruchom ponownie.|
|RingDevice| Poproś urządzenia pierścień, aby go znaleźć, gdy zostaje utracona.|Pierścienia Mój telefon.| 
|SetBrightness|Ustaw jasność urządzenia.|Ustawianie jasności średni<br/>Ustawianie jasności wysoki<br/>Ustawianie jasności niski|
|SetupDevice|Inicjowanie konfiguracji urządzenia.|Chcę zainstalować Instalatora systemu operacyjnego<br/>Sprawdź ustawienia<br/>Czy ustawienia dla mnie|
|ShowAppBar|Pokaż pasek aplikacji.|Pokaż pasek aplikacji<br/>Sprawdź paska aplikacji<br/>Zezwól na pasku aplikacji w temacie|
|ShowContextMenu|Pokaż menu kontekstowe.|Mogę wyświetlić menu kontekstowe<br/>Menu kontekstowe należy<br/>Chcę zobaczyć menu kontekstowe|
|Obsługa uśpienia|Umieść urządzenia w stan uśpienia.|Idź spać<br/>Obsługa uśpienia<br/>Mój komputer jest uśpiony|
|SwitchApplication|Przełączanie aplikacji do użycia na urządzeniu.|Przełącz do mojego media player.|
|TurnDownBrightness|Zmniejsz jasność urządzenia.|Dim ekranu.|
|TurnOffSetting|Wyłącz ustawienia urządzenia.|Dezaktywuj Bluetooth<br/>Wyłącz danych<br/>Odłącz bluetooth|
|TurnOnSetting|Włącz ustawienia urządzenia.|Włączone <br/> Wyłączone|
|TurnUpBrightness|Włącz zapasowej jasności urządzenia.|Czy można utworzyć ekranu jaśniejszy?|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AppName | Nazwa aplikacji na urządzeniu.|SoundCloud<br/>YouTube|
| BrightnessLevel | Ustaw poziom jasności na urządzeniu.|Procent sto<br/>50<br/>40%|
| Nazwa kontaktu | Nazwa kontaktu na urządzeniu.|Pawła<br/>Maksymalna liczba Marlen|
| DeviceType | Typ urządzenia. |Numer telefonu<br/>Kindle<br/>Laptop|
| Typ nośnika | Typ nośnika, obsługiwane przez urządzenia.|Muzyka<br/>Film<br/>Programy telewizyjne|
| Typu serwera | Typ ustawienia lub panel ustawień, które użytkownik chce edytować.|Wi-Fi<br/>Sieci bezprzewodowej<br/>Schemat kolorów<br/>Centrum powiadomień|

## <a name="places"></a>Miejsca  
Domena miejsca zapewnia opcje obsługi zapytań dotyczących miejsc, jak firmach, instytucji, restauracji, publicznego spacje i adresy.

### <a name="examples"></a>Przykłady
```
Save this location to my favorites
How far away is Holiday Inn?
At what time does Safeway close?
```

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AddFavoritePlace | Dodaj lokalizację do listy ulubionych użytkownika.|Ta lokalizacja zapisywania do moich ulubionych<br/>Dodaj ten adres do moich ulubionych|
|CheckAccident|Zapytaj, czy na drodze określony jest awarii.|Na 880 jest przypadkowo?<br/>Pokaż informacje o awarii|
|CheckAreaTraffic|Sprawdź ruchu dla ogólnego obszaru lub autostrady, nie na określoną trasę.|Ruch w Seattle<br/>Co to jest ruchu podobnie jak w Seattle?|
|CheckIntoPlace|Sprawdź miejscu przy użyciu mediów społecznościowych.|Zaewidencjonuj mi na usługi Foursquare<br/>Sprawdź w tym miejscu|
|CheckRouteTraffic| Sprawdź ruch określoną trasę określone przez użytkownika.|W jaki sposób ruch do Mashiko?<br/>Pokaż traffice do Kirkland<br/>W jaki sposób ruch do Seattle?| 
|Potwierdź|Potwierdzenie akcji dotyczących miejsca.|Potwierdź Moje rezerwacji restauracji.|
|Zakończ|Akcja, aby zakończyć zadanie odnoszących się do miejsca.|Należy zamknąć<br/>Zamknij, podając mnie wskazówki|
|FindPlace|Wyszukiwanie miejsca (business, instytucji, restauracji, przestrzeń publicznych adresów).|Gdzie jest najbliższej biblioteki?<br/>Znaleźć dobrej restauracji Włoska w widoku górski|
|GetAddress| Poproś o adres miejsca.|Pokaż adres Guu na Robson ulica<br/>Co to jest adres najbliższej Starbucks?| 
|GetDistance|Poproś o odległość do określonego miejsca.|Odległość jest Inn świątecznych?<br/>jak daleko jest do Bellevue kwadratowych w tym miejscu<br/>Co to jest odległość Tahoe|
|GetHours|Poproś o godzin pracy dla miejsca.|Pory Safeway zamknąć?<br/>Co to są godzin Home magazynu?<br/>Starbucks jest wciąż otwarty?|
|GetMenu|Poproś o elementów menu dla restauracji.|Jest Zucca służyć niczego vegan?<br/>Co to jest menu w Sizzler<br/>Pokaż Applebee w menu|
|GetPhoneNumber| Poproś o numer telefonu miejsca.|Co to jest numer telefonu, z dokładnością Starbucks?<br/>Wprowadź numer dla Home magazynu| 
|GetPriceRange| Pyta o zakresie cena miejsca.|Jest Zucca tanie?<br/>Jest cena połowa Cineplex w środy?<br/>Jaki jest koszt obiad całego homarca na Sizzler?|
|GetReviews|Poproś o przeglądami miejsce.|Pokaż przeglądami Cheesecase fabryki<br/>Czytamy Cineplex w Yelp|
|GetRoute|Poproś o instrukcje miejsce.|Jak opisano do Bellevue kwadratowe<br/>Pokaż najkrótszy sposobem 8 a 59th w tym miejscu<br/>Pobierz mnie kierunki do góry widoku urzędu certyfikacji|
|GetStarRating|Poproś o gwiazdek miejsca.|Jak jest Zucca sklasyfikowane zgodnie z Yelp<br/>Ile gwiazdek ma pralnicze francuski?<br/>Jest dobry akwarium w Monterrey?|
|GetTransportationSchedule|Pobierz harmonogram magistrali miejsce.|Czas, jaki jest szyny dalej do downtown?<br/>Pokaż magistrali w króla Województwo|
|GetTravelTime|Poproś o czas podróży do określonej lokalizacji docelowej.|Jak długo trwa na uzyskanie dostępu do sieci San Francisco w tym miejscu<br/>Co to jest czas Zakopanego z rz|
|MakeCall|Rozpoczynanie rozmowy telefonicznej do miejsca.|Wywołania programu Microsoft Operations Manager<br/>Chcę wywołać Skype Anna<br/>Wywołanie Jim|
|MakeReservation|Żądanie rezerwacji restauracji lub innych firm.|Zarezerwuj na Zucca dla dwóch dla tonight<br/>Podręcznik tabeli w bieżącym<br/>Tabela 3 w Palo Alto na 8|
|MapQuestions|Żądanie informacji na temat wskazówek lub czy określonego drogowej przechodzi do miejsca docelowego.|13 przekazywane downtown?<br/>Czy można wykonać 880 w celu Oakland?|
|Klasyfikacja|Opis klasyfikacji restauracji lub miejsce pobierania.|Ile gwiazdek ma numer identyfikacyjny Contoso?|
|ReadAloud|Odczytaj listę miejsca głos.|Plik Readme pierwsza z nich<br/>Plik Readme szczegóły|
|SelectItem|Wybierz element z listy wyborów odnoszących się do miejsca lub miejsca.|Wybierz drugi<br/>Wybierz pierwsze|
|ShowMap|Pokaż mapę obszaru.|Pokaż mapę dla drugiej<br/>Pokaż mapę<br/>Znajdź Poznań na mapie|
|ShowNext|Wyświetlenie następnego elementu w serii.|Pokaż następny<br/>Przejdź do następnej strony|
|ShowPrevious|Pokaż poprzedni element w serii.|Pokaż poprzedniego<br/>wstecz<br/>Przejdź do poprzedniego|
|StartOver|Ponownie uruchom aplikację i rozpocznij nową sesję.|Zacznij od nowa<br/>Nowa sesja<br/>
restart|
|TakesReservations|Zapytaj, czy miejsce akceptuje rezerwacji.|Galeria są akceptowane zastrzeżenia<br/>Czy jest możliwe rezerwować na ogrodu oliwek

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| AbsoluteLocation | Lokalizacja lub adres miejsca.|Palo Alto<br/>300 112th Ave. SE<br/>Seattle|
| Obiektów | Celu właściwości/korzyści miejsce.|dzieci jeść bezpłatne<br/>waterfront<br/>Zwolnij postojowe|
| Atmosfery | Atmosfery miejsce.|Kącik przyjaznego<br/>restauracja zwykłych<br/>Sporty|
| Cuisine | Cuisine miejsca. |Śródziemnego<br/>Włoski<br/>Indyjskiego|
| DestinationAddress| Lokalizacja docelowa lub adres.|Palo Alto<br/>300 112th Ave. SE<br/>Seattle|
| DestinationPlaceName| Nazwa lokalizacji docelowej, która jest biznesowych, restauracji, przyciągania publicznych lub instytucji.|Wstrzymywanie środkowe<br/>safeway<br/>walmart|
| DestinationPlaceType | Typ lokalizacji docelowej, która jest firm, restauracji, przyciągania publicznych lub instytucji. |Restauracja<br/>Opera<br/>kinowych|
| Odległość | Odległość w miejsce.|15 mil<br/>5 miles<br/>10 mil optymalizacji|
| MealType | Typ zawierają najważniejsze nowości, takich jak śniadanie lub obiad. |Śniadanie<br/>Obiad<br/>Obiad<br/>Supper|
| OpenStatus | Wskazuje, czy miejsce jest otwarty lub zamknięty.|Otwarty<br/>zamknięte<br/>Otwieranie|
| PlaceName | Nazwa miejsca.|Fabryka cheesecake|
| PlaceType | Typ miejsca.|Cafe<br/>Wojennych<br/>Biblioteka|
| PreferredRoute | Trasa preferowanych określone przez użytkownika. | 101 <br/>202 <br/>Trasy 401|
| Product (Produkt) | Produkt oferowanych przez to miejsce. | Odzieży<br/>Aparaty cyfrowe ASR<br/>Nowa ryb | 
| PublicTransportationRoute | Nazwa trasy transport publiczny, który wyszukuje użytkownika. | Północno-przedział pociągu<br/>Tras 3 X |
| Klasyfikacja | Ocena miejsce. | 5 gwiazdek<br/>3 gwiazdki<br/>4 gwiazdki|
| RouteAvoidanceCriteria | Kryteria dla uniknięcia określonej trasy, takich jak unikanie awarii, konstrukcji lub przejazd | Przejazd <br/>Konstrukcje<br/>Trasy 11|
| ServiceProvided | Jest to oferowane przez firmy lub miejsca, takie jak nowe zlecenie, śniegu plowing, krajobrazowe. | Pracownik<br/>mechanika<br/>plumber|
| TransportationCompany | Nazwa dostawcy transportu.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Typ transportu.|Magistrala<br/>Szkolenie<br/>Jazda samochodem|

## <a name="reminder"></a>Przypomnienie 
Domena monitu zapewnia intencje i jednostki dla tworzenia, edytowania i znajdowanie przypomnienia.

### <a name="examples"></a>Przykłady
```
Change my interview to 9 am tomorrow
Remind me to buy milk on my way back home
Can you check if I have a reminder about Christine's birthday?
```

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Change| Zmień przypomnienia.|Zmień Moje rozmowy 9 am jutro<br/>Przenieść mój monitu przypisania do jutra|
| Przycisk Utwórz| Utworzyć monit.|Tworzenie przypomnienia<br/>Przypomnij kupić mleka<br/>Chcę Pamiętaj, aby wywołać Rebecca, gdy jestem w domu|
| Usuwanie | Usuń przypomnienia.|Usuń Moje przypomnienie obrazu<br/>Usuń to przypomnienie|
| Znajdowanie | Znajdź przypomnienia.|Należy przypomnienia o Moje rocznicy?<br/>Można sprawdzić posiadający monitu o urodzinach Christine firmy?|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Tekst | Opis tekstowy przypomnienia.|Podnieś czyszczenia<br/>porzucanie mój samochód w Centrum usługi|

## <a name="restaurantreservation"></a>RestaurantReservation 
Domena RestaurantReservation zapewnia intencje i jednostki powiązane z zarządzaniem zastrzeżenia restauracji.

### <a name="examples"></a>Przykłady
```
Reserve at Zucca for two for tonight
Book a table at BJ's for tomorrow
Table for 3 in Palo Alto at 7
```

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Rezerwacja | Żądanie Rezerwacja restauracji. |Zarezerwuj na Zucca dla dwóch dla tonight<br/>Podręcznik tabeli w bieżącym<br/>Tabela 3 w Palo Alto na 7|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Adres| Lokalizacja zdarzeń lub adres zastrzeżenia.|Palo Alto<br/>300 112th Ave. SE<br/>Seattle|
| Obiektów | Atrybut opisujący udogodnień miejsca.|Widok Oceanu<br/>z systemem innym niż palenia|
| AppName | Nazwa aplikacji do tworzenia rezerwacji.|OtwórzTabelę<br/>Yelp<br/>TripAdvisor|
| Atmosfery | Opis atmosfery restauracji lub inne miejsce.|Romantyczna<br/>zwykłych<br/>nadaje się do grupy|
| Cuisine | Typ przynależność żywności, cuisine lub cuisine. |Chiński<br/>Włoski<br/>Meksykański|
| MealType | Typ zawierają najważniejsze nowości skojarzone z rezerwacji.|Śniadanie<br/>Obiad<br/>Obiad<br/>Supper|
| PlaceName | Nazwa lokalnej firmy, restauracji, przyciągania publicznych lub instytucji.|IHOP<br/>Fabryka cheesecake<br/>Louvre|
| PlaceType | Typ lokalnej firmy, restauracji, przyciągania publicznych lub instytucji.|restauracja<br/>Opera<br/>kinowych|
| Klasyfikacja | Ocena miejsca lub restauracji.|5 gwiazdek<br/>3 gwiazdki<br/>4 gwiazdki|

## <a name="taxi"></a>Taksówki 
 
Domena taksówki zapewnia intencje i jednostek do tworzenia i zarządzania taksówki rezerwacji.

### <a name="examples"></a>Przykłady
```
Get me a cab at 3 pm
How much longer do I have to wait for my taxi?
Cancel my Uber
```

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Podręcznik | Wywołanie taksówki. |Pobierz mnie plik cab<br/>Znajdź taksówki<br/>Skoroszyt mnie pełny x|
| Cancel | Anulowanie akcji dotyczących rezerwacji taksówki.|Anuluj mojej taksówki<br/>Anuluj mojej pełny|
| Śledź | Śledzenie trasy taksówki.|Jak długo ma oczekiwać na mój taksówki<br/>Gdzie jest Mój pełny?|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Adres| Adres skojarzony z rezerwacji taksówki. |Palo Alto<br/>300 112th Ave. SE<br/>Seattle|
| DestinationAddress| Lokalizacja docelowa lub adres. |Palo Alto<br/>300 112th Ave. SE<br/>Seattle|
| DestinationPlaceName | Nazwa lokalizacji docelowej, która jest firm, restauracji, przyciągania publicznych lub instytucji. |Wstrzymywanie środkowe<br/>Safeway<br/>Walmart|
| DestinationPlaceType | Typ lokalizacji docelowej, która jest firm, restauracji, przyciągania publicznych lub instytucji. |Restauracja<br/>Opera<br/>kinowych|
| PlaceName | Nazwa lokalnego biznesowych, restauracji, przyciągania publicznych lub instytucji. |Wstrzymywanie środkowe<br/>Safeway<br/>Walmart|
| PlaceType| Typ miejsca w żądaniu zarezerwować taksówki.|Restauracja<br/>Opera<br/>kinowych|
| TransportationCompany | Nazwa dostawcy transportu.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Typ transportu.|Magistrala<br/>Szkolenie<br/>Jazda samochodem|

## <a name="translate"></a>Tłumaczenie 
Domena Przetłumacz zapewnia intencje i obiektów związanych z tłumaczenie tekstu do języka docelowego.

### <a name="examples"></a>Przykłady
```
Translate to French
Translate hello to German
Translate this sentence to English
```

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Tłumaczenie| Tłumaczenie tekstu na inny język.|Tłumaczenia na język francuski<br/>Tłumaczenie hello na język niemiecki|


### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| TargetLanguage | Docelowy język tłumaczenia.|Francuski<br/>Niemiecki<br/>Koreański|
| Tekst | Tekst do tłumaczenia.|Witaj, świecie<br/>Dzień dobry<br/>Dobry wieczór|

## <a name="tv"></a>TV 
 
Domeny TV zapewnia intencje i jednostek kontrolowanie telewizorach.

### <a name="examples"></a>Przykłady
```
Switch channel to BBC
Show TV guide
Watch National Geographic
```

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| ChangeChannel| Zmień kanał telewizorach.|Zmień kanał CNN<br/>Kanał przełącznika BBC<br/>Przejdź do kanału 4|
| ShowGuide| Pokaż przewodnik TV.|Pokaż przewodnik TV<br/>Co to jest w kanale filmu teraz?<br/>Wyświetlanie listy programu|
| WatchTV| Poproś Obejrzyj kanału telewizji.|Chcę obejrzeć kanału Disneya<br/>Przejdź do Telewizora należy<br/>Obserwuj geograficznego National|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| ChannelName | Nazwa kanału telewizji.|CNN<br/>BBC<br/>Kanał film|

## <a name="utilities"></a>Usługi użyteczności publicznej  
Domena narzędzia zapewnia opcje dla zadania, które są typowe dla wielu zadań, takich jak pozdrowienia, anulowania, potwierdzenia, pomocy, powtarzania, nawigacji, uruchamianie i zatrzymywanie.

### <a name="examples"></a>Przykłady
```
Go back to Twitter
Please help
Repeat last question please
```

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Cancel | Anulowanie akcji.|Anuluj wiadomości<br/>Nie chcę już wysyłania wiadomości e-mail|
| Potwierdź | Potwierdź akcję.|tak Niestety potwierdzam<br/>Dobre am I potwierdzenie<br/>Zgoda am I potwierdzenie|
| FinishTask | Zakończ zadanie użytkownik rozpoczął.|Gotowe<br/>Zakończona<br/>Została ona wysłana|
| GoBack | Przejdź wstecz jeden krok lub wróć do poprzedniego kroku.|Wróć do usługi Twitter<br/>Przejść wstecz<br/>Wstecz|
| Pomoc | Żądanie pomocy.|Pomóż<br/>Otwórz okno Pomoc<br/>pomoc|
| Powtarzanie | Ponownie operację.|Powtórz ostatni pytanie należy<br/>Powtórz ostatni utworu|
| ShowNext | Wyświetlenie następnego elementu w serii. |Pokaż następny<br/>Przejdź do następnej strony|
| ShowPrevious | Pokaż poprzedni element w serii.|Pokaż poprzedniego|
| StartOver | Ponownie uruchom aplikację i rozpocznij nową sesję.|Zacznij od nowa<br/>Nowa sesja<br/>restart|
| Stop | Zatrzymaj akcji.| Zatrzymaj informacją tego<br/>Zamknij się<br/>Należy zatrzymać|

## <a name="weather"></a>Pogody 
Domeny pogody zapewnia intencje i jednostek uzyskiwanie pogody i prognozy.

### <a name="examples"></a>Przykłady
```
weather in London in september
What?s the 10 day forecast?
What's the average temperature in India in september?
```

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| GetCondition | Pobierz dane historyczne związanych z pogodą. |pogody w Londynie we wrześniu<br/>Co to jest średnia temperatura Indie we wrześniu?|
| GetForecast | Pobierz bieżący pogody i prognozy dalej kilka dni. |W jaki sposób pogody dzisiaj?<br/>Co to jest 10 dnia prognozy?<br/>Jak pogody będzie to weekendowe?|

### <a name="entities"></a>Jednostki
| Nazwa jednostki | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Lokalizacja| Lokalizacja bezwzględna żądania pogody.|Seattle<br/>Paryż<br/>Palo Alto|

## <a name="web"></a>Sieć Web 
Domeny w sieci Web udostępnia opcje do nawigowania do witryny sieci Web.

### <a name="examples"></a>Przykłady
```
Navigate to facebook.com
Go to www.twitter.com
Navigate to www.bing.com
```

### <a name="intents"></a>Intencje
| Nazwa metody konwersji | Opis | Przykłady |
| ---------------- |-----------------------|----|
| Nawigacja | Żądanie, aby przejść do określonej witryny sieci Web. |Przejdź do facebook.com<br/>Przejdź do www.twitter.com|

