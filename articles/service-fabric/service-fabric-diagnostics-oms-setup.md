---
title: Konfigurowanie monitorowania przy użyciu dzienników Azure Monitor
description: Dowiedz się, jak skonfigurować Azure Monitor dzienników do wizualizacji i analizowania zdarzeń do monitorowania klastrów Service Fabric platformy Azure.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: cf0fab9942dcbb7ee09e554f2c9ba8738f208009
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609931"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Konfigurowanie dzienników Azure Monitor dla klastra

Dzienniki Azure Monitor to nasze zalecenie do monitorowania zdarzeń na poziomie klastra. Można skonfigurować obszar roboczy usługi Log Analytics, za pośrednictwem usługi Azure Resource Manager, programu PowerShell lub portalu Azure Marketplace. Jeśli zachowasz zaktualizowany szablon Menedżer zasobów wdrożenia do użytku w przyszłości, użyj tego samego szablonu, aby skonfigurować środowisko dzienników Azure Monitor. Wdrożenia za pośrednictwem witryny Marketplace jest łatwiejsze, jeśli masz już klaster, który został wdrożony z włączoną diagnostyką. Jeśli nie masz dostępu na poziomie subskrypcji na koncie, do której jest wdrażany z, należy wdrożyć przy użyciu programu PowerShell lub szablonu usługi Resource Manager.

> [!NOTE]
> Aby skonfigurować Azure Monitor dzienników do monitorowania klastra, musisz mieć włączoną diagnostykę do wyświetlania zdarzeń na poziomie klastra lub na poziomie platformy. Zapoznaj się [jak konfigurowanie diagnostyki w klastrach Windows](service-fabric-diagnostics-event-aggregation-wad.md) i [sposób konfigurowania diagnostyki w klastrach systemu Linux](service-fabric-diagnostics-oms-syslog.md) Aby uzyskać więcej informacji

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

W przypadku korzystania z systemu Windows wykonaj następujące kroki, aby połączyć dzienniki Azure Monitor z kontem magazynu, na którym są przechowywane zdarzenia klastra. 

>[!NOTE]
>Rozwiązanie Service Fabric Analytics jest obsługiwane tylko w przypadku klastrów systemu Windows. W przypadku klastrów systemu Linux zapoznaj się z artykułem dotyczącym [konfigurowania dzienników Azure monitor dla klastrów systemu Linux](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Połącz obszar roboczy usługi Log Analytics z klastrem 

1. Obszaru roboczego musi być podłączony do danych diagnostycznych z klastra. Przejdź do grupy zasobów, w którym utworzono rozwiązania analiza usługi Service Fabric. Wybierz **ServiceFabric\<nameOfWorkspace\>**  i przejdź do strony Przegląd. Z tego miejsca można zmienić ustawienia rozwiązania, ustawienia obszaru roboczego i dostęp do obszaru roboczego usługi Log Analytics.

2. W menu nawigacji po lewej stronie w obszarze **źródła danych obszaru roboczego**, wybierz opcję **dzienniki kont magazynu**.

3. Na **dzienniki konta magazynu** wybierz opcję **Dodaj** u góry, aby dodać dzienniki klastra do obszaru roboczego.

4. Wybierz **konta magazynu** dodać odpowiednie konto utworzone w klastrze. Jeśli używana jest domyślna nazwa, konto magazynu jest **sfdg\<resourceGroupName\>** . Można również to potwierdzić, przy użyciu szablonu usługi Azure Resource Manager umożliwia wdrażanie klastra, sprawdzając wartość **applicationDiagnosticsStorageAccountName**. Jeśli nazwa nie jest widoczne, przewiń w dół i wybierz **Załaduj więcej**. Wybierz nazwę konta magazynu.

5. Określ typ danych. Ustaw ją na **usługi Service Fabric zdarzenia**.

6. Upewnij się, że źródłem jest automatycznie ustawiana na **WADServiceFabric\*EventTable**.

7. Wybierz **OK** nawiązać obszaru dzienniki usługi klastra.

    ![Dodawanie dzienników kont magazynu do dzienników Azure Monitor](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Konto teraz wyświetlane jako część konta magazynu dzienników w źródłach danych obszaru roboczego.

Rozwiązania analiza usługi Service Fabric zostały dodane, w obszarze roboczym usługi Log Analytics, która jest teraz prawidłowo podłączone do klastra platformy oraz tabeli dziennika aplikacji. W ten sam sposób, można dodać dodatkowe źródła do obszaru roboczego.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Wdrażanie dzienników Azure Monitor przy użyciu Azure Resource Manager

Podczas wdrażania klastra przy użyciu szablonu usługi Resource Manager szablon tworzy nowy obszar roboczy usługi Log Analytics, dodająca rozwiązanie usługi Service Fabric do obszaru roboczego i konfiguruje go do odczytywania danych z tabel odpowiedniego magazynu.

Możesz użyć i zmodyfikuj [ten przykładowy szablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) zgodnie z wymaganiami. Ten szablon wykonuje następujące czynności

* Tworzy klaster usługi Service Fabric z 5 węzłami
* Tworzy obszar roboczy usługi Log Analytics i rozwiązania usługi Service Fabric
* Konfiguruje agenta usługi Log Analytics do zbierania i wysyłania 2 liczniki wydajności przykładowe do obszaru roboczego
* Konfiguruje WAD do zbierania usługi Service Fabric, a następnie wysyła je do tabel usługi Azure storage (WADServiceFabric * EventTable)
* Konfiguruje obszaru roboczego usługi Log Analytics do odczytywania zdarzeń z tych tabel


Szablon można wdrożyć jako uaktualnienie Menedżer zasobów do klastra przy użyciu interfejsu API `New-AzResourceGroupDeployment` w module Azure PowerShell. Przykładowe polecenie będzie następująca:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Usługa Azure Resource Manager wykryje, że to polecenie aktualizacji istniejącego zasobu. Przetwarza tylko zmiany między szablonu zachęcanie istniejącego wdrożenia i nowy szablon, pod warunkiem.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Wdrażanie dzienników Azure Monitor przy użyciu Azure PowerShell

Możesz również wdrożyć zasób usługi log Analytics za pomocą programu PowerShell przy użyciu polecenia `New-AzOperationalInsightsWorkspace`. Aby użyć tej metody, upewnij się, zainstalowano [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Użyj tego skryptu, aby utworzyć nowy obszar roboczy usługi Log Analytics i Dodawanie rozwiązania do usługi Service Fabric do niego: 

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

Gdy skończysz, wykonaj kroki opisane w poprzedniej sekcji, aby połączyć Azure Monitor dzienniki z odpowiednim kontem magazynu.

Można również dodać inne rozwiązania lub wprowadzić inne zmiany do swojego obszaru roboczego usługi Log Analytics przy użyciu programu PowerShell. Aby dowiedzieć się więcej, zobacz [Zarządzanie dziennikami Azure monitor przy użyciu programu PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Następne kroki
* [Wdróż agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md) na węzły do zbierania liczników wydajności i zbieranie statystyk platformy docker i dzienniki kontenerów
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../log-analytics/log-analytics-log-searches.md) , które są oferowane w ramach dzienników Azure monitor
* [Używanie projektanta widoków do tworzenia widoków niestandardowych w dziennikach Azure Monitor](../azure-monitor/platform/view-designer.md)
