---
title: Kontrola dostępu oparta na rolach w usłudze Azure Automation
description: Funkcja kontroli dostępu opartej na rolach (role-based access control, RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure. Ten artykuł zawiera opis sposobu konfigurowania funkcji RBAC w usłudze Azure Automation.
keywords: automation rbac, kontrola dostępu oparta na rolach, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 05/17/2018
ms.topic: conceptual
ms.openlocfilehash: 122e1f69e952acc00aba3cad2d75cb87b8fd08ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421586"
---
# <a name="role-based-access-control-in-azure-automation"></a>Kontrola dostępu oparta na rolach w usłudze Azure Automation

Funkcja kontroli dostępu opartej na rolach (role-based access control, RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure. Za pomocą [RBAC](../role-based-access-control/overview.md)można oddzielić cła w zespole i przyznać dostęp tylko do użytkowników, grup i aplikacji, których potrzebują do wykonywania swoich zadań. Prawo dostępu oparte na rolach może zostać przydzielone użytkownikom za pomocą witryny Azure Portal, narzędzi wiersza polecenia platformy Azure lub interfejsów API zarządzania platformy Azure.

## <a name="roles-in-automation-accounts"></a>Role na kontach usługi Automation

W usłudze Azure Automation prawo dostępu jest nadawane poprzez przypisywanie użytkownikom, grupom i aplikacjom odpowiednich ról RBAC w zakresie konta usługi. Poniżej przedstawiono wbudowane role obsługiwane przez konto automatyzacji:

| **Rola** | **Opis** |
|:--- |:--- |
| Właściciel |Rola właściciela umożliwia dostęp do wszystkich zasobów i akcji w ramach konta usługi Automation, w tym zapewnia dostęp innym użytkownikom, grupom i aplikacjom w celu zarządzania kontem usługi Automation. |
| Współautor |Rola Współautor umożliwia zarządzanie wszystkim, z wyjątkiem modyfikowania uprawnień dostępu innych użytkowników do konta usługi Automation. |
| Czytelnik |Rola Czytelnik służy do wyświetlania wszystkich zasobów w ramach konta usługi Automation, ale nie pozwala na wprowadzanie żadnych zmian. |
| Operator usługi |Rola operatora automatyzacji umożliwia wyświetlenie nazwy i właściwości elementu Runbook oraz tworzenie zadań i zarządzanie nimi dla wszystkich elementów Runbook w ramach konta usługi Automation. Ta rola jest przydatna, jeśli zasoby konta usługi Automation, takie jak zasoby poświadczeń i inne elementy Runbook, mają być chronione przed możliwością wyświetlenia lub modyfikowania, ale członkowie organizacji mają mieć możliwość wykonywania tych elementów Runbook. |
|Operator zadania automatyzacji|Rola operatora zadań automatyzacji umożliwia tworzenie zadań dla wszystkich elementów Runbook w ramach konta usługi Automation i zarządzanie nimi.|
|Operator elementu Runbook usługi Automation|Rola operatora elementu Runbook usługi Automation umożliwia wyświetlenie nazwy i właściwości elementu Runbook.|
| Współautor usługi Log Analytics | Rola współautor Log Analytics umożliwia odczytywanie wszystkich danych monitorowania i edytowanie ustawień monitorowania. Edytowanie ustawień monitorowania obejmuje dodanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych, przeczytanie kluczy konta magazynu w celu skonfigurowania kolekcji dzienników z usługi Azure Storage, tworzenia i konfigurowania kont usługi Automation, dodawania rozwiązań i konfigurowania diagnostyki platformy Azure na wszystkie zasoby platformy Azure.|
| Czytelnik usługi Log Analytics | Rola czytelnik Log Analytics umożliwia wyświetlanie i przeszukiwanie wszystkich danych monitorowania oraz Wyświetlanie ustawień monitorowania. Obejmuje to wyświetlanie konfiguracji diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. |
| Współautor monitorowania | Rola współautor monitorowania umożliwia odczytywanie wszystkich danych monitorowania i aktualizowanie ustawień monitorowania.|
| Czytnik monitorowania | Rola czytelnik monitorowania umożliwia odczytywanie wszystkich danych monitorowania. |
| Administrator dostępu użytkowników |Rola Administrator dostępu użytkowników umożliwia zarządzanie dostępem użytkowników do kont usługi Azure Automation. |

## <a name="role-permissions"></a>Uprawnienia roli

W poniższych tabelach opisano określone uprawnienia dla każdej roli. Może to obejmować akcje, które powodują, że uprawnienia i nienaruszone ograniczenia.

### <a name="owner"></a>Właściciel

Właściciel może zarządzać wszystkimi, w tym dostępem. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|Akcje|Opis|
|---|---|
|Microsoft.Automation/automationAccounts/|Twórz zasoby wszystkich typów i zarządzaj nimi.|

### <a name="contributor"></a>Współautor

Współautor może zarządzać wszystko z wyjątkiem dostępu. W poniższej tabeli przedstawiono uprawnienia udzielone i odrzucone dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Twórz zasoby wszystkich typów i zarządzaj nimi|
|**Nie akcje**||
|Microsoft.Authorization/*/Delete| Usuń role i przypisania ról.       |
|Microsoft.Authorization/*/Write     |  Tworzenie ról i przypisań ról.       |
|Microsoft.Authorization/elevateAccess/Action    | Odrzuca możliwość tworzenia administratora dostępu użytkownika.       |

### <a name="reader"></a>Czytelnik

Czytelnik może wyświetlać wszystkie zasoby na koncie usługi Automation, ale nie może wprowadzać żadnych zmian.

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Wyświetl wszystkie zasoby na koncie usługi Automation. |

### <a name="automation-operator"></a>Operator usługi

Operator usługi Automation może tworzyć zadania i zarządzać nimi oraz odczytywać nazwy elementów Runbook i właściwości wszystkich elementów Runbook w ramach konta usługi Automation.  Uwaga: Jeśli chcesz kontrolować dostęp operatorów do poszczególnych elementów Runbook, nie ustawiaj tej roli, a zamiast tego użyj ról operatora zadań automatyzacji i operator elementu Runbook usługi Automation w połączeniu. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Zapoznaj się z autoryzacją.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Odczytaj zasoby hybrydowego procesu roboczego elementu Runbook.|
|Microsoft.Automation/automationAccounts/jobs/read|Wyświetl listę zadań elementu Runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Wznów zadanie, które zostało wstrzymane.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Anuluj zadanie w toku.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Odczytaj strumienie i dane wyjściowe zadania.|
|Microsoft. Automation/automationAccounts/Jobs/Output/Read|Pobierz dane wyjściowe zadania.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Wstrzymaj zadanie w toku.|
|Microsoft.Automation/automationAccounts/jobs/write|Tworzenie zadań.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Pobierz harmonogram zadań Azure Automation.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Utwórz harmonogram zadań Azure Automation.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Pobierz obszar roboczy połączony z kontem usługi Automation.|
|Microsoft.Automation/automationAccounts/read|Pobierz konto Azure Automation.|
|Microsoft.Automation/automationAccounts/runbooks/read|Pobierz element Runbook Azure Automation.|
|Microsoft.Automation/automationAccounts/schedules/read|Pobierz zasób harmonogramu Azure Automation.|
|Microsoft.Automation/automationAccounts/schedules/write|Utwórz lub zaktualizuj zasób harmonogramu Azure Automation.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Odczytuj role i przypisania ról.         |
|Microsoft. resources/Deployments/*      |Tworzenie wdrożeń grup zasobów i zarządzanie nimi.         |
|Microsoft.Insights/alertRules/*      | Tworzenie reguł alertów i zarządzanie nimi.        |
|Microsoft.Support/* |Twórz bilety pomocy technicznej i zarządzaj nimi.|

### <a name="automation-job-operator"></a>Operator zadania automatyzacji

Rola operatora zadań automatyzacji jest przyznawana w zakresie konta usługi Automation. Umożliwia to uprawnienia operatora do tworzenia zadań dla wszystkich elementów Runbook w ramach konta i zarządzania nimi. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Zapoznaj się z autoryzacją.|
|Microsoft.Automation/automationAccounts/jobs/read|Wyświetl listę zadań elementu Runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Wznów zadanie, które zostało wstrzymane.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Anuluj zadanie w toku.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Odczytaj strumienie i dane wyjściowe zadania.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Wstrzymaj zadanie w toku.|
|Microsoft.Automation/automationAccounts/jobs/write|Tworzenie zadań.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Odczytuj role i przypisania ról.       |
|Microsoft. resources/Deployments/*      |Tworzenie wdrożeń grup zasobów i zarządzanie nimi.         |
|Microsoft.Insights/alertRules/*      | Tworzenie reguł alertów i zarządzanie nimi.        |
|Microsoft.Support/* |Twórz bilety pomocy technicznej i zarządzaj nimi.|

### <a name="automation-runbook-operator"></a>Operator elementu Runbook usługi Automation

Rola operatora elementu Runbook usługi Automation jest przyznawana w zakresie elementu Runbook. Operator elementu Runbook usługi Automation może wyświetlać nazwę i właściwości elementu Runbook.  Ta rola w połączeniu z rolą "operator zadania automatyzacji" umożliwia operatorowi tworzenie zadań dla elementu Runbook i zarządzanie nimi. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Wyświetl listę elementów Runbook.        |
|Microsoft.Authorization/*/read      | Zapoznaj się z autoryzacją.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Odczytuj role i przypisania ról.         |
|Microsoft. resources/Deployments/*      | Tworzenie wdrożeń grup zasobów i zarządzanie nimi.         |
|Microsoft.Insights/alertRules/*      | Tworzenie reguł alertów i zarządzanie nimi.        |
|Microsoft.Support/*      | Twórz bilety pomocy technicznej i zarządzaj nimi.        |

### <a name="log-analytics-contributor"></a>Współautor usługi Log Analytics

Współautor Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych; Odczytywanie kluczy konta magazynu w celu skonfigurowania kolekcji dzienników z usługi Azure Storage; Tworzenie i Konfigurowanie kont usługi Automation; Dodawanie rozwiązań; i Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych.|
|Microsoft.Automation/automationAccounts/*|Zarządzaj kontami usługi Automation.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Utwórz rozszerzenia maszyny wirtualnej i zarządzaj nimi.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Wyświetl listę kluczy klasycznych kont magazynu.|
|Microsoft.Compute/virtualMachines/extensions/*|Utwórz klasyczne rozszerzenia maszyn wirtualnych i zarządzaj nimi.|
|Microsoft.Insights/alertRules/*|Odczyt/zapis/usuwanie reguł alertów.|
|Microsoft.Insights/diagnosticSettings/*|Odczyt/zapis/usuwanie ustawień diagnostycznych.|
|Microsoft.OperationalInsights/*|Zarządzaj dziennikami Azure Monitor.|
|Microsoft.OperationsManagement/*|Zarządzanie rozwiązaniami w obszarach roboczych.|
|Microsoft. resources/Deployments/*|Tworzenie wdrożeń grup zasobów i zarządzanie nimi.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Tworzenie wdrożeń grup zasobów i zarządzanie nimi.|
|Microsoft.Storage/storageAccounts/listKeys/action|Wyświetl listę kluczy konta magazynu.|
|Microsoft.Support/*|Twórz bilety pomocy technicznej i zarządzaj nimi.|

### <a name="log-analytics-reader"></a>Czytelnik usługi Log Analytics

Czytelnik Log Analytics może wyświetlać i przeszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym Wyświetlanie konfiguracji diagnostyki platformy Azure na wszystkich zasobach platformy Azure. W poniższej tabeli przedstawiono uprawnienia udzielone lub odrzucone dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Zarządzanie zapytaniami w dziennikach Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/search/action|Wyszukaj Azure Monitor dane dziennika.|
|Microsoft.Support/*|Twórz bilety pomocy technicznej i zarządzaj nimi.|
|**Nie akcje**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Nie można odczytać kluczy dostępu współdzielonego.|

### <a name="monitoring-contributor"></a>Współautor monitorowania

Współautor monitorowania może odczytać wszystkie dane monitorowania i zaktualizować ustawienia monitorowania. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych.|
|Microsoft.AlertsManagement/alerts/*|Zarządzanie alertami.|
|Microsoft.AlertsManagement/alertsSummary/*|Zarządzaj pulpitem nawigacyjnym alertów.|
|Microsoft.Insights/AlertRules/*|Zarządzaj regułami alertów.|
|Microsoft.Insights/components/*|Zarządzanie składnikami Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Zarządzaj ustawieniami diagnostycznymi.|
|Microsoft.Insights/eventtypes/*|Wyświetl listę zdarzeń dziennika aktywności (zdarzenia zarządzania) w subskrypcji. To uprawnienie dotyczy zarówno dostępu programowego, jak i portalu do dziennika aktywności.|
|Microsoft.Insights/LogDefinitions/*|To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Wyświetl listę kategorii dziennika w dzienniku aktywności.|
|Microsoft.Insights/MetricDefinitions/*|Odczytaj definicje metryk (lista dostępnych typów metryk dla zasobu).|
|Microsoft.Insights/Metrics/*|Odczytaj metryki dla zasobu.|
|Microsoft.Insights/Register/Action|Zarejestruj dostawcę Microsoft. Insights.|
|Microsoft.Insights/webtests/*|Zarządzaj Application Insights testami sieci Web.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Zarządzanie Azure Monitor rejestruje pakiety rozwiązań.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Zarządzaj zapisanymi wyszukiwaniami dzienników Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/search/action|Wyszukaj Log Analytics obszary robocze.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Lista kluczy dla obszaru roboczego Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Zarządzaj Azure Monitor dzienników usługi Storage Insights.|
|Microsoft.Support/*|Twórz bilety pomocy technicznej i zarządzaj nimi.|
|Microsoft.WorkloadMonitor/workloads/*|Zarządzanie obciążeniami.|

### <a name="monitoring-reader"></a>Czytnik monitorowania

Czytnik monitorowania może odczytywać wszystkie dane monitorowania. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych.|
|Microsoft.OperationalInsights/workspaces/search/action|Wyszukaj Log Analytics obszary robocze.|
|Microsoft.Support/*|Tworzenie biletów pomocy technicznej i zarządzanie nimi|

### <a name="user-access-administrator"></a>Administrator dostępu użytkowników

Administrator dostępu użytkowników może zarządzać dostępem użytkowników do zasobów platformy Azure. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Odczytuj wszystkie zasoby|
|Microsoft.Authorization/*|Zarządzanie autoryzacją|
|Microsoft.Support/*|Tworzenie biletów pomocy technicznej i zarządzanie nimi|

## <a name="onboarding"></a>Dołączanie

W poniższych tabelach przedstawiono minimalne wymagane uprawnienia wymagane do dołączania maszyn wirtualnych na potrzeby śledzenia zmian lub rozwiązań do zarządzania aktualizacjami.

### <a name="onboarding-from-a-virtual-machine"></a>Dołączanie z maszyny wirtualnej

|**Akcja**  |**Zezwolenie**  |**Zakres minimalny**  |
|---------|---------|---------|
|Napisz nowe wdrożenie      | Microsoft. resources/Deployments/*          |Subskrypcja          |
|Zapisz nową grupę zasobów      | Microsoft.Resources/subscriptions/resourceGroups/write        | Subskrypcja          |
|Utwórz nowy domyślny obszar roboczy      | Microsoft.OperationalInsights/workspaces/write         | Grupa zasobów         |
|Utwórz nowe konto      |  Microsoft.Automation/automationAccounts/write        |Grupa zasobów         |
|Połącz obszar roboczy i konto      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Obszar roboczy</br>Konto usługi Automation
|Utwórz rozwiązanie      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Grupa zasobów          |
|Utwórz rozszerzenie MMA      | Microsoft.Compute/virtualMachines/write         | Maszyna wirtualna         |
|Utwórz zapisane wyszukiwanie      | Microsoft.OperationalInsights/workspaces/write          | Obszar roboczy         |
|Utwórz konfigurację zakresu      | Microsoft.OperationalInsights/workspaces/write          | Obszar roboczy         |
|Połącz rozwiązanie z konfiguracją zakresu      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Rozwiązanie         |
|Sprawdzanie stanu dołączania — obszar roboczy odczytu      | Microsoft.OperationalInsights/workspaces/read         | Obszar roboczy         |
|Sprawdzanie stanu dołączania — odczytywanie właściwości połączonego obszaru roboczego konta     | Microsoft.Automation/automationAccounts/read      | Konto usługi Automation        |
|Sprawdzanie stanu dołączania — Rozpoznaj rozwiązanie      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Rozwiązanie         |
|Sprawdzanie stanu dołączania — Odczytaj maszynę wirtualną      | Microsoft.Compute/virtualMachines/read         | Maszyna wirtualna         |
|Sprawdzanie stanu dołączania — konto do odczytu      | Microsoft.Automation/automationAccounts/read  |  Konto usługi Automation   |
| Sprawdzanie obszaru roboczego dołączania dla maszyny wirtualnej<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Subskrypcja         |
| Rejestrowanie dostawcy Log Analytics |Microsoft. Insights/Register/Action | Subskrypcja|

<sup>1</sup> to uprawnienie jest konieczne do dołączania do środowiska portalu maszyn wirtualnych.

### <a name="onboarding-from-automation-account"></a>Dołączanie z konta usługi Automation

|**Akcja**  |**Zezwolenie** |**Zakres minimalny**  |
|---------|---------|---------|
|Utwórz nowe wdrożenie     | Microsoft. resources/Deployments/*        | Subskrypcja         |
|Utwórz nową grupę zasobów     | Microsoft.Resources/subscriptions/resourceGroups/write         | Subskrypcja        |
|Blok AutomationOnboarding — Tworzenie nowego obszaru roboczego     |Microsoft.OperationalInsights/workspaces/write           | Grupa zasobów        |
|Blok AutomationOnboarding — odczytywanie połączonego obszaru roboczego     | Microsoft.Automation/automationAccounts/read        | Konto usługi Automation       |
|Blok AutomationOnboarding — rozwiązanie do odczytu     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Rozwiązanie        |
|Blok AutomationOnboarding — obszar roboczy odczytywania     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Obszar roboczy        |
|Utwórz łącze dla obszaru roboczego i konta     | Microsoft.OperationalInsights/workspaces/write        | Obszar roboczy        |
|Zapisz konto dla Shoebox      | Microsoft.Automation/automationAccounts/write        | Konto        |
|Utwórz rozwiązanie      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Grupa zasobów         |
|Utwórz/Edytuj zapisane wyszukiwanie     | Microsoft.OperationalInsights/workspaces/write        | Obszar roboczy        |
|Utwórz/edytuj konfigurację zakresu     | Microsoft.OperationalInsights/workspaces/write        | Obszar roboczy        |
|Połącz rozwiązanie z konfiguracją zakresu      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Rozwiązanie         |
| Rejestrowanie dostawcy Log Analytics |Microsoft. Insights/Register/Action | Subskrypcja|
|**Krok 2 — dołączanie wielu maszyn wirtualnych**     |         |         |
|Blok VMOnboarding — tworzenie rozszerzenia MMA     | Microsoft.Compute/virtualMachines/write           | Maszyna wirtualna        |
|Utwórz/Edytuj zapisane wyszukiwanie     | Microsoft.OperationalInsights/workspaces/write           | Obszar roboczy        |
|Utwórz/edytuj konfigurację zakresu  | Microsoft.OperationalInsights/workspaces/write   | Obszar roboczy|

## <a name="update-management"></a>Zarządzanie aktualizacjami

Zarządzanie aktualizacjami dociera do wielu usług w celu zapewnienia swojej usługi. W poniższej tabeli przedstawiono uprawnienia, które są konieczne do zarządzania wdrożeniami zarządzania aktualizacjami:

|**Zasób**  |**Rola**  |**Zakres**  |
|---------|---------|---------|
|Konto usługi Automation     | Współautor usługi Log Analytics       | Konto usługi Automation        |
|Konto usługi Automation    | Współautor maszyny wirtualnej        | Grupa zasobów dla konta        |
|Obszar roboczy usługi Log Analytics     | Współautor usługi Log Analytics| Obszar roboczy usługi Log Analytics        |
|Obszar roboczy usługi Log Analytics |Czytelnik usługi Log Analytics| Subskrypcja|
|Rozwiązanie     |Współautor usługi Log Analytics         | Rozwiązanie|
|Maszyna wirtualna     | Współautor maszyny wirtualnej        | Maszyna wirtualna        |

## <a name="configure-rbac-for-your-automation-account"></a>Konfigurowanie funkcji RBAC dla konta usługi Automation

W poniższej sekcji pokazano, jak skonfigurować kontrolę RBAC na koncie usługi Automation za pomocą [portalu](#configure-rbac-using-the-azure-portal) i [programu PowerShell](#configure-rbac-using-powershell)

### <a name="configure-rbac-using-the-azure-portal"></a>Konfigurowanie kontroli RBAC przy użyciu Azure Portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) i otwórz swoje konto usługi Automation ze strony Konta usługi Automation.
2. Kliknij kontrolkę **Kontrola dostępu (IAM)** w lewym górnym rogu. Spowoduje to otwarcie strony **Kontrola dostępu (IAM)** , na której można dodać nowych użytkowników, grupy i aplikacje do zarządzania kontem usługi Automation oraz wyświetlić istniejące role, które można skonfigurować dla konta usługi Automation.
3. Kliknij przycisk **przypisań ról** kartę.

   ![Przycisk Dostęp](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Dodawanie nowego użytkownika i przypisywanie roli

1. Na stronie **Kontrola dostępu (IAM)** kliknij pozycję **+ Dodaj przypisanie roli** , aby otworzyć stronę **Dodawanie przypisania roli** , na której możesz dodać użytkownika, grupę lub aplikację, a następnie przypisz do nich rolę.

2. Wybierz rolę z listy dostępnych ról. Można wybrać dowolną z dostępnych ról wbudowanych, które obsługuje konto usługi Automation, lub dowolną rolę niestandardową, która może być zdefiniowana.

3. Wpisz nazwę użytkownika, do którego chcesz nadać uprawnienia w polu **Wybierz** . Wybierz użytkownika z listy i kliknij przycisk **Zapisz**.

   ![Dodawanie użytkowników](media/automation-role-based-access-control/automation-04-add-users.png)

   Teraz powinien zostać wyświetlony użytkownik dodany do strony **Użytkownicy** z przypisaną wybraną rolą

   ![Wyświetlanie użytkowników](media/automation-role-based-access-control/automation-05-list-users.png)

   Możesz także przypisać rolę użytkownikowi na stronie **Role**.
4. Kliknij pozycję **role** na stronie **kontroli dostępu (IAM)** , aby otworzyć stronę **role** . Na tej stronie możesz wyświetlić nazwę roli oraz liczbę użytkowników i grup przypisanych do tej roli.

    ![Przypisywanie roli na stronie użytkowników](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Kontrolę dostępu opartą na rolach można ustawić tylko w zakresie konta usługi Automation, a nie w żadnym z zasobów poniżej konta usługi Automation.

#### <a name="remove-a-user"></a>Usuwanie użytkownika

Możesz usunąć uprawnienie dostępu dla użytkownika, który nie zarządza kontem usługi Automation lub który nie działa już w organizacji. Poniżej przedstawiono kroki prowadzące do usunięcia użytkownika:

1. Na stronie **Kontrola dostępu (IAM)** wybierz użytkownika, którego chcesz usunąć, a następnie kliknij przycisk **Usuń**.
2. Kliknij przycisk **Usuń** w okienku szczegółów przypisania.
3. Kliknij przycisk **Tak**, aby potwierdzić usunięcie.

   ![Usuwanie użytkowników](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Konfigurowanie kontroli RBAC przy użyciu programu PowerShell

Dostęp oparty na rolach można również skonfigurować na koncie usługi Automation przy użyciu następujących [poleceń cmdlet Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

[Get-AzureRmRoleDefinition](/previous-versions/azure/mt603792(v=azure.100)) wyświetla wszystkie role RBAC, które są dostępne w Azure Active Directory. Można użyć tego polecenia z właściwością **Nazwa**, aby wyświetlić listę wszystkich akcji, które mogą być wykonywane przez określoną rolę.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Oto przykładowe dane wyjściowe:

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

[Get-AzureRmRoleAssignment](/previous-versions/azure/mt619413(v=azure.100)) wyświetla przypisania ról RBAC usługi Azure AD w określonym zakresie. Bez żadnych parametrów to polecenie zwraca wszystkie przypisania ról dokonane w ramach subskrypcji. Parametr **ExpandPrincipalGroups** powoduje wyświetlenie listy przypisań dla określonego użytkownika oraz grup, których członkiem jest użytkownik.
    **Przykład:** użyj poniższego polecenia, aby wyświetlić listę wszystkich użytkowników i ich ról w ramach konta usługi Automation.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Oto przykładowe dane wyjściowe:

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

Polecenie [New-AzureRmRoleAssignment](/previous-versions/azure/mt603580(v=azure.100)) umożliwia przypisywanie dostępu do użytkowników, grup i aplikacji do określonego zakresu.
    **Przykład:** Użyj poniższego polecenia, aby przypisać rolę "operator usługi" dla użytkownika w zakresie konta usługi Automation.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Oto przykładowe dane wyjściowe:

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

Użyj polecenie [Remove-AzureRmRoleAssignment](/previous-versions/azure/mt603781(v=azure.100)) , aby usunąć dostęp określonego użytkownika, grupy lub aplikacji z określonego zakresu.
    **Przykład:** Użyj poniższego polecenia, aby usunąć użytkownika z roli "operator usługi" w zakresie konta usługi Automation.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

W powyższych przykładach Zamień **Identyfikator logowania**, **Identyfikator subskrypcji**, **nazwę grupy zasobów**i **nazwę konta usługi Automation** na szczegóły swojego konta. Gdy zostanie wyświetlony monit o potwierdzenie usunięcia przypisania roli do użytkownika, wybierz pozycję **Tak**.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Środowisko użytkownika dla roli operatora automatyzacji — konto usługi Automation

Gdy użytkownik przypisany do roli operatora usługi w zakresie konta usługi Automation przegląda konto usługi Automation, do którego jest przypisany, może wyświetlić tylko listę elementów Runbook, zadań elementów Runbook i harmonogramów utworzonych w ramach konta usługi Automation, ale nie może wyświetlić ich definicji. Może uruchamiać, zatrzymywać, wstrzymywać, wznawiać lub planować zadania elementu runbook. Użytkownik nie ma dostępu do innych zasobów usługi Automation, takich jak konfiguracje, grupy hybrydowych procesów roboczych lub węzły DSC.

![Brak dostępu do zasobów](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Konfigurowanie kontroli RBAC dla elementów Runbook

Azure Automation umożliwia przypisanie kontroli RBAC do określonych elementów Runbook. Aby to zrobić, uruchom następujący skrypt, aby dodać użytkownika do określonego elementu Runbook. Następujący skrypt może być uruchamiany przez administratora konta usługi Automation lub administratora dzierżawy.

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

Po uruchomieniu programu użytkownik może zalogować się do Azure Portal i wyświetlić **wszystkie zasoby**. Na liście zobaczy element Runbook, który został dodany jako **operator elementu Runbook usługi Automation** .

![Kontrola RBAC elementu Runbook w portalu](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Środowisko użytkownika dla roli operatora automatyzacji — element Runbook

Gdy użytkownik, który jest przypisany do roli operatora usługi w zakresie elementu Runbook, wyświetli element Runbook, do którego jest przypisany, może tylko uruchomić element Runbook i wyświetlić zadania elementu Runbook.

![Tylko ma dostęp do uruchamiania](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Następne kroki

* Więcej informacji dotyczących różnych sposobów konfigurowania funkcji RBAC w usłudze Azure Automation można znaleźć w artykule [Manage RBAC with Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) (Zarządzanie funkcją RBAC przy użyciu programu Azure PowerShell).
* Szczegółowe informacje dotyczące różnych sposobów uruchamiania elementu Runbook można znaleźć w artykule [Uruchamianie elementu Runbook](automation-starting-a-runbook.md).
* Informacje dotyczące różnych typów elementów Runbook można znaleźć w artykule [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook w usłudze Azure Automation)

