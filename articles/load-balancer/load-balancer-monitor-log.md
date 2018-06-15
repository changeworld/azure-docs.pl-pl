---
title: Monitorowanie działań, zdarzenia i liczniki dla usługi równoważenia obciążenia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć zdarzenia alertu i sondy kondycji stanu rejestrowania dla usługi równoważenia obciążenia Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 56656d74-0241-4096-88c8-aa88515d676d
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: dabf4bcae957559978e731636bb13554f1a68b73
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30179059"
---
# <a name="log-analytics-for-azure-load-balancer"></a>Analiza dzienników dotyczących usługi Azure Load Balancer

>[!NOTE] 
>Usługa Azure Load Balancer obsługuje dwie warstwy: Podstawowa i Standardowa. W tym artykule omówiono usługę Load Balancer w warstwie Podstawowa. Aby uzyskać więcej informacji na temat standardowe usługi równoważenia obciążenia, zobacz [Omówienie usługi równoważenia obciążenia standardowego](load-balancer-standard-overview.md).

Różne typy dzienników Azure umożliwia zarządzanie i rozwiązywanie problemów z usługi równoważenia obciążenia. Niektóre z tych dzienników jest możliwy za pośrednictwem portalu. Wszystkie dzienniki można wyodrębnić z magazynu obiektów blob platformy Azure i wyświetlane w różnych narzędzi, takich jak program Excel i Power BI. Możesz można dowiedzieć się więcej o różnych typach dzienników z poniższej listy.

* **Dzienniki inspekcji:** można użyć [dzienników inspekcji platformy Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (wcześniej znane jako operacyjne dzienniki), aby wyświetlić wszystkie operacje przesyłany do Twojej subskrypcji platformy Azure i ich stan. Dzienniki inspekcji są domyślnie włączone i mogą być wyświetlane w portalu Azure.
* **Dzienniki zdarzeń alertów:** ten dziennik służy do wyświetlenia alerty wygenerowane przez moduł równoważenia obciążenia. Stan usługi równoważenia obciążenia są gromadzone co pięć minut. Ten dziennik napisano tylko, jeśli zdarzenia alertu modułu równoważenia obciążenia jest wywoływane.
* **Dzienniki badania kondycji:** ten dziennik służy do wyświetlania problemów wykrytych przez użytkownika sondy kondycji, takie jak liczba wystąpień w puli zaplecza, które nie są odbierane żądań z modułu równoważenia obciążenia z powodu błędów sondy kondycji. Ten dziennik jest zapisywany po zmianie stanu sondy kondycji.

> [!IMPORTANT]
> Zaloguj się analytics jest obecnie obsługiwane tylko w przypadku internetowy usług równoważenia obciążenia. Dzienniki są dostępne tylko dla zasobów wdrożone w modelu wdrażania usługi Resource Manager. Nie można używać dzienników zasobów w klasycznym modelu wdrażania. Aby uzyskać więcej informacji na temat modeli wdrażania, zobacz [wdrożenia Understanding Resource Manager oraz wdrażania klasycznego](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Włącz rejestrowanie

Rejestrowanie inspekcji jest automatycznie włączona dla każdego zasobu usługi Resource Manager. Musisz włączyć zdarzeń i rejestrowania sondy kondycji w celu rozpoczęcia, zbierania danych dostępne za pośrednictwem tych dzienników. Wykonaj następujące kroki, aby włączyć rejestrowanie.

Zaloguj się do [portalu Azure](http://portal.azure.com). Jeśli nie masz już usługę równoważenia obciążenia, [tworzenia modułu równoważenia obciążenia](load-balancer-get-started-internet-arm-ps.md) przed kontynuowaniem.

1. W portalu kliknij **Przeglądaj**.
2. Wybierz **usługi równoważenia obciążenia**.

    ![Portal — moduł równoważenia obciążenia](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Wybierz istniejący moduł równoważenia obciążenia >> **wszystkie ustawienia**.
4. Po prawej stronie okna dialogowego pod nazwą usługi równoważenia obciążenia, przewiń do **monitorowanie**, kliknij przycisk **diagnostyki**.

    ![Portal — ustawienia usługi równoważenia obciążenia](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. W **diagnostyki** okienku w obszarze **stan**, wybierz pozycję **na**.
6. Kliknij przycisk **konta magazynu**.
7. W obszarze **DZIENNIKI**, wybrać istniejące konto magazynu lub Utwórz nową. Aby określić liczbę dni ważności danych zdarzenia będą przechowywane w dziennikach zdarzeń za pomocą suwaka. 
8. Kliknij pozycję **Zapisz**.

    ![Portal — dzienniki diagnostyczne](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Dzienniki inspekcji nie wymagają oddzielnego konta magazynu. Użycie magazynu dla zdarzeń i kondycji sondowania rejestrowania będą naliczane opłaty za usługę.

## <a name="audit-log"></a>Dziennik inspekcji

Dziennik inspekcji jest generowany domyślnie. Dzienniki są zachowywane przez 90 dni w magazynie dzienniki zdarzeń platformy Azure. Dowiedz się więcej o tych dzienników, odczytując [wyświetlania zdarzeń i dzienniki inspekcji](../monitoring-and-diagnostics/insights-debugging-with-events.md) artykułu.

## <a name="alert-event-log"></a>Dziennik zdarzeń alertów

Ten dziennik jest generowany tylko, jeśli włączono na poszczególnych usługi równoważenia obciążenia. Zdarzenia są rejestrowane w formacie JSON i przechowywane na koncie magazynu, określone po włączeniu rejestrowania. Oto przykład zdarzenia.

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

Przedstawia dane wyjściowe JSON *eventname* właściwość, która będzie opisywać Przyczyna dla usługi równoważenia obciążenia utworzony alert. W takim przypadku alert wygenerowany został z powodu wyczerpania port TCP spowodowane przez źródło IP translatora adresów Sieciowych limity (SNAT).

## <a name="health-probe-log"></a>Dziennik badania kondycji

Ten dziennik jest generowany tylko, jeśli włączono na poszczególnych obciążenia równoważenia zgodnie z opisem powyżej. Dane są przechowywane na koncie magazynu, określone po włączeniu rejestrowania. Kontener o nazwie "insights dzienniki loadbalancerprobehealthstatus" jest tworzony i rejestrowane są następujące dane:

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

Dane wyjściowe JSON w polu właściwości zawiera podstawowe informacje dotyczące stanu kondycji sondowania. *DipDownCount* właściwość zawiera całkowitą liczbę wystąpień na zaplecza, które nie są odbierane ruchu sieciowego z powodu odpowiedzi sondy nie powiodło się.

## <a name="view-and-analyze-the-audit-log"></a>Wyświetlanie i analizowanie dzienników inspekcji

Można wyświetlać i analizować dane dzienników inspekcji przy użyciu dowolnej z następujących metod:

* **Narzędzia platformy Azure:** pobieranie informacji z dzienników inspekcji za pośrednictwem programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure (CLI), interfejsu API REST Azure lub w portalu Azure w wersji zapoznawczej. Instrukcje krok po kroku dla każdej metody wyszczególnione w [inspekcji operacji za pomocą Menedżera zasobów](../azure-resource-manager/resource-group-audit.md) artykułu.
* **Usługa Power BI:** Jeśli jeszcze nie masz [usługi Power BI](https://powerbi.microsoft.com/pricing) konta, możesz spróbować ją bezpłatnie. Przy użyciu [dzienników inspekcji platformy Azure zawartości pakietu dla usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs)można analizować danych za pomocą wstępnie skonfigurowanych pulpitów nawigacyjnych i można dostosowywać widoki ze swoimi potrzebami.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Wyświetlanie i analizowanie sondy kondycji i dziennika zdarzeń

Należy do łączenia się z kontem magazynu i pobierania JSON wpisów dziennika zdarzeń i kondycji dzienników sondowania. Po pobraniu pliki w formacie JSON, należy przekonwertować je na CSV i widoku w programie Excel, usłudze PowerBI lub inne narzędzie do wizualizacji danych.

> [!TIP]
> Jeśli znasz podstawowe koncepcje zmiany wartości stałych i zmiennych w języku C# i Visual Studio, możesz użyć [dziennika narzędzia konwertera](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępne w serwisie GitHub.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wizualizuj dzienników inspekcji Azure przy użyciu usługi Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) wpis w blogu.
* [Wyświetlanie i analizowanie dzienników inspekcji platformy Azure w usłudze Power BI i nie tylko](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) wpis w blogu.

## <a name="next-steps"></a>Kolejne kroki

[Opis sond modułu równoważenia obciążenia](load-balancer-custom-probe-overview.md)
