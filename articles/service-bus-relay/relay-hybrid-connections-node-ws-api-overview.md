---
title: Omówienie interfejsów API węzła usługi Azure Relay | Dokumentacja firmy Microsoft
description: Omówienie interfejsu API węzła przekazywania
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 794e797e504d6064c13ffe0a4ed131e668d86e97
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64699397"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Przekazywania Przegląd węzła interfejsowi API połączeń hybrydowych

## <a name="overview"></a>Omówienie

[ `hyco-ws` ](https://www.npmjs.com/package/hyco-ws) Pakiet Node dla połączeń hybrydowych usługi Azure Relay działa w oparciu i rozszerza ["ws"](https://www.npmjs.com/package/ws) pakietów Menedżera NPM. Ten pakiet ponownie eksportuje wszystkich eksporty tego samego podstawowego pakietu i dodaje nowe eksportu, które umożliwiają integrację z funkcją połączeń hybrydowych usługi Azure Relay. 

Istniejące aplikacje, `require('ws')` mogą używać tego pakietu przy użyciu `require('hyco-ws')` zamiast tego, co pozwala także scenariusze hybrydowe, w których aplikacja może nasłuchiwać połączeń protokołu WebSocket przy użyciu połączeń hybrydowych i lokalnie z "wewnątrz zapory" wszystko na tym samym czasie.
  
## <a name="documentation"></a>Dokumentacja

Interfejsy API są [udokumentowane w pakiecie głównej ws](https://github.com/websockets/ws/blob/master/doc/ws.md). W tym artykule opisano, jak ten pakiet różni się od tej linii bazowej. 

Podstawowe różnice między pakiet podstawowy i to "hyco-ws" jest dodaje nową klasę serwera eksportowania za pomocą `require('hyco-ws').RelayedServer`oraz kilka metod pomocniczych.

### <a name="package-helper-methods"></a>Metody pomocnika pakietu

Brak dostępnych kilka metod narzędzie Eksportuj pakiet, którego można się odwoływać w następujący sposób:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Metody pomocnicze są przeznaczone do użytku z tym pakietem, ale można również przez serwer węzłów włączania klientów sieci web lub urządzenia do tworzenia obiektów nasłuchujących lub nadawcy. Serwer używa tych metod, przekazując je identyfikatory URI, które krótkotrwałe tokeny osadzania. Te identyfikatory URI mogą również za pomocą wspólnego stosów protokołu WebSocket, które nie obsługują nagłówków HTTP ustawienie uzgadnianie protokołu WebSocket. Osadzanie tokenach autoryzacji w identyfikatorze URI jest obsługiwane głównie dla tych scenariuszy użycia biblioteki zewnętrznej. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Tworzy prawidłowy odbiornika połączenie hybrydowe usługi Azure Relay identyfikator URI dla danej przestrzeni nazw i ścieżkę. Następnie można ten identyfikator URI przekazywania wersję klasy WebSocketServer.

- `namespaceName` (wymagane) — nazwa kwalifikowana domeny przestrzeni nazw usługi Azure Relay do użycia.
- `path` (wymagane) — nazwa istniejącego połączenia hybrydowego przekaźnika usługi Azure w tej przestrzeni nazw.
- `token` (opcjonalnie) — wcześniej wydanych przekazywania tokenu dostępu, zostanie osadzony w odbiornika identyfikatora URI (zobacz poniższy przykład).
- `id` (opcjonalnie) — identyfikator śledzenia, który umożliwia śledzenia end-to-end diagnostyki żądań.

`token` Wartość jest opcjonalna i powinna służyć wyłącznie, gdy nie jest możliwe do wysyłania nagłówków HTTP wraz z uzgadniania protokołu WebSocket, tak jak w przypadku ze stosem W3C WebSocket.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Tworzy prawidłowy wysyłania połączenie hybrydowe usługi Azure Relay identyfikatora URI do danej przestrzeni nazw i ścieżkę. Ten identyfikator URI może służyć za pomocą dowolnego klienta protokołu WebSocket.

- `namespaceName` (wymagane) — nazwa kwalifikowana domeny przestrzeni nazw usługi Azure Relay do użycia.
- `path` (wymagane) — nazwa istniejącego połączenia hybrydowego przekaźnika usługi Azure w tej przestrzeni nazw.
- `token` (opcjonalnie) — wcześniej wydanych przekazywania tokenu dostępu, zostanie osadzony w wysyłanie identyfikatora URI (zobacz poniższy przykład).
- `id` (opcjonalnie) — identyfikator śledzenia, który umożliwia śledzenia end-to-end diagnostyki żądań.

`token` Wartość jest opcjonalna i powinna służyć wyłącznie, gdy nie jest możliwe do wysyłania nagłówków HTTP wraz z uzgadniania protokołu WebSocket, tak jak w przypadku ze stosem W3C WebSocket.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Tworzy token usługi Azure Relay sygnatury dostępu współdzielonego (SAS) dla danego obiektu docelowego identyfikatora URI, reguły sygnatury dostępu Współdzielonego i klucz sygnatury dostępu Współdzielonego reguły, która obowiązuje dla danej liczby sekund lub bieżącą godzinę w przypadku pominięcia argumentu wygaśnięcia.

- `uri` (wymagana): identyfikator URI, dla którego ma zostać wystawiony token. Identyfikator URI jest znormalizować do użycia schemat HTTP i informacji o ciągu zapytania jest usuwany.
- `ruleName` (wymagana): nazwa dla jednostki reprezentowanej przez dany identyfikator URI lub przestrzeni nazw, reprezentowany przez identyfikator URI części hosta są reguły dla sygnatury dostępu Współdzielonego.
- `key` (wymagane) - prawidłowy klucz dla reguły sygnatury dostępu Współdzielonego. 
- `expirationSeconds` (opcjonalnie) — liczba sekund do wygaśnięcia wygenerowany token. Jeśli nie zostanie określony, wartość domyślna to 1 godziny (3600).

Wystawiony token przyznaje praw skojarzonych z określoną regułą sygnatury dostępu Współdzielonego w danym okresie.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Ta metoda jest funkcjonalnym odpowiednikiem `createRelayToken` metoda opisany wcześniej, ale zwraca token poprawnie dołączone do wprowadzania identyfikatora URI.

### <a name="class-wsrelayedserver"></a>Klasa ws. RelayedServer

`hycows.RelayedServer` Klasy stanowi alternatywę `ws.Server` klasę, która nie będzie nasłuchiwać w sieci lokalnej, ale delegaty nasłuchiwanie usługi Azure Relay.

Przede wszystkim kontraktu niezgodne, co oznacza, że istniejących aplikacji za pomocą są dwie klasy `ws.Server` klasy można łatwo zmienić, aby użyć wersji obsługiwanych przez przekaźnik. Główne różnice są w Konstruktorze i dostępne opcje.

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

`RelayedServer` Konstruktor obsługuje inny zbiór argumentów niż `Server`, ponieważ nie jest odbiornik autonomicznego lub mógł zostać osadzone w istniejącej struktury odbiornika HTTP. Brak dostępnych również mniej opcji ponieważ zarządzanie WebSocket stopniu jest delegowane do usługi przekazywania.

Argumenty konstruktora:

- `server` (wymagane) — w pełni kwalifikowany identyfikator URI dla nazwy połączenia hybrydowego do nasłuchiwania, zwykle skonstruowane, za pomocą metody pomocnika WebSocket.createRelayListenUri().
- `token` (wymagane) - tego argumentu przechowuje wcześniej wydanych ciąg tokenu lub funkcja wywołania zwrotnego, która może być wywoływana w celu uzyskania tokenu ciągu. Opcja wywołania zwrotnego jest preferowane, ponieważ dzięki niej odnowienia tokenu.

#### <a name="events"></a>Events

`RelayedServer` wystąpienia emitować trzy zdarzenia, które umożliwiają obsłużyć żądań przychodzących, nawiązywać połączenia i wykrywania warunków błędów. Należy zasubskrybować `connect` zdarzeń do obsługi wiadomości. 

##### <a name="headers"></a>Nagłówki

```JavaScript 
function(headers)
```

`headers` Zdarzenie jest wywoływane przed akceptowane połączenia przychodzące, umożliwiając modyfikację te nagłówki, aby wysłać do klienta. 

##### <a name="connection"></a>połączenie

```JavaScript
function(socket)
```

Emitowane po zaakceptowaniu nowe połączenie protokołu WebSocket. Obiekt jest typu `ws.WebSocket`, jest taka sama, jak przy użyciu podstawowego pakietu.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Jeśli podstawowy serwer generuje błąd, są przekazywane w tym miejscu.  

#### <a name="helpers"></a>Pomocnicy

Aby uprościć uruchamianie serwera obsługiwanych przez przekaźnik i od razu Subskrybuj połączeń przychodzących, pakiet udostępnia proste funkcji pomocnika, która, który jest również używany w przykładach w następujący sposób:

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

Ta metoda wywołuje konstruktora, aby utworzyć nowe wystąpienie klasy RelayedServer i następnie jest podłączone podanego wywołania zwrotnego do zdarzenia "połączenia".
 
##### <a name="relayedconnect"></a>relayedConnect

Po prostu dublowania `createRelayedServer` pomocnika w funkcji `relayedConnect` tworzy połączenie klienta i subskrybuje zdarzenie "Otwórz" na gniazdo wynikowe.

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
Aby dowiedzieć się więcej na temat usługi Azure Relay, skorzystaj z następujących linków:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Interfejsy API dostępne usługi Relay](relay-api-overview.md)
