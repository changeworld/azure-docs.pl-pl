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
ms.date: 05/02/2018
ms.author: clemensv
ms.openlocfilehash: 306a21add76261dce99c954a2ba373e4b5047a75
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895414"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure protokołu przekazywania połączeń hybrydowych

Przekazywania Azure to jedna z kolumn klucza możliwości platformy Azure Service Bus. Nowy _połączeń hybrydowych_ możliwość przekazywania jest bezpieczne, protokołu open zmiany, na podstawie HTTP i protokołu WebSockets. Zastępuje ona poprzedniego jednakowo o nazwie _usługi BizTalk Services_ funkcji, który został utworzony na podstawie zastrzeżonym protokołem. Integrację usługi aplikacji Azure połączeń hybrydowych było możliwe będzie działać jako — jest.

Połączeń hybrydowych umożliwia komunikacja dwukierunkowa binarne strumienia i proste datagram przepływ między dwiema aplikacjami sieci. Jedną lub obie strony może znajdować się za NAT lub zaporą.

W tym artykule opisano interakcje po stronie klienta przekazywania połączeń hybrydowych nawiązywania połączeń klientów w rolach nadawcy i odbiornika. Opisuje również, jak odbiorników akceptować nowych połączeń i żądań.

## <a name="interaction-model"></a>Model interakcji

Przekazywania połączeń hybrydowych łączy dwie strony zapewniając punkt spotkania w chmurze Azure, który strony mogą odnaleźć i nawiązać połączenie z punktu widzenia własnych sieci. Punkt spotkania jest nazywany "Połączenia hybrydowego" w tym i innych dokumentacji, interfejsy API, a także w portalu Azure. Punkt końcowy usługi połączeń hybrydowych było możliwe jest określana jako "Usługa" dla pozostałej części tego artykułu.

Usługa umożliwia przekazywanie połączenia gniazda sieci Web i żądań HTTP (S) i odpowiedzi.

Model interakcji leans na nomenklaturę wyznaczane przez wiele innych sieci interfejsów API. Brak odbiornika, najpierw wskazuje gotowości do obsługi połączeń przychodzących, a następnie akceptuje, zgodnie z ich odbierania. Z drugiej strony klient nawiąże połączenie do odbiornika, oczekiwano takiego połączenia akceptację ustalania ścieżki komunikacja dwukierunkowa. "Połącz", "Nasłuchiwania" i "Zaakceptuj" są tego samego postanowienia, które możesz znaleźć w gnieździe większość interfejsów API.

Każdy model komunikacji obsługiwanych przez przekaźnik ma albo strony wykonywania połączeń wychodzących na punkt końcowy usługi. Ułatwia to "odbiornika" również "client" potocznej używana i może również spowodować inne przeciążenia terminologii. Terminologia dokładne, dlatego używane do połączeń hybrydowych było możliwe jest następujący:

Programy po obu stronach połączenia są nazywane "klientów", ponieważ są one klientów do usługi. Czeka, który akceptuje połączenia klienta jest "odbiornika" lub jest określany jako "roli odbiornika." Klienta, który inicjuje nowe połączenie do odbiornika za pomocą usługi jest nazywany "sender" lub "rolą nadawcy."

### <a name="listener-interactions"></a>Odbiornik interakcji

Odbiornik ma pięć interakcji z usługą; w dalszej części tego artykułu zamieszczone w tej sekcji opisano wszystkie szczegóły danych przesyłanych w sieci.

Komunikaty nasłuchiwania, Zaakceptuj i żądania są odbierane z usługi. Odnawianie i operacje Ping są wysyłane przez odbiornik.

#### <a name="listen-message"></a>Nasłuchiwać komunikatów

Aby wskazać gotowość do usługi, która jest odbiornik gotowy do akceptowania połączeń, tworzy wychodzące połączenia obiektu WebSocket. Uzgadnianie połączenia przyjmuje nazwę połączenie hybrydowe skonfigurowany na przestrzeń nazw przekazywania i token zabezpieczający, który przyznaje "Nasłuchiwania" zgodny z nazwą.

Gdy protokół WebSocket jest akceptowane przez usługę, rejestracja została zakończona i ustanowić protokołu WebSocket jest utrzymywane jako "kanał kontrolny" włączania wszystkich kolejnych interakcji. Usługa umożliwia maksymalnie 25 równoczesnych odbiorników jednego połączenia hybrydowego. Limit przydziału AppHooks zostanie określony.

Dla połączeń hybrydowych było możliwe jeśli istnieje co najmniej dwa odbiorniki aktywne połączenia przychodzące są równoważone między je w kolejności losowej. odpowiedni dystrybucji jest usiłowano optymalnego.

#### <a name="accept-message"></a>Akceptowanie komunikatu

Po otwarciu nowego połączenia w usłudze nadawcy usługa wybierze i powiadamia jednej aktywnej odbiorników na połączenia hybrydowego. To powiadomienie jest wysyłane do odbiornika za pośrednictwem kanału open kontroli jako wiadomość JSON. Komunikat zawiera adres URL punktu końcowego protokołu WebSocket, który odbiornika należy nawiązać połączenie do akceptowania połączeń.

Adres URL może i mogą być używane bezpośrednio przez odbiornik bez konieczności wykonywania dodatkowych działań.
Dane zakodowane jest prawidłowa tylko krótki okres czasu, zasadniczo tak długo, jak nadawca jest chce czekać na połączenie za ustalonych end-to-end. Maksymalna założenie to 30 sekund. Adres URL można używać tylko dla jednego pomyślnego połączenia. Natychmiast po nawiązaniu połączenia obiektu WebSocket z adresem URL spotkania wszystkich dalszych działań w tym protokołu WebSocket jest przekazywany z i do nadawcy. Dzieje się bez interwencji lub interpretacji przez usługę.

### <a name="request-message"></a>Komunikat żądania

Oprócz połączeń protokołu WebSocket odbiornik może również odbierać ramki żądania HTTP od nadawcy, jeśli ta możliwość jest jawnie włączona na połączenia hybrydowego.

Odbiorniki dołączone do połączeń hybrydowych z obsługą protokołu HTTP musi obsługiwać `request` gestu. Odbiornik, który nie obsługuje `request` i w związku z tym powoduje, że błędy przekroczenia limitu czasu powtórzony podczas połączenia może być na liście zabronionych numerów przez usługę w przyszłości.

HTTP ramki nagłówka metadanych jest przetłumaczyć JSON obsługi prostsze przez platformę odbiornika również ponieważ rzadkich niż analizatory składni JSON są biblioteki podczas analizowania nagłówka HTTP. Metadane HTTP, które ma zastosowanie tylko dla relacji między nadawcą i bramy HTTP przekazywania, w tym informacje o autoryzacji nie są przesyłane dalej. Treści żądania HTTP niewidocznie są przekazywane jako binarne ramki protokołu WebSocket.

Odbiornik może odpowiadać na żądania HTTP za pomocą gestu równoważne odpowiedzi.

Przepływ żądania/odpowiedzi domyślnie używa kanału kontroli, ale można "uaktualnić" do różnych spotkania WebSocket zawsze, gdy wymagane. Różne połączeń protokołu WebSocket zwiększyć przepływność dla każdego klienta konwersacji, ale ich obciążać odbiornik o większej liczby połączeń, które muszą być obsługiwani, które nie mogą być potrzeby mogli lekkie klientów.

W kanale kontroli treści żądania i odpowiedzi są ograniczone do maksymalnie 64 kB. Metadane nagłówka HTTP jest może zawierać maksymalnie 32 kB. Jeśli żądanie lub odpowiedź przekracza progu, odbiornika należy uaktualnić do wersji spotkania za pomocą gestu odpowiednikiem Obsługa protokołu WebSocket [Akceptuj](#accept-message).

Dla żądania usługi decyduje o tym, czy można przekierować żądania w kanale kontroli. To obejmuje, ale nie może być ograniczone do przypadków, w przypadku, gdy żądanie przekracza 64 kB (nagłówki oraz treści) bezpośrednich lub gdy żądanie zostanie wysłane z ["fragmentaryczne" transfer-encoding](https://tools.ietf.org/html/rfc7230#section-4.1) i usługa ma Przyczyna oczekiwać żądanie może przekraczać 64 kB lub Odczytywanie żądania nie jest natychmiastowe. Jeśli usługa wybierze się dostarczyć żądania za pośrednictwem spotkania, tylko przekazuje adresu spotkania do odbiornika.
Następnie odbiornika należy ustanowić spotkania protokołu WebSocket i usługa natychmiast dostarcza pełne żądanie razem z treściami spotkania protokołu WebSocket. Odpowiedzi muszą również korzystać spotkania protokołu WebSocket.

Dla żądania przychodzące za pośrednictwem kanału kontroli odbiornik zadecyduje, czy odpowiedź kanałem sterowania lub za pośrednictwem spotkania. Usługa musi zawierać adres spotkania z każdym żądaniem kierowane za pośrednictwem kanału kontroli. Ten adres jest prawidłowy tylko w przypadku uaktualniania z bieżącego żądania.

Jeśli odbiornik uaktualnienia, łączy i natychmiast dostarcza odpowiedzi za pośrednictwem ustalonych spotkania gniazda.

Raz spotkania, który protokołu WebSocket została ustanowiona, odbiornika powinien jego utrzymania dla dalszego obsługiwanie żądań i odpowiedzi z tego samego klienta. Usługa zachowa protokołu WebSocket dla tak długo, jak połączenie z nadawcą gniazda HTTPS się powtarza, a będzie przekierowywać wszystkie kolejne żądania z tego nadawcy za pomocą obsługiwanych protokołu WebSocket. Jeśli odbiornik porzucić spotkania protokołu WebSocket w bok, usługi spowoduje również porzucenie połączenie nadawcy, niezależnie od tego, czy kolejne żądania mogą być już w toku.

#### <a name="renew-operation"></a>Odnów operacji

Token zabezpieczający, który musi być używany do rejestrowania odbiornika i obsługa kanał kontrolny może wygaśnie, gdy odbiornika jest aktywny. Wygaśnięcia tokenu nie ma wpływu na bieżące połączenia, ale powodować kanału kontroli ma być przerwane przez usługę w lub wkrótce po chwili wygaśnięcia. Operacja "odnowić" jest komunikat JSON, który odbiornika można wysyłać do Zamień token skojarzony z kanału kontroli, tak aby kanał kontrolny mogą być obsługiwane przez dłuższy czas.

#### <a name="ping-operation"></a>Operacja ping

Jeśli kanał kontrolny pozostanie bezczynny przez długi czas pośredników w taki sposób, takie jak obciążenia równoważenia lub NAT może porzucić połączenie TCP. Operacja "ping" pozwala uniknąć który wysyłając niewielką ilość danych w kanale, który przypomina o tym, wszyscy członkowie trasę sieciową, która ma być aktywne połączenie i służy również jako "na żywo" testu dla odbiornika. Jeśli polecenie ping nie powiedzie się, kanał kontrolny należy traktować jako bezużyteczne i odbiornika powinni połączyć się ponownie.

### <a name="sender-interaction"></a>Nadawca interakcji

Nadawca ma dwa interakcji z usługą: nawiązuje połączenie gniazda sieci Web lub wysyłania żądań za pośrednictwem protokołu HTTPS. Żądania nie można wysłać za pośrednictwem gniazda sieci Web z roli nadawcy.

#### <a name="connect-operation"></a>Operację łączenia

Operacja "Połącz" otwiera WebSocket w usłudze podanie nazwy połączenia hybrydowego i (opcjonalnie, ale wymagane domyślnie) tokenu zabezpieczeń przyznania uprawnień "Send" w ciągu zapytania. Usługa użyje odbiornika w sposób opisany wcześniej, a odbiornik tworzy połączenie spotkania, który jest połączony z tym protokołu WebSocket. Po zaakceptowaniu żądania WebSocket wszystkich dalszych interakcji, w tym protokołu WebSocket są połączone odbiornika.

#### <a name="request-operation"></a>Operacja żądania

Dla połączeń hybrydowych, dla którego włączono funkcję nadawca wysyłać przede wszystkim nieograniczony żądania HTTP do odbiorników.

Z wyjątkiem tokenu dostępu przekazywania, która jest albo osadzonego w ciągu zapytania lub nagłówek HTTP żądania przekaźnika jest całkowicie niewidoczny dla wszystkich operacji HTTP adresu przekazywania i wszystkie sufiksy Ścieżka adresu przekazywania, pozostawiając odbiornika pełni kontrolę nad en d-to-end autoryzacji i nawet funkcji rozszerzenia protokołu HTTP, takich jak [CORS](https://www.w3.org/TR/cors/).

Nadawca autoryzacji z punktem końcowym przekazywania jest domyślnie włączona, ale jest opcjonalne. Właściciel połączenia hybrydowego można wybrać opcję Zezwalaj na anonimowe nadawcy. Usługa przechwycenia, inspekcji i usuwanie informacji o autoryzacji w następujący sposób:

1. Jeśli ciąg zapytania zawiera `sb-hc-token` wyrażenia, wyrażenie będzie zawsze być usunięte z ciągu zapytania. Będą oceniane, jeśli włączono przekazywania autoryzacji.
2. Jeśli zawierają nagłówki żądania `ServiceBusAuthorization` nagłówek, nagłówek wyrażenie będzie zawsze być usunięte z kolekcji nagłówka.
   Będą oceniane, jeśli włączono przekazywania autoryzacji.
3. Tylko w przypadku przekazywania autoryzacji jest włączona, a nagłówki żądania zawierają `Authorization` nagłówka i żadna z poprzedniego wyrażenia nie jest obecny, nagłówek zostanie obliczone i usunięta. W przeciwnym razie `Authorization`zawsze są przekazywane jako — jest.

Jeśli nie ma żadnych aktywnych odbiornik, usługa zwróci 502 kod błędu "Nieprawidłowa brama". Jeśli usługi nie można obsłużyć żądania, usługa zwróci 504 "limit czasu bramy" po 60 sekundach.

### <a name="interaction-summary"></a>Interakcja podsumowania

Wynik tego modelu interakcji jest, czy klient nadawcy pochodzą poza uzgadnianie z WebSocket "Wyczyść", która jest połączona z odbiornik i wymaga nie dalsze preambles lub przygotowania. Ten model umożliwia praktycznie dowolnego istniejącego implementacja klienta WebSocket łatwo korzystać z usługi połączeń hybrydowych było możliwe, podając poprawnie skonstruowany adres URL do ich warstwy klienta protokołu WebSocket.

Połączenie WebSocket, który odbiornika uzyskuje się za pośrednictwem interakcji Akceptuj spotkania również jest czysty i można przekazać do istniejącej implementacji serwera protokołu WebSocket z niektórych minimalnego abstrakcji dodatkowe odróżniająca między operacjami "Akceptuj" w ich ramach odbiorników sieci lokalnej i połączeń hybrydowych było możliwe operacje zdalnego "Akceptuj".

Model żądanie i odpowiedź HTTP zapewnia nadawca przede wszystkim nieograniczony HTTP protokołu powierzchni z warstwą opcjonalne autoryzacji. Odbiornik pobiera wstępnie przeanalizowany sekcję do nagłówka żądania HTTP, którą z powrotem do podrzędnego żądania HTTP lub obsługiwane jako binarne ramek wykonywanie treści HTTP. Odpowiedzi na tym samym formatu. Interakcje z mniej niż 64 KB treści żądania i odpowiedzi mogą być obsługiwane za pośrednictwem jednego gniazda sieci Web, który jest współużytkowany dla wszystkich nadawców. Większe żądań i odpowiedzi można obsługiwać przy użyciu modelu spotkania.

## <a name="protocol-reference"></a>Odwołanie do protokołu

W tej sekcji opisano interakcji protokołu opisanych powyżej.

Wszystkie połączenia obiektu WebSocket są wykonane na porcie 443 uaktualnienie z 1.1 HTTPS, który jest powszechnie usunięte przez niektóre framework WebSocket lub interfejsu API. Opis w tym miejscu jest przechowywana implementacji neutralne, bez sugerowanie dla określonej platformy.

### <a name="listener-protocol"></a>Protokół odbiornika

Protokół odbiornika składa się z dwóch gestów połączenia i trzy operacje dotyczące komunikatów.

#### <a name="listener-control-channel-connection"></a>Połączenie kanału kontroli odbiornika

Kanał kontrolny jest otwarty z tworzenia połączenia obiektu WebSocket:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

`namespace-address` Jest w pełni kwalifikowaną nazwę przestrzeni nazw przekazywania Azure obsługującym połączenia hybrydowego, zazwyczaj formę `{myname}.servicebus.windows.net`.

Dostępne są następujące opcje parametru ciągu zapytania.

| Parametr        | Wymagane | Opis
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Yes      | Dla roli odbiornika parametr musi być **sb hc akcji = nasłuchiwania**
| `{path}`         | Yes      | Ścieżka zakodowane w adresie URL przestrzeni nazw połączenia hybrydowego wstępnie skonfigurowane, aby zarejestrować się w tym odbiorniku. To wyrażenie jest dołączany do stałej `$hc/` części ścieżki.
| `sb-hc-token`    | Tak\*    | Odbiornik Podaj prawidłową, zakodowane w adresie URL usługi magistrali udostępnionych Token dostępu dla przestrzeni nazw lub połączenie hybrydowe, który przyznaje **nasłuchiwania** prawo.
| `sb-hc-id`       | Nie       | Ten identyfikator opcjonalne dostarczonych przez klienta umożliwia śledzenie diagnostyczne end-to-end.

Jeśli połączenia obiektu WebSocket nie powiodło się z powodu ścieżka połączenia hybrydowego nie jest zarejestrowany lub token nieprawidłowe lub brakujące lub inny błąd, błąd opinii jest realizowane przy użyciu modelu regularne opinii stanu HTTP 1.1. Opis stanu zawiera błąd — identyfikator śledzenia być przekazane do personelu pomocy technicznej platformy Azure:

| Kod | Błąd          | Opis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nie znaleziono      | Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowy adres URL jest nieprawidłowo sformułowany.
| 401  | Brak autoryzacji   | Token zabezpieczający jest brak lub źle sformułowany lub nieprawidłowy.
| 403  | Zabroniony      | Token zabezpieczający nie jest prawidłowy dla tej ścieżki do wykonania tej akcji.
| 500  | Błąd wewnętrzny | Wystąpił problem w usłudze.

Wyłączenie połączenia obiektu WebSocket celowo przez usługę po jego został początkowo, przyczyna to przesyłane przy użyciu odpowiednich kod błędu protokołu WebSocket wraz z komunikat z opisem błędu, który obejmuje również identyfikator śledzenia Usługa nie wyłączy kanału kontroli bez napotkania warunek błędu. Wszelkie czystego zamknięcia jest kontrolowane przez klienta.

| Stan WS | Opis
| --------- | -------------------------------------------------------------------------------
| 1001      | Ścieżka połączenia hybrydowego została usunięta lub wyłączona.
| 1008      | Token zabezpieczeń wygasł, w związku z tym naruszenia zasad autoryzacji.
| 1011      | Wystąpił problem w usłudze.

#### <a name="accept-handshake"></a>Zaakceptuj uzgadniania

"Zaakceptuj" powiadomienie jest wysyłane przez usługę do odbiornika w kanale kontroli uprzednio ustanowionym w jako wiadomość JSON do ramki protokołu WebSocket. Brak odpowiedzi na tę wiadomość nie istnieje.

Komunikat zawiera obiekt JSON o nazwie "Zaakceptuj", który definiuje następujące właściwości w tym czasie:

* **adres** — ciąg adresu URL używanego do utworzenia obiektu WebSocket do usługi do akceptowania połączeń przychodzących.
* **Identyfikator** — Unikatowy identyfikator dla tego połączenia. Jeśli identyfikator został podany przez klienta nadawcy, jest nadawca podana wartość, w przeciwnym razie wartość wygenerowana przez system.
* **connectHeaders** — wszystkie nagłówki HTTP, które zostały dostarczone z punktem końcowym przekazywania przez nadawcę, który obejmuje również protokół WebSocket s i nagłówków rozszerzeń-s-protokołu WebSocket.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
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

##### <a name="accepting-the-socket"></a>Akceptowania gniazda

Aby zaakceptować, odbiornika ustanawia połączenie obiektu WebSocket na podany adres.

Jeśli wiadomość "Zaakceptuj" przenosi `Sec-WebSocket-Protocol` nagłówka, oczekuje się, że odbiornika akceptuje tylko protokół WebSocket Jeśli obsługuje tego protokołu. Ponadto ustawia nagłówek ustalonych protokołu WebSocket.

To samo dotyczy `Sec-WebSocket-Extensions` nagłówka. Jeśli platforma obsługuje rozszerzenie, należy ustawić nagłówek odpowiedź po stronie serwera wymaganego `Sec-WebSocket-Extensions` uzgadniania dla rozszerzenia.

Adres URL musi być używany jako — jest ustalania gniazda Akceptuj, ale zawiera następujące parametry:

| Parametr      | Wymagane | Opis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Yes      | Akceptowania gniazda, parametr musi być `sb-hc-action=accept`
| `{path}`       | Yes      | (zobacz następujący ustęp)
| `sb-hc-id`     | Nie       | Zobacz opis poprzedniego **identyfikator**.

`{path}` to ścieżka zakodowane w adresie URL przestrzeni nazw wstępnie skonfigurowane połączenia hybrydowego, w którym można zarejestrować tego odbiornika. To wyrażenie jest dołączany do stałej `$hc/` części ścieżki.

`path` Wyrażenie może zostać rozszerzona z sufiksem i następującym po oddzielający ukośnik zarejestrowaną nazwę wyrażenia ciągu zapytania.
Dzięki temu klient nadawcy przekazać argumenty wysyłania akceptują odbiornika, gdy nie jest możliwe podanie nagłówków HTTP. Oczekuje się, że framework odbiornika analizuje części stałym ścieżki i nazwy zarejestrowane ze ścieżki i sprawia, że pozostała, prawdopodobnie bez żadnych argumentów ciągu zapytania poprzedzony `sb-`, dostępne dla aplikacji dotyczących decydowania, czy akceptować połączenia.

Aby uzyskać więcej informacji zobacz sekcję "Nadawcy protokół" poniżej.

Jeśli występuje błąd, usługa może odpowiedzieć w następujący sposób:

| Kod | Błąd          | Opis
| ---- | -------------- | -----------------------------------
| 403  | Zabroniony      | Adres URL jest nieprawidłowy.
| 500  | Błąd wewnętrzny | Wystąpił problem w usłudze

 Po ustanowieniu połączenia serwera zamknięty, protokół WebSocket kiedy nadawcy WebSocket przebiega w dół lub z następujących stanów:

| Stan WS | Opis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Nadawca klient zamyka połączenie.                                    |
| 1001      | Ścieżka połączenia hybrydowego została usunięta lub wyłączona.                        |
| 1008      | Token zabezpieczeń wygasł, w związku z tym naruszenia zasad autoryzacji. |
| 1011      | Wystąpił problem w usłudze.                                            |

##### <a name="rejecting-the-socket"></a>Odrzucanie gniazda

 Odrzucanie gniazda po sprawdzeniu `accept` wiadomości nie wymaga uzgadniania podobny kod stanu i opis stanu komunikowania się z powodu dla odrzucenia mogą przepływać z powrotem do nadawcy.

 Protokół wyboru tych elementów w tym miejscu jest użycie uzgadniania protokołu WebSocket (opracowaną w celu zakończenia w stanie błędu zdefiniowanych), aby implementacjach klienta odbiornika mogą w dalszym ciągu polegać na kliencie protokołu WebSocket i nie trzeba stosować dodatkowe, bez systemu operacyjnego klienta HTTP.

 Aby odrzucić gniazda, klient pobiera adres URI z `accept` wiadomości i dołącza dwóch parametrów ciągu zapytania, w następujący sposób:

| Param                   | Wymagane | Opis                              |
| ----------------------- | -------- | ---------------------------------------- |
| SB-hc-statusCode        | Yes      | Kod stanu HTTP.                |
| SB-hc-statusDescription | Yes      | Człowieka czytelny przyczynę odrzucenia. |

Wynikowy identyfikatora URI jest następnie używany do ustanawiania połączenia obiektu WebSocket.

Po zakończeniu poprawnie, to uzgadnianie celowo kończy się niepowodzeniem z kodem błędu HTTP 410, ponieważ WebSocket nie została ustanowiona. Jeśli jakaś nieprawidłowość, następujące kody opis błędu:

| Kod | Błąd          | Opis                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Zabroniony      | Adres URL jest nieprawidłowy.                |
| 500  | Błąd wewnętrzny | Wystąpił problem w usłudze. |

#### <a name="request-message"></a>Komunikat żądania

`request` Komunikatów są wysyłane przez usługę do odbiornika za pośrednictwem kanału kontroli. Ten sam komunikat zostanie także wysłana za pośrednictwem protokołu WebSocket po spotkania.

`request` Składa się z dwóch części: ramki treści binarnego i nagłówek.
Jeśli nie ma żadnych treści, zostały pominięte ramki treści. Czy treść jest obecny wskaźnik jest typu boolean `body` właściwości w komunikacie żądania.

Dla żądania z treści żądania struktura może wyglądać następująco:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

Odbiornik musi obsługiwać odbieranie treści żądania, podzielić na wiele ramek binarne (zobacz [fragmenty protokołu WebSocket](https://tools.ietf.org/html/rfc6455#section-5.4)).
Żądanie kończy się po otrzymaniu binarne ramki z ustawioną flagą w wynikach wyszukiwania.

Żądanie bez treści jest tylko jednej ramce.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

Zawartość JSON `request` wygląda następująco:

* **adres** -ciągu identyfikatora URI. Jest to adres spotkania do użycia dla tego żądania. Jeśli żądanie przychodzące jest większy niż 64 kB, pozostała część tego komunikatu jest puste i klient musi inicjować uzgadniania spotkania odpowiednikiem `accept` operacji opisane poniżej. Usługa następnie wprowadzi pełną `request` w ustalonych gniazda sieci Web. Jeśli odpowiedź można spodziewać się być dłuższa niż 64 kB, odbiornika musi również inicjować uzgadniania spotkania i przekazać odpowiedzi za pośrednictwem ustalonych gniazda sieci Web.
* **Identyfikator** — ciąg. Unikatowy identyfikator dla tego żądania.
* **requestHeaders** — ten obiekt zawiera wszystkie nagłówki HTTP, które zostały dostarczone z punktem końcowym przez nadawcę, z wyjątkiem informacji o autoryzacji zgodnie z objaśnieniem [powyżej](#request-operation), a nagłówki, które dotyczą wyłącznie połączenie z bramą. W szczególności wszystkie nagłówki zdefiniowany lub zastrzeżone w [RFC7230](https://tools.ietf.org/html/rfc7230), z wyjątkiem `Via`, są usunięte i nie zostały przekazane:

  * `Connection` (RFC7230 sekcji 6.1)
  * `Content-Length`  (RFC7230, sekcja 3.3.2)
  * `Host`  (RFC7230, 5.4 sekcji)
  * `TE`  (RFC7230 4.3 sekcji)
  * `Trailer`  (RFC7230, 4.4 sekcji)
  * `Transfer-Encoding`  Sekcja 3.3.1 (RFC7230;)
  * `Upgrade` (RFC7230 6.7 sekcji)
  * `Close`  (RFC7230 sekcji 8.1)

* **requestTarget** — ciąg. Ta właściwość przechowuje ["Cel żądania" (RFC7230, 5.3 sekcji)](https://tools.ietf.org/html/rfc7230#section-5.3) żądania. Obejmuje to część ciągu zapytania, które będą wszystkie `sb-hc-` prefiksem parametrów.
* **Metoda** -ciąg. Jest to metoda żądania, na [RFC7231, 4 sekcji](https://tools.ietf.org/html/rfc7231#section-4). `CONNECT` Metody nie mogą być używane.
* **treść** — wartość logiczna. Wskazuje, czy więcej więcej binarne klatka treści jest zgodna.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>Odpowiada na żądania

Odbiorca musi odpowiedzieć. Powtórzony odpowiadać na żądania przy zachowaniu połączenia może spowodować odbiornika pierwsze na liście zabronionych numerów.

Odpowiedzi mogą być wysyłane w dowolnej kolejności, ale każde żądanie musi być odpowiedzi w ciągu 60 sekund lub dostarczanie będą raportowane jako posiadające nie powiodło się. Termin 60 sekund jest liczony do `response` ramki zostały odebrane przez usługę. Bieżące odpowiedzi z wieloma ramkami binarny nie może stać się bezczynności przez więcej niż 60 sekund lub zostanie zakończony.

Odebranie żądania w kanale kontroli, odpowiedź albo muszą być wysyłane w kanale kontroli, z której zostało odebrane żądanie lub musi być wysyłane za pośrednictwem kanału spotkania.

Odpowiedzią jest obiekt JSON o nazwie "odpowiedzi". Zasady obsługi zawartości w treści są dokładnie tak samo, jak z `request` wiadomości i na podstawie `body` właściwości.

* **Identyfikator żądania** — ciąg. WYMAGANE. `id` Wartość właściwości `request` komunikatu odpowiedzi.
* **statusCode** — numer. WYMAGANE. numeryczny kod stanu HTTP wskazująca wynik powiadomienia. Wszystkie kody stanu [RFC7231, 6 sekcji](https://tools.ietf.org/html/rfc7231#section-6) są dozwolone z wyjątkiem [502 "zły brama"](https://tools.ietf.org/html/rfc7231#section-6.6.3) i [504 "limit czasu bramy"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** -ciąg. OPCJONALNE. Fraza przyczyny kod stanu HTTP na [RFC7230, sekcja 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** — nagłówków HTTP w zewnętrznych odpowiedzi HTTP.
  Jak `request`, RFC7230 zdefiniowanych nagłówków nie mogą być używane.
* **treść** — wartość logiczna. Wskazuje, czy binarne treści ramki follow(s).

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Odpowiada za pośrednictwem spotkania

Dla odpowiedzi, które może przekraczać 64 kB odpowiedzi muszą być dostarczone przez gniazda spotkania. Również, jeśli żądanie przekracza 64 kB i `request` zawiera tylko pole adresu gniazda spotkania należy ustanowić uzyskanie `request`. Po ustanowieniu gniazda spotkania odpowiedzi na odpowiednim kliencie i kolejne żądania z tego klienta odpowiednich muszą być dostarczone za pośrednictwem gniazda spotkania, podczas ona.

`address` Adres URL w `request` muszą być używane jako — jest ustalania gniazda spotkania, ale zawiera następujące parametry:

| Parametr      | Wymagane | Opis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Yes      | Akceptowania gniazda, parametr musi być `sb-hc-action=request`

Jeśli występuje błąd, usługa może odpowiedzieć w następujący sposób:

| Kod | Błąd           | Opis
| ---- | --------------- | -----------------------------------
| 400  | Nieprawidłowe żądanie | Akcja nierozpoznany lub nieprawidłowy adres URL.
| 403  | Zabroniony       | Adres URL utracił ważność.
| 500  | Błąd wewnętrzny  | Wystąpił problem w usłudze

 Po ustanowieniu połączenia serwera zamknięty protokołu WebSocket podczas zamykania klienta HTTP gniazda lub z następujących stanów:

| Stan WS | Opis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Nadawca klient zamyka połączenie.                                    |
| 1001      | Ścieżka połączenia hybrydowego została usunięta lub wyłączona.                        |
| 1008      | Token zabezpieczeń wygasł, w związku z tym naruszenia zasad autoryzacji. |
| 1011      | Wystąpił problem w usłudze.                                            |


#### <a name="listener-token-renewal"></a>Odbiornik odnowienia tokenu

Gdy token odbiornika jest wygaśnie, go można zastąpić, wysyłając wiadomość SMS ramki za pośrednictwem kanału kontroli ustanowionych z usługą. Komunikat zawiera obiekt JSON o nazwie `renewToken`, który definiuje następującą właściwość w tym czasie:

* **Token** — tokenu dostępu udostępnionego magistrali usługi prawidłowy, zakodowane w adresie URL dla przestrzeni nazw lub połączenie hybrydowe, który przyznaje **nasłuchiwania** prawo.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

W przypadku niepowodzenia weryfikacji tokenu dostępu, a usługa w chmurze zamyka kanał kontrolny protokołu WebSocket z powodu błędu. W przeciwnym razie jest żadnej odpowiedzi.

| Stan WS | Opis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Token zabezpieczeń wygasł, w związku z tym naruszenia zasad autoryzacji. |

### <a name="web-socket-connect-protocol"></a>Protokół połączenia gniazda sieci Web

Protokół nadawcy jest identyczne jak odbiornik zostanie nawiązane.
Celem jest maksymalną przezroczystość WebSocket end-to-end. Adres do nawiązania połączenia jest taki sam, jak w przypadku odbiornika, ale "Akcja" różni się i token musi różne uprawnienia:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

_Przestrzeń nazw adresów_ jest w pełni kwalifikowaną nazwę przestrzeni nazw przekazywania Azure obsługującym połączenia hybrydowego, zazwyczaj formę `{myname}.servicebus.windows.net`.

Żądanie może zawierać dowolne dodatkowe nagłówków HTTP, w tym te zdefiniowane przez aplikację. Wszystkie podane nagłówki przepływać do odbiornika i znajduje się na `connectHeader` obiektu **zaakceptować** komunikatu kontroli.

Dostępne są następujące opcje parametru ciągu zapytania:

| Param          | Wymagana? | Opis
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Yes       | Dla roli nadawcy parametr musi być `sb-hc-action=connect`.
| `{path}`       | Yes       | (zobacz następujący ustęp)
| `sb-hc-token`  | Tak\*     | Odbiornik Podaj prawidłową, zakodowane w adresie URL usługi magistrali udostępnionych Token dostępu dla przestrzeni nazw lub połączenie hybrydowe, który przyznaje **wysyłania** prawo.
| `sb-hc-id`     | Nie        | Opcjonalny identyfikator umożliwia śledzenie diagnostyczne end-to-end, który ma zostać udostępnione do odbiornika podczas uzgadniania accept.

 `{path}` Jest ścieżką zakodowane w adresie URL przestrzeni nazw wstępnie skonfigurowane połączenia hybrydowego, w którym można zarejestrować tego odbiornika. `path` Wyrażenie można rozszerzyć z sufiksem i wyrażenia ciągu zapytania do dalszej komunikacji. Jeśli połączenie hybrydowe jest zarejestrowany w ścieżce `hyco`, `path` wyrażenie może być `hyco/suffix?param=value&...` następuje parametrów ciągu zapytania, które są zdefiniowane w tym miejscu. Pełne wyrażenie może wyglądać następująco:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

`path` Wyrażenie jest przekazywana do odbiornika dla adresu URI zawarte w komunikacie kontroli "Zaakceptuj".

Jeśli połączenia obiektu WebSocket nie powiodło się z powodu ścieżka połączenia hybrydowego nie jest zarejestrowany, token nieprawidłowe lub brakujące lub inny błąd, błąd opinii jest realizowane przy użyciu modelu regularne opinii stanu HTTP 1.1. Opis stanu zawiera błąd śledzenia identyfikator, który może być przekazywane do personelu pomocy technicznej platformy Azure:

| Kod | Błąd          | Opis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nie znaleziono      | Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowy adres URL jest nieprawidłowo sformułowany.
| 401  | Brak autoryzacji   | Token zabezpieczający jest brak lub źle sformułowany lub nieprawidłowy.
| 403  | Zabroniony      | Token zabezpieczający nie jest prawidłowy dla tej ścieżki, a dla tej akcji.
| 500  | Błąd wewnętrzny | Wystąpił problem w usłudze.

Wyłączenie połączenia obiektu WebSocket celowo przez usługę po została początkowo prawidłowo skonfigurowana przyczyna to przesyłane przy użyciu odpowiednich kod błędu protokołu WebSocket wraz z komunikat z opisem błędu, który obejmuje również identyfikator śledzenia

| Stan WS | Opis
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Odbiornik zamknąć gniazda.
| 1001      | Ścieżka połączenia hybrydowego została usunięta lub wyłączona.
| 1008      | Token zabezpieczeń wygasł, w związku z tym naruszenia zasad autoryzacji.
| 1011      | Wystąpił problem w usłudze.

### <a name="http-request-protocol"></a>Protokół HTTP żądania

Żądanie protokołu HTTP umożliwia dowolnego żądania HTTP, z wyjątkiem uaktualnienia protokołu.
Żądania HTTP są odnosi się pod adresem regularne środowiska uruchomieniowego jednostki, bez infiksu $hc, służący do połączenia hybrydowe klientów protokołu WebSocket.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

_Przestrzeń nazw adresów_ jest w pełni kwalifikowaną nazwę przestrzeni nazw przekazywania Azure obsługującym połączenia hybrydowego, zazwyczaj formę `{myname}.servicebus.windows.net`.

Żądanie może zawierać dowolne dodatkowe nagłówków HTTP, w tym te zdefiniowane przez aplikację. Wszystkie podane nagłówki, z wyjątkiem tych bezpośrednio definiowane w RFC7230 (zobacz [komunikat żądania](#Request message)) przepływać do odbiornika i znajduje się na `requestHeader` obiektu **żądania** wiadomości.

Dostępne są następujące opcje parametru ciągu zapytania:

| Param          | Wymagana? | Opis
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Tak\*     | Odbiornik Podaj prawidłową, zakodowane w adresie URL usługi magistrali udostępnionych Token dostępu dla przestrzeni nazw lub połączenie hybrydowe, który przyznaje **wysyłania** prawo.

Tokenu można wykonać w jednym `ServiceBusAuthorization` lub `Authorization` nagłówka HTTP. Tokenu można pominąć, jeśli połączenie hybrydowe jest skonfigurowany w celu zezwolenia na anonimowe żądania.

Ponieważ usługa skutecznie działa jako serwer proxy, nawet jeśli nie, jako wartość true, serwer proxy HTTP albo dodaje `Via` nagłówka lub oznacza istniejące `Via` nagłówka zgodne z [RFC7230, sekcja 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
Usługa dodaje przekazywania hostname przestrzeni nazw do `Via`.

| Kod | Komunikat  | Opis                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | Żądanie zostało obsłużone przez co najmniej jeden odbiornik.  |
| 202  | Zaakceptowany | Żądanie zostało zaakceptowane przez co najmniej jednego odbiornika. |

Jeśli występuje błąd, usługa może odpowiedzieć w następujący sposób. Określa, czy odpowiedź pochodzi z usługi lub odbiornika mogą zostać zidentyfikowane przez obecności `Via` nagłówka. Jeśli występuje nagłówek odpowiedzi jest z odbiornika.

| Kod | Błąd           | Opis
| ---- | --------------- |--------- |
| 404  | Nie znaleziono       | Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowy adres URL jest nieprawidłowo sformułowany.
| 401  | Brak autoryzacji    | Token zabezpieczający jest brak lub źle sformułowany lub nieprawidłowy.
| 403  | Zabroniony       | Token zabezpieczający nie jest prawidłowy dla tej ścieżki, a dla tej akcji.
| 500  | Błąd wewnętrzny  | Wystąpił problem w usłudze.
| 503  | Zła brama     | Żądanie nie może być kierowane do dowolnego odbiornika.
| 504  | Limit czasu bramy | Żądanie został skierowany do odbiornika, ale odbiornik nie potwierdził przyjęcia w wymaganym czasie.

## <a name="next-steps"></a>Kolejne kroki

* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)
* [Tworzenie przestrzeni nazw](relay-create-namespace-portal.md)
* [Wprowadzenie do programu .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Wprowadzenie do programu Node](relay-hybrid-connections-node-get-started.md)
