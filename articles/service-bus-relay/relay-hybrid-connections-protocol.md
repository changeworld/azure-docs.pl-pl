---
title: Przewodnik po protokole protokołu połączeń hybrydowych usługi Azure Relay | Dokumenty firmy Microsoft
description: W tym artykule opisano interakcje po stronie klienta z przekaźnikiem połączeń hybrydowych do łączenia klientów w rolach odbiornika i nadawcy.
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
ms.date: 01/21/2020
ms.author: clemensv
ms.openlocfilehash: 68668452152064584d1c419a3053ccb642b103f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514956"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protokół połączeń hybrydowych usługi Azure Relay

Usługa Azure Relay jest jednym z kluczowych filarów możliwości platformy Usługi Azure Service Bus. Nowa funkcja _połączeń hybrydowych_ Relay to bezpieczna, otwarta ewolucja protokołu oparta na http i websockets. Zastępuje tę dawną, równie nazwaną funkcję _BizTalk Services,_ która została zbudowana na zastrzeżonym fundamencie protokołu. Integracja połączeń hybrydowych z usługami Azure App Services będzie nadal działać w stanie, w jakim się znajduje.

Połączenia hybrydowe umożliwiają dwukierunkową, binarną komunikację strumienia i prosty przepływ datagramu między dwiema aplikacjami sieciowymi. Każda lub obie strony mogą przebywać za nat lub zapór.

W tym artykule opisano interakcje po stronie klienta z przekaźnikiem połączeń hybrydowych do łączenia klientów w rolach odbiornika i nadawcy. Opisano w nim również, jak detektory akceptują nowe połączenia i żądania.

## <a name="interaction-model"></a>Model interakcji

Przekaźnik połączeń hybrydowych łączy dwie strony, zapewniając punkt spotkania w chmurze platformy Azure, który strony mogą odnajdywać i łączyć się z ich własnej sieci perspektywy. Punkt spotkania jest nazywany "Połączenie hybrydowe" w tej i innej dokumentacji, w interfejsach API, a także w witrynie Azure portal. Punkt końcowy usługi połączenia hybrydowe jest określany jako "usługa" w pozostałej części tego artykułu.

Usługa umożliwia przekazywanie połączeń z gniazdem sieci Web oraz żądań i odpowiedzi HTTP(S).

Model interakcji opiera się na nomenklaturze ustanowionej przez wiele innych interfejsów API sieci. Istnieje odbiornik, który najpierw wskazuje gotowość do obsługi połączeń przychodzących, a następnie akceptuje je po ich przybyciu. Z drugiej strony klient łączy się z odbiornikiem, oczekując, że połączenie zostanie zaakceptowane do ustanowienia dwukierunkowej ścieżki komunikacji. "Connect", "Listen" i "Accept" to te same terminy, które można znaleźć w większości interfejsów API gniazda.

Każdy model komunikacji przekazywanej ma jedną ze stron podejmowania połączeń wychodzących do punktu końcowego usługi. To sprawia, że "odbiornik" również "klient" w użyciu potocznym, a także może powodować inne przeciążenia terminologii. Dokładna terminologia używana w przypadku połączeń hybrydowych jest następująca:

Programy po obu stronach połączenia są nazywane "klientami", ponieważ są klientami usługi. Klient, który czeka i akceptuje połączenia jest "odbiornik" lub mówi się, że w "roli odbiornika." Klient, który inicjuje nowe połączenie z odbiornikiem za pośrednictwem usługi, jest nazywany "nadawcą" lub jest w "roli nadawcy".

### <a name="listener-interactions"></a>Interakcje z odbiornikiem

Odbiornik ma pięć interakcji z usługą; wszystkie szczegóły przewodu są opisane w dalszej części tego artykułu w sekcji referencyjnej.

Komunikaty nasłuchij, zaakceptuj i zażądaj są odbierane z usługi. Operacje Odnawianie i Ping są wysyłane przez odbiornik.

#### <a name="listen-message"></a>Odsłuchij wiadomość

Aby wskazać gotowość do usługi, że odbiornik jest gotowy do akceptowania połączeń, tworzy wychodzące połączenie WebSocket. Uzgadnianie połączenia nosi nazwę połączenia hybrydowego skonfigurowanego w obszarze nazw przekazywania i token zabezpieczający, który nadaje tej nazwie prawo "Nasłuchiwanie".

Gdy WebSocket zostanie zaakceptowany przez usługę, rejestracja jest zakończona, a ustanowiony WebSocket jest utrzymywany przy życiu jako "kanał sterowania" do włączania wszystkich kolejnych interakcji. Usługa umożliwia maksymalnie 25 równoczesnych odbiorników dla jednego połączenia hybrydowego. Należy określić przydział dla elementów elementów czepianych.

W przypadku połączeń hybrydowych, jeśli istnieją dwa lub więcej aktywnych odbiorników, połączenia przychodzące są równoważone między nimi w kolejności losowej; sprawiedliwej dystrybucji jest podejmowana z jak najlepszym wysiłkiem.

#### <a name="accept-message"></a>Zaakceptuj wiadomość

Gdy nadawca otworzy nowe połączenie w usłudze, usługa wybiera i powiadamia jednego z aktywnych odbiorników w połączeniu hybrydowym. To powiadomienie jest wysyłane do odbiornika za poczęsny otwartego kanału sterowania jako komunikat JSON. Komunikat zawiera adres URL punktu końcowego WebSocket, z którym odbiornik musi się połączyć w celu zaakceptowania połączenia.

Adres URL może i musi być używany bezpośrednio przez odbiornik bez dodatkowej pracy.
Zakodowane informacje są ważne tylko przez krótki okres czasu, zasadniczo tak długo, jak nadawca jest gotów czekać na połączenie, które ma zostać ustanowione end-to-end. Maksymalna wartość zakładana to 30 sekund. Adres URL może być używany tylko dla jednej pomyślnej próby połączenia. Jak tylko połączenie WebSocket z adresem URL spotkania zostanie nawiązane, wszystkie dalsze działania na tej stronie WebSocket są przekazywane od i do nadawcy. Dzieje się tak bez interwencji lub interpretacji przez służby.

### <a name="request-message"></a>Prośba o wiadomość

Oprócz połączeń WebSocket odbiornik może również odbierać ramki żądań HTTP od nadawcy, jeśli ta funkcja jest jawnie włączona w połączeniu hybrydowym.

Detektory, które dołączają do połączeń hybrydowych z obsługą HTTP MUSI obsługiwać `request` gest. Odbiornik, który nie obsługuje `request` i w związku z tym powoduje powtarzające się błędy limitu czasu podczas podłączenia może być na czarnej liście przez usługę w przyszłości.

Metadane nagłówka ramki HTTP są tłumaczone na język JSON w celu prostszej obsługi przez platformę odbiornika, również dlatego, że biblioteki analizujące nagłówki HTTP są rzadsze niż analizatory JSON. Metadane HTTP, które są istotne tylko dla relacji między nadawcą a bramą HTTP przekazywania, w tym informacje o autoryzacji, nie są przekazywane dalej. Obiekty żądań HTTP są przesyłane w sposób przezroczysty jako ramki binarne WebSocket.

Odbiornik może odpowiadać na żądania HTTP przy użyciu równoważnego gestu odpowiedzi.

Przepływ żądania/odpowiedzi domyślnie używa kanału sterowania, ale można go "uaktualnić" do odrębnego spotkania WebSocket, gdy jest to wymagane. Różne połączenia WebSocket poprawić przepływność dla każdej konwersacji klienta, ale obciążają odbiornika więcej połączeń, które muszą być obsługiwane, które mogą nie być w stanie lekkich klientów.

Na kanale sterowania jednostki żądające i odpowiedzi są ograniczone do co najwyżej 64 kB. Metadane nagłówka HTTP są ograniczone do łącznej liczby 32 kB. Jeśli żądanie lub odpowiedź przekroczy ten próg, odbiornik MUSI uaktualnić do spotkania WebSocket przy użyciu gestu równoważnego do obsługi [Accept](#accept-message).

W przypadku żądań usługa decyduje, czy chcesz kierować żądania za kanał kontrolny. Obejmuje to, ale nie może być ograniczona do przypadków, gdy żądanie przekracza 64 kB (nagłówki plus treść) wprost lub jeśli żądanie jest wysyłane z ["chunked" kodowania transferu](https://tools.ietf.org/html/rfc7230#section-4.1) i usługa ma powód, aby oczekiwać, że żądanie przekracza 64kB lub odczytu żądania nie jest natychmiastowe. Jeśli usługa zdecyduje się dostarczyć żądanie za pośrednictwem spotkania, przekazuje tylko adres spotkania do odbiornika.
Odbiornik następnie musi ustanowić spotkanie WebSocket i usługa niezwłocznie dostarcza pełne żądanie, w tym organów na spotkanie WebSocket. Odpowiedź MUSI również korzystać z spotkania WebSocket.

W przypadku żądań, które przychodzą za pośrednictwem kanału sterowania, odbiornik decyduje, czy odpowiedzieć za pośrednictwem kanału sterowania lub za pośrednictwem spotkania. Usługa MUSI zawierać adres spotkania z każdym żądaniem kierowane przez kanał sterowania. Ten adres jest prawidłowy tylko do uaktualnienia z bieżącego żądania.

Jeśli odbiornik zdecyduje się uaktualnić, łączy się i niezwłocznie dostarcza odpowiedzi za pośrednictwem ustanowionego gniazda spotkania.

Po ustanowieniu spotkania WebSocket, odbiornik powinien zachować go do dalszej obsługi żądań i odpowiedzi od tego samego klienta. Usługa będzie utrzymywać WebSocket tak długo, jak długo będzie utrzymywało się połączenie gniazda HTTPS z nadawcą i będzie kierować wszystkie kolejne żądania od tego nadawcy za pośrednictwem obsługiwanego websocket. Jeśli odbiornik zdecyduje się upuścić spotkanie WebSocket z jego strony, usługa również upuścić połączenie z nadawcą, niezależnie od tego, czy kolejne żądanie może być już w toku.

#### <a name="renew-operation"></a>Odnów operację

Token zabezpieczający, który musi służyć do rejestrowania odbiornika i utrzymania kanału sterowania może wygasnąć, gdy odbiornik jest aktywny. Wygaśnięcie tokenu nie wpływa na bieżące połączenia, ale powoduje, że kanał sterowania ma zostać usunięty przez usługę w momencie wygaśnięcia lub wkrótce po jego wygaśnięciu. Operacja "renew" to komunikat JSON, który odbiornik może wysłać, aby zastąpić token skojarzony z kanałem sterowania, dzięki czemu kanał sterowania może być obsługiwany przez dłuższy czas.

#### <a name="ping-operation"></a>Operacja Ping

Jeśli kanał sterowania pozostaje bezczynny przez długi czas, pośrednicy w drodze, takich jak moduły równoważenia obciążenia lub NAT może upuścić połączenie TCP. Operacja "ping" pozwala uniknąć tego, wysyłając niewielką ilość danych na kanale, który przypomina wszystkim na trasie sieci, że połączenie ma być żywe, a także służy jako test "na żywo" dla odbiornika. Jeśli ping nie powiedzie się, kanał sterowania należy uznać za bezużyteczny i odbiornik powinien ponownie się połączyć.

### <a name="sender-interaction"></a>Interakcja z nadawcą

Nadawca ma dwie interakcje z usługą: łączy gniazdo sieci Web lub wysyła żądania za pośrednictwem protokołu HTTPS. Żądania nie mogą być wysyłane za pośrednictwem gniazda sieci Web z roli nadawcy.

#### <a name="connect-operation"></a>Operacja łączenia

Operacja "connect" otwiera WebSocket w usłudze, podając nazwę połączenia hybrydowego i (opcjonalnie, ale domyślnie wymagane) token zabezpieczający przyznający uprawnienie "Wyślij" w ciągu zapytania. Usługa następnie współdziała z odbiornikiem w sposób opisany wcześniej, a odbiornik tworzy połączenie spotkania, które jest połączone z tym WebSocket. Po zaakceptowaniu WebSocket, wszystkie dalsze interakcje na tym WebSocket są z podłączonym odbiornikiem.

#### <a name="request-operation"></a>Operacja żądania

W przypadku połączeń hybrydowych, dla których funkcja została włączona, nadawca może wysyłać w dużej mierze nieograniczone żądania HTTP do detektorów.

Z wyjątkiem tokenu dostępu przekazywania, który jest osadzony w ciągu zapytania lub w nagłówku HTTP żądania, Przekazywanie jest w pełni niewidoczne dla wszystkich operacji HTTP na adresie przekazywania i wszystkich sufiksów ścieżki adresu przekazywania, pozostawiając odbiornik w pełni pod kontrolą autoryzacji end-to-end, a nawet funkcje rozszerzenia HTTP, takie jak [CORS](https://www.w3.org/TR/cors/).

Autoryzacja nadawcy z punktem końcowym przekazywania jest domyślnie włączona, ale jest opcjonalna. Właściciel połączenia hybrydowego może zezwolić na anonimowych nadawców. Usługa przechwytuje, kontroluje i usuwa informacje o autoryzacji w następujący sposób:

1. Jeśli ciąg zapytania `sb-hc-token` zawiera wyrażenie, wyrażenie zawsze zostanie usunięte z ciągu zapytania. Zostanie on oceniony, jeśli autoryzacja przekaźnika jest włączona.
2. Jeśli nagłówki żądań `ServiceBusAuthorization` zawierają nagłówek, wyrażenie nagłówka zawsze zostanie usunięte z kolekcji nagłówka.
   Zostanie on oceniony, jeśli autoryzacja przekaźnika jest włączona.
3. Tylko wtedy, gdy autoryzacja przekazywania jest włączona, a nagłówki żądań zawierają `Authorization` nagłówek, a żadne z wcześniejszych wyrażeń nie jest obecny, nagłówek zostanie oceniony i usunięty. W przeciwnym `Authorization`razie jest zawsze przekazywane jako —jest.

Jeśli nie ma aktywnego odbiornika, usługa zwróci kod błędu 502 "Bad Gateway". Jeśli usługa nie wydaje się obsługiwać żądania, usługa zwróci 504 "Limit czasu bramy" po 60 sekundach.

### <a name="interaction-summary"></a>Podsumowanie interakcji

Wynikiem tego modelu interakcji jest to, że klient nadawcy wychodzi z uzgadniania z "czystą" websocketet, która jest podłączona do odbiornika i że nie wymaga dalszych preambuł lub przygotowania. Ten model umożliwia praktycznie każdej istniejącej implementacji klienta WebSocket łatwo skorzystać z usługi połączenia hybrydowe, dostarczając poprawnie skonstruowany adres URL do ich websocket warstwy klienta.

Połączenie spotkania WebSocket, które odbiornik uzyskuje za pośrednictwem interakcji accept jest również czyste i może być przekazane do dowolnej istniejącej implementacji serwera WebSocket z minimalną dodatkową abstrakcją, która rozróżnia operacje "akceptuj" na ich platformy lokalnych odbiorników sieci i połączeń hybrydowych zdalnych operacji "accept".

Model żądania/odpowiedzi HTTP nadaje nadawcy w dużej mierze nieograniczony obszar powierzchni protokołu HTTP z warstwą autoryzacji OPCJONALNE. Odbiornik pobiera wstępnie przeanalizowaną sekcję nagłówka żądania HTTP, którą można przywrócić do podrzędnego żądania HTTP lub obsłużyć tak, jak jest, z ramkami binarnymi z treściami HTTP. Odpowiedzi używają tego samego formatu. Interakcje z treścią żądania i odpowiedzi o mniejszej niż 64 KB mogą być obsługiwane za pośrednictwem jednego gniazda sieci Web udostępnionego dla wszystkich nadawców. Większe żądania i odpowiedzi mogą być obsługiwane przy użyciu modelu spotkania.

## <a name="protocol-reference"></a>Odwołanie do protokołu

W tej sekcji opisano szczegóły interakcji protokołu opisane wcześniej.

Wszystkie połączenia WebSocket są nawiązywały na porcie 443 jako uaktualnienie z protokołu HTTPS 1.1, który jest powszechnie abstrakcjonowany przez niektóre struktury WebSocket lub interfejsu API. Opis w tym miejscu jest zachowana implementacja neutralna, bez sugerowania określonych struktur.

### <a name="listener-protocol"></a>Protokół odbiornika

Protokół odbiornika składa się z dwóch gestów połączenia i trzech operacji wiadomości.

#### <a name="listener-control-channel-connection"></a>Połączenie kanału sterowania odbiornikiem

Kanał sterowania jest otwierany z utworzeniem połączenia WebSocket do:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

Jest `namespace-address` to w pełni kwalifikowana nazwa domeny obszaru nazw usługi Azure Relay, w którym jest nawiązywanie połączenia hybrydowego, zazwyczaj formularza. `{myname}.servicebus.windows.net`

Opcje parametru ciągu kwerendy są następujące.

| Parametr        | Wymagany | Opis
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Tak      | Dla roli odbiornika parametr musi być **sb-hc-action=listen**
| `{path}`         | Tak      | Ścieżka obszaru nazw zakodowanego w adresie URL wstępnie skonfigurowanego połączenia hybrydowego w celu zarejestrowania tego odbiornika. To wyrażenie jest dołączane `$hc/` do części ścieżki stałej.
| `sb-hc-token`    | Tak\*    | Odbiornik musi dostarczyć prawidłowy token współdzielonego dostępu magistrali usług zakodowany w adresie URL dla obszaru nazw lub połączenia hybrydowego, który przyznaje prawo **nasłuchiwania.**
| `sb-hc-id`       | Nie       | Ten opcjonalny identyfikator dostarczony przez klienta umożliwia śledzenie diagnostyczne typu end-to-end.

Jeśli połączenie WebSocket nie powiedzie się z powodu ścieżki połączenia hybrydowego nie jest zarejestrowany, nieprawidłowy lub brakujący token lub inny błąd, informacje zwrotne o błędzie jest dostarczany przy użyciu zwykłego modelu opinii o stanie HTTP 1.1. Opis stanu zawiera identyfikator śledzenia błędów, który można przekazać personelowi pomocy technicznej platformy Azure:

| Code | Błąd          | Opis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nie znaleziono      | Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowy adres URL jest zniekształcony.
| 401  | Brak autoryzacji   | Brakuje tokenu zabezpieczającego, jest on nieprawidłowo sformułowany lub nieprawidłowy.
| 403  | Forbidden      | Token zabezpieczający jest nieprawidłowy dla tej ścieżki dla tej akcji.
| 500  | Błąd wewnętrzny | Coś poszło nie tak w serwisie.

Jeśli połączenie WebSocket jest celowo zamykane przez usługę po jego początkowej konfiguracji, przyczyna tego działania jest przekazywana przy użyciu odpowiedniego kodu błędu protokołu WebSocket wraz z opisowym komunikatem o błędzie, który zawiera również identyfikator śledzenia. Usługa nie zostanie zamknięta kanał sterowania bez napotkania warunku błędu. Każde czyste zamknięcie jest kontrolowane przez klienta.

| Stan WS | Opis
| --------- | -------------------------------------------------------------------------------
| 1001      | Ścieżka połączenia hybrydowego została usunięta lub wyłączona.
| 1008      | Token zabezpieczający wygasł, w związku z czym zasady autoryzacji są naruszane.
| 1011      | Coś poszło nie tak w serwisie.

#### <a name="accept-handshake"></a>Akceptowanie uzgadniania

Powiadomienie "accept" jest wysyłane przez usługę do odbiornika za pośrednictwem wcześniej ustanowionego kanału sterowania jako komunikat JSON w ramce tekstowej WebSocket. Nie ma odpowiedzi na tę wiadomość.

Komunikat zawiera obiekt JSON o nazwie "accept", który definiuje następujące właściwości w tej chwili:

* **adres** — ciąg adresu URL, który ma być używany do ustanawiania WebSocket do usługi, aby zaakceptować połączenie przychodzące.
* **id** – unikatowy identyfikator tego połączenia. Jeśli identyfikator został dostarczony przez klienta nadawcy, jest to wartość dostarczona przez nadawcę, w przeciwnym razie jest to wartość wygenerowana przez system.
* **connectHeaders** — wszystkie nagłówki HTTP, które zostały dostarczone do punktu końcowego przekazywania przez nadawcę, który zawiera również Sec-WebSocket-Protocol i Sec-WebSocket-Extensions nagłówki.

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

Adres URL adresu podany w wiadomości JSON jest używany przez odbiornik do ustanowienia WebSocket do akceptowania lub odrzucania gniazda nadawcy.

##### <a name="accepting-the-socket"></a>Akceptacja gniazda

Aby zaakceptować, odbiornik ustanawia połączenie WebSocket do podanego adresu.

Jeśli komunikat "accept" `Sec-WebSocket-Protocol` zawiera nagłówek, oczekuje się, że odbiornik akceptuje WebSocket tylko wtedy, gdy obsługuje ten protokół. Ponadto ustawia nagłówek jako WebSocket jest ustanawiana.

To samo dotyczy `Sec-WebSocket-Extensions` nagłówka. Jeśli struktura obsługuje rozszerzenie, należy ustawić nagłówek do odpowiedzi po stronie `Sec-WebSocket-Extensions` serwera uzgadniania wymagane dla rozszerzenia.

Adres URL musi być używany jako — jest do ustanawiania gniazda akceptowania, ale zawiera następujące parametry:

| Parametr      | Wymagany | Opis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Tak      | W celu zaakceptowania gniazda parametr musi być`sb-hc-action=accept`
| `{path}`       | Tak      | (patrz następujący akapit)
| `sb-hc-id`     | Nie       | Zobacz poprzedni opis **identyfikatora**.

`{path}`jest ścieżką obszaru nazw zakodowanego w adresie URL wstępnie skonfigurowanego połączenia hybrydowego, na której można zarejestrować tego odbiornika. To wyrażenie jest dołączane `$hc/` do części ścieżki stałej.

Wyrażenie `path` może zostać rozszerzone o sufiks i wyrażenie ciągu zapytania, które następuje po zarejestrowanej nazwie po rozdzielaniu ukośnika do przodu.
Dzięki temu klient nadawcy do przekazywania argumentów wysyłki do odbiornika akceptującego, gdy nie jest możliwe dołączenie nagłówków HTTP. Oczekuje się, że struktura odbiornika analizuje część ścieżki stałej i zarejestrowanej nazwy ze ścieżki i sprawia, że `sb-`reszta, ewentualnie bez żadnych argumentów ciągu kwerendy poprzedzonych przez , dostępne dla aplikacji do podejmowania decyzji, czy zaakceptować połączenie.

Aby uzyskać więcej informacji, zobacz następującą sekcję "Protokół nadawcy".

Jeśli wystąpi błąd, usługa może odpowiedzieć w następujący sposób:

| Code | Błąd          | Opis
| ---- | -------------- | -----------------------------------
| 403  | Forbidden      | Adres URL jest nieprawidłowy.
| 500  | Błąd wewnętrzny | Coś poszło nie tak w serwisie

 Po nawiązaniu połączenia serwer wyłącza websocket po zamknięciu websocket nadawcy lub o następującym stanie:

| Stan WS | Opis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Klient nadawcy zamyka połączenie.                                    |
| 1001      | Ścieżka połączenia hybrydowego została usunięta lub wyłączona.                        |
| 1008      | Token zabezpieczający wygasł, w związku z czym zasady autoryzacji są naruszane. |
| 1011      | Coś poszło nie tak w serwisie.                                            |

##### <a name="rejecting-the-socket"></a>Odrzucanie gniazda

 Odrzucenie gniazda po sprawdzeniu `accept` wiadomości wymaga podobnego uzgadniania, aby kod stanu i opis stanu informujące o przyczynie odrzucenia mogły wrócić do nadawcy.

 Wybór projektu protokołu w tym miejscu jest użycie WebSocket uzgadniania (który jest przeznaczony do końca w stanie zdefiniowanego błędu), dzięki czemu implementacje klienta odbiornika mogą nadal polegać na kliencie WebSocket i nie trzeba zatrudniać dodatkowego, gołego klienta HTTP.

 Aby odrzucić gniazdo, klient pobiera identyfikator `accept` URI adresu z wiadomości i dołącza do niego dwa parametry ciągu zapytania, w następujący sposób:

| Param                   | Wymagany | Opis                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusKoduj        | Tak      | Numeryczny kod stanu HTTP.                |
| sb-hc-statusOpiryk | Tak      | Człowiek czytelny powód odrzucenia. |

Wynikowy identyfikator URI jest następnie używany do ustanawiania połączenia WebSocket.

Podczas poprawnego wypełniania ten uzgadnianie celowo kończy się niepowodzeniem z kodem błędu HTTP 410, ponieważ nie został ustanowiony websocket. Jeśli coś pójdzie nie tak, następujące kody opisują błąd:

| Code | Błąd          | Opis                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Forbidden      | Adres URL jest nieprawidłowy.                |
| 500  | Błąd wewnętrzny | Coś poszło nie tak w serwisie. |

#### <a name="request-message"></a>Prośba o wiadomość

Wiadomość `request` jest wysyłana przez usługę do odbiornika za pomocą kanału sterowania. Ten sam komunikat jest również wysyłany przez spotkanie WebSocket po ustanowieniu.

Składa `request` się z dwóch części: nagłówka i binarnej ramki(-ów) korpusu(-ów).
Jeśli nie ma obiektu, ramki ciała są pomijane. Wskaźnik, czy treść jest obecny jest `body` właściwością logiczną w komunikacie żądania.

W przypadku żądania z treścią żądania struktura może wyglądać następująco:

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

Odbiornik MUSI obsługiwać odbieranie treści żądania podzielone na wiele ramek binarnych (patrz [WebSocket fragmenty](https://tools.ietf.org/html/rfc6455#section-5.4)).
Żądanie kończy się po odebraniu ramki binarnej z zestawem flag FIN.

W przypadku żądania bez treści istnieje tylko jedna ramka tekstowa.

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

* **adres** — ciąg URI. Jest to adres spotkania do użycia dla tego żądania. Jeśli żądanie przychodzące jest większe niż 64 kB, pozostała część tej wiadomości pozostaje pusta, a klient `accept` MUSI zainicjować uzgadnianie spotkania równoważne operacji opisanej poniżej. Usługa następnie umieścić kompletny `request` na ustalonym gnieździe sieci Web. Jeśli można oczekiwać, że odpowiedź przekroczy 64 kB, odbiornik MUSI również zainicjować uzgadnianie spotkania, a następnie przenieść odpowiedź za pośrednictwem ustalonego gniazda sieci Web.
* **id** – ciąg. Unikatowy identyfikator dla tego żądania.
* **requestHeaders** — ten obiekt zawiera wszystkie nagłówki HTTP, które zostały dostarczone do punktu końcowego przez nadawcę, z wyjątkiem informacji o autoryzacji, jak wyjaśniono [powyżej,](#request-operation)i nagłówki, które ściśle odnoszą się do połączenia z bramą. W szczególności wszystkie nagłówki zdefiniowane lub zarezerwowane w [RFC7230](https://tools.ietf.org/html/rfc7230), z wyjątkiem `Via`, są usuwane i nie przekazywane dalej:

  * `Connection`(RFC7230, sekcja 6.1)
  * `Content-Length`(RFC7230, sekcja 3.3.2)
  * `Host`(RFC7230, sekcja 5.4)
  * `TE`(RFC7230, sekcja 4.3)
  * `Trailer`(RFC7230, sekcja 4.4)
  * `Transfer-Encoding`(RFC7230, sekcja 3.3.1)
  * `Upgrade`(RFC7230, sekcja 6.7)
  * `Close`(RFC7230, sekcja 8.1)

* **requestTarget** – ciąg. Ta właściwość zawiera ["Request Target" (RFC7230, sekcja 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) żądania. Obejmuje to część ciągu zapytania, która `sb-hc-` jest pozbawiona wszystkich poprzedzonych parametrów.
* **metoda** - ciąg. Jest to metoda żądania, zgodnie z [RFC7231, sekcja 4](https://tools.ietf.org/html/rfc7231#section-4). Metoda `CONNECT` NIE MOŻE BYĆ STOSOWANA.
* **ciało** – logiczne. Wskazuje, czy następuje jedna lub więcej ramek treści binarnej.

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

Odbiornik MUSI odpowiedzieć. Powtarzające się niepowodzenie odpowiadania na żądania przy zachowaniu połączenia może spowodować, że odbiornik zostanie umieszczony na czarnej liście.

Odpowiedzi mogą być wysyłane w dowolnej kolejności, ale każde żądanie musi zostać odpowiedzieć w ciągu 60 sekund lub dostawa zostanie zgłoszona jako nieudana. 60-sekundowy termin jest liczony do momentu otrzymania `response` ramki przez służby. Bieżąca odpowiedź z wieloma ramkami binarnymi nie może stać się bezczynna przez więcej niż 60 sekund lub zostanie zakończona.

Jeśli żądanie zostanie odebrane za pośrednictwem kanału kontrolnego, odpowiedź MUSI być wysłana na kanale sterowania, z którego żądanie zostało odebrane, albo musi zostać wysłana za pośrednictwem kanału spotkania.

Odpowiedź jest obiektem JSON o nazwie "odpowiedź". Zasady obsługi zawartości treści są dokładnie `request` takie jak w `body` przypadku wiadomości i oparte na właściwości.

* **requestId** – ciąg. Wymagane. Wartość `id` właściwości wiadomości, na `request` którą jest odpowiedzi.
* **statusCode** – numer. Wymagane. numeryczny kod stanu HTTP, który wskazuje wynik powiadomienia. Wszystkie kody stanu [RFC7231, Sekcja 6](https://tools.ietf.org/html/rfc7231#section-6) są dozwolone, z wyjątkiem [502 "Bad Gateway"](https://tools.ietf.org/html/rfc7231#section-6.6.3) i [504 "Limit czasu bramy"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** - ciąg. Opcjonalne. Fraza przyczyny kodu stanu HTTP na [RFC7230, sekcja 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** — nagłówki HTTP, które mają być ustawione w zewnętrznej odpowiedzi HTTP.
  Podobnie jak `request`w przypadku , RFC7230 zdefiniowane nagłówki NIE MOGĄ BYĆ UŻYWANE.
* **ciało** – logiczne. Wskazuje, czy ramki(-y) binarne(-y) są następujące(-e).

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

##### <a name="responding-via-rendezvous"></a>Odpowiadanie za pośrednictwem spotkania

W przypadku odpowiedzi przekraczających 64 kB odpowiedź MUSI być dostarczona za pośrednictwem gniazda spotkania. Ponadto, jeśli żądanie przekracza 64 kB, a `request` tylko zawiera pole adresu, gniazdo spotkania musi `request`zostać ustanowione w celu uzyskania . Po ustanowieniu gniazda spotkania odpowiedzi na odpowiedniego klienta i kolejne żądania od tego odpowiedniego klienta muszą być dostarczane za pośrednictwem gniazda spotkania, gdy będzie ono trwać.

Adres `address` URL `request` w musi być używany jako — jest do ustanawiania gniazda spotkania, ale zawiera następujące parametry:

| Parametr      | Wymagany | Opis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Tak      | W celu zaakceptowania gniazda parametr musi być`sb-hc-action=request`

Jeśli wystąpi błąd, usługa może odpowiedzieć w następujący sposób:

| Code | Błąd           | Opis
| ---- | --------------- | -----------------------------------
| 400  | Nieprawidłowe żądanie | Nierozpoznana akcja lub adres URL nieprawidłowy.
| 403  | Forbidden       | Adres URL wygasł.
| 500  | Błąd wewnętrzny  | Coś poszło nie tak w serwisie

 Po nawiązaniu połączenia serwer wyłącza websocket po zamknięciu gniazda HTTP klienta lub o następującym stanie:

| Stan WS | Opis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Klient nadawcy zamyka połączenie.                                    |
| 1001      | Ścieżka połączenia hybrydowego została usunięta lub wyłączona.                        |
| 1008      | Token zabezpieczający wygasł, w związku z czym zasady autoryzacji są naruszane. |
| 1011      | Coś poszło nie tak w serwisie.                                            |


#### <a name="listener-token-renewal"></a>Odnowienie tokenu odbiornika

Gdy token odbiornika ma wygasnąć, można go zastąpić, wysyłając wiadomość w ramce tekstowej do usługi za pośrednictwem ustalonego kanału sterowania. Komunikat zawiera obiekt JSON `renewToken`o nazwie , który definiuje następującą właściwość w tej chwili:

* **token** — prawidłowy token udostępnionego dostępu usługi magistrali usług zakodowany w adresie URL dla obszaru nazw lub połączenia hybrydowego, który przyznaje prawo **nasłuchiwania.**

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Jeśli sprawdzanie poprawności tokenu nie powiedzie się, odmowa dostępu, a usługa w chmurze zamyka kanał sterowania WebSocket z błędem. W przeciwnym razie nie ma odpowiedzi.

| Stan WS | Opis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Token zabezpieczający wygasł, w związku z czym zasady autoryzacji są naruszane. |

### <a name="web-socket-connect-protocol"></a>Protokół połączania gniazda sieci Web

Protokół nadawcy jest skutecznie identyczny ze sposobem ustanowienia odbiornika.
Celem jest maksymalna przejrzystość dla end-to-end WebSocket. Adres do łączenia się jest taki sam jak dla odbiornika, ale "akcja" różni się i token potrzebuje innego uprawnienia:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

_Adres obszaru nazw_ jest w pełni kwalifikowaną nazwą domeny obszaru nazw usługi Azure Relay, w którym jest nawiązywanie połączenia hybrydowego, zazwyczaj formularza. `{myname}.servicebus.windows.net`

Żądanie może zawierać dowolne dodatkowe nagłówki HTTP, w tym te zdefiniowane przez aplikację. Wszystkie dostarczone nagłówki przepływu do odbiornika i `connectHeader` można znaleźć na obiekcie komunikatu kontroli **accept.**

Opcje parametrów ciągu kwerendy są następujące:

| Param          | Wymagana? | Opis
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Tak       | Dla roli nadawcy parametr musi `sb-hc-action=connect`być .
| `{path}`       | Tak       | (patrz następujący akapit)
| `sb-hc-token`  | Tak\*     | Odbiornik musi dostarczyć prawidłowy token udostępnionego dostępu magistrali usług zakodowany w adresie URL dla obszaru nazw lub połączenia hybrydowego, który nadaje prawo **wysyłania.**
| `sb-hc-id`     | Nie        | Opcjonalny identyfikator, który umożliwia śledzenie diagnostyczne end-to-end i jest udostępniany odbiornikowi podczas uzgadniania akceptowania.

 Jest `{path}` to ścieżka obszaru nazw zakodowana w adresie URL wstępnie skonfigurowanego połączenia hybrydowego, na której ma być rejestrowana ta odbiornik. Wyrażenie `path` można rozszerzyć za pomocą sufiksu i wyrażenia ciągu zapytania, aby komunikować się dalej. Jeśli połączenie hybrydowe jest `hyco`zarejestrowane `path` w `hyco/suffix?param=value&...` ścieżce , wyrażenie może być następuje parametry ciągu zapytania zdefiniowane w tym miejscu. Pełne wyrażenie może być wówczas następujące:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

Wyrażenie `path` jest przekazywane do odbiornika w adresie URI zawarte w komunikacie sterującym "accept".

Jeśli połączenie WebSocket nie powiedzie się z powodu ścieżki połączenia hybrydowego nie są zarejestrowane, nieprawidłowy lub brakujący token lub inny błąd, informacje zwrotne o błędzie jest dostarczany przy użyciu zwykłego modelu opinii o stanie HTTP 1.1. Opis stanu zawiera identyfikator śledzenia błędów, który można przekazać personelowi pomocy technicznej platformy Azure:

| Code | Błąd          | Opis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nie znaleziono      | Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowy adres URL jest zniekształcony.
| 401  | Brak autoryzacji   | Brakuje tokenu zabezpieczającego, jest on nieprawidłowo sformułowany lub nieprawidłowy.
| 403  | Forbidden      | Token zabezpieczający jest nieprawidłowy dla tej ścieżki i dla tej akcji.
| 500  | Błąd wewnętrzny | Coś poszło nie tak w serwisie.

Jeśli połączenie WebSocket zostanie celowo zamknięte przez usługę po jego początkowej konfiguracji, przyczyna tego zdarzenia jest przekazywana przy użyciu odpowiedniego kodu błędu protokołu WebSocket wraz z opisowym komunikatem o błędzie, który zawiera również identyfikator śledzenia .

| Stan WS | Opis
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Odbiornik wyłączył gniazdo.
| 1001      | Ścieżka połączenia hybrydowego została usunięta lub wyłączona.
| 1008      | Token zabezpieczający wygasł, w związku z czym zasady autoryzacji są naruszane.
| 1011      | Coś poszło nie tak w serwisie.

### <a name="http-request-protocol"></a>Protokół żądania HTTP

Protokół żądania HTTP umożliwia dowolne żądania HTTP, z wyjątkiem uaktualnień protokołów.
Żądania HTTP są wskazywane na adres środowiska wykonawczego jednostki, bez $hc infix, który jest używany dla połączeń hybrydowych klientów WebSocket.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

_Adres obszaru nazw_ jest w pełni kwalifikowaną nazwą domeny obszaru nazw usługi Azure Relay, w którym jest nawiązywanie połączenia hybrydowego, zazwyczaj formularza. `{myname}.servicebus.windows.net`

Żądanie może zawierać dowolne dodatkowe nagłówki HTTP, w tym te zdefiniowane przez aplikację. Wszystkie dostarczone nagłówki, z wyjątkiem tych bezpośrednio zdefiniowanych w RFC7230 (zobacz [komunikat żądania)](#Request message)przepływu do odbiornika i można znaleźć na `requestHeader` obiekcie komunikatu **żądania.**

Opcje parametrów ciągu kwerendy są następujące:

| Param          | Wymagana? | Opis
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Tak\*     | Odbiornik musi dostarczyć prawidłowy token udostępnionego dostępu magistrali usług zakodowany w adresie URL dla obszaru nazw lub połączenia hybrydowego, który nadaje prawo **wysyłania.**

Token może być również przenoszony w nagłówku `ServiceBusAuthorization` lub `Authorization` HTTP. Token można pominąć, jeśli połączenie hybrydowe jest skonfigurowany do zezwalania na anonimowe żądania.

Ponieważ usługa skutecznie działa jako serwer proxy, nawet jeśli nie jako `Via` prawdziwy serwer proxy HTTP, dodaje nagłówek lub adnotuje istniejący `Via` nagłówek zgodny z [RFC7230, Sekcja 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
Usługa dodaje nazwę hosta obszaru `Via`nazw przekaźnika do pliku .

| Code | Komunikat  | Opis                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | Żądanie zostało obsłużone przez co najmniej jednego odbiornika.  |
| 202  | Zaakceptowane | Żądanie zostało zaakceptowane przez co najmniej jednego odbiornika. |

Jeśli wystąpi błąd, usługa może odpowiedzieć w następujący sposób. Czy odpowiedź pochodzi z usługi lub od odbiornika można zidentyfikować `Via` za pośrednictwem obecności nagłówka. Jeśli nagłówek jest obecny, odpowiedź pochodzi od odbiornika.

| Code | Błąd           | Opis
| ---- | --------------- |--------- |
| 404  | Nie znaleziono       | Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowy adres URL jest zniekształcony.
| 401  | Brak autoryzacji    | Brakuje tokenu zabezpieczającego, jest on nieprawidłowo sformułowany lub nieprawidłowy.
| 403  | Forbidden       | Token zabezpieczający jest nieprawidłowy dla tej ścieżki i dla tej akcji.
| 500  | Błąd wewnętrzny  | Coś poszło nie tak w serwisie.
| 503  | Zła brama     | Nie można przekierować żądania do żadnego odbiornika.
| 504  | Limit czasu bramy | Żądanie zostało skierowane do odbiornika, ale odbiornik nie potwierdził odbioru w wymaganym czasie.

## <a name="next-steps"></a>Następne kroki

* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)
* [Tworzenie przestrzeni nazw](relay-create-namespace-portal.md)
* [Rozpoczęcie pracy z programem .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Wprowadzenie do programu Node](relay-hybrid-connections-node-get-started.md)
