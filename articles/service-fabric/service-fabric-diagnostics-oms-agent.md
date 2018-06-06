---
title: Usługa Azure sieci szkieletowej — monitorowanie za pomocą analizy dzienników wydajności | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować Agent pakietu OMS monitorowania kontenery i liczniki wydajności dla klastrów sieci szkieletowej usług Azure.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: af0007daf6cb8a2e17e1806f47587e5958b15bd2
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716078"
---
# <a name="performance-monitoring-with-log-analytics"></a>Wydajność monitorowania za pomocą analizy dzienników

W tym artykule opisano kroki, aby dodać agenta analizy dzienników, znanej także jako OMS, jak skalowania maszyny wirtualnej ustawić rozszerzenia do klastra i łącząc go z istniejącym obszarem roboczym usługi Analiza dzienników Azure. Dzięki temu zbierania danych diagnostycznych o kontenerów, aplikacji i monitorowania wydajności. Dodając ją jako rozszerzenie do zasobu zestawu skali maszyny wirtualnej, usługi Azure Resource Manager zapewnia, że pobiera zainstalowane na każdym węźle, nawet gdy skalowanie klastra.

> [!NOTE]
> W tym artykule przyjęto założenie, że obszarem roboczym usługi Analiza dzienników Azure już skonfigurowane. Jeśli nie chcesz, przejdź do [— Konfiguracja usługi Analiza dzienników Azure](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Dodaj rozszerzenie agenta za pomocą wiersza polecenia platformy Azure

Najlepszy sposób, aby dodać do klastra Agent pakietu OMS ustawiono za pośrednictwem skali maszyny wirtualnej dostępne interfejsy API wiersza polecenia platformy Azure. Jeśli nie masz jeszcze skonfigurowane wiersza polecenia platformy Azure, przejdź do portalu Azure i otworzyć [powłoki chmury](../cloud-shell/overview.md) wystąpienia, lub [zainstalować Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Po zażądaniu powłoki chmury upewnij się, że pracujesz w tej samej subskrypcji co zasób. Sprawdź z `az account show` i upewnij się, że wartość "name" zgodny z subskrypcji z klastra.

2. W portalu przejdź do grupy zasobów, w którym znajduje się obszar roboczy analizy dzienników. Kliknij przycisk do zasobu analizy dzienników (typ zasobu będzie Log Analytics). Po przejściu na stronie przeglądu zasobów, wybierz polecenie **Zaawansowane ustawienia** w sekcji Ustawienia w menu po lewej stronie.

    ![Strona właściwości analizy dzienników](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Polecenie **serwerów z systemem Windows** jeśli są stały się klastra systemu Windows i **serwerów z systemem Linux** w przypadku tworzenia klastrów systemu Linux. Ta strona zostanie wyświetlona z `workspace ID` i `workspace key` (wyświetlane jako klucz podstawowy w portalu). Należy zarówno do następnego kroku.

4. Uruchom polecenie, aby zainstalować agenta pakietu OMS na klaster, przy użyciu `vmss extension set` interfejsu API w chmurze powłoki:

    Dla klastra systemu Windows:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Przez klaster systemu Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Oto przykład Agent pakietu OMS dodawany do klastra systemu Windows.

    ![Polecenia interfejsu wiersza polecenia agenta pakietu OMS](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. To powinno zająć mniej niż 15 min pomyślnie dodać agenta do węzłów. Możesz sprawdzić, czy agenci zostały dodane przy użyciu `az vmss extension list` interfejsu API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Dodanie agenta za pomocą szablonu usługi Resource Manager

Jest dostępny dla Menedżera zasobów przykładowej szablonów, które wdrażanie obszarem roboczym usługi Analiza dzienników Azure i dodać agenta do każdego z węzłów [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) lub [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Można pobrać i zmodyfikować tego szablonu można wdrożyć klaster, który najlepiej odpowiada Twoim potrzebom.

## <a name="view-performance-counters-in-the-log-analytics-portal"></a>Wyświetl liczniki wydajności w portalu usługi Analiza dzienników

Teraz, gdy agent pakietu OMS head dodano na do portalu usługi Analiza dzienników, wybierz liczniki wydajności, które chcesz zebrać. 

1. W portalu Azure przejdź do grupy zasobów, w której utworzono rozwiązania analizy sieci szkieletowej usług. Wybierz **ServiceFabric\<nameOfOMSWorkspace\>**.

2. Kliknij przycisk **obszarem roboczym pakietu OMS**.

3. Kliknij przycisk **Zaawansowane ustawienia**.

4. Kliknij przycisk **danych**, następnie kliknij przycisk **systemu Windows lub Linux liczniki wydajności**. Znajduje się lista domyślne liczniki, które można włączyć i interwał dla kolekcji można ustawić także. Można również dodać [dodatkowe liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md) do zbierania. W tym odwołuje się do prawidłowego formatu [artykułu](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Kliknij przycisk **zapisać**, następnie kliknij przycisk **OK**.

6. Zamknij bloku ustawienia zaawansowane.

7. Pod pozycją Ogólne, kliknij przycisk **omówienie**.

8. Zobaczysz Kafelki w formie wykresu dla każdego z rozwiązań włączone, w tym dla sieci szkieletowej usług. Kliknij przycisk **sieci szkieletowej usług** wykres tak, aby kontynuować do rozwiązania analizy sieci szkieletowej usług.

9. Zobaczysz kilka Kafelki wykresami kanałem operacyjne i niezawodne usługi zdarzenia. Graficzna reprezentacja dane przepływające w wybranych liczników będą wyświetlane w obszarze węzła metryki. 

10. Kliknij na wykresie Metryka kontenera, aby wyświetlić dodatkowe szczegóły. Możesz także zbadać na dane licznika wydajności, podobnie jak zdarzenia i filtr węzłów, nazwa licznika wydajności i wartości przy użyciu języka zapytań Kusto.

![Kwerendy licznika wydajności OMS](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.png)

## <a name="next-steps"></a>Kolejne kroki

* Zbieranie odpowiednich [liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md). Aby skonfigurować agenta pakietu OMS zbierać konkretnych licznikach wydajności, przejrzyj [Konfigurowanie źródeł danych](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Konfigurowanie analizy dzienników, aby skonfigurować [automatycznego alerty](../log-analytics/log-analytics-alerts.md) do pomocy w wykrywaniu i Diagnostyka
* Alternatywnie można zebrać liczników wydajności za pośrednictwem [rozszerzenia diagnostyki Azure i wysyłać je do usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)
