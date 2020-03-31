---
title: Omówienie interfejsów API węzła przekaźnika platformy Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie interfejsu API node.js dla usługi Azure Relay. Pokazuje również, jak używać pakietu węzła hyco-ws.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 2877284c419da4999e23490fc986e5da44e5d92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514514"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Omówienie interfejsu API węzła połączeń hybrydowych przekazywania

## <a name="overview"></a>Omówienie

Pakiet [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) node dla połączeń hybrydowych usługi Azure Relay jest zbudowany na i rozszerza pakiet NPM ["ws".](https://www.npmjs.com/package/ws) Ten pakiet ponownie eksportuje cały eksport tego pakietu podstawowego i dodaje nowe eksporty, które umożliwiają integrację z funkcją połączenia hybrydowe usługi Azure Relay. 

Istniejące aplikacje, które `require('ws')` `require('hyco-ws')` można użyć tego pakietu z zamiast tego, co również umożliwia scenariusze hybrydowe, w których aplikacja może nasłuchiować połączeń WebSocket lokalnie z "wewnątrz zapory" i za pośrednictwem połączeń hybrydowych, wszystkie w tym samym czasie.
  
## <a name="documentation"></a>Dokumentacja

Interfejsy API są [udokumentowane w głównym pakiecie "ws"](https://github.com/websockets/ws/blob/master/doc/ws.md). W tym artykule opisano, jak ten pakiet różni się od tej linii bazowej. 

Kluczowe różnice między pakietem podstawowym a tym "hyco-ws" polega na tym, że dodaje nową klasę serwera, eksportowane za pośrednictwem `require('hyco-ws').RelayedServer`, i kilka metod pomocnika.

### <a name="package-helper-methods"></a>Metody pomocnika pakietu

Istnieje kilka metod narzędzi dostępnych na eksport pakietu, które można odwoływać się w następujący sposób:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Metody pomocnicze są używane z tym pakietem, ale mogą być również używane przez serwer węzła do włączania klientów sieci web lub urządzeń do tworzenia odbiorników lub nadawców. Serwer używa tych metod, przekazując im identyfikatory URI, które osadzają tokeny krótkotrwałe. Te identyfikatory URI mogą być również używane ze wspólnymi stosami WebSocket, które nie obsługują ustawiania nagłówków HTTP dla uzgadniania WebSocket. Osadzanie tokenów autoryzacji w identyfikatorze URI jest obsługiwane przede wszystkim dla tych scenariuszy użycia zewnętrznego biblioteki. 

#### <a name="createrelaylistenuri"></a>tworzenieRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Tworzy prawidłowy identyfikator URI odbiornika łącza hybrydowego usługi Azure Relay dla danego obszaru nazw i ścieżki. Ten identyfikator URI może być następnie używany z wersją przekazywania klasy WebSocketServer.

- `namespaceName`(wymagane) — do użycia kwalifikowana nazwa obszaru nazw usługi Azure Relay.
- `path`(wymagane) — nazwa istniejącego połączenia hybrydowego usługi Azure Relay w tym obszarze nazw.
- `token`(opcjonalnie) — wcześniej wystawiony token dostępu przekazywania, który jest osadzony w identyfikatorze URI odbiornika odbiornika (zobacz poniższy przykład).
- `id`(opcjonalnie) — identyfikator śledzenia, który umożliwia kompleksowe śledzenie diagnostyki żądań.

Wartość `token` jest opcjonalna i powinna być używana tylko wtedy, gdy nie jest możliwe wysyłanie nagłówków HTTP wraz z uzgadniania WebSocket, jak w przypadku stosu W3C WebSocket.                  


#### <a name="createrelaysenduri"></a>tworzenieRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Tworzy prawidłowe połączenie hybrydowe usługi Azure Relay wysyłania identyfikatora URI dla danego obszaru nazw i ścieżki. Ten identyfikator URI może być używany z dowolnym klientem WebSocket.

- `namespaceName`(wymagane) — do użycia kwalifikowana nazwa obszaru nazw usługi Azure Relay.
- `path`(wymagane) — nazwa istniejącego połączenia hybrydowego usługi Azure Relay w tym obszarze nazw.
- `token`(opcjonalnie) — wcześniej wystawiony token dostępu przekazywania, który jest osadzony w identyfikatorze URI wysyłania (zobacz poniższy przykład).
- `id`(opcjonalnie) — identyfikator śledzenia, który umożliwia kompleksowe śledzenie diagnostyki żądań.

Wartość `token` jest opcjonalna i powinna być używana tylko wtedy, gdy nie jest możliwe wysyłanie nagłówków HTTP wraz z uzgadniania WebSocket, jak w przypadku stosu W3C WebSocket.                   


#### <a name="createrelaytoken"></a>tworzenieRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Tworzy token podpisu udostępnionego dostępu usługi Azure Relay (SAS) dla danego docelowego identyfikatora URI, reguły sygnatury dostępu współdzielonego i klucza reguły sygnatury dostępu współdzielonego, który jest prawidłowy dla danej liczby sekund lub przez godzinę od bieżącego komunikatu natychmiastowego, jeśli argument wygaśnięcia zostanie pominięty.

- `uri`(wymagane) — identyfikator URI, dla którego ma zostać wystawiony token. Identyfikator URI jest znormalizowany do używania schematu HTTP, a informacje o ciągu zapytania są usuwane.
- `ruleName`(wymagane) — nazwa reguły sygnatury dostępu Współdzielonego dla jednostki reprezentowanej przez dany identyfikator URI lub dla obszaru nazw reprezentowanej przez część hosta identyfikatora URI.
- `key`(wymagane) - prawidłowy klucz dla reguły sygnatury dostępu Współdzielonego. 
- `expirationSeconds`(opcjonalnie) - liczba sekund do wygaśnięcia wygenerowanego tokenu. Jeśli nie zostanie określony, wartość domyślna to 1 godzina (3600).

Wystawiony token nadaje prawa skojarzone z określoną regułą sygnatury dostępu Współdzielonego przez dany czas trwania.

#### <a name="appendrelaytoken"></a>dodatekRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Ta metoda jest funkcjonalnie równoważne `createRelayToken` metody udokumentowane wcześniej, ale zwraca token poprawnie dołączone do wejściowego identyfikatora URI.

### <a name="class-wsrelayedserver"></a>Klasa ws. Serwer przekazu

Klasa `hycows.RelayedServer` jest alternatywą `ws.Server` dla klasy, która nie nasłuchuje w sieci lokalnej, ale delegatów nasłuchuje usługi Azure Relay.

Dwie klasy są w większości zgodne z umową, `ws.Server` co oznacza, że istniejąca aplikacja przy użyciu klasy można łatwo zmienić w celu użycia wersji przekazywanych. Główne różnice są w konstruktorze i dostępnych opcji.

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

Konstruktor `RelayedServer` obsługuje inny zestaw argumentów niż `Server`, ponieważ nie jest samodzielnym odbiornikiem lub może być osadzony w istniejącej ramach odbiornika HTTP. Dostępnych jest również mniej opcji, ponieważ zarządzanie WebSocket jest w dużej mierze delegowane do usługi Przekazywanie.

Argumenty konstruktora:

- `server`(wymagane) — w pełni kwalifikowany identyfikator URI dla nazwy połączenia hybrydowego, na którym można nasłuchiwać, zwykle skonstruowany za pomocą metody pomocnika WebSocket.createRelayListen().
- `token`(wymagane) — ten argument zawiera wcześniej wystawiony ciąg tokenu lub funkcję wywołania zwrotnego, która może zostać wywołana w celu uzyskania takiego ciągu tokenu. Preferowana jest opcja wywołania zwrotnego, ponieważ umożliwia odnowienie tokenu.

#### <a name="events"></a>Zdarzenia

`RelayedServer`wystąpienia emitują trzy zdarzenia, które umożliwiają obsługę żądań przychodzących, ustanawianie połączeń i wykrywanie warunków błędów. Musisz zasubskrybować zdarzenie do `connect` obsługi wiadomości. 

##### <a name="headers"></a>Nagłówki

```JavaScript 
function(headers)
```

Zdarzenie `headers` jest wywoływane tuż przed zaakceptowaniem połączenia przychodzącego, umożliwiając modyfikację nagłówków do wysłania do klienta. 

##### <a name="connection"></a>połączenie

```JavaScript
function(socket)
```

Emitowane, gdy nowe połączenie WebSocket jest akceptowane. Obiekt jest typu, `ws.WebSocket`tak samo jak w pakiecie podstawowym.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Jeśli serwer źródłowy emituje błąd, jest on przekazywał dalej w tym miejscu.  

#### <a name="helpers"></a>Pomocników

Aby uprościć uruchamianie serwera przekazywanym i natychmiastowe subskrybowanie połączeń przychodzących, pakiet udostępnia prostą funkcję pomocnika, która jest również używana w przykładach, w następujący sposób:

##### <a name="createrelayedlistener"></a>tworzenieRelayedListener

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

##### <a name="createrelayedserver"></a>tworzenieRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Ta metoda wywołuje konstruktora, aby utworzyć nowe wystąpienie RelayedServer, a następnie subskrybuje podane wywołanie zwrotne do zdarzenia "connection".
 
##### <a name="relayedconnect"></a>relayedConnect

Po prostu `createRelayedServer` dublowanie pomocnika `relayedConnect` w funkcji, tworzy połączenie klienta i subskrybuje zdarzenie "open" na wynikowym gnieździe.

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

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o usłudze Azure Relay, odwiedź następujące łącza:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Dostępne interfejsy API przekazywania](relay-api-overview.md)
