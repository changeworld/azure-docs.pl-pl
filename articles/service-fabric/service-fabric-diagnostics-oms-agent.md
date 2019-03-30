---
title: Dzienniki usługi Azure Service Fabric — monitorowanie wydajności za pomocą usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować agenta usługi Log Analytics do monitorowania kontenerów i liczniki wydajności dla klastrów usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 819f6ee4ab079361279a567bceeb74c33fe14186
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662010"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Monitorowanie wydajności za pomocą dzienników usługi Azure Monitor

W tym artykule opisano kroki, aby dodać agenta usługi Log Analytics, ponieważ rozszerzenie zestawu skalowania maszyny wirtualnej, do klastra i podłącz go do istniejącego obszaru roboczego usługi Azure Log Analytics. Umożliwia to zbieranie danych diagnostycznych o kontenerach, aplikacji i monitorowanie wydajności. Dodając ją jako rozszerzenie do zasobu zestawu skalowania maszyny wirtualnej, usługi Azure Resource Manager zapewnia, że pobiera zainstalowany w każdym węźle, nawet podczas skalowania klastra.

> [!NOTE]
> W tym artykule założono, że masz już skonfigurowany obszar roboczy usługi Azure Log Analytics. Jeśli tego nie zrobisz, przejdź do [Konfigurowanie dzienników usługi Azure Monitor](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Dodaj rozszerzenie agenta za pomocą wiersza polecenia platformy Azure

Najlepszym sposobem, aby dodać agenta usługi Log Analytics do klastra za pośrednictwem skalowania maszyn wirtualnych jest ustawiana interfejsami API dostępnymi z wiersza polecenia platformy Azure. Jeśli nie masz jeszcze skonfigurowane interfejsu wiersza polecenia platformy Azure, przejdź do witryny Azure portal i otworzyć [Cloud Shell](../cloud-shell/overview.md) wystąpienia lub [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Gdy wymagane są usługi Cloud Shell, upewnij się, że pracujesz w tej samej subskrypcji co zasób. Sprawdź to za pomocą `az account show` i upewnij się, wartości "name" zgodne z subskrypcji usługi klastra.

2. W portalu przejdź do grupy zasobów, w którym znajduje się obszar roboczy usługi Log Analytics. Klikaj zasób analizy dziennika (typ zasobu będą obszaru roboczego usługi Log Analytics). Po przejściu na stronie Przegląd zasobów, kliknij pozycję **Zaawansowane ustawienia** w sekcji Ustawienia w menu po lewej stronie.

    ![Strona właściwości analizy dzienników](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Kliknij pozycję **serwerów Windows** jeśli są stałego klaster Windows i **serwerów z systemem Linux** w przypadku tworzenia klastra z systemem Linux. Ta strona zostanie wyświetlona Twoja `workspace ID` i `workspace key` (oznaczone jako klucza podstawowego w portalu). Należy zarówno do kolejnego kroku.

4. Uruchom polecenie, aby zainstalować agenta usługi Log Analytics do klastra, przy użyciu `vmss extension set` interfejs API usługi Cloud Shell:

    W przypadku klastra Windows:

    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    W przypadku klastra systemu Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Oto przykład agenta usługi Log Analytics są dodane do klastra Windows.

    ![Zaloguj się Analytics agent polecenia interfejsu wiersza polecenia](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Powinno to zająć mniej niż 15 minut, aby dodać agenta do węzłów. Możesz sprawdzić, czy agenci zostały dodane za pomocą `az vmss extension list` interfejsu API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Dodawanie agenta za pomocą szablonu usługi Resource Manager

Szablony przykładowych Resource Manager, Wdróż obszaru roboczego usługi Azure Log Analytics i Dodaj agenta do każdego z węzłów, które jest dostępne dla [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) lub [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Możesz pobrać i zmodyfikować ten szablon służy do wdrażania klastra, który najlepiej odpowiada Twoim potrzebom.

## <a name="view-performance-counters"></a>Przeglądanie liczników wydajności

Teraz, że dodano agenta usługi Log Analytics, head w tryb failover do portalu usługi Log Analytics, aby wybrać, liczniki wydajności, które chcesz zbierać.

1. W witrynie Azure portal przejdź do grupy zasobów, w którym utworzono rozwiązania analiza usługi Service Fabric. Wybierz **ServiceFabric\<nameOfLog AnalyticsWorkspace\>**.

2. Kliknij pozycję **Log Analytics**.

3. Kliknij przycisk **Zaawansowane ustawienia**.

4. Kliknij przycisk **danych**, następnie kliknij przycisk **Windows lub Linux, liczniki wydajności**. Znajduje się lista domyślne liczniki, które użytkownik może włączyć i interwał dla kolekcji można ustawić także. Można również dodać [dodatkowe liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md) do zbierania. W tym odwołuje się do prawidłowego formatu [artykułu](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Kliknij przycisk **Zapisz**, następnie kliknij przycisk **OK**.

6. Zamknij blok ustawienia zaawansowane.

7. Pod nagłówkiem Ogólne kliknij **podsumowanie obszaru roboczego**.

8. Zostaną wyświetlone Kafelki w formie wykresu dla każdego rozwiązania włączone, w tym dla usługi Service Fabric. Kliknij przycisk **usługi Service Fabric** wykres, aby kontynuować do rozwiązania analiza usługi Service Fabric.

9. Na kanał operacyjny i zdarzenia interfejsu reliable services, pojawi się kilka Kafelki z wykresami. Graficzna reprezentacja danych przepływających w wybranych liczników będą wyświetlane metryki węzła.

10. Kliknij wykres metryki kontenera, aby wyświetlić dodatkowe szczegóły. Można także badać dane licznika wydajności, podobnie jak filtrować dane według węzłów, nazwa licznika wydajności i wartości za pomocą języka zapytania Kusto i zdarzenia klastra.

![Zapytanie licznika wydajności analizy dzienników](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Kolejne kroki

* Zbierz odpowiednie [liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md). Aby skonfigurować agenta usługi Log Analytics można zebrać liczników wydajności określonych, zapoznaj się z [Konfigurowanie źródeł danych](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources).
* Skonfiguruj dzienniki usługi Azure Monitor, aby skonfigurować [automatyczne alerty](../log-analytics/log-analytics-alerts.md) ułatwiające wykrywanie i przeprowadzanie diagnostyki
* Alternatywnie można zebrać liczników wydajności za pośrednictwem [rozszerzenie diagnostyki platformy Azure i wysyłać je do usługi Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
