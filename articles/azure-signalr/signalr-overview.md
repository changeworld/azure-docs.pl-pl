---
title: Co to jest Azure SignalR | Microsoft Docs
description: Omówienie usługi Azure SignalR Service.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/17/2018
ms.author: zhshang
ms.openlocfilehash: c574a3dd26b36b656cc931a0801cbf0ef23cf362
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43668245"
---
# <a name="what-is-azure-signalr-service"></a>Co to jest Azure SignalR Service

Usługa Microsoft Azure SignalR Service jest obecnie dostępna w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure SignalR Service jest usługą platformy Azure bazującą na bibliotece [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction). ASP.NET Core SignalR jest [biblioteką typu open source](https://github.com/aspnet/signalr), która upraszcza proces dodawania do aplikacji możliwości działania w czasie rzeczywistym w Internecie za pośrednictwem protokołu HTTP. Ta możliwość działania w czasie rzeczywistym umożliwia serwerowi internetowemu wypychanie aktualizacji zawartości do połączonych klientów. W związku z tym klienci otrzymują aktualizacje bez konieczności sondowania serwera czy przesyłania nowych żądań HTTP o aktualizacje.

Ten artykuł zawiera omówienie usługi Azure SignalR Service. Jeśli chcesz rozpocząć pracę, zacznij od [przewodnika Szybki start dla platformy ASP.NET Core](signalr-quickstart-dotnet-core.md).

## <a name="what-is-signalr-service-used-for"></a>Do czego służy usługa SignalR Service? 

Istnieje wiele typów aplikacji, które wymagają aktualizacji zawartości w czasie rzeczywistym. Następujące przykładowe typy aplikacji są dobrymi kandydatami do korzystania z usługi Azure SignalR Service:

* Aplikacje, które wymagają częstych aktualizacji z serwera. Są to na przykład gry i aplikacje do obsługi sieci społecznościowych, głosowania, aukcji, map i nawigacji GPS.
* Pulpity nawigacyjne i aplikacje do monitorowania. Przykładami mogą być firmowe pulpity nawigacyjne, błyskawiczne powiadomienia o sprzedaży lub alerty dotyczące podróży.
* Aplikacje do współpracy. Przykładami aplikacji do współpracy są aplikacje tablicy i oprogramowanie do spotkań zespołowych.
* Aplikacje, które wymagają powiadomień. Wiele aplikacji korzysta z powiadomień, w tym gry i aplikacje do obsługi sieci społecznościowych, poczty e-mail, czatów i alertów dotyczących podróży.

Wewnętrznie biblioteka SignalR stanowi abstrakcję szeregu technik służących do tworzenia aplikacji internetowych działających w czasie rzeczywistym. [Protokół WebSockets](https://wikipedia.org/wiki/WebSocket) stanowi optymalną metodę transportu, ale inne techniki, takie jak [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) i Long Polling są używane, gdy nie są dostępne inne opcje. Biblioteka SignalR automatycznie wykrywa i inicjuje odpowiednią metodę transportu na podstawie funkcji obsługiwanych przez serwer i klienta.

## <a name="developing-signalr-apps"></a>Tworzenie aplikacji SignalR

Obecnie dostępne są dwie wersje biblioteki SignalR, których można używać z aplikacjami internetowymi: SignalR for ASP.NET i ASP.NET Core SignalR, która jest najnowszą wersją. Usługa Azure SignalR Service jest usługą zarządzaną przez platformę Azure, zbudowaną na podstawie biblioteki ASP.NET Core SignalR. 

ASP.NET Core SignalR to ponownie napisana poprzednia wersja tej biblioteki. W związku z tym biblioteka ASP.NET Core SignalR nie jest zgodna z wcześniejszą wersją biblioteki SignalR. Interfejsy API i działanie różnią się. Zestaw ASP.NET Core SignalR SDK jest zgodny ze specyfikacją .NET Standard, więc nadal można z niego korzystać na platformie .NET Framework. Należy jednak używać nowych interfejsów API zamiast starych. Jeśli używasz biblioteki SignalR i chcesz przejść na bibliotekę ASP.NET Core SignalR lub usługę Azure SignalR Service, musisz zmodyfikować swój kod tak, aby uwzględnić różnice w interfejsach API.

W przypadku usługi Azure SignalR Service składnik biblioteki ASP.NET Core SignalR po stronie serwera jest hostowany na platformie Azure. Jednak ponieważ technologia jest oparta na platformie ASP.NET Core, masz możliwość uruchamiania rzeczywistej aplikacji internetowej na wielu platformach (Windows, Linux i MacOS), hostując ją w usłudze [Azure App Service](../app-service/app-service-web-overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) lub [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). Możesz też użyć własnego hostingu w ramach swojego procesu.

Jeśli cele Twojej aplikacji obejmują obsługę najnowszej funkcji aktualizowania klientów internetowych o aktualizacje zawartości w czasie rzeczywistym, uruchamianie na wielu platformach (Azure, Windows, Linux i MacOS) oraz hostowanie w różnych środowiskach, najlepszym wyborem będzie skorzystanie z usługi Azure SignalR Service.


## <a name="why-not-deploy-signalr-myself"></a>Dlaczego samodzielnie nie wdrażać biblioteki SignalR?

Wdrażanie własnej aplikacji internetowej platformy Azure obsługującej bibliotekę ASP.NET Core SignalR jako składnik zaplecza całej aplikacji internetowej jest nadal prawidłowym podejściem.

Jednym z najważniejszych argumentów za korzystaniem z usługi Azure SignalR Service jest prostota. W przypadku usługi Azure SignalR Service nie trzeba martwić się o takie rzeczy jak wydajność, skalowalność i dostępność. Tymi kwestiami zajmujemy się my, dając umowę SLA gwarantującą czas dostępności na poziomie 99,9%.

Ponadto preferowaną techniką aktualizacji zawartości w czasie rzeczywistym jest protokół WebSocket. Jednak równoważenie obciążenia dużą liczbą trwałych połączeń protokołu WebSocket staje się problemem niełatwym do rozwiązania podczas skalowania. Jego typowe rozwiązania obejmują równoważenie obciążenia DNS, sprzętowe moduły równoważenia obciążenia i programowe równoważenie obciążenia. Usługa Azure SignalR Service rozwiązuje ten problem za Ciebie.

Innym argumentem może być to, że w ogóle nie potrzebujesz hostować aplikacji internetowej. Logika Twojej aplikacji internetowej może wykorzystywać [obliczenia bezserwerowe](https://azure.microsoft.com/overview/serverless-computing/). Na przykład Twój kod może być hostowany i wykonywany jedynie na żądanie przy użyciu wyzwalaczy usługi [Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Ten scenariusz może być niełatwy, ponieważ Twój kod jest wykonywany tylko na żądanie i nie utrzymuje długich połączeń z klientami. Usługa Azure SignalR Service może obsłużyć taką sytuację, ponieważ już zarządza połączeniami za Ciebie.

## <a name="how-does-it-scale"></a>Jak wykonywane jest skalowanie?

Częstą sytuacją jest skalowanie biblioteki SignalR za pomocą programu SQL Server lub usługi Azure Service Bus albo Redis Cache. Usługa Azure SignalR Service obsługuje podejście ze skalowaniem za Ciebie. Wydajność i koszty są porównywalne jak w powyższym podejściu, ale bez złożoności związanej z obsługą tych innych usług. Wszystko, co trzeba zrobić, to zaktualizować liczbę jednostek dla swojej usługi. Każda jednostka usługi obsługuje do 1000 połączeń z klientami.

## <a name="next-steps"></a>Następne kroki
* [Szybki start: tworzenie pokoju rozmów za pomocą usługi Azure SignalR](signalr-quickstart-dotnet-core.md)  
  

