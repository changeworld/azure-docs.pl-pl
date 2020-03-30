---
title: Szybki start — przykłady poleceń programu PowerShell usługi Azure Monitor
description: Użyj programu PowerShell, aby uzyskać dostęp do funkcji usługi Azure Monitor, takich jak skalowanie automatyczne, alerty, elementy webhook i dzienniki aktywności wyszukiwania.
ms.subservice: ''
ms.topic: conceptual
ms.date: 2/14/2018
ms.openlocfilehash: 9f039f71954998ef561d1efd1e559318740c86ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274322"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Szybki start — przykłady poleceń programu PowerShell usługi Azure Monitor
W tym artykule przedstawiono przykładowe polecenia programu PowerShell ułatwiające dostęp do funkcji usługi Azure Monitor.

> [!NOTE]
> Usługa Azure Monitor to nowa nazwa usługi "Usługa Azure Insights" do 25 września 2016 r. Jednak obszary nazw, a tym samym następujące polecenia nadal zawierają słowo "insights".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Konfigurowanie programu PowerShell
Jeśli jeszcze tego nie zrobiłeś, skonfiguruj program PowerShell do uruchamiania na komputerze. Aby uzyskać więcej informacji, zobacz [Jak zainstalować i skonfigurować program PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Przykłady w tym artykule
Przykłady w tym artykule ilustrują, jak można używać poleceń cmdlet usługi Azure Monitor. Można również przejrzeć całą listę poleceń cmdlet programu Azure Monitor PowerShell w poleceniach [cmdlet usługi Azure Monitor (Insights).](https://docs.microsoft.com/powershell/module/az.applicationinsights)

## <a name="sign-in-and-use-subscriptions"></a>Logowanie się i korzystanie z subskrypcji
Najpierw zaloguj się do subskrypcji platformy Azure.

```powershell
Connect-AzAccount
```

Zostanie wyświetlony ekran logowania. Po zalogowaniu się na swoje konto, TenantID i domyślny identyfikator subskrypcji są wyświetlane. Wszystkie polecenia cmdlet platformy Azure działają w kontekście domyślnej subskrypcji. Aby wyświetlić listę subskrypcji, do których masz dostęp, użyj następującego polecenia:

```powershell
Get-AzSubscription
```

Aby wyświetlić kontekst roboczy (który subskrypcja, przeciwko której są uruchamiane polecenia, użyj następującego polecenia:

```powershell
Get-AzContext
```
Aby zmienić kontekst pracy na inną subskrypcję, użyj następującego polecenia:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Pobieranie dziennika aktywności dla subskrypcji
Użyj polecenia cmdlet [Get-AzLog.](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog)  Oto kilka typowych przykładów. Dziennik aktywności zawiera ostatnie 90 dni operacji. Użycie dat przed tą godziną powoduje komunikat o błędzie.  

Zobacz, jaka jest bieżąca data/godzina, aby sprawdzić, jakich godzin użyć w poniższych poleceniach:
```powershell
Get-Date
```

Pobierz wpisy dziennika od tej godziny /daty do chwili obecnej:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Pobierz wpisy dziennika między zakresem czasu/daty:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wpisy dziennika z określonej grupy zasobów:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Pobierz wpisy dziennika od określonego dostawcy zasobów między zakresem czasu/daty:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wszystkie wpisy dziennika z określonym rozmówcą:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Następujące polecenie pobiera ostatnie 1000 zdarzeń z dziennika działań:

```powershell
Get-AzLog -MaxRecord 10
```

`Get-AzLog`obsługuje wiele innych parametrów. Zobacz `Get-AzLog` odwołanie, aby uzyskać więcej informacji.

> [!NOTE]
> `Get-AzLog`zapewnia tylko 15 dni historii. Za pomocą **parametru -MaxRecords** umożliwia wykonywanie zapytań o ostatnie zdarzenia N, poza 15 dni. Aby uzyskać dostęp do zdarzeń starszych niż 15 dni, użyj interfejsu API REST lub SDK (przykład języka C# przy użyciu zestawu SDK). Jeśli nie zostanie uwzględnieni **StartTime**, wartość domyślna to **EndTime** minus jedna godzina. Jeśli nie zostanie uwzględnieni **EndTime**, wartość domyślna to bieżący czas. Wszystkie czasy są w UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Pobieranie historii alertów
Aby wyświetlić wszystkie zdarzenia alertów, można zbadać dzienniki usługi Azure Resource Manager przy użyciu następujących przykładów.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Aby wyświetlić historię dla określonej reguły `Get-AzAlertHistory` alertu, można użyć polecenia cmdlet, przekazując w identyfikatorze zasobu reguły alertu.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Polecenie `Get-AzAlertHistory` cmdlet obsługuje różne parametry. Więcej informacji można znaleźć w [pliku Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Pobieranie informacji o regułach alertów
Wszystkie poniższe polecenia działają w grupie zasobów o nazwie "montest".

Wyświetl wszystkie właściwości reguły alertu:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Pobieranie wszystkich alertów w grupie zasobów:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Pobierz wszystkie reguły alertów ustawione dla zasobu docelowego. Na przykład wszystkie reguły alertów ustawione na maszynie Wirtualnej.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule`obsługuje inne parametry. Aby uzyskać więcej informacji, zobacz [Get-AlertRule.](https://msdn.microsoft.com/library/mt282459.aspx)

## <a name="create-metric-alerts"></a>Tworzenie alertów dotyczących metryk
Polecenia `Add-AlertRule` cmdlet można użyć do utworzenia, zaktualizowania lub wyłączenia reguły alertu.

Można tworzyć właściwości poczty e-mail `New-AzAlertRuleEmail` `New-AzAlertRuleWebhook`i webhook przy użyciu i , odpowiednio. W powiększenie reguły alertu przypisz te właściwości jako akcje do właściwości **Akcje** reguły alertu.

W poniższej tabeli opisano parametry i wartości używane do tworzenia alertu przy użyciu metryki.

| parametr | value |
| --- | --- |
| Nazwa |simpletestdiskwrite |
| Lokalizacja tej reguły alertu |Wschodnie stany USA |
| ResourceGroup |montest (montest) |
| Obiekt docelowyResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName tworzonego alertu |\PhysicalDisk(_Total)\Zapisy na dysku/s. Zobacz `Get-MetricDefinitions` polecenie cmdlet o tym, jak pobrać dokładne nazwy metryki |
| operator |GreaterThan |
| Wartość progowa (liczba/s dla tej metryki) |1 |
| Rozmiar okna (format hh:mm:ss) |00:05:00 |
| agregator (statystyka metryki, która w tym przypadku używa średniej liczby) |Średnia |
| niestandardowe wiadomości e-mail (tablica ciągów) |'foo@example.com','bar@example.com' |
| wysyłanie wiadomości e-mail do właścicieli, współpracowników i czytelników |-SendToServiceOwners |

Tworzenie akcji e-mail

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Tworzenie akcji elementu webhook

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Tworzenie reguły alertu na metryce cpu% na klasycznej maszynie Wirtualnej

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Pobieranie reguły alertu

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Polecenie cmdlet dodaj alert aktualizuje również regułę, jeśli reguła alertu już istnieje dla danych właściwości. Aby wyłączyć regułę alertu, należy dołączyć parametr **-DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Uzyskaj listę dostępnych danych dla alertów
Za pomocą `Get-AzMetricDefinition` polecenia cmdlet można wyświetlić listę wszystkich metryk dla określonego zasobu.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

Poniższy przykład generuje tabelę z metryką Name i Jednostką dla niej.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pełna lista dostępnych opcji `Get-AzMetricDefinition` jest dostępna w [get-metricdefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Tworzenie alertów dziennika aktywności i zarządzanie nimi
Za pomocą `Set-AzActivityLogAlert` polecenia cmdlet można ustawić alert dziennika aktywności. Alert dziennika aktywności wymaga, aby najpierw zdefiniować warunki jako słownik warunków, a następnie utworzyć alert, który używa tych warunków.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

Dodatkowe właściwości elementu webhook są opcjonalne. Zawartość alertu dziennika aktywności można odzyskać `Get-AzActivityLogAlert`za pomocą programu .

## <a name="create-and-manage-autoscale-settings"></a>Tworzenie ustawień autoskalować i zarządzanie nimi
Zasób (aplikacja sieci Web, maszyna wirtualna, usługa w chmurze lub zestaw skalowania maszyny wirtualnej) może mieć tylko jedno ustawienie skalowania automatycznego skonfigurowane dla niego.
Jednak każde ustawienie skalowania automatycznego może mieć wiele profili. Na przykład jeden dla profilu skalowania opartego na wydajności, a drugi dla profilu opartego na harmonogramie. Każdy profil może mieć wiele reguł skonfigurowanych na nim. Aby uzyskać więcej informacji na temat skalowania [automatycznego,](../../cloud-services/cloud-services-how-to-scale-portal.md)zobacz Jak automatycznie skalować aplikację .

Oto kroki, których należy użyć:

1. Utwórz reguły.
2. Utwórz profile mapujące reguły utworzone wcześniej na profile.
3. Opcjonalnie: tworzenie powiadomień do skalowania automatycznego przez skonfigurowanie właściwości elementu webhook i poczty e-mail.
4. Utwórz ustawienie skalowania automatycznego z nazwą zasobu docelowego, mapując profile i powiadomienia utworzone w poprzednich krokach.

Poniższe przykłady pokazują, jak można utworzyć ustawienie skalowania automatycznego dla zestawu skalowania maszyny wirtualnej dla systemu operacyjnego Windows na podstawie metryki wykorzystania procesora CPU.

Najpierw utwórz regułę do skalowania w poziomie, ze wzrostem liczby wystąpień.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Następnie utwórz regułę do skalowania, ze zmniejszeniem liczby wystąpień.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Następnie utwórz profil reguł.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Utwórz właściwość elementu webhook.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Utwórz właściwość powiadomienia dla ustawienia skalowania automatycznego, w tym wiadomości e-mail i elementu webhook utworzonego wcześniej.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Na koniec utwórz ustawienie skalowania automatycznego, aby dodać profil utworzony wcześniej. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Aby uzyskać więcej informacji na temat zarządzania ustawieniami skalowania [automatycznego, zobacz Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Historia skalowania automatycznego
W poniższym przykładzie pokazano, jak można wyświetlić ostatnie zdarzenia skalowania automatycznego i alertów. Użyj wyszukiwania dziennika aktywności, aby wyświetlić historię skalowania automatycznego.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Za pomocą `Get-AzAutoScaleHistory` polecenia cmdlet można pobrać historię skalowania automatycznego.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Aby uzyskać więcej informacji, zobacz [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Wyświetlanie szczegółów ustawienia skalowania automatycznego
Za pomocą `Get-Autoscalesetting` polecenia cmdlet można pobrać więcej informacji o ustawieniu skalowania automatycznego.

W poniższym przykładzie przedstawiono szczegółowe informacje o wszystkich ustawieniach skalowania automatycznego w grupie zasobów "myrg1".

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

W poniższym przykładzie przedstawiono szczegółowe informacje o wszystkich ustawieniach skalowania automatycznego w grupie zasobów "myrg1", a w szczególności o ustawieniu skalowania automatycznego o nazwie "MyScaleVMSSSetting".

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Usuwanie ustawienia skalowania automatycznego
Za pomocą `Remove-Autoscalesetting` polecenia cmdlet można usunąć ustawienie skalowania automatycznego.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Zarządzanie profilami dzienników dla dziennika aktywności
Można utworzyć *profil dziennika* i wyeksportować dane z dziennika aktywności na konto magazynu i skonfigurować przechowywanie danych dla niego. Opcjonalnie można również przesyłać strumieniowo dane do Centrum zdarzeń. Ta funkcja jest obecnie w wersji zapoznawczej i można utworzyć tylko jeden profil dziennika na subskrypcję. Do tworzenia profilów dzienników i zarządzania nimi można użyć następujących poleceń cmdlet. Można również wybrać określoną subskrypcję. Chociaż program PowerShell domyślnie wpływa na bieżącą `Set-AzContext`subskrypcję, zawsze można ją zmienić za pomocą programu . Można skonfigurować dziennik aktywności, aby rozsyłać dane do dowolnego konta magazynu lub Centrum zdarzeń w ramach tej subskrypcji. Dane są zapisywane jako pliki obiektów blob w formacie JSON.

### <a name="get-a-log-profile"></a>Uzyskaj profil dziennika
Aby pobrać istniejące profile dziennika, `Get-AzLogProfile` użyj polecenia cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Dodawanie profilu dziennika bez przechowywania danych
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Usuwanie profilu dziennika
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Dodawanie profilu dziennika z przechowywaniem danych
Można określić **-RetentionInDays** właściwość z liczbą dni, jako dodatnia liczba całkowita, gdzie dane są zachowywane.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Dodawanie profilu dziennika z przechowywaniem i eventhubem
Oprócz routingu danych do konta magazynu, można również przesyłać strumieniowo do Centrum zdarzeń. W tej wersji zapoznawczej konfiguracja konta magazynu jest obowiązkowa, ale konfiguracja Centrum zdarzeń jest opcjonalna.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Konfigurowanie dzienników diagnostycznych
Wiele usług platformy Azure zapewnia dodatkowe dzienniki i dane telemetryczne, które mogą wykonać co najmniej jedną z następujących czynności: 
 - można skonfigurować do zapisywania danych na koncie usługi Azure Storage
 - wysyłane do Centrów zdarzeń
 - wysyłane do obszaru roboczego usługi Log Analytics. 

Operację można wykonać tylko na poziomie zasobu. Konto magazynu lub centrum zdarzeń powinny znajdować się w tym samym regionie co zasób docelowy, w którym skonfigurowano ustawienie diagnostyki.

### <a name="get-diagnostic-setting"></a>Pobierz ustawienie diagnostyczne
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Wyłączanie ustawienia diagnostycznego

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Włączanie ustawień diagnostycznych bez retencji

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Włącz ustawienie diagnostyczne z retencją

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Włączanie ustawienia diagnostycznego z przechowywaniem dla określonej kategorii dziennika

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Włącz ustawienie diagnostyczne dla Centrów zdarzeń

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Włącz ustawienie diagnostyczne dla usługi Log Analytics

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Należy zauważyć, że właściwość WorkspaceId przyjmuje *identyfikator zasobu* obszaru roboczego. Identyfikator zasobu obszaru roboczego usługi Log Analytics można uzyskać za pomocą następującego polecenia:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Te polecenia można łączyć w celu wysyłania danych do wielu miejsc docelowych.

