---
title: Odporność i odzyskiwania po awarii w usłudze Azure SignalR Service
description: Omówienie sposobu konfigurowania wielu wystąpień usługi SignalR w celu uzyskania odporności i udostępnienia możliwości odzyskiwania po awarii
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: eb70e65db4a086afc60e91cadf55a8844b102591
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620280"
---
# <a name="resiliency-and-disaster-recovery"></a>Odporność i odzyskiwanie po awarii

Odporność i odzyskiwanie po awarii to typowe potrzeby systemów online. Sama usługa Azure SignalR Service gwarantuje dostępność na poziomie 99,9%, ale nadal jest to usługa regionalna.
Wystąpienia usługi zawsze działa w jednym regionie, a nie trybu failover do innego regionu po awarii całego regionu.

Zamiast tego zestaw SDK usługi udostępnia obsługę wielu wystąpień usługi SignalR i automatycznego przełączania do innych wystąpień, gdy niektóre z nich staną się niedostępne.
Dzięki tej funkcji masz możliwość przeprowadzenia odzyskiwania, gdy wystąpi awaria, lecz poprawną topologię systemu należy skonfigurować samodzielnie. W tym dokumencie dowiesz się, jak to zrobić.

## <a name="high-available-architecture-for-signalr-service"></a>Architektura wysokiej dostępności usługi SignalR

Aby zapewnić odporność obejmującą wiele regionów dla usługi SignalR, musisz skonfigurować wiele wystąpień usługi w różnych regionach. Wtedy gdy jeden region nie działa, można użyć innych jako zapasowych.
W przypadku łączenia wielu wystąpień usługi z serwerem aplikacji można określić dla wystąpienia rolę określającą je jako podstawowe lub pomocnicze.
Wystąpienie podstawowe to wystąpienie, które obsługuje ruch online, a wystąpienie pomocnicze to wystąpienie w pełni funkcjonalne, lecz stanowiące zapas dla wystąpienia podstawowego.
W implementacji zestawu SDK negocjacja zwraca tylko podstawowe punkty końcowe, dzięki czemu w standardowych przypadkach klienci łączą się tylko z podstawowymi punktami końcowymi.
Ale jeśli podstawowe wystąpienie nie działa, negocjacja zwróci pomocnicze punkty końcowe, dzięki czemu klient będzie mógł nawiązać połączenie.
Wystąpienia podstawowego i serwera aplikacji, które są połączone za pośrednictwem połączenia z serwerem normalne, ale dodatkowej wystąpienia i serwera aplikacji, które są połączone za pośrednictwem specjalny rodzaj połączenia o nazwie słabe połączenie.
Główna różnica słabe połączenie jest, że nie zaakceptować routing połączenia klienta, ponieważ wystąpienie dodatkowej znajduje się w innym regionie. Routing klienta do innego regionu nie jest optymalnym wyborem (zwiększa opóźnienia).

Jedno wystąpienie usługi może mieć różne role, jeśli łączy się z wieloma serwerami aplikacji.
Jedną z typowych konfiguracji dla scenariusza z wieloma regionami jest skonfigurowanie dwóch (lub więcej) par wystąpień usługi SignalR i serwerów aplikacji.
W każdej parze serwer aplikacji i usługa SignalR znajdują się w tym samym regionie, a usługa SignalR jest połączona z serwerem aplikacji za pomocą roli podstawowej.
Serwer aplikacji i usługa SignalR w różnych parach są także połączone, lecz usługa SignalR łączy się z serwerem w innym regionie jako pomocnicza.

W takiej topologii komunikat z jednego serwera może być w dalszym ciągu dostarczony do wszystkich klientów, ponieważ wszystkie serwery aplikacji i wystąpienia usługi SignalR są wzajemnie połączone.
Lecz kiedy klient nawiązuje połączenie, jest on zawsze kierowany do serwera aplikacji w tym samym regionie w celu osiągnięcia optymalnego opóźnienia sieci.

Poniżej przedstawiono diagram, który ilustruje taką topologię:

![topology](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Konfigurowanie serwerów aplikacji od kątem wielu wystąpień usługi SignalR

Po utworzeniu usługi SignalR i serwerów aplikacji w każdym regionie można skonfigurować serwery aplikacji pod kątem łączenia ze wszystkimi wystąpieniami usługi SignalR.

Można to zrobić na dwa sposoby:

### <a name="through-config"></a>Za pomocą konfiguracji

Powinna wiedzieć jak ustawić parametry połączenia usługi SignalR za pośrednictwem settings/web.cofig zmienne/aplikacji środowiska, za pomocą pozycji konfiguracji o nazwie `Azure:SignalR:ConnectionString`.
Jeśli masz wiele punktów końcowych, możesz je umieścić w wielu wpisach konfiguracji, z których każdy jest w następującym formacie:

```
Azure:SignalR:Connection:<name>:<role>
```

W tym miejscu parametr `<name>` to nazwa punktu końcowego, a parametr `<role>` to jego rola (podstawowy lub pomocniczy).
Nazwa jest opcjonalna, lecz będzie przydatna, jeśli chcesz dostosować zachowanie routingu między wieloma punktami końcowymi.

### <a name="through-code"></a>Za pomocą kodu

Jeśli wolisz przechowywać parametry połączenia w innym miejscu, możesz także odczytać je w kodzie i użyć jako parametrów wywołania `AddAzureSignalR()` (na platformie ASP.NET Core) lub `MapAzureSignalR()` (na platformie ASP.NET).

Oto przykładowy kod:

ASP.NET Core:

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET:

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

## <a name="failover-sequence-and-best-practice"></a>Sekwencja trybu failover i najlepsze rozwiązania

Teraz masz skonfigurowaną właściwą topologię systemu. Zawsze, gdy wystąpienie usługi SignalR zostanie wyłączone, ruch online będzie kierowany do innych wystąpień.
Oto, co dzieje się w przypadku wyłączenia wystąpienia podstawowego (i jego odzyskania po pewnym czasie):

1. Gdy wystąpienie podstawowe usługi zostanie wyłączone, wszystkie połączenia serwera z tym wystąpieniem zostaną przerwane.
2. Wszystkie serwery połączone z tym wystąpieniem oznaczą ją jako będące w trybie offline, a negocjacje przestaną zwracać ten punkt końcowy i zaczną zwracać pomocniczy punkt końcowy.
3. Wszystkie połączenia klientów z tym wystąpieniem zostaną także zamknięte, a klienci nawiążą połączenie ponownie. Ponieważ serwery aplikacji teraz zwracają pomocniczy punkt końcowy, klienci łączą się z wystąpieniem pomocniczym.
4. Teraz wystąpienie pomocnicze obsługuje cały ruch w trybie online. Wszystkie komunikaty wysyłane przez serwer do klientów nadal mogą być dostarczane, ponieważ wystąpienie pomocnicze jest połączone ze wszystkimi serwerami aplikacji. Jednak komunikaty wysyłane przez klienta do serwera są kierowane tylko do serwera aplikacji w tym samym regionie.
5. Po odzyskaniu wystąpienia podstawowego i jego przejściu z powrotem w tryb online serwer aplikacji nawiąże z nim połączenie ponownie i oznaczy je jako będące w trybie online. Negocjacje będą teraz zwracać podstawowy punkt końcowy i nowi klienci będą z powrotem łączyć się z nim. Istniejący klienci nie zostaną rozłączeni i będą w dalszym ciągu kierowani do wystąpienia dodatkowego, dopóki sami się nie rozłączą.

Poniższe diagramy przedstawiają przechodzenie w tryb failover przez usługę SignalR:

Rys. 1 Przed przejściem w tryb failover ![Przed przejściem w tryb failover](media/signalr-concept-disaster-recovery/before-failover.png)

Rys. 2 Po przejściu w tryb failover ![Po przejściu w tryb failover](media/signalr-concept-disaster-recovery/after-failover.png)

Rys. 3 Krótko po odzyskaniu wystąpienia podstawowego ![Krótko po odzyskaniu wystąpienia podstawowego](media/signalr-concept-disaster-recovery/after-recover.png)

Jak widać, w normalnym przypadku tylko podstawowy serwer aplikacji i podstawowa usługa SignalR obsługują ruch online (w kolorze niebieskim).
Po przejściu w tryb failover pomocniczy serwer aplikacji i pomocnicza usługa SignalR także stają się aktywne.
Gdy podstawowa usługa SignalR wróci do trybu online, nowi klienci będą łączyć się z podstawową usługą SignalR. Lecz istniejący klienci nadal będą łączyć się z wystąpieniem pomocniczym, więc oba wystąpienia będą obsługiwać ruch.
Gdy wszyscy istniejący klienci rozłączą się, system wróci do normalnego stanu (Rys. 1).

Architekturę wysokiej dostępności z wieloma regionami można zaimplementować za pomocą dwóch głównych wzorców:

1. Pierwszy to para serwer aplikacji i wystąpienie usługi SignalR obsługujące cały ruch online z inną parą jako zapasem (wzorzec aktywny/pasywny przedstawiony na rysunku 1). 
2. Drugi to dwie (lub więcej) pary serwerów aplikacji i wystąpień usługi SignalR, przy czym wszystkie obsługują ruch online i służą jako zapas dla innych par (wzorzec aktywny/aktywny podobny do przedstawionego na rysunku 3).

Usługa SignalR może obsługiwać oba wzorce, a główna różnica zależy od sposobu implementacji serwerów aplikacji.
Jeśli serwery aplikacji są zgodne ze wzorcem aktywny/pasywny, usługa SignalR będzie także zgodna z tym wzorcem (ponieważ podstawowy serwer aplikacji zwraca tylko swoje podstawowe wystąpienie usługi SignalR).
Jeśli serwery aplikacji są zgodne z wzorcem aktywny/aktywny, usługa SignalR będzie także zgodna z tym wzorcem (ponieważ wszystkie serwery aplikacji zwracają własne podstawowe wystąpienia usługi SignalR, to wszystkie wystąpienia mogą obsługiwać ruch).

Należy zauważyć, niezależnie od tego, wzorców, które chcesz użyć, musisz nawiązać połączenie na serwerze aplikacji jako podstawowy każde wystąpienie usługi SignalR.

Ze względu na charakter połączenia usługi SignalR (jest to połączenie długie) połączenia klientów zostaną przerwane w przypadku wystąpienia awarii i przejścia do trybu failover.
Takie przypadki należy obsługiwać po stronie klienta, aby były przezroczyste dla użytkowników końcowych. Na przykład należy nawiązać ponownie połączenie po zamknięciu połączenia.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób konfigurowania aplikacji pod kątem odporności ze względu na usługę SignalR. Aby dowiedzieć się więcej na temat połączenia serwera/klienta i routingu połączenia w usłudze SignalR, możesz przeczytać [ten artykuł](signalr-concept-internals.md) przedstawiający elementy wewnętrzne usługi SignalR.

Skalowanie scenariuszy, takich jak dzielenie na fragmenty, korzystających z wielu wystąpień ze sobą do obsługi dużej liczby połączeń, przeczytaj [sposób skalowania wielu wystąpień](signalr-howto-scale-multi-instances.md)?