---
title: Omówienie usługi Azure Relay interfejsu API | Dokumentacja firmy Microsoft
description: Omówienie dostępnych interfejsów API usługi Azure Relay
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2018
ms.author: spelluru
ms.openlocfilehash: 05d7ac56d6c1c48125eb458d0eee852ba396b300
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60593342"
---
# <a name="available-relay-apis"></a>Interfejsy API dostępne usługi Relay

## <a name="runtime-apis"></a>Interfejsów API środowiska wykonawczego

Poniższa tabela zawiera listę wszystkich dostępnych klientów środowiska uruchomieniowego przekazywania.

[Dodatkowe informacje](#additional-information) sekcja zawiera więcej informacji o stanie wszystkich bibliotek środowiska uruchomieniowego.

| Języka/platformy | Funkcja dostępna | Pakiet klienta | Repozytorium |
| --- | --- | --- | --- |
| .NET Standard | Połączenia hybrydowe | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Przekaźnik WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | ND |
| Węzeł | Połączenia hybrydowe | [Gniazda Websocket: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Gniazda Websocket: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Żądania HTTP: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Dodatkowe informacje

#### <a name="net"></a>.NET

Ekosystem platformy .NET ma wielu modułów wykonawczych, więc istnieje wiele bibliotek platformy .NET dla usługi Relay. Biblioteki .NET Standard mogą być uruchamiane przy użyciu platformy .NET Core lub .NET Framework, natomiast biblioteka .NET Framework można uruchomić tylko w środowisku .NET Framework. Aby uzyskać więcej informacji na temat platformy .NET, zobacz [framework w wersji](/dotnet/articles/standard/frameworks).

Biblioteka programu .NET Framework tylko obsługuje model programowania usług WCF i zależy od własności protokołu binarne oparte na WCF `net.tcp` transportu. Ten protokół i biblioteka jest zachowywana na potrzeby zapewnienia zgodności z istniejącymi aplikacjami.

Biblioteki .NET Standard opiera się na otwartym protokole definicji przekaźnik połączeń hybrydowych, który opiera się na HTTP i Websocket. Biblioteka obsługuje pozyskiwania strumienia, przez protokół Websockets i gest proste odpowiedź na żądanie interfejsu API dla odpowiadanie na żądania HTTP. [Interfejsu API sieci Web](https://github.com/Azure/azure-relay-dotnet) przykład pokazuje, jak zintegrować połączeń hybrydowych przy użyciu platformy ASP.NET Core dla usług sieci web.

#### <a name="nodejs"></a>Node.js

Moduły połączeń hybrydowych, wymienione w powyższej tabeli zastąpienia lub zmiany istniejących modułów Node.js z alternatywnych implementacji, które nasłuchują usługi Azure Relay, a nie lokalny stosu sieciowego.

`hyco-https` Moduł zmienia i częściowo zastępuje modułów Node.js core `http` i `https`, udostępniając implementacji odbiornika protokołu HTTPS, który jest zgodny z wielu istniejących modułów Node.js i aplikacje, które zależą od tych podstawowych moduły.

`hyco-ws` i `hyco-websocket` modułów zmiany popularnej `ws` i `websocket` modułów dla środowiska Node.js, dostarczając implementacje odbiornika alternatywne, umożliwiające modułów i aplikacje, opierając się na obu modułu w tle rozwiązania hybrydowego Połączenia usługi Relay.

Szczegółowe informacje o tych modułów, które można znaleźć w [azure relay węzłów](https://github.com/Azure/azure-relay-node) repozytorium GitHub.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Azure Relay, skorzystaj z następujących linków:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)
