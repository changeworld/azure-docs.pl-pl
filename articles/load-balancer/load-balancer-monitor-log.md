---
title: Monitorowanie operacji, zdarzenia i liczniki publiczny podstawowy moduł równoważenia obciążenia
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak włączyć zdarzenia alarmowe i sondy kondycji stanu rejestrowania publiczny podstawowy moduł równoważenia obciążenia
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: kumud
ms.openlocfilehash: 0d7c792c5230a5d82e97f4598a5dcfb864cead74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861171"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Usługa Azure Monitor dzienniki publiczny podstawowy moduł równoważenia obciążenia

>[!IMPORTANT] 
>Usługa Azure Load Balancer obsługuje dwa różne typy: Podstawowa i Standardowa. W tym artykule omówiono usługę Load Balancer w warstwie Podstawowa. Aby uzyskać więcej informacji na temat Balancer w warstwie standardowa, zobacz [omówienie Standard Load Balancer](load-balancer-standard-overview.md) która udostępnia dane telemetryczne za pomocą metryk wielowymiarowych w usłudze Azure Monitor.

Różne typy dzienników platformy Azure umożliwia zarządzanie i rozwiązywanie problemów podstawowe usługi równoważenia obciążenia. Niektóre z tych dzienników jest możliwy za pośrednictwem portalu. Wszystkie dzienniki można wyodrębnić z usługi Azure blob storage i wyświetlane w różnych narzędzi, takich jak program Excel i usługi Power BI. Można znaleźć więcej informacji na temat różnych typów dzienników z poniższej listy.

* **Dzienniki inspekcji:** Możesz użyć [dzienników inspekcji platformy Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (wcześniej znane jako dzienniki operacyjne) aby wyświetlić wszystkie operacje są przesyłane do subskrypcji platformy Azure i ich stan. Dzienniki inspekcji są domyślnie włączone i mogą być wyświetlane w witrynie Azure portal.
* **Dzienniki zdarzeń alertów:** Korzystanie z tego dziennika, aby wyświetlić alerty wygenerowane przez moduł równoważenia obciążenia. Stan modułu równoważenia obciążenia jest zbieranych co pięć minut. Tylko ten dziennik jest zapisywany, jeśli zdarzenie alertu modułu równoważenia obciążenia jest zgłaszane w.
* **Dzienników sond kondycji:** Ten dziennik służy do wyświetlania problemów wykrytych przez Twoje sondę kondycji, np. liczbę wystąpień w puli zaplecza, które nie otrzymuje żądania z modułu równoważenia obciążenia z powodu niepowodzeń sondy kondycji. Ten dziennik jest zapisywany po zmianę stanu sondy kondycji.

> [!IMPORTANT]
> Usługa Azure Monitor rejestruje obecnie działa tylko w przypadku publiczne podstawowe moduły równoważenia obciążenia. Dzienniki są dostępne tylko dla zasobów wdrożonych w modelu wdrażania usługi Resource Manager. Nie można używać dzienników zasobów w klasycznym modelu wdrażania. Aby uzyskać więcej informacji na temat modeli wdrażania, zobacz [wdrażania Understanding Resource Manager oraz wdrażania klasycznego](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Włącz rejestrowanie

Rejestrowanie inspekcji jest automatycznie włączona dla wszystkich zasobów usługi Resource Manager. Musisz włączyć zdarzeń i rejestrowania sondy kondycji w celu rozpoczęcia zbierania danych dostępne za pośrednictwem tych dzienników. Wykonaj następujące kroki, aby włączyć rejestrowanie.

Zaloguj się do [witryny Azure portal](https://portal.azure.com). Jeśli nie masz jeszcze usługi równoważenia obciążenia, [Tworzenie usługi load balancer](load-balancer-get-started-internet-arm-ps.md) przed kontynuowaniem.

1. W portalu, kliknij przycisk **Przeglądaj**.
2. Wybierz **moduły równoważenia obciążenia**.

    ![Portal — moduł równoważenia obciążenia](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Wybieranie istniejącego modułu równoważenia obciążenia >> **wszystkie ustawienia**.
4. Po prawej stronie okna dialogowego, w obszarze nazwy modułu równoważenia obciążenia, przewiń do **monitorowanie**, kliknij przycisk **diagnostyki**.

    ![Portal — ustawienia usługi równoważenia obciążenia](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. W **diagnostyki** okienku w obszarze **stan**, wybierz opcję **na**.
6. Kliknij przycisk **konta magazynu**.
7. W obszarze **DZIENNIKI**, wybierz istniejące konto magazynu lub Utwórz nową. Za pomocą suwaka, aby ustalić, ile dni ważności danych zdarzenia będą przechowywane w dziennikach zdarzeń. 
8. Kliknij pozycję **Zapisz**.

Diagnostyka zostaną zapisane w usłudze Table Storage na koncie magazynu określonym. Jeśli dzienniki nie są zapisywane, jest to, ponieważ nie ma odpowiednich dzienników jest generowany.

![Portal — dzienniki diagnostyczne](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Dzienniki inspekcji nie wymagają oddzielnego konta magazynu. Użycie magazynu na potrzeby zdarzeń i kondycji sondy rejestrowania będą naliczane opłaty za usługę.

## <a name="audit-log"></a>Dziennik inspekcji

Domyślnie generowany jest dziennik inspekcji. Dzienniki są zachowywane przez 90 dni w magazynie dzienniki zdarzeń platformy Azure. Dowiedz się więcej na temat tych dzienników, zapoznając [wyświetlania zdarzeń i dzienników inspekcji](../monitoring-and-diagnostics/insights-debugging-with-events.md) artykułu.

## <a name="alert-event-log"></a>Dziennik zdarzeń alertów

Ten dziennik jest generowany tylko, jeśli włączono na poszczególnych modułu równoważenia obciążenia. Zdarzenia są rejestrowane w formacie JSON i przechowywane na koncie magazynu, określony po włączeniu rejestrowania. Oto przykład zdarzenia.

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

Dane wyjściowe JSON *eventname* właściwość, która będzie opisywać Przyczyna dla modułu równoważenia obciążenia utworzony alert. W tym przypadku alercie wygenerowanym była spowodowana przez źródło IP translatora adresów Sieciowych limitów (SNAT) wyczerpanie portów TCP.

## <a name="health-probe-log"></a>Dziennik sondy kondycji

Ten dziennik jest generowany tylko, jeśli włączono na poszczególnych load balancer zgodnie z opisem powyżej. Dane są przechowywane na koncie magazynu, określony po włączeniu rejestrowania. Tworzenie kontenera o nazwie "ruch loadbalancerprobehealthstatus insights dzienniki" i rejestrowane są następujące dane:

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

Wyświetlone dane wyjściowe JSON w polu właściwości podstawowe informacje dotyczące sondy stanu kondycji. *DipDownCount* właściwość zawiera całkowitą liczbę wystąpień na zaplecza, która nie odbierają ruchu sieciowego z powodu braku odpowiedzi sond.

## <a name="view-and-analyze-the-audit-log"></a>Wyświetlanie i analizowanie dzienników inspekcji

Można wyświetlać i analizować dane dzienników inspekcji przy użyciu dowolnej z następujących metod:

* **Narzędzia platformy Azure:** Pobieranie informacji z dzienników inspekcji za pomocą programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure (CLI), Azure REST API lub portalu Azure w wersji zapoznawczej. Instrukcje krok po kroku dla każdej metody są szczegółowo opisane w [inspekcji operacji przy użyciu usługi Resource Manager](../azure-resource-manager/resource-group-audit.md) artykułu.
* **Power BI:** Jeśli nie masz jeszcze [usługi Power BI](https://powerbi.microsoft.com/pricing) konta, możesz ją wypróbować bezpłatnie. Za pomocą [dzienników inspekcji platformy Azure pakietu zawartości dla usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), można analizować dane przy użyciu wstępnie skonfigurowane pulpity nawigacyjne lub dostosować widoki ze swoimi potrzebami.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Wyświetlanie i analizowanie sondy kondycji oraz dziennik zdarzeń

Musisz połączyć się z kontem magazynu i pobierania wpisów dziennika JSON dla dziennikami sondy kondycji i zdarzeń. Po pobraniu pliki w formacie JSON, możesz przekonwertować je do formatu CSV i widok w programie Excel, Power bi lub inne narzędzie do wizualizacji danych.

> [!TIP]
> Jeśli znasz program Visual Studio oraz podstawowe pojęcia dotyczące zmiany wartości stałych i zmiennych w języku C#, możesz skorzystać z [konwerterów dzienników](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępnych w witrynie GitHub.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wizualizuj dzienniki inspekcji platformy Azure z usługą Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) wpis w blogu.
* [Wyświetlanie i analizowanie dzienników inspekcji platformy Azure w usłudze Power BI i nie tylko](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) wpis w blogu.

## <a name="next-steps"></a>Kolejne kroki

[Opis sond modułu równoważenia obciążenia](load-balancer-custom-probe-overview.md)
