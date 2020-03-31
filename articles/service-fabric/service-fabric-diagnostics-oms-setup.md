---
title: Konfigurowanie monitorowania za pomocą dzienników usługi Azure Monitor
description: Dowiedz się, jak skonfigurować dzienniki usługi Azure Monitor do wizualizacji i analizowania zdarzeń w celu monitorowania klastrów sieci szkieletowej usług Azure.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: cf0fab9942dcbb7ee09e554f2c9ba8738f208009
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609931"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Konfigurowanie dzienników usługi Azure Monitor dla klastra

Dzienniki usługi Azure Monitor to nasze zalecenie do monitorowania zdarzeń na poziomie klastra. Obszar roboczy usługi Log Analytics można skonfigurować za pośrednictwem usługi Azure Resource Manager, PowerShell lub Azure Marketplace. Jeśli zachowasz zaktualizowany szablon Menedżera zasobów wdrożenia do wykorzystania w przyszłości, użyj tego samego szablonu, aby skonfigurować środowisko dzienników usługi Azure Monitor. Wdrażanie za pośrednictwem portalu Marketplace jest łatwiejsze, jeśli masz już wdrożony klaster z włączoną diagnostyką. Jeśli nie masz dostępu na poziomie subskrypcji na koncie, na którym wdrażasz, wdrożyć przy użyciu programu PowerShell lub szablonu Menedżera zasobów.

> [!NOTE]
> Aby skonfigurować dzienniki usługi Azure Monitor do monitorowania klastra, musisz mieć włączoną diagnostykę, aby wyświetlać zdarzenia na poziomie klastra lub na poziomie platformy. Aby uzyskać więcej [informacji, zapoznaj się z instrukcjami konfigurowania diagnostyki w klastrach systemu Windows](service-fabric-diagnostics-event-aggregation-wad.md) i [konfigurowania diagnostyki w klastrach systemu Linux](service-fabric-diagnostics-oms-syslog.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Wdrażanie obszaru roboczego usługi Log Analytics przy użyciu portalu Azure Marketplace

Jeśli chcesz dodać obszar roboczy usługi Log Analytics po wdrożeniu klastra, przejdź do portalu w portalu w portalu w portalu i **poszukaj usługi Service Fabric Analytics.** Jest to niestandardowe rozwiązanie dla wdrożeń sieci szkieletowej usług, które ma dane specyficzne dla sieci szkieletowej usług. W tym procesie utworzysz zarówno rozwiązanie (pulpit nawigacyjny do wyświetlania szczegółowych informacji), jak i obszar roboczy (agregacja podstawowych danych klastra).

1. W menu nawigacji po lewej stronie wybierz **pozycję Nowy.** 

2. Wyszukaj **usługę Service Fabric Analytics**. Wybierz zasób, który się pojawi.

3. Wybierz **pozycję Utwórz**.

    ![Analiza sieci szkieletowych usług w marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. W oknie Tworzenia analizy sieci szkieletowej usług wybierz pozycję **Wybierz obszar roboczy** dla pola **Obszar roboczy systemu OMS,** a następnie **utwórz nowy obszar roboczy**. Wypełnij wymagane wpisy. Jedynym wymaganiem w tym miejscu jest, że subskrypcja dla klastra sieci szkieletowej usług i obszaru roboczego jest taka sama. Po sprawdzeniu poprawności wpisów obszar roboczy rozpoczyna wdrażanie. Wdrożenie trwa tylko kilka minut.

5. Po zakończeniu wybierz pozycję **Utwórz** ponownie u dołu okna tworzenia usługi Analytics. Upewnij się, że nowy obszar roboczy jest wyświetlany w obszarze **obszar roboczy systemu OMS**. Ta akcja dodaje rozwiązanie do utworzonego obszaru roboczego.

Jeśli używasz systemu Windows, przejdź do następujących kroków, aby połączyć dzienniki usługi Azure Monitor z kontem magazynu, na którym są przechowywane zdarzenia klastra. 

>[!NOTE]
>Rozwiązanie analizy sieci szkieletowej usług jest obsługiwane tylko dla klastrów systemu Windows. W przypadku klastrów systemu Linux zapoznaj się z naszym artykułem na [temat konfigurowania dzienników usługi Azure Monitor dla klastrów systemu Linux.](service-fabric-diagnostics-oms-syslog.md)  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Łączenie obszaru roboczego usługi Log Analytics z klastrem 

1. Obszar roboczy musi być połączony z danymi diagnostycznymi pochodzącymi z klastra. Przejdź do grupy zasobów, w której utworzono rozwiązanie analizy sieci szkieletowej usług. Wybierz **ServiceFabric\<nameOfWorkspace\> ** i przejdź do jego strony przeglądu. W tym miejscu można zmienić ustawienia rozwiązania, ustawienia obszaru roboczego i uzyskać dostęp do obszaru roboczego usługi Log Analytics.

2. W menu nawigacji po lewej stronie w obszarze **Źródła danych obszaru roboczego**wybierz pozycję **Dzienniki kont magazynu**.

3. Na stronie **Dzienniki konta magazynu** wybierz pozycję **Dodaj** u góry, aby dodać dzienniki klastra do obszaru roboczego.

4. Wybierz **konto magazynu,** aby dodać odpowiednie konto utworzone w klastrze. Jeśli użyto nazwy domyślnej, kontem magazynu jest **sfdg\<\>resourceGroupName**. Można to również potwierdzić za pomocą szablonu usługi Azure Resource Manager używanego do wdrażania klastra, sprawdzając wartość używaną dla **aplikacjiDiagnosticsStorageAccountName**. Jeśli nazwa nie jest ana w górę, przewiń w dół i wybierz pozycję **Załaduj więcej**. Wybierz nazwę konta magazynu.

5. Określ typ danych. Ustaw go na **Zdarzenia sieci szkieletowej usług**.

6. Upewnij się, że źródło jest automatycznie ustawione na **\*WADServiceFabric EventTable**.

7. Wybierz **przycisk OK,** aby połączyć obszar roboczy z dziennikami klastra.

    ![Dodawanie dzienników kont magazynu do dzienników usługi Azure Monitor](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Konto jest teraz wyświetlane jako część dzienników konta magazynu w źródłach danych obszaru roboczego.

Dodano rozwiązanie analizy sieci szkieletowej usług w obszarze roboczym usługi Log Analytics, który jest teraz poprawnie połączony z platformą klastra i tabelą dziennika aplikacji. Dodatkowe źródła można dodać do obszaru roboczego w ten sam sposób.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Wdrażanie dzienników usługi Azure Monitor za pomocą usługi Azure Resource Manager

Podczas wdrażania klastra przy użyciu szablonu Menedżera zasobów szablon tworzy nowy obszar roboczy usługi Log Analytics, dodaje rozwiązanie sieci szkieletowej usług do obszaru roboczego i konfiguruje go do odczytu danych z odpowiednich tabel magazynu.

Można użyć i zmodyfikować [ten przykładowy szablon,](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) aby spełnić wymagania. W tym szablonie przedstawiono następujące czynności:

* Tworzy klaster sieci szkieletowej usług 5 węzłów
* Tworzy obszar roboczy usługi Log Analytics i rozwiązanie sieci szkieletowej usług
* Konfiguruje agenta usługi Log Analytics do zbierania i wysyłania 2 przykładowych liczników wydajności do obszaru roboczego
* Konfiguruje wad do zbierania sieci szkieletowej usług i wysyła je do tabel magazynu platformy Azure (WADServiceFabric *EventTable)
* Konfiguruje obszar roboczy usługi Log Analytics do odczytu zdarzeń z tych tabel


Szablon można wdrożyć jako uaktualnienie usługi Resource `New-AzResourceGroupDeployment` Manager do klastra przy użyciu interfejsu API w module programu Azure PowerShell. Przykładowe polecenie może być:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Usługa Azure Resource Manager wykrywa, że to polecenie jest aktualizacją istniejącego zasobu. Przetwarza tylko zmiany między szablonem napędzając istniejącego wdrożenia i nowy szablon pod warunkiem.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Wdrażanie dzienników usługi Azure Monitor za pomocą programu Azure PowerShell

Można również wdrożyć zasób analizy dziennika `New-AzOperationalInsightsWorkspace` za pomocą programu PowerShell za pomocą polecenia. Aby użyć tej metody, upewnij się, że zainstalowano [program Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Ten skrypt służy do tworzenia nowego obszaru roboczego usługi Log Analytics i dodawania do niego rozwiązania sieci szkieletowej usług: 

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

Po zakończeniu wykonaj kroki opisane w poprzedniej sekcji, aby połączyć dzienniki usługi Azure Monitor z odpowiednim kontem magazynu.

Można również dodać inne rozwiązania lub wprowadzić inne modyfikacje do obszaru roboczego usługi Log Analytics przy użyciu programu PowerShell. Aby dowiedzieć się więcej, zobacz [Zarządzanie dziennikami usługi Azure Monitor przy użyciu programu PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Następne kroki
* [Wdrażanie agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md) w węzłach w celu zbierania liczników wydajności i zbierania statystyk i dzienników docker dla kontenerów
* Zapoznaj się z funkcjami [wyszukiwania i wyszukiwania w dziennikach](../log-analytics/log-analytics-log-searches.md) oferowanych w ramach dzienników usługi Azure Monitor
* [Tworzenie widoków niestandardowych w dziennikach usługi Azure Monitor za pomocą projektanta widoków](../azure-monitor/platform/view-designer.md)
