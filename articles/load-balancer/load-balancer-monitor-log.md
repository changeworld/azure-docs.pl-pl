---
title: Monitorowanie operacji, zdarzeń i liczników dla publicznego podstawowego modułu równoważenia obciążenia
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak włączyć zdarzenia alertów i rejestrować stan sondowania dla publicznego podstawowego modułu równoważenia obciążenia
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: 0a21af683d9fa7849d3e96c545983c9f40a8d4c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935333"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Dzienniki usługi Azure Monitor dla usługi Load Balancer w warstwie Podstawowa

>[!IMPORTANT]
>Usługa Azure Load Balancer obsługuje dwie warstwy: Podstawowa i Standardowa. W tym artykule omówiono usługę Load Balancer w warstwie Podstawowa. Aby uzyskać więcej informacji na temat standardowego modułu równoważenia obciążenia, zobacz [omówienie standardowego modułu równoważenia obciążenia,](load-balancer-standard-overview.md) które udostępnia dane telemetryczne za pomocą metryk wielowymiarowych w usłudze Azure Monitor.

Można używać różnych typów dzienników na platformie Azure do zarządzania podstawowymi modułami równoważenia obciążenia i rozwiązywania problemów z nimi. Niektóre z tych dzienników są dostępne za pośrednictwem portalu. Dzienniki mogą być przesyłane strumieniowo do centrum zdarzeń lub obszaru roboczego usługi Log Analytics. Wszystkie dzienniki można wyodrębnić z magazynu obiektów blob platformy Azure i wyświetlać w różnych narzędziach, takich jak Excel i Power BI.  Możesz dowiedzieć się więcej o różnych typach dzienników z poniższej listy.

* **Dzienniki aktywności:** Za pomocą funkcji Wyświetl dzienniki aktywności można [monitorować akcje dotyczące zasobów,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) aby wyświetlić wszystkie działania przesyłane do subskrypcji platformy Azure i ich stan. Dzienniki aktywności są domyślnie włączone i można je wyświetlać w witrynie Azure portal.
* **Dzienniki zdarzeń alertów:** Za pomocą tego dziennika można wyświetlać alerty wywoływane przez moduł równoważenia obciążenia. Stan modułu równoważenia obciążenia jest zbierany co pięć minut. Ten dziennik jest zapisywany tylko wtedy, gdy zdarzenie alertu modułu równoważenia obciążenia jest wywoływane.
* **Dzienniki sondy kondycji:** Można użyć tego dziennika do wyświetlania problemów wykrytych przez sondę kondycji, takich jak liczba wystąpień w puli wewnętrznej bazy danych, które nie otrzymują żądań od modułu równoważenia obciążenia z powodu błędów sondy kondycji. Ten dziennik jest zapisywany, gdy nastąpi zmiana stanu sondy kondycji.

> [!IMPORTANT]
> Dzienniki usługi Azure Monitor działają obecnie tylko w przypadku publicznych modułów równoważenia obciążenia basic. Dzienniki są dostępne tylko dla zasobów wdrożonych w modelu wdrażania Menedżera zasobów. Nie można używać dzienników dla zasobów w klasycznym modelu wdrażania. Aby uzyskać więcej informacji na temat modeli wdrażania, zobacz [Opis wdrażania Menedżera zasobów i wdrożenia klasycznego](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Włącz rejestrowanie

Rejestrowanie aktywności jest automatycznie włączone dla wszystkich zasobów usługi Resource Manager. Włącz rejestrowanie sondy zdarzeń i kondycji, aby rozpocząć zbieranie danych dostępnych za pośrednictwem tych dzienników. Aby włączyć rejestrowanie, należy wykonać następujące czynności.

Zaloguj się do [Portalu Azure](https://portal.azure.com). Jeśli nie masz jeszcze modułu równoważenia obciążenia, [utwórz moduł równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) przed kontynuowaniem.

1. W portalu kliknij pozycję **Grupy zasobów**.
2. Wybierz ** \<nazwę grupy zasobów>** miejsca, w którym znajduje się moduł równoważenia obciążenia.
3. Wybierz moduł równoważenia obciążenia.
4. Wybierz **opcję Monitorowanie** > **ustawień diagnostycznych**.
5. W okienku **Ustawienia diagnostyki** w obszarze **Ustawienia diagnostyki**wybierz pozycję **+ Dodaj ustawienie diagnostyczne**.
6. W okienku tworzenia **ustawień diagnostyki** wprowadź **myLBDiagnostics** w polu **Nazwa.**
7. Dostępne są trzy opcje **ustawień diagnostyki**.  Możesz wybrać jeden, dwa lub wszystkie trzy i skonfigurować każdy z nich dla swoich wymagań:
   * **Archiwizuj na koncie magazynu**
   * **Przesyłanie strumieniowe do centrum zdarzeń**
   * **Wysyłanie do usługi Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Zarchiwizuj na koncie magazynu
    Będziesz potrzebować konta magazynu już utworzonego dla tego procesu.  Aby utworzyć konto magazynu, zobacz [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Zaznacz pole wyboru obok **pozycji Archiwum na koncie magazynu**.
    2. Wybierz **pozycję Konfiguruj,** aby otworzyć okienko **Wybierz konto magazynu.**
    3. Wybierz **subskrypcję,** w której utworzono konto magazynu w polu rozwijanych.
    4. Wybierz nazwę konta magazynu w obszarze **Konto magazynu** w polu rozwijanego.
    5. Wybierz przycisk OK.

    ### <a name="stream-to-an-event-hub"></a>Przesyłaj strumieniowo do centrum zdarzeń
    Będziesz potrzebować centrum zdarzeń już utworzonego dla tego procesu.  Aby utworzyć centrum zdarzeń, zobacz [Szybki start: tworzenie centrum zdarzeń przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Zaznacz pole wyboru obok **pozycji Strumień do centrum zdarzeń**
    2. Wybierz **pozycję Konfiguruj,** aby otworzyć okienko **Centrum zdarzeń Wybierz.**
    3. Wybierz **subskrypcję,** w której centrum zdarzeń zostało utworzone w polu rozwijanego.
    4. **Wybierz obszar nazw centrum zdarzeń** w polu rozwijany.
    5. **Wybierz nazwę zasad centrum zdarzeń** w polu rozwijany.
    6. Wybierz przycisk OK.

    ### <a name="send-to-log-analytics"></a>Wysyłanie do usługi Log Analytics
    Musisz już mieć obszar roboczy analizy dziennika utworzony i skonfigurowany dla tego procesu.  Aby utworzyć obszar roboczy usługi Log Analytics, zobacz [Tworzenie obszaru roboczego usługi Log Analytics w portalu Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Zaznacz pole wyboru obok **pozycji Wyślij do usługi Log Analytics**.
    2. Wybierz **subskrypcję,** w której obszar roboczy usługi Log Analytics znajduje się w polu rozwijany.
    3. Wybierz **obszar roboczy usługi Log Analytics** w polu rozwijany.


8. W sekcji **LOG** w okienku **Ustawienia diagnostyki** zaznacz pole wyboru obok obu:
   * **LoadBalancerAlertEvent (Równourza obciążenia)**
   * **LoadBalancerProbeHealthStatus**

9.  W sekcji **METRYKA** w okienku **Ustawienia diagnostyki** zaznacz pole wyboru obok:
   * **AllMetrics**

11. Sprawdź, czy wszystko wygląda poprawnie i kliknij przycisk **Zapisz** u góry okienka **ustawienia tworzenia diagnostyki.**

## <a name="activity-log"></a>Dziennik aktywności

Dziennik aktywności jest generowany domyślnie. Dzienniki są zachowywane przez 90 dni w magazynie dzienników zdarzeń platformy Azure. Dowiedz się więcej o tych dziennikach, czytając [zobacz dzienniki aktywności w celu monitorowania akcji w zasobach.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)

## <a name="archive-to-storage-account-logs"></a>Archiwizuj dzienniki kont magazynu

### <a name="alert-event-log"></a>Dziennik zdarzeń alertów

Ten dziennik jest generowany tylko wtedy, gdy włączono go na podstawie modułu równoważenia obciążenia. Zdarzenia są rejestrowane w formacie JSON i przechowywane na koncie magazynu określonym po włączeniu rejestrowania. Poniższy przykład jest zdarzeniem.

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

Dane wyjściowe JSON pokazuje właściwość *eventname,* która opisze przyczynę utworzenia alertu przez moduł równoważenia obciążenia. W takim przypadku wygenerowany alert był spowodowany wyczerpaniem portu TCP spowodowanym przez źródłowe limity NAT IP (SNAT).

### <a name="health-probe-log"></a>Dziennik sondy kondycji

Ten dziennik jest generowany tylko wtedy, gdy włączono go na podstawie modułu równoważenia obciążenia, jak opisano powyżej. Dane są przechowywane na koncie magazynu określonym po włączeniu rejestrowania. Kontener o nazwie "insights-logs-loadbalancerprobehealthstatus" jest tworzony i rejestrowane są następujące dane:

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

Dane wyjściowe JSON pokazuje w polu właściwości podstawowe informacje o stanie kondycji sondy. Właściwość *dipDownCount* pokazuje całkowitą liczbę wystąpień na zapleczu, które nie odbierają ruchu sieciowego z powodu nieudanych odpowiedzi sondy.

### <a name="view-and-analyze-the-activity-log"></a>Wyświetlanie i analizowanie dziennika aktywności

Można wyświetlać i analizować dane dziennika aktywności przy użyciu dowolnej z następujących metod:

* **Narzędzia platformy Azure:** Pobieranie informacji z dziennika aktywności za pośrednictwem usługi Azure PowerShell, interfejsu wiersza polecenia platformy Azure (CLI), interfejsu API rest platformy Azure lub witryny Azure portal. Instrukcje krok po kroku dla każdej metody są szczegółowo opisane w [inspekcji operacji z Menedżerem zasobów](../azure-resource-manager/management/view-activity-logs.md) artykułu.
* **Usługa Power BI:** Jeśli nie masz jeszcze konta [usługi Power BI,](https:// .microsoft.com/pricing) możesz wypróbować je bezpłatnie. Korzystając z [pakietu zawartości Dzienniki inspekcji platformy Azure dla usługi Power BI,](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)można analizować dane za pomocą wstępnie skonfigurowanych pulpitów nawigacyjnych lub dostosowywać widoki do własnych wymagań.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Wyświetlanie i analizowanie sondy kondycji i dziennika zdarzeń

Połącz się z kontem magazynu i pobierz wpisy dziennika JSON dla dzienników sondy zdarzeń i kondycji. Po pobraniu plików JSON można je przekonwertować na csv i wyświetlić w programie Excel, Power BI lub innym narzędziu do wizualizacji danych.

> [!TIP]
> Jeśli znasz program Visual Studio oraz podstawowe pojęcia dotyczące zmiany wartości stałych i zmiennych w języku C#, możesz skorzystać z [konwerterów dzienników](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępnych w witrynie GitHub.

## <a name="stream-to-an-event-hub"></a>Przesyłaj strumieniowo do centrum zdarzeń
Gdy informacje diagnostyczne są przesyłane strumieniowo do centrum zdarzeń, może służyć do scentralizowanej analizy dziennika w narzędziu SIEM innej firmy z integracją usługi Azure Monitor. Aby uzyskać więcej informacji, zobacz [Strumieniowanie danych monitorowania platformy Azure do centrum zdarzeń](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Wysyłanie do usługi Log Analytics
Zasoby na platformie Azure mogą mieć ich informacje diagnostyczne wysyłane bezpośrednio do obszaru roboczego usługi Log Analytics, gdzie złożone zapytania mogą być uruchamiane z informacjami dotyczącymi rozwiązywania problemów i analizy.  Aby uzyskać więcej informacji, zobacz [Zbieranie dzienników zasobów platformy Azure w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>Następne kroki

[Opis sond modułu równoważenia obciążenia](load-balancer-custom-probe-overview.md)
