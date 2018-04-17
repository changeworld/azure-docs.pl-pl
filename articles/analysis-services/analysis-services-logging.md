---
title: Rejestrowanie Diganostic dla usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Więcej informacji na temat konfigurowania rejestrowania diagnostycznego dla usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 12f745958130e931bc3c8e81a0a61f3c3f4c4e3c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="setup-diagnostic-logging"></a>Ustawienia rejestrowania diagnostycznego

Ważnym elementem od wszelkich rozwiązań do usług Analysis Services jest monitorowanie, jak działają serwery. Z [dzienników diagnostycznych zasobów platformy Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), można monitorować i wysłać dzienniki, aby [usługi Azure Storage](https://azure.microsoft.com/services/storage/), ich do strumienia [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)oraz eksportowanie ich do [dziennika Analiza](https://azure.microsoft.com/services/log-analytics/), usługi z [Azure](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Rejestrowania diagnostycznego do magazynu usługi Event Hubs i analizy dzienników](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>Co to jest rejestrowane?

Możesz wybrać **aparat**, **usługi**, i **metryki** kategorii.

### <a name="engine"></a>Aparat

Wybieranie **aparat** wszystkie dzienniki [systemu xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Nie można wybrać poszczególnych zdarzeń. 

|Kategorie systemu XEvent |Nazwa zdarzenia  |
|---------|---------|
|Inspekcja zabezpieczeń    |   Inspekcji logowania      |
|Inspekcja zabezpieczeń    |   Wyloguj inspekcji      |
|Inspekcja zabezpieczeń    |   Audit Server uruchamia i zatrzymuje      |
|Raporty z postępów     |   Rozpocznij raportu postępu      |
|Raporty z postępów     |   Postęp zakończenia raportu      |
|Raporty z postępów     |   Bieżący raport postępu      |
|Zapytania     |  Rozpocznij zapytania       |
|Zapytania     |   Końcowy zapytania      |
|Polecenia     |  Polecenie Begin       |
|Polecenia     |  Polecenie zakończenia       |
|Błędy i ostrzeżenia     |   Błąd      |
|Wykrywanie     |   Odnajdywanie zakończenia      |
|Powiadomienia     |    Powiadomienia     |
|Sesja     |  Inicjowanie sesji       |
|Blokady    |  Zakleszczenie       |
|Przetwarzanie zapytań     |   VertiPaq SE Query Begin      |
|Przetwarzanie zapytań     |   VertiPaq SE Query End      |
|Przetwarzanie zapytań     |   VertiPaq SE Query Cache Match      |
|Przetwarzanie zapytań     |   Rozpocznij zapytania bezpośredniego      |
|Przetwarzanie zapytań     |  Końcowy zapytania bezpośredniego       |

### <a name="service"></a>Usługa

|Nazwa operacji  |Występuje, gdy  |
|---------|---------|
|CreateGateway     |   Użytkownik konfiguruje bramy na serwerze      |
|ResumeServer     |    Wznów serwera     |
|SuspendServer    |   Wstrzymywanie serwera      |
|DeleteServer     |    Usuwanie serwera     |
|RestartServer    |     Użytkownik uruchamia ponownie z serwerem za pomocą narzędzia SSMS lub programu PowerShell    |
|GetServerLogFiles    |    Użytkownik eksportuje dziennik serwera za pomocą programu PowerShell     |
|ExportModel     |   Użytkownik eksportuje modelu w portalu za pomocą polecenia Otwórz w programie Visual Studio     |

### <a name="all-metrics"></a>Wszystkie metryki

Kategoria metryki rejestruje takie same [metryki serwera](analysis-services-monitor.md#server-metrics) wyświetlane w metryki.

## <a name="setup-diagnostics-logging"></a>Konfiguracja rejestrowania diagnostyki

### <a name="azure-portal"></a>Azure Portal

1. W [portalu Azure](https://portal.azure.com) > serwera, kliknij przycisk **dzienniki diagnostyczne** nawigacji po lewej stronie, a następnie kliknij polecenie **Włącz diagnostykę**.

    ![Włączanie rejestrowania diagnostyki dla bazy danych Azure rozwiązania Cosmos w portalu Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. W **ustawień diagnostycznych**, określ następujące opcje: 

    * **Nazwa**. Wprowadź nazwę dla dzienników do utworzenia.

    * **Archiwum na konto magazynu**. Aby użyć tej opcji, należy istniejące konto magazynu, aby nawiązać połączenie. Zobacz [Utwórz konto magazynu](../storage/common/storage-create-storage-account.md). Postępuj zgodnie z instrukcjami, aby utworzyć konto ogólnego przeznaczenia, Menedżer zasobów, a następnie wybierz konto magazynu, wracając do tej strony w portalu. Może upłynąć kilka minut dla kont magazynu nowo utworzony pojawią się w menu rozwijanym.
    * **Strumień do Centrum zdarzeń**. Aby użyć tej opcji, należy istniejących Centrum zdarzeń przestrzeni nazw i zdarzenia koncentratora do nawiązania połączenia. Aby dowiedzieć się więcej, zobacz [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą portalu Azure](../event-hubs/event-hubs-create.md). Następnie wróć do tej strony w portalu, aby wybrać nazwę przestrzeni nazw i zasad Centrum zdarzeń.
    * **Wyślij do analizy dzienników**. Aby użyć tej opcji, użyj istniejący obszar roboczy lub utworzyć nowy obszar roboczy analizy dzienników, wykonując następujące kroki, aby [Utwórz nowy obszar roboczy](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) w portalu. Aby uzyskać więcej informacji o wyświetlaniu dzienników w analizy dzienników, zobacz [Wyświetl dzienniki w analizy dzienników](#view-in-loganalytics).

    * **Engine**. Wybierz tę opcję, aby zalogować się systemów Xevent. Jeśli w przypadku archiwizacji konta magazynu można wybrać okres przechowywania dzienników diagnostycznych. Dzienniki są autodeleted, po wygaśnięciu okresu przechowywania.
    * **Usługa**. Wybierz tę opcję, aby rejestrować zdarzenia na poziomie usługi. Jeśli archiwizacji do konta magazynu można wybrać okres przechowywania dzienników diagnostycznych. Dzienniki są autodeleted, po wygaśnięciu okresu przechowywania.
    * **Metryki**. Wybierz tę opcję, aby przechowywać pełne dane w [metryki](analysis-services-monitor.md#server-metrics). Jeśli archiwizacji do konta magazynu można wybrać okres przechowywania dzienników diagnostycznych. Dzienniki są autodeleted, po wygaśnięciu okresu przechowywania.

3. Kliknij pozycję **Zapisz**.

    Jeśli zostanie wyświetlony komunikat o błędzie informujący "nie można zaktualizować diagnostyki dla \<nazwa obszaru roboczego >. Subskrypcja \<identyfikator subskrypcji > nie jest zarejestrowany do użycia w elemencie microsoft.insights. " Postępuj zgodnie z [Rozwiązywanie problemów z diagnostyki Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) instrukcjami, aby zarejestrować konto, następnie ponów próbę wykonania tej procedury.

    Jeśli chcesz zmienić sposób dzienników diagnostycznych są zapisywane w dowolnym momencie w przyszłości, można powrócić do tej strony, aby zmodyfikować ustawienia.

### <a name="powershell"></a>PowerShell

Poniżej przedstawiono podstawowe polecenia umożliwiające przechodząc. Jeśli chcesz pomoc krok po kroku dotyczące konfigurowania rejestrowania na konto magazynu przy użyciu programu PowerShell, zobacz samouczek w dalszej części tego artykułu.

Aby włączyć metryki i diagnostyki rejestrowanie przy użyciu programu PowerShell, użyj następujących poleceń:

- Aby włączyć magazyn dzienników diagnostycznych na konto magazynu, należy użyć tego polecenia:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Identyfikator konta magazynu jest identyfikator zasobu dla konta magazynu, której chcesz wysłać dzienniki.

- Aby włączyć przesyłania strumieniowego dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Identyfikator reguły Azure Service Bus to ciąg w formacie:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego analizy dzienników, użyj tego polecenia:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Identyfikator zasobu obszaru roboczego analizy dzienników można uzyskać za pomocą następującego polecenia:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Można połączyć tych parametrów, aby włączyć wiele opcji danych wyjściowych.

### <a name="rest-api"></a>Interfejs API REST

Dowiedz się, jak [zmiany ustawień diagnostyki za pomocą interfejsu API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Dowiedz się, jak [. Włącz ustawienia diagnostyki na tworzenie zasobów przy użyciu szablonu usługi Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="manage-your-logs"></a>Zarządzasz dziennikami

Dzienniki są zwykle dostępne w ramach konfigurowania rejestrowania kilka godzin. To Ty zarządzasz dziennikami na swoim koncie magazynu:

* Użyj standardowych metod kontroli dostępu platformy Azure w celu zabezpieczenia dzienników, wprowadzając ograniczenia co do tego, kto może uzyskiwać do nich dostęp.
* Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.
* Pamiętaj ustawić okres przechowywania, tak aby stare dzienniki są usuwane z konta magazynu.

## <a name="view-logs-in-log-analytics"></a>Wyświetl dzienniki w analizy dzienników

Zdarzenia metryki i serwera są zintegrowane z systemu xEvents w analizy dzienników dla side-by-side analizy. Analiza dzienników można również skonfigurować do odbierania zdarzeń z innymi usługami Azure, zapewniając holistyczny widok danych diagnostycznych rejestrowania między architektury.

Aby wyświetlić dane diagnostyczne analizy dzienników, otwórz stronę wyszukiwania dziennika z menu po lewej stronie lub z obszaru zarządzania, jak pokazano poniżej.

![Opcje wyszukiwania dziennika w portalu Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

Teraz, gdy aktywowano zbierania danych w **wyszukiwania dziennika**, kliknij przycisk **wszystkie zebrane dane**.

W **typu**, kliknij przycisk **AzureDiagnostics**, a następnie kliknij przycisk **Zastosuj**. AzureDiagnostics zawiera aparat i usługi zdarzenia. Należy zauważyć, że zapytania analizy dziennika jest tworzony na bieżąco. EventClass\_s pole zawiera nazwy systemu xEvent, które może wyglądać znajomo, jeśli systemu xEvents była używana dla logowania lokalnego.

Kliknij przycisk **EventClass\_s** lub jedną z nazw zdarzeń i analizy dzienników nadal konstruowanie zapytania. Pamiętaj zapisać zapytania do późniejszego.

Pamiętaj zobaczyć analizy dzienników, która zapewnia witryny sieci Web z rozszerzoną zapytania, dashboarding i możliwości alertów w zebranych danych.

### <a name="queries"></a>Zapytania

Brak setki zapytania, których można użyć. Poniżej przedstawiono kilka ułatwiających rozpoczęcie pracy.
Aby dowiedzieć się więcej o korzystaniu z nowego języka zapytań wyszukiwania dziennika, zobacz [opis dziennika przeszukuje analizy dzienników](../log-analytics/log-analytics-log-search-new.md). 

* Zapytanie zwraca przesłać zapytań do usług Azure Analysis Services zajęło ponad pięć minut (300 000 w milisekundach), aby zakończyć.

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Zidentyfikuj skalowania w poziomie repliki.

    ```
    search * | summarize count() by ServerName_s
    ```
    Korzystając z skalowalnego w poziomie, można zidentyfikować repliki tylko do odczytu, ponieważ nazwa serwera\_wartości pól s ma numer repliki dołączonym do nazwy. Pole zasobu zawiera nazwę zasobów platformy Azure, która jest zgodna z nazwą serwera, które użytkownicy widzą. Pola IsQueryScaleoutReadonlyInstance_s jest równa true dla replik.



> [!TIP]
> Ma dużą zapytania analizy dzienników, które chcesz udostępnić? Jeśli masz konto GitHub, można dodać go do tego artykułu. Po prostu kliknij **Edytuj** w prawym górnym rogu strony.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Samouczek - Włącz rejestrowanie przy użyciu programu PowerShell
W tym samouczku szybki co serwer usług Analysis tworzenia konta magazynu w tej samej subskrypcji i grupy zasobów. Następnie należy Set-AzureRmDiagnosticSetting Aby włączyć diagnostykę rejestrowania, wysyłanie danych wyjściowych do nowego konta magazynu.

### <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka, musi mieć następujące zasoby:

* Istniejący serwer usług Azure Analysis Services. Aby uzyskać instrukcje tworzenia zasobów serwerów, zobacz [utworzyć serwer w portalu Azure](analysis-services-create-server.md), lub [Tworzenie serwera usług Analysis Services dla platformy Azure przy użyciu programu PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Nawiązać połączenia z subskrypcjami

Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:  

```powershell
Login-AzureRmAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Program Azure PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem, i domyślnie użyje pierwszej.

Jeśli masz wiele subskrypcji, określ konkretne konto, które zostało użyte do utworzenia usługi Azure Key Vault. Wpisz następujące polecenie, aby zobaczyć subskrypcje dla swojego konta:

```powershell
Get-AzureRmSubscription
```

Następnie aby określić subskrypcję skojarzoną z konta usług Azure Analysis Services, które są rejestrowania, wpisz:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Jeśli masz wiele subskrypcji skojarzonych z Twoim kontem, jest ważne określić subskrypcję.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Tworzenie nowego konta magazynu dla dzienników

Można użyć istniejącego konta magazynu dla dzienników, pod warunkiem że jest w tej samej subskrypcji co serwer. W tym samouczku utworzysz nowe konto magazynu dedykowane do dzienników usług Analysis Services. Aby ułatwić, są przechowywane szczegóły konta magazynu w zmiennej o nazwie **sa**.

Możesz również użyć tej samej grupie zasobów, która zawiera serwer usług Analysis Services. Zastąp wartości `awsales_resgroup`, `awsaleslogs`, i `West Central US` z własne wartości:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Zidentyfikowanie konta serwera dla dzienników

Ustaw nazwę konta do zmiennej o nazwie **konta**, gdzie ResourceName jest nazwa konta.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Włącz rejestrowanie

Aby włączyć rejestrowanie, należy użyć polecenia cmdlet Set-AzureRmDiagnosticSetting wraz ze zmiennymi dla nowego konta magazynu, konto serwera i kategorii. Uruchom następujące polecenie, ustawienie **-włączone** flaga **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
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

Potwierdza to, że włączono rejestrowanie dla serwera, informacje są zapisywane na koncie magazynu.

Można również ustawić zasad przechowywania dla dzienników, więc starsze dzienniki są automatycznie usuwane. Na przykład ustawić zasady przechowywania, używając **- RetentionEnabled** flaga **$true**i ustaw **- RetentionInDays** parametr **90**. Dzienniki starsze niż 90 dni są automatycznie usuwane.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [rejestrowania diagnostycznego zasobów platformy Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

Zobacz [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) w Pomocy programu PowerShell.