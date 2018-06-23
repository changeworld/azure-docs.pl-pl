---
title: Odwołanie Cortana wbudowane aplikacji | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca Cortana Pomocnik wbudowane aplikacji z języka opis inteligentnego usług (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: cd808c30a6781fc0252992c13ba247486e35ad44
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347233"
---
# <a name="cortana-prebuilt-app-reference"></a>Odwołanie Cortana wbudowane aplikacji
To odwołanie wymieniono intencje i jednostkami, którymi Cortana wbudowane rozpoznawane.

## <a name="cortana-prebuilt-intents"></a>Cortana intencje wbudowane
Wbudowane Pomocnik aplikacji można określić następujące opcje:

| Celem | Przykład zniesławiających |
|--------| ------------------|
| BUILTIN.Intent.alarm.alarm_other|Aktualizuj moje 7:30 alarmowych jako ten użytkownik ośmiu <br/>Zmień moje alarm od 8 do 9 am |
| BUILTIN.Intent.alarm.delete_alarm|Usuń alarmu <br/>Usuń Moje alarm "wznawiania"|
| BUILTIN.Intent.alarm.find_alarm|czas, jaki Moje alarm wznawiania ustawiono dla? <br/> Moje alarm wznawiania znajduje się na? |
| BUILTIN.Intent.alarm.set_alarm|Włącz Moje alarm wznawiania<br/>alarm można ustawić dla 12 o nazwie mają antybiotyków?|
| BUILTIN.Intent.alarm.snooze|Odłóż alarm 5 minut<br/>Odłóż alarmu|
| BUILTIN.Intent.alarm.time_remaining| jak długo mają do "wznawiania" <br/> czas, jaki do mojego alarm dalej?|
| BUILTIN.Intent.alarm.turn_off_alarm | Wyłącz Moje alarm 7: 00 <br/> Wyłącz Moje alarm wznawiania |
| BUILTIN.Intent.Calendar.change_calendar_entry| Zmienianie terminu<br/>Przenieś spotkania od dzisiaj do jutra|
|BUILTIN.Intent.Calendar.check_availability|Timowi jest zajęty w piątek?<br/>jest bezpłatne w sobotę Brianowi|
|BUILTIN.Intent.Calendar.connect_to_meeting|Połącz na spotkanie<br/>Dołącz do spotkania w trybie online|
|BUILTIN.Intent.Calendar.create_calendar_entry|Należy zaplanować spotkanie z tony dla piątek<br/>Wprowadź termin z Ewa w niedzielę godzinie 2|
|BUILTIN.Intent.Calendar.delete_calendar_entry|Usuń Moje termin<br/>Usuwanie spotkania godzinie 3 jutro kalendarza|
|BUILTIN.Intent.Calendar.find_calendar_entry|Pokaż mój kalendarz<br/>Wyświetl moje co tydzień kalendarza|
|BUILTIN.Intent.Calendar.find_calendar_when|gdy mój dalej jest spotkanie z Jan?<br/>czas, jaki jest Mój terminu z larry w poniedziałek?|
|BUILTIN.Intent.Calendar.find_calendar_where|Pokaż lokalizację spotkania 6 użytkownik<br/>w przypadku tej sesji z Jan?|
|BUILTIN.Intent.Calendar.find_calendar_who|kto jest to spotkanie z?<br/>kto jeszcze jest zaproszenie?|
|BUILTIN.Intent.Calendar.find_calendar_why|jakie są szczegóły spotkania 11 ten użytkownik?<br/>Co to jest spotkaniu z Jan o?|
|BUILTIN.Intent.Calendar.find_duration|jak długo trwa spotkania dalej<br/>ile minut długo jest spotkania tego popołudnie|
|BUILTIN.Intent.Calendar.time_remaining|czas, jaki do mojego następnego terminu?<br/>ile więcej czasu są dostępne przed spotkania?|
|BUILTIN.Intent.Communication.add_contact|Zapisz ten numer i umieszcza nazwę jako jose<br/>Umieść jason w listy kontaktów|
|BUILTIN.Intent.Communication.answer_phone|odpowiedzi<br/>odpowiedzi na wywołanie|
|BUILTIN.Intent.Communication.assign_nickname|Edytuj pseudonimu dla nickolas<br/>Dodawanie pseudonimu dla Jan Kowalski|
|BUILTIN.Intent.Communication.call_voice_mail|Poczta głosowa<br/>Wywołanie pocztę głosową|
|BUILTIN.Intent.Communication.find_contact|Pokaż kontaktów<br/>Znajdź kontaktów|
|BUILTIN.Intent.Communication.forwarding_off|przerwanie przekazywania Moje rozmowy<br/>Wyłącz przekazywania połączeń|
|BUILTIN.Intent.Communication.forwarding_on|Ustaw wywołanie funkcji przekazywania danych do wysyłania wywołań Telefon domowy<br/>Telefon domowy wywołania do przodu|
|BUILTIN.Intent.Communication.ignore_incoming|nie udzielono odpowiedzi tego wywołania<br/>nie teraz jestem zajęty|
|BUILTIN.Intent.Communication.ignore_with_message|Nie można znaleźć odpowiedzi tego wywołania, ale zamiast tego Wyślij wiadomość<br/>Ignoruj i Wyślij ponownie tekstu|
|BUILTIN.Intent.Communication.make_call|Wywołaj Roberta i Jan<br/>wywołania programu Microsoft Operations Manager i Tato|
|BUILTIN.Intent.Communication.press_key|wybierania gwiazdy<br/>Naciśnij klawisz 1 2 3|
|BUILTIN.Intent.Communication.read_aloud|Odczytaj tekst<br/>co ona powiedzieć w komunikacie|
|BUILTIN.Intent.Communication.redial|Wybierz ponownie moje ostatnim wywołaniu<br/>Wybierz ponownie|
|BUILTIN.Intent.Communication.send_email|wiadomości e-mail, aby Mike'owi wody Jan czy świetnie się obiad w ostatnim tygodniu<br/>Wyślij wiadomość e-mail do bob|
|BUILTIN.Intent.Communication.send_text|Wysyłanie tekstu do Boba oraz Jan<br/>message|
|BUILTIN.Intent.Communication.speakerphone_off|Chcę wyłączyć prelegenta<br/>Wyłącz głośnomówiącego|
|BUILTIN.Intent.Communication.speakerphone_on|Tryb głośnomówiącego<br/>Umieść głośnomówiącego|
|BUILTIN.Intent.MYSTUFF.find_attachment|Find Jan dokumentu pocztą e-mail do mnie wczoraj <br/>Znajdź dokumentów z Jan|
|BUILTIN.Intent.MYSTUFF.find_my_stuff|Chcę edytować listy zakupów od wczoraj<br/>Znajdź notatek chemia od września
|BUILTIN.Intent.MYSTUFF.search_messages|Otwórz wiadomość <br/>z chmury do urządzenia|
|BUILTIN.Intent.MYSTUFF.transform_my_stuff|Udostępnij listy zakupów Moje mąż<br/>Usuń z listy zakupów|
|BUILTIN.Intent.ondevice.open_setting|Otwórz ustawienia cortana <br/>Przejście do powiadomienia|
|BUILTIN.Intent.ondevice.pause|Wyłącz utworów muzycznych<br/>Muzyka wyłączone|
|BUILTIN.Intent.ondevice.play_music|Chcę, aby usłyszeć właściciela samotnych pulsu<br/>odtwarzanie niektórych muzyki gospel|
|BUILTIN.Intent.ondevice.recognize_song|Powiedz mi, jest to utworu<br/>analizowanie i pobrać tytuł utworu|
|BUILTIN.Intent.ondevice.REPEAT|Powtórz tę ścieżkę<br/>odtwarzanie utworu tego ponownie|
|BUILTIN.Intent.ondevice.resume|Uruchom ponownie utworów muzycznych<br/>Uruchom ponownie utworów muzycznych|
|BUILTIN.Intent.ondevice.skip_back|Wykonaj kopię zapasową ścieżki<br/>poprzedniego|
|BUILTIN.Intent.ondevice.skip_forward|następnego utworu<br/>przejść od razu ścieżki|
|BUILTIN.Intent.ondevice.turn_off_setting|Wi-Fi poza<br/>Wyłącz tryb samolotowy|
|BUILTIN.Intent.ondevice.turn_on_setting|Wi-Fi na<br/>Włącz funkcję bluetooth|
|BUILTIN.Intent.Places.add_favorite_place|Dodaj ten adres do moich ulubionych<br/>Ta lokalizacja zapisywania do moich ulubionych|
|BUILTIN.Intent.Places.book_public_transportation|Kup biletu pociągu<br/>Podręcznik przebiegu tramwajem|
|BUILTIN.Intent.Places.book_taxi|można znaleźć mnie jazdy tej godzinie?<br/>Znajdź taksówki|
|BUILTIN.Intent.Places.check_area_traffic|Co to jest ruch podobnie jak na 520<br/>w jaki sposób ruch na i-5|
|BUILTIN.Intent.Places.check_into_place|Sprawdź w joya<br/>Sprawdź w tym miejscu|
|BUILTIN.Intent.Places.check_route_traffic|Pokaż ruch na sposób kirkland<br/>w jaki sposób ruch do seattle?|
|BUILTIN.Intent.Places.find_place|miejsca zamieszkania <br/>Udostępnij restauracji japońskiego 3 pierwszych|
|BUILTIN.Intent.Places.get_address|Pokaż adres guu na robson ulicy<br/>Co to jest adres najbliższej starbucks?|
|BUILTIN.Intent.Places.get_coupon|Znajdź dotyczy odbiorniku w obszarze Moje<br/>zniżki w widoku górski|
|BUILTIN.Intent.Places.get_distance|odległość jest inn świątecznych?<br/>Co to jest odległość tahoe|
|BUILTIN.Intent.Places.get_hours|Co to są paska del corso godziny od poniedziałku?<br/>Po otwarciu biblioteki?|
|BUILTIN.Intent.Places.get_menu|Pokaż applebee w menu<br/>Co to jest menu w sizzler|
|BUILTIN.Intent.Places.get_phone_number|Wprowadź numer dla magazynu macierzystego<br/>Co to jest numer telefonu, z dokładnością starbucks?|
|BUILTIN.Intent.Places.get_price_range|ile kosztuje obiad kosztem homara czerwony<br/>Koszt jest purpurowy kawiarni|
|BUILTIN.Intent.Places.get_reviews|Pokaż przeglądami dla lokalnych magazynów sprzętu<br/>Aby wyświetlić sprawdza restauracji|
|BUILTIN.Intent.Places.get_route|Udostępnij instrukcje|Umożliwia uzyskanie dostępu do pomieszczenie pizza przeprowadzone|
|BUILTIN.Intent.Places.get_star_rating|ile gwiazdek ma francuskim pralnicze?<br/>jest dobry akwarium w monterrey?|
|BUILTIN.Intent.Places.get_transportation_schedule|czas, jaki sieć san francisco prom pozostaw?<br/>gdy jest najnowsze pociągu seattle?|
|BUILTIN.Intent.Places.get_travel_time|Co to jest czas Zakopanego z rz<br/>jak długo trwa na uzyskanie dostępu do sieci san francisco w tym miejscu|
|BUILTIN.Intent.Places.make_call|Wywołanie Kowalski Dr w bellevue<br/>Wywołanie macierzystego magazynu na ulicy 1|
|BUILTIN.Intent.Places.rate_place|Nadaj ocenę w tym restauracji<br/>szybkość il fornaio 5 gwiazdek w yelp|
|BUILTIN.Intent.Places.show_map|Co to jest mojej bieżącej lokalizacji <br/>Co to jest mojej lokalizacji|
|BUILTIN.Intent.Places.takes_reservations|czy jest możliwe zastrzeżenie w oliwek ogrodu<br/>Galeria są akceptowane zastrzeżenia|
|BUILTIN.Intent.Reminder.change_reminder|Zmień przypomnienia<br/>Przenieś w górę Moje monitu dzień obraz 30 minut|
|BUILTIN.Intent.Reminder.create_single_reminder|Przypomnij wznawiania w 7: 00<br/>Przypomnij Przejdź lewy lub traktowanie z luca godzinie 4:40|
|BUILTIN.Intent.Reminder.delete_reminder|Usuń to przypomnienie<br/>Usuń Moje przypomnienie obrazu|
|BUILTIN.Intent.Reminder.find_reminder|można mieć żadnych monitów ustawione dla dzisiaj<br/>czy mają przypomnienia ustawione dla firm na luca|
|BUILTIN.Intent.Reminder.read_aloud|odczytywany monitu<br/>Przeczytaj ten monit|
|BUILTIN.Intent.Reminder.snooze|Odłóż tego monitu do jutra<br/>Odłóż to przypomnienie|
|BUILTIN.Intent.Reminder.turn_off_reminder|Wyłącz monitu<br/>Odrzuć monitu wybierania lotniska|
|BUILTIN.Intent.weather.change_temperature_unit|Zmień z f kelvin —<br/>Zmień z f c|
|BUILTIN.Intent.weather.check_weather|Pokaż prognozy dla moich nadchodzących podróży do seattle<br/>jak pogody będzie to weekendowe|
|BUILTIN.Intent.weather.check_weather_facts|Co to jest pogody podobnie jak w Hawaje w grudniu?<br/>jaki był temperatury ten czas ostatniego roku?|
|BUILTIN.Intent.weather.compare_weather|Udostępnij porównanie temperatury wysokiej i złych wyników dallas i houston, tx<br/>jak pogody porównania slc i nyc|
|BUILTIN.Intent.weather.get_frequent_locations|Udostępnij Moje najczęstsze lokalizacji<br/>Pokaż najczęściej zatrzymuje|
|BUILTIN.Intent.weather.get_weather_advisory|ostrzeżenia pogody<br/>Pokaż pogody advisory dla sacramento|
|BUILTIN.Intent.weather.get_weather_maps|Wyświetl mapa pogody<br/>Pokaż mapy pogody na Afryka|
|BUILTIN.Intent.weather.question_weather|czy będą mglisty rano jutro?<br/>będzie konieczne shovel śniegu tego weekendowe?|
|BUILTIN.Intent.weather.show_weather_progression|Pokaż radarowego pogody lokalnego<br/>Rozpocznij radarowy|
|BUILTIN.Intent.None|Ile masz lat<br/>Otwórz aparatu|

## <a name="prebuilt-entities"></a>Wbudowane jednostek 

Oto kilka przykładów jednostek, który można zidentyfikować aplikacji wbudowane Pomocnik:

|Jednostka |Przykład jednostki w utterance |
|-------|------------------|
|BUILTIN.alarm.alarm_state | Włącz `off` Moje alarm wznawiania    <br/> jest Mój alarm wznawiania `on`  | 
|BUILTIN.alarm.DURATION |Odłóż dla `10 minutes`    <br/> Odłóż alarmu `5 minutes`  |
|BUILTIN.alarm.start_date | Ustawianie alarmu `monday` na 7: 00   <br/> Ustawianie alarmu `tomorrow` w południe   |
|BUILTIN.alarm.start_time | Utwórz alarmu `30 minutes`    <br/> alarmu nastąpi `in 20 minutes`   |
|BUILTIN.alarm.Title | jest mój `wake up` alarm włączony <br/> można ustawić alarm kwartału do 12 od poniedziałku do piątku o nazwie `take antibiotics` |
|BUILTIN.Calendar.absolute_location | Utwórz termin w bieżącym w `123 main street`   <br/> spotkanie będą miały miejsce w `cincinnati` na 5 z czerwca    |
|BUILTIN.Calendar.contact_name|Umieść marketing spotkania w kalendarzu i upewnij się, że `joe` istnieje <br/>Chcesz skonfigurować obiad na il fornaio i zapraszanie `paul` |   
|BUILTIN.Calendar.destination_calendar|Dodaj do mojego `work` harmonogramu   <br/>Umieść to Mój `personal` kalendarza |
|BUILTIN.Calendar.DURATION| Ustawianie terminu dla `an hour` zmieniły 6 <br/>  Podręcznik `2 hour` spotkanie z Jan |  
|BUILTIN.Calendar.end_date | Utwórz kalendarz pozycja urlopu w przyszłości do `next monday` <br/>    Blokuj Mój czas jako zajęty do `monday, october 5th` | 
|BUILTIN.Calendar.end_time | kończy się spotkania w `5:30 PM` <br/> Zaplanowane od 11 do `noon`  |   
|BUILTIN.Calendar.implicit_location | Anuluj termin w dmv <br/> Zmień lokalizację urodzin mil `poppy restaurant` |    
|BUILTIN.Calendar.move_earlier_time | Wypychanie spotkania do przodu `an hour` <br/> Przenieś w górę dentist terminu `30 minutes`       |
|BUILTIN.Calendar.move_later_time | przenieść mój dentist terminu `30 minutes`    <br/> Przekładanie spotkania `an hour` |  
|BUILTIN.Calendar.original_start_date | Ponowne planowanie Moje osobistego na barber z "Wtorek" do środy <br/> Przenieś spotkania za pomocą ken z `monday` do wtorek |
|BUILTIN.Calendar.original_start_time | Ponowne planowanie spotkania z `2:00` 3  <br/> Zmień moje termin dentist z `3:30` 4 |  
|BUILTIN.Calendar.start_date | na czas, jaki jest uruchamiany mojej firmy `flag day` <br/> Zaplanuj obiad `friday after next` w południe 
|BUILTIN.Calendar.start_time | Warto zaplanować je `this morning` <br/> Warto zaplanować ją w `morning` |  
|BUILTIN.Calendar.Title | `vet appointment`  <br/> `dentist` Wtorek |
|BUILTIN.Communication.audio_device_type | Upewnij się, za pomocą wywołania `bluetooth`  <br/> Wywołanie przy użyciu mojej `headset` | 
|BUILTIN.Communication.contact_name | tekst `bob jones`  <br/> | wywołania `sarah`|
|BUILTIN.Communication.destination_platform | Wywołanie dave w `london` <br/> Wywołaj jego `work line` |    
|BUILTIN.Communication.from_relationship_name | Pokaż wywołania z mojej `daughter` <br/> Przeczytaj wiadomość e-mail z `mom`   |   
|BUILTIN.Communication.key | telefoniczny `star` <br/>  Naciśnij klawisz `hash` klucza    |
|BUILTIN.Communication.Message | carly znaczy poczty e-mail `i'm running late` <br/> tekst angus smith `good luck on your exam` |    
|BUILTIN.Communication.message_category | oznaczone do nowych wiadomości e-mail `follow up`  <br/> oznaczone jako nowy adres e-mail `high priority` |    
|BUILTIN.Communication.message_type | Wyślij `email`   <br/> Przeczytaj mojej `text` wiadomości na głos |
|BUILTIN.Communication.phone_number | Chcę wybrać `1-800-328-9459` <br/>     wywołania `555-555-5555` |   
|BUILTIN.Communication.relationship_name | tekst Moje `husband` <br/>  Adres e-mail `family`| 
|BUILTIN.Communication.slot_attribute | Zmień `recipient` <br/>    Zmień `text` | 
|BUILTIN.comminication.source_platform | Wywołaj mu `skype` <br/> Wywołaj mu Moje `personal line` |
|BUILTIN.MYSTUFF.Attachment| z dokumentami `attached` <br/> Znajdź wiadomości e-mail `attachment` bob wysyłane |
|BUILTIN.MYSTUFF.contact_name| Znajdź Ewa wysyłana do arkusza kalkulacyjnego `me` <br/> dokument i wysyłania do `susan` ostatniego nocy |
|BUILTIN.MYSTUFF.data_source | `c:\dev\` <br/> Moje `desktop` <br/> |`"resolution": { "resolution_type": "metadataItems", "metadataType": CanonicalEntity", "value": "desktop" }`|
|BUILTIN.MYSTUFF.data_type | Zlokalizuj `document` i pracy w nocy ostatniego <br/> |`"resolution": {"resolution_type": "metadataItems", "metadataType": "CanonicalEntity", "value":Document" }` <br/> Wywołaj jego Jan `visio diagram`  |
|BUILTIN.MYSTUFF.end_date| Pokaż dokumenty, które i Praca nad `between yesterday and today`   <br/> Znajdź, jakie dokumentu i pracy na `before thursday the 31st` |
|BUILTIN.MYSTUFF.end_time| wyszukiwanie plików i zapisać `before noon` <br/> Znajdź, jakie dokumentu i pracy na `before 4pm`|      
|BUILTIN.MYSTUFF.file_action| Otwórz arkusz kalkulacyjny i `saved` wczoraj <br/> Znajdź arkusz kalkulacyjny Kevina `created`| 
|BUILTIN.MYSTUFF.from_contact_name | Znajdź wniosku `jason` wysyłane do mnie <br/> Otwórz `isaac` w ostatniej wiadomości e-mail |
|BUILTIN.MYSTUFF.Keyword | Pokaż `french conjugation` plików <br/> Znajdź `marketing plan` i przygotowuje wczoraj |
|BUILTIN.MYSTUFF.Location | dokument i edytować `work` <br/> zdjęć i miał w `paris` |
|BUILTIN.MYSTUFF.message_category | Wyszukaj Mój `new` wiadomości e-mail <br/> Wyszukaj Mój `high priority` poczty e-mail |
|BUILTIN.MYSTUFF.message_type | Sprawdź mój `email` <br/>  Pokaż Moje `text` wiadomości  |
|BUILTIN.MYSTUFF.source_platform | Wyszukiwanie Mój `hotmail` wiadomość e-mail dotycząca wiadomości e-mail z Jan    <br/> Znaleziono dokument i wysłany z `work` |
|BUILTIN.MYSTUFF.start_date | Znajdowanie notatek z `january` <br/> Znajdź wysyłania wiadomości e-mail i rob `after january 1st` |
|BUILTIN.MYSTUFF.start_time | Znajdź tej wiadomości e-mail wysyłane i rob chwilę przed 14: 00, ale `after noon`? <br/> Znajdź arkusz, który kristin wysłanych do użytkownika, który można edytować `last night` | 
|BUILTIN.MYSTUFF.Title | `c:\dev\mystuff.txt` <br/> `*.txt` |
|BUILTIN.MYSTUFF.transform_action | `download` Jan plików wysłane do mnie <br/> `open` dokumencie wytyczne adnotacji |
|BUILTIN.note.note_text | Tworzenie listy spożywczych włącznie `pork chops, applesauce and milk` <br/> Zwróć uwagę na `buy milk` |
|BUILTIN.note.Title | Zwróć uwagę, o nazwie `grocery list` <br/> Zwróć uwagę, o nazwie `people to call` |
|BUILTIN.ondevice.music_artist_name | Odtwórz wszystkie przez `rufus wainwright` <br/> Odtwórz `garth brooks` utworów muzycznych |   
|BUILTIN.ondevice.music_genre | Pokaż `classic rock` utworów muzycznych <br/> Odtwórz mój `classical` utworów muzycznych z Barok okresu. |
|BUILTIN.ondevice.music_playlist | losowo wszystkich spears britney z `workout` listy odtwarzania <br/> Odtwórz `breakup` listy odtwarzania
|BUILTIN.ondevice.music_song_name | Play `summertime` <br/> Play `me and bobby mcgee` | 
|BUILTIN.ondevice.setting_type | `quiet hours` <br/> `airplane mode` |
|BUILTIN.Places.absolute_location | Przejdź do punktu przecięcia `5th and pike` <br/> Nie, ma instrukcje `1 microsoft way redmond wa 98052` |
|BUILTIN.Places.atmosphere | Wyszukaj `interesting` miejsca do realizowanych    <br/> gdzie można znaleźć `casual` restauracji |  
|BUILTIN.Places.audio_device_type |Wywołaj pocztowym na `hands free` <br/> Jan papa z wywołań `speakerphone` |    
|BUILTIN.Places.avoid_route | należy unikać `toll road` <br/> nawiązać unikanie Poznań `construction on 101` |  
|BUILTIN.Places.cuisine | `halal` Ogranicznik u góry widoku   <br/> `kosher` drobne jadalni na Półwyspie |
|BUILTIN.Places.Date | rezerwować `next friday the 12th` <br/>  jest otwarty na mashiko `mondays` ? |
|BUILTIN.Places.discount_type | Znajdź `coupon` dla tego macy <br/> Znajdź mnie `coupon` |
|BUILTIN.Places.Distance | jest dobrym diner `within 5 miles` z tutaj <br/> Znajdź w nich `within 15 miles` |   
|BUILTIN.Places.from_absolute_location | kierunkach `45 elm street` do strony głównej <br/> Pobierz mnie kierunkach `san francisco` do palo alto  |
|BUILTIN.Places.from_place_name | prowadzenie z `post office` — 56 center ulica <br/> Pobierz mnie kierunkach `home depot` do lowes |
|BUILTIN.Places.from_place_type | instrukcje downtown z `work` <br/>  Pobierz mnie kierunkach `drug store` do strony głównej |
|BUILTIN.Places.meal_type | pobliżu miejsc dla `dinner` <br/> Znajdź dobrym miejscem dla firm `lunch` | 
|BUILTIN.Places.nearby | Pokaż niektóre sklepy chłodnych `near` mnie  <br/> czy istnieją wszystkie dobrej restauracji libański `around` w tym miejscu? |
|BUILTIN.Places.open_status | gdy jest małe `closed` <br/> Pobierz mnie `opening` godziny magazynu | 
|BUILTIN.Places.place_name | Przejdź do `central park` <br/> Wyszukiwanie `eiffel tower` |   
|BUILTIN.Places.place_type | `atms` <br/> `post office` |
|BUILTIN.Places.prefer_route | Pokaż kierunki przez `shortest` trasy <br/> podejmij `fastest` trasy | 
|BUILTIN.Places.price_range| Udostępnij miejsca, które są `moderately affordable` <br/>  Chcę `expensive` jeden   |
|BUILTIN.Places.Product | gdzie można uzyskać `fresh fish` wokół tutaj  <br/> w przypadku gdy wokół tutaj sprzedaży `bare minerals` |
|BUILTIN.Places.public_transportation_route | Harmonogram magistrali `m2` magistrali <br/> tras `3x` |  
|BUILTIN.Places.rating | Pokaż `3 star` restauracji <br/> Pokaż wyniki, które są `3 stars or higher`  |
|BUILTIN.Places.reservation_number | skoroszyt tabelę dla `seven` osoby <br/>  rezerwować `two` na il fornaio |
|BUILTIN.Places.results_number | Pokaż `10` kawiarniach najbliżej tutaj <br/> Pokaż górny `3` akwariach  
|BUILTIN.Places.service_provided | gdzie można skontaktować się `whale watch` przez magistralę? <br/> Potrzebuję mechanika do `fix my brakes` |    
|BUILTIN.Places.Time | Chcę miejsca, które są otwarte w sobotę o godzinie `8 am`| mashiko jest otwarty `now` |
|BUILTIN.Places.transportation_company | harmonogramy pociągu `new jersey transit` <br/> można uzyskać istnieje `bart` | 
|BUILTIN.Places.transportation_type | gdzie jest magazynem muzyka i może uzyskać dostęp do `on foot`? <br/>  Udostępnij `biking` instrukcje mashiko|
|BUILTIN.Places.travel_time | Chcę dysków `less than 15 minutes` <br/>   Chcę innym, który można uzyskać w celu `under 15 minutes` |   
|BUILTIN.Reminder.absolute_location | Przypomnij wywołania Moje Tato, gdy i przejście do `chicago` <br/> gdy I wrócić do `seattle` Przypomnij mi nawiązać gazu |
|BUILTIN.Reminder.contact_name | gdy `bob` wywołań, Przypomnij mi potwierdzić joke <br/> Tworzenie przypomnienia wspomnieć o magistrali służbowe I mówiąc do `arthur` |
|BUILTIN.Reminder.leaving_absolute_location | Przypomnienie odebrania craig podczas opuszczania `1200 main` |
|BUILTIN.Reminder.leaving_implicit_location | Przypomnij mi nawiązać gazu po opuszczeniu `work`  |
|BUILTIN.Reminder.original_start_date | zmiana przypomnienia o trawnika z `saturday` do niedziela <br/> przenieść mój monitu o szkole z `monday` do wtorek   |
|BUILTIN.Reminder.relationship_name | gdy mój `husband` wywołań, Przypomnij poproś go o spotkaniu pta <br/> Przypomnij ponownie po `mom` wywołania|
|BUILTIN.Reminder.reminder_text | można możesz Przypomnij do `bring up my small spot of patchy skin` podczas dr smith'calls  <br/> Przypomnij mi o `pick up dry cleaning` na 4:40   |
|BUILTIN.Reminder.start_date | Przypomnij mi `thursday after next` godzinie 8  <br/> Przypomnij mi `next thursday the 18th` godzinie 8    |
|BUILTIN.Reminder.start_time | Tworzenie przypomnienia `in 30 minutes` <br/> Tworzenie przypomnienia o wody zakładów `this evening at 7` |  
|BUILTIN.weather.absolute_location | będzie ona fartuchów w `boston` <br/> Co to jest prognozy dla `seattle`?  |
|BUILTIN.weather.date_range | pogody w nyc `this weekend` <br/>   Wyszukiwanie `five day` prognozy hollywood florida |
|BUILTIN.weather.suitable_for | można przejść `hiking` w szorty tego weekendowe?   <br/> będzie ona nieuprzywilejowany do `walk` do gry dzisiaj? | 
|BUILTIN.weather.temperature_unit | Co to jest temperatura dzisiaj `kelvin`   <br/> Pokaż temps w `celsius` |  
|BUILTIN.weather.time_range | jest prawdopodobnie będzie ona śniegu `tonight`? <br/> jest to prawo wieje `now`?  |
|BUILTIN.weather.weather_condition | Pokaż `precipitation` <br/> jak grubości jest `snow` w tahoe lake teraz?  |

