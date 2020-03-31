---
title: Omówienie interfejsu API usługi Azure Relay | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie dostępnych interfejsów API usługi Azure Relay (.NET Standard, .NET Framework, Node.js itp.)
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 41d9e2026c19c959dc6fe2546b0ef699571ec7cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513970"
---
# <a name="available-relay-apis"></a>Dostępne interfejsy API przekazywania

## <a name="runtime-apis"></a>Interfejsy API środowiska wykonawczego

W poniższej tabeli wymieniono wszystkich aktualnie dostępnych klientów środowiska wykonawczego przekazywania.

Dodatkowa sekcja [informacji](#additional-information) zawiera więcej informacji na temat stanu każdej biblioteki środowiska wykonawczego.

| Język/platforma | Dostępna funkcja | Pakiet klienta | Repozytorium |
| --- | --- | --- | --- |
| .NET Standard | Połączenia hybrydowe | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Przekaźnik WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | Nie dotyczy |
| Węzeł | Połączenia hybrydowe | [Websocket:`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websocket:`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Żądania HTTP:`hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Dodatkowe informacje

#### <a name="net"></a>.NET

Ekosystem .NET ma wiele uruchomień, w związku z tym istnieje wiele bibliotek .NET dla przekazywania. Bibliotekę .NET Standard można uruchomić przy użyciu programu .NET Core lub programu .NET Framework, podczas gdy biblioteka .NET Framework może być uruchamiana tylko w środowisku .NET Framework. Aby uzyskać więcej informacji na temat programu .NET Frameworks, zobacz [wersje frameworka](/dotnet/articles/standard/frameworks).

Biblioteka .NET Framework obsługuje tylko model programowania WCF i opiera się na `net.tcp` zastrzeżonym protokole binarnym opartym na transporcie WCF. Ten protokół i biblioteka jest utrzymywana w celu zapewnienia zgodności z istniejącymi aplikacjami wstecznie.

Biblioteka .NET Standard jest oparta na definicji otwartego protokołu dla przekazywania połączeń hybrydowych, która opiera się na interfejsach HTTP i WebSockets. Biblioteka obsługuje abstrakcję strumienia za pośrednictwem websockets i gest interfejsu API proste żądanie odpowiedzi na odpowiedzi na żądania HTTP. Przykład [interfejsu API sieci Web](https://github.com/Azure/azure-relay-dotnet) pokazuje, jak zintegrować połączenia hybrydowe z ASP.NET Core dla usług sieci web.

#### <a name="nodejs"></a>Node.js

Moduły połączeń hybrydowych wymienione w powyższej tabeli zastępują lub zmieniają istniejące moduły Node.js za pomocą alternatywnych implementacji, które nasłuchują w usłudze Azure Relay zamiast na lokalnym stosie sieciowym.

Moduł `hyco-https` zmienia i częściowo zastępuje podstawowe moduły `http` Node.js `https`i zapewnia implementację odbiornika HTTPS, która jest zgodna z wieloma istniejącymi modułami i aplikacjami Node.js, które opierają się na tych podstawowych modułach.

`hyco-ws` Moduły `hyco-websocket` i moduły `ws` `websocket` zmieniają popularne i moduły dla node.js, zapewniając alternatywne implementacje odbiornika, które umożliwiają modułom i aplikacjom korzystającym z obu modułów do pracy za przekaźnikiem połączeń hybrydowych.

Szczegółowe informacje na temat tych modułów można znaleźć w repozytorium GitHub z węzłem azure.Details about those modules can be found in the [azure-relay-node](https://github.com/Azure/azure-relay-node) GitHub repozytorium.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze Azure Relay, odwiedź następujące łącza:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)
