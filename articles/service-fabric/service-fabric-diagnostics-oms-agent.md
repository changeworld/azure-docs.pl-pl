---
title: Monitorowanie wydajności za pomocą dzienników usługi Azure Monitor
description: Dowiedz się, jak skonfigurować agenta analizy dzienników do monitorowania kontenerów i liczników wydajności dla klastrów sieci szkieletowej usług Azure.
author: srrengar
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: c3c1bf511f3313e7408d6ce90b73de60bd1309f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366749"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Monitorowanie wydajności za pomocą dzienników usługi Azure Monitor

W tym artykule opisano kroki, aby dodać agenta usługi Log Analytics jako rozszerzenie zestawu skalowania maszyny wirtualnej do klastra i połączyć go z istniejącym obszarem roboczym usługi Azure Log Analytics. Umożliwia to zbieranie danych diagnostycznych dotyczących kontenerów, aplikacji i monitorowania wydajności. Dodając go jako rozszerzenie do zasobu zestawu skalowania maszyny wirtualnej, usługa Azure Resource Manager zapewnia, że zostanie zainstalowany w każdym węźle, nawet podczas skalowania klastra.

> [!NOTE]
> W tym artykule przyjęto założenie, że masz już skonfigurowany obszar roboczy usługi Azure Log Analytics. Jeśli tego nie zrobisz, przejdź do [konfigurowania dzienników usługi Azure Monitor](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Dodawanie rozszerzenia agenta za pośrednictwem interfejsu wiersza polecenia platformy Azure

Najlepszym sposobem dodania agenta usługi Log Analytics do klastra jest za pośrednictwem interfejsów API zestawu skalowania maszyny wirtualnej dostępnych w interfejsie wiersza polecenia platformy Azure. Jeśli nie masz jeszcze skonfigurowanego interfejsu wiersza polecenia platformy Azure, przejdź do witryny Azure portal i otwórz [wystąpienie powłoki w chmurze](../cloud-shell/overview.md) lub [zainstaluj narzędzie interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli)

1. Po zażądaniu usługi Cloud Shell upewnij się, że pracujesz w tej samej subskrypcji co zasób. Sprawdź to `az account show` i upewnij się, że wartość "nazwa" jest zgodna z wartością subskrypcji klastra.

2. W portalu przejdź do grupy zasobów, w której znajduje się obszar roboczy usługi Log Analytics. Kliknij zasób analizy dzienników (typem zasobu będzie obszar roboczy usługi Log Analytics). Gdy jesteś na stronie przegląd zasobów, kliknij ustawienia **zaawansowane** w sekcji Ustawienia w menu po lewej stronie.

    ![Strona właściwości analizy dzienników](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Kliknij na **serwery systemu Windows,** jeśli stoisz w klastrze systemu Windows, a **serwery Systemu Linux,** jeśli tworzysz klaster systemu Linux. Na tej stronie `workspace ID` pojawi `workspace key` się twój i (wymieniony jako klucz podstawowy w portalu). Będziesz potrzebował obu do następnego kroku.

4. Uruchom polecenie, aby zainstalować agenta usługi Log `vmss extension set` Analytics w klastrze przy użyciu interfejsu API:

    W przypadku klastra systemu Windows:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Dla klastra Linuksa:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Oto przykład agenta usługi Log Analytics dodawany do klastra systemu Windows.

    ![Polecenie cli agenta usługi Log Analytics](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Powinno to potrwać mniej niż 15 minut, aby pomyślnie dodać agenta do węzłów. Można sprawdzić, czy agenci zostały dodane `az vmss extension list` przy użyciu interfejsu API:

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Dodawanie agenta za pomocą szablonu Menedżera zasobów

Przykładowe szablony Menedżera zasobów, które wdrażają obszar roboczy usługi Azure Log Analytics i dodają agenta do każdego z węzłów, są dostępne dla [systemu Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) lub [Linux.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS)

Można pobrać i zmodyfikować ten szablon, aby wdrożyć klaster, który najlepiej odpowiada Twoim potrzebom.

## <a name="view-performance-counters"></a>Wyświetlanie liczników wydajności

Teraz, gdy dodano agenta usługi Log Analytics, przejdź do portalu usługi Log Analytics, aby wybrać liczniki wydajności, które chcesz zebrać.

1. W witrynie Azure portal przejdź do grupy zasobów, w której utworzono rozwiązanie analizy sieci szkieletowej usług. Wybierz **servicefabric\<nameOfLog\>AnalyticsWorkspace**.

2. Kliknij pozycję **Log Analytics**.

3. Kliknij pozycję **Ustawienia zaawansowane**.

4. Kliknij **pozycję Dane**, a następnie kliknij pozycję **Liczniki wydajności systemu Windows lub Linux**. Istnieje lista liczników domyślnych, które można włączyć i można ustawić interwał dla kolekcji zbyt. Można również dodać [dodatkowe liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md) do zbierania. W tym [artykule](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx)odwołuje się odpowiedni format .

5. Kliknij **pozycję Zapisz**, a następnie kliknij przycisk **OK**.

6. Zamknij blok Ustawienia zaawansowane.

7. W nagłówku Ogólne kliknij pozycję **Podsumowanie obszaru roboczego**.

8. Zostaną wyświetlone kafelki w postaci wykresu dla każdego z włączonych rozwiązań, w tym jednego dla sieci szkieletowej usług. Kliknij wykres **sieci szkieletowej usług,** aby przejść do rozwiązania analizy sieci szkieletowej usług.

9. Zobaczysz kilka kafelków z wykresami na kanale operacyjnym i zdarzeniach niezawodnych usług. Graficzna reprezentacja danych płynących dla wybranych liczników pojawi się w obszarze Metryki węzłów.

10. Kliknij wykres metryki kontenera, aby wyświetlić dodatkowe szczegóły. Można również kwerendy na dane licznika wydajności podobnie do zdarzeń klastra i filtrować na węzłach, nazwę licznika perf i wartości przy użyciu języka zapytania Kusto.

![Zapytanie licznika analizy dzienników](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Następne kroki

* Zbierz odpowiednie [liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md). Aby skonfigurować agenta usługi Log Analytics do zbierania określonych liczników wydajności, należy [przejrzeć konfigurowanie źródeł danych](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources).
* Konfigurowanie dzienników usługi Azure Monitor w celu [skonfigurowania automatycznego ostrzegania](../log-analytics/log-analytics-alerts.md) w celu ułatwienia wykrywania i diagnostyki
* Alternatywnie można zbierać liczniki wydajności za pośrednictwem [rozszerzenia diagnostyki platformy Azure i wysyłać je do usługi Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
