---
title: Przekazywanie danych zadania usługi Azure Automation do dzienników usługi Azure Monitor
description: W tym artykule pokazano, jak wysłać stan zadania i strumieni zadań runbook do dzienników usługi Azure Monitor, aby zapewnić dodatkowe informacje i zarządzanie.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: beb69edc57b5a13db0f6d2e5e1536804f3472aff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75421915"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Przesyłanie dalej stanu zadania i strumieni zadań z automatyzacji do dzienników usługi Azure Monitor

Automatyzacja może wysyłać stan zadania i strumienie zadań do obszaru roboczego usługi Log Analytics. Ten proces nie obejmuje łączenia obszaru roboczego i jest całkowicie niezależny. Dzienniki zadań i strumienie zadań są widoczne w witrynie Azure portal lub z programem PowerShell dla poszczególnych zadań, co umożliwia wykonywanie prostych dochodzeń. Teraz za pomocą dzienników usługi Azure Monitor możesz:

* Uzyskaj wgląd w swoje zadania automatyzacji.
* Wyzwalanie wiadomości e-mail lub alertu na podstawie stanu zadania egoisty (na przykład nie powiodło się lub zawieszono).
* Pisanie zaawansowanych zapytań w strumieniach zadań.
* Skorelowanie zadań między kontami automatyzacji.
* Wizualizuj historię pracy w czasie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Wymagania wstępne i zagadnienia dotyczące wdrażania

Aby rozpocząć wysyłanie dzienników automatyzacji do dzienników usługi Azure Monitor, potrzebujesz:

* Najnowsza wersja [programu Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Obszar roboczy usługi Log Analytics. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do dzienników usługi Azure Monitor](../log-analytics/log-analytics-get-started.md).
* Identyfikator zasobu dla konta usługi Azure Automation.

Aby znaleźć identyfikator zasobu dla konta usługi Azure Automation:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Aby znaleźć identyfikator zasobu dla obszaru roboczego usługi Log Analytics, uruchom następujący program PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Jeśli w danych wyjściowych poleceń znajduje się więcej niż jedno konta automatyzacji lub obszary robocze, znajdź *nazwę,* którą musisz skonfigurować i skopiować wartość *identyfikatora ResourceId*.

Jeśli chcesz znaleźć *nazwę* konta automatyzacji, w witrynie Azure portal wybierz swoje konto automatyzacji z bloku **konta automatyzacji** i wybierz **wszystkie ustawienia**. W bloku **Wszystkie ustawienia** w obszarze **Ustawienia konta** wybierz pozycję **Właściwości**.  W bloku **Właściwości** możesz zauważyć poniższe wartości.<br> ![Właściwości konta](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)automatyzacji .

## <a name="set-up-integration-with-azure-monitor-logs"></a>Konfigurowanie integracji z dziennikami usługi Azure Monitor

1. Na komputerze uruchom program **Windows PowerShell** na ekranie **startowym.**
2. Uruchom następujący program PowerShell i edytuj `[your resource id]` `[resource id of the log analytics workspace]` wartość dla i z wartościami z poprzedniego kroku.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Po uruchomieniu tego skryptu może upłynąć godzinę przed rozpoczęciem, aby wyświetlić rekordy w dziennikach usługi Azure Monitor nowych JobLogs lub JobStreams są zapisywane.

Aby wyświetlić dzienniki, uruchom następującą kwerendę w wyszukiwaniu dziennika analizy dzienników:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Weryfikowanie konfiguracji

Aby potwierdzić, że konto automatyzacji wysyła dzienniki do obszaru roboczego usługi Log Analytics, sprawdź, czy diagnostyka jest poprawnie skonfigurowana na koncie automatyzacji przy użyciu następującego programu PowerShell:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Na wyjściu należy zapewnić, że:

* W obszarze *Dzienniki*wartość *włączonej* wartości to *True*.
* Wartość *WorkspaceId* jest ustawiona na Identyfikator zasobu obszaru roboczego usługi Log Analytics.

## <a name="azure-monitor-log-records"></a>Rekordy dziennika usługi Azure Monitor

Diagnostyka z usługi Azure Automation tworzy dwa typy rekordów w dziennikach usługi Azure Monitor i jest oznaczona jako **AzureDiagnostics.** Następujące kwerendy używają uaktualnionego języka zapytań do dzienników usługi Azure Monitor. Aby uzyskać informacje na temat typowych zapytań między starszym językiem zapytań a nowym językiem zapytań platformy Azure Kusto odwiedź starszy arkusz starszej wersji [programu Azure Kusto Query Language](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Dzienniki zadań

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina dla wykonania zadania elementu Runbook. |
| RunbookName_s |Nazwa elementu Runbook. |
| Caller_s |Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań. |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawy dla wywołującego. |
| JobId_g |Identyfikator GUID, który jest identyfikatorem zadania elementu Runbook. |
| Resulttype |Stan zadania elementu Runbook. Możliwe wartości:<br>- Nowe<br>- Utworzono<br>— Uruchomione<br>— Zatrzymane<br>— Wstrzymane<br>— Nie powiodło się<br>- Ukończono |
| Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobLogs. |
| OperationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku automatyzacji wartością jest Zadanie. |
| Zasób | Nazwa konta automatyzacji |
| SourceSystem | Jak dzienniki usługi Azure Monitor zebrane dane. Zawsze *diagnostyka platformy Azure* dla platformy Azure. |
| Opis wyników |Opisuje stan wyniku zadania elementu Runbook. Możliwe wartości:<br>— Zadanie jest uruchomione<br>— Zadanie nie powiodło się<br>— Zadanie zostało ukończone |
| CorrelationId |Identyfikator GUID, który jest identyfikatorem korelacji zadania elementu Runbook. |
| ResourceId |Określa identyfikator zasobu konta usługi Azure Automation w yjsce. |
| SubscriptionId | Identyfikator subskrypcji platformy Azure (GUID) dla konta automatyzacji. |
| ResourceGroup | Nazwa grupy zasobów dla konta automatyzacji. |
| ResourceProvider | Microsoft. Automatyzacji |
| ResourceType | KONTA AUTOMATYZACJI |


### <a name="job-streams"></a>Strumienie zadań
| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina dla wykonania zadania elementu Runbook. |
| RunbookName_s |Nazwa elementu Runbook. |
| Caller_s |Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań. |
| StreamType_s |Typ strumienia zadania. Możliwe wartości:<br>— Postęp<br>— Dane wyjściowe<br>— Ostrzeżenie<br>— Błąd<br>— Debugowanie<br>— Pełne |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawy dla wywołującego. |
| JobId_g |Identyfikator GUID, który jest identyfikatorem zadania elementu Runbook. |
| Resulttype |Stan zadania elementu Runbook. Możliwe wartości:<br>- W toku |
| Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobStreams. |
| OperationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku automatyzacji wartością jest Zadanie. |
| Zasób | Nazwa konta automatyzacji |
| SourceSystem | Jak dzienniki usługi Azure Monitor zebrane dane. Zawsze *diagnostyka platformy Azure* dla platformy Azure. |
| Opis wyników |Obejmuje strumień wyjściowy z elementu Runbook. |
| CorrelationId |Identyfikator GUID, który jest identyfikatorem korelacji zadania elementu Runbook. |
| ResourceId |Określa identyfikator zasobu konta usługi Azure Automation w yjsce. |
| SubscriptionId | Identyfikator subskrypcji platformy Azure (GUID) dla konta automatyzacji. |
| ResourceGroup | Nazwa grupy zasobów dla konta automatyzacji. |
| ResourceProvider | Microsoft. Automatyzacji |
| ResourceType | KONTA AUTOMATYZACJI |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Wyświetlanie dzienników automatyzacji w dziennikach usługi Azure Monitor

Po rozpoczęciu wysyłania dzienników zadań automatyzacji do dzienników usługi Azure Monitor zobacz, co można zrobić z tymi dziennikami w dziennikach usługi Azure Monitor.

Aby wyświetlić dzienniki, uruchom następującą kwerendę:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Wysyłanie wiadomości e-mail, gdy zadanie runbooka nie powiedzie się lub zawiesza
Jeden z najlepszych klientów pyta o możliwość wysyłania wiadomości e-mail lub tekstu, gdy coś pójdzie nie tak z zadaniem podstawowego.

Aby utworzyć regułę alertu, należy rozpocząć od utworzenia wyszukiwania dziennika rekordów zadań runbook, które powinny wywoływać alert. Kliknij przycisk **Alert,** aby utworzyć i skonfigurować regułę alertu.

1. Na stronie Omówienie obszaru roboczego usługi Log Analytics kliknij pozycję **Wyświetl dzienniki**.
2. Utwórz zapytanie wyszukiwania dziennika dla alertu, wpisując `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` następujące wyszukiwanie w polu kwerendy: Można również grupować według Nazwy Elementów Runbook, używając:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Jeśli skonfigurujesz dzienniki z więcej niż jednego konta automatyzacji lub subskrypcji do obszaru roboczego, można pogrupować alerty według subskrypcji i konta automatyzacji. Nazwa konta automatyzacji można znaleźć w polu Zasób w wyszukiwaniu dzienników zadań.
3. Aby otworzyć ekran **Reguła tworzenia,** kliknij przycisk **+ Nowa reguła alertu** u góry strony. Aby uzyskać więcej informacji na temat opcji konfigurowania alertu, zobacz [Dziennik alertów na platformie Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Znajdź wszystkie zadania, które zostały ukończone z błędami
Oprócz alertów o błędach można znaleźć, gdy zadanie uruchomieniu ma błąd nieubiegający. W takich przypadkach program PowerShell generuje strumień błędów, ale błędy nie kończące się nie powodują, że zadanie zawiesza się lub kończy się niepowodzeniem.

1. W obszarze roboczym usługi Log Analytics kliknij pozycję **Dzienniki**.
2. W polu kwerendy `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` wpisz, a następnie kliknij przycisk **Wyszukaj.**

### <a name="view-job-streams-for-a-job"></a>Wyświetlanie strumieni zadań dla zadania
Podczas debugowania zadania, można również zajrzeć do strumieni zadań. Następująca kwerenda pokazuje wszystkie strumienie dla pojedynczego zadania z GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Wyświetlanie stanu zadania historycznego
Na koniec możesz chcieć wizualizować historię zadań w czasie. Za pomocą tej kwerendy można wyszukiwać stan zadań w czasie.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Wykres stanu historycznego zadania analizy dziennika](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Usuwanie ustawień diagnostycznych

Aby usunąć ustawienie diagnostyczne z konta automatyzacji, uruchom następujące polecenia:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Podsumowanie

Wysyłając stan zadania automatyzacji i przesyłaj dane strumieniowe do dzienników usługi Azure Monitor, można uzyskać lepszy wgląd w stan zadań automatyzacji, korzystając z:
+ Konfigurowanie alertów informujących o problemie.
+ Używanie widoków niestandardowych i zapytań wyszukiwania do wizualizacji wyników elementu runbook, stanu zadania elementu runbook i innych powiązanych kluczowych wskaźników lub metryk.

Dzienniki usługi Azure Monitor zapewniają lepszą widoczność operacyjną zadań automatyzacji i mogą pomóc w szybszym rozwiązywaniu zdarzeń.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pomoc dotyczącą rozwiązywania problemów z analizą dzienników, zobacz [Rozwiązywanie problemów z tym, dlaczego usługa Log Analytics nie zbiera już danych.](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)
* Aby dowiedzieć się więcej na temat konstruowania różnych zapytań wyszukiwania i przeglądania dzienników zadań automatyzacji za pomocą dzienników usługi Azure Monitor, zobacz [Dzienniki wyszukiwania w dziennikach usługi Azure Monitor.](../log-analytics/log-analytics-log-searches.md)
* Aby dowiedzieć się, jak tworzyć i pobierać komunikaty wyjściowe i komunikaty o błędach z żyłaczek, zobacz [Dane wyjściowe i komunikaty runbooka](automation-runbook-output-and-messages.md).
* Aby dowiedzieć się więcej o wykonywaniu elementów runbook, sposobie monitorowania zadań elementów runbook i innych szczegółach technicznych, zobacz [Track a runbook job](automation-runbook-execution.md) (Śledzenie zadania elementu runbook).
* Aby dowiedzieć się więcej o dziennikach i źródłach zbierania danych usługi Azure Monitor, zobacz [Zbieranie danych magazynu platformy Azure w omówieniu dzienników usługi Azure Monitor.](../azure-monitor/platform/collect-azure-metrics-logs.md)

