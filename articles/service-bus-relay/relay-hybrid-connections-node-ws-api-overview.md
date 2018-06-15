---
title: Omówienie przekaźnika usługi Azure API węzła | Dokumentacja firmy Microsoft
description: Przegląd interfejsu API węzła przekazywania
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 696e3f77a283cc31d3c8f6007a839480ae8eb984
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019464"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Przegląd interfejsu API węzła połączeń hybrydowych przekazywania

## <a name="overview"></a>Przegląd

[ `hyco-ws` ](https://www.npmjs.com/package/hyco-ws) Pakiet węzła dla połączeń hybrydowych przekazywania Azure jest oparty na i rozszerza ["ws"](https://www.npmjs.com/package/ws) pakietu NPM. Ten pakiet ponownie Eksportuje wszystkie eksportu pakietu, aby podstawowa i dodaje nowe eksportu, które umożliwiają integrację z funkcją połączeń hybrydowych usługi przekaźnika usługi Azure. 

Istniejące aplikacje który `require('ws')` można użyć tego pakietu z `require('hyco-ws')` zamiast tego, który umożliwia także scenariuszy hybrydowych, w których aplikacja może nasłuchiwać połączeń protokołu WebSocket lokalnie z "wewnątrz zapory" i via połączeń hybrydowych było możliwe, wszystko na tym samym czasie.
  
## <a name="documentation"></a>Dokumentacja

Interfejsy API są [udokumentowane w pakiecie głównej ws](https://github.com/websockets/ws/blob/master/doc/ws.md). W tym artykule opisano, jak ten pakiet różni się od tej linii bazowej. 

Główne różnice między pakiet podstawowy i to 'hyco-ws' jest dodaje nową klasę serwera wyeksportowane za pomocą `require('hyco-ws').RelayedServer`i kilka metod pomocniczych.

### <a name="package-helper-methods"></a>Metody pomocnicze pakietu

Brak dostępnych kilka metod narzędzie podczas eksportowania pakietu, który może odwoływać się w następujący sposób:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Metody pomocnicze są do użycia z tym pakietem, ale można również przez serwer węzła umożliwiających klientów sieci web lub urządzenia do utworzenia odbiorników lub nadawcy. Serwer używa tych metod, przekazując ich identyfikatorów URI osadzaj tokenów krótkim okresie. Te identyfikatory URI mogą również z typowych stosy protokołu WebSocket, które nie obsługują nagłówków HTTP ustawienie na uzgadnianie protokołu WebSocket. Osadzanie tokeny autoryzacji w identyfikatorze URI jest obsługiwana przede wszystkim dotyczące tych scenariuszy użycia zewnętrznej biblioteki. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Tworzy prawidłowego odbiornika połączenia hybrydowego przekazywania Azure identyfikatora URI dla danej przestrzeni nazw i ścieżkę. Następnie można ten identyfikator URI przy użyciu wersji przekazywania klasy WebSocketServer.

- `namespaceName`(wymagana) — nazwa kwalifikowana domeny przekazywania Azure przestrzeni nazw do użycia.
- `path`(wymagana) — nazwa istniejącego połączenia hybrydowego przekazywania Azure w tej przestrzeni nazw.
- `token`(opcjonalnie) - wcześniej wydanych przekazywanie tokenu który jest osadzony w odbiornika identyfikatora URI (zobacz poniższy przykład).
- `id`(opcjonalnie) — identyfikator śledzenia, który umożliwia śledzenia end-to-end diagnostyki żądań.

`token` Wartość jest opcjonalna i należy używać tylko w przypadku nie jest możliwe na wysyłanie nagłówków HTTP wraz z uzgadniania protokołu WebSocket, tak jak w przypadku stosu protokołu WebSocket W3C.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Tworzy prawidłowy wysyłania połączenia hybrydowego przekazywania Azure identyfikatora URI dla danej przestrzeni nazw i ścieżkę. Tego identyfikatora URI można używać z dowolnego klienta protokołu WebSocket.

- `namespaceName`(wymagana) — nazwa kwalifikowana domeny przekazywania Azure przestrzeni nazw do użycia.
- `path`(wymagana) — nazwa istniejącego połączenia hybrydowego przekazywania Azure w tej przestrzeni nazw.
- `token`(opcjonalnie) - wcześniej wydanych przekazywanie tokenu który jest osadzony w wysyłania identyfikatora URI (zobacz poniższy przykład).
- `id`(opcjonalnie) — identyfikator śledzenia, który umożliwia śledzenia end-to-end diagnostyki żądań.

`token` Wartość jest opcjonalna i należy używać tylko w przypadku nie jest możliwe na wysyłanie nagłówków HTTP wraz z uzgadniania protokołu WebSocket, tak jak w przypadku stosu protokołu WebSocket W3C.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Tworzy token Azure przekazywania dostępu sygnatury dostępu Współdzielonego dla danego docelowy identyfikator URI, reguła sygnatury dostępu Współdzielonego i SAS reguły klucza prawidłowe dla danej liczby sekund lub godzinę z bieżącą pominięcie argumentu wygaśnięcia.

- `uri`(wymagane) - URI, dla którego ma zostać wystawiony token. Identyfikator URI jest znormalizowane do użycia schemat HTTP i informacji o ciągu zapytania jest usuwany.
- `ruleName`(wymagane) - SAS reguł nazwy dla jednostki reprezentowanej przez dany identyfikator URI lub przestrzeni nazw reprezentowany przez identyfikator URI części hosta.
- `key`(wymagane) - prawidłowego klucza dla reguły sygnatury dostępu Współdzielonego. 
- `expirationSeconds`(opcjonalnie) — liczba sekund do wygaśnięcia wygenerowany token. Jeśli nie zostanie określony, wartość domyślna to 1 godzina (3600).

Wystawiony token przyznaje praw skojarzonych z określoną regułą SAS w danym czasie trwania.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Ta metoda jest funkcjonalnym odpowiednikiem `createRelayToken` metody opisane wcześniej, ale zwraca token poprawnie dołączone do identyfikatora URI danych wejściowych.

### <a name="class-wsrelayedserver"></a>Klasa ws. RelayedServer

`hycows.RelayedServer` Klasa stanowi alternatywę dla `ws.Server` klasy, która nie będzie nasłuchiwać w sieci lokalnej, ale delegatów nasłuchiwanie usługi przekaźnika usługi Azure.

Przede wszystkim kontraktu zgodny, oznacza to, że istniejących aplikacji przy użyciu są dwie klasy `ws.Server` klasy można łatwo zmienić korzysta z obsługiwanych przez przekaźnik wersji. Główne różnice są w Konstruktorze i dostępnych opcji.

#### <a name="constructor"></a>Konstruktor  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

`RelayedServer` Konstruktor obsługuje inny zestaw argumentów niż `Server`, ponieważ nie jest autonomiczny odbiornik, lub czy można osadzone w istniejących ramach odbiornika HTTP. Dostępne są również mniej opcje ponieważ zarządzanie WebSocket przede wszystkim jest delegowane do usługi przekazywania.

Argumenty konstruktora:

- `server`(wymagane) - pełni kwalifikowanego identyfikatora URI, nazwy połączenia hybrydowego do nasłuchiwania, skonstruowane zazwyczaj za pomocą metody pomocnika WebSocket.createRelayListenUri().
- `token`(wymagana) — ten argument zawiera ciąg tokenu wcześniej wydanych lub funkcja wywołania zwrotnego, który można wywołać w celu uzyskania tokenu ciągu. Opcję jest preferowane, ponieważ umożliwia ona odnowienia tokenu.

#### <a name="events"></a>Zdarzenia

`RelayedServer`wystąpienia Emituj trzy zdarzenia, które umożliwiają obsługę żądań przychodzących, nawiązywać połączenia i wykrywania błędów. Należy zasubskrybować `connect` zdarzeń do obsługi wiadomości. 

##### <a name="headers"></a>nagłówki

```JavaScript 
function(headers)
```

`headers` Zdarzenie jest wywoływane przed połączenia przychodzącego jest akceptowany, włączenie modyfikacji nagłówków, aby wysłać do klienta. 

##### <a name="connection"></a>połączenie

```JavaScript
function(socket)
```

Emitowane po zaakceptowaniu nowego połączenia obiektu WebSocket. Obiekt jest typu `ws.WebSocket`, identyczny z pakietem podstawowej.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Jeśli serwer źródłowy emituje błąd, jest przekazywany w tym miejscu.  

#### <a name="helpers"></a>Pomocnicy

Aby uprościć uruchomienie serwera obsługiwanych przez przekaźnik i natychmiast subskrybowanie połączeń przychodzących, pakiet ujawnia funkcji pomocnika proste, która jest również używana w przykładach w następujący sposób:

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Ta metoda wywołuje konstruktor do utworzenia nowego wystąpienia RelayedServer i następnie subskrybuje dostarczone wywołanie zwrotne w zdarzeniu "połączenia".
 
##### <a name="relayedconnect"></a>relayedConnect

Po prostu dublowania `createRelayedServer` pomocnika w funkcji `relayedConnect` tworzy połączenie klienta i subskrybuje "open" zdarzenia w wynikowej gniazda.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat przekaźnika usługi Azure, odwiedź te linki:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Interfejsy API dostępne przekazywania](relay-api-overview.md)
