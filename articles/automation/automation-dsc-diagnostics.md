---
title: Przekazuj konfiguracji stanu automatyzacji Azure dane raportowania z dziennikami usługi Azure Monitor
description: W tym artykule pokazano sposób wysyłania Desired State Configuration (DSC) dane raportowania z usługi Azure Automation stanu konfiguracji do usługi Azure Monitor dzienników dostarczyć dodatkowy wgląd i zarządzania.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0dad74f75fd7b73e7dab0b2dddbdfda193d5b2ec
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445795"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Przekazuj konfiguracji stanu automatyzacji Azure dane raportowania z dziennikami usługi Azure Monitor

Konfiguracja stanu usługi Azure Automation przechowuje dane stanu węzła przez 30 dni.
Jeśli chcesz zachować te dane przez dłuższy czas, możesz wysłać dane stanu węzła do obszaru roboczego usługi Log Analytics.
Stan zgodności jest widoczna w witrynie Azure portal lub za pomocą programu PowerShell dla węzłów i poszczególne zasoby DSC z konfiguracjami węzłów.
Przy użyciu dzienników usługi Azure Monitor możesz wykonywać następujące czynności:

- Uzyskaj informacje o zgodności dla węzłów zarządzanych i poszczególne zasoby
- Wyzwól wiadomość e-mail lub alertu na podstawie stanu zgodności
- Pisanie zapytań zaawansowanych między węzły zarządzane
- Korelowanie stan zgodności z konta usługi Automation
- Wizualizowanie historii zgodności węzła wraz z upływem czasu

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć wysyłanie raportów konfiguracji stanu usługi Automation do dzienników usługi Azure Monitor, potrzebne są:

- Listopad 2016 lub nowszej wersji [programu Azure PowerShell](/powershell/azure/overview) (v2.3.0).
- Konto usługi Azure Automation. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure Automation](automation-offering-get-started.md)
- Obszar roboczy usługi Log Analytics za pomocą **Automation & Control** oferty usługi. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure Monitor dzienniki](../log-analytics/log-analytics-get-started.md).
- Co najmniej jeden węzeł konfiguracji stan automatyzacji platformy Azure. Aby uzyskać więcej informacji, zobacz [dołączanie maszyn w celu zarządzania usługi Azure Automation stanu konfiguracji](automation-dsc-onboarding.md)

## <a name="set-up-integration-with-azure-monitor-logs"></a>Konfigurowanie integracji przy użyciu dzienników usługi Azure Monitor

Aby rozpocząć, importowanie danych z usługi Azure Automation DSC do usługi Azure Monitor dzienników, wykonaj następujące czynności:

1. Zaloguj się do konta platformy Azure w programie PowerShell. Zobacz [Zaloguj się przy użyciu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
1. Pobierz _ResourceId_ konta usługi automation, uruchamiając następujące polecenie programu PowerShell: (Jeśli masz więcej niż jedno konto usługi automation, wybierz _ResourceID_ dla konta, którą chcesz skonfigurować).

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzureRmResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Pobierz _ResourceId_ obszaru roboczego usługi Log Analytics, uruchamiając następujące polecenie programu PowerShell: (Jeśli masz więcej niż jeden obszar roboczy, wybierz _ResourceID_ dla obszaru roboczego, którą chcesz skonfigurować).

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzureRmResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Uruchom następujące polecenie programu PowerShell, zastępując `<AutomationResourceId>` i `<WorkspaceResourceId>` z _ResourceId_ wartości z każdego z poprzednich kroków:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories 'DscNodeStatus'
   ```

Jeśli chcesz zatrzymać importowanie danych z usługi Azure Automation stanu konfiguracji do dzienników usługi Azure Monitor, uruchom następujące polecenie programu PowerShell:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Wyświetlanie dzienników Konfiguracja stanu

Po skonfigurowaniu integracji z usługą Azure Monitor dzienników dla danych konfiguracji stanu automatyzacji **wyszukiwanie w dzienniku** na pojawi się przycisk **węzłów DSC** bloku konta usługi automation. Kliknij przycisk **wyszukiwanie w dzienniku** przycisk, aby wyświetlić dzienniki Aby uzyskać dane węzła DSC.

![Przycisk wyszukiwania dziennika](media/automation-dsc-diagnostics/log-search-button.png)

**Wyszukiwanie w dzienniku** zostanie otwarty blok, a zobaczysz **DscNodeStatusData** operacji dla każdego węzła konfiguracji stanu i **DscResourceStatusData** operacji dla każdego [ Zasób DSC](/powershell/dsc/resources) o nazwie w konfiguracji węzła zastosowane do tego węzła.

**DscResourceStatusData** operacja zawiera informacje o błędzie dla wszystkie zasoby DSC, które nie powiodło się.

Kliknij pozycję każdej operacji na liście, aby zobaczyć dane dla tej operacji.

Można również przeglądać dzienniki, wykonując wyszukiwanie w dziennikach w usłudze Azure Monitor.
Zobacz [znaleźć dane przy użyciu wyszukiwania w dzienniku](../log-analytics/log-analytics-log-searches.md).
Wpisz następujące zapytanie, aby znaleźć dzienników stan konfiguracji: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

Można również zawęzić zapytanie według nazwy operacji. Na przykład: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Wyślij wiadomość e-mail, gdy sprawdzanie zgodności konfiguracji stanu nie powiodło się.

Jedno z naszych żądań klienta jest możliwość wysyłania wiadomości e-mail lub SMS, gdy coś pójdzie nie tak z konfiguracją DSC.

Aby utworzyć regułę alertu, należy rozpocząć od tworzenia przeszukiwania dzienników dla rekordów raport konfiguracji stanu, które powinny wywoływać alert. Kliknij przycisk **+ Nowa reguła alertu** przycisk, aby utworzyć i skonfigurować regułę alertu.

1. Na stronie Przegląd obszaru roboczego usługi Log Analytics kliknij **dzienniki**.
1. Utwórz zapytanie wyszukiwania w dzienniku alertu, wpisując następujące wyszukiwanie w polu zapytania:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Jeśli po skonfigurowaniu dzienniki z więcej niż jednego konta usługi Automation lub subskrypcji do swojego obszaru roboczego, można grupować alerty według subskrypcji i konto usługi Automation.  
   Nazwa konta usługi Automation mogą pochodzić z pól zasobów w wyszukiwaniu DscNodeStatusData.  
1. Aby otworzyć **Utwórz regułę** ekranu, kliknij przycisk **+ Nowa reguła alertu** w górnej części strony. Aby uzyskać więcej informacji na temat opcji, aby skonfigurować alert, zobacz [Tworzenie reguły alertu](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Znajdź zasoby DSC nie powiodła się we wszystkich węzłach

Jedną z zalet przy użyciu dzienników usługi Azure Monitor to, że można wyszukiwać sprawdzenia zakończone niepowodzeniem w węzłach.
Aby znaleźć wszystkie wystąpienia zasobów DSC, które nie powiodło się.

1. Na stronie Przegląd obszaru roboczego usługi Log Analytics kliknij **dzienniki**.
1. Utwórz zapytanie wyszukiwania w dzienniku alertu, wpisując następujące wyszukiwanie w polu zapytania:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Widok historyczny stan węzła DSC

Na koniec można wizualizować historię stan węzła DSC wraz z upływem czasu.  
To zapytanie służy do wyszukiwania stan swój stan węzła DSC wraz z upływem czasu.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Spowoduje to wyświetlenie wykres stanu węzła wraz z upływem czasu.

## <a name="azure-monitor-logs-records"></a>Usługa Azure Monitor rejestruje rekordy

Diagnostyka usługi Azure Automation tworzy dwie kategorie rekordów w dziennikach w usłudze Azure Monitor.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina, gdy uruchomiono sprawdzania zgodności. |
| OperationName |DscNodeStatusData |
| ResultType |Czy ten węzeł jest zgodne. |
| NodeName_s |Nazwa węzła zarządzanego. |
| NodeComplianceStatus_s |Czy ten węzeł jest zgodne. |
| DscReportStatus |Czy sprawdzanie zgodności został uruchomiony pomyślnie. |
| ConfigurationMode | Jak konfiguracja jest stosowana do węzła. Możliwe wartości to __"ApplyOnly"__,__"ApplyandMonitior"__, i __"ApplyandAutoCorrect"__. <ul><li>__ApplyOnly__: Ma zastosowanie do konfiguracji DSC, a nie robi nic więcej, chyba że nowa konfiguracja zostanie przypisany do węzła docelowego lub gdy nowa konfiguracja zostanie ściągnięty z serwera. Po początkowej stosowania nowej konfiguracji DSC nie sprawdza odejście od stanu wcześniej skonfigurowany. DSC, podejmuje próbę zastosowania konfiguracji, dopóki nie zostanie pomyślnie przed __ApplyOnly__ staje się skuteczny. </li><li> __ApplyAndMonitor__: Jest to wartość domyślna. LCM stosuje wszystkie nowe konfiguracje. Po początkowym aplikacji nowej konfiguracji Jeśli węzeł docelowy drifts z żądanego stanu DSC raporty niezgodności w dziennikach. DSC, podejmuje próbę zastosowania konfiguracji, dopóki nie zostanie pomyślnie przed __ApplyAndMonitor__ staje się skuteczny.</li><li>__ApplyAndAutoCorrect__: DSC stosuje wszystkie nowe konfiguracje. Po początkowym aplikacji nowej konfiguracji Jeśli węzeł docelowy drifts z żądanego stanu DSC raporty niezgodności w dziennikach, a następnie przywrócenie bieżącej konfiguracji.</li></ul> |
| HostName_s | Nazwa węzła zarządzanego. |
| adres IP | Adres IPv4 zarządzany węzeł. |
| Kategoria | DscNodeStatus |
| Zasób | Nazwa konta usługi Azure Automation. |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawy dla obiektu wywołującego. |
| NodeId_g |Identyfikator GUID, który identyfikuje zarządzany węzeł. |
| DscReportId_g |Identyfikator GUID, który identyfikuje raportu. |
| LastSeenTime_t |Data i godzina, kiedy ostatnio były wyświetlane raportu. |
| ReportStartTime_t |Data i godzina rozpoczęcia raportu. |
| ReportEndTime_t |Data i godzina zakończenia raportu. |
| NumberOfResources_d |W konfiguracji węzeł o nazwie liczba zasobów DSC. |
| SourceSystem | Jak dane zbierane dzienniki usługi Azure Monitor. Zawsze *Azure* dla diagnostyki platformy Azure. |
| ResourceId |Określa konto usługi Azure Automation. |
| ResultDescription | Opis dla tej operacji. |
| SubscriptionId | Subskrypcja platformy Azure identyfikator (GUID) dla konta usługi Automation. |
| ResourceGroup | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | FIRMY MICROSOFT. USŁUGI AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Identyfikator GUID, który jest identyfikatorem korelacji raportu zgodności. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina, gdy uruchomiono sprawdzania zgodności. |
| OperationName |DscResourceStatusData|
| ResultType |Czy zasób jest zgodne. |
| NodeName_s |Nazwa węzła zarządzanego. |
| Kategoria | DscNodeStatus |
| Zasób | Nazwa konta usługi Azure Automation. |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawy dla obiektu wywołującego. |
| NodeId_g |Identyfikator GUID, który identyfikuje zarządzany węzeł. |
| DscReportId_g |Identyfikator GUID, który identyfikuje raportu. |
| DscResourceId_s |Nazwa wystąpienia zasobu DSC. |
| DscResourceName_s |Nazwa zasobu DSC. |
| DscResourceStatus_s |Czy zasobu DSC jest w zakresie zgodności. |
| DscModuleName_s |Nazwa modułu programu PowerShell, który zawiera zasób DSC. |
| DscModuleVersion_s |Wersja modułu programu PowerShell, który zawiera zasób DSC. |
| DscConfigurationName_s |Nazwa konfiguracji stosowane do węzła. |
| ErrorCode_s | Kod błędu, jeśli zasób nie powiodła się. |
| ErrorMessage_s |Komunikat o błędzie, jeśli zasób nie powiodło się. |
| DscResourceDuration_d |Czas w sekundach, który uruchomił zasobów DSC. |
| SourceSystem | Jak dane zbierane dzienniki usługi Azure Monitor. Zawsze *Azure* dla diagnostyki platformy Azure. |
| ResourceId |Określa konto usługi Azure Automation. |
| ResultDescription | Opis dla tej operacji. |
| SubscriptionId | Subskrypcja platformy Azure identyfikator (GUID) dla konta usługi Automation. |
| ResourceGroup | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | FIRMY MICROSOFT. USŁUGI AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Identyfikator GUID, który jest identyfikatorem korelacji raportu zgodności. |

## <a name="summary"></a>Podsumowanie

Wysyłając danych konfiguracji stanu usługi Automation do usługi Azure Monitor dzienników, można uzyskać lepszy wgląd w stan węzłów Konfiguracja stanu usługi Automation przez:

- Konfigurowanie alertów, aby otrzymywać powiadomienia, gdy występuje problem
- Przy użyciu niestandardowych widoków i zapytania wyszukiwania, aby zwizualizować wyniki elementu runbook, stan zadania elementu runbook i inne powiązane, kluczowe wskaźniki lub metryk.  

Dzienniki platformy Azure Monitor zapewnia większą widoczność operacyjną w danych konfiguracji stanu usługi Automation i pomóc w rozwiązuj szybciej.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [konfiguracji stan automatyzacji platformy Azure](automation-dsc-overview.md)
- Aby rozpocząć pracę, zobacz [wprowadzenie do usługi Azure Automation stanu konfiguracji](automation-dsc-getting-started.md)
- Aby dowiedzieć się więcej na temat kompilowanie konfiguracji DSC, dzięki czemu można je przypisać do węzłów docelowych, zobacz [kompilowanie konfiguracji w konfiguracji stan automatyzacji platformy Azure](automation-dsc-compile.md)
- Dokumentacja poleceń cmdlet programu PowerShell, można zobaczyć [poleceń cmdlet usługi Azure Automation stanu konfiguracji](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [cennika usługi Azure Automation stanu konfiguracji](https://azure.microsoft.com/pricing/details/automation/)
- Aby wyświetlić przykład użycia usługi Azure Automation stanu konfiguracji w potoku ciągłego wdrażania, zobacz [ciągłe przy użyciu usługi Azure Automation stan konfiguracji wdrożenia i narzędzia Chocolatey](automation-dsc-cd-chocolatey.md)
- Aby dowiedzieć się więcej o sposobie tworzenia różnych zapytań wyszukiwania i przejrzyj dzienniki konfiguracji stanu usługi Automation przy użyciu dzienników usługi Azure Monitor, zobacz [przeszukiwania dzienników w dzienniki usługi Azure Monitor](../log-analytics/log-analytics-log-searches.md)
- Aby dowiedzieć się więcej na temat dzienników usługi Azure Monitor i źródłach zbierania danych, zobacz [Azure zbieranie danych magazynu w usłudze Azure Monitor dzienników — omówienie](../azure-monitor/platform/collect-azure-metrics-logs.md)
