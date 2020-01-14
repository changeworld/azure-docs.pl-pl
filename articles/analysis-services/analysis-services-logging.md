---
title: Rejestrowanie diagnostyczne dla Azure Analysis Services | Microsoft Docs
description: Opisuje sposób konfigurowania rejestrowania diagnostyki zasobów platformy Azure w celu monitorowania serwera Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 05ba1d97d4eba92f492289375f85425f8920510b
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749749"
---
# <a name="setup-diagnostic-logging"></a>Konfigurowanie rejestrowania diagnostycznego

Ważną częścią dowolnego rozwiązania usług Analysis Services to monitorowania, jak działają serwery. [Dzienniki diagnostyczne zasobów platformy Azure](../azure-monitor/platform/platform-logs-overview.md)umożliwiają monitorowanie i wysyłanie dzienników do [usługi Azure Storage](https://azure.microsoft.com/services/storage/), przesyłanie ich strumieniowo do [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i eksportowanie do [dzienników Azure monitor](../azure-monitor/azure-monitor-log-hub.md).

![Rejestrowanie diagnostyczne do magazynu, Event Hubs lub dzienników Azure Monitor](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Co to jest rejestrowane?

Możesz wybrać **aparatu**, **usługi**, i **metryki** kategorii.

### <a name="engine"></a>Aparat

Wybieranie **aparatu** rejestruje wszystkie [xEvents](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Nie można wybrać poszczególne zdarzenia. 

|Kategorie systemu XEvent |Nazwa zdarzenia  |
|---------|---------|
|Inspekcja zabezpieczeń    |   Inspekcja logowania      |
|Inspekcja zabezpieczeń    |   Inspekcja wylogowania      |
|Inspekcja zabezpieczeń    |   Audit Server uruchamia i zatrzymuje      |
|Sprawozdania z postępu prac     |   Początek raportu postępu      |
|Sprawozdania z postępu prac     |   Koniec raportu postępu      |
|Sprawozdania z postępu prac     |   Bieżący raport postępu      |
|Zapytania     |  Początek zapytania       |
|Zapytania     |   Koniec zapytania      |
|Polecenia     |  Polecenie Begin       |
|Polecenia     |  Koniec polecenia       |
|Błędy i ostrzeżenia     |   Błąd      |
|Wykrywaj     |   Odkryj zakończenia      |
|Powiadomienia     |    Powiadomienia     |
|Session     |  Inicjowanie sesji       |
|Blokady    |  Zakleszczenia       |
|Przetwarzanie zapytań     |   VertiPaq SE Query Begin      |
|Przetwarzanie zapytań     |   VertiPaq SE Query End      |
|Przetwarzanie zapytań     |   VertiPaq SE Query Cache Match      |
|Przetwarzanie zapytań     |   Początek zapytania bezpośredniego      |
|Przetwarzanie zapytań     |  Koniec zapytania bezpośredniego       |

### <a name="service"></a>Usługa

|Nazwa operacji  |Występuje, gdy  |
|---------|---------|
|ResumeServer     |    Wznów serwera     |
|SuspendServer    |   Wstrzymaj serwera      |
|DeleteServer     |    Usuwanie serwera     |
|RestartServer    |     Użytkownika spowoduje ponowne uruchomienie serwera za pomocą programu SSMS lub PowerShell    |
|GetServerLogFiles    |    Użytkownik eksportuje dziennika serwera za pomocą programu PowerShell     |
|ExportModel     |   Użytkownik eksportuje model w witrynie portal, za pomocą polecenia Otwórz w programie Visual Studio     |

### <a name="all-metrics"></a>Wszystkie metryki

Kategoria metryki rejestruje te same [metryki serwera](analysis-services-monitor.md#server-metrics) w tabeli AzureMetrics. Jeśli używasz [skalowania](analysis-services-scale-out.md) w poziomie kwerendy i musisz oddzielić metryki dla każdej repliki odczytu, zamiast tego użyj tabeli AzureDiagnostics, gdzie **OperationName** jest równa **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Konfigurowanie rejestrowania diagnostycznego

### <a name="azure-portal"></a>Portal Azure

1. W [witryny Azure portal](https://portal.azure.com) > serwera, kliknij przycisk **dzienniki diagnostyczne** w nawigacji po lewej stronie, a następnie kliknij przycisk **Włącz diagnostykę**.

    ![Włączanie rejestrowania diagnostycznego usługi Azure Cosmos DB w witrynie Azure portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. W **ustawień diagnostycznych**, określ następujące opcje: 

    * **Nazwa**. Wprowadź nazwę dla dzienników w celu tworzenia.

    * **Zarchiwizuj na koncie magazynu**. Aby użyć tej opcji, należy istniejące konto magazynu, aby nawiązać połączenie. Zobacz [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md). Postępuj zgodnie z instrukcjami, aby utworzyć Menedżera zasobów, konto ogólnego przeznaczenia, a następnie wybierz swoje konto magazynu, zwracając do tej strony w portalu. Może upłynąć kilka minut, zanim konta nowo utworzonego magazynu pojawią się w menu rozwijanym.
    * **Stream do usługi event hub**. Aby użyć tej opcji, należy istniejącego Centrum zdarzeń przestrzeni nazw i Centrum zdarzeń, aby nawiązać połączenie. Aby dowiedzieć się więcej, zobacz [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą witryny Azure portal](../event-hubs/event-hubs-create.md). Następnie wróć do tej strony w portalu, aby wybrać nazwę przestrzeni nazw i zasad Centrum zdarzeń.
    * **Wyślij do Azure monitor (log Analytics obszar roboczy)** . Aby użyć tej opcji, użyj istniejącego obszaru roboczego lub [Utwórz nowy zasób obszaru roboczego](../azure-monitor/learn/quick-create-workspace.md) w portalu. Aby uzyskać więcej informacji na temat wyświetlania dzienników, zobacz [Wyświetlanie dzienników w obszarze roboczym log Analytics](#view-logs-in-log-analytics-workspace) w tym artykule.

    * **Engine**. Wybierz tę opcję, aby rejestrować xEvents. Jeśli masz archiwizacji na koncie magazynu można wybrać okres przechowywania dla dzienników diagnostycznych. Dzienniki są autodeleted, po upływie okresu przechowywania.
    * **Usługa**. Wybierz tę opcję, aby rejestrować zdarzenia na poziomie usługi. Jeśli archiwizacji na koncie magazynu można wybrać okres przechowywania dla dzienników diagnostycznych. Dzienniki są autodeleted, po upływie okresu przechowywania.
    * **Metryki**. Wybierz tę opcję, aby przechowywać pełne dane w [metryki](analysis-services-monitor.md#server-metrics). Jeśli archiwizacji na koncie magazynu można wybrać okres przechowywania dla dzienników diagnostycznych. Dzienniki są autodeleted, po upływie okresu przechowywania.

3. Kliknij pozycję **Zapisz**.

    Jeśli otrzymasz komunikat o błędzie, który jest wyświetlany komunikat "nie można zaktualizować diagnostykę dla \<nazwa obszaru roboczego >. Subskrypcja \<identyfikator subskrypcji > nie jest zarejestrowana do używania microsoft.insights. " Postępuj zgodnie z [Rozwiązywanie problemów z usługi Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) instrukcjami, aby zarejestrować konto, następnie ponów próbę wykonania tej procedury.

    Jeśli chcesz zmienić, jak dzienników diagnostycznych są zapisywane w dowolnym momencie w przyszłości, można zwrócić się do tej strony, aby zmodyfikować ustawienia.

### <a name="powershell"></a>PowerShell

Poniżej przedstawiono podstawowe polecenia, które umożliwią Ci pracę. Jeśli chcesz, aby pomoc krok po kroku dotyczące konfigurowania logowania na koncie magazynu przy użyciu programu PowerShell, zapoznaj się z samouczkiem, w dalszej części tego artykułu.

Aby włączyć metryki i Diagnostyka rejestrowania przy użyciu programu PowerShell, użyj następujących poleceń:

- Aby włączyć magazyn dzienniki diagnostyczne na koncie magazynu, użyj tego polecenia:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Identyfikator konta magazynu jest identyfikator zasobu dla konta magazynu, w której chcesz wysłać dzienniki.

- Aby włączyć strumieniowe przesyłanie dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Identyfikator reguły usługi Azure Service Bus jest ciągiem o następującym formacie:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego usługi Log Analytics, użyj tego polecenia:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Identyfikator zasobu obszaru roboczego usługi Log Analytics można uzyskać za pomocą następującego polecenia:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Można połączyć te parametry, aby włączyć wiele opcji danych wyjściowych.

### <a name="rest-api"></a>Interfejs API REST

Dowiedz się, jak [zmiany ustawień diagnostycznych przy użyciu interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Dowiedz się, jak [włączanie ustawień diagnostycznych podczas tworzenia zasobów przy użyciu szablonu usługi Resource Manager](../azure-monitor/platform/diagnostic-settings-template.md). 

## <a name="manage-your-logs"></a>Zarządzasz dziennikami

Dzienniki są zwykle dostępne w ciągu kilku godzin konfigurowania rejestrowania. To Ty zarządzasz dziennikami na swoim koncie magazynu:

* Użyj standardowych metod kontroli dostępu platformy Azure w celu zabezpieczenia dzienników, wprowadzając ograniczenia co do tego, kto może uzyskiwać do nich dostęp.
* Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.
* Pamiętaj ustawić okres przechowywania, tak, aby stare dzienniki są usuwane z konta magazynu.

## <a name="view-logs-in-log-analytics-workspace"></a>Wyświetlanie dzienników w obszarze roboczym Log Analytics

Metryki i zdarzenia serwera są zintegrowane z xEvents w ramach zasobu obszaru roboczego Log Analytics w celu przeprowadzenia analizy równoległej. Obszar roboczy Log Analytics może być również skonfigurowany do odbierania zdarzeń z innych usług platformy Azure, co zapewnia całościowy wgląd w dane rejestrowania diagnostycznego w ramach architektury.

Aby wyświetlić dane diagnostyczne, w obszarze roboczym Log Analytics Otwórz pozycję **dzienniki** w menu po lewej stronie.

![Opcje wyszukiwania dzienników w witrynie Azure portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

W konstruktorze zapytań rozwiń węzeł **LogManagement** > **AzureDiagnostics**. AzureDiagnostics zawiera zdarzenia silnika i usług. Zwróć uwagę na to, że zapytanie jest tworzone na bieżąco. EventClass\_s pole zawiera nazwy systemu xEvent, które mogą wygląda znajomo, jeśli wcześniej używano systemu xEvents dla lokalnego rejestrowania. Kliknij pozycję **EventClass\_s** lub jeden z nazw zdarzeń, a obszar roboczy log Analytics kontynuuje konstruowanie zapytania. Pamiętaj zapisać zapytania do późniejszego.

### <a name="example-queries"></a>Przykładowe zapytania

#### <a name="example-1"></a>Przykład 1

Następujące zapytanie zwraca czasy trwania dla każdego zdarzenia zakończenia/odświeżenia zapytania dla bazy danych i serwera modelu. W przypadku skalowania wyniki są rozbite przez replikę, ponieważ numer repliki jest uwzględniony w ServerName_s. Grupowanie według RootActivityId_g zmniejsza liczbę wierszy pobieranych z interfejsu API REST Diagnostyka Azure i pomaga w granicach, zgodnie z opisem w [log Analytics limitów szybkości](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>Przykład 2

Następujące zapytanie zwraca pamięć i QPU użycie dla serwera. W przypadku skalowania wyniki są rozbite przez replikę, ponieważ numer repliki jest uwzględniony w ServerName_s.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>Przykład 3

Następujące zapytanie zwraca liczniki wydajności aparatu Analysis Services odczyt/s dla serwera.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Istnieją setki zapytań, których można użyć. Aby dowiedzieć się więcej o zapytaniach, zobacz Rozpoczynanie [pracy z dziennikami Azure monitor](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Włącz rejestrowanie przy użyciu programu PowerShell

W ramach tego szybkiego samouczka utworzysz konto magazynu w tej samej subskrypcji i grupie zasobów co serwer usług Analysis Service. Następnie należy użyć polecenia Set-AzDiagnosticSetting, aby włączyć rejestrowanie diagnostyczne, wysyłając dane wyjściowe do nowego konta magazynu.

### <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka, musi mieć następujące zasoby:

* Istniejący serwer usług Azure Analysis Services. Aby uzyskać instrukcje na temat tworzenia zasobu serwera, zobacz [Utwórz serwer w witrynie Azure portal](analysis-services-create-server.md), lub [Tworzenie serwera usługi Azure Analysis Services przy użyciu programu PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Nawiązać połączenie z subskrypcji

Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:  

```powershell
Connect-AzAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Program Azure PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem, i domyślnie użyje pierwszej.

Jeśli masz wiele subskrypcji, określ konkretne konto, które zostało użyte do utworzenia usługi Azure Key Vault. Wpisz następujące polecenie, aby zobaczyć subskrypcje dla swojego konta:

```powershell
Get-AzSubscription
```

Następnie aby określić subskrypcję, która jest skojarzona z konta usług Azure Analysis Services, który się loguje, wpisz:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Jeśli masz wiele subskrypcji skojarzonych z Twoim kontem, jest ważne określić subskrypcję.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Tworzenie nowego konta magazynu dla dzienników

Można użyć istniejącego konta magazynu dla dzienników, pod warunkiem znajduje się w tej samej subskrypcji co serwer. W tym samouczku utworzysz nowe konto magazynu przeznaczone do Analysis Services dzienników. Aby ułatwić, szczegółów konta magazynu są przechowywane w zmiennej o nazwie **sa**.

Możesz również użyć tej samej grupie zasobów, która zawiera serwer usług Analysis Services. Zastąp wartości `awsales_resgroup`, `awsaleslogs`, i `West Central US` własnymi wartościami:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Określ konto serwera dla dzienników

Ustaw nazwę konta do zmiennej o nazwie **konta**, gdzie ResourceName jest nazwa konta.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Włączanie rejestrowania

Aby włączyć rejestrowanie, należy użyć polecenia cmdlet Set-AzDiagnosticSetting wraz ze zmiennymi dla nowego konta magazynu, konta serwera i kategorii. Uruchom następujące polecenie, ustawienie **— włączone** flaga **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Dane wyjściowe powinny wyglądać następująco:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Dane wyjściowe potwierdzają, że rejestrowanie jest teraz włączone dla serwera, co pozwala na zapisywanie informacji na koncie magazynu.

Można również ustawić zasady przechowywania dla dzienników, aby starsze dzienniki są automatycznie usuwane. Na przykład Ustaw zasady przechowywania **- Retentionenable** flaga **$true**i ustaw **- RetentionInDays** parametr **90**. Dzienniki starsze niż 90 dni zostaną automatycznie usunięte.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [rejestrowania diagnostycznego usługi Azure resource](../azure-monitor/platform/platform-logs-overview.md).

Zobacz [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) w pomocy programu PowerShell.
