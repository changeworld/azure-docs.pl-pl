---
title: Przekazywanie danych zadania usługi Azure Automation do usługi Log Analytics
description: W tym artykule pokazano sposób wysyłania stan zadania i runbook strumieni zadania Analiza dzienników Azure dostarczać dodatkowe szczegółowe dane i zarządzania.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 116096fb27af299545a0f9a6adf57d794bbb2f6e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics"></a>Przekazuj strumienie zadania i stan zadania z automatyzacji do analizy dzienników
Automatyzacja może wysyłać runbook strumieni zadania stanu i zadania do swojego obszaru roboczego analizy dzienników. Rejestruje zadania i strumieni zadania są widoczne w portalu Azure lub przy użyciu programu PowerShell, dla poszczególnych zadań i to umożliwia wykonywanie prostych dochodzenia. Teraz przy użyciu analizy dzienników można:

* Uzyskiwanie wglądu w zadaniach automatyzacji.
* Wyzwalacz poczty e-mail lub alertu oparte na stan zadania elementu runbook (na przykład nie powiodło się lub wstrzymane).
* Zapis zaawansowanych zapytań na strumienie zadania.
* Korelowanie zadań na konta automatyzacji.
* Wizualizuj historię zadania wraz z upływem czasu.

## <a name="prerequisites-and-deployment-considerations"></a>Wymagania wstępne i zagadnienia dotyczące wdrażania
Aby rozpocząć wysyłanie dzienników automatyzacji do analizy dzienników, potrzebne są:

* Wydanie listopada 2016 lub nowszy [programu Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
* Obszar roboczy analizy dzienników. Aby uzyskać więcej informacji, zobacz [wprowadzenie do analizy dzienników](../log-analytics/log-analytics-get-started.md). 
* Element ResourceId dla Twojego konta usługi Automatyzacja Azure.


Aby znaleźć element ResourceId dla konta usługi Automatyzacja Azure:

```powershell-interactive
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Aby znaleźć element ResourceId obszaru roboczego analizy dzienników, uruchom następujące środowiska PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Jeśli masz więcej niż jednego konta automatyzacji lub znaleźć obszary robocze, w wyniku poprzedniego polecenia, *nazwa* należy skonfigurować i skopiuj wartość *ResourceId*.

Jeśli trzeba znaleźć *nazwa* Twojego konta automatyzacji w portalu Azure wybierz konto automatyzacji z **konta automatyzacji** bloku, a następnie wybierz **wszystkie ustawienia** . W bloku **Wszystkie ustawienia** w obszarze **Ustawienia konta** wybierz pozycję **Właściwości**.  W bloku **Właściwości** możesz zauważyć poniższe wartości.<br> ![Właściwości konta automatyzacji](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Konfigurowanie integracji z analizy dzienników

1. Na komputerze, należy uruchomić **programu Windows PowerShell** z **Start** ekranu.
2. Uruchom następujące środowiska PowerShell i edytować wartość `[your resource id]` i `[resource id of the log analytics workspace]` z wartościami z poprzedniego kroku.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true
   ```

Po uruchomieniu tego skryptu, zobaczysz rekordów w analizy dzienników w ciągu 10 minut nowe JobLogs lub JobStreams zapisywana.

Aby wyświetlić dzienniki, uruchom następujące zapytanie w wyszukiwania dziennika analizy dzienników: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Weryfikowanie konfiguracji
Aby upewnić się, że Twoje konto usługi Automatyzacja jest wysyłania dzienników do swojego obszaru roboczego analizy dzienników, sprawdź, czy diagnostyki są prawidłowo skonfigurowane na koncie automatyzacji za pomocą programu PowerShell następujące:

```powershell-interactive
Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

W danych wyjściowych upewnij się, że:
+ W obszarze *dzienniki*, wartość *włączone* jest *True*.
+ Wartość *WorkspaceId* ustawiono ResourceId obszaru roboczego analizy dzienników.

## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics

Diagnostyka usługi Automatyzacja Azure tworzy dwa typy rekordów w analizy dzienników i są oznaczone jako **AzureDiagnostics**. Następujące kwerendy używać języka kwerend uaktualniony do analizy dzienników. Aby uzyskać informacje dotyczące typowych kwerend między zapytania starszej wersji języka i nowy język zapytań usługi Analiza dzienników Azure można znaleźć [starszej wersji, aby nowy arkusz ze wskazówkami dotyczącymi język zapytań Analiza dzienników Azure](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Rejestruje zadania
| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina dla wykonania zadania elementu Runbook. |
| RunbookName_s |Nazwa elementu Runbook. |
| Caller_s |Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań. |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawy do obiektu wywołującego. |
| JobId_g |Identyfikator GUID, który jest identyfikatorem zadania elementu Runbook. |
| Typ ResultType |Stan zadania elementu Runbook. Możliwe wartości:<br>— Nowy<br>— Uruchomione<br>— Zatrzymane<br>— Wstrzymane<br>— Nie powiodło się<br>-Ukończone |
| Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobLogs. |
| OperationName | Określa typ operacji wykonywanej na platformie Azure. Do automatyzacji wartość jest zadanie. |
| Zasób | Nazwa konta automatyzacji |
| SourceSystem | Jak analizy dzienników zbierane dane. Zawsze *Azure* diagnostyki Azure. |
| ResultDescription |Opisuje stan wyniku zadania elementu Runbook. Możliwe wartości:<br>— Zadanie jest uruchomione<br>— Zadanie nie powiodło się<br>— Zadanie zostało ukończone |
| CorrelationId |Identyfikator GUID, który jest identyfikatorem korelacji zadania elementu Runbook. |
| ResourceId |Określa identyfikator zasobu konto usługi Automatyzacja Azure elementu runbook. |
| SubscriptionId | Subskrypcja platformy Azure identyfikatora (GUID) dla konta automatyzacji. |
| ResourceGroup | Nazwa grupy zasobów dla konta automatyzacji. |
| ResourceProvider | FIRMY MICROSOFT. AUTOMATYZACJA |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Strumienie zadania
| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina dla wykonania zadania elementu Runbook. |
| RunbookName_s |Nazwa elementu Runbook. |
| Caller_s |Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań. |
| StreamType_s |Typ strumienia zadania. Możliwe wartości:<br>— Postęp<br>— Dane wyjściowe<br>— Ostrzeżenie<br>— Błąd<br>— Debugowanie<br>— Pełne |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawy do obiektu wywołującego. |
| JobId_g |Identyfikator GUID, który jest identyfikatorem zadania elementu Runbook. |
| Typ ResultType |Stan zadania elementu Runbook. Możliwe wartości:<br>— W toku |
| Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobStreams. |
| OperationName | Określa typ operacji wykonywanej na platformie Azure. Do automatyzacji wartość jest zadanie. |
| Zasób | Nazwa konta automatyzacji |
| SourceSystem | Jak analizy dzienników zbierane dane. Zawsze *Azure* diagnostyki Azure. |
| ResultDescription |Obejmuje strumień wyjściowy z elementu Runbook. |
| CorrelationId |Identyfikator GUID, który jest identyfikatorem korelacji zadania elementu Runbook. |
| ResourceId |Określa identyfikator zasobu konto usługi Automatyzacja Azure elementu runbook. |
| SubscriptionId | Subskrypcja platformy Azure identyfikatora (GUID) dla konta automatyzacji. |
| ResourceGroup | Nazwa grupy zasobów dla konta automatyzacji. |
| ResourceProvider | FIRMY MICROSOFT. AUTOMATYZACJA |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Wyświetlanie automatyzacji loguje analizy dzienników
Teraz, gdy rozpoczęto wysyłanie dzienników zadania automatyzacji do analizy dzienników, zobacz, co można zrobić za pomocą tych dzienników wewnątrz analizy dzienników.

Aby wyświetlić dzienniki, uruchom następujące zapytanie: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Wyślij wiadomość e-mail, gdy zadanie elementu runbook nie powiodło się lub wstrzymuje
Jeden z najwyższym odbiorcy zapyta, jest możliwość wysyłania wiadomości e-mail lub tekstu w przypadku wystąpienia problemów z zadanie elementu runbook.   

Aby utworzyć regułę alertu, rozpoczyna się od utworzenia dziennika wyszukiwanie rekordów zadań elementu runbook, które powinny wywoływać alert. Kliknij przycisk **Alert** przycisk, aby utworzyć i skonfigurować regułę alertu.

1. Na stronie Przegląd analizy dziennika kliknij **wyszukiwania dziennika**.
2. Utwórz kwerendę wyszukiwania dziennika dla alertu przez wpisanie następującego wyszukiwania w polu zapytania: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` można także grupować według RunbookName przy użyciu: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Jeśli konfigurujesz dzienniki z więcej niż jednego konta automatyzacji lub subskrypcji do swojego obszaru roboczego, można grupować alerty subskrypcji i konto automatyzacji. Nazwa konta automatyzacji znajduje się w polu zasobu w wyszukiwaniu JobLogs.
1. Aby otworzyć **Dodaj regułę alertu** kliknij **alertu** w górnej części strony. Aby uzyskać więcej informacji na temat opcji, aby skonfigurować alert, zobacz [alertów w analizy dzienników](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Znajdź wszystkie zadania, które zostały ukończone z błędami
Oprócz alertów przy błędach, można znaleźć błąd niepowodujący po zadanie elementu runbook. W takich przypadkach PowerShell tworzy strumień błędów, ale błędy niepowodujący nie powodują zadania wstrzymać lub zakończyć się niepowodzeniem.    

1. W obszarze roboczym analizy dzienników, kliknij przycisk **wyszukiwania dziennika**.
2. W polu kwerendy wpisz `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` , a następnie kliknij przycisk **wyszukiwania** przycisku.

### <a name="view-job-streams-for-a-job"></a>Wyświetl zadania strumieni zadania
Debugowanie zadania, można również przyglądać strumieni zadania. Następujące zapytanie Wyświetla wszystkich strumieni dla jednego zadania z identyfikatorem GUID 2ebd22ea-e05e-4eb9 - 9d 76-d73cbd4356e0:   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Widok stanu zadania historycznych
Na koniec można zwizualizować historię zadania wraz z upływem czasu. Skorzystaj z tej kwerendy, aby wyszukać stan zadań wraz z upływem czasu.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Wykres stanu historycznego zadanie analizy dziennika](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Podsumowanie
Wysyłając automatyzacji zadań stan strumienia danych i do analizy dzienników, można uzyskać lepszy wgląd w stan zadań automatyzacji przez:
+ Konfigurowanie alertów powiadomienie, gdy występuje problem.
+ Przy użyciu niestandardowych widoków i zapytania wyszukiwania, aby wizualizować wyniki elementu runbook, stan zadania elementu runbook i inne powiązane kluczowych wskaźników i metryki.  

Analiza dzienników zapewnia lepszą widoczność operacyjnej do automatyzacji zadań i może ułatwić adres zdarzenia szybciej.  

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat sposobu konstruowania różne zapytania i przejrzyj dzienniki zadania automatyzacji z analizy dzienników, zobacz [Zaloguj wyszukiwania analizy dzienników](../log-analytics/log-analytics-log-searches.md).
* Aby poznać sposób tworzenia i pobrać dane wyjściowe i komunikaty o błędach z elementów runbook, zobacz [Runbook dane wyjściowe i komunikaty](automation-runbook-output-and-messages.md).
* Aby dowiedzieć się więcej o wykonywaniu elementów runbook, sposobie monitorowania zadań elementów runbook i innych szczegółach technicznych, zobacz [Track a runbook job](automation-runbook-execution.md) (Śledzenie zadania elementu runbook).
* Aby dowiedzieć się więcej na temat analizy dzienników i źródeł danych kolekcji, zobacz [Azure zbierania danych magazynu w omówieniu analizy dzienników](../log-analytics/log-analytics-azure-storage.md).
