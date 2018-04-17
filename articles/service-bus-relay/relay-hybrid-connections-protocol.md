---
title: Azure połączeń hybrydowych przekazywania protokołu przewodnik | Dokumentacja firmy Microsoft
description: Przewodnik protokołu Azure przekazywania połączeń hybrydowych było możliwe.
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: ''
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 1979746d143dbf8c3f4bca3f9a3a7925fe8e3f0d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure protokołu przekazywania połączeń hybrydowych
Przekazywania Azure to jedna z kolumn klucza możliwości platformy Azure Service Bus. Nowy *połączeń hybrydowych* możliwość przekazywania jest bezpieczne, protokołu open zmiany, na podstawie HTTP i protokołu WebSockets. Zastępuje ona poprzedniego o identycznej nazwie *usługi BizTalk Services* funkcji, który został utworzony na podstawie zastrzeżonym protokołem. Integrację usługi aplikacji Azure połączeń hybrydowych było możliwe będzie działać jako — jest.

Połączeń hybrydowych umożliwia strumienia dwukierunkowe, binarnych komunikacji między dwiema aplikacjami sieciowym, w których jedną lub obie strony może znajdować się za NAT lub zaporą. W tym artykule opisano interakcje przekazywania połączeń hybrydowych nawiązywania połączeń klientów w odbiornik i ról nadawcy i jak odbiorników akceptować nowych połączeń po stronie klienta.

## <a name="interaction-model"></a>Model interakcji
Przekazywania połączeń hybrydowych łączy dwie strony zapewniając punkt spotkania w chmurze Azure, który zarówno strony mogą odnaleźć i nawiązać połączenie z punktu widzenia własnych sieci. Tego punktu spotkania nazywa się "Połączenia hybrydowego" w tym i innych dokumentacji interfejsy API, a także w portalu Azure. Punkt końcowy usługi połączeń hybrydowych było możliwe jest określana jako "Usługa" dla pozostałej części tego artykułu. Model interakcji leans na nomenklaturę wyznaczane przez wiele innych sieci interfejsów API.

Brak odbiornika, najpierw wskazuje gotowości do obsługi połączeń przychodzących, a następnie akceptuje, zgodnie z ich odbierania. Z drugiej strony Brak połączenia klienta, który udostępnia połączenie do odbiornika, oczekiwano takiego połączenia akceptację ustalania ścieżki komunikacja dwukierunkowa.
"Połącz", "Nasłuchiwania" i "Zaakceptuj" są tego samego postanowienia, które możesz znaleźć w gnieździe większość interfejsów API.

Każdy model komunikacji obsługiwanych przez przekaźnik ma obie strony wykonywania połączeń wychodzących na punkt końcowy usługi, dzięki czemu "odbiornika" również "client" potocznej używana i może również spowodować inne przeciążenia terminologii. Terminologia dokładne, w związku z tym używanych dla połączeń hybrydowych było możliwe jest następujący:

Programy po obu stronach połączenia są nazywane "klientów", ponieważ są one klientów do usługi. Czeka, który akceptuje połączenia klienta jest "odbiornika" lub jest określany jako "roli odbiornika." Klienta, który inicjuje nowe połączenie do odbiornika za pomocą usługi jest nazywany "sender" lub "rolą nadawcy."

### <a name="listener-interactions"></a>Odbiornik interakcji
Odbiornik ma cztery interakcji z usługą; w dalszej części tego artykułu zamieszczone w tej sekcji opisano wszystkie szczegóły danych przesyłanych w sieci.

#### <a name="listen"></a>Nasłuchuj
Aby wskazać gotowość do usługi, która jest odbiornik gotowy do akceptowania połączeń, tworzy wychodzące połączenia obiektu WebSocket. Uzgadnianie połączenia przyjmuje nazwę połączenie hybrydowe skonfigurowany na przestrzeń nazw przekazywania i token zabezpieczający, który przyznaje "Nasłuchiwania" zgodny z nazwą.
Gdy protokół WebSocket jest akceptowane przez usługę, rejestracja została zakończona i ustanowić protokołu WebSocket jest utrzymywane jako "kanał kontrolny" włączania wszystkich kolejnych interakcji. Usługa umożliwia połączenie hybrydowe maksymalnie 25 równoczesnych odbiorników. W przypadku co najmniej dwa odbiorniki aktywnego połączenia przychodzące są równoważone między je w kolejności losowej. odpowiedni dystrybucji nie jest gwarantowana.

#### <a name="accept"></a>Zaakceptuj
Po otwarciu nowego połączenia w usłudze nadawcy usługa wybierze i powiadamia jednej aktywnej odbiorników na połączenia hybrydowego. To powiadomienie jest wysyłane do odbiornika za pośrednictwem kanału open kontroli jako wiadomość JSON zawierający adres URL punktu końcowego protokołu WebSocket, który odbiornika musi łączyć się w celu akceptowania połączenia.

Adres URL może i mogą być używane bezpośrednio przez odbiornik bez konieczności wykonywania dodatkowych działań.
Dane zakodowane jest prawidłowa tylko krótki okres czasu, zasadniczo tak długo, jak nadawca jest gotowa oczekiwania dla połączenia nawiązane end-to-end można, ale maksymalnie 30 sekund. Adres URL można używać tylko dla jednego pomyślnego połączenia. Natychmiast po nawiązaniu połączenia obiektu WebSocket z adresem URL spotkania wszystkich dalszych działań w tym protokołu WebSocket jest przekazywany z i do nadawcy, bez konieczności interwencji lub interpretacji przez usługę.

#### <a name="renew"></a>Renew
Token zabezpieczający, który musi być używany do rejestrowania odbiornika i obsługa kanał kontrolny może wygaśnie, gdy odbiornika jest aktywny. Wygaśnięcia tokenu nie ma wpływu na bieżące połączenia, ale powodować kanału kontroli ma być przerwane przez usługę w lub wkrótce po chwili wygaśnięcia. Operacja "odnowić" jest komunikat JSON, który odbiornika można wysyłać do Zamień token skojarzony z kanału kontroli, tak aby kanał kontrolny mogą być obsługiwane przez dłuższy czas.

#### <a name="ping"></a>Ping
Jeśli kanał kontrolny pozostanie bezczynny przez długi czas, pośredników w taki sposób, takie jak moduły równoważenia obciążenia lub translatory adresów sieciowych, mogą porzucić połączenie TCP. Operacja "ping" pozwala uniknąć który wysyłając niewielką ilość danych w kanale, który przypomina o tym, wszyscy członkowie trasę sieciową, która ma być aktywne połączenie i służy również jako "na żywo" testu dla odbiornika. Jeśli polecenie ping nie powiedzie się, kanał kontrolny należy traktować jako bezużyteczne i odbiornika powinni połączyć się ponownie.

### <a name="sender-interaction"></a>Nadawca interakcji
Nadawca ma tylko jeden interakcji z usługą: nawiązuje połączenie.

#### <a name="connect"></a>Połączenie
Operacja "Połącz" otwiera WebSocket w usłudze podanie nazwy połączenia hybrydowego i (opcjonalnie, ale wymagane domyślnie) tokenu zabezpieczeń przyznania uprawnień "Send" w ciągu zapytania. Usługa użyje odbiornika w sposób opisany wcześniej, a odbiornik tworzy połączenie spotkania, który jest połączony z tym protokołu WebSocket. Po zaakceptowaniu żądania WebSocket wszystkich dalszych interakcji, w tym protokołu WebSocket są połączone odbiornika.

### <a name="interaction-summary"></a>Interakcja podsumowania
Wynik tego modelu interakcji jest, czy klient nadawcy pochodzą poza uzgadnianie z WebSocket "Wyczyść", która jest połączona z odbiornik i wymaga nie dalsze preambles lub przygotowania. Ten model umożliwia praktycznie dowolnego istniejącego implementacja klienta WebSocket łatwo korzystać z usługi połączeń hybrydowych było możliwe, podając adres URL poprawnie skonstruowany do ich warstwy klienta protokołu WebSocket.

Połączenie WebSocket, który odbiornika uzyskuje się za pośrednictwem interakcji Akceptuj spotkania również jest czysty i można przekazać do istniejącej implementacji serwera protokołu WebSocket z niektórych minimalnego abstrakcji dodatkowe odróżniająca między operacjami "Akceptuj" w ich ramach odbiorników sieci lokalnej i połączeń hybrydowych było możliwe operacje zdalnego "Akceptuj".

## <a name="protocol-reference"></a>Odwołanie do protokołu

W tej sekcji opisano interakcji protokołu opisanych powyżej.

Wszystkie połączenia obiektu WebSocket są wykonane na porcie 443 uaktualnienie z 1.1 HTTPS, który jest powszechnie usunięte przez niektóre framework WebSocket lub interfejsu API. Opis w tym miejscu jest przechowywana implementacji neutralne, bez sugerowanie dla określonej platformy.

### <a name="listener-protocol"></a>Protokół odbiornika
Protokół odbiornika składa się z dwóch gestów połączenia i trzy operacje dotyczące komunikatów.

#### <a name="listener-control-channel-connection"></a>Połączenie kanału kontroli odbiornika
Kanał kontrolny jest otwarty przez utworzenie połączenia obiektu WebSocket:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...
```

`namespace-address` Jest w pełni kwalifikowaną nazwę przestrzeni nazw przekazywania Azure obsługującym połączenia hybrydowego, zazwyczaj formę `{myname}.servicebus.windows.net`.

Dostępne są następujące opcje parametru ciągu zapytania.

| Parametr | Wymagane | Opis |
| --- | --- | --- |
| `sb-hc-action` |Yes |Dla roli odbiornika parametr musi być **sb hc akcji = nasłuchiwania** |
| `{path}` |Yes |Ścieżka zakodowane w adresie URL przestrzeni nazw połączenia hybrydowego wstępnie skonfigurowane, aby zarejestrować się w tym odbiorniku. To wyrażenie jest dołączany do stałej `$hc/` części ścieżki. |
| `sb-hc-token` |Tak\* |Odbiornik Podaj prawidłową, zakodowane w adresie URL usługi magistrali udostępnionych Token dostępu dla przestrzeni nazw lub połączenie hybrydowe, który przyznaje **nasłuchiwania** prawo. |
| `sb-hc-id` |Nie |Ten identyfikator opcjonalne dostarczonych przez klienta umożliwia śledzenie diagnostyczne end-to-end. |

Jeśli połączenia obiektu WebSocket nie powiodło się z powodu ścieżka połączenia hybrydowego nie jest zarejestrowany lub token nieprawidłowe lub brakujące lub inny błąd, błąd opinii jest realizowane przy użyciu modelu regularne opinii stanu HTTP 1.1. Opis stanu zawiera błąd — identyfikator śledzenia być przekazane do personelu pomocy technicznej platformy Azure:

| Kod | Błąd | Opis |
| --- | --- | --- |
| 404 |Nie znaleziono |Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowy adres URL jest nieprawidłowo sformułowany. |
| 401 |Brak autoryzacji |Token zabezpieczający jest brak lub źle sformułowany lub nieprawidłowy. |
| 403 |Zabroniony |Token zabezpieczający nie jest prawidłowy dla tej ścieżki do wykonania tej akcji. |
| 500 |Błąd wewnętrzny |Wystąpił problem w usłudze. |

Wyłączenie połączenia obiektu WebSocket celowo przez usługę po jego został początkowo, przyczyna to przesyłane przy użyciu odpowiednich kod błędu protokołu WebSocket wraz z komunikat z opisem błędu, który obejmuje również identyfikator śledzenia Usługa nie wyłączy kanału kontroli bez napotkania warunek błędu. Wszelkie czystego zamknięcia jest kontrolowane przez klienta.

| Stan WS | Opis |
| --- | --- |
| 1001 |Ścieżka połączenia hybrydowego została usunięta lub wyłączona. |
| 1008 |Token zabezpieczeń wygasł, w związku z tym naruszenia zasad autoryzacji. |
| 1011 |Wystąpił problem w usłudze. |

### <a name="accept-handshake"></a>Zaakceptuj uzgadniania
"Zaakceptuj" powiadomienie jest wysyłane przez usługę do odbiornika w kanale kontroli uprzednio ustanowionym w jako wiadomość JSON do ramki protokołu WebSocket. Brak odpowiedzi na tę wiadomość nie istnieje.

Komunikat zawiera obiekt JSON o nazwie "Zaakceptuj", który definiuje następujące właściwości w tym momencie:

* **adres** — ciąg adresu URL używanego do utworzenia obiektu WebSocket do usługi do akceptowania połączeń przychodzących.
* **Identyfikator** — Unikatowy identyfikator dla tego połączenia. Jeśli identyfikator został podany przez klienta nadawcy, jest nadawca podana wartość, w przeciwnym razie wartość wygenerowana przez system.
* **connectHeaders** — wszystkie nagłówki HTTP, które zostały dostarczone z punktem końcowym przekazywania przez nadawcę, który obejmuje również protokół WebSocket s i nagłówków rozszerzeń-s-protokołu WebSocket.

#### <a name="accept-message"></a>Akceptowanie komunikatu

```json
{                                                           
    "accept" : {
        "address" : "wss://168.61.148.205:443/$hc/{path}?..."    
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",  
        "connectHeaders" : {                                         
            "Host" : "...",                                                
            "Sec-WebSocket-Protocol" : "...",                              
            "Sec-WebSocket-Extensions" : "..."                             
        }                                                            
     }                                                         
}
```

Adres URL zawarty w wiadomości JSON jest używany przez odbiornik do ustanawiania protokołu WebSocket dla akceptowanie lub odrzucanie gniazda nadawcy.

#### <a name="accepting-the-socket"></a>Akceptowania gniazda
Aby zaakceptować, odbiornika ustanawia połączenie obiektu WebSocket na podany adres.

Jeśli wiadomość "Zaakceptuj" przenosi `Sec-WebSocket-Protocol` nagłówka, oczekuje się, że odbiornika akceptuje tylko protokół WebSocket Jeśli obsługuje tego protokołu. Ponadto ustawia nagłówek ustalonych protokołu WebSocket.

To samo dotyczy `Sec-WebSocket-Extensions` nagłówka. Jeśli platforma obsługuje rozszerzenie, należy ustawić nagłówek odpowiedź po stronie serwera wymaganego `Sec-WebSocket-Extensions` uzgadniania dla rozszerzenia.

Adres URL musi być używany jako — jest ustalania gniazda Akceptuj, ale zawiera następujące parametry:

| Parametr | Wymagane | Opis |
| --- | --- | --- |
| `sb-hc-action` |Yes |Akceptowania gniazda, parametr musi być `sb-hc-action=accept` |
| `{path}` |Yes |(zobacz następujący ustęp) |
| `sb-hc-id` |Nie |Zobacz opis poprzedniego **identyfikator**. |

`{path}` to ścieżka zakodowane w adresie URL przestrzeni nazw wstępnie skonfigurowane połączenia hybrydowego, w którym można zarejestrować tego odbiornika. To wyrażenie jest dołączany do stałej `$hc/` części ścieżki. 

`path` Wyrażenie może zostać rozszerzona z sufiksem i następującym po oddzielający ukośnik zarejestrowaną nazwę wyrażenia ciągu zapytania. Dzięki temu klient nadawcy przekazać argumenty wysyłania akceptują odbiornika, gdy nie jest możliwe podanie nagłówków HTTP. Oczekuje się, że framework odbiornika analizuje części stałym ścieżki i nazwy zarejestrowane ze ścieżki i sprawia, że pozostała, prawdopodobnie bez żadnych argumentów ciągu zapytania poprzedzony `sb-`, dostępne dla aplikacji dotyczących decydowania, czy akceptować połączenia.

Aby uzyskać więcej informacji zobacz sekcję "Nadawcy protokół" poniżej.

Jeśli występuje błąd, usługa może odpowiedzieć w następujący sposób:

| Kod | Błąd | Opis |
| --- | --- | --- |
| 403 |Zabroniony |Adres URL jest nieprawidłowy. |
| 500 |Błąd wewnętrzny |Wystąpił problem w usłudze |

Po ustanowieniu połączenia serwera zamknięty, protokół WebSocket kiedy nadawcy WebSocket przebiega w dół lub z następujących stanów:

| Stan WS | Opis |
| --- | --- |
| 1001 |Nadawca klient zamyka połączenie. |
| 1001 |Ścieżka połączenia hybrydowego została usunięta lub wyłączona. |
| 1008 |Token zabezpieczeń wygasł, w związku z tym naruszenia zasad autoryzacji. |
| 1011 |Wystąpił problem w usłudze. |

#### <a name="rejecting-the-socket"></a>Odrzucanie gniazda
Odrzucanie gniazda po sprawdzeniu komunikat "Zaakceptuj" wymaga uzgadniania podobny kod stanu i opis stanu komunikacji przyczynę odrzucenia może przepływać do nadawcy.

Protokół wyboru tych elementów w tym miejscu jest użycie uzgadniania protokołu WebSocket (opracowaną w celu zakończenia w stanie błędu zdefiniowanych), aby implementacjach klienta odbiornika mogą w dalszym ciągu polegać na kliencie protokołu WebSocket i nie trzeba stosować dodatkowe, bez systemu operacyjnego klienta HTTP.

Aby odrzucić gniazda, klient pobiera adres URI z komunikatu "Zaakceptuj" i dołącza dwóch parametrów ciągu zapytania, w następujący sposób:

| Param | Wymagane | Opis |
| --- | --- | --- |
| statusCode |Yes |Kod stanu HTTP. |
| StatusDescription |Yes |Człowieka czytelny przyczynę odrzucenia. |

Wynikowy identyfikatora URI jest następnie używany do ustanawiania połączenia obiektu WebSocket.

Po zakończeniu poprawnie, to uzgadnianie celowo kończy się niepowodzeniem z kodem błędu HTTP 410, ponieważ WebSocket nie została ustanowiona. Jeśli jakaś nieprawidłowość, następujące kody opis błędu:

| Kod | Błąd | Opis |
| --- | --- | --- |
| 403 |Zabroniony |Adres URL jest nieprawidłowy. |
| 500 |Błąd wewnętrzny |Wystąpił problem w usłudze. |

### <a name="listener-token-renewal"></a>Odbiornik odnowienia tokenu
Gdy token odbiornika jest wygaśnie, odbiornika można zastąpić go, wysyłając wiadomość SMS ramki za pośrednictwem kanału kontroli ustanowionych z usługą. Komunikat zawiera obiekt JSON o nazwie `renewToken`, który definiuje następującą właściwość w tym czasie:

* **Token** — tokenu dostępu udostępnionego magistrali usługi prawidłowy, zakodowane w adresie URL dla przestrzeni nazw lub połączenie hybrydowe, który przyznaje **nasłuchiwania** prawo.

#### <a name="renewtoken-message"></a>komunikat renewToken

```json
{                                                                                                                                                                        
    "renewToken" : {                                                                                                                                                      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"  
    }                                                                                                                                                                     
}
```

W przypadku niepowodzenia weryfikacji tokenu dostępu, a usługa w chmurze zamyka kanał kontrolny protokołu WebSocket z powodu błędu. W przeciwnym razie jest żadnej odpowiedzi.

| Stan WS | Opis |
| --- | --- |
| 1008 |Token zabezpieczeń wygasł, w związku z tym naruszenia zasad autoryzacji. |

## <a name="sender-protocol"></a>Protokół nadawcy
Protokół nadawcy jest identyczne jak odbiornik zostanie nawiązane.
Celem jest maksymalną przezroczystość WebSocket end-to-end. Adres do nawiązania połączenia jest taki sam, jak w przypadku odbiornika, ale "Akcja" różni się i token musi różne uprawnienia:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

*Przestrzeń nazw adresów* jest w pełni kwalifikowaną nazwę przestrzeni nazw przekazywania Azure obsługującym połączenia hybrydowego, zazwyczaj formę `{myname}.servicebus.windows.net`.

Żądanie może zawierać dowolne dodatkowe nagłówków HTTP, w tym te zdefiniowane przez aplikację. Wszystkie podane nagłówki przepływać do odbiornika i znajduje się na `connectHeader` obiektu **zaakceptować** komunikatu kontroli.

Dostępne są następujące opcje parametru ciągu zapytania:

| Param | Wymagana? | Opis |
| --- | --- | --- |
| `sb-hc-action` |Yes |Dla roli nadawcy parametr musi być `action=connect`. |
| `{path}` |Yes |(zobacz następujący ustęp) |
| `sb-hc-token` |Tak\* |Odbiornik Podaj prawidłową, zakodowane w adresie URL usługi magistrali udostępnionych Token dostępu dla przestrzeni nazw lub połączenie hybrydowe, który przyznaje **wysyłania** prawo. |
| `sb-hc-id` |Nie |Opcjonalny identyfikator umożliwia śledzenie diagnostyczne end-to-end, który ma zostać udostępnione do odbiornika podczas uzgadniania accept. |

`{path}` Jest ścieżką zakodowane w adresie URL przestrzeni nazw wstępnie skonfigurowane połączenia hybrydowego, w którym można zarejestrować tego odbiornika. `path` Wyrażenie można rozszerzyć z sufiksem i wyrażenia ciągu zapytania do dalszej komunikacji. Jeśli połączenie hybrydowe jest zarejestrowany w ścieżce `hyco`, `path` wyrażenie może być `hyco/suffix?param=value&...` następuje parametrów ciągu zapytania, które są zdefiniowane w tym miejscu. Pełne wyrażenie może wyglądać następująco:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

`path` Wyrażenie jest przekazywana do odbiornika dla adresu URI zawarte w komunikacie kontroli "Zaakceptuj".

Jeśli połączenia obiektu WebSocket nie powiodło się z powodu ścieżka połączenia hybrydowego nie jest zarejestrowany, token nieprawidłowe lub brakujące lub inny błąd, błąd opinii jest realizowane przy użyciu modelu regularne opinii stanu HTTP 1.1. Opis stanu zawiera błąd śledzenia identyfikator, który może być przekazywane do personelu pomocy technicznej platformy Azure:

| Kod | Błąd | Opis |
| --- | --- | --- |
| 404 |Nie znaleziono |Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowy adres URL jest nieprawidłowo sformułowany. |
| 401 |Brak autoryzacji |Token zabezpieczający jest brak lub źle sformułowany lub nieprawidłowy. |
| 403 |Zabroniony |Token zabezpieczający nie jest prawidłowy dla tej ścieżki, a dla tej akcji. |
| 500 |Błąd wewnętrzny |Wystąpił problem w usłudze. |

Wyłączenie połączenia obiektu WebSocket celowo przez usługę po została początkowo prawidłowo skonfigurowana przyczyna to przesyłane przy użyciu odpowiednich kod błędu protokołu WebSocket wraz z komunikat z opisem błędu, który obejmuje również identyfikator śledzenia

| Stan WS | Opis |
| --- | --- |
| 1000 |Odbiornik zamknąć gniazda. |
| 1001 |Ścieżka połączenia hybrydowego została usunięta lub wyłączona. |
| 1008 |Token zabezpieczeń wygasł, w związku z tym naruszenia zasad autoryzacji. |
| 1011 |Wystąpił problem w usłudze. |

## <a name="next-steps"></a>Kolejne kroki
* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)
* [Tworzenie przestrzeni nazw](relay-create-namespace-portal.md)
* [Wprowadzenie do programu .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Wprowadzenie do programu Node](relay-hybrid-connections-node-get-started.md)

