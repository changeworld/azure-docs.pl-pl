---
title: Monitoruj operacje, zdarzenia i liczniki dla Load Balancer publicznego w warstwie Podstawowa
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak włączyć zdarzenia alertu i rejestrować stan kondycji sondy dla publicznych Load Balancer podstawowych
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: allensu
ms.openlocfilehash: 1995ad5e8179fdee11e960c2ad0e7c03602ebd31
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274796"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Dzienniki Azure Monitor dla publicznych Load Balancer podstawowych

>[!IMPORTANT] 
>Azure Load Balancer obsługuje dwa różne typy: Podstawowa i Standardowa. W tym artykule omówiono usługę Load Balancer w warstwie Podstawowa. Aby uzyskać więcej informacji na temat usługa Load Balancer w warstwie Standardowa, zobacz [omówienie usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md) , które udostępnia dane telemetryczne za pośrednictwem wielowymiarowych metryk w Azure monitor.

Możesz użyć różnych typów dzienników na platformie Azure do zarządzania podstawowymi modułami równoważenia obciążenia i rozwiązywania problemów. Dostęp do niektórych z tych dzienników można uzyskać za pomocą portalu. Wszystkie dzienniki można wyodrębnić z usługi Azure Blob Storage i przeglądać w różnych narzędziach, takich jak program Excel i usługa PowerBI. Więcej informacji o różnych typach dzienników można znaleźć na poniższej liście.

* **Dzienniki inspekcji:** Możesz użyć [dzienników inspekcji platformy Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (dawniej nazywanych dziennikami operacyjnymi), aby wyświetlić wszystkie operacje przesyłane do subskrypcji platformy Azure i ich stan. Dzienniki inspekcji są domyślnie włączone i można je wyświetlić w Azure Portal.
* **Dzienniki zdarzeń alertu:** Ten dziennik służy do wyświetlania alertów wygenerowanych przez moduł równoważenia obciążenia. Stan usługi równoważenia obciążenia jest zbierany co pięć minut. Ten dziennik jest zapisywana tylko w przypadku zgłoszenia zdarzenia alertu modułu równoważenia obciążenia.
* **Dzienniki sondowania kondycji:** Ten dziennik służy do wyświetlania problemów wykrytych przez sondę kondycji, takich jak liczba wystąpień w puli zaplecza, które nie otrzymują żądań z modułu równoważenia obciążenia z powodu błędów sondy kondycji. Ten dziennik jest zapisywana w przypadku zmiany stanu sondy kondycji.

> [!IMPORTANT]
> Dzienniki Azure Monitor są obecnie przeznaczone tylko dla publicznych usług równoważenia obciążenia. Dzienniki są dostępne tylko dla zasobów wdrożonych w modelu wdrażania Menedżer zasobów. Nie można używać dzienników dla zasobów w klasycznym modelu wdrażania. Aby uzyskać więcej informacji na temat modeli wdrażania, zobacz [opis Menedżer zasobów wdrożenia i wdrożenia klasycznego](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Włącz rejestrowanie

Rejestrowanie inspekcji jest automatycznie włączane dla każdego Menedżer zasobów zasobu. Musisz włączyć rejestrowanie sondowania zdarzeń i kondycji, aby rozpocząć zbieranie danych dostępnych w tych dziennikach. Wykonaj następujące kroki, aby włączyć rejestrowanie.

Zaloguj się do [Azure Portal](https://portal.azure.com). Jeśli nie masz jeszcze modułu równoważenia obciążenia, przed kontynuowaniem [Utwórz moduł równoważenia obciążenia](load-balancer-get-started-internet-arm-ps.md) .

1. W portalu kliknij przycisk **Przeglądaj**.
2. Wybierz pozycję usługi **równoważenia obciążenia**.

    ![Portal — Równoważenie obciążenia](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Wybierz istniejący moduł równoważenia obciążenia > > **wszystkie ustawienia**.
4. Po prawej stronie okna dialogowego pod nazwą modułu równoważenia obciążenia przewiń do pozycji **monitorowanie**, kliknij pozycję **Diagnostyka**.

    ![Portal — Równoważenie obciążenia — ustawienia](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. W okienku **Diagnostyka** w obszarze **stan**wybierz pozycję **włączone**.
6. Kliknij pozycję **konto magazynu**.
7. W obszarze **dzienniki**Wybierz istniejące konto magazynu lub Utwórz nowe. Użyj suwaka, aby określić, ile dni dane zdarzeń będą przechowywane w dziennikach zdarzeń. 
8. Kliknij polecenie **Zapisz**.

Diagnostyka zostanie zapisana w Table Storage na określonym koncie magazynu. Jeśli dzienniki nie są zapisywane, jest to spowodowane tym, że nie są generowane żadne odpowiednie dzienniki.

![Portal — dzienniki diagnostyki](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Dzienniki inspekcji nie wymagają oddzielnego konta magazynu. Użycie magazynu do rejestrowania zdarzeń i sondowania kondycji spowoduje naliczenie opłat za usługę.

## <a name="audit-log"></a>Dziennik inspekcji

Dziennik inspekcji jest domyślnie generowany. Dzienniki są przechowywane przez 90 dni w magazynie dzienników zdarzeń platformy Azure. Dowiedz się więcej o tych dziennikach, odczytując artykuł [Wyświetlanie zdarzeń i dzienników inspekcji](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="alert-event-log"></a>Dziennik zdarzeń alertu

Ten dziennik jest generowany tylko wtedy, gdy włączono go na podstawie usługi równoważenia obciążenia. Zdarzenia są rejestrowane w formacie JSON i przechowywane na koncie magazynu określonym podczas włączania rejestrowania. Poniżej znajduje się przykład zdarzenia.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

W danych wyjściowych JSON  zostanie wyświetlona Właściwość EventName opisująca przyczynę modułu równoważenia obciążenia, który utworzył alert. W takim przypadku wygenerowany alert był spowodowany wyczerpaniem portów TCP spowodowanym przez limity NAT źródła IP.

## <a name="health-probe-log"></a>Dziennik sondy kondycji

Ten dziennik jest generowany tylko wtedy, gdy włączono go na podstawie usługi równoważenia obciążenia, jak opisano powyżej. Dane są przechowywane na koncie magazynu określonym podczas włączania rejestrowania. Utworzono kontener o nazwie "Insights-Logs-loadbalancerprobehealthstatus" i rejestrowane są następujące dane:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

Dane wyjściowe JSON są wyświetlane w polu właściwości podstawowe informacje o stanie kondycji sondy. Właściwość *dipDownCount* pokazuje całkowitą liczbę wystąpień na zapleczu, które nie odbierają ruchu sieciowego z powodu niepowodzeń odpowiedzi sondy.

## <a name="view-and-analyze-the-audit-log"></a>Wyświetlanie i analizowanie dziennika inspekcji

Można wyświetlać i analizować dane dziennika inspekcji przy użyciu dowolnej z następujących metod:

* **Narzędzia platformy Azure:** Pobierz informacje z dzienników inspekcji za pośrednictwem Azure PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST platformy Azure lub portalu Azure w wersji zapoznawczej. Instrukcje krok po kroku dla każdej metody opisano szczegółowo w operacjach [inspekcji w Menedżer zasobów](../azure-resource-manager/resource-group-audit.md) artykule.
* **Power BI:** Jeśli nie masz jeszcze konta [Power BI](https://powerbi.microsoft.com/pricing) , możesz wypróbować go bezpłatnie. Za pomocą [pakietu zawartości dzienników inspekcji platformy Azure dla Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs)można analizować dane przy użyciu wstępnie skonfigurowanych pulpitów nawigacyjnych lub dostosować widoki zgodnie z wymaganiami.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Wyświetlanie i analizowanie sondy kondycji i dziennika zdarzeń

Musisz nawiązać połączenie z kontem magazynu i pobrać wpisy dziennika JSON dla dzienników sondowania zdarzeń i kondycji. Po pobraniu plików JSON można je przekonwertować na woluminy CSV i przeglądać w programie Excel, usługi PowerBI lub dowolnym innym narzędziu do wizualizacji danych.

> [!TIP]
> Jeśli znasz program Visual Studio oraz podstawowe pojęcia dotyczące zmiany wartości stałych i zmiennych w języku C#, możesz skorzystać z [konwerterów dzienników](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępnych w witrynie GitHub.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wizualizuj dzienniki inspekcji platformy Azure za pomocą wpisu w blogu Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
* [Wyświetlanie i analizowanie dzienników inspekcji platformy Azure w Power BI i więcej wpisów w](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogu.

## <a name="next-steps"></a>Kolejne kroki

[Opis sond modułu równoważenia obciążenia](load-balancer-custom-probe-overview.md)
