---
title: Azure Service Fabric — Konfigurowanie monitorowania z agentem pakietu OMS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować Agent pakietu OMS monitorowania kontenery i liczniki wydajności dla klastrów sieci szkieletowej usług Azure.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: 4b0845cbb25d160b53b483641e242422c98029ee
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="add-the-oms-agent-to-a-cluster"></a>Dodawanie Agent pakietu OMS do klastra

W tym artykule opisano kroki, aby dodać Agent pakietu OMS jako skalowania maszyny wirtualnej ustawić rozszerzenia do klastra i łącząc go z istniejącym obszarem roboczym usługi Analiza dzienników Azure. Dzięki temu zbierania danych diagnostycznych o kontenerów, aplikacji i monitorowania wydajności. Dodając ją jako rozszerzenie, usługi Azure Resource Manager zapewnia, że pobiera zainstalowane na każdym węźle, nawet gdy skalowanie klastra.

> [!NOTE]
> W tym artykule przyjęto założenie, że obszarem roboczym usługi Analiza dzienników Azure już skonfigurowane. Jeśli nie chcesz, przejdź do [— Konfiguracja usługi Analiza dzienników Azure](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Dodaj rozszerzenie agenta za pomocą wiersza polecenia platformy Azure

Najlepszy sposób, aby dodać do klastra Agent pakietu OMS ustawiono za pośrednictwem skali maszyny wirtualnej dostępne interfejsy API wiersza polecenia platformy Azure. Jeśli nie masz jeszcze skonfigurowane wiersza polecenia platformy Azure, przejdź do portalu Azure i otworzyć [powłoki chmury](../cloud-shell/overview.md) wystąpienia, lub [zainstalować Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Po zażądaniu powłoki chmury upewnij się, że pracujesz w tej samej subskrypcji co zasób. Sprawdź z `az account show` i upewnij się, że wartość "name" zgodny z subskrypcji z klastra.

2. W portalu przejdź do grupy zasobów, w którym znajduje się obszar roboczy analizy dzienników. Kliknij przycisk do analizy dzienników zasoby (typ zasobu będzie analizy dzienników), prawy nawigacji, przewiń w dół i kliknij pozycję **właściwości**.

    ![Strona właściwości analizy dzienników](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    Zwróć uwagę na Twojej `workspaceId`. 

3. Należy również Twojej `workspaceKey` wdrażania agenta. Aby uzyskać klucz należy, kliknij polecenie **Zaawansowane ustawienia**w obszarze *ustawienia* sekcji nawigacji po lewej stronie. Polecenie **serwerów z systemem Windows** jeśli są stały się klastra systemu Windows i **serwerów z systemem Linux** w przypadku tworzenia klastrów systemu Linux. Będziesz potrzebować *klucza podstawowego* który zostaną wyświetlone do wdrożenia agentów, jako `workspaceKey`.

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
 
5. Uruchom polecenie, aby zastosować tę konfigurację do wystąpień maszyn wirtualnych, które już istnieją:  

    ```sh
    az vmss update-instances
    ```

    To powinno zająć mniej niż 15 min pomyślnie dodać agenta do węzłów. Możesz sprawdzić, czy agenci zostały dodane przy użyciu `az vmss extension list` interfejsu API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Dodanie agenta za pomocą szablonu usługi Resource Manager

Jest dostępny dla Menedżera zasobów przykładowej szablonów, które wdrażanie obszarem roboczym usługi Analiza dzienników Azure i dodać agenta do każdego z węzłów [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) lub [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Można pobrać i zmodyfikować tego szablonu można wdrożyć klaster, który najlepiej odpowiada Twoim potrzebom.

## <a name="next-steps"></a>Następne kroki

* Zbieranie odpowiednich [liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md). Aby skonfigurować agenta pakietu OMS zbierać konkretnych licznikach wydajności, przejrzyj [Konfigurowanie źródeł danych](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Konfigurowanie analizy dzienników, aby skonfigurować [automatycznego alerty](../log-analytics/log-analytics-alerts.md) do pomocy w wykrywaniu i Diagnostyka
