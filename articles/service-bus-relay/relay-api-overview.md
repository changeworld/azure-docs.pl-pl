---
title: Omówienie usługi Azure API przekazywania | Dokumentacja firmy Microsoft
description: Przegląd informacji o dostępnych interfejsach API przekaźnika usługi Azure
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2018
ms.author: sethm
ms.openlocfilehash: 00496ca6c0138a840322c053d7d20944df228e9f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33893448"
---
# <a name="available-relay-apis"></a>Interfejsy API dostępne przekazywania

## <a name="runtime-apis"></a>Interfejsy API środowiska wykonawczego

Poniższa tabela zawiera listę wszystkich aktualnie dostępne klientów środowiska uruchomieniowego przekazywania.

[Dodatkowe informacje](#additional-information) sekcja zawiera więcej informacji o stanie wszystkich bibliotek środowiska uruchomieniowego.

| Język/Platform | Funkcja | Pakiet klienta | Repozytorium |
| --- | --- | --- | --- |
| .NET standard | Połączenia hybrydowe | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Przekaźnik WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | ND |
| Węzeł | Połączenia hybrydowe | [Obiekty Websocket: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Obiekty Websocket: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Żądania HTTP: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Dodatkowe informacje

#### <a name="net"></a>.NET

Ekosystemu platformy .NET ma wiele środowisk uruchomieniowych, więc istnieje wiele bibliotek .NET dla przekaźnika. Biblioteki standardowe .NET mogą być uruchamiane przy użyciu platformy .NET Core lub .NET Framework, gdy biblioteka programu .NET Framework może być uruchamiany tylko w środowisku .NET Framework. Aby uzyskać więcej informacji dotyczących platformy .NET Framework, zobacz [framework w wersji](/dotnet/articles/standard/frameworks#framework-versions).

Biblioteka programu .NET Framework tylko obsługuje model programowania usług WCF i korzysta z zastrzeżonym protokołem binarne oparte na usługi WCF `net.tcp` transportu. Ten protokół i biblioteki jest zachowywana na potrzeby zapewnienia zgodności z istniejącymi aplikacjami.

Biblioteki standardowej .NET jest oparta na definicję protokołu open bazującą na HTTP i technologia WebSockets przekaźnika połączeń hybrydowych. Biblioteka obsługuje abstrakcji strumienia, przez protokół Websockets i gestu interfejsu API proste żądań i odpowiedzi dla odpowiadanie na żądania HTTP. [Interfejsu API sieci Web](https://github.com/Azure/azure-relay-dotnet) przykładowych pokazano, jak zintegrować połączenia hybrydowe z platformy ASP.NET Core dla usług sieci web.

#### <a name="nodejs"></a>Node.js

Moduły połączeń hybrydowych wymienione w powyższej tabeli Zamień lub zmiana istniejących modułów Node.js z alternatywnych implementacji, które nasłuchują usługi przekaźnika usługi Azure zamiast lokalnego stosu sieciowego.

`hyco-https` Moduł zmienia i częściowo zastępuje modułów Node.js core `http` i `https`, pod warunkiem implementacji odbiornika protokołu HTTPS, który jest zgodny z wielu istniejących modułów Node.js i aplikacje korzystające z tych podstawowych moduły.

`hyco-ws` i `hyco-websocket` modułów zmiany popularnych `ws` i `websocket` modułów node.js, zapewniając implementacji alternatywnych odbiornika, umożliwiających moduły i aplikacje zależne albo modułu w tle hybrydowego Połączenia przekaźnika.

Szczegółowe informacje o tych modułów, można znaleźć w [azure przekazywania węzłem](https://github.com/Azure/azure-relay-node) repozytorium GitHub.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat przekaźnika usługi Azure, odwiedź te linki:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)