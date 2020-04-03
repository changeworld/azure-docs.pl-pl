---
title: Uruchamianie/zatrzymywania maszyn wirtualnych poza godzinami pracy
description: To rozwiązanie do zarządzania maszynami wirtualnymi uruchamia i zatrzymuje maszyny wirtualne platformy Azure zgodnie z harmonogramem i proaktywnie monitoruje dzienniki usługi Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 906c7728365cc902549bd46c57972e1c90af979c
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607475"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Rozwiązanie umożliwiające uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu w usłudze Azure Automation

Maszyny wirtualne Start/Stop podczas uruchamiania rozwiązania poza godzinami pracy i zatrzymuje maszyny wirtualne platformy Azure zgodnie z harmonogramami zdefiniowanymi przez użytkownika, udostępnia szczegółowe informacje za pośrednictwem dzienników usługi Azure Monitor i wysyła opcjonalne wiadomości e-mail przy użyciu [grup akcji.](../azure-monitor/platform/action-groups.md) Obsługuje zarówno usługi Azure Resource Manager, jak i klasyczne maszyny wirtualne dla większości scenariuszy. 

Aby użyć tego rozwiązania z klasycznymi maszynami wirtualnymi, potrzebujesz klasycznego konta RunAs, które nie jest tworzone domyślnie. Aby uzyskać instrukcje dotyczące tworzenia klasycznego konta RunAs, zobacz [Klasyczne konta uruchamiane jako](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> Maszyny wirtualne Start/Stop podczas poza godzinami pracy zostały zaktualizowane w celu obsługi najnowszych wersji modułów platformy Azure, które są dostępne.

To rozwiązanie zapewnia zdecentralizowaną opcję automatyzacji o niskich kosztach dla użytkowników, którzy chcą zoptymalizować swoje koszty maszyny Wirtualnej. Dzięki temu rozwiązaniu możesz:

- [Zaplanuj uruchamianie i zatrzymywać maszyny wirtualne](automation-solution-vm-management-config.md#schedule).
- Zaplanuj maszyny wirtualne, aby rozpocząć i zatrzymać w kolejności rosnącej [przy użyciu tagów platformy Azure](automation-solution-vm-management-config.md#tags) (nie są obsługiwane dla klasycznych maszyn wirtualnych).
- Autostop maszyn wirtualnych w oparciu o [niskie zużycie procesora](automation-solution-vm-management-config.md#cpuutil).

Poniżej przedstawiono ograniczenia dotyczące bieżącego rozwiązania:

- To rozwiązanie zarządza maszynami wirtualnymi w dowolnym regionie, ale może być używane tylko w tej samej subskrypcji co konto usługi Azure Automation.
- To rozwiązanie jest dostępne na platformie Azure i azure government dla dowolnego regionu, który obsługuje obszar roboczy usługi Log Analytics, konto usługi Azure Automation i alerty. Regiony platformy Azure dla instytucji rządowych obecnie nie obsługują funkcji poczty e-mail.

> [!NOTE]
> Jeśli używasz rozwiązania dla klasycznych maszyn wirtualnych, wszystkie maszyny wirtualne będą przetwarzane sekwencyjnie na usługę w chmurze. Maszyny wirtualne są nadal przetwarzane równolegle w różnych usługach w chmurze. Jeśli masz więcej niż 20 maszyn wirtualnych na usługę w chmurze, zaleca się utworzenie wielu harmonogramów z **nadrzędnym elementem** runbook ScheduledStartStop_Parent i określić 20 maszyn wirtualnych na harmonogram. We właściwościach harmonogramu określ jako listę oddzieloną przecinkami nazwy maszyn wirtualnych w parametrze **VMList.** W przeciwnym razie jeśli zadanie automatyzacji dla tego rozwiązania działa dłużej niż trzy godziny, jest tymczasowo zwalniane lub zatrzymywane zgodnie z limitem [sprawiedliwego udziału.](automation-runbook-execution.md#fair-share)
>
> Azure Cloud Solution Provider (Azure CSP) subscriptions support only the Azure Resource Manager model, non-Azure Resource Manager services are not available in the program. Po uruchomieniu rozwiązania Start/Stop mogą pojawić się błędy, ponieważ ma polecenia cmdlet do zarządzania zasobami klasycznymi. Aby dowiedzieć się więcej o CSP, zobacz [Dostępne usługi w subskrypcjach CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Jeśli używasz subskrypcji CSP, należy zmodyfikować zmienną [**External_EnableClassicVMs**](#variables) do **False** po wdrożeniu.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Podręczniki dla tego rozwiązania działają z [kontem Azure Run As](automation-create-runas-account.md). Konto Uruchom jako jest preferowaną metodą uwierzytelniania, ponieważ używa uwierzytelniania certyfikatu zamiast hasła, które może często wygasać lub zmieniać.

Zaleca się użycie oddzielnego konta automatyzacji dla rozwiązania Start/Stop VM. Dzieje się tak, ponieważ wersje modułów platformy Azure są często uaktualniane, a ich parametry mogą ulec zmianie. Rozwiązanie start/stop maszyny wirtualnej nie jest uaktualniane w tym samym rytmie, więc może nie działać z nowszymi wersjami poleceń cmdlet, których używa. Przed zaimportowanie ich na konta automatyzacji w produkcji zalecamy również przetestowanie aktualizacji modułu na koncie automatyzacji testowego.

### <a name="permissions"></a>Uprawnienia

Istnieją pewne uprawnienia, które użytkownik musi mieć do wdrożenia start/stop maszyn wirtualnych w godzinach pracy rozwiązania. Uprawnienia te są różne, jeśli przy użyciu wstępnie utworzonego konta automatyzacji i obszaru roboczego usługi Log Analytics lub tworzenia nowych podczas wdrażania. Jeśli jesteś współautorem subskrypcji i administratorem globalnym w dzierżawie usługi Azure Active Directory, nie musisz konfigurować następujących uprawnień. Jeśli nie masz tych praw lub musisz skonfigurować rolę niestandardową, zobacz uprawnienia wymagane poniżej.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Istniejący wcześniej obszar roboczy Kont automatyzacji i usługi Log Analytics

Aby wdrożyć rozwiązanie Start/Stop VMs poza godzinami pracy w istniejącym obszarze roboczym Konto automatyzacji i usługa Log Analytics, użytkownik wdrażający rozwiązanie wymaga następujących uprawnień w **grupie zasobów**. Aby dowiedzieć się więcej o rolach, zobacz [Role niestandardowe dla zasobów platformy Azure](../role-based-access-control/custom-roles.md).

| Uprawnienie | Zakres|
| --- | --- |
| Automatyzacja/automatyzacja Microsoft.AutomationKonta/odczyt | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/variables/write | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/schedules/write | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/runbooks/write | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationKonta/połączenia/zapis | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/certificates/write | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/modules/write | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationKonta/moduły/odczyt | Grupa zasobów |
| Microsoft.automation/automationKonta/zadaniaSześci/zapis | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/jobs/write | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/jobs/read | Grupa zasobów |
| Microsoft.OperationsManagement/solutions/write | Grupa zasobów |
| Microsoft.OperationalInsights/obszary robocze/* | Grupa zasobów |
| Microsoft.Insights/diagnosticSettings/write | Grupa zasobów |
| Microsoft.Insights/ActionGroups/Write | Grupa zasobów |
| Microsoft.Insights/ActionGroups/read | Grupa zasobów |
| Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Grupa zasobów |
| Zasoby/wdrożenia firmy Microsoft/* | Grupa zasobów |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nowe konto automatyzacji i nowy obszar roboczy usługi Log Analytics

Aby wdrożyć rozwiązanie Start/Stop VMs poza godzinami pracy w nowym obszarze roboczym Konto automatyzacji i usługa Log Analytics, użytkownik wdrażający rozwiązanie potrzebuje uprawnień zdefiniowanych w poprzedniej sekcji, a także następujących uprawnień:

- Współadministrator w ramach subskrypcji — jest potrzebny tylko do utworzenia klasycznego konta uruchom jako konto, jeśli masz zamiar zarządzać klasycznymi maszynami wirtualnymi. [Klasyczne konta RunAs](automation-create-standalone-account.md#classic-run-as-accounts) nie są już domyślnie tworzone.
- Członek roli **dewelopera aplikacji** [usługi Azure Active Directory.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Aby uzyskać więcej informacji na temat konfigurowania funkcji Uruchom jako konta, zobacz [Uprawnienia do konfigurowania kont Uruchom jako](manage-runas-account.md#permissions).
- Współautor w ramach subskrypcji lub następujących uprawnień.

| Uprawnienie |Zakres|
| --- | --- |
| Microsoft.Autoryzacja/Operacje/odczyt | Subskrypcja|
| Microsoft.Autoryzacja/uprawnienia/odczyt |Subskrypcja|
| Microsoft.Authorization/roleAssignments/read | Subskrypcja |
| Microsoft.Authorization/roleAssignments/write | Subskrypcja |
| Microsoft.Authorization/roleAssignments/delete Microsoft.Authorization/roleAssignments/delete Microsoft.Authorization/roleAssignments/delete Microsoft. | Subskrypcja |
| Automatyzacja/automatyzacja Microsoft.AutomationKonta/połączenia/odczyt | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/certificates/read | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationKonta/zapis | Grupa zasobów |
| Microsoft.OperationalInsights/workspaces/write | Grupa zasobów |

## <a name="solution-components"></a>Składniki rozwiązania

To rozwiązanie obejmuje wstępnie skonfigurowane elementy runbook, harmonogramy i integrację z dziennikami usługi Azure Monitor, dzięki czemu można dostosować uruchamianie i zamykanie maszyn wirtualnych do potrzeb biznesowych.

### <a name="runbooks"></a>Elementy Runbook

W poniższej tabeli wymieniono elementy runbook wdrożone na koncie automatyzacji przez to rozwiązanie. Nie należy wprowadzać zmian w kodzie uruchomieniu. . Zamiast tego napisz własny podręcznik runbook dla nowych funkcji.

> [!IMPORTANT]
> Nie należy bezpośrednio uruchamiać żadnego umnienia z *nianią* z dołączonym do jego nazwy.

Wszystkie nadrzędne elementy runbook zawierają parametr _WhatIf._ Po ustawieniu **true**, _WhatIf_ obsługuje szczegółowo dokładne zachowanie runbook trwa podczas uruchamiania bez _WhatIf_ parametru i sprawdza poprawność poprawne maszyny wirtualne są kierowane. Obiekt runbook wykonuje tylko zdefiniowane akcje, gdy parametr _WhatIf_ jest ustawiony na **False**.

|Element Runbook | Parametry | Opis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject (100) <br> Powiadomienie <br> Identyfikator elementów WebHookURI | Wywoływana z nadrzędnego egonatu. Ten element runbook tworzy alerty na podstawie dla zasobu dla scenariusza Autostop.|
|AutoStop_CreateAlert_Parent | Lista maszyn wirtualnych<br> CoIf: Prawda lub Fałsz  | Tworzy lub aktualizuje reguły alertów platformy Azure na maszynach wirtualnych w grupach subskrypcji lub zasobów docelowych. <br> Lista maszyn wirtualnych: oddzielona przecinkami lista maszyn wirtualnych. Na przykład _vm1, vm2, vm3_.<br> *WhatIf* sprawdza poprawność logiki uruchomieniu bez wykonywania.|
|AutoStop_Disable | brak | Wyłącza alerty Autostop i domyślny harmonogram.|
|AutoStop_VM_Child | Element WebHookData | Wywoływana z nadrzędnego egonatu. Reguły alertów wywołać ten system runbook, aby zatrzymać klasycznej maszyny Wirtualnej.|
|AutoStop_VM_Child_ARM | Element WebHookData |Wywoływana z nadrzędnego egonatu. Reguły alertów wywołać ten projekt uruchamiany, aby zatrzymać maszynę wirtualną.  |
|ScheduledStartStop_Base_Classic | Nazwa usługi w chmurze<br> Działanie: Uruchamianie lub zatrzymywać<br> Lista maszyn wirtualnych  | Ten projekt runbook używany do wykonywania akcji start lub stop w klasycznej grupie maszyn wirtualnych przez usługi w chmurze.<br> Lista maszyn wirtualnych: oddzielona przecinkami lista maszyn wirtualnych. Na przykład _vm1, vm2, vm3_. |
|ScheduledStartStop_Child | VMName <br> Działanie: Uruchamianie lub zatrzymywać <br> ResourceGroupName | Wywoływana z nadrzędnego egonatu. Wykonuje akcję rozpoczęcia lub zatrzymania dla zaplanowanego zatrzymania.|
|ScheduledStartStop_Child_Classic | VMName<br> Działanie: Uruchamianie lub zatrzymywać<br> ResourceGroupName | Wywoływana z nadrzędnego egonatu. Wykonuje akcję uruchamiania lub zatrzymywania dla zaplanowanego zatrzymania dla klasycznych maszyn wirtualnych. |
|ScheduledStartStop_Parent | Działanie: Uruchamianie lub zatrzymywać <br>Lista maszyn wirtualnych <br> CoIf: Prawda lub Fałsz | To ustawienie ma wpływ na wszystkie maszyny wirtualne w subskrypcji. Edytuj **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames,** aby wykonywać tylko w tych docelowych grupach zasobów. Można również wykluczyć określonych maszyn wirtualnych, aktualizując **zmienną External_ExcludeVMNames.**<br> Lista maszyn wirtualnych: oddzielona przecinkami lista maszyn wirtualnych. Na przykład _vm1, vm2, vm3_.<br> _WhatIf_ sprawdza poprawność logiki uruchomieniu bez wykonywania.|
|SequencedStartStop_Parent | Działanie: Uruchamianie lub zatrzymywać <br> CoIf: Prawda lub Fałsz<br>Lista maszyn wirtualnych| Utwórz znaczniki o nazwie **sequencestart** i **sequencestop** na każdej maszynie wirtualnej, dla której chcesz sekwencjonować działanie start/stop. W tych nazwach tagów rozróżniana jest wielkość liter. Wartość znacznika powinna być dodatnią liczą całkowitą (1, 2, 3), która odpowiada kolejności, w jakiej chcesz rozpocząć lub zatrzymać. <br> Lista maszyn wirtualnych: oddzielona przecinkami lista maszyn wirtualnych. Na przykład _vm1, vm2, vm3_. <br> _WhatIf_ sprawdza poprawność logiki uruchomieniu bez wykonywania. <br> **Uwaga:** Maszyny wirtualne muszą znajdować się w grupach zasobów zdefiniowanych jako External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames i External_ExcludeVMNames w zmiennych usługi Azure Automation. Muszą mieć odpowiednie tagi, aby akcje zaczęły obowiązywać.|

### <a name="variables"></a>Zmienne

W poniższej tabeli wymieniono zmienne utworzone na koncie automatyzacji. Modyfikuj tylko zmienne poprzedzone **zewnętrznymi**. Modyfikowanie zmiennych poprzedzonych **wewnętrznymi** powoduje niepożądane skutki.

|Zmienna | Opis|
|---------|------------|
|External_AutoStop_Condition | Operator warunkowy wymagany do skonfigurowania warunku przed wyzwoleniem alertu. Dopuszczalne wartości to **GreaterThan**, **GreaterThanEqual**, **LessThan**i **LessThanEqual**.|
|External_AutoStop_Description | Alert, aby zatrzymać maszynę wirtualną, jeśli procent procesora CPU przekracza próg.|
|External_AutoStop_Frequency | Częstotliwość oceny dla reguły. Ten parametr akceptuje dane wejściowe w formacie timespan. Możliwe wartości to od 5 minut do 6 godzin. |
|External_AutoStop_MetricName | Nazwa metryki wydajności, dla której ma być skonfigurowana reguła usługi Azure Alert.|
|External_AutoStop_Severity | Ważność alertu metryki, który może wynosić od 0 do 4. |
|External_AutoStop_Threshold | Próg dla reguły usługi Azure Alert określony w _zmiennej External_AutoStop_MetricName_. Wartości procentowe mogą wynosić od 1 do 100.|
|External_AutoStop_TimeAggregationOperator | Operator agregacji czasu, który jest stosowany do wybranego rozmiaru okna w celu oceny warunku. Dopuszczalne wartości **to Średnia,** **Minimalna,** **Maksymalna,** **Całkowita**i **Ostatnia**.|
|External_AutoStop_TimeWindow | Rozmiar okna, podczas którego platforma Azure analizuje wybrane metryki do wyzwalania alertu. Ten parametr akceptuje dane wejściowe w formacie timespan. Możliwe wartości to od 5 minut do 6 godzin.|
|External_EnableClassicVMs| Określa, czy klasyczne maszyny wirtualne są kierowane przez rozwiązanie. Wartością domyślną jest True. To powinno być ustawione na False dla subskrypcji CSP. Klasyczne maszyny wirtualne wymagają [klasycznego konta run-as.](automation-create-standalone-account.md#classic-run-as-accounts)|
|External_ExcludeVMNames | Wprowadź nazwy maszyn wirtualnych, które mają zostać wykluczone, oddzielając nazwy za pomocą przecinka bez spacji. Jest to ograniczone do 140 maszyn wirtualnych. Jeśli dodasz więcej niż 140 maszyn wirtualnych do tej listy oddzielone przecinkami, maszyny wirtualne, które są ustawione do wykluczenia, mogą zostać przypadkowo uruchomione lub zatrzymane.|
|External_Start_ResourceGroupNames | Określa jedną lub więcej grup zasobów, oddzielając wartości za pomocą przecinka, przeznaczonych do akcji początkowych.|
|External_Stop_ResourceGroupNames | Określa jedną lub więcej grup zasobów, oddzielając wartości za pomocą przecinka, przeznaczonych dla akcji zatrzymania.|
|External_WaitTimeForVMRetrySeconds |Czas oczekiwania w sekundach dla akcji, które mają być wykonywane na maszynach wirtualnych dla sekwencjonowane start/stop runbook.<br> Wartość domyślna to 2100 sekund i obsługuje konfigurowanie do maksymalnej wartości 10800 lub trzy godziny.|
|Internal_AutomationAccountName | Określa nazwę konta automatyzacji.|
|Internal_AutoSnooze_ARM_WebhookURI | Określa identyfikator URI elementu webhook wywoływany dla scenariusza Autostop dla maszyn wirtualnych.|
|Internal_AutoSnooze_WebhookUri | Określa identyfikator URI elementu webhook wywoływany dla scenariusza Autostop dla klasycznych maszyn wirtualnych.|
|Internal_AzureSubscriptionId | Określa identyfikator subskrypcji platformy Azure.|
|Internal_ResourceGroupName | Określa nazwę grupy zasobów konta automatyzacji.|

>[!NOTE]
>Dla zmiennej **External_WaitTimeForVMRetryInSeconds**wartość domyślna została zaktualizowana z 600 do 2100. Ta zmienna umożliwia **sekwencyjny start/stop scenariusz** runbook czekać na operacje podrzędne dla określonej liczby sekund przed kontynuowaniem następnej akcji.
>

We wszystkich scenariuszach **do**kierowania na maszyny wirtualne niezbędne są zmienne External_Start_ResourceGroupNames **, External_Stop_ResourceGroupNames**i **External_ExcludeVMNames,** z wyjątkiem udostępnienia listy maszyn wirtualnych oddzielonych przecinkami dla **AutoStop_CreateAlert_Parent,** **SequencedStartStop_Parent**i **ScheduledStartStop_Parent** likemi. Oznacza to, że maszyny wirtualne muszą znajdować się w docelowych grupach zasobów, aby uruchamiać i zatrzymywać akcje. Logika działa podobnie do zasad platformy Azure, w tym można kierować subskrypcji lub grupy zasobów i akcje dziedziczone przez nowo utworzonych maszyn wirtualnych. Takie podejście pozwala uniknąć konieczności utrzymywania oddzielnego harmonogramu dla każdej maszyny Wirtualnej i zarządzania uruchomieniami i zatrzymaniami w skali.

### <a name="schedules"></a>Harmonogramy

W poniższej tabeli wymieniono każdy z domyślnych harmonogramów utworzonych na koncie automatyzacji.Można je zmodyfikować lub utworzyć własne harmonogramy niestandardowe.Domyślnie wszystkie harmonogramy są wyłączone z wyjątkiem **Scheduled_StartVM** i **Scheduled_StopVM**.

Nie należy włączać wszystkich harmonogramów, ponieważ może to spowodować nakładające się akcje harmonogramu. Najlepiej jest określić, które optymalizacje chcesz wykonać i odpowiednio zmodyfikować. Zobacz przykładowe scenariusze w sekcji przeglądowej, aby uzyskać dalsze wyjaśnienia.

|Nazwa harmonogramu | Częstotliwość | Opis|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Co 8 godzin | Uruchamia AutoStop_CreateAlert_Parent runbook co 8 godzin, co z kolei zatrzymuje wartości oparte na maszynie wirtualnej w External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames i External_ExcludeVMNames w zmiennych usługi Azure Automation. Alternatywnie można określić listę maszyn wirtualnych oddzieloną przecinkami przy użyciu parametru VMList.|
|Scheduled_StopVM | Zdefiniowane przez użytkownika, codziennie | Uruchamia Scheduled_Parent runbook z parametrem _Zatrzymaj_ codziennie o określonej godzinie.Automatycznie zatrzymuje wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez zmienne zasobów.Włącz powiązany harmonogram, **Scheduled-StartVM**.|
|Scheduled_StartVM | Zdefiniowane przez użytkownika, codziennie | Uruchamia Scheduled_Parent runbook z parametrem _Start_ codziennie o określonej godzinie. Automatycznie uruchamia wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez odpowiednie zmienne.Włącz powiązany harmonogram **Scheduled-StopVM**.|
|Sekwencyjny-StopVM | 1:00 (UTC), w każdy piątek | Uruchamia Sequenced_Parent runbook z parametrem _Stop_ w każdy piątek o określonej godzinie.Sekwencyjnie (rosnąco) zatrzymuje wszystkie maszyny wirtualne z tagiem **SequenceStop** zdefiniowane przez odpowiednie zmienne. Aby uzyskać więcej informacji na temat wartości znaczników i zmiennych zasobów, zobacz sekcję Księgi ekscjowe.Włącz powiązany **harmonogram, Sequenced-StartVM**.|
|Sekwencjonowane-StartVM | 13:00 (UTC), w każdy poniedziałek | Uruchamia Sequenced_Parent runbook z parametrem _Start_ w każdy poniedziałek o określonej godzinie. Sekwencyjnie (malejąco) uruchamia wszystkie maszyny wirtualne z tagiem **SequenceStart** zdefiniowane przez odpowiednie zmienne. Aby uzyskać więcej informacji na temat wartości znaczników i zmiennych zasobów, zobacz sekcję Księgi ekscjowe. Włącz powiązany **harmonogram, Sequenced-StopVM**.|

## <a name="enable-the-solution"></a>Włączanie rozwiązania

Aby rozpocząć korzystanie z rozwiązania, wykonaj kroki opisane w [rozwiązaniu Włącz maszyny wirtualne Start/Stop](automation-solution-vm-management-enable.md).

## <a name="viewing-the-solution"></a>Wyświetlanie rozwiązania

Dostęp do rozwiązania można uzyskać po włączeniu go z jednego z następujących sposobów:

* Na koncie automatyzacji wybierz **pozycję Start/Stop VM** w obszarze **Zasoby pokrewne**. Na stronie **Start/Stop VM** wybierz pozycję **Zarządzaj rozwiązaniem** z prawej strony, w sekcji **Zarządzanie rozwiązaniami maszyn wirtualnych Start/Stop**.

* Przejdź do obszaru roboczego usługi Log Analytics połączonego z kontem automatyzacji, a po wybraniu obszaru roboczego wybierz pozycję **Rozwiązania** z lewego okienka. Na stronie **Rozwiązania** wybierz z listy rozwiązanie **Start-Stop-Stop-VM[workspace].**  

Wybranie rozwiązania powoduje wyświetlenie strony rozwiązania **Start-Stop-Stop-VM[obszar roboczy].** W tym miejscu można przejrzeć ważne szczegóły, takie jak kafelek **StartStopVM.** Podobnie jak w obszarze roboczym usługi Log Analytics, ten kafelek wyświetla liczbę i graficzną reprezentację zadań runbook dla rozwiązania, które zostały uruchomione i zakończone pomyślnie.

![Strona rozwiązania do zarządzania aktualizacjami automatyzacji](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

W tym miejscu można przeprowadzić dalszą analizę rekordów zadań, klikając kafelek pierścienia. Pulpit nawigacyjny rozwiązania zawiera historię zadań i wstępnie zdefiniowane zapytania wyszukiwania dziennika. Przełącz się do zaawansowanego portalu analizy dzienników, aby wyszukiwać na podstawie zapytań wyszukiwania.

## <a name="update-the-solution"></a>Aktualizowanie rozwiązania

Jeśli wdrożono poprzednią wersję tego rozwiązania, należy najpierw usunąć go z konta przed wdrożeniem zaktualizowanej wersji. Wykonaj kroki, aby [usunąć rozwiązanie,](#remove-the-solution) a następnie wykonaj kroki, aby [wdrożyć rozwiązanie](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>Usuwanie rozwiązania

Jeśli zdecydujesz, że nie trzeba już używać rozwiązania, można go usunąć z konta automatyzacji. Usunięcie rozwiązania usuwa tylko runbooki. Nie powoduje usunięcia harmonogramów ani zmiennych, które zostały utworzone podczas dodawania rozwiązania. Te zasoby, które należy usunąć ręcznie, jeśli nie używasz ich z innymi elementami runbook.

Aby usunąć rozwiązanie, wykonaj następujące czynności:

1. Z konta automatyzacji w obszarze **Powiązane zasoby**wybierz pozycję Połączony **obszar roboczy**.

2. Wybierz **pozycję Przejdź do obszaru roboczego**.

3. W **obszarze Ogólne**wybierz pozycję **Rozwiązania**. 

4. Na stronie **Rozwiązania** wybierz rozwiązanie **Start-Stop-VM[Obszar roboczy].** Na stronie **VMManagementSolution[Workspace]** z menu wybierz polecenie **Usuń**.<br><br> ![Usuń rozwiązanie VM Mgmt](media/automation-solution-vm-management/vm-management-solution-delete.png)

5. W oknie **Usuń rozwiązanie** upewnij się, że chcesz usunąć rozwiązanie.

6. Gdy informacje są weryfikowane, a rozwiązanie jest usuwane, można śledzić jego **postępy** w obszarze Powiadomienia z menu. Po uruchomieniu procesu usuwania rozwiązania zostaniesz przywrócony do strony **Rozwiązania.**

Konto automatyzacji i obszar roboczy usługi Log Analytics nie są usuwane w ramach tego procesu. Jeśli nie chcesz zachować obszaru roboczego usługi Log Analytics, musisz go ręcznie usunąć. Można to osiągnąć za pomocą portalu Azure:

1. W witrynie Azure portal wyszukaj i wybierz **obszary robocze usługi Log Analytics**.

2. Na stronie **Obszary robocze usługi Log Analytics** wybierz obszar roboczy.

3. Z menu na stronie ustawienia obszaru roboczego wybierz **polecenie Usuń.**

Jeśli nie chcesz zachować składniki konta usługi Azure Automation, można ręcznie usunąć każdy z nich. Aby uzyskać listę elementów runbook, zmiennych i harmonogramów utworzonych przez rozwiązanie, zobacz [składniki rozwiązania](#solution-components).

## <a name="next-steps"></a>Następne kroki

[Włącz](automation-solution-vm-management-enable.md) rozwiązanie Start/Stop podczas poza godzinami pracy dla maszyn wirtualnych platformy Azure.
