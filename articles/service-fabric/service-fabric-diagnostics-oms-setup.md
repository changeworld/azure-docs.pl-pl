---
title: Usługa Azure Service Fabric — skonfiguruj monitorowanie przy użyciu dzienników usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować dzienniki usługi Azure Monitor umożliwiające wizualizowanie i analizowanie zdarzeń do monitorowania klastrów usługi Azure Service Fabric.
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
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: c8f7198b59a0fe7ed6775736f8b97f5b5a262640
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306862"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Konfigurowanie dzienników usługi Azure Monitor dla klastra

Dzienniki platformy Azure Monitor to nasze zalecenie, aby monitorować zdarzenia poziomu klastra. Można skonfigurować obszar roboczy usługi Log Analytics, za pośrednictwem usługi Azure Resource Manager, programu PowerShell lub portalu Azure Marketplace. Jeśli zachowasz zaktualizowany szablon usługi Resource Manager, który wdrożenia do użycia w przyszłości, należy użyć tego samego szablonu do skonfigurowania środowiska dzienniki usługi Azure Monitor. Wdrożenia za pośrednictwem witryny Marketplace jest łatwiejsze, jeśli masz już klaster, który został wdrożony z włączoną diagnostyką. Jeśli nie masz dostępu na poziomie subskrypcji na koncie, do której jest wdrażany z, należy wdrożyć przy użyciu programu PowerShell lub szablonu usługi Resource Manager.

> [!NOTE]
> Aby skonfigurować dzienniki usługi Azure Monitor do monitorowania klastra, musisz mieć włączone, aby wyświetlić zdarzenia z poziomu klastra lub poziom platformy diagnostyki. Zapoznaj się [jak konfigurowanie diagnostyki w klastrach Windows](service-fabric-diagnostics-event-aggregation-wad.md) i [sposób konfigurowania diagnostyki w klastrach systemu Linux](service-fabric-diagnostics-oms-syslog.md) Aby uzyskać więcej informacji

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Wdrażanie obszaru roboczego usługi Log Analytics przy użyciu portalu Azure Marketplace

Jeśli chcesz dodać obszar roboczy usługi Log Analytics, po wdrożeniu klastra, przejdź do portalu Azure Marketplace w portalu i poszukaj **analiza usługi Service Fabric**. To niestandardowe rozwiązanie w przypadku wdrożeń usługi Service Fabric zawiera dane specyficzne dla usługi Service Fabric. W ramach tego procesu możesz tworzyć rozwiązania (pulpit nawigacyjny, aby wyświetlić szczegółowe informacje) oraz obszaru roboczego (agregacji danych bazowych klastra).

1. Wybierz **New** w menu nawigacji po lewej stronie. 

2. Wyszukaj **usługi Service Fabric Analytics**. Wybierz zasób, który pojawia się.

3. Wybierz pozycję **Utwórz**.

    ![Analiza usługi Service Fabric w witrynie Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. W oknie tworzenia analiza usługi Service Fabric, wybierz **wybierz obszar roboczy** dla **obszaru roboczego pakietu OMS** pola, a następnie **Utwórz nowy obszar roboczy**. Wypełnij wymagane wpisy. Jedynym wymaganiem jest, czy subskrypcja dla klastra usługi Service Fabric i obszaru roboczego jest taki sam. Po sprawdzeniu poprawności wpisy rozpocznie wdrażanie obszaru roboczego. Przeprowadzenie wdrożenia zajmuje tylko kilka minut.

5. Po zakończeniu wybierz pozycję **Utwórz** ponownie w dolnej części okna Tworzenie analiza usługi Service Fabric. Upewnij się, że nowy obszar roboczy będzie wyświetlane w **obszaru roboczego pakietu OMS**. Ta akcja dodaje rozwiązania do obszaru roboczego, który został utworzony.

Jeśli używasz Windows Kontynuuj poniższe kroki, aby nawiązać połączenie z dzienników usługi Azure Monitor na koncie magazynu przechowywania zdarzenia klastra. 

>[!NOTE]
>Rozwiązania analiza usługi Service Fabric jest obsługiwana tylko w przypadku klastrów Windows. W przypadku klastrów systemu Linux, zapoznaj się z artykułem na [sposób konfigurowania usługi Azure Monitor dzienników dla klastrów systemu Linux](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Połącz obszar roboczy usługi Log Analytics z klastrem 

1. Obszaru roboczego musi być podłączony do danych diagnostycznych z klastra. Przejdź do grupy zasobów, w którym utworzono rozwiązania analiza usługi Service Fabric. Wybierz **ServiceFabric\<nameOfWorkspace\>**  i przejdź do strony Przegląd. Z tego miejsca można zmienić ustawienia rozwiązania, ustawienia obszaru roboczego i dostęp do obszaru roboczego usługi Log Analytics.

2. W menu nawigacji po lewej stronie w obszarze **źródła danych obszaru roboczego**, wybierz opcję **dzienniki kont magazynu**.

3. Na **dzienniki konta magazynu** wybierz opcję **Dodaj** u góry, aby dodać dzienniki klastra do obszaru roboczego.

4. Wybierz **konta magazynu** dodać odpowiednie konto utworzone w klastrze. Jeśli używana jest domyślna nazwa, konto magazynu jest **sfdg\<resourceGroupName\>** . Można również to potwierdzić, przy użyciu szablonu usługi Azure Resource Manager umożliwia wdrażanie klastra, sprawdzając wartość **applicationDiagnosticsStorageAccountName**. Jeśli nazwa nie jest widoczne, przewiń w dół i wybierz **Załaduj więcej**. Wybierz nazwę konta magazynu.

5. Określ typ danych. Ustaw ją na **usługi Service Fabric zdarzenia**.

6. Upewnij się, że źródłem jest automatycznie ustawiana na **WADServiceFabric\*EventTable**.

7. Wybierz **OK** nawiązać obszaru dzienniki usługi klastra.

    ![Dodaj dzienniki konta magazynu do dzienników usługi Azure Monitor](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Konto teraz wyświetlane jako część konta magazynu dzienników w źródłach danych obszaru roboczego.

Rozwiązania analiza usługi Service Fabric zostały dodane, w obszarze roboczym usługi Log Analytics, która jest teraz prawidłowo podłączone do klastra platformy oraz tabeli dziennika aplikacji. W ten sam sposób, można dodać dodatkowe źródła do obszaru roboczego.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Wdrażanie usługi Azure Monitor dzienników przy użyciu usługi Azure Resource Manager

Podczas wdrażania klastra przy użyciu szablonu usługi Resource Manager szablon tworzy nowy obszar roboczy usługi Log Analytics, dodająca rozwiązanie usługi Service Fabric do obszaru roboczego i konfiguruje go do odczytywania danych z tabel odpowiedniego magazynu.

Możesz użyć i zmodyfikuj [ten przykładowy szablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) zgodnie z wymaganiami. Ten szablon wykonuje następujące czynności

* Tworzy klaster usługi Service Fabric z 5 węzłami
* Tworzy obszar roboczy usługi Log Analytics i rozwiązania usługi Service Fabric
* Konfiguruje agenta usługi Log Analytics do zbierania i wysyłania 2 liczniki wydajności przykładowe do obszaru roboczego
* Konfiguruje WAD do zbierania usługi Service Fabric, a następnie wysyła je do tabel usługi Azure storage (WADServiceFabric * EventTable)
* Konfiguruje obszaru roboczego usługi Log Analytics do odczytywania zdarzeń z tych tabel


Można wdrożyć tego szablonu jako uaktualniania przy użyciu usługi Resource Manager z klastrem przy użyciu `New-AzResourceGroupDeployment` interfejsu API w module programu Azure PowerShell. Przykładowe polecenie będzie następująca:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Usługa Azure Resource Manager wykryje, że to polecenie aktualizacji istniejącego zasobu. Przetwarza tylko zmiany między szablonu zachęcanie istniejącego wdrożenia i nowy szablon, pod warunkiem.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Wdrażanie usługi Azure Monitor dzienników za pomocą programu Azure PowerShell

Zasób log analytics za pośrednictwem programu PowerShell można także wdrożyć za pomocą `New-AzOperationalInsightsWorkspace` polecenia. Aby użyć tej metody, upewnij się, zainstalowano [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Użyj tego skryptu, aby utworzyć nowy obszar roboczy usługi Log Analytics i Dodawanie rozwiązania do usługi Service Fabric do niego: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Gdy wszystko będzie gotowe, wykonaj kroki opisane w poprzedniej sekcji, aby nawiązać połączenie na koncie magazynu odpowiednie dzienniki usługi Azure Monitor.

Można również dodać inne rozwiązania lub wprowadzić inne zmiany do swojego obszaru roboczego usługi Log Analytics przy użyciu programu PowerShell. Aby dowiedzieć się więcej, zobacz [zarządzania usługi Azure Monitor loguje się przy użyciu programu PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Kolejne kroki
* [Wdróż agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md) na węzły do zbierania liczników wydajności i zbieranie statystyk platformy docker i dzienniki kontenerów
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../log-analytics/log-analytics-log-searches.md) dostępnymi w ramach dzienniki usługi Azure Monitor
* [Projektant widoków umożliwia tworzenie niestandardowych widoków w dziennikach w usłudze Azure Monitor](../azure-monitor/platform/view-designer.md)
