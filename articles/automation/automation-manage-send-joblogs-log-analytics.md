---
title: Przekazywanie danych zadania usługi Azure Automation do dzienników usługi Azure Monitor
description: W tym artykule pokazano, jak wysyłać strumienie zadań i stanów zadań elementu Runbook do Azure Monitor dzienników w celu zapewnienia dodatkowego wglądu i zarządzania.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ff455ed355d4412bcf042208d2fd1e7a2a11b965
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186785"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Przekazywanie strumieni zadań i stanu zadań z automatyzacji do dzienników Azure Monitor

Automatyzacja może wysyłać strumienie zadań elementu Runbook i zadań do obszaru roboczego Log Analytics. Ten proces nie obejmuje łączenia obszarów roboczych i jest całkowicie niezależny. Dzienniki zadań i strumienie zadań są widoczne w Azure Portal lub w programie PowerShell dla poszczególnych zadań. pozwala to na wykonywanie prostych badań. Teraz z dziennikami Azure Monitor można:

* Uzyskiwanie szczegółowych informacji na temat zadań usługi Automation.
* Wyzwalanie wiadomości e-mail lub alertu na podstawie stanu zadania elementu Runbook (na przykład Niepowodzenie lub wstrzymane).
* Pisanie zaawansowanych zapytań obejmujących wiele strumieni zadań.
* Korelowanie zadań z wielu kont usługi Automation.
* Wizualizowanie historii zadań w czasie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Wymagania wstępne i zagadnienia dotyczące wdrażania

Aby rozpocząć wysyłanie dzienników usługi Automation do dzienników Azure Monitor, potrzebne są:

* Najnowsza wersja [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Obszar roboczy usługi Log Analytics. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z dziennikami Azure monitor](../log-analytics/log-analytics-get-started.md).
* Identyfikator zasobu dla konta Azure Automation.

Aby znaleźć identyfikator zasobu dla konta Azure Automation:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Aby znaleźć identyfikator zasobu dla obszaru roboczego Log Analytics, uruchom następujące polecenie programu PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Jeśli masz więcej niż jedno konto usługi Automation lub obszary robocze, w danych wyjściowych powyższych poleceń Znajdź *nazwę* potrzebną do skonfigurowania i skopiowania wartości elementu *ResourceID*.

Jeśli chcesz znaleźć *nazwę* konta usługi Automation, w obszarze Azure Portal wybierz konto usługi Automation z bloku **konto usługi Automation** , a następnie wybierz pozycję **wszystkie ustawienia**. W bloku **Wszystkie ustawienia** w obszarze **Ustawienia konta** wybierz pozycję **Właściwości**.  W bloku **Właściwości** możesz zauważyć poniższe wartości.<br> ![Właściwości](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)konta usługi Automation.

## <a name="set-up-integration-with-azure-monitor-logs"></a>Konfigurowanie integracji z dziennikami Azure Monitor

1. Na komputerze Uruchom program **Windows PowerShell** z ekranu **startowego** .
2. Uruchom następujące polecenie programu PowerShell i przeprowadź edycję wartości `[your resource id]` dla `[resource id of the log analytics workspace]` i za pomocą wartości z poprzedniego kroku.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Po uruchomieniu tego skryptu może upłynąć godzinę przed rozpoczęciem wyświetlania rekordów w Azure Monitor dzienników nowych JobLogs lub JobStreams.

Aby wyświetlić dzienniki, uruchom następujące zapytanie w przeszukiwaniu dzienników usługi log Analytics:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Zweryfikuj konfigurację

Aby upewnić się, że konto usługi Automation wysyła dzienniki do obszaru roboczego Log Analytics, sprawdź, czy na koncie usługi Automation prawidłowo skonfigurowano diagnostykę, używając następującego programu PowerShell:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

W danych wyjściowych upewnij się, że:

* W obszarze *dzienniki*wartość opcji *włączone* jest *równa true*.
* Wartość *Identyfikator obszaru roboczego* jest ustawiana na identyfikator ResourceID obszaru roboczego log Analytics.

## <a name="azure-monitor-log-records"></a>Azure Monitor rekordy dziennika

Diagnostyka z Azure Automation tworzy dwa typy rekordów w dziennikach Azure Monitor i są oznaczone jako **AzureDiagnostics**. Poniższe zapytania używają uaktualnionego języka zapytań do Azure Monitor dzienników. Aby uzyskać informacje o typowych zapytaniach między starszym językiem zapytań a nowym językiem zapytań usługi Azure Kusto, odwiedź stronę [starszej wersji z nowym arkuszem języka zapytań usługi Azure Kusto](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Dzienniki zadań

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina dla wykonania zadania elementu Runbook. |
| RunbookName_s |Nazwa elementu Runbook. |
| Caller_s |Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań. |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawcę dla obiektu wywołującego. |
| JobId_g |Identyfikator GUID, który jest identyfikatorem zadania elementu Runbook. |
| Result |Stan zadania elementu Runbook. Możliwe wartości to:<br>-Nowe<br>-Utworzono<br>— Uruchomione<br>— Zatrzymane<br>— Wstrzymane<br>— Nie powiodło się<br>— Ukończono |
| Category | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobLogs. |
| OperationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest zadanie. |
| Resource | Nazwa konta usługi Automation |
| SourceSystem | Jak dzienniki Azure Monitor zbierane dane. Zawsze *platforma Azure* dla diagnostyki Azure. |
| ResultDescription |Opisuje stan wyniku zadania elementu Runbook. Możliwe wartości to:<br>— Zadanie jest uruchomione<br>— Zadanie nie powiodło się<br>— Zadanie zostało ukończone |
| Identyfikator korelacji |Identyfikator GUID, który jest identyfikatorem korelacji zadania elementu Runbook. |
| ResourceId |Określa identyfikator zasobu konta Azure Automation elementu Runbook. |
| SubscriptionId | Identyfikator subskrypcji platformy Azure (GUID) dla konta usługi Automation. |
| Grupa zasobów | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | PROGRAMU. AUTOMATYZACJI |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Strumienie zadań
| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina dla wykonania zadania elementu Runbook. |
| RunbookName_s |Nazwa elementu Runbook. |
| Caller_s |Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań. |
| StreamType_s |Typ strumienia zadania. Możliwe wartości to:<br>— Postęp<br>— Dane wyjściowe<br>— Ostrzeżenie<br>— Błąd<br>— Debugowanie<br>— Pełne |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawcę dla obiektu wywołującego. |
| JobId_g |Identyfikator GUID, który jest identyfikatorem zadania elementu Runbook. |
| Result |Stan zadania elementu Runbook. Możliwe wartości to:<br>— W toku |
| Category | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobStreams. |
| OperationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest zadanie. |
| Resource | Nazwa konta usługi Automation |
| SourceSystem | Jak dzienniki Azure Monitor zbierane dane. Zawsze *platforma Azure* dla diagnostyki Azure. |
| ResultDescription |Obejmuje strumień wyjściowy z elementu Runbook. |
| Identyfikator korelacji |Identyfikator GUID, który jest identyfikatorem korelacji zadania elementu Runbook. |
| ResourceId |Określa identyfikator zasobu konta Azure Automation elementu Runbook. |
| SubscriptionId | Identyfikator subskrypcji platformy Azure (GUID) dla konta usługi Automation. |
| Grupa zasobów | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | PROGRAMU. AUTOMATYZACJI |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Wyświetlanie dzienników automatyzacji w dziennikach Azure Monitor

Teraz, po rozpoczęciu wysyłania dzienników zadań usługi Automation do dzienników Azure Monitor, zobaczmy, co możesz zrobić z tymi dziennikami w Azure Monitor dzienników.

Aby wyświetlić dzienniki, uruchom następujące zapytanie:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Wyślij wiadomość e-mail, gdy zadanie elementu Runbook ulegnie awarii lub zostanie zawieszone
Jednym z najważniejszych monitów klienta jest możliwość wysyłania wiadomości e-mail lub tekstu, gdy coś się nie stało z zadaniem elementu Runbook.

Aby utworzyć regułę alertu, należy zacząć od utworzenia przeszukiwania dzienników dla rekordów zadań elementu Runbook, które powinny wywoływać alert. Kliknij przycisk **alert** , aby utworzyć i skonfigurować regułę alertu.

1. Na stronie Przegląd obszaru roboczego Log Analytics kliknij pozycję **Wyświetl dzienniki**.
2. Utwórz zapytanie przeszukiwania dzienników dla alertu, wpisując następujące polecenie wyszukiwania w polu zapytania: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`Można również grupować według elementu Runbookname przy użyciu:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Jeśli skonfigurujesz dzienniki z więcej niż jednego konta usługi Automation lub subskrypcji w obszarze roboczym, możesz grupować alerty według subskrypcji i konta usługi Automation. Nazwę konta usługi Automation można znaleźć w polu zasobu w wyszukiwaniu JobLogs.
3. Aby otworzyć ekran **Utwórz regułę** , kliknij pozycję **+ Nowa reguła alertów** u góry strony. Aby uzyskać więcej informacji na temat opcji konfigurowania alertu, zobacz [alerty dzienników na platformie Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Znajdź wszystkie zadania, które zostały zakończone z błędami
Oprócz alertów dotyczących błędów można znaleźć, kiedy zadanie elementu Runbook ma błąd niepowodujący zakończenia. W takich przypadkach program PowerShell tworzy strumień błędów, ale błędy niepowodujące zakończenia nie powodują zawieszenia zadania lub jego niepowodzenie.

1. W obszarze roboczym Log Analytics kliknij pozycję **dzienniki**.
2. W polu zapytania wpisz `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` , a następnie kliknij przycisk **wyszukiwania** .

### <a name="view-job-streams-for-a-job"></a>Wyświetlanie strumieni zadań dla zadania
Podczas debugowania zadania warto również zajrzeć do strumienia zadań. Następujące zapytanie wyświetla wszystkie strumienie dla pojedynczego zadania o identyfikatorze GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Wyświetl stan zadania historycznego
Na koniec możesz chcieć wizualizować historię zadań z upływem czasu. To zapytanie służy do wyszukiwania stanu zadań w czasie.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Wykres stanu zadania Log Analytics historycznego](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Usuń ustawienia diagnostyczne

Aby usunąć ustawienie diagnostyczne z konta usługi Automation, uruchom następujące polecenia:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Podsumowanie

Wysyłając dane o stanie zadania usługi Automation i przesyłanych strumieniowo do dzienników Azure Monitor, można uzyskać lepszy wgląd w informacje o stanie zadań automatyzacji, wykonując następujące zadania:
+ Konfigurowanie alertów powiadamiających o wystąpieniu problemu.
+ Używanie widoków niestandardowych i zapytań wyszukiwania w celu wizualizacji wyników elementu Runbook, stanu zadania elementu Runbook oraz innych powiązanych wskaźników lub metryk kluczy.

Dzienniki Azure Monitor zapewniają większą widoczność operacyjną zadań automatyzacji i ułatwiają szybkie rozwiązywanie problemów.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pomoc w rozwiązywaniu problemów Log Analytics, zobacz [Rozwiązywanie problemów, dlaczego log Analytics nie zbiera już danych](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Aby dowiedzieć się więcej na temat tworzenia różnych zapytań wyszukiwania i przeglądania dzienników zadań usługi Automation z dziennikami Azure Monitor, zobacz [Wyszukiwanie w dzienniku w](../log-analytics/log-analytics-log-searches.md)dziennikach Azure monitor.
* Aby dowiedzieć się, jak tworzyć i pobierać dane wyjściowe i komunikaty o błędach z elementów Runbook, zobacz [Runbook Output and messages](automation-runbook-output-and-messages.md).
* Aby dowiedzieć się więcej o wykonywaniu elementów runbook, sposobie monitorowania zadań elementów runbook i innych szczegółach technicznych, zobacz [Track a runbook job](automation-runbook-execution.md) (Śledzenie zadania elementu runbook).
* Aby dowiedzieć się więcej na temat dzienników Azure Monitor i źródeł zbierania danych, zobacz [zbieranie danych usługi Azure Storage w dziennikach Azure monitor Omówienie](../azure-monitor/platform/collect-azure-metrics-logs.md).

