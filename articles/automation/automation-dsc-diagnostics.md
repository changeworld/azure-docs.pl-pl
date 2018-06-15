---
title: Przekazuj Konfiguracja DSC automatyzacji Azure raportowania danych do analizy dzienników
description: W tym artykule przedstawiono sposób wysyłania żądanego stanu konfiguracji (DSC) raportowania danych do analizy dzienników dostarczać dodatkowe szczegółowe dane i zarządzania.
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d01042a02f2339f039f23d4f6e021de503dc3815
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195968"
---
# <a name="forward-azure-automation-dsc-reporting-data-to-log-analytics"></a>Przekazuj Konfiguracja DSC automatyzacji Azure raportowania danych do analizy dzienników

Automatyzacja może wysyłać dane stanu węzła DSC do obszaru roboczego analizy dzienników.  
Stan zgodności jest widoczny w portalu Azure lub programu PowerShell, dla węzłów i dla poszczególnych zasobów DSC w konfiguracji węzła. Analiza dzienników można:

* Pobierz informacje o zgodności dla węzłów zarządzanych i pojedynczych zasobów
* Wyzwalanie poczty e-mail lub alertu na podstawie stanu zgodności
* Zapis zaawansowanych zapytań na węzłach zarządzanych
* Korelowanie stanu zgodności dla konta automatyzacji
* Wizualizuj historii zgodności węzła wraz z upływem czasu

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć wysyłanie raportów usługi Konfiguracja DSC automatyzacji do analizy dzienników, potrzebne są:

* Wydanie listopada 2016 lub nowszy [programu Azure PowerShell](/powershell/azure/overview) (v2.3.0).
* Konto usługi Azure Automation. Aby uzyskać więcej informacji, zobacz [wprowadzenie do korzystania z usługi Automatyzacja Azure](automation-offering-get-started.md)
* Obszar roboczy analizy dzienników z **Automatyzacja i kontrola** oferty usługi. Aby uzyskać więcej informacji, zobacz [wprowadzenie do analizy dzienników](../log-analytics/log-analytics-get-started.md).
* Co najmniej jeden węzeł Konfiguracja DSC automatyzacji Azure. Aby uzyskać więcej informacji, zobacz [dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure](automation-dsc-onboarding.md)

## <a name="set-up-integration-with-log-analytics"></a>Konfigurowanie integracji z analizy dzienników

Aby rozpocząć, importowanie danych z usługi Konfiguracja DSC automatyzacji Azure do analizy dzienników, wykonaj następujące kroki:

1. Zaloguj się do konta platformy Azure w programie PowerShell. Zobacz [Zaloguj się przy użyciu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)
1. Pobierz _ResourceId_ Twojego konta automatyzacji za pomocą następującego polecenia programu PowerShell: (Jeśli masz więcej niż jedno konto automatyzacji wybierz _ResourceID_ dla konta, którego chcesz skonfigurować).

  ```powershell
  # Find the ResourceId for the Automation Account
  Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
  ```
1. Pobierz _ResourceId_ obszaru roboczego analizy dzienników, uruchamiając następujące polecenie programu PowerShell: (Jeśli masz więcej niż jednego obszaru roboczego wybierz _ResourceID_ dla obszaru roboczego, aby skonfigurować).

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
  ```
1. Uruchom następujące polecenie programu PowerShell, zastępując `<AutomationResourceId>` i `<WorkspaceResourceId>` z _ResourceId_ wartości z każdej z poprzednich kroków:

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories "DscNodeStatus"
  ```

Jeśli chcesz zatrzymać importowanie danych z usługi Konfiguracja DSC automatyzacji Azure do analizy dzienników, uruchom następujące polecenie programu PowerShell.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories "DscNodeStatus"
```

## <a name="view-the-dsc-logs"></a>Sprawdź dzienniki z usługi Konfiguracja DSC

Po skonfigurowaniu integracji z analizy dzienników dla danych usługi Konfiguracja DSC automatyzacji **wyszukiwania dziennika** na pojawi się przycisk **węzłów DSC** bloku Twoje konto usługi Automatyzacja. Kliknij przycisk **wyszukiwania dziennika** przycisk, aby wyświetlić dzienniki, aby dane węzła DSC.

![Przycisk wyszukiwania dziennika](media/automation-dsc-diagnostics/log-search-button.png)

**Wyszukiwania dziennika** zostanie otwarty blok i widzisz **DscNodeStatusData** operacji dla każdego węzła DSC i **DscResourceStatusData** operacja dla każdej [zasobów DSC](https://msdn.microsoft.com/powershell/dsc/resources) o nazwie w konfiguracji węzła zastosowane do tego węzła.

**DscResourceStatusData** operacja zawiera informacje o błędzie dla wszystkich zasobów DSC, których nie powiodła się.

Kliknij przycisk każdej operacji na liście, aby wyświetlić dane dla tej operacji.

Możesz również wyświetlić dzienniki, wyszukując [w module analiz dziennika. Zobacz [wyszukiwanie danych przy użyciu dziennika wyszukiwania](../log-analytics/log-analytics-log-searches.md).
Wpisz poniższe zapytanie w celu znalezienia dzienników DSC: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus"`

Można również ograniczyć zapytanie według nazwy operacji. Na przykład: "typ = AzureDiagnostics ResourceProvider ="MICROSOFT. Kategoria AUTOMATYZACJI"="DscNodeStatus"OperationName ="DscNodeStatusData"

### <a name="send-an-email-when-a-dsc-compliance-check-fails"></a>Wyślij wiadomość e-mail w przypadku niepowodzenia sprawdzania zgodności DSC

Jeden z naszych żądań najwyższym odbiorcy jest możliwość wysyłania wiadomości e-mail lub tekstu w przypadku wystąpienia problemów z konfiguracją usługi Konfiguracja DSC.   

Aby utworzyć regułę alertu, rozpoczyna się od utworzenia dziennika wyszukiwanie rekordów raportu DSC, które powinny wywoływać alert.  Kliknij przycisk **Alert** przycisk, aby utworzyć i skonfigurować regułę alertu.

1. Na stronie Przegląd analizy dziennika kliknij **wyszukiwania dziennika**.
1. Utwórz kwerendę wyszukiwania dziennika dla alertu przez wpisanie następującego wyszukiwania w polu kwerendy:  `Type=AzureDiagnostics Category=DscNodeStatus NodeName_s=DSCTEST1 OperationName=DscNodeStatusData ResultType=Failed`

  Jeśli zdefiniowano dzienniki z więcej niż jednego konta automatyzacji lub subskrypcji do swojego obszaru roboczego, można grupować alerty subskrypcji i konto automatyzacji.  
  Nazwa konta automatyzacji mogą pochodzić z pola zasobów do wyszukiwania DscNodeStatusData.  
1. Aby otworzyć **Dodaj regułę alertu** kliknij **alertu** w górnej części strony. Aby uzyskać więcej informacji na temat opcji, aby skonfigurować alert, zobacz [alertów w analizy dzienników](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Znajdź zasoby DSC nie powiodło się we wszystkich węzłach

Jedną z zalet przy użyciu analizy dzienników jest, że można wyszukiwać sprawdzenie nie powiodło się w węzłach.
Aby znaleźć wszystkie wystąpienia usługi Konfiguracja DSC zasobów, których nie powiodła się.

1. Na stronie Przegląd analizy dziennika kliknij **wyszukiwania dziennika**.
1. Utwórz kwerendę wyszukiwania dziennika dla alertu przez wpisanie następującego wyszukiwania w polu kwerendy:  `Type=AzureDiagnostics Category=DscNodeStatus OperationName=DscResourceStatusData ResultType=Failed`

### <a name="view-historical-dsc-node-status"></a>Wyświetl historyczne stan węzła DSC

Na koniec można zwizualizować historii stanu węzła DSC wraz z upływem czasu.  
Skorzystaj z tej kwerendy, aby wyszukać stan stan węzła DSC wraz z upływem czasu.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Spowoduje to wyświetlenie wykres stanu węzła wraz z upływem czasu.

## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics

Diagnostyka usługi Automatyzacja Azure tworzy dwie kategorie rekordów w analizy dzienników.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina, gdy uruchomiono sprawdzania zgodności. |
| OperationName |DscNodeStatusData |
| Typ ResultType |Określa, czy ten węzeł jest zgodne. |
| NodeName_s |Nazwa węzła zarządzanego. |
| NodeComplianceStatus_s |Określa, czy ten węzeł jest zgodne. |
| DscReportStatus |Określa, czy sprawdzanie zgodności został uruchomiony pomyślnie. |
| ConfigurationMode | Jak konfiguracja zostanie zastosowana do węzła. Możliwe wartości to __"ApplyOnly"__,__"ApplyandMonitior"__, i __"ApplyandAutoCorrect"__. <ul><li>__ApplyOnly__: DSC ma zastosowanie do konfiguracji i nie działają dalsze, chyba że nowa konfiguracja zostanie przypisany do węzła docelowego lub nowej konfiguracji są pobierane z serwera. Po początkowej stosowania nowej konfiguracji DSC nie sprawdza odejście od stanu wcześniej skonfigurowany. DSC próbuje zastosować konfigurację, dopóki nie zostanie pomyślnie przed __ApplyOnly__ obowiązuje. </li><li> __ApplyAndMonitor__: jest to wartość domyślna. LCM stosuje wszelkie nowe konfiguracje. Po początkowej stosowania nowej konfiguracji Jeśli węzeł docelowy drifts z żądanego stanu usługi Konfiguracja DSC raporty niezgodności w dziennikach. DSC próbuje zastosować konfigurację, dopóki nie zostanie pomyślnie przed __ApplyAndMonitor__ obowiązuje.</li><li>__ApplyAndAutoCorrect__: DSC stosuje wszelkie nowe konfiguracje. Po początkowej stosowania nowej konfiguracji Jeśli węzeł docelowy drifts z żądanego stanu DSC raporty niezgodności w dziennikach, a następnie przywrócenie bieżącej konfiguracji.</li></ul> |
| HostName_s | Nazwa węzła zarządzanego. |
| Adres IP | Adres IPv4 węzła zarządzanego. |
| Kategoria | DscNodeStatus |
| Zasób | Nazwa konta usługi Automatyzacja Azure. |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawy do obiektu wywołującego. |
| NodeId_g |Identyfikator GUID, który identyfikuje węzła zarządzanego. |
| DscReportId_g |Identyfikator GUID, który identyfikuje raportu. |
| LastSeenTime_t |Data i godzina, kiedy ostatnio były wyświetlane raportu. |
| ReportStartTime_t |Data i godzina uruchomienia raportu. |
| ReportEndTime_t |Data i godzina zakończenia raportu. |
| NumberOfResources_d |W konfiguracji zastosowany do węzła o nazwie liczba zasobów usługi Konfiguracja DSC. |
| SourceSystem | Jak analizy dzienników zbierane dane. Zawsze *Azure* diagnostyki Azure. |
| ResourceId |Określa konto usługi Automatyzacja Azure. |
| ResultDescription | Opis dla tej operacji. |
| SubscriptionId | Subskrypcja platformy Azure identyfikatora (GUID) dla konta automatyzacji. |
| ResourceGroup | Nazwa grupy zasobów dla konta automatyzacji. |
| ResourceProvider | FIRMY MICROSOFT. AUTOMATYZACJA |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Identyfikator GUID jest identyfikatorem korelacji raport zgodności. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina, gdy uruchomiono sprawdzania zgodności. |
| OperationName |DscResourceStatusData|
| Typ ResultType |Określa, czy zasób jest zgodne. |
| NodeName_s |Nazwa węzła zarządzanego. |
| Kategoria | DscNodeStatus |
| Zasób | Nazwa konta usługi Automatyzacja Azure. |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawy do obiektu wywołującego. |
| NodeId_g |Identyfikator GUID, który identyfikuje węzła zarządzanego. |
| DscReportId_g |Identyfikator GUID, który identyfikuje raportu. |
| DscResourceId_s |Nazwa wystąpienia zasobu usługi Konfiguracja DSC. |
| DscResourceName_s |Nazwa zasobu usługi Konfiguracja DSC. |
| DscResourceStatus_s |Określa, czy zasób DSC jest zgodne. |
| DscModuleName_s |Nazwa modułu programu PowerShell, zawierająca zasób usługi Konfiguracja DSC. |
| DscModuleVersion_s |Wersja moduł programu PowerShell, który zawiera zasób usługi Konfiguracja DSC. |
| DscConfigurationName_s |Nazwa konfiguracji zastosowany do węzła. |
| ErrorCode_s | Kod błędu, jeśli zasób nie powiodła się. |
| ErrorMessage_s |Komunikat o błędzie, jeśli zasób nie powiodło się. |
| DscResourceDuration_d |Czas w sekundach, którzy uruchomili zasobów usługi Konfiguracja DSC. |
| SourceSystem | Jak analizy dzienników zbierane dane. Zawsze *Azure* diagnostyki Azure. |
| ResourceId |Określa konto usługi Automatyzacja Azure. |
| ResultDescription | Opis dla tej operacji. |
| SubscriptionId | Subskrypcja platformy Azure identyfikatora (GUID) dla konta automatyzacji. |
| ResourceGroup | Nazwa grupy zasobów dla konta automatyzacji. |
| ResourceProvider | FIRMY MICROSOFT. AUTOMATYZACJA |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Identyfikator GUID jest identyfikatorem korelacji raport zgodności. |

## <a name="summary"></a>Podsumowanie

Wysyła dane usługi Konfiguracja DSC automatyzacji do analizy dzienników, można uzyskać lepszy wgląd w stan węzły DSC automatyzacji przez:

* Konfigurowanie alertów powiadomienie, gdy występuje problem
* Przy użyciu niestandardowych widoków i zapytania wyszukiwania, aby wizualizować wyniki elementu runbook, stan zadania elementu runbook i inne powiązane kluczowych wskaźników i metryki.  

Analiza dzienników zapewnia lepszą widoczność operacyjnej do danych usługi Konfiguracja DSC automatyzacji i może ułatwić szybciej adres zdarzenia.  

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat sposobu konstruowania różne zapytania i przejrzyj dzienniki usługi Konfiguracja DSC automatyzacji z analizy dzienników, zobacz [Zaloguj wyszukiwania analizy dzienników](../log-analytics/log-analytics-log-searches.md)
* Aby dowiedzieć się więcej o korzystaniu z usługi Konfiguracja DSC automatyzacji Azure, zobacz [wprowadzenie do korzystania z usługi Konfiguracja DSC automatyzacji Azure](automation-dsc-getting-started.md)
* Aby dowiedzieć się więcej na temat analizy dzienników i źródeł danych kolekcji, zobacz [Azure zbierania danych magazynu w omówieniu analizy dzienników](../log-analytics/log-analytics-azure-storage.md)

