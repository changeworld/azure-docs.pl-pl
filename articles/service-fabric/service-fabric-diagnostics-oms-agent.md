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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 6e1c870458f43bcc5d6d40f0e40e2b2a95bee2af
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
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

1. W portalu Azure przejdź do grupy zasobów, w której utworzono rozwiązania analizy sieci szkieletowej usług. Wybierz **ServiceFabric\<nameOfOMSWorkspace\>**  i przejdź do strony Przegląd. U góry kliknij łącze, aby przejść do portalu OMS.

2. Po nawiązaniu połączenia w portalu, zobaczysz Kafelki w formie wykresu dla każdego z rozwiązań włączone, w tym dla sieci szkieletowej usług. Kliknij tutaj, aby przejść do rozwiązania analizy sieci szkieletowej usług. 

3. Zostanie wyświetlone kilka Kafelki wykresami kanałem operacyjne i niezawodne usługi zdarzenia. Po prawej stronie kliknij ikonę narzędzi, aby przejść do strony ustawień.

    ![Ustawienia OMS](media/service-fabric-diagnostics-oms-agent/oms-solutions-settings.png)

4. Na stronie Ustawienia kliknij danych, a następnie wybierz pozycję Windows lub Linux liczników wydajności. Istnieją listę domyślne te, które można włączyć i interwał dla kolekcji można ustawić także. Można również dodać [dodatkowe liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md) do zbierania. W tym odwołuje się do prawidłowego formatu [artykułu](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

Po liczniki sieci są skonfigurowane, head z powrotem do strony rozwiązań i pojawi się wkrótce przepływu danych w i wyświetlane na wykresach w obszarze **metryki węzła**. Możesz także zbadać na dane licznika wydajności, podobnie jak zdarzenia i filtr węzłów, nazwa licznika wydajności i wartości przy użyciu języka zapytań Kusto. 

![Kwerendy licznika wydajności OMS](media/service-fabric-diagnostics-oms-agent/oms-perf-counter-query.png)

## <a name="next-steps"></a>Kolejne kroki

* Zbieranie odpowiednich [liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md). Aby skonfigurować agenta pakietu OMS zbierać konkretnych licznikach wydajności, przejrzyj [Konfigurowanie źródeł danych](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Konfigurowanie analizy dzienników, aby skonfigurować [automatycznego alerty](../log-analytics/log-analytics-alerts.md) do pomocy w wykrywaniu i Diagnostyka
* Alternatywnie można zebrać liczników wydajności za pośrednictwem [rozszerzenia diagnostyki Azure i wysyłać je do usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)