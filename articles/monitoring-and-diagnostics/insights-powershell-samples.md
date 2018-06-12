---
title: Przykładów dla platformy Azure Monitor PowerShell szybki start
description: Dostęp do funkcji Azure Monitor, takich jak skalowania automatycznego, alertów, elementów webhook i wyszukiwanie Dzienniki aktywności za pomocą programu PowerShell.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/14/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: d7275bb0399ae5832e85089e44f4b9a1fe412747
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262423"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Przykładów dla platformy Azure Monitor PowerShell szybki start
Ten artykuł przedstawia przykładowe polecenia programu PowerShell, aby ułatwić dostęp do funkcji Azure monitora.

> [!NOTE]
> Azure Monitor to nowa nazwa dla proponowaną "Azure Insights" do 25 września 2016 r. Jednak przestrzenie nazw, dlatego poniższe polecenia nadal zawierać słowo "insights".
> 
> 

## <a name="set-up-powershell"></a>Konfigurowanie środowiska PowerShell
Jeśli nie jest jeszcze zdefiniować programu PowerShell do uruchamiania na komputerze. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Przykłady w tym artykule
Przykłady w artykule pokazują, jak można użyć poleceń cmdlet Azure monitora. Można również przejrzeć całą listę poleceń cmdlet programu PowerShell usługi Azure monitora na [poleceń cmdlet Azure monitora (Insights)](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).

## <a name="sign-in-and-use-subscriptions"></a>Zaloguj się i korzystać z subskrypcji
Po pierwsze Zaloguj się do subskrypcji platformy Azure.

```PowerShell
Connect-AzureRmAccount
```

Zobaczysz ekran logowania, w. Po zalogowaniu się konto dla identyfikatora dzierżawcy, i domyślnego Identyfikatora subskrypcji są wyświetlane. Wszystkie polecenia cmdlet systemu Azure działają w kontekście subskrypcji domyślne. Aby wyświetlić subskrypcje, do których masz dostęp do listy, użyj następującego polecenia:

```PowerShell
Get-AzureRmSubscription
```

Aby zmienić kontekst pracy do innej subskrypcji, użyj następującego polecenia:

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Pobrać dziennik aktywności dla subskrypcji
Użyj `Get-AzureRmLog` polecenia cmdlet.  Poniżej przedstawiono niektóre typowe przykłady.

Pobierz wpisy dziennika z tym datę i godzinę do prezentowania:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Pobierz wpisy dziennika z zakresu daty/godziny:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wpisy dziennika z danej grupy zasobów:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Wpisy dziennika należy uzyskać od dostawcy zasobów określonego zakresu daty/godziny:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wszystkie wpisy dziennika z określonym obiektem wywołującym:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

Polecenie pobiera ostatnich 1000 zdarzenia z dziennika aktywności:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` obsługuje wiele innych parametrów. Zobacz `Get-AzureRmLog` odwołania, aby uzyskać więcej informacji.

> [!NOTE]
> `Get-AzureRmLog` zapewnia tylko 15 dni historii. Przy użyciu **- MaxEvents** parametr umożliwia zapytania ostatniego N zdarzeń ponad 15 dni. Aby dostępu zdarzenia starsze niż 15 dni należy użyć interfejsu API REST lub zestawu SDK (C# przykład przy użyciu zestawu SDK). Jeśli nie zostanie uwzględniony **StartTime**, to wartością domyślną jest **EndTime** minus jedną godzinę. Jeśli nie zostanie uwzględniony **EndTime**, to wartością domyślną jest bieżący czas. Wszystkie godziny są w formacie UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Pobrać historii alertów
Aby wyświetlić wszystkie zdarzenia alertu, można zbadać dzienniki usługi Azure Resource Manager za pomocą poniższych przykładów.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Aby wyświetlić historię dla określonej reguły alertu, można użyć `Get-AzureRmAlertHistory` polecenia cmdlet, przekazując identyfikator zasobu reguły alertu.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

`Get-AzureRmAlertHistory` Polecenie cmdlet obsługuje różne parametry. Więcej informacji, zobacz [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Pobieranie informacji na temat reguł alertów
Wszystkie z poniższych poleceń, działają na grupy zasobów o nazwie "montest".

Wyświetl wszystkie właściwości reguły alertu:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Pobieranie wszystkich alertów w grupie zasobów:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Pobierz wszystkie reguły alertu dla zasobu docelowego. Na przykład wszystkie reguły alertu ustawione na maszynie Wirtualnej.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` obsługuje inne parametry. Zobacz [Get AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) Aby uzyskać więcej informacji.

## <a name="create-metric-alerts"></a>Tworzenie metryk alertów
Można użyć `Add-AlertRule` polecenia cmdlet do tworzenia, aktualizowania ani wyłączyć regułę alertu.

Można utworzyć właściwości poczty e-mail i elementu webhook za pomocą `New-AzureRmAlertRuleEmail` i `New-AzureRmAlertRuleWebhook`odpowiednio. W poleceniu cmdlet reguły alertu, Przypisz te właściwości jako akcje **akcje** właściwości reguły alertu.

W poniższej tabeli opisano parametry i wartości używane do utworzenia alertu za pomocą metryki.

| parametr | wartość |
| --- | --- |
| Name (Nazwa) |simpletestdiskwrite |
| Lokalizacja tę regułę alertów |Wschodnie stany USA |
| ResourceGroup |montest |
| Element TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName alertu, który jest tworzony |\Disk \PhysicalDisk (_Total) / s. Zobacz `Get-MetricDefinitions` polecenia cmdlet dotyczące pobrania dokładne metryki nazw |
| Operator |GreaterThan |
| Wartość progowa (liczba/s w tym metryki) |1 |
| Rozmiar_okna (w formacie hh: mm:) |00:05:00 |
| agregatora (Statystyka metryki, który używa w tym przypadku średnia liczba) |Średnia |
| niestandardowe wiadomości e-mail (tablicy ciągów) |'foo@example.com','bar@example.com' |
| Wyślij wiadomość e-mail do właściciele, współautorzy i czytelnicy |-SendToServiceOwners |

Tworzy akcję, poczty E-mail

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Utworzenie elementu Webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Utwórz regułę alertu na metryki % procesora CPU na klasycznej maszyny Wirtualnej

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Pobieranie reguły alertu

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Alert polecenia cmdlet Add aktualizuje również reguły, jeśli reguły alertu już istnieje dla danej właściwości. Aby wyłączyć regułę alertu, należy dodać parametr **- DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Pobierz listę dostępnych metryk dla alertów
Można użyć `Get-AzureRmMetricDefinition` polecenia cmdlet, aby wyświetlić listę wszystkich metryki dla określonego zasobu.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

Poniższy przykład generuje tabeli o nazwie metryki i jednostki dla niego.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pełną listę dostępnych opcji `Get-AzureRmMetricDefinition` znajduje się w temacie [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Tworzenie i Zarządzanie alertami dziennik aktywności
Można użyć `Set-AzureRmActivityLogAlert` polecenia cmdlet, aby ustawić alert dziennik aktywności. Alert dziennik aktywności wymaga czy najpierw zdefiniować warunki jako słownik warunków, a następnie tworzony alert, który używa tych warunków.

```PowerShell

$condition1 = New-AzureRmActivityLogAlertCondition -Field 'category' -Equals 'Administrative'
$condition2 = New-AzureRmActivityLogAlertCondition -Field 'operationName' -Equals 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzureRmActionGroup -ActionGroupId 'actiongr1' -WebhookProperties $dict
Set-AzureRmActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/' -Action $actionGrp1 -Condition $condition1, $condition2

```

Właściwości elementu webhook dodatkowe są opcjonalne. Możesz odzyskać zawartość działania dziennika alertów za pomocą `Get-AzureRmActivityLogAlert`.

## <a name="create-and-manage-autoscale-settings"></a>Utwórz i Zarządzaj ustawieniami automatycznego skalowania
Zasób (aplikacja sieci Web, maszyn wirtualnych, usługa w chmurze lub zestawu skalowania maszyn wirtualnych) może mieć tylko jedno ustawienie skalowania automatycznego skonfigurowane pod jego kątem.
Jednak każdego ustawienia automatycznego skalowania może mieć wiele profilów. Na przykład jeden profil na podstawie wydajności skali i drugi dla profilu oparte na harmonogramie. Każdy profil może mieć wiele reguł skonfigurowane na nim. Aby uzyskać więcej informacji na temat skalowania automatycznego, zobacz [sposobu skalowania automatycznego aplikacji](../cloud-services/cloud-services-how-to-scale-portal.md).

Poniżej przedstawiono kroki, aby użyć:

1. Tworzenie reguł.
2. Utwórz Profile mapowania reguł, które zostały utworzone wcześniej do profilów.
3. Opcjonalnie: Utworzyć powiadomienia na potrzeby skalowania automatycznego konfigurowania właściwości elementu webhook i poczty e-mail.
4. Utwórz ustawienie skalowania automatycznego o nazwie nad zasobem docelowym przez mapowanie profile i powiadomienia, które zostały utworzone w poprzednich krokach.

Poniższe przykłady przedstawiają sposób tworzenia ustawieniu skalowania automatycznego dla zestawu skalowania maszyn wirtualnych systemu operacyjnego Windows za pomocą metryki użycia procesora CPU.

Najpierw utwórz regułę w celu skalowania o zwiększenie liczby wystąpień.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Następnie należy utworzyć regułę skalować, zmniejszenie liczby wystąpień.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Następnie utwórz profil dla reguły.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Utwórz właściwość elementu webhook.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Utwórz właściwość powiadomień w ustawieniu skalowania automatycznego, w tym wiadomości e-mail i utworzonego wcześniej elementu webhook.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Na koniec Utwórz w ustawieniu skalowania automatycznego, aby dodać profil, który został utworzony wcześniej. 

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Aby uzyskać więcej informacji o zarządzaniu Ustawienia skalowania automatycznego, zobacz [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Historia skalowania automatycznego
Poniższy przykład przedstawia, jak można wyświetlić ostatnie skalowania automatycznego i zdarzenia alertu. Umożliwia wyświetlenie historii skalowania automatycznego wyszukiwania dziennika aktywności.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Można użyć `Get-AzureRmAutoScaleHistory` polecenia cmdlet można pobrać historii automatycznego skalowania.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Aby uzyskać więcej informacji, zobacz [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Wyświetlanie szczegółów dla ustawienie automatycznego skalowania
Można użyć `Get-Autoscalesetting` polecenia cmdlet, aby pobrać więcej informacji o ustawieniu skalowania automatycznego.

Poniższy przykład przedstawia szczegółowe informacje o wszystkich ustawień automatycznego skalowania w grupie zasobów "myrg1".

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

Poniższy przykład przedstawia szczegółowe informacje dotyczące wszystkie ustawienia skalowania automatycznego w grupie zasobów "myrg1", a w szczególności w ustawieniu skalowania automatycznego o nazwie "MyScaleVMSSSetting".

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Usuń ustawienie skalowania automatycznego
Można użyć `Remove-Autoscalesetting` polecenia cmdlet, aby usunąć ustawienia skalowania automatycznego.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Zarządzanie profilami dziennika dla dziennika aktywności
Można utworzyć *dziennika profilu* i eksportu danych z dziennika aktywności konta magazynu i należy skonfigurować przechowywania danych dla niego. Opcjonalnie można również strumienia danych do Centrum zdarzeń. Ta funkcja jest obecnie w wersji zapoznawczej i można utworzyć tylko jeden profil dziennika na subskrypcję. Za pomocą następujących poleceń cmdlet i Twojej bieżącej subskrypcji do tworzenia i zarządzania profilami dziennika. Możesz również określonej subskrypcji. Mimo że domyślne programu PowerShell do bieżącej subskrypcji, zawsze można zmienić tego za pomocą `Set-AzureRmContext`. Można skonfigurować dziennik aktywności na przesyłanie danych do dowolnego konta magazynu lub Centrum zdarzeń, w ramach danej subskrypcji. Dane są zapisywane jako pliki obiektu blob w formacie JSON.

### <a name="get-a-log-profile"></a>Pobierz profil dziennika
Aby pobrać profili istniejącego dziennika, należy użyć `Get-AzureRmLogProfile` polecenia cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Dodawanie profilu dziennika bez przechowywania danych
```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Usuń profil dziennika
```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Dodawanie profilu dziennika z przechowywaniem danych
Można określić **- RetentionInDays** właściwości z liczbą dni, jako dodatnią liczbą całkowitą, gdzie są przechowywane dane.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Dodawanie profilu dziennika z przechowywania i EventHub
Oprócz routingu danych do konta magazynu, można również strumienia go do Centrum zdarzeń. W tej wersji zapoznawczej konfiguracji konta magazynu jest wymagane, ale konfiguracja Centrum zdarzeń jest opcjonalne.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Konfigurowanie dzienników diagnostycznych
Wiele usług platformy Azure zapewniają dodatkowe dzienniki i dane telemetryczne, które można wykonać jedną lub więcej z następujących czynności: 
 - można skonfigurować w celu zapisywania danych na koncie magazynu Azure
 - wysyłane do usługi Event Hubs
 - wysyłane do obszaru roboczego analizy dzienników. 

Tę operację można wykonać tylko na poziomie zasobów. Centrum konta lub zdarzenia magazynu powinien znajdować się w tym samym regionie co zasób docelowy, na którym skonfigurowano ustawienie diagnostyki.

### <a name="get-diagnostic-setting"></a>Pobierz ustawienia diagnostyki
```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Wyłącz ustawienie diagnostyczne

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Włącz ustawienie diagnostyczne bez przechowywania

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Włącz ustawienie diagnostyczne z przechowywaniem

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Włącz ustawienie diagnostyczne z przechowywania dla kategorii określonego dziennika

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Włącz ustawienie diagnostyczne dla usługi Event Hubs

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Włącz ustawienie diagnostyczne dla analizy dzienników

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Należy pamiętać, że właściwość WorkspaceId przyjmuje *identyfikator zasobu* obszaru roboczego. Można uzyskać Identyfikatora zasobu obszaru roboczego analizy dzienników przy użyciu następującego polecenia:

```PowerShell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId

```

Te polecenia można łączyć z wysyłania danych do wielu miejsc docelowych.
