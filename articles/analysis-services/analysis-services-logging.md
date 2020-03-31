---
title: Rejestrowanie diagnostyczne dla usług Azure Analysis Services | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania rejestrowania diagnostycznego zasobów platformy Azure w celu monitorowania serwera usług Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0f13f297facedceb50920c0f6afca63fe1df0b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266184"
---
# <a name="setup-diagnostic-logging"></a>Konfigurowanie rejestrowania diagnostycznego

Ważną częścią dowolnego rozwiązania Analysis Services jest monitorowanie wydajności serwerów. Dzięki [dziennikom zasobów platformy Azure](../azure-monitor/platform/platform-logs-overview.md)można monitorować i wysyłać dzienniki do usługi Azure [Storage,](https://azure.microsoft.com/services/storage/)przesyłać strumieniowo je do [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i eksportować do [dzienników usługi Azure Monitor.](../azure-monitor/azure-monitor-log-hub.md)

![Rejestrowanie diagnostyczne w dziennikach magazynu, centrów zdarzeń lub usługi Azure Monitor](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Co zostało zarejestrowane?

Można wybrać kategorie **Aparat,** **Usługa**i **Metryki.**

### <a name="engine"></a>Aparat

Wybranie **engine** rejestruje wszystkie [xEvents](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Nie można wybrać poszczególnych zdarzeń. 

|Kategorie XEvent |Nazwa wydarzenia  |
|---------|---------|
|Inspekcja zabezpieczeń    |   Logowanie inspekcji      |
|Inspekcja zabezpieczeń    |   Wylogowanie inspekcji      |
|Inspekcja zabezpieczeń    |   Serwer inspekcji uruchamia się i zatrzymuje      |
|Raporty o postępach     |   Rozpoczęcie raportu o postępie      |
|Raporty o postępach     |   Koniec raportu o postępie      |
|Raporty o postępach     |   Bieżący raport z postępu prac      |
|Zapytania     |  Początek zapytania       |
|Zapytania     |   Koniec zapytania      |
|Polecenia     |  Polecenie Rozpocznij       |
|Polecenia     |  Koniec polecenia       |
|Ostrzeżenia o błędach &     |   Błąd      |
|Odnajdź     |   Odkryj koniec      |
|Powiadomienie     |    Powiadomienie     |
|Sesja     |  Inicjowanie sesji       |
|Blokady    |  Zakleszczenie       |
|Przetwarzanie zapytań     |   Rozpoczynanie kwerendy VertiPaq SE      |
|Przetwarzanie zapytań     |   Koniec kwerendy VertiPaq SE      |
|Przetwarzanie zapytań     |   VertiPaq SE Zapytanie pamięci podręcznej      |
|Przetwarzanie zapytań     |   Rozpoczęcie kwerendy bezpośredniej      |
|Przetwarzanie zapytań     |  Zakończenie kwerendy bezpośredniej       |

### <a name="service"></a>Usługa

|Nazwa operacji  |Występuje, gdy  |
|---------|---------|
|Serwer ResumeServer     |    Wznawianie serwera     |
|SuspendServer (Serwer wstrzymania)    |   Wstrzymywanie serwera      |
|Serwer DeleteServer     |    Usuwanie serwera     |
|Serwer RestartServer    |     Użytkownik ponownie uruchamia serwer za pośrednictwem usługi SSMS lub programu PowerShell    |
|Pliki GetServerLogFiles    |    Użytkownik eksportuje dziennik serwera za pośrednictwem programu PowerShell     |
|ExportModel (Eksportowy-     |   Użytkownik eksportuje model w portalu przy użyciu funkcji Otwórz w programie Visual Studio     |

### <a name="all-metrics"></a>Wszystkie metryki

Kategoria Metryki rejestruje te same [metryki serwera](analysis-services-monitor.md#server-metrics) do tabeli AzureMetrics. Jeśli używasz kwerendy [skalowane w poziomie](analysis-services-scale-out.md) i trzeba oddzielić metryki dla każdej repliki odczytu, użyj AzureDiagnostics tabeli zamiast, gdzie **OperationName** jest równa **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Konfigurowanie rejestrowania diagnostycznego

### <a name="azure-portal"></a>Portal Azure

1. W [witrynie Azure portal](https://portal.azure.com) > serwer kliknij pozycję **Dzienniki diagnostyczne** w lewej nawigacji, a następnie kliknij pozycję **Włącz diagnostykę**.

    ![Włączanie rejestrowania diagnostycznego dla usługi Azure Cosmos DB w portalu Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. W obszarze **Ustawienia diagnostyczne** określ następujące opcje: 

    * **Nazwa**. Wprowadź nazwę dzienników do utworzenia.

    * **Archiwum na koncie magazynu**. Aby użyć tej opcji, potrzebujesz istniejącego konta magazynu, z którego chcesz się połączyć. Zobacz [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md). Postępuj zgodnie z instrukcjami, aby utworzyć Menedżera zasobów, konto ogólnego przeznaczenia, a następnie wybierz konto magazynu, wracając do tej strony w portalu. Może upłynąć kilka minut, zanim konta nowo utworzonego magazynu pojawią się w menu rozwijanym.
    * **Przesyłanie strumieniowe do centrum zdarzeń**. Aby użyć tej opcji, potrzebujesz istniejącego obszaru nazw Centrum zdarzeń i centrum zdarzeń, aby się połączyć. Aby dowiedzieć się więcej, zobacz temat [Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń za pomocą witryny Azure Portal](../event-hubs/event-hubs-create.md). Następnie wróć do tej strony w portalu, aby wybrać obszar nazw Centrum zdarzeń i nazwę zasad.
    * **Wyślij do usługi Azure Monitor (obszar roboczy analizy dzienników).** Aby użyć tej opcji, użyj istniejącego obszaru roboczego lub [utwórz nowy zasób obszaru roboczego](../azure-monitor/learn/quick-create-workspace.md) w portalu. Aby uzyskać więcej informacji na temat wyświetlania dzienników, zobacz [Wyświetlanie dzienników w obszarze roboczym usługi Log Analytics](#view-logs-in-log-analytics-workspace) w tym artykule.

    * **Silnik**. Wybierz tę opcję, aby rejestrować xEvents. Jeśli archiwizujesz konto magazynu, możesz wybrać okres przechowywania dzienników diagnostycznych. Dzienniki są automatycznie usuwane po upływie okresu przechowywania.
    * **serwis .** Wybierz tę opcję, aby rejestrować zdarzenia na poziomie usługi. Jeśli dokonujesz archiwizacji na koncie magazynu, możesz wybrać okres przechowywania dzienników diagnostycznych. Dzienniki są automatycznie usuwane po upływie okresu przechowywania.
    * **Metryki**. Wybierz tę opcję, aby przechowywać pełne dane w [metrykach](analysis-services-monitor.md#server-metrics). Jeśli dokonujesz archiwizacji na koncie magazynu, możesz wybrać okres przechowywania dzienników diagnostycznych. Dzienniki są automatycznie usuwane po upływie okresu przechowywania.

3. Kliknij przycisk **Zapisz**.

    Jeśli zostanie wyświetlony błąd informujący "Nie można \<zaktualizować diagnostyki dla nazwy obszaru roboczego>. Identyfikator \<subskrypcji> nie jest zarejestrowany do korzystania z microsoft.insights." postępuj zgodnie z instrukcjami [diagnostyki platformy Azure,](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) aby zarejestrować konto, a następnie ponów próbę wykonania tej procedury.

    Jeśli chcesz zmienić sposób zapisywania dzienników diagnostycznych w dowolnym momencie w przyszłości, możesz powrócić do tej strony, aby zmodyfikować ustawienia.

### <a name="powershell"></a>PowerShell

Oto podstawowe polecenia, aby zacząć. Jeśli chcesz uzyskać pomoc krok po kroku podczas konfigurowania rejestrowania konta magazynu przy użyciu programu PowerShell, zobacz samouczek w dalszej części tego artykułu.

Aby włączyć metryki i rejestrowanie diagnostyki przy użyciu programu PowerShell, użyj następujących poleceń:

- Aby włączyć magazyn dzienników diagnostycznych na koncie magazynu, użyj tego polecenia:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Identyfikator konta magazynu jest identyfikatorem zasobu konta magazynu, do którego chcesz wysłać dzienniki.

- Aby włączyć strumieniowe przesyłanie dzienników diagnostycznych do centrum zdarzeń, użyj tego polecenia:

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

Można połączyć te parametry, aby uaktywnić wiele opcji danych wyjściowych.

### <a name="rest-api"></a>Interfejs API REST

Dowiedz się, jak [zmienić ustawienia diagnostyczne przy użyciu interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Dowiedz się, jak [włączyć ustawienia diagnostyczne podczas tworzenia zasobu przy użyciu szablonu usługi Menedżer zasobów](../azure-monitor/platform/diagnostic-settings-template.md). 

## <a name="manage-your-logs"></a>Zarządzanie dziennikami

Dzienniki są zazwyczaj dostępne w ciągu kilku godzin od skonfigurowania rejestrowania. To Ty zarządzasz dziennikami na swoim koncie magazynu:

* Użyj standardowych metod kontroli dostępu platformy Azure w celu zabezpieczenia dzienników, wprowadzając ograniczenia co do tego, kto może uzyskiwać do nich dostęp.
* Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.
* Pamiętaj, aby ustawić okres przechowywania dla tak stare dzienniki są usuwane z konta magazynu.

## <a name="view-logs-in-log-analytics-workspace"></a>Wyświetlanie dzienników w obszarze roboczym usługi Log Analytics

Metryki i zdarzenia serwera są zintegrowane z xEvents w zasobie obszaru roboczego usługi Log Analytics do analizy side-by-side. Obszar roboczy usługi Log Analytics można również skonfigurować do odbierania zdarzeń z innych usług platformy Azure, zapewniając całościowy widok danych rejestrowania diagnostycznego w całej architekturze.

Aby wyświetlić dane diagnostyczne, w obszarze roboczym usługi Log Analytics otwórz **dzienniki** z lewego menu.

![Opcje wyszukiwania dziennika w witrynie Azure portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

W konstruktorze zapytań rozwiń węzeł **LogManagement** > **AzureDiagnostics**. AzureDiagnostics zawiera zdarzenia aparatu i usługi. Zwróć uwagę, że kwerenda jest tworzona w locie. Pole EventClass\_s zawiera nazwy xEvent, które mogą wyglądać znajomo, jeśli używano xEvents do rejestrowania lokalnego. Kliknij **pozycję\_EventClass s** lub jedną z nazw zdarzeń, a obszar roboczy usługi Log Analytics kontynuuje konstruowanie kwerendy. Pamiętaj, aby zapisywać zapytania, dzięki czemu będzie można skorzystać z nich później.

### <a name="example-queries"></a>Przykładowe zapytania

#### <a name="example-1"></a>Przykład 1

Następująca kwerenda zwraca czasy trwania dla każdego zdarzenia końcowego/odświeżania kwerendy dla bazy danych modelu i serwera. Jeśli skalowane w poziomie, wyniki są podzielone według repliki, ponieważ numer repliki jest uwzględniony w ServerName_s. Grupowanie według RootActivityId_g zmniejsza liczbę wierszy pobranych z interfejsu API REST diagnostyki platformy Azure i pomaga pozostawać w granicach opisanych w [limitach szybkości analizy dzienników.](https://dev.loganalytics.io/documentation/Using-the-API/Limits)

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

Następująca kwerenda zwraca zużycie pamięci i QPU dla serwera. Jeśli skalowane w poziomie, wyniki są podzielone według repliki, ponieważ numer repliki jest uwzględniony w ServerName_s.

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

Następująca kwerenda zwraca liczniki wydajności aparatu Analysis Services odczytu/s wierszy dla serwera.

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

Istnieją setki zapytań, z których można skorzystać. Aby dowiedzieć się więcej o kwerendach, zobacz [Wprowadzenie do zapytań dziennika usługi Azure Monitor](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Włączanie rejestrowania przy użyciu programu PowerShell

W tym krótkim samouczku utworzysz konto magazynu w tej samej grupie subskrypcji i zasobów, co serwer usługi Analysis Service. Następnie należy użyć Set-AzDiagnosticSetting, aby włączyć rejestrowanie diagnostyki, wysyłanie danych wyjściowych do nowego konta magazynu.

### <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten samouczek, musisz mieć następujące zasoby:

* Istniejący serwer usług Analizy Platformy Azure. Aby uzyskać instrukcje dotyczące tworzenia zasobu serwera, zobacz [Tworzenie serwera w witrynie Azure Portal](analysis-services-create-server.md)lub Tworzenie serwera usług Azure Analysis Services przy użyciu programu [PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Nawiązywanie połączenia z subskrypcjami

Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:  

```powershell
Connect-AzAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Program Azure PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem, i domyślnie użyje pierwszej.

Jeśli masz wiele subskrypcji, określ konkretne konto, które zostało użyte do utworzenia usługi Azure Key Vault. Wpisz następujące polecenie, aby zobaczyć subskrypcje dla swojego konta:

```powershell
Get-AzSubscription
```

Następnie, aby określić subskrypcję skojarzoną z kontem usługi Azure Analysis Services, które rejestrujesz, wpisz:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Jeśli masz wiele subskrypcji skojarzonych z kontem, ważne jest, aby określić subskrypcję.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Tworzenie nowego konta magazynu dla dzienników

Można użyć istniejącego konta magazynu dla dzienników, pod warunkiem, że jest w tej samej subskrypcji co serwer. W tym samouczku utworzysz nowe konto magazynu dedykowane dziennikom usług Analysis Services. Aby to ułatwić, przechowujesz szczegóły konta magazynu w zmiennej o nazwie **sa**.

Należy również użyć tej samej grupy zasobów, co ta, która zawiera serwer usług Analysis Services. Zastąp `awsaleslogs`wartości `West Central US` `awsales_resgroup`, i własnymi wartościami:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identyfikowanie konta serwera dla dzienników

Ustaw nazwę konta na zmienną o nazwie **konto**, gdzie Nazwa zasobów jest nazwą konta.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Włącz rejestrowanie

Aby włączyć rejestrowanie, użyj polecenia cmdlet Set-AzDiagnosticSetting wraz ze zmiennymi dla nowego konta magazynu, konta serwera i kategorii. Uruchom następujące polecenie, ustawiając flagę **-Enabled** **na $true:**

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

To dane wyjściowe potwierdza, że rejestrowanie jest teraz włączone dla serwera, zapisując informacje na koncie magazynu.

Można również ustawić zasady przechowywania dzienników, aby starsze dzienniki były automatycznie usuwane. Na przykład ustaw zasady przechowywania przy użyciu **flagi -RetentionEnabled,** aby **$true**, i ustaw parametr **-RetentionInDays** na **90**. Dzienniki starsze niż 90 dni są automatycznie usuwane.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [rejestrowaniu diagnostycznym zasobów platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

Zobacz [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) w pomocy programu PowerShell.
