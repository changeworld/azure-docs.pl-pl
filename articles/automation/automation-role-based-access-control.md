---
title: Kontrola dostępu oparta na rolach w usłudze Azure Automation
description: Funkcja kontroli dostępu opartej na rolach (role-based access control, RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure. Ten artykuł zawiera opis sposobu konfigurowania funkcji RBAC w usłudze Azure Automation.
keywords: automation rbac, kontrola dostępu oparta na rolach, azure rbac
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bcbda2464a4607aaa0b1bb96ef8f34c8713cb5f1
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918794"
---
# <a name="role-based-access-control-in-azure-automation"></a>Kontrola dostępu oparta na rolach w usłudze Azure Automation

Funkcja kontroli dostępu opartej na rolach (role-based access control, RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure. Za pomocą [RBAC](../role-based-access-control/overview.md), można segregować obowiązki w ramach zespołu i udzielać uprawnień dostępu do użytkowników, grup i aplikacji, które są im niezbędne do wykonywania swoich zadań. Prawo dostępu oparte na rolach może zostać przydzielone użytkownikom za pomocą witryny Azure Portal, narzędzi wiersza polecenia platformy Azure lub interfejsów API zarządzania platformy Azure.

## <a name="roles-in-automation-accounts"></a>Role w ramach kont usługi Automation

W usłudze Azure Automation prawo dostępu jest nadawane poprzez przypisywanie użytkownikom, grupom i aplikacjom odpowiednich ról RBAC w zakresie konta usługi. Poniżej przedstawiono wbudowane role obsługiwane przez konto automatyzacji:

| **Rola** | **Opis** |
|:--- |:--- |
| Właściciel |Rola właściciela umożliwia dostęp do wszystkich zasobów i akcji w ramach konta usługi Automation, w tym zapewnienie dostępu innym użytkownikom, grupom i aplikacjom Zarządzanie kontem usługi Automation. |
| Współautor |Rola Współautor umożliwia zarządzanie wszystkim, z wyjątkiem modyfikowania uprawnień dostępu innych użytkowników do konta usługi Automation. |
| Czytelnik |Rola Czytelnik służy do wyświetlania wszystkich zasobów w ramach konta usługi Automation, ale nie pozwala na wprowadzanie żadnych zmian. |
| Operator usługi |Rola Operator usługi Automation pozwala wyświetlić nazwę elementu runbook i właściwości oraz do tworzenia zadań i zarządzanie nimi dla wszystkich elementów runbook w ramach konta usługi Automation. Ta rola jest przydatna, jeśli zasoby konta usługi Automation, takie jak zasoby poświadczeń i inne elementy Runbook, mają być chronione przed możliwością wyświetlenia lub modyfikowania, ale członkowie organizacji mają mieć możliwość wykonywania tych elementów Runbook. |
|Operator zadań usługi Automation|Rola Operator zadań automatyzacji pozwala na tworzenie zadań i zarządzanie nimi dla wszystkich elementów runbook w ramach konta usługi Automation.|
|Operator elementów runbook usługi Automation|Rola Operator elementów Runbook usługi Automation umożliwia wyświetlenie nazwy i właściwości elementu runbook.|
| Współautor usługi Log Analytics | Rola Współautor usługi Log Analytics umożliwia odczytywanie wszystkich danych monitorowania i edytowanie ustawień monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia maszyny Wirtualnej do maszyn wirtualnych, odczytywanie kluczy kont magazynu, aby można było skonfigurować zbieranie dzienników z usługi Azure storage, tworzenie i konfigurowanie kont usługi Automation, dodawanie rozwiązań i konfigurowanie diagnostyki platformy Azure na wszystkie zasoby platformy Azure.|
| Czytelnik usługi Log Analytics | Rola Czytelnik usługi Log Analytics umożliwia wyświetlanie i wyszukiwanie wszystkich monitorowania danych także wyświetlać ustawienia monitorowania. Dotyczy to również wyświetlanie konfiguracji diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. |
| Współautor monitorowania | Rola Współautor monitorowania umożliwia odczytywanie wszystkich danych monitorowania i aktualizacji ustawienia monitorowania.|
| Czytelnik monitorowania | Rola Czytelnik monitorowania umożliwia odczytywanie wszystkich danych monitorowania. |
| Administrator dostępu użytkowników |Rola Administrator dostępu użytkowników umożliwia zarządzanie dostępem użytkowników do kont usługi Azure Automation. |

## <a name="role-permissions"></a>Uprawnienia ról

W poniższych tabelach opisano określone uprawnienia udzielone każdej roli. Może to obejmować działania, które należy nadać uprawnienia, i NotActions, która je ograniczyć.

### <a name="owner"></a>Właściciel

Właściciel może zarządzać wszystkim łącznie z dostępem. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|Akcje|Opis|
|---|---|
|Microsoft.Automation/automationAccounts/|Tworzenie i zarządzanie zasobami wszystkich typów.|

### <a name="contributor"></a>Współautor

Współautor mogą zarządzać wszystkim poza dostępem. W poniższej tabeli przedstawiono uprawnienia udzielane i odmowy dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Tworzenie i zarządzanie zasobami wszystkich typów|
|**Nie akcje**||
|Microsoft.Authorization/*/Delete| Usuwanie ról i przypisań ról.       |
|Microsoft.Authorization/*/Write     |  Tworzenie ról i przypisań ról.       |
|Microsoft.Authorization/elevateAccess/Action    | Nie zezwala na możliwość tworzenia Administrator dostępu użytkowników.       |

### <a name="reader"></a>Czytelnik

Czytnik mogą wyświetlać wszystkie zasoby w ramach konta usługi Automation, ale nie może wprowadzać żadnych zmian.

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Wyświetlanie wszystkich zasobów w ramach konta usługi Automation. |

### <a name="automation-operator"></a>Operator usługi

Operator usługi Automation jest możliwość tworzenia i zarządzania zadaniami i Odczyt nazwy elementów runbook i właściwości dla wszystkich elementów runbook w ramach konta usługi Automation.  Uwaga: Jeśli chcesz kontrolować operator dostępu do poszczególnych elementów runbook, a następnie nie Ustaw tę rolę, a zamiast tego użyj "Operator zadań automatyzacji" i "Operator elementów Runbook usługi Automation" ról w połączeniu. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Przeczytaj autoryzacji.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Przeczytaj zasoby hybrydowego procesu roboczego elementu Runbook.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista zadań elementu runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Wznów zadanie, które zostało wstrzymane.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Anulowanie zadania w toku.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Przeczytaj strumieni zadań i danych wyjściowych.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Pobierz dane wyjściowe zadania.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Wstrzymaj zadanie w toku.|
|Microsoft.Automation/automationAccounts/jobs/write|Tworzenie zadań.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Pobierz harmonogram zadań usługi Azure Automation.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Utwórz harmonogram zadań usługi Azure Automation.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Pobieranie obszaru roboczego, połączony z kontem usługi automation.|
|Microsoft.Automation/automationAccounts/read|Uzyskaj konto usługi Azure Automation.|
|Microsoft.Automation/automationAccounts/runbooks/read|Pobierz element runbook usługi Automation.|
|Microsoft.Automation/automationAccounts/schedules/read|Pobierz zasób harmonogramu usługi Azure Automation.|
|Microsoft.Automation/automationAccounts/schedules/write|Utwórz lub zaktualizuj zasób harmonogramu usługi Azure Automation.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Przeczytaj, ról i przypisań ról.         |
|Microsoft.Resources/deployments/*      |Tworzenie i zarządzanie wdrożeniami grup zasobów.         |
|Microsoft.Insights/alertRules/*      | Utwórz i Zarządzaj regułami alertów.        |
|Microsoft.Support/* |Tworzenie i zarządzanie biletami pomocy technicznej.|

### <a name="automation-job-operator"></a>Operator zadań usługi Automation

Rola Operator zadań automatyzacji udzielany jest w zakresie konta usługi Automation. Dzięki temu uprawnienia operatora do tworzenia zadań i zarządzanie nimi dla wszystkich elementów runbook w ramach konta. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Przeczytaj autoryzacji.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista zadań elementu runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Wznów zadanie, które zostało wstrzymane.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Anulowanie zadania w toku.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Przeczytaj strumieni zadań i danych wyjściowych.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Wstrzymaj zadanie w toku.|
|Microsoft.Automation/automationAccounts/jobs/write|Tworzenie zadań.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Przeczytaj, ról i przypisań ról.       |
|Microsoft.Resources/deployments/*      |Tworzenie i zarządzanie wdrożeniami grup zasobów.         |
|Microsoft.Insights/alertRules/*      | Utwórz i Zarządzaj regułami alertów.        |
|Microsoft.Support/* |Tworzenie i zarządzanie biletami pomocy technicznej.|

### <a name="automation-runbook-operator"></a>Operator elementów runbook usługi Automation

Rola Operator elementów Runbook usługi Automation jest udzielany w zakresie elementu Runbook. Operator elementów Runbook usługi Automation można wyświetlić nazwę i właściwości elementu runbook.  Tej roli w połączeniu z roli "Operator zadań automatyzacji" umożliwia operatorowi również tworzenie zadań i zarządzanie nimi dla elementu runbook. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Wyświetl listę elementów runbook.        |
|Microsoft.Authorization/*/read      | Przeczytaj autoryzacji.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Przeczytaj, ról i przypisań ról.         |
|Microsoft.Resources/deployments/*      | Tworzenie i zarządzanie wdrożeniami grup zasobów.         |
|Microsoft.Insights/alertRules/*      | Utwórz i Zarządzaj regułami alertów.        |
|Microsoft.Support/*      | Tworzenie i zarządzanie biletami pomocy technicznej.        |

### <a name="log-analytics-contributor"></a>Współautor usługi Log Analytics

Współautor usługi Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia do maszyn wirtualnych; Odczytywanie kluczy kont magazynu, aby można było skonfigurować zbieranie dzienników z usługi Azure Storage; Tworzenie i konfigurowanie kont usługi Automation; Dodawanie rozwiązań. i konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Przeczytaj zasoby wszystkich typów, z wyjątkiem wpisów tajnych.|
|Microsoft.Automation/automationAccounts/*|Zarządzanie kontami usługi automation.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Tworzenie i zarządzanie nimi rozszerzenia maszyny wirtualnej.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Wyświetl klucze konta magazynu klasycznego.|
|Microsoft.Compute/virtualMachines/extensions/*|Utwórz i Zarządzaj rozszerzeniami klasycznej maszyny wirtualnej.|
|Microsoft.Insights/alertRules/*|Odczyt/zapis/usuwanie reguły alertu.|
|Microsoft.Insights/diagnosticSettings/*|Odczyt/zapis/usuwanie ustawień diagnostycznych.|
|Microsoft.OperationalInsights/*|Zarządzanie dziennikami usługi Azure Monitor.|
|Microsoft.OperationsManagement/*|Zarządzanie rozwiązaniami, w obszarach roboczych.|
|Microsoft.Resources/deployments/*|Tworzenie i zarządzanie wdrożeniami grup zasobów.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Tworzenie i zarządzanie wdrożeniami grup zasobów.|
|Microsoft.Storage/storageAccounts/listKeys/action|Wyświetl klucze konta magazynu.|
|Microsoft.Support/*|Tworzenie i zarządzanie biletami pomocy technicznej.|

### <a name="log-analytics-reader"></a>Czytelnik usługi Log Analytics

Czytelnik usługi Log Analytics można wyświetlać i wyszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, tym konfigurację diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. W poniższej tabeli przedstawiono uprawnienia udzielić lub odmówić dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Przeczytaj zasoby wszystkich typów, z wyjątkiem wpisów tajnych.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Zarządzanie zapytaniami w dziennikach w usłudze Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/search/action|Wyszukaj dane dzienników z usługi Azure Monitor.|
|Microsoft.Support/*|Tworzenie i zarządzanie biletami pomocy technicznej.|
|**Nie akcje**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Nie można odczytać klucze dostępu współużytkowanego.|

### <a name="monitoring-contributor"></a>Współautor monitorowania

Współautor monitorowania może odczytywać wszystkie dane monitorowania i aktualizować ustawienia monitorowania. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Przeczytaj zasoby wszystkich typów, z wyjątkiem wpisów tajnych.|
|Microsoft.AlertsManagement/alerts/*|Zarządzanie alertami.|
|Microsoft.AlertsManagement/alertsSummary/*|Zarządzaj alertu pulpitu nawigacyjnego.|
|Microsoft.Insights/AlertRules/*|Zarządzaj regułami alertów.|
|Microsoft.Insights/components/*|Zarządzanie składnikami usługi Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Zarządzaj ustawienia diagnostyczne.|
|Microsoft.Insights/eventtypes/*|Wyświetl listę zdarzeń dziennika aktywności (zdarzenia zarządzania) w ramach subskrypcji. To uprawnienie ma zastosowanie do portalu i programowy dostęp do dziennika aktywności.|
|Microsoft.Insights/LogDefinitions/*|To uprawnienie jest wymagane dla użytkowników, którzy potrzebują dostępu do dzienników aktywności w portalu. Lista kategorii dziennika w dzienniku aktywności.|
|Microsoft.Insights/MetricDefinitions/*|Przeczytaj definicje metryk (lista dostępnych typów metryki dla zasobu).|
|Microsoft.Insights/Metrics/*|Odczytać metryki dla zasobu.|
|Microsoft.Insights/Register/Action|Rejestrowanie dostawcy Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Zarządzaj testy sieci web usługi Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Zarządzaj pakietów rozwiązań dzienniki usługi Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Zarządzaj wyszukiwaniami zapisane dzienniki usługi Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/search/action|Przeszukaj obszary robocze usługi Log Analytics.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Wyświetl listę kluczy dla obszaru roboczego usługi Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Zarządzaj konfiguracje insight magazynu dzienników usługi Azure Monitor.|
|Microsoft.Support/*|Tworzenie i zarządzanie biletami pomocy technicznej.|
|Microsoft.WorkloadMonitor/workloads/*|Zarządzanie obciążeniami.|

### <a name="monitoring-reader"></a>Czytelnik monitorowania

Czytelnik monitorowania może czytać wszystkie dane monitorowania. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Przeczytaj zasoby wszystkich typów, z wyjątkiem wpisów tajnych.|
|Microsoft.OperationalInsights/workspaces/search/action|Przeszukaj obszary robocze usługi Log Analytics.|
|Microsoft.Support/*|Tworzenie i zarządzanie biletami pomocy technicznej|

### <a name="user-access-administrator"></a>Administrator dostępu użytkowników

Administrator dostępu użytkowników można zarządzać dostępem użytkowników do zasobów platformy Azure. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Odczyt wszystkich zasobów|
|Microsoft.Authorization/*|Zarządzanie autoryzacji|
|Microsoft.Support/*|Tworzenie i zarządzanie biletami pomocy technicznej|

## <a name="onboarding"></a>Dołączanie

Poniższe tabele pokazują minimalnych wymaganych uprawnień, służące do dołączania maszyn wirtualnych w celu śledzenia zmian lub zaktualizować rozwiązania do zarządzania.

### <a name="onboarding-from-a-virtual-machine"></a>Dołączanie z maszyny wirtualnej

|**Akcja**  |**Uprawnienie**  |**Minimalny zakres**  |
|---------|---------|---------|
|Pisanie nowego wdrożenia      | Microsoft.Resources/deployments/*          |Subskrypcja          |
|Zapisać nową grupę zasobów      | Microsoft.Resources/subscriptions/resourceGroups/write        | Subskrypcja          |
|Utwórz nowe rozwiązanie domyślne obszaru roboczego      | Microsoft.OperationalInsights/workspaces/write         | Grupa zasobów         |
|Utwórz nowe konto      |  Microsoft.Automation/automationAccounts/write        |Grupa zasobów         |
|Połącz obszar roboczy i konto      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Obszar roboczy</br>Konto usługi Automation
|Utwórz rozwiązanie      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Grupa zasobów          |
|Tworzenie rozszerzenia programu MMA      | Microsoft.Compute/virtualMachines/write         | Maszyna wirtualna         |
|Tworzenie zapisanego wyszukiwania      | Microsoft.OperationalInsights/workspaces/write          | Obszar roboczy         |
|Tworzenie konfiguracji zakresu      | Microsoft.OperationalInsights/workspaces/write          | Obszar roboczy         |
|Rozwiązanie łączy do konfiguracji zakresu      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Rozwiązanie         |
|Sprawdź stan dołączania — odczytu obszaru roboczego      | Microsoft.OperationalInsights/workspaces/read         | Obszar roboczy         |
|Sprawdzanie stanu dołączania do - odczytu połączony obszar roboczy własności konta     | Microsoft.Automation/automationAccounts/read      | Konto usługi Automation        |
|Sprawdź stan dołączania — rozwiązanie do odczytu      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Rozwiązanie         |
|Sprawdzanie stanu dołączania do - odczytu maszyny Wirtualnej      | Microsoft.Compute/virtualMachines/read         | Maszyna wirtualna         |
|Sprawdź stan dołączania — odczytu konta      | Microsoft.Automation/automationAccounts/read  |  Konto usługi Automation   |
| Dołączanie do obszaru roboczego wyboru dla maszyny Wirtualnej<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Subskrypcja         |

<sup>1</sup> to uprawnienie jest wymagane do dołączania za pomocą środowiska portalu maszyny Wirtualnej.

### <a name="onboarding-from-automation-account"></a>Dołączanie z konta usługi Automation

|**Akcja**  |**Uprawnienie** |**Minimalny zakres**  |
|---------|---------|---------|
|Utwórz nowe wdrożenie     | Microsoft.Resources/deployments/*        | Subskrypcja         |
|Utwórz nową grupę zasobów     | Microsoft.Resources/subscriptions/resourceGroups/write         | Subskrypcja        |
|Blok AutomationOnboarding — Utwórz nowy obszar roboczy     |Microsoft.OperationalInsights/workspaces/write           | Grupa zasobów        |
|Blok AutomationOnboarding — odczyt połączonego obszaru roboczego     | Microsoft.Automation/automationAccounts/read        | Konto usługi Automation       |
|Blok AutomationOnboarding — odczyt rozwiązania     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Rozwiązanie        |
|Blok AutomationOnboarding — odczytu obszaru roboczego     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Obszar roboczy        |
|Utworzyć link do obszaru roboczego i konta     | Microsoft.OperationalInsights/workspaces/write        | Obszar roboczy        |
|Konto dla shoebox zapisu      | Microsoft.Automation/automationAccounts/write        | Konto        |
|Utwórz rozwiązanie      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Grupa zasobów         |
|Utwórz/Edytuj zapisane wyszukiwanie     | Microsoft.OperationalInsights/workspaces/write        | Obszar roboczy        |
|Tworzenie/Edytowanie konfiguracji zakresu     | Microsoft.OperationalInsights/workspaces/write        | Obszar roboczy        |
|Rozwiązanie łączy do konfiguracji zakresu      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Rozwiązanie         |
|**Krok 2 — dołączanie wielu maszyn wirtualnych**     |         |         |
|Blok VMOnboarding — tworzenie MMA rozszerzenia     | Microsoft.Compute/virtualMachines/write           | Maszyna wirtualna        |
|Utwórz / Edytuj zapisane wyszukiwanie     | Microsoft.OperationalInsights/workspaces/write           | Obszar roboczy        |
|Tworzenie i edytowanie konfiguracji zakresu  | Microsoft.OperationalInsights/workspaces/write   | Obszar roboczy|

## <a name="update-management"></a>Zarządzanie aktualizacjami

Rozwiązanie Update management osiągnie wielu usług, aby zapewnić jej obsługę. W poniższej tabeli przedstawiono uprawnienia wymagane do zarządzania wdrożeniami zarządzania aktualizacji:

|**Zasób**  |**Rola**  |**Zakres**  |
|---------|---------|---------|
|Konto usługi Automation     | Współautor usługi Log Analytics       | Konto usługi Automation        |
|Konto usługi Automation    | Współautor maszyny wirtualnej        | Grupa zasobów dla konta        |
|Obszar roboczy usługi Log Analytics     | Współautor usługi Log Analytics| Obszar roboczy usługi Log Analytics        |
|Obszar roboczy usługi Log Analytics |Czytelnik usługi Log Analytics| Subskrypcja|
|Rozwiązanie     |Współautor usługi Log Analytics         | Rozwiązanie|
|Maszyna wirtualna     | Współautor maszyny wirtualnej        | Maszyna wirtualna        |

## <a name="configure-rbac-for-your-automation-account"></a>Konfigurowanie funkcji RBAC dla konta usługi Automation

W poniższej sekcji pokazano sposób konfigurowania funkcji RBAC dla konta usługi Automation za pośrednictwem [portal](#configure-rbac-using-the-azure-portal) i [programu PowerShell](#configure-rbac-using-powershell)

### <a name="configure-rbac-using-the-azure-portal"></a>Konfigurowanie funkcji RBAC przy użyciu witryny Azure portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) i otwórz swoje konto usługi Automation ze strony Konta usługi Automation.
2. Kliknij pozycję **kontrola dostępu (IAM)** kontroli w lewym górnym rogu. Spowoduje to otwarcie **kontrola dostępu (IAM)** strony, gdzie można dodać nowych użytkowników, grup i aplikacji do automatyzacji zarządzania konta oraz wyświetlić istniejące role, które można skonfigurować dla konta usługi Automation.
3. Kliknij przycisk **przypisań ról** kartę.

   ![Przycisk Dostęp](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Dodawanie nowego użytkownika i przypisywanie roli

1. Z **kontrola dostępu (IAM)** kliknij **+ Dodaj przypisanie roli** otworzyć **Dodaj przypisanie roli** strony, w którym można dodać użytkownika, grupę lub aplikację i przypisać rolę do nich.

2. Wybierz rolę z listy dostępnych ról. Można wybrać jedną z dostępnych wbudowanych ról obsługiwanych przez konto usługi Automation lub dowolną rolę niestandardową, które zostały zdefiniowane.

3. Wpisz nazwę użytkownika, użytkownika, którą chcesz nadać uprawnienia do w **wybierz** pola. Wybierz użytkownika z listy, a następnie kliknij przycisk **Zapisz**.

   ![Dodawanie użytkowników](media/automation-role-based-access-control/automation-04-add-users.png)

   Powinien zostać wyświetlony użytkownik dodany do **użytkowników** strony z wybraną rolą przypisane

   ![Wyświetlanie użytkowników](media/automation-role-based-access-control/automation-05-list-users.png)

   Możesz także przypisać rolę użytkownikowi na stronie **Role**.
4. Kliknij przycisk **role** z **kontrola dostępu (IAM)** strony, aby otworzyć **role** strony. Na tej stronie możesz wyświetlić nazwę roli oraz liczbę użytkowników i grup przypisanych do tej roli.

    ![Przypisywanie roli na stronie użytkowników](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Kontrola dostępu oparta na rolach można ustawić tylko w zakresie konta usługi Automation, a nie z poziomu jakiegokolwiek zasobu poniżej konta usługi Automation.

#### <a name="remove-a-user"></a>Usuwanie użytkownika

Można usunąć uprawnienia dostępu dla użytkownika, który nie jest zarządzane konto usługi Automation lub nie pracuje już w organizacji. Poniżej przedstawiono kroki prowadzące do usunięcia użytkownika:

1. Z **kontrola dostępu (IAM)** użytkownika chcesz usunąć, a następnie kliknij przycisk Wybierz **Usuń**.
2. Kliknij przycisk **Usuń** w okienku szczegółów przypisania.
3. Kliknij przycisk **Tak**, aby potwierdzić usunięcie.

   ![Usuwanie użytkowników](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Konfigurowanie funkcji RBAC przy użyciu programu PowerShell

Można również skonfigurować opartej na rolach dostęp do konta usługi Automation za pomocą następujących [poleceń cmdlet programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

[Get-AzureRmRoleDefinition](/previous-versions/azure/mt603792(v=azure.100)) Wyświetla wszystkie role RBAC, które są dostępne w usłudze Azure Active Directory. Można użyć tego polecenia z właściwością **Nazwa**, aby wyświetlić listę wszystkich akcji, które mogą być wykonywane przez określoną rolę.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Poniżej przedstawiono przykładowe dane wyjściowe:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzureRmRoleAssignment](/previous-versions/azure/mt619413(v=azure.100)) Wyświetla przypisania ról RBAC usługi Azure AD w podanym zakresie. Bez żadnych parametrów to polecenie zwraca wszystkie przypisania ról dokonane w ramach subskrypcji. Parametr **ExpandPrincipalGroups** powoduje wyświetlenie listy przypisań dla określonego użytkownika oraz grup, których członkiem jest użytkownik.
    **Przykład:** Użyj następującego polecenia, aby wyświetlić listę wszystkich użytkowników i ich ról w ramach konta usługi automation.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Poniżej przedstawiono przykładowe dane wyjściowe:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

[Nowy-AzureRmRoleAssignment](/previous-versions/azure/mt603580(v=azure.100)) Aby przypisać dostęp użytkownikom, grupom i aplikacjom dla określonego zakresu.
    **Przykład:** Użyj następującego polecenia, aby przypisać rolę "Operator usługi" dla użytkownika w zakresie konta usługi Automation.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Poniżej przedstawiono przykładowe dane wyjściowe:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Użyj [Remove-AzureRmRoleAssignment](/previous-versions/azure/mt603781(v=azure.100)) można usunąć dostępu określonego użytkownika, grupy lub aplikacji z określonego zakresu.
    **Przykład:** Użyj następującego polecenia, aby usunąć użytkownika z roli "Operator usługi" w zakresie konta usługi Automation.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

W powyższych przykładach należy zastąpić **identyfikator logowania**, **identyfikator subskrypcji**, **nazwy grupy zasobów**, i **nazwa konta usługi Automation** za pomocą usługi Szczegóły konta. Gdy zostanie wyświetlony monit o potwierdzenie usunięcia przypisania roli do użytkownika, wybierz pozycję **Tak**.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Środowiska użytkownika dla roli operatora usługi - konto usługi Automation

Gdy użytkownik, kto jest przydzielony do roli operatora usługi w zakresie konta usługi Automation przegląda konto usługi Automation, które są przypisane do, mogą je tylko przeglądać listę elementów runbook, zadań elementów runbook i harmonogramów utworzonych w usłudze Automation konta, ale nie można wyświetlić ich Definicja. Może uruchamiać, zatrzymywać, wstrzymywać, wznawiać lub planować zadania elementu runbook. Użytkownik nie ma dostępu do innych zasobów usługi Automation, takie jak konfiguracje, grupy hybrydowych procesów roboczych lub węzłów DSC.

![Brak dostępu do zasobów](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Konfigurowanie funkcji RBAC dla elementów Runbook

Usługa Azure Automation umożliwia przypisywanie RBAC do poszczególnych elementów runbook. Tym celu uruchom następujący skrypt, aby dodać użytkownika do określonego elementu runbook. Poniższy skrypt można uruchomić przez administratora konta usługi Automation lub administratora dzierżawy.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation Account
$automationAccountName ="<Automation Account Name>" # Name of the Automation Account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation Account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be ran once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Raz wykonane, użytkownik, zaloguj się do witryny Azure portal i wyświetl **wszystkie zasoby**. Na liście zobaczą elementu Runbook, zostały dodane jako **Operator elementów Runbook usługi Automation** dla.

![RBAC elementu Runbook w portalu](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Środowiska użytkownika dla roli operatora usługi - elementu Runbook

Gdy użytkownik, który jest przypisany do roli operatora usługi Automation na element Runbook określania zakresu widoków w elemencie Runbook są przypisane do, mogą tylko uruchomić element runbook i służy do wyświetlania zadań elementu runbook.

![Tylko ma dostęp do uruchomienia](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Kolejne kroki

* Więcej informacji dotyczących różnych sposobów konfigurowania funkcji RBAC w usłudze Azure Automation można znaleźć w artykule [Manage RBAC with Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) (Zarządzanie funkcją RBAC przy użyciu programu Azure PowerShell).
* Szczegółowe informacje dotyczące różnych sposobów uruchamiania elementu Runbook można znaleźć w artykule [Uruchamianie elementu Runbook](automation-starting-a-runbook.md).
* Informacje dotyczące różnych typów elementów Runbook można znaleźć w artykule [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook w usłudze Azure Automation)

