---
title: Kontrola dostępu oparta na rolach w usłudze Azure Automation
description: Funkcja kontroli dostępu opartej na rolach (role-based access control, RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure. Ten artykuł zawiera opis sposobu konfigurowania funkcji RBAC w usłudze Azure Automation.
keywords: automation rbac, kontrola dostępu oparta na rolach, azure rbac
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: c9cdebd2fb7f650b042fa04f345ac440e0b41cb8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="role-based-access-control-in-azure-automation"></a>Kontrola dostępu oparta na rolach w usłudze Azure Automation

Funkcja kontroli dostępu opartej na rolach (role-based access control, RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure. Przy użyciu [RBAC](../role-based-access-control/role-assignments-portal.md), możesz rozdzielenie obowiązków w obrębie organizacji i udzielić tylko takiego dostępu użytkowników, grup i aplikacji potrzebnych im do wykonywania swoich zadań. Prawo dostępu oparte na rolach może zostać przydzielone użytkownikom za pomocą witryny Azure Portal, narzędzi wiersza polecenia platformy Azure lub interfejsów API zarządzania platformy Azure.

## <a name="roles-in-automation-accounts"></a>Role w ramach kont automatyzacji
W usłudze Azure Automation prawo dostępu jest nadawane poprzez przypisywanie użytkownikom, grupom i aplikacjom odpowiednich ról RBAC w zakresie konta usługi. Poniżej przedstawiono wbudowane role obsługiwane przez konto automatyzacji:

| **Rola** | **Opis** |
|:--- |:--- |
| Właściciel |Rola właściciela zezwala na dostęp do wszystkich zasobów i akcji w ramach konta automatyzacji tym zapewnianie dostępu do innych użytkowników, grup i aplikacji Zarządzanie kontem automatyzacji. |
| Współautor |Rola Współautor umożliwia zarządzanie wszystkim, z wyjątkiem modyfikowania uprawnień dostępu innych użytkowników do konta usługi Automation. |
| Czytelnik |Rola Czytelnik służy do wyświetlania wszystkich zasobów w ramach konta usługi Automation, ale nie pozwala na wprowadzanie żadnych zmian. |
| Operator usługi |Rola Operator usługi Automation umożliwia wykonywanie zadań operacyjnych, takich jak uruchamianie, zatrzymywanie, wstrzymywanie, wznawianie i planowanie zadań. Ta rola jest przydatna, jeśli zasoby konta usługi Automation, takie jak zasoby poświadczeń i inne elementy Runbook, mają być chronione przed możliwością wyświetlenia lub modyfikowania, ale członkowie organizacji mają mieć możliwość wykonywania tych elementów Runbook. |
|Operator zadań usługi Automation|Rola operatora zadanie usługi Automatyzacja umożliwia tworzyć zadania i zarządzać nimi za pomocą elementów runbook automatyzacji.|
|Operator elementów runbook usługi Automation|Rola operatora elementu Runbook usługi Automatyzacja umożliwia odczytanie właściwości elementu runbook. Jest również możliwe tworzenie zadań elementu runbook.|
| Współautor usługi Log Analytics | Rola współautora analizy dziennika umożliwia odczytanie wszystkich danych monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia maszyny Wirtualnej do maszyn wirtualnych, odczytywanie klucze konta magazynu, aby można było skonfigurować kolekcję dzienników z usługi Azure storage, tworzenia i konfigurowania kont automatyzacji, Dodawanie rozwiązania i konfigurowania diagnostyki Azure w wszystkie zasoby platformy Azure.|
| Czytelnik usługi Log Analytics | Rolę czytelnika analizy dziennika umożliwia wyświetlanie i wyszukiwanie wszystkich monitorowania danych jak widok ustawienia monitorowania. Dotyczy to również wyświetlanie konfiguracji diagnostyki Azure wszystkich zasobów platformy Azure. |
| Współautor monitorowania | Rola współautora monitorowania umożliwia odczytanie wszystkich danych monitorowania i aktualizacji ustawienia monitorowania.|
| Czytelnik monitorowania | Rolę czytelnika Montioring umożliwia odczytanie wszystkich danych monitorowania. |
| Administrator dostępu użytkowników |Rola Administrator dostępu użytkowników umożliwia zarządzanie dostępem użytkowników do kont usługi Azure Automation. |

## <a name="role-permissions"></a>Uprawnienia roli

W poniższych tabelach opisano określone uprawnienia do poszczególnych ról. Może to obejmować akcje, które należy udzielić uprawnień, i NotActions, które je ograniczyć.

### <a name="owner"></a>Właściciel

Właściciel mogą zarządzać wszystkim łącznie z dostępem. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|Akcje|Opis|
|---|---|
|Microsoft.Automation/automationAccounts/|Utwórz i Zarządzaj zasoby wszystkich typów.|

### <a name="contributor"></a>Współautor

Współautor mogą zarządzać wszystkim poza dostępem. W poniższej tabeli przedstawiono uprawnienia przyznane i odmowy dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Tworzenie i zarządzanie zasobami wszystkich typów|
|**Akcje nie**||
|Microsoft.Authorization/*/Delete| Usuń role i przypisań ról.       |
|Microsoft.Authorization/*/Write     |  Tworzenie ról i przypisań ról.       |
|Microsoft.Authorization/elevateAccess/Action    | Nie zezwala na możliwość tworzenia Administrator dostępu użytkowników.       |

### <a name="reader"></a>Czytelnik

Czytnik mogą wyświetlać wszystkie zasoby w konto usługi Automatyzacja, ale nie można wprowadzać żadnych zmian.

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Wyświetlanie wszystkich zasobów na koncie automatyzacji. |

### <a name="automation-job-operator"></a>Operator zadań usługi Automation

Uzyskuje Operator zadanie usługi Automatyzacja w zakresie konta automatyzacji. Dzięki temu uprawnienia operatora zarządzać zadaniami w ramach konta.

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Przeczytaj autoryzacji.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista zadań elementu runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Wznów zadanie, które zostało wstrzymane.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Anulowanie zadania w toku.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Przeczytaj strumieni zadania i dane wyjściowe.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Wstrzymaj zadanie w toku.|
|Microsoft.Automation/automationAccounts/jobs/write|Tworzenie zadań.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Odczytywanie ról i przypisań ról.       |
|Microsoft.Resources/deployments/*      |Utwórz i Zarządzaj wdrożenia grupy zasobów.         |
|Microsoft.Insights/alertRules/*      | Utwórz i Zarządzaj reguł alertów.        |
|Microsoft.Support/* |Tworzenie i zarządzanie biletami pomocy technicznej.|

### <a name="automation-runbook-operator"></a>Operator elementów runbook usługi Automation

Rola operatora elementu Runbook automatyzacji udzielany jest w zakresie elementu Runbook. Operator elementów Runbook automatyzacji wyświetlana nazwa elementu runbook. To uprawnienie połączeniu z "Automatyzacji zadań Operator" w zakresie konta automatyzacji, umożliwia operatorowi akcje Operator automatyzacji dla określonego elementu runbook. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Lista elementów runbook.        |
|Microsoft.Authorization/*/read      | Przeczytaj autoryzacji.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Odczytywanie ról i przypisań ról.         |
|Microsoft.Resources/deployments/*      | Utwórz i Zarządzaj wdrożenia grupy zasobów.         |
|Microsoft.Insights/alertRules/*      | Utwórz i Zarządzaj reguł alertów.        |
|Microsoft.Support/*      | Tworzenie i zarządzanie biletami pomocy technicznej.        |

### <a name="automation-operator"></a>Operator usługi

Operator automatyzacji jest w stanie uruchamianie, zatrzymywanie, wstrzymywanie i wznawianie prac. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Przeczytaj autoryzacji.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista zadań elementu runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Wznów zadanie, które zostało wstrzymane.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Anulowanie zadania w toku.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Przeczytaj strumieni zadania i dane wyjściowe.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Wstrzymaj zadanie w toku.|
|Microsoft.Automation/automationAccounts/jobs/write|Tworzenie zadań.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Odczytywanie ról i przypisań ról.         |
|Microsoft.Resources/deployments/*      |Utwórz i Zarządzaj wdrożenia grupy zasobów.         |
|Microsoft.Insights/alertRules/*      | Utwórz i Zarządzaj reguł alertów.        |
|Microsoft.Support/* |Tworzenie i zarządzanie biletami pomocy technicznej.|

### <a name="log-analytics-contributor"></a>Współautor usługi Log Analytics

Współautor analizy dziennika można odczytać wszystkich danych monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia maszyny Wirtualnej do maszyn wirtualnych; Odczyt kluczy konta magazynu, aby można było skonfigurować kolekcję dzienników z usługi Magazyn Azure; Tworzenie i konfigurowanie kont automatyzacji; Dodawanie rozwiązań. i konfigurowania diagnostyki Azure dla wszystkich zasobów systemu Azure. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|* / Odczyt|Przeczytaj zasoby wszystkich typów, z wyjątkiem kluczy tajnych.|
|Microsoft.Automation/automationAccounts/*|Zarządzanie kontami automatyzacji.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Utwórz i Zarządzaj rozszerzenia maszyny wirtualnej.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Wyświetl klucze konta magazynu klasycznego.|
|Microsoft.Compute/virtualMachines/extensions/*|Utwórz i Zarządzaj rozszerzeniami klasyczne maszyny wirtualnej.|
|Microsoft.Insights/alertRules/*|Odczyt/zapis/usuwania reguł alertów.|
|Microsoft.Insights/diagnosticSettings/*|Ustawienia diagnostyki odczytu/zapisu/usuwania.|
|Microsoft.OperationalInsights/*|Zarządzanie analizy dzienników.|
|Microsoft.OperationsManagement/*|Zarządzaj rozwiązaniami w obszarach roboczych.|
|Microsoft.Resources/deployments/*|Utwórz i Zarządzaj wdrożenia grupy zasobów.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Utwórz i Zarządzaj wdrożenia grupy zasobów.|
|Microsoft.Storage/storageAccounts/listKeys/action|Wyświetl klucze konta magazynu.|
|Microsoft.Support/*|Tworzenie i zarządzanie biletami pomocy technicznej.|


### <a name="log-analytics-reader"></a>Czytelnik usługi Log Analytics

Czytnik dziennika Analytics można wyświetlać i wyszukiwanie wszystkich danych monitorowania i widoku monitorowania ustawień, takich jak wyświetlanie konfiguracji diagnostyki Azure wszystkich zasobów platformy Azure. W poniższej tabeli przedstawiono uprawnienia udzielono lub odmówiono dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|* / Odczyt|Przeczytaj zasoby wszystkich typów, z wyjątkiem kluczy tajnych.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Zarządzanie zapytaniami w programie analizy dzienników.|
|Microsoft.OperationalInsights/workspaces/search/action|Wyszukiwanie danych analizy dzienników.|
|Microsoft.Support/*|Tworzenie i zarządzanie biletami pomocy technicznej.|
|**Akcje nie**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Nie można odczytać klawiszy dostępu współdzielonego.|

### <a name="monitoring-contributor"></a>Współautor monitorowania

Współautor monitorowania można odczytać wszystkich danych monitorowania i aktualizowanie ustawień monitorowania. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|* / Odczyt|Przeczytaj zasoby wszystkich typów, z wyjątkiem kluczy tajnych.|
|Microsoft.AlertsManagement/alerts/*|Zarządzanie alertami.|
|Microsoft.AlertsManagement/alertsSummary/*|Zarządzanie alertu pulpitu nawigacyjnego.|
|Microsoft.Insights/AlertRules/*|Zarządzaj regułami alertów.|
|Microsoft.Insights/components/*|Zarządzanie składnikami usługi Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Zarządzaj ustawieniami diagnostycznych.|
|Microsoft.Insights/eventtypes/*|Wyświetl zdarzenia dziennika aktywności (zdarzeń zarządzania) w ramach subskrypcji. To uprawnienie jest dotyczy zarówno programowe, jak i portalu dostęp do dziennika aktywności.|
|Microsoft.Insights/LogDefinitions/*|To uprawnienie jest wymagane dla użytkowników, którzy potrzebują dostępu do dzienników aktywności w portalu. Lista kategorii dziennika w dzienniku aktywności.|
|Microsoft.Insights/MetricDefinitions/*|Odczytaj definicje metryki (listy dostępnych typów metryki dla zasobu).|
|Microsoft.Insights/Metrics/*|Odczytać metryki dla zasobu.|
|Microsoft.Insights/Register/Action|Zarejestruj dostawcę w elemencie Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Zarządzanie testy sieci web usługi Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Zarządzanie pakietami rozwiązania analizy dzienników.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Zarządzaj zapisanymi wyszukiwaniami analizy dzienników.|
|Microsoft.OperationalInsights/workspaces/search/action|Wyszukaj analizy dzienników obszarów roboczych.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Lista kluczy do obszaru roboczego analizy dzienników.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Zarządzanie konfiguracjami wglądu magazynu analizy dzienników.|
|Microsoft.Support/*|Tworzenie i zarządzanie biletami pomocy technicznej.|
|Microsoft.WorkloadMonitor/workloads/*|Zarządzanie zadaniami.|

### <a name="monitoring-reader"></a>Czytelnik monitorowania

Czytnik monitorowania może czytać wszystkie dane monitorowania. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|* / Odczyt|Przeczytaj zasoby wszystkich typów, z wyjątkiem kluczy tajnych.|
|Microsoft.OperationalInsights/workspaces/search/action|Wyszukaj analizy dzienników obszarów roboczych.|
|Microsoft.Support/*|Tworzenie i zarządzanie biletami pomocy technicznej|

### <a name="user-access-administrator"></a>Administrator dostępu użytkowników

Administrator dostępu użytkowników można zarządzać dostępem użytkowników do zasobów platformy Azure. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|* / Odczyt|Wszystkie zasoby do odczytu|
|Microsoft.Authorization/*|Zarządzanie autoryzacji|
|Microsoft.Support/*|Tworzenie i zarządzanie biletami pomocy technicznej|

## <a name="onboarding"></a>Dołączanie

Poniższe tabele Pokaż minimalne wymagane uprawnienia potrzebne do dołączenia do maszyn wirtualnych do śledzenia zmian lub zaktualizować rozwiązania do zarządzania.

### <a name="onboarding-from-a-virtual-machine"></a>Dołączania z maszyny wirtualnej

|**Akcja**  |**Uprawnienia**  |**Minimalna zakresu**  |
|---------|---------|---------|
|Pisanie nowego wdrożenia      | Microsoft.Resources/deployments/*          |Subskrypcja          |
|Zapisać nową grupę zasobów      | Microsoft.Resources/subscriptions/resourceGroups/write        | Subskrypcja          |
|Tworzenie nowego domyślnego obszaru roboczego      | Microsoft.OperationalInsights/workspaces/write         | Grupa zasobów         |
|Utwórz nowe konto      |  Microsoft.Automation/automationAccounts/write        |Grupa zasobów         |
|Obszar roboczy łącza i konta      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Obszar roboczy</br>Konto usługi Automation
|Utwórz rozwiązanie      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Grupa zasobów          |
|Tworzenie rozszerzenia MMA      | Microsoft.Compute/virtualMachines/write         | Maszyna wirtualna         |
|Utwórz zapisanego wyszukiwania      | Microsoft.OperationalInsights/workspaces/write          | Obszar roboczy         |
|Tworzenie konfiguracji zakresu      | Microsoft.OperationalInsights/workspaces/write          | Obszar roboczy         |
|Rozwiązanie łącze do konfiguracji zakresu      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Rozwiązanie         |
|Sprawdź stan dołączania - odczytu obszaru roboczego      | Microsoft.OperationalInsights/workspaces/read         | Obszar roboczy         |
|Sprawdź stan dołączania - odczytu połączone właściwości obszaru roboczego konta     | Microsoft.Automation/automationAccounts/read      | Konto usługi Automation        |
|Sprawdź stan dołączania - odczytu rozwiązania      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Rozwiązanie         |
|Sprawdzanie stanu przechodzenia do - odczytu maszyny Wirtualnej      | Microsoft.Compute/virtualMachines/read         | Maszyna wirtualna         |
|Sprawdź stan dołączania - Odczyt Konto      | Microsoft.Automation/automationAccounts/read  |  Konto usługi Automation   |

### <a name="onboarding-from-automation-account"></a>Dołączania z konta automatyzacji

|**Akcja**  |**Uprawnienia** |**Minimalna zakresu**  |
|---------|---------|---------|
|Tworzenie nowego wdrożenia     | Microsoft.Resources/deployments/*        | Subskrypcja         |
|Utwórz nową grupę zasobów     | Microsoft.Resources/subscriptions/resourceGroups/write         | Subskrypcja        |
|Blok AutomationOnboarding — Utwórz nowy obszar roboczy     |Microsoft.OperationalInsights/workspaces/write           | Grupa zasobów        |
|Blok AutomationOnboarding — połączonego obszaru roboczego do odczytu     | Microsoft.Automation/automationAccounts/read        | Konto usługi Automation       |
|Blok AutomationOnboarding — rozwiązanie do odczytu     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Rozwiązanie        |
|Blok AutomationOnboarding — obszaru roboczego do odczytu     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Obszar roboczy        |
|Utwórz łącze do obszaru roboczego i konta     | Microsoft.OperationalInsights/workspaces/write        | Obszar roboczy        |
|Konto shoebox zapisu      | Microsoft.Automation/automationAccounts/write        | Konto        |
|Utwórz rozwiązanie      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Grupa zasobów         |
|Utwórz/Edytuj zapisane wyszukiwania     | Microsoft.OperationalInsights/workspaces/write        | Obszar roboczy        |
|Utwórz/Edytuj zakres konfiguracji     | Microsoft.OperationalInsights/workspaces/write        | Obszar roboczy        |
|Rozwiązanie łącze do konfiguracji zakresu      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Rozwiązanie         |
|**Krok 2 - dołączyć wiele maszyn wirtualnych**     |         |         |
|Blok VMOnboarding — tworzenie MMA rozszerzenia     | Microsoft.Compute/virtualMachines/write           | Maszyna wirtualna        |
|Utwórz / Edytuj zapisanego wyszukiwania     | Microsoft.OperationalInsights/workspaces/write           | Obszar roboczy        |
|Utwórz / Edytuj zakres konfiguracji  | Microsoft.OperationalInsights/workspaces/write   | Obszar roboczy|

## <a name="update-management"></a>Zarządzanie aktualizacjami

Zarządzanie aktualizacjami osiągnie w wielu usługach do jego obsługi. W poniższej tabeli przedstawiono uprawnienia potrzebne do zarządzania wdrożeniami zarządzania aktualizacji:

|**Zasób**  |**Rola**  |**Zakres**  |
|---------|---------|---------|
|Konto usługi Automation     | Współautor usługi Log Analytics       | Konto usługi Automation        |
|Konto usługi Automation    | Współautor maszyny wirtualnej        | Grupa zasobów dla konta        |
|Obszar roboczy usługi Log Analytics     | Współautor usługi Log Analytics| Obszar roboczy usługi Log Analytics        |
|Obszar roboczy usługi Log Analytics |Czytelnik usługi Log Analytics| Subskrypcja|
|Rozwiązanie     |Współautor usługi Log Analytics         | Rozwiązanie|
|Maszyna wirtualna     | Współautor maszyny wirtualnej        | Maszyna wirtualna        |

## <a name="configure-rbac-for-your-automation-account-using-azure-portal"></a>Konfigurowanie RBAC dla konta automatyzacji za pomocą portalu Azure
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) i otwórz swoje konto usługi Automation ze strony Konta usługi Automation.  
2. Polecenie **(IAM) kontroli dostępu** kontroli w lewym górnym rogu. Spowoduje to otwarcie **(IAM) kontroli dostępu** strony, gdzie można dodać nowych użytkowników, grup i aplikacji do zarządzania z automatyzacji konta i wyświetlić istniejących ról, które można skonfigurować dla konta automatyzacji.
   
   ![Przycisk Dostęp](media/automation-role-based-access-control/automation-01-access-button.png)  

### <a name="add-a-new-user-and-assign-a-role"></a>Dodawanie nowego użytkownika i przypisywanie roli
1. Z **(IAM) kontroli dostępu** kliknij przycisk **+ Dodaj** otworzyć **dodać uprawnienia** strony, w którym można dodać użytkownika, grupy lub aplikacji i przypisać rolę do nich.  

2. Wybierz rolę z listy dostępnych ról. Można wybrać jedną z dostępnych ról wbudowane konto usługi Automatyzacja obsługiwanych przez lub dowolnej roli niestandardowych, które zostały zdefiniowane.

3. Wpisz nazwy użytkownika użytkownika chcesz nadać kontu uprawnienia w **wybierz** pola. Wybierz użytkownika z listy i kliknij przycisk **zapisać**.
   
   ![Dodawanie użytkowników](media/automation-role-based-access-control/automation-04-add-users.png)  
   
   Teraz powinien zostać wyświetlony użytkownik dodany do **użytkowników** strony z wybraną rolą przypisane.  
   
   ![Wyświetlanie użytkowników](media/automation-role-based-access-control/automation-05-list-users.png)  
   
   Możesz także przypisać rolę użytkownikowi na stronie **Role**. 
4. Kliknij przycisk **ról** z **(IAM) kontroli dostępu** strony, aby otworzyć **ról** strony. Na tej stronie możesz wyświetlić nazwę roli oraz liczbę użytkowników i grup przypisanych do tej roli.
   
    ![Przypisywanie roli na stronie użytkowników](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
   > [!NOTE]
   > Kontrola dostępu oparta na rolach można ustawić tylko w zakresie konta automatyzacji, a nie w dowolnym zasobem poniżej konta automatyzacji.

### <a name="remove-a-user"></a>Usuwanie użytkownika
Można usunąć uprawnienia dostępu dla użytkownika, który nie obsługuje konta automatyzacji lub który nie działa dla organizacji. Poniżej przedstawiono kroki prowadzące do usunięcia użytkownika: 

1. Z **(IAM) kontroli dostępu** użytkownika chcesz usunąć, a następnie kliknij przycisk Wybierz **Usuń**.
2. Kliknij przycisk **Usuń** w okienku szczegółów przypisania.
3. Kliknij przycisk **Tak**, aby potwierdzić usunięcie.

   ![Usuwanie użytkowników](media/automation-role-based-access-control/automation-08-remove-users.png)

## <a name="role-assigned-user"></a>Rola przypisana do użytkownika

Gdy użytkownik przypisany do roli loguje się do platformy Azure i wybiera swojego konta automatyzacji, będą teraz widoczne konta właściciela wymienione na liście **katalogów**. Aby wyświetlić konto usługi Automation, do którego został dodany, musi przełączyć katalog domyślny na katalog właściciela.

### <a name="user-experience-for-automation-operator-role"></a>Środowisko użytkownika dla roli operatora usługi
Gdy użytkownik, który jest przypisany do widoków roli operatora automatyzacji konto automatyzacji, które są przypisane, można tylko wyświetlić listę elementów runbook, zadania elementów runbook i harmonogramy utworzone w ramach konta automatyzacji, ale nie można wyświetlić ich definicji. Może uruchamiać, zatrzymywać, wstrzymywać, wznawiać lub planować zadania elementu runbook. Użytkownik nie ma dostępu do innych zasobów automatyzacji, takie jak konfiguracje, hybrydowego procesu roboczego grupy lub węzłów DSC.

![Brak dostępu do zasobów](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

Użytkownik ma dostęp do wyświetlania i tworzenia harmonogramów, ale nie ma dostępu do jakichkolwiek innych typów zasobów.

Użytkownik nie ma także dostępu do funkcji wyświetlania elementów webhook skojarzonych z elementem Runbook

![Brak dostępu do elementów webhook](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## <a name="configure-rbac-for-your-automation-account-using-azure-powershell"></a>Konfigurowanie RBAC dla konta automatyzacji za pomocą programu Azure PowerShell
Można również skonfigurować opartej na rolach dostępu do konta automatyzacji za pomocą następujących [poleceń cmdlet programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) wyświetla wszystkie role RBAC, które są dostępne w usłudze Azure Active Directory. Można użyć tego polecenia z właściwością **Nazwa**, aby wyświetlić listę wszystkich akcji, które mogą być wykonywane przez określoną rolę.

```powershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Poniżej przedstawiono przykładowe dane wyjściowe:

```powershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action, 
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
``` 

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) wyświetla przypisania ról RBAC usługi Azure AD w podanym zakresie. Bez żadnych parametrów to polecenie zwraca wszystkie przypisania ról dokonane w ramach subskrypcji. Parametr **ExpandPrincipalGroups** powoduje wyświetlenie listy przypisań dla określonego użytkownika oraz grup, których członkiem jest użytkownik.  
    **Przykład:** użyj poniższego polecenia, aby wyświetlić listę wszystkich użytkowników i ich ról w ramach konta usługi Automation.

```powershell-interactive
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

• [AzureRmRoleAssignment nowy](https://msdn.microsoft.com/library/mt603580.aspx) udzielania dostępu do użytkowników, grup i aplikacji do określonego zakresu.  
    **Przykład:** Użyj następującego polecenia, aby przypisać rolę "Automatyzacji operatora" dla użytkownika w zakresie konta automatyzacji.

```powershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Poniżej przedstawiono przykładowe dane wyjściowe:

```powershell
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

• Użyj [AzureRmRoleAssignment Usuń](https://msdn.microsoft.com/library/mt603781.aspx) usunięcie dostępu do określonego użytkownika, grupy lub aplikacji z określonego zakresu.  
    **Przykład:** Użyj następującego polecenia, aby usunąć użytkownika z roli "Automatyzacji operatora" w zakresie konta automatyzacji.

```powershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

W powyższych przykładach, Zastąp **zaloguj identyfikator**, **identyfikator subskrypcji**, **Nazwa grupy zasobów**, i **nazwa konta automatyzacji** z sieci Szczegóły konta. Gdy zostanie wyświetlony monit o potwierdzenie usunięcia przypisania roli do użytkownika, wybierz pozycję **Tak**.   

## <a name="next-steps"></a>Kolejne kroki
* Więcej informacji dotyczących różnych sposobów konfigurowania funkcji RBAC w usłudze Azure Automation można znaleźć w artykule [Manage RBAC with Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) (Zarządzanie funkcją RBAC przy użyciu programu Azure PowerShell).
* Szczegółowe informacje dotyczące różnych sposobów uruchamiania elementu Runbook można znaleźć w artykule [Uruchamianie elementu Runbook](automation-starting-a-runbook.md).
* Informacje dotyczące różnych typów elementów Runbook można znaleźć w artykule [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook w usłudze Azure Automation)

