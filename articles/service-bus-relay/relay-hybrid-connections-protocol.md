---
title: Połączenia hybrydowe usługi Azure Relay protokołu przewodnik | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący protokołu Azure połączeń hybrydowych usługi Relay.
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
ms.openlocfilehash: e96d0103a03e841f39e8adb88215f6d6e24a305a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706078"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Usługa Azure protokół połączeń hybrydowych usługi Relay

Usługa Azure Relay jest jednym z filarów kluczowy czynnik wpływający platformy Azure Service Bus. Nowy _połączeń hybrydowych_ możliwość przekazywania jest bezpieczne, otwartym protokole ewolucji, na podstawie protokołu HTTP i Websocket. Zastępuje ona pierwsze, o nazwie _usługi BizTalk Services_ funkcji, który został zbudowany na fundamencie własności protokołu. Integracja połączeń hybrydowych w usłudze Azure App Services będą w dalszym ciągu działać jako-to.

Połączenia hybrydowe umożliwia komunikację dwukierunkową strumienia danych binarnych i datagram prosty przepływ między dwiema aplikacjami sieciowymi. Lub obydwie strony mogą znajdować się poza translatorami adresów sieciowych lub zaporami.

W tym artykule opisano interakcje po stronie klienta za pomocą połączeń hybrydowych usługi relay klientów w ramach ról odbiornik i nadawcy nawiązujących połączenie. Opisano również, jak odbiorników akceptować nowych połączeń i żądania.

## <a name="interaction-model"></a>Model interakcji

Połączeń hybrydowych usługi relay łączy dwie strony, podając punkt spotkania w chmurze platformy Azure, które strony może odnajdywać i nawiązać połączenie z punktu widzenia własną sieć. Punkt spotkania jest nazywany "Połączenie hybrydowe" w tym i innych dokumentach, interfejsy API, a także w witrynie Azure portal. Punkt końcowy usługi połączeń hybrydowych, jest nazywana "Usługa" dla pozostałej części tego artykułu.

Usługa umożliwia przekazywanie połączeń gniazda sieci Web i żądań HTTP (S) i odpowiedzi.

Model interakcji leans na nomenklatury ustanowione przez wiele innych sieci interfejsów API. Brak odbiornik, który najpierw wskazuje gotowości do obsługi połączeń przychodzących, a następnie akceptuje, zgodnie z ich odbierania. Na tej stronie klient nawiąże połączenie do odbiornika, oczekiwano tego połączenia byli akceptowani ustanawiania ścieżki komunikacji dwukierunkowej. "Połącz", "Nasłuchiwania" i "Zaakceptuj" są te same warunki, w większości gniazda interfejsów API.

Każdy model komunikacji z przekazywaniem została każda ze stron wykonywania połączeń wychodzących do punktu końcowego usługi. To sprawia, że "odbiornik" również "client" w użyciu kolokwialne i może również spowodować, że inne przeciążenia terminologii. Dokładne terminologii, dlatego są używane dla połączeń hybrydowych jest następująca:

Programy po obu stronach połączenia są nazywane "klientów", ponieważ są to klienci z usługą. Klient, który czeka i akceptuje połączenia jest "odbiornik" lub jest określany jako "rolę odbiornika." Klient, który inicjuje nowe połączenie do odbiornika za pośrednictwem usługi nosi nazwę "nadawca" lub jest w "rolę nadawca."

### <a name="listener-interactions"></a>Interakcje odbiornika

Odbiornik ma pięć interakcji z usługą; wszystkie szczegółowe informacje o komunikacji sieciowej są opisane w dalszej części tego artykułu zamieszczone w tej sekcji.

Komunikaty nasłuchiwania, Zaakceptuj i żądania są odbierane z usługi. Odnów, oraz operacji Ping są wysyłane przez odbiornik.

#### <a name="listen-message"></a>Nasłuchiwać komunikatów

Aby wskazać gotowość do usługi, która jest odbiornik do akceptowania połączeń, tworzy wychodzące połączenie WebSocket. Uzgadnianie połączenia niesie ze sobą nazwa połączenia hybrydowego skonfigurowane na przestrzeń nazw usługi Relay, a token zabezpieczający, który przyznaje "Nasłuchiwania" bezpośrednio na nazwą.

Po zaakceptowaniu WebSocket przez usługę rejestracji zostało ukończone i ustanowionych WebSocket jest życiu jako "kanał kontrolny" umożliwiające wszystkie kolejne interakcje. Usługa umożliwia maksymalnie 25 współbieżnych odbiorników jedno połączenie hybrydowe. Limit przydziału AppHooks zostanie określony.

Dla połączeń hybrydowych Jeśli istnieją dwa lub więcej aktywne odbiorniki połączeń przychodzących są równoważone w je w kolejności losowej. uczciwe dystrybucji zostanie podjęta przy użyciu najlepszy nakład pracy.

#### <a name="accept-message"></a>Zaakceptować komunikat

Po otwarciu nowego połączenia w usłudze nadawcy usługa wybierze i powiadamia o jeden aktywne odbiorniki połączenie hybrydowe. To powiadomienie jest wysyłane do odbiornika za pośrednictwem kanału open kontroli, jak jeden komunikat w formacie JSON. Komunikat zawiera adres URL punktu końcowego protokołu WebSocket, który odbiornika należy nawiązać połączenie do akceptowania połączeń.

Adres URL można i musi zostać wykorzystana bezpośrednio przez odbiornik bez konieczności wykonywania dodatkowych działań.
Dane zakodowane jest prawidłowy tylko przez krótki okres czasu, zasadniczo tak długo, jak nadawca chce czekać na połączenie za ustanowionych end-to-end. Maksymalna, aby założył, to 30 sekund. Adres URL należy używać tylko dla jednego pomyślnym nawiązaniu połączenia. Jak najszybciej po nawiązaniu połączenia protokołu WebSocket przy użyciu adresu URL spotkania wszelkie dalsze działania w ramach tego protokołu WebSocket jest przekazywane z i do nadawcy. Dzieje się bez interwencji lub interpretacji przez usługę.

### <a name="request-message"></a>Komunikat żądania

Oprócz połączeń protokołu WebSocket odbiornik może również odbierać ramek żądania HTTP od nadawcy, jeśli ta funkcja jest jawnie włączone dla połączenia hybrydowego.

Odbiorników, dołączanie do połączeń hybrydowych z obsługą protokołu HTTP, które musi obsłużyć `request` gestu. Odbiornik, który nie obsługuje `request` i w związku z tym powoduje, że błędy powtarzanych przekroczenia limitu czasu podczas połączenia może być na czarnej liście przez usługę w przyszłości.

HTTP ramki nagłówka metadanych jest tłumaczona na JSON obsługi prostsze przez platformę odbiornika również ponieważ rzadkich niż analizatory składni JSON biblioteki analizy nagłówka HTTP. Metadane HTTP, które ma zastosowanie tylko dla relacji między nadawcą i bramy HTTP przekazywania, w tym informacji o autoryzacji, nie są przekazywane. Treści żądania HTTP przezroczyste są przekazywane jako binarne ramki protokołu WebSocket.

Odbiornik może odpowiadać na żądania HTTP za pomocą gestu równoważne odpowiedzi.

Przepływ żądań/odpowiedzi domyślnie używa kanału kontroli, ale mogą być "uaktualniane" do różnych spotkania WebSocket zawsze wtedy, gdy wymagane. DISTINCT połączeń protokołu WebSocket zwiększyć przepływność dla każdego klienta konwersacji, ale ich obciążać odbiornik mający więcej połączeń, które muszą być obsługiwani, może nie być wymaganą stanie uproszczone klientów.

W kanale kontroli treści żądania i odpowiedzi są ograniczone do co najwyżej 64 kB. Metadane nagłówka HTTP jest ograniczona do łącznie 32 kB. Jeśli żądania lub odpowiedzi przekracza progu, odbiornika należy uaktualnić do spotkania WebSocket przy użyciu odpowiednikiem obsługi gestu [Akceptuj](#accept-message).

W przypadku żądań usługi określa, czy należy kierować żądania za pośrednictwem kanału kontroli. To obejmuje, ale nie może być ograniczone do przypadków, w przypadku, gdy żądanie przekracza 64 kB (nagłówki i treść) od razu wykupić lub jeśli żądanie jest wysyłane za pomocą ["fragmentaryczne" transfer-encoding](https://tools.ietf.org/html/rfc7230#section-4.1) i usługa ma Przyczyna, których można spodziewać się żądanie przekracza 64 kB lub Odczytywanie żądania nie jest natychmiastowa. Jeśli usługa dostarczyć żądania za pośrednictwem spotkania, przekazuje on tylko kierującej komunikaty do adresu do odbiornika.
Następnie odbiornika należy ustanowić spotkania WebSocket, a usługa niezwłocznie zapewnia pełną żądania, w tym organów za pośrednictwem protokołu WebSocket spotkania. Odpowiedzi, należy również użyć spotkania protokołu WebSocket.

Dla żądania przychodzące za pośrednictwem kanału kontroli odbiornik Określa, czy należy odpowiedzieć za pośrednictwem kanału sterowania lub za pośrednictwem spotkania. Usługa musi zawierać adresem spotkania z każdym żądaniem kierowane za pośrednictwem kanału kontroli. Ten adres jest prawidłowy tylko dla uaktualnienie z bieżącego żądania.

Jeśli odbiornik zdecyduje się na uaktualnienie, łączy i natychmiast dostarcza odpowiedzi za pośrednictwem gniazd ustanowionych spotkania.

Raz spotkania, na których została ustanowiona WebSocket, odbiornik powinna obsługiwać za więcej obsługę żądań i odpowiedzi z tego samego klienta. Usługa będzie utrzymywać WebSocket dla tak długo, jak HTTPS gniazda połączenia z nadawcą się powtarza, a będzie przekierowywać wszystkie kolejne żądania z tego nadawcy przez WebSocket utrzymywane w dobrym stanie. Jeśli odbiornik porzucić spotkania WebSocket w bok, usługi spowoduje również porzucenie połączenie nadawcy, niezależnie od tego, czy kolejne żądanie może być już w toku.

#### <a name="renew-operation"></a>Operacja odnowienia

Token zabezpieczający, który musi być używany do rejestrowania odbiornik i obsługa kanał kontrolny mogą wygasnąć, gdy odbiornika jest aktywny. Wygaśnięcie tokenu nie ma wpływu na trwającą połączenia, ale spowodować, że kanał kontrolny, który ma zostać przerwane przez usługę w lub wkrótce po chwili wygaśnięcia. Operacja "odnowić" jest komunikat JSON, który odbiornik wysłać, aby zastąpić tokenu skojarzone z kanału kontroli, tak, aby kanał kontrolny mogą być obsługiwane przez dłuższy czas.

#### <a name="ping-operation"></a>Operacja ping

Jeśli kanał kontrolny pozostanie bezczynny przez długi czas pośredników w ten sposób, takich jak obciążenia równoważenia lub translatorami adresów sieciowych może porzucić połączenia TCP. Operacja "ping" pozwala uniknąć, wysyłając niewielką ilość danych w kanale, który przypomina o tym, wszyscy członkowie trasę sieciową, która ma być aktywne połączenia i służy również jako "na żywo" test dla odbiornika. Jeśli polecenie ping nie powiedzie się, kanał kontrolny powinna być uznana za niezdatną do, a odbiornik powinni ponownie nawiązać połączenie.

### <a name="sender-interaction"></a>Nadawca interakcji

Nadawca ma dwa interakcji z usługą: nawiąże połączenie gniazda sieci Web lub wysyła żądania za pośrednictwem protokołu HTTPS. Za pośrednictwem gniazda sieci Web nie można wysłać żądania, z roli nadawcy.

#### <a name="connect-operation"></a>Operacja połączenia

Operacja "Połącz" otwiera WebSocket w usłudze podanie nazwy połączenia hybrydowego i (opcjonalnie, ale wymagane domyślnie) token zabezpieczeń przyznania uprawnienia "Wyślij" w ciągu zapytania. Usługa użyje odbiornika w sposób opisany wcześniej, a odbiornik tworzy połączenie spotkania, która jest połączona z tego protokołu WebSocket. Po zaakceptowaniu WebSocket, wszystkie dalsze interakcje w tym protokołu WebSocket są połączonego odbiornika.

#### <a name="request-operation"></a>Operacja żądania

Dla połączeń hybrydowych, dla którego włączono funkcję nadawca może wysyłać stopniu nieograniczony żądania HTTP do odbiorników.

Z wyjątkiem przekazywania tokenu dostępu, który jest albo osadzony w ciągu zapytania lub nagłówka żądania HTTP usługi Relay jest w pełni przezroczyste wszystkie operacje HTTP na adres usługi Relay i wszystkie sufiksy ścieżki adresu usługi Relay, pozostawiając odbiornik pełni kontrolę nad en d-to-end autoryzacji i nawet funkcji rozszerzenia protokołu HTTP, takich jak [CORS](https://www.w3.org/TR/cors/).

Nadawca autoryzacji z punktem końcowym przekaźnika jest domyślnie włączona, ale jest OPCJONALNY. Właściciel połączenia hybrydowego można wybrać umożliwiał anonimowym nadawcom. Usługa przechwycić, zbadaj i paska informacji o autoryzacji w następujący sposób:

1. Jeśli ciąg zapytania zawiera `sb-hc-token` wyrażenia, wyrażenie zawsze zostanie pozbawiony ciągu zapytania. Zostanie ocenione, jeśli włączono autoryzacji usługi Relay.
2. Jeśli zawierają nagłówki żądania `ServiceBusAuthorization` nagłówek, nagłówek wyrażenie zawsze zostanie pozbawiony kolekcję nagłówków.
   Zostanie ocenione, jeśli włączono autoryzacji usługi Relay.
3. Tylko wtedy, gdy autoryzacja usługi Relay jest włączona i musi zawierać nagłówki żądania `Authorization` nagłówek i żadna z poprzednich wyrażeń nie jest obecny, nagłówek zostanie ocenione i usunięte. W przeciwnym razie `Authorization`jest zawsze przekazywana jako-to.

Jeśli nie ma żadnych aktywny odbiornik, usługa zwraca 502 kod błędu "Nieprawidłowa brama". Jeśli usługa nie zostanie wyświetlony obsłużyć żądania, usługa zwraca 504 "Gateway Timeout" po 60 sekundach.

### <a name="interaction-summary"></a>Podsumowanie interakcji

Ten model interakcji powstaje, czy klient nadawcy pochodzą poza uzgadnianie za pomocą "czysta" WebSocket, która jest połączona z odbiornik i wymagający żadne dalsze preambles ani przygotowania. Ten model umożliwia praktycznie dowolnym istniejącą implementacją klienta protokołu WebSocket, można łatwo korzystać z zalet usługi połączeń hybrydowych poprzez dostarczenie poprawnie skonstruowany adres URL do ich warstwy klienta protokołu WebSocket.

Spotkania połączeń protokołu WebSocket, który odbiornik uzyskuje się za pośrednictwem interakcji Akceptuj również jest czysty i można go przekazać żadnych istniejących implementacji serwera protokołu WebSocket przy użyciu niektóre minimalne abstrakcji dodatkowych, który rozróżnia "Akceptuj" operacje na sieć lokalną listeners ich framework i połączenia hybrydowe usługi zdalnej "Zaakceptuj" operacji.

Model żądań/odpowiedzi HTTP zapewnia nadawcy stopniu nieograniczony HTTP protokołu powierzchni z warstwą opcjonalne autoryzacji. Odbiornik pobiera wstępnie przeanalizowany sekcji do nagłówka żądania HTTP, która mogą zostać przekształcone podrzędnego żądania HTTP lub obsługiwane zgodnie z ramkami binarne wykonywania treści HTTP. Odpowiedzi używać tego samego formatu. Interakcje z mniej niż 64 KB w treści żądania i odpowiedzi mogą być obsługiwane za pośrednictwem jednego gniazda sieci Web, który jest udostępniany dla wszystkich nadawców. Większe żądania i odpowiedzi można obsługiwać przy użyciu modelu spotkania.

## <a name="protocol-reference"></a>Odwołanie do protokołu

W tej sekcji opisano interakcji protokołu opisanych powyżej.

Wszystkich połączeń protokołu WebSocket są dokonywane na porcie 443 jako uaktualnienie z 1.1 protokołu HTTPS, który jest powszechnie wyodrębnione przez niektóre framework WebSocket lub interfejsu API. Opis, w tym miejscu jest przechowywana implementacji neutralne, bez sugerowanie określonym środowiskiem.

### <a name="listener-protocol"></a>Odbiornik protokołu

Protokół odbiornika składa się z dwóch gesty połączenia i trzy operacje dotyczące wiadomości.

#### <a name="listener-control-channel-connection"></a>Połączenia kanału kontrolnego odbiornika

Kanał kontrolny jest otwierany przy użyciu tworzenia połączenia protokołu WebSocket:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

`namespace-address` Jest w pełni kwalifikowana nazwa domeny usługi Azure Relay przestrzeni nazw, który jest hostem połączenie hybrydowe, zwykle w formie `{myname}.servicebus.windows.net`.

Dostępne są następujące opcje parametru ciągu zapytania.

| Parametr        | Wymagane | Opis
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Tak      | Dla roli odbiornika parametr musi być **akcji w przypadku połączenia hybrydowego sb = nasłuchiwania**
| `{path}`         | Tak      | Ścieżka zakodowane jako adres URL przestrzeni nazw wstępnie skonfigurowane połączenie hybrydowe, aby zarejestrować tego odbiornika na. To wyrażenie jest dołączany do stałej `$hc/` część ścieżki.
| `sb-hc-token`    | Tak\*    | Odbiornik, musisz podać prawidłową, zakodowane w adresie URL udostępnione dostęp do tokenu usługi Service Bus dla przestrzeni nazw lub połączenie hybrydowe, która przyznaje **nasłuchiwania** prawo.
| `sb-hc-id`       | Nie       | Ten identyfikator opcjonalnie dostarczonych przez klienta umożliwia śledzenia diagnostycznego end-to-end.

Jeśli połączenie WebSocket zakończy się niepowodzeniem ze względu na ścieżce połączenia hybrydowego nie jest zarejestrowany lub tokenu nieprawidłowych lub brakujących lub inny błąd, informacji zwrotnych o błędach są udostępniane za pomocą regularnego modelu opinii stanu protokołu HTTP 1.1. Opis stanu zawiera błąd — identyfikator śledzenia mogą być przekazywane do personelu pomocy technicznej platformy Azure:

| Kod | Błąd          | Opis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nie można odnaleźć      | Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowego adresu URL jest nieprawidłowo sformułowany.
| 401  | Brak autoryzacji   | Token zabezpieczający jest brak lub źle sformułowany lub nieprawidłowy.
| 403  | Zabroniony      | Token zabezpieczeń jest nieprawidłowy dla tej ścieżki dla tej akcji.
| 500  | Błąd wewnętrzny | Wystąpił błąd w usłudze.

Jeśli połączenie WebSocket jest celowo zamknięta przez usługę po pierwotnie został ustawiony się Przyczyna w ten sposób są przekazywane przy użyciu odpowiedniego kodu błędu protokołu WebSocket wraz z opisowy komunikat o błędzie zawierający identyfikator śledzenia Usługa nie wyłączy kanał kontrolny nie powodując warunek błędu. Wszelkie czystego zamknięcia jest kontrolowany klienta.

| Stan usługi WS | Opis
| --------- | -------------------------------------------------------------------------------
| 1001      | Ścieżka połączenie hybrydowe zostało usunięte lub wyłączone.
| 1008      | Wygasł token zabezpieczający, w związku z tym naruszenia zasad autoryzacji.
| 1011      | Wystąpił błąd w usłudze.

#### <a name="accept-handshake"></a>Zaakceptuj uzgadniania

"Zaakceptuj" powiadomienie jest wysyłane przez usługę odbiornik za pośrednictwem kanału kontrolnego ustanowiony wcześniej sposób w jako wiadomość JSON do ramki protokołu WebSocket. Brak jest odpowiedzi na tę wiadomość.

Komunikat zawiera obiekt JSON o nazwie "Zaakceptuj", który definiuje następujące właściwości w tej chwili:

* **adres** — ciągu adresu URL do użytku z ustanowienie protokołu WebSocket z usługą można przyjmować połączenia przychodzące.
* **Identyfikator** — Unikatowy identyfikator dla tego połączenia. Jeśli identyfikator został dostarczony przez klienta nadawcy, jest to wartość nadawcy dostarczony, w przeciwnym razie wartość wygenerowanej przez system.
* **connectHeaders** — wszystkie nagłówki HTTP, które zostały dostarczone z punktem końcowym przekaźnika przez nadawcę, która obejmuje też protokół WebSocket s i nagłówków protokołu WebSocket-s-rozszerzenia.

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

Adres URL podany w formacie JSON wiadomości jest używany przez odbiornik ustanowienie protokołu WebSocket, aby zaakceptować lub odrzucić gniazda nadawcy.

##### <a name="accepting-the-socket"></a>Akceptowanie gniazda

Aby zaakceptować, odbiornik nawiązuje połączenie WebSocket na podany adres.

Jeśli komunikat "Zaakceptuj" niesie ze sobą `Sec-WebSocket-Protocol` nagłówka, oczekuje się, że odbiornik akceptuje tylko protokołu WebSocket, gdy obsługuje taki protokół. Ponadto ustawia nagłówek ustalonej pomocą protokołu WebSocket.

To samo dotyczy `Sec-WebSocket-Extensions` nagłówka. Jeśli struktura obsługuje rozszerzenie, należy ustawić nagłówek odpowiedź po stronie serwera wymaganego `Sec-WebSocket-Extensions` uzgadniania dla rozszerzenia.

Adres URL musi być używany jako — jest możliwość ustanowienia gniazda accept, ale zawiera następujące parametry:

| Parametr      | Wymagane | Opis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Tak      | Do akceptowania gniazda, parametr musi być `sb-hc-action=accept`
| `{path}`       | Tak      | (zobacz następujący akapit)
| `sb-hc-id`     | Nie       | Zobacz opis poprzedniego **identyfikator**.

`{path}` jest to ścieżka zakodowane jako adres URL przestrzeni nazw, wstępnie skonfigurowane połączenia hybrydowego, w którym można zarejestrować tego odbiornika. To wyrażenie jest dołączany do stałej `$hc/` część ścieżki.

`path` Wyrażenie może zostać rozszerzona przy użyciu sufiksu i wyrażenie ciągu zapytania, które następuje zarejestrowanej nazwy po oddzielający ukośnikiem.
Dzięki temu klient nadawcy przekazać argumenty wysyłania akceptują połączenia z odbiornikiem, gdy nie jest możliwe nagłówków HTTP. Oczekuje się, że framework odbiornika analizuje część ścieżki stały i zarejestrowanej nazwy ze ścieżki i sprawia, że resztę, prawdopodobnie bez żadnych argumentów ciągów zapytania, poprzedzony `sb-`, która jest dostępna do aplikacji w celu podejmowania decyzji Określa, czy akceptować połączenia.

Aby uzyskać więcej informacji zobacz następującą sekcję "Protocol nadawca".

Jeśli wystąpi błąd, usługa potrzebują pomocy eksperta w następujący sposób:

| Kod | Błąd          | Opis
| ---- | -------------- | -----------------------------------
| 403  | Zabroniony      | Adres URL jest nieprawidłowy.
| 500  | Błąd wewnętrzny | Wystąpił problem w usłudze

 Po ustanowieniu połączenia serwera zamknięty, WebSocket kiedy nadawcy WebSocket przebiega w dół lub z następujących stanów:

| Stan usługi WS | Opis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Klient nadawcy zamknięcie połączenia.                                    |
| 1001      | Ścieżka połączenie hybrydowe zostało usunięte lub wyłączone.                        |
| 1008      | Wygasł token zabezpieczający, w związku z tym naruszenia zasad autoryzacji. |
| 1011      | Wystąpił błąd w usłudze.                                            |

##### <a name="rejecting-the-socket"></a>Odrzuca gniazda

 Odrzuca gniazda po sprawdzeniu `accept` komunikat wymaga uzgadniania podobny kod stanu i opis stanu podczas komunikowania się z powodu, dla odrzucenia może przepływać z powrotem do nadawcy.

 Protokół uzasadnienie wyboru tych elementów w tym miejscu jest użycie uzgadniania protokołu WebSocket (który jest przeznaczony do końca w stanie błędu zdefiniowany), aby odbiornika implementacjach klienta mogą w dalszym ciągu opierają się na kliencie protokołu WebSocket i nie trzeba stosować dodatkowy, bez klienta HTTP.

 Aby odrzucić gniazda, klient pobiera adres URI z `accept` komunikatu i dołącza dwa parametry ciągu zapytania, w następujący sposób:

| Param                   | Wymagane | Opis                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusCode        | Tak      | Kod stanu HTTP.                |
| sb-hc-statusDescription | Tak      | Ludzi czytelny Przyczyna odrzucenia. |

Wynikowy identyfikatora URI jest następnie używany do ustanawiania połączenia protokołu WebSocket.

Podczas wykonywania poprawnie, ta uzgadnianie celowo kończy się niepowodzeniem z kodem błędu HTTP 410, ponieważ WebSocket nie została ustanowiona. Jeśli coś pójdzie nie tak, następujące kody opisać błąd:

| Kod | Błąd          | Opis                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Zabroniony      | Adres URL jest nieprawidłowy.                |
| 500  | Błąd wewnętrzny | Wystąpił błąd w usłudze. |

#### <a name="request-message"></a>Komunikat żądania

`request` Komunikat jest wysyłany przez usługę do odbiornika za pośrednictwem kanału kontroli. Ten sam komunikat jest również przesyłany przez spotkania WebSocket po.

`request` Składa się z dwóch części: Nagłówek i danych binarnych ramki treści.
W przypadku bez treści ramek treści są pomijane. Wskaźnik do tego, czy treść jest obecny jest atrybut typu wartość logiczna `body` właściwości w komunikacie żądania.

Dla żądania o treści żądania struktura może wyglądać następująco:

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

Odbiornik musi obsługiwać odbieranie treści żądania, podzielone między wiele ramek binarny (zobacz [fragmenty WebSocket](https://tools.ietf.org/html/rfc6455#section-5.4)).
Żądanie kończy się, gdy binarne ramkę z ustawioną flagą Finanse zostały odebrane.

Żądania bez treści jest tylko jednej ramce.

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

Zawartość JSON `request` jest następująca:

* **adres** — ciągu identyfikatora URI. Jest to kierującej komunikaty do adresu do użycia dla tego żądania. Jeśli żądanie przychodzące ma rozmiar większy niż 64 kB, pozostała część tego komunikatu jest puste i klient musi inicjować uzgadniania spotkania odpowiednikiem `accept` operacji opisanych poniżej. Usługa następnie składane w pełne `request` w ustalonych gniazda sieci Web. Jeśli można oczekiwać odpowiedzi przekracza 64 kB, odbiornik musi również Zainicjuj uzgadniania spotkania i następnie przenieść odpowiedzi za pośrednictwem ustalonych gniazda sieci Web.
* **Identyfikator** — ciąg. Unikatowy identyfikator dla tego żądania.
* **requestHeaders** — ten obiekt zawiera wszystkie nagłówki HTTP, które zostały przekazane do punktu końcowego przez nadawcę, z wyjątkiem informacji o autoryzacji zgodnie z objaśnieniem [powyżej](#request-operation), nagłówki, które dotyczą wyłącznie i połączenie z bramą. W szczególności wszystkie nagłówki zdefiniowany lub zastrzeżone w [RFC7230](https://tools.ietf.org/html/rfc7230), z wyjątkiem `Via`, są usunięte i nie dalej:

  * `Connection` (RFC7230 sekcji 6.1)
  * `Content-Length`  (RFC7230 sekcji 3.3.2)
  * `Host`  (RFC7230, ppkt 5.4)
  * `TE`  (RFC7230 4.3 sekcji)
  * `Trailer`  (RFC7230 sekcji 4.4)
  * `Transfer-Encoding`  (RFC7230 sekcji 3.3.1)
  * `Upgrade` (RFC7230 sekcji 6.7)
  * `Close`  (RFC7230 sekcji 8.1)

* **requestTarget** — ciąg. Tej właściwości jest przechowywana ["Żądanie Target" (RFC7230, sekcja 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) żądania. Obejmuje to część ciągu zapytania, które będą wszystkie `sb-hc-` prefiks parametrów.
* **Metoda** -ciąg. Jest to metoda żądania na [RFC7231, sekcja 4](https://tools.ietf.org/html/rfc7231#section-4). `CONNECT` Metoda nie może być używany.
* **treść** — jest to wartość logiczna. Wskazuje, czy następuje jeden lub więcej ramek dane binarne ciała.

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

##### <a name="responding-to-requests"></a>Odpowiadanie na żądania

Odbiorca musi odpowiedzieć. Powtarzane na odpowiadanie na żądania przy zachowaniu połączenia może spowodować odbiornika wprowadzenie na czarnej liście.

Odpowiedzi mogą być przesyłane w dowolnej kolejności, ale każde żądanie musi być wypełniona w ciągu 60 sekund lub dostarczania będą raportowane jako posiadające nie powiodło się. Termin 60 sekund jest naliczana do momentu `response` ramki zostały odebrane przez usługę. Trwającą odpowiedzi z wieloma ramkami binarny nie przejdzie w stan bezczynności przez więcej niż 60 sekund lub zostanie zakończony.

Jeśli żądanie zostanie odebrana w kanale kontroli, odpowiedzi albo musi zostać wysłany w kanale kontroli, z której zostało odebrane żądanie lub musi zostać wysłany za pośrednictwem kanału spotkania.

Odpowiedzią jest obiekt JSON o nazwie "response". Do obsługi zawartości w treści dostępne są następujące reguły dokładnie, jak za pomocą `request` wiadomości i na podstawie `body` właściwości.

* **Identyfikator żądania** — ciąg. WYMAGANE. `id` Wartość właściwości `request` wiadomości jest wypełniona.
* **statusCode** — jest to liczba. WYMAGANE. wartości liczbowych HTTP kod stanu wskazujący wynik powiadomienia. Wszystkie kody stanu [RFC7231, sekcja 6](https://tools.ietf.org/html/rfc7231#section-6) są dozwolone, z wyjątkiem [502 ": Nieprawidłowa brama"](https://tools.ietf.org/html/rfc7231#section-6.6.3) i [504 "Gateway Timeout"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **Opisstanu** -ciąg. OPCJONALNIE. Fraza przyczyny kod stanu HTTP na [RFC7230, sekcja 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** — nagłówki HTTP w zewnętrznych odpowiedzi HTTP.
  Podobnie jak w przypadku `request`, RFC7230 zdefiniowanych nagłówków nie mogą być używane.
* **treść** — jest to wartość logiczna. Wskazuje, czy dane binarne ciała ramki follow(s).

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

Odpowiedzi, które przekracza 64 kB, aby uzyskać odpowiedzi muszą być dostarczone przez gniazdo spotkania. Również, jeśli żądanie przekracza 64 kB, a `request` zawiera tylko pola Adres gniazda spotkania, należy ustanowić uzyskać `request`. Po ustanowieniu gniazda spotkania, odpowiedzi na odpowiednim kliencie i kolejne żądania z tego odpowiedniego klienta musi być dostarczana za pośrednictwem gniazd spotkania, będzie się utrzymywał go.

`address` Adresu URL w `request` muszą być używane jako — jest możliwość ustanowienia gniazda spotkania, ale zawiera następujące parametry:

| Parametr      | Wymagane | Opis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Tak      | Do akceptowania gniazda, parametr musi być `sb-hc-action=request`

Jeśli wystąpi błąd, usługa potrzebują pomocy eksperta w następujący sposób:

| Kod | Błąd           | Opis
| ---- | --------------- | -----------------------------------
| 400  | Nieprawidłowe żądanie | Akcja nierozpoznany lub nieprawidłowy adres URL.
| 403  | Zabroniony       | Adres URL wygasł.
| 500  | Błąd wewnętrzny  | Wystąpił problem w usłudze

 Po ustanowieniu połączenia serwera kończy pracę WebSocket, wyłączaniu gniazda HTTP klienta lub z następujących stanów:

| Stan usługi WS | Opis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Klient nadawcy zamknięcie połączenia.                                    |
| 1001      | Ścieżka połączenie hybrydowe zostało usunięte lub wyłączone.                        |
| 1008      | Wygasł token zabezpieczający, w związku z tym naruszenia zasad autoryzacji. |
| 1011      | Wystąpił błąd w usłudze.                                            |


#### <a name="listener-token-renewal"></a>Odbiornik odnowienia tokenu

Po token odbiornik o zbliżającym się go można zastąpić, wysyłając wiadomość SMS ramki za pośrednictwem kanału kontroli ustanowionych w usłudze. Komunikat zawiera obiekt JSON o nazwie `renewToken`, która definiuje następującą właściwość w tej chwili:

* **Token** — prawidłowy, zakodowane w adresie URL tokenu dostępu do usługi Service Bus udostępniony dla przestrzeni nazw lub połączenie hybrydowe, która przyznaje **nasłuchiwania** prawo.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

W przypadku niepowodzenia weryfikacji tokenu odmowa dostępu i usługi w chmurze zamyka kanał kontrolny protokołu WebSocket z powodu błędu. W przeciwnym razie jest brak odpowiedzi.

| Stan usługi WS | Opis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Wygasł token zabezpieczający, w związku z tym naruszenia zasad autoryzacji. |

### <a name="web-socket-connect-protocol"></a>Protokół połączenia gniazda sieci Web

Protokół nadawcy jest identyczne jak odbiornik zostanie nawiązane.
Celem jest przezroczystości maksymalne protokołu WebSocket end-to-end. Adres, aby nawiązać połączenie jest taka sama, jak w przypadku odbiornika, ale "action" różni się i token wymaga różne uprawnienia:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

_Przestrzeń nazw adresów_ jest w pełni kwalifikowana nazwa domeny usługi Azure Relay przestrzeni nazw, który jest hostem połączenie hybrydowe, zwykle w formie `{myname}.servicebus.windows.net`.

Żądanie może zawierać dowolne dodatkowe nagłówki HTTP, w tym zdefiniowanych przez aplikację. Wszystkie podane nagłówki przepływu połączenia z odbiornikiem i znajduje się na `connectHeader` obiektu **zaakceptować** komunikatu kontroli.

Dostępne są następujące opcje parametru ciągu zapytania:

| Param          | Wymagana? | Opis
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Yes       | Dla roli nadawcy, parametr musi być `sb-hc-action=connect`.
| `{path}`       | Yes       | (zobacz następujący akapit)
| `sb-hc-token`  | Tak\*     | Odbiornik, musisz podać prawidłową, zakodowane w adresie URL udostępnione dostęp do tokenu usługi Service Bus dla przestrzeni nazw lub połączenie hybrydowe, która przyznaje **wysyłania** prawo.
| `sb-hc-id`     | Nie        | Opcjonalny identyfikator umożliwia śledzenia diagnostycznego end-to-end, która ma zostać udostępnione do odbiornika podczas uzgadniania accept.

 `{path}` Jest ścieżką zakodowane jako adres URL przestrzeni nazw wstępnie skonfigurowane połączenie hybrydowe, w którym można zarejestrować tego odbiornika. `path` Wyrażenia można rozszerzyć za pomocą sufiksu i wyrażenia ciągu zapytania do dalszej komunikacji. Jeśli połączenie hybrydowe jest zarejestrowany w ścieżce `hyco`, `path` wyrażenie może być `hyco/suffix?param=value&...` następuje parametry ciągu zapytania, które są zdefiniowane w tym miejscu. Dokończ wyrażenie może być następująca:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

`path` Wyrażenie jest przekazywana do odbiornika adres URI znajdujących się w oknie komunikatu kontrola "Zaakceptuj".

Jeśli połączenie WebSocket zakończy się niepowodzeniem z powodu ścieżki połączenia hybrydowego nie jest zarejestrowany, token nieprawidłowych lub brakujących lub inny błąd, informacji zwrotnych o błędach są udostępniane za pomocą regularnego modelu opinii stanu protokołu HTTP 1.1. Opis stanu zawiera błąd śledzenia identyfikator, który może być przekazywane do personelu pomocy technicznej platformy Azure:

| Kod | Błąd          | Opis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nie można odnaleźć      | Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowego adresu URL jest nieprawidłowo sformułowany.
| 401  | Brak autoryzacji   | Token zabezpieczający jest brak lub źle sformułowany lub nieprawidłowy.
| 403  | Zabroniony      | Token zabezpieczający nie jest prawidłowy dla tej ścieżki, a dla tej akcji.
| 500  | Błąd wewnętrzny | Wystąpił błąd w usłudze.

Jeśli połączenie WebSocket jest celowo zamknięta przez usługę po jego wstępnie skonfigurowano, przyczyna w ten sposób są przekazywane przy użyciu odpowiedni kod błędu protokołu WebSocket wraz z opisowy komunikat o błędzie zawierający identyfikator śledzenia: .

| Stan usługi WS | Opis
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Odbiornik Zamknij gniazda.
| 1001      | Ścieżka połączenie hybrydowe zostało usunięte lub wyłączone.
| 1008      | Wygasł token zabezpieczający, w związku z tym naruszenia zasad autoryzacji.
| 1011      | Wystąpił błąd w usłudze.

### <a name="http-request-protocol"></a>Protokół żądania HTTP

Żądanie protokołu HTTP umożliwia podpisywanie dowolnych żądań HTTP, z wyjątkiem uaktualnień protokołu.
Żądania HTTP są wskazany pod adresem regularnych środowiska uruchomieniowego jednostki, bez wrostkowe $hc, używany dla połączeń hybrydowych klientów protokołu WebSocket.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

_Przestrzeń nazw adresów_ jest w pełni kwalifikowana nazwa domeny usługi Azure Relay przestrzeni nazw, który jest hostem połączenie hybrydowe, zwykle w formie `{myname}.servicebus.windows.net`.

Żądanie może zawierać dowolne dodatkowe nagłówki HTTP, w tym zdefiniowanych przez aplikację. Wszystkie podane nagłówki, z wyjątkiem tych bezpośrednio jest zdefiniowana w RFC7230 (zobacz [komunikatu żądania](#Request message)) przepływu połączenia z odbiornikiem i znajduje się na `requestHeader` obiektu **żądania** wiadomości.

Dostępne są następujące opcje parametru ciągu zapytania:

| Param          | Wymagana? | Opis
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Tak\*     | Odbiornik, musisz podać prawidłową, zakodowane w adresie URL udostępnione dostęp do tokenu usługi Service Bus dla przestrzeni nazw lub połączenie hybrydowe, która przyznaje **wysyłania** prawo.

Tokenu można wykonać w obu `ServiceBusAuthorization` lub `Authorization` nagłówka HTTP. Tokenu można pominąć, jeśli połączenie hybrydowe jest skonfigurowany, aby zezwolić na żądania anonimowe.

Ponieważ usługa skutecznie działa jako serwer proxy, nawet wtedy, gdy nie jako wartość true, serwer proxy HTTP albo dodaje `Via` nagłówek lub oznacza stosowanym istniejące `Via` nagłówka zgodne z [RFC7230, sekcja 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
Usługa dodaje nazwę hosta przestrzeni nazw usługi Relay do `Via`.

| Kod | Message  | Opis                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | Żądanie jest przetwarzane przez co najmniej jeden odbiornik.  |
| 202  | Zaakceptowane | Żądanie zostało zaakceptowane przez co najmniej jednego odbiornika. |

Jeśli wystąpi błąd, usługa potrzebują pomocy eksperta w następujący sposób. Czy odpowiedź pochodzi z usługi lub odbiornik mogą być identyfikowane przez obecność `Via` nagłówka. Jeśli występuje nagłówek odpowiedzi jest z odbiornika.

| Kod | Błąd           | Opis
| ---- | --------------- |--------- |
| 404  | Nie można odnaleźć       | Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowego adresu URL jest nieprawidłowo sformułowany.
| 401  | Brak autoryzacji    | Token zabezpieczający jest brak lub źle sformułowany lub nieprawidłowy.
| 403  | Zabroniony       | Token zabezpieczający nie jest prawidłowy dla tej ścieżki, a dla tej akcji.
| 500  | Błąd wewnętrzny  | Wystąpił błąd w usłudze.
| 503  | Zła brama     | Żądanie nie może być kierowany do dowolnego odbiornika.
| 504  | Limit czasu bramy | Żądanie zostało kierowane do odbiornika, ale odbiornik nie potwierdził przyjęcia w wymaganym czasie.

## <a name="next-steps"></a>Kolejne kroki

* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)
* [Tworzenie przestrzeni nazw](relay-create-namespace-portal.md)
* [Wprowadzenie do programu .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Wprowadzenie do programu Node](relay-hybrid-connections-node-get-started.md)
