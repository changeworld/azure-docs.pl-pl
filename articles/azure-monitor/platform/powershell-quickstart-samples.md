---
title: Przykłady — szybki start usługi Azure Monitor programu PowerShell
description: Dostęp do funkcji usługi Azure Monitor, takich jak automatyczne skalowanie, alerty, elementy webhook i wyszukiwania dzienników aktywności za pomocą programu PowerShell.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/14/2018
ms.author: robb
ms.subservice: ''
ms.openlocfilehash: 59cb14c86963d956b0bd63f65b10776dff4aa97f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452725"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Przykłady — szybki start usługi Azure Monitor programu PowerShell
Ten artykuł przedstawia przykładowe polecenia programu PowerShell, aby ułatwić dostęp do funkcji usługi Azure Monitor.

> [!NOTE]
> Usługa Azure Monitor to nowa nazwa dla proponowaną "Usługi Azure Insights" do 25 września 2016 r. Jednak przestrzenie nazw i w związku z tym następujące polecenia nadal zawierać słowo "insights".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Konfigurowanie programu PowerShell
Jeśli jeszcze ich, konfigurowanie programu PowerShell do uruchamiania na komputerze. Aby uzyskać więcej informacji, zobacz [jak instalowanie i konfigurowanie programu PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Przykłady w niniejszym artykule
Przykłady w artykule pokazują, jak można użyć poleceń cmdlet usługi Azure Monitor. Możesz również przejrzeć całą listę Azure Monitor poleceń cmdlet programu PowerShell w [poleceń cmdlet usługi Azure Monitor (Insights)](https://docs.microsoft.com/powershell/module/az.applicationinsights).

## <a name="sign-in-and-use-subscriptions"></a>Zaloguj się i używać subskrypcji
Najpierw zaloguj się do subskrypcji platformy Azure.

```powershell
Connect-AzAccount
```

Zostanie wyświetlony ekran logowania. Po zalogowaniu się na Twoim koncie, identyfikator dzierżawy, a domyślny identyfikator subskrypcji są wyświetlane. Wszystkie polecenia cmdlet platformy Azure działa w ramach subskrypcji domyślnej. Aby wyświetlić listę subskrypcji masz dostęp, użyj następującego polecenia:

```powershell
Get-AzSubscription
```

Aby zmienić kontekst pracy do innej subskrypcji, użyj następującego polecenia:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Pobieranie dziennika aktywności dla subskrypcji
Użyj `Get-AzLog` polecenia cmdlet.  Poniżej przedstawiono kilka typowych przykładów.

Pobierz wpisy dziennika tej daty/godziny do przedstawienia:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Pobierz wpisy dziennika z zakresu daty/godziny:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wpisy dziennika w określonej grupie zasobów:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Pobierz wpisy dziennika od dostawcy określonego zasobu z zakresu daty/godziny:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wszystkie wpisy dziennika przy użyciu określonego obiektu wywołującego:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Następujące polecenie pobiera ostatnie 1000 zdarzeń z dziennika aktywności:

```powershell
Get-AzLog -MaxEvents 1000
```

`Get-AzLog` obsługuje wiele innych parametrów. Zobacz `Get-AzLog` odwołania, aby uzyskać więcej informacji.

> [!NOTE]
> `Get-AzLog` zapewnia tylko 15 dni historii. Za pomocą **— wartości elementu MaxEvents** parametr umożliwia zapytania ostatnie N zdarzeń dłużej niż 15 dni. Aby zdarzenia dostępu do starszych niż 15 dni należy użyć interfejsu API REST lub zestawu SDK (C# przykład za pomocą zestawu SDK). Jeśli nie dołączysz **StartTime**, wówczas wartość domyślna to **EndTime** pomniejszona o jedną godzinę. Jeśli nie dołączysz **EndTime**, wartością domyślną jest bieżący czas. Wszystkie godziny są w formacie UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Pobieranie historii alertów
Aby wyświetlić wszystkie zdarzenia alertów, można tworzyć zapytania dzienniki usługi Azure Resource Manager przy użyciu poniższych przykładach.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Aby wyświetlić historię dla określonej reguły alertu, można użyć `Get-AzAlertHistory` polecenia cmdlet, przekazując identyfikator zasobu reguły alertu.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

`Get-AzAlertHistory` Polecenie cmdlet obsługuje różne parametry. Więcej informacji, zobacz [Get AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Pobieranie informacji na temat reguł alertów
Wszystkie z poniższych poleceń, działają na grupę zasobów o nazwie "montest".

Wyświetl wszystkie właściwości reguły alertu:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Pobieranie wszystkich alertów w grupie zasobów:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Pobieranie wszystkich reguł alertów dla zasobu docelowego. Na przykład wszystkich reguł alertów jest ustawiony na maszynie Wirtualnej.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` obsługuje inne parametry. Zobacz [Get AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) Aby uzyskać więcej informacji.

## <a name="create-metric-alerts"></a>Tworzenie alertów dotyczących metryk
Możesz użyć `Add-AlertRule` polecenia cmdlet w celu utworzenia, aktualizacji lub wyłączyć regułę alertu.

Można utworzyć właściwości wiadomości e-mail i elementy webhook, za pomocą `New-AzAlertRuleEmail` i `New-AzAlertRuleWebhook`, odpowiednio. W poleceniu cmdlet reguły alertu, Przypisz te właściwości jako akcje **akcje** właściwości reguły alertu.

W poniższej tabeli opisano parametry i wartości używane do tworzenia alertu za pomocą metryk.

| parametr | value |
| --- | --- |
| Name (Nazwa) |simpletestdiskwrite |
| Lokalizacja tę regułę alertu |Wschodnie stany USA |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName alertu, który jest tworzony |\PhysicalDisk (%) (_łącznie) \Disk zapisu na sekundę. Zobacz `Get-MetricDefinitions` polecenia cmdlet, o tym, jak pobrać dokładnej nazwy metryki |
| operator |GreaterThan |
| Wartość progowa (liczba/s w tym metryki) |1 |
| Rozmiar_okna (w formacie: mm: ss) |00:05:00 |
| agregatora (Statystyka metryki, która używa w tym przypadku średnia liczba) |Średnia |
| niestandardowe wiadomości e-mail (tablicy ciągów) |'foo@example.com','bar@example.com' |
| Wyślij wiadomość e-mail do właściciele, współautorzy i czytelnicy |-SendToServiceOwners |

Tworzenie akcji w wiadomości E-mail

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Tworzenie akcji elementu Webhook

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Utwórz regułę alertu na metryce % procesora CPU na klasycznej maszynie Wirtualnej

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Pobieranie reguły alertu

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Alertu polecenia cmdlet Add aktualizuje również reguły, jeśli reguły alertu, już istnieje dla danej właściwości. Aby wyłączyć reguły alertu, Uwzględnij parametr **- DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Pobierz listę dostępnych metryk dla alertów
Możesz użyć `Get-AzMetricDefinition` polecenia cmdlet, aby wyświetlić listę wszystkich metryk dla określonego zasobu.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

Poniższy przykład generuje tabelę o nazwie metryki i jednostki dla niego.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pełną listę dostępnych opcji `Get-AzMetricDefinition` znajduje się w temacie [Get MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Tworzenie i zarządzanie nimi alertów dziennika aktywności
Możesz użyć `Set-AzActivityLogAlert` polecenia cmdlet, aby ustawić alert dziennika aktywności. Alert dziennika aktywności zostanie wymaga, należy najpierw zdefiniować warunkach jako słownik warunków, a następnie Utwórz alert, który używa tych warunków.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

Właściwości elementu webhook dodatkowe są opcjonalne. Możesz odzyskać zawartość alertu dziennika aktywności przy użyciu `Get-AzActivityLogAlert`.

## <a name="create-and-manage-autoscale-settings"></a>Tworzenie i zarządzanie nimi ustawień automatycznego skalowania
Zasób (aplikacja sieci Web, maszyn wirtualnych, usługi w chmurze lub zestawu skalowania maszyn wirtualnych) może mieć tylko jedno ustawienie skalowania automatycznego w skonfigurowanych.
Jednak każde ustawienie automatycznego skalowania może mieć wiele profilów. Na przykład jeden dla profilu skalowania na podstawie wydajności i drugi dla profilu sieci na podstawie harmonogramu. Każdy profil może zawierać wiele reguł, które skonfigurowano w nim. Aby uzyskać więcej informacji na temat automatycznego skalowania, zobacz [jak automatyczne skalowanie aplikacji](../../cloud-services/cloud-services-how-to-scale-portal.md).

Poniżej przedstawiono kroki, aby użyć:

1. Tworzenie reguł.
2. Utwórz profile mapowanie reguł, które zostały utworzone wcześniej do profilów.
3. Opcjonalnie: Tworzenie powiadomienia dotyczące automatycznego skalowania, konfigurując właściwości elementu webhook i wiadomości e-mail.
4. Tworzenie ustawienia automatycznego skalowania o nazwie nad zasobem docelowym przez mapowanie profile i powiadomień, które zostały utworzone w poprzednich krokach.

W poniższych przykładach pokazano, jak utworzyć ustawienia automatycznego skalowania dla zestawu skalowania maszyn wirtualnych dla systemu Windows za pomocą metryk użycia procesora CPU.

Najpierw należy utworzyć zasadę, aby skalować w poziomie, za pomocą wzrost liczby wystąpień.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Następnie utwórz regułę, skalować, zmniejszenie liczby wystąpień.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Następnie utwórz profil dla reguły.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Utwórz właściwość elementu webhook.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Utwórz właściwość powiadomienia, ustawienia automatycznego skalowania, w tym wiadomości e-mail i elementu webhook, który został utworzony wcześniej.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Na koniec Utwórz ustawienie skalowania automatycznego, aby dodać profil, który został utworzony wcześniej. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Aby uzyskać więcej informacji o zarządzaniu ustawień automatycznego skalowania, zobacz [Get AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Historia automatycznego skalowania
Poniższy przykład pokazuje, jak można wyświetlić ostatnie automatycznego skalowania i zdarzenia alertów. Aby wyświetlić historię skalowania automatycznego, należy użyć wyszukiwanie w dzienniku aktywności.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Możesz użyć `Get-AzAutoScaleHistory` polecenia cmdlet, aby pobrać historii automatycznego skalowania.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Aby uzyskać więcej informacji, zobacz [Get AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Wyświetl szczegóły ustawienia autoskalowania
Możesz użyć `Get-Autoscalesetting` polecenie cmdlet do pobierania informacji o ustawieniu skalowania automatycznego.

Poniższy przykład przedstawia szczegółowe informacje o wszystkich ustawień automatycznego skalowania w grupie zasobów "myrg1".

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

Poniższy kod przedstawia szczegółowe informacje o wszystkich ustawień skalowania automatycznego w grupie zasobów "myrg1" i specjalnie ustawienie automatycznego skalowania o nazwie "MyScaleVMSSSetting".

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Usuń ustawienia automatycznego skalowania
Możesz użyć `Remove-Autoscalesetting` polecenia cmdlet, aby usunąć ustawienia automatycznego skalowania.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Zarządzanie profilami dziennika dla dziennika aktywności
Możesz utworzyć *profil dziennika* i Eksportuj dane z dziennika aktywności na koncie magazynu i możesz skonfigurować przechowywania danych dla niego. Opcjonalnie możesz również przesyłać strumieniowo dane do Centrum zdarzeń. Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a następnie można utworzyć tylko jeden profil dziennika na subskrypcję. Za pomocą następujących poleceń cmdlet i Twojej bieżącej subskrypcji można utworzyć i zarządzać profilami dziennika. Możesz również określonej subskrypcji. Mimo że program PowerShell wartością domyślną jest bieżąca subskrypcja, zawsze można zmienić, przy użyciu `Set-AzContext`. Można skonfigurować dziennika aktywności do kierowania danych do wszystkich kont magazynu lub Centrum zdarzeń w ramach tej subskrypcji. Dane są zapisywane jako pliki obiektów blob w formacie JSON.

### <a name="get-a-log-profile"></a>Pobieranie profilu dziennika
Aby pobrać swoje istniejące profile dziennika, należy użyć `Get-AzLogProfile` polecenia cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Dodaj profil dziennika bez przechowywania danych
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Usuwanie profilu dziennika
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Dodaj profil dziennika z przechowywaniem danych
Można określić **- RetentionInDays** właściwości z liczbą dni, jako dodatnią liczbą całkowitą, w którym dane są zachowywane.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Dodaj profil dziennika przechowywania i Centrum zdarzeń
Oprócz routingu danych do konta magazynu, użytkownik może również Prześlij go strumieniowo do Centrum zdarzeń. W tej wersji zapoznawczej konfigurację konta magazynu jest wymagane, ale konfiguracja Centrum zdarzeń jest opcjonalna.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Skonfiguruj dzienniki diagnostyczne
Wiele usług platformy Azure zapewniają dodatkowe dzienniki i dane telemetryczne, które można wykonać co najmniej jeden z następujących czynności: 
 - można skonfigurować w celu zapisywania danych na swoim koncie usługi Azure Storage
 - wysyłane do usługi Event Hubs
 - wysyłane do obszaru roboczego usługi Log Analytics. 

Operację można wykonać tylko na poziomie zasobów. Centrum konta lub zdarzenia magazynu powinny znajdować się w tym samym regionie co zasób docelowy, na której skonfigurowano ustawienia diagnostyki.

### <a name="get-diagnostic-setting"></a>Pobierz ustawienie diagnostyczne
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Wyłącz ustawienie diagnostyczne

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Włącz ustawienie diagnostyczne bez przechowywania

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Włącz ustawienie diagnostyczne z przechowywaniem

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Włącz ustawienie diagnostyczne z przechowywaniem dla kategorii określonego dziennika

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Włącz ustawienie diagnostyczne dla usługi Event Hubs

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Włącz ustawienie diagnostyczne dla usługi Log Analytics

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Należy zauważyć, że właściwość WorkspaceId przyjmuje *identyfikator zasobu* obszaru roboczego. Możesz uzyskać identyfikator zasobu obszaru roboczego usługi Log Analytics przy użyciu następującego polecenia:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Te polecenia mogą być połączone do wysyłania danych do wielu miejsc docelowych.

