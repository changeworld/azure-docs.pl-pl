---
title: Start/Stop VMs during off-hours rozwiązanie
description: To rozwiązanie do zarządzania MASZYNą wirtualną uruchamia i wstrzymuje Azure Resource Managere maszyny wirtualne zgodnie z harmonogramem i aktywnie monitoruje z Azure Monitor dzienników.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/21/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 15036b33e637953de7dc12100468d3dd8570f775
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376094"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Start/Stop VMs during off-hours rozwiązanie w Azure Automation

Start/Stop VMs during off-hours rozwiązanie uruchamia i wstrzymuje maszyny wirtualne platformy Azure w harmonogramach zdefiniowanych przez użytkownika, udostępnia wgląd w dzienniki Azure Monitor i wysyła opcjonalne wiadomości e-mail przy użyciu [grup akcji](../azure-monitor/platform/action-groups.md). Obsługuje ona zarówno Azure Resource Manager, jak i klasycznych maszyn wirtualnych w większości scenariuszy. Aby można było używać tego rozwiązania z klasycznymi maszynami wirtualnymi, musisz mieć klasyczne konto Uruchom jako, które nie jest domyślnie tworzone. Aby uzyskać instrukcje dotyczące tworzenia klasycznego konta Uruchom jako, zobacz [klasyczne konta Uruchom jako](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> Rozwiązanie Start/Stop VMs during off-hours zostało przetestowane z modułami platformy Azure, które zostały zaimportowane do konta usługi Automation podczas wdrażania rozwiązania. Rozwiązanie nie działa obecnie z nowszymi wersjami modułu platformy Azure. Ma to wpływ tylko na konto usługi Automation używane do uruchamiania rozwiązania Start/Stop VMs during off-hours. Nadal można używać nowszych wersji modułu platformy Azure na innych kontach usługi Automation, zgodnie z opisem w artykule [jak zaktualizować moduły Azure PowerShell w Azure Automation](automation-update-azure-modules.md)

To rozwiązanie zapewnia zdecentralizowaną opcję automatyzacji o niskich kosztach dla użytkowników, którzy chcą zoptymalizować koszty maszyn wirtualnych. Dzięki temu rozwiązaniu można:

- Zaplanuj uruchamianie i zatrzymywanie maszyn wirtualnych.
- Zaplanuj uruchamianie i zatrzymywanie maszyn wirtualnych w porządku rosnącym przy użyciu tagów platformy Azure (nieobsługiwanych w przypadku klasycznych maszyn wirtualnych).
- Zatrzymaj zatrzymywanie maszyn wirtualnych na podstawie niskiego użycia procesora CPU.

Poniżej przedstawiono ograniczenia dotyczące bieżącego rozwiązania:

- To rozwiązanie służy do zarządzania maszynami wirtualnymi w dowolnym regionie, ale mogą być używane tylko w tej samej subskrypcji co konto Azure Automation.
- To rozwiązanie jest dostępne na platformie Azure i AzureGov do dowolnego regionu, który obsługuje obszar roboczy Log Analytics, konto Azure Automation i alerty. Regiony AzureGov obecnie nie obsługują funkcji poczty e-mail.

> [!NOTE]
> Jeśli korzystasz z rozwiązania dla klasycznych maszyn wirtualnych, wszystkie maszyny wirtualne będą przetwarzane sekwencyjnie dla każdej usługi w chmurze. Maszyny wirtualne są nadal przetwarzane równolegle w różnych usługach w chmurze.
>
> Subskrypcje dostawcy rozwiązań w chmurze platformy Azure (CSP) obsługują tylko model Azure Resource Manager, usługi nieAzure Resource Managerowe nie są dostępne w programie. Po uruchomieniu rozwiązania uruchamiania/zatrzymywania może wystąpić błąd, ponieważ zawiera polecenia cmdlet służące do zarządzania klasycznymi zasobami. Aby dowiedzieć się więcej na temat dostawcy usług kryptograficznych, zobacz [dostępne usługi w subskrypcjach programu CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments). W przypadku korzystania z subskrypcji dostawcy CSP należy zmodyfikować zmienną [**External_EnableClassicVMs**](#variables) na **false** po wdrożeniu.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Elementy Runbook dla tego rozwiązania działają przy użyciu [konta Uruchom jako platformy Azure](automation-create-runas-account.md). Konto Uruchom jako jest preferowaną metodą uwierzytelniania, ponieważ używa uwierzytelniania certyfikatu zamiast hasła, które może wygasnąć lub zmienić.

Zalecane jest użycie oddzielnego konta usługi Automation dla rozwiązania do uruchamiania/zatrzymywania maszyn wirtualnych. Dzieje się tak, ponieważ wersje modułów platformy Azure są często uaktualniane i ich parametry mogą ulec zmianie. Rozwiązanie uruchamiania/zatrzymywania maszyny wirtualnej nie jest uaktualniane na tym samym erze, więc może nie współpracować z nowszymi wersjami poleceń cmdlet, których używa. Zaleca się przetestowanie aktualizacji modułu na koncie automatyzacji testów przed ich zaimportowaniem na konto automatyzacji produkcji.

### <a name="permissions-needed-to-deploy"></a>Uprawnienia do wdrożenia

Istnieją pewne uprawnienia, które użytkownik musi wykonać, aby wdrożyć maszyny wirtualne uruchamiania/zatrzymywania poza godziną. Te uprawnienia są różne w przypadku używania wstępnie utworzonego konta usługi Automation i obszaru roboczego Log Analytics lub tworzenia nowych podczas wdrażania. Jeśli jesteś współautorem subskrypcji i administratorem globalnym w dzierżawie Azure Active Directory, nie musisz konfigurować następujących uprawnień. Jeśli nie masz tych praw lub musisz skonfigurować rolę niestandardową, zapoznaj się z uprawnieniami wymaganymi poniżej.

#### <a name="pre-existing-automation-account-and-log-analytics-account"></a>Istniejące konto usługi Automation i konto Log Analytics

Aby wdrożyć rozwiązanie do uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami na koncie usługi Automation i Log Analytics wdrożenie przez użytkownika rozwiązania wymaga następujących uprawnień do **grupy zasobów**. Aby dowiedzieć się więcej na temat ról, zobacz [role niestandardowe dla zasobów platformy Azure](../role-based-access-control/custom-roles.md).

| Uprawnienie | Zakres|
| --- | --- |
| Microsoft. Automation/automationAccounts/Read | Grupa zasobów |
| Microsoft. Automation/automationAccounts/zmienne/zapis | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Schedules/Write | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Runbooks/Write | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Connections/Write | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Certificates/Write | Grupa zasobów |
| Microsoft. Automation/automationAccounts/moduły/zapis | Grupa zasobów |
| Microsoft. Automation/automationAccounts/moduły/odczyt | Grupa zasobów |
| Microsoft. Automation/automationAccounts/jobSchedules/Write | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Jobs/Write | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Jobs/Read | Grupa zasobów |
| Microsoft. OperationsManagement/Solutions/Write | Grupa zasobów |
| Microsoft. OperationalInsights/obszary robocze/* | Grupa zasobów |
| Microsoft. Insights/diagnosticSettings/Write | Grupa zasobów |
| Microsoft. Insights/ActionGroups/Write | Grupa zasobów |
| Microsoft. Insights/ActionGroups/Read | Grupa zasobów |
| Microsoft. resources/subscriptions/resourceGroups/Read | Grupa zasobów |
| Microsoft. resources/Deployments/* | Grupa zasobów |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nowe konto usługi Automation i nowy obszar roboczy Log Analytics

Aby wdrożyć rozwiązanie do uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami na nowym koncie usługi Automation i Log Analytics obszarze roboczym, wdrożenie przez użytkownika rozwiązania wymaga uprawnień zdefiniowanych w poprzedniej sekcji, a także następujących uprawnień:

- Współadministrator w ramach subskrypcji — jest to potrzebne tylko do utworzenia klasycznego konta Uruchom jako, jeśli zamierzasz zarządzać klasycznymi maszynami wirtualnymi. [Klasyczne konta Uruchom jako](automation-create-standalone-account.md#classic-run-as-accounts) nie są już domyślnie tworzone.
- Należy do roli **Deweloper aplikacji** [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) . Aby uzyskać więcej informacji na temat konfigurowania kont Uruchom jako, zobacz [uprawnienia do konfigurowania kont Uruchom jako](manage-runas-account.md#permissions).
- Współautorem subskrypcji lub następujących uprawnień.

| Uprawnienie |Zakres|
| --- | --- |
| Microsoft. Authorization/Operations/Read | Subskrypcja|
| Microsoft. Autoryzacja/uprawnienia/Odczyt |Subskrypcja|
| Microsoft. Authorization/roleAssignments/Read | Subskrypcja |
| Microsoft.Authorization/roleAssignments/write | Subskrypcja |
| Microsoft. Authorization/roleAssignments/Delete | Subskrypcja |
| Microsoft. Automation/automationAccounts/Connections/Read | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Certificates/Read | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Write | Grupa zasobów |
| Microsoft. OperationalInsights/obszary robocze/zapis | Grupa zasobów |

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Wykonaj następujące kroki, aby dodać rozwiązanie Start/Stop VMs during off-hours do konta usługi Automation, a następnie skonfiguruj zmienne, aby dostosować rozwiązanie.

1. Z poziomu konta usługi Automation wybierz pozycję **Uruchom/Zatrzymaj maszynę wirtualną** w obszarze **powiązane zasoby**. W tym miejscu możesz kliknąć **więcej informacji na temat i włączyć rozwiązanie**. Jeśli masz już wdrożone rozwiązanie do uruchamiania/zatrzymywania maszyny wirtualnej, możesz je wybrać, klikając pozycję **Zarządzaj rozwiązaniem** i wyszukując je na liście.

   ![Włącz z konta usługi Automation](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Możesz również utworzyć go z dowolnego miejsca w Azure Portal, klikając pozycję **Utwórz zasób**. Na stronie Marketplace wpisz słowo kluczowe, takie jak **Start** lub **Start/Stop**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Alternatywnie możesz wpisać jedno lub więcej słów kluczowych z pełnej nazwy rozwiązania, a następnie nacisnąć klawisz ENTER. Wybierz **Start/Stop VMS during off-hours** z wyników wyszukiwania.

2. Na stronie **Start/Stop VMS during off-hours** wybranego rozwiązania Przejrzyj informacje podsumowujące, a następnie kliknij przycisk **Utwórz**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

3. Zostanie wyświetlona strona **Dodawanie rozwiązania** . Zostanie wyświetlony monit o skonfigurowanie rozwiązania przed zaimportowaniem go do subskrypcji usługi Automation.

   ![Strona dodawania rozwiązania do zarządzania maszyną wirtualną](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Na stronie **Dodawanie rozwiązania** wybierz pozycję **obszar roboczy**. Wybierz obszar roboczy Log Analytics, który jest połączony z tą samą subskrypcją platformy Azure, w której znajduje się konto usługi Automation. Jeśli nie masz obszaru roboczego, wybierz pozycję **Utwórz nowy obszar roboczy**. Na stronie **obszar roboczy log Analytics** wykonaj następujące czynności:
   - Określ nazwę nowego **obszaru roboczego log Analytics**, na przykład "ContosoLAWorkspace".
   - Wybierz **subskrypcję** , z którą chcesz utworzyć łącze, wybierając pozycję z listy rozwijanej, jeśli wybrana wartość domyślna nie jest odpowiednia.
   - W obszarze **Grupa zasobów**można utworzyć nową grupę zasobów lub wybrać istniejącą.
   - Wybierz **lokalizację**. Obecnie jedyne dostępne lokalizacje to **Australia Południowo-Wschodnia**, **Kanada środkowa**, **Indie Środkowe**, **Wschodnie stany USA**, **Japonia Wschodnia**, **Azja Południowo**-Wschodnia, **Południowe Zjednoczone Królestwo**, **Europa Zachodnia**i **zachodnie stany USA 2** .
   - Wybierz **warstwę cenową**. Wybierz opcję **na GB (autonomiczne)** . W przypadku dzienników Azure Monitor Zaktualizowano [Cennik](https://azure.microsoft.com/pricing/details/log-analytics/) i jedyną opcją jest warstwa za GB.

   > [!NOTE]
   > W przypadku włączenia rozwiązań tylko w niektórych regionach jest obsługiwane łączenie obszaru roboczego usługi Log Analytics i konta usługi Automation.
   >
   > Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

5. Po podania wymaganych informacji na stronie **obszaru roboczego log Analytics** kliknij przycisk **Utwórz**. Postęp można śledzić w obszarze **powiadomienia** z menu, co powoduje powrót do strony **Dodawanie rozwiązania** po zakończeniu.
6. Na stronie **Dodawanie rozwiązania** wybierz pozycję **konto usługi Automation**. Jeśli tworzysz nowy obszar roboczy Log Analytics, możesz utworzyć nowe konto usługi Automation, które ma być skojarzone z nim, lub wybrać istniejące konto usługi Automation, które nie jest już połączone z Log Analytics obszarem roboczym. Wybierz istniejące konto usługi Automation lub kliknij pozycję **Utwórz konto usługi Automation**, a następnie na stronie **Dodawanie konta usługi Automation** podaj następujące informacje:
   - W polu **Nazwa** wprowadź nazwę konta usługi Automation.

     Wszystkie inne opcje są wypełniane automatycznie na podstawie wybranego obszaru roboczego Log Analytics. Tych opcji nie można modyfikować. Konto Uruchom jako platformy Azure jest domyślną metodą uwierzytelniania dla elementów Runbook zawartych w tym rozwiązaniu. Po kliknięciu przycisku **OK**opcje konfiguracji są weryfikowane i tworzone jest konto usługi Automation. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

7. Na koniec na stronie **Dodawanie rozwiązania** wybierz pozycję **Konfiguracja**. Zostanie wyświetlona strona **Parametry** .

   ![Strona parametrów rozwiązania](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   W tym miejscu zostanie wyświetlony monit o:
   - Określ **nazwy docelowych nazw zasobów**. Te wartości to nazwy grup zasobów, które zawierają maszyny wirtualne, które mają być zarządzane przez to rozwiązanie. Można wprowadzić więcej niż jedną nazwę i oddzielić każdy z nich przy użyciu przecinka (w wartościach nie jest rozróżniana wielkość liter). Użycie symbolu wieloznacznego jest obsługiwane. Możesz skorzystać z tej opcji, jeśli chcesz uwzględnić maszyny wirtualne we wszystkich grupach zasobów w subskrypcji. Ta wartość jest przechowywana w zmiennych **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames** .
   - Określ **listę wykluczeń maszyny wirtualnej (ciąg)** . Ta wartość jest nazwą co najmniej jednej maszyny wirtualnej z docelowej grupy zasobów. Można wprowadzić więcej niż jedną nazwę i oddzielić każdy z nich przy użyciu przecinka (w wartościach nie jest rozróżniana wielkość liter). Użycie symbolu wieloznacznego jest obsługiwane. Ta wartość jest przechowywana w zmiennej **External_ExcludeVMNames** .
   - Wybierz **harmonogram**. Wybierz datę i godzinę dla harmonogramu. Zostanie utworzony dzienny harmonogram, który rozpoczyna się od wybranego czasu. Wybór innego regionu nie jest dostępny. Aby skonfigurować harmonogram do określonej strefy czasowej po skonfigurowaniu rozwiązania, zobacz [Modyfikowanie harmonogramu uruchamiania i zamykania](#modify-the-startup-and-shutdown-schedules).
   - Aby otrzymywać **powiadomienia e-mail** z grupy akcji, Zaakceptuj wartość domyślną **tak** i Podaj prawidłowy adres e-mail. Jeśli wybierzesz pozycję **nie** , ale zdecydujesz się na późniejszą datę otrzymywania powiadomień e-mail, możesz zaktualizować [grupę akcji](../azure-monitor/platform/action-groups.md) utworzoną przy użyciu prawidłowych adresów e-mail rozdzielonych przecinkami. Należy również włączyć następujące reguły alertów:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Wartość domyślna dla **docelowych nazw zasobów** jest **&ast;** . Dotyczy to wszystkich maszyn wirtualnych w subskrypcji. Jeśli nie chcesz, aby rozwiązanie przekierować wszystkie maszyny wirtualne w subskrypcji, ta wartość musi zostać zaktualizowana do listy nazw grup zasobów przed włączeniem harmonogramów.

8. Po skonfigurowaniu początkowych ustawień wymaganych dla rozwiązania kliknij przycisk **OK** , aby zamknąć stronę **Parametry** , a następnie wybierz pozycję **Utwórz**. Po zweryfikowaniu wszystkich ustawień rozwiązanie zostanie wdrożone w ramach subskrypcji. Ten proces może potrwać kilka sekund, a postęp można śledzić w obszarze **powiadomienia** z menu.

> [!NOTE]
> Jeśli masz subskrypcję dostawcy rozwiązań w chmurze platformy Azure (CSP), po zakończeniu wdrożenia na koncie usługi Automation przejdź do pozycji **zmienne** w obszarze **zasoby udostępnione** i ustaw dla zmiennej [**External_EnableClassicVMs**](#variables) **wartość false.** . To uniemożliwia rozwiązanie wyszukiwanie klasycznych zasobów maszyn wirtualnych.

## <a name="scenarios"></a>Scenariusze

Rozwiązanie zawiera trzy różne scenariusze. Te scenariusze są następujące:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scenariusz 1. Uruchamianie/zatrzymywanie maszyn wirtualnych zgodnie z harmonogramem

Ten scenariusz jest domyślną konfiguracją podczas pierwszego wdrożenia rozwiązania. Można na przykład skonfigurować go tak, aby zatrzymać wszystkie maszyny wirtualne w ramach subskrypcji, gdy opuszczasz pracę w wieczorie i zaczniesz je od rano, gdy wrócisz do biura. Podczas konfigurowania harmonogramów **zaplanowane — StartVM** i **zaplanowany-StopVM** podczas wdrażania, uruchamiają i zatrzymują maszyny wirtualne. Konfigurowanie tego rozwiązania do tylko zatrzymywania maszyn wirtualnych jest obsługiwane, zobacz [Modyfikowanie harmonogramów uruchamiania i zamykania](#modify-the-startup-and-shutdown-schedules) , aby dowiedzieć się, jak skonfigurować harmonogram niestandardowy.

> [!NOTE]
> Strefa czasowa jest bieżącą strefą czasową podczas konfigurowania parametru czasu planowania. Są one jednak przechowywane w formacie UTC w Azure Automation. Nie trzeba wykonywać żadnej konwersji strefy czasowej, ponieważ jest ona obsługiwana podczas wdrażania.

Aby kontrolować, które maszyny wirtualne znajdują się w zakresie, należy skonfigurować następujące zmienne: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**i **External_ExcludeVMNames**.

Można włączyć opcję określania wartości docelowej dla subskrypcji i grupy zasobów lub przeznaczenie określonej listy maszyn wirtualnych, ale nie obu.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Kierowanie akcji uruchamiania i zatrzymywania względem subskrypcji i grupy zasobów

1. Skonfiguruj zmienne **External_Stop_ResourceGroupNames** i **External_ExcludeVMNames** w celu określenia docelowych maszyn wirtualnych.
2. Włącz i zaktualizuj harmonogramy **zaplanowane-StartVM** i **zaplanowane StopVM** .
3. Uruchom element Runbook **ScheduledStartStop_Parent** z parametrem Action ustawionym na wartość **Start** i parametrem WHATIF ustawionym na **true** , aby wyświetlić podgląd zmian.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Kierowanie akcji Rozpocznij i Zatrzymaj według maszyny wirtualnej

1. Uruchom element Runbook **ScheduledStartStop_Parent** z parametrem Action ustawionym na wartość **Start**, Dodaj rozdzieloną przecinkami listę maszyn wirtualnych w parametrze *VMList* , a następnie ustaw parametr WHATIF na **true**. Wyświetl podgląd zmian.
1. Skonfiguruj parametr **External_ExcludeVMNames** z rozdzieloną przecinkami listą maszyn wirtualnych (VM1, VM2, VM3).
1. Ten scenariusz nie honoruje zmiennych **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupnames** . W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać szczegółowe informacje, zobacz [Planowanie elementu Runbook w Azure Automation](../automation/automation-schedules.md).

> [!NOTE]
> Wartość **docelowej nazwy obiektu** do odniesień jest przechowywana jako wartość zarówno **External_Start_ResourceGroupNames** , jak i **External_Stop_ResourceGroupNames**. Aby uzyskać więcej stopnia szczegółowości, można zmodyfikować każdą z tych zmiennych dla różnych grup zasobów. Dla akcji uruchamiania Użyj **External_Start_ResourceGroupNames**i dla akcji zatrzymania, użyj **External_Stop_ResourceGroupNames**. Maszyny wirtualne są automatycznie dodawane do harmonogramów uruchamiania i zatrzymywania.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scenariusz 2. Uruchamianie/zatrzymywanie maszyn wirtualnych w sekwencji przy użyciu tagów

W środowisku zawierającym co najmniej dwa składniki na wielu maszynach wirtualnych obsługujących obciążenie rozproszone, obsługa sekwencji, w której składniki zostały uruchomione i zatrzymane, jest ważna. Ten scenariusz można wykonać, wykonując następujące czynności:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Kierowanie akcji uruchamiania i zatrzymywania względem subskrypcji i grupy zasobów

1. Dodaj tag **sequencestart** i **sequencestop** z dodatnią wartością całkowitą do maszyn wirtualnych, które są przeznaczone do zmiennych **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames** . Akcje uruchamiania i zatrzymywania są wykonywane w kolejności rosnącej. Aby dowiedzieć się, jak oznaczyć maszynę wirtualną, zobacz [znakowanie maszyny wirtualnej z systemem Windows na platformie Azure](../virtual-machines/windows/tag.md) i oznaczanie [maszyny wirtualnej z systemem Linux na platformie Azure](../virtual-machines/linux/tag.md).
1. Zmodyfikuj harmonogramy **Sequenced-StartVM** i **Sequenced-StopVM** do daty i godziny, które spełniają Twoje wymagania, i Włącz harmonogram.
1. Uruchom element Runbook **SequencedStartStop_Parent** z parametrem Action ustawionym na wartość **Start** i parametrem WHATIF ustawionym na **true** , aby wyświetlić podgląd zmian.
1. Wyświetl podgląd akcji i wprowadź wszelkie niezbędne zmiany przed wdrożeniem na maszynach wirtualnych w środowisku produkcyjnym. Gdy wszystko będzie gotowe, ręcznie wykonaj element Runbook z parametrem ustawionym na **wartość false**lub pozwól na sekwencjonowanie harmonogramu usługi Automation **-StartVM** i **Sequenced-StopVM** działa automatycznie zgodnie z określonym harmonogramem.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Kierowanie akcji Rozpocznij i Zatrzymaj według maszyny wirtualnej

1. Dodaj tag **sequencestart** i **sequencestop** z dodatnią wartością całkowitą do maszyn wirtualnych, które planujesz dodać do parametru **VMList** .
1. Uruchom element Runbook **SequencedStartStop_Parent** z parametrem Action ustawionym na wartość **Start**, Dodaj rozdzieloną przecinkami listę maszyn wirtualnych w parametrze *VMList* , a następnie ustaw parametr WHATIF na **true**. Wyświetl podgląd zmian.
1. Skonfiguruj parametr **External_ExcludeVMNames** z rozdzieloną przecinkami listą maszyn wirtualnych (VM1, VM2, VM3).
1. Ten scenariusz nie honoruje zmiennych **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupnames** . W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać szczegółowe informacje, zobacz [Planowanie elementu Runbook w Azure Automation](../automation/automation-schedules.md).
1. Wyświetl podgląd akcji i wprowadź wszelkie niezbędne zmiany przed wdrożeniem na maszynach wirtualnych w środowisku produkcyjnym. Gdy wszystko będzie gotowe, ręcznie wykonaj polecenie monitoring-and-Diagnostics/monitoring-Action-groupsrunbook z parametrem ustawionym na **wartość false**lub pozwól na sekwencjonowanie harmonogramu usługi Automation **-StartVM** i **Sequenced-StopVM** uruchamiane automatycznie zgodnie z ustalonym harmonogramem.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Scenariusz 3. automatyczne uruchamianie/zatrzymywanie na podstawie użycia procesora CPU

To rozwiązanie może pomóc w zarządzaniu kosztami uruchamiania maszyn wirtualnych w ramach subskrypcji przez ocenę maszyn wirtualnych platformy Azure, które nie są używane w okresach poza szczytem, na przykład po godzinach, i ich automatyczne wyłączenie, jeśli użycie procesora jest mniejsze niż x%.

Domyślnie rozwiązanie jest wstępnie skonfigurowane, aby oszacować procentową metrykę procesora CPU, aby sprawdzić, czy średnie użycie nie jest równe 5 procent. Ten scenariusz jest kontrolowany przez następujące zmienne i można go zmodyfikować, jeśli wartości domyślne nie spełniają wymagań:

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

Można włączyć opcję określania wartości docelowej dla subskrypcji i grupy zasobów lub przeznaczenie określonej listy maszyn wirtualnych, ale nie obu.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Przekieruj akcję zatrzymania na subskrypcję i grupę zasobów

1. Skonfiguruj zmienne **External_Stop_ResourceGroupNames** i **External_ExcludeVMNames** w celu określenia docelowych maszyn wirtualnych.
1. Włącz i zaktualizuj harmonogram **Schedule_AutoStop_CreateAlert_Parent** .
1. Uruchom element Runbook **AutoStop_CreateAlert_Parent** z parametrem Action ustawionym na wartość **Start** i parametrem WHATIF ustawionym na **true** , aby wyświetlić podgląd zmian.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Kierowanie akcji Rozpocznij i Zatrzymaj według maszyny wirtualnej

1. Uruchom element Runbook **AutoStop_CreateAlert_Parent** z parametrem Action ustawionym na wartość **Start**, Dodaj rozdzieloną przecinkami listę maszyn wirtualnych w parametrze *VMList* , a następnie ustaw parametr WHATIF na **true**. Wyświetl podgląd zmian.
1. Skonfiguruj parametr **External_ExcludeVMNames** z rozdzieloną przecinkami listą maszyn wirtualnych (VM1, VM2, VM3).
1. Ten scenariusz nie honoruje zmiennych **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupnames** . W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać szczegółowe informacje, zobacz [Planowanie elementu Runbook w Azure Automation](../automation/automation-schedules.md).

Teraz, gdy masz harmonogram zatrzymywania maszyn wirtualnych na podstawie użycia procesora CPU, musisz włączyć jeden z poniższych harmonogramów, aby je uruchomić.

- Docelowa akcja uruchamiania według subskrypcji i grupy zasobów. Zapoznaj się z instrukcjami w [scenariuszu 1](#scenario-1-startstop-vms-on-a-schedule) w celu testowania i włączania harmonogramów **harmonogramu StartVM** .
- Docelowa akcja uruchamiania według subskrypcji, grupy zasobów i tagu. Zobacz kroki w [scenariuszu 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) , aby przetestować i włączyć harmonogramy **sekwencyjne-StartVM** .

## <a name="solution-components"></a>Składniki rozwiązania

To rozwiązanie obejmuje wstępnie skonfigurowane elementy Runbook, harmonogramy i integrację z dziennikami Azure Monitor, dzięki czemu można dostosować uruchamianie i zamykanie maszyn wirtualnych zgodnie z potrzebami firmy.

### <a name="runbooks"></a>Elementy Runbook

Poniższa tabela zawiera listę elementów Runbook wdrożonych na koncie usługi Automation według tego rozwiązania. Nie wprowadzaj zmian w kodzie elementu Runbook. Zamiast tego napisz własny element Runbook pod kątem nowych funkcji.

> [!IMPORTANT]
> Nie należy bezpośrednio uruchamiać żadnego elementu Runbook z elementem "Child" dołączonym do jego nazwy.

Wszystkie nadrzędne elementy Runbook zawierają parametr _whatIf_ . Po ustawieniu na **wartość true**, _whatIf_ obsługuje szczegóły dokładnego zachowania elementu Runbook, gdy jest uruchamiany bez parametru _whatIf_ i sprawdza, czy są wskazane poprawne maszyny wirtualne. Element Runbook wykonuje tylko zdefiniowane akcje, gdy parametr _whatIf_ ma wartość **false**.

|Element Runbook | Parametry | Opis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Wywoływana z nadrzędnego elementu Runbook. Ten element Runbook tworzy alerty dotyczące poszczególnych zasobów dla scenariusza zatrzymywania.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true lub false  | Tworzy lub aktualizuje reguły alertów platformy Azure na maszynach wirtualnych w ramach dostosowanej subskrypcji lub grup zasobów. <br> VMList: rozdzielona przecinkami lista maszyn wirtualnych. Na przykład _VM1, VM2, VM3_.<br> Wartość *whatIf* weryfikuje logikę elementu Runbook bez wykonywania operacji.|
|AutoStop_Disable | brak | Wyłącza alerty autozatrzymaj i domyślny harmonogram.|
|AutoStop_StopVM_Child | WebHookData | Wywoływana z nadrzędnego elementu Runbook. Reguły alertów wywołują ten element Runbook, aby zatrzymać maszynę wirtualną.|
|Bootstrap_Main | brak | Używane jednorazowo do konfigurowania konfiguracji ładowania początkowego, takich jak webhookURI, które zwykle nie są dostępne w Azure Resource Manager. Ten element Runbook jest usuwany automatycznie po pomyślnym wdrożeniu.|
|ScheduledStartStop_Child | VMName <br> Akcja: uruchamianie lub zatrzymywanie <br> ResourceGroupName | Wywoływana z nadrzędnego elementu Runbook. Wykonuje akcję uruchamiania lub zatrzymywania dla zaplanowanego zatrzymania.|
|ScheduledStartStop_Parent | Akcja: uruchamianie lub zatrzymywanie <br>VMList <br> WhatIf: true lub false | To ustawienie ma wpływ na wszystkie maszyny wirtualne w subskrypcji. Edytuj **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames** , aby wykonywać tylko te grupy zasobów. Można również wykluczyć określone maszyny wirtualne, aktualizując zmienną **External_ExcludeVMNames** .<br> VMList: rozdzielona przecinkami lista maszyn wirtualnych. Na przykład _VM1, VM2, VM3_.<br> Wartość _whatIf_ weryfikuje logikę elementu Runbook bez wykonywania operacji.|
|SequencedStartStop_Parent | Akcja: uruchamianie lub zatrzymywanie <br> WhatIf: true lub false<br>VMList| Utwórz Tagi o nazwie **sequencestart** i **sequencestop** na każdej maszynie wirtualnej, dla której chcesz sekwencjonować działanie uruchamiania/zatrzymywania. W tych nazwach tagów jest rozróżniana wielkość liter. Wartość tagu powinna być dodatnią liczbą całkowitą (1, 2, 3) odpowiadającą kolejności, w której ma zostać uruchomiona lub zatrzymana. <br> VMList: rozdzielona przecinkami lista maszyn wirtualnych. Na przykład _VM1, VM2, VM3_. <br> Wartość _whatIf_ weryfikuje logikę elementu Runbook bez wykonywania operacji. <br> **Uwaga**: maszyny wirtualne muszą znajdować się w grupach zasobów zdefiniowanych jako External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames i External_ExcludeVMNames w zmiennych Azure Automation. Muszą mieć odpowiednie Tagi, aby akcje zaczęły obowiązywać.|

### <a name="variables"></a>Zmienne

Poniższa tabela zawiera listę zmiennych utworzonych na koncie usługi Automation. Modyfikuj tylko zmienne z prefiksem **zewnętrznym**. Modyfikowanie zmiennych poprzedzonych prefiksem **Internal** powoduje niepożądane skutki.

|Zmienna | Opis|
|---------|------------|
|External_AutoStop_Condition | Operator warunkowy wymagany do skonfigurowania warunku przed wyzwoleniem alertu. Dopuszczalne wartości to **GreaterThan**, **GreaterThanOrEqual**, **LessThan**i **LessThanOrEqual**.|
|External_AutoStop_Description | Alert, aby zatrzymać maszynę wirtualną, jeśli procent procesora CPU przekroczy wartość progową.|
|External_AutoStop_MetricName | Nazwa metryki wydajności, dla której ma zostać skonfigurowana reguła alertu platformy Azure.|
|External_AutoStop_Threshold | Próg reguły alertu platformy Azure określony w zmiennej _External_AutoStop_MetricName_. Wartości procentowe mogą mieć wartość z zakresu od 1 do 100.|
|External_AutoStop_TimeAggregationOperator | Operator agregacji czasu, który jest stosowany do wybranego rozmiaru okna, aby oszacować warunek. Dopuszczalne wartości to **Average**, **minimum**, **maksimum**, **Suma**i **ostatnie**.|
|External_AutoStop_TimeWindow | Rozmiar okna, w którym platforma Azure analizuje wybrane metryki na potrzeby wyzwalania alertu. Ten parametr akceptuje dane wejściowe w formacie TimeSpan. Możliwe wartości to od 5 minut do 6 godzin.|
|External_EnableClassicVMs| Określa, czy klasyczne maszyny wirtualne są objęte rozwiązaniem. Wartość domyślna to true. Dla subskrypcji CSP należy ustawić wartość false. Klasyczne maszyny wirtualne wymagają [klasycznego konta Uruchom jako](automation-create-standalone-account.md#classic-run-as-accounts).|
|External_ExcludeVMNames | Wprowadź nazwy maszyn wirtualnych do wykluczenia, oddzielając nazwy przecinkami bez spacji. Jest to ograniczone do 140 maszyn wirtualnych. Jeśli dodasz więcej niż 140 maszyn wirtualnych do tej listy rozdzielanej przecinkami, maszyny wirtualne, które są wykluczone, mogą zostać przypadkowo uruchomione lub zatrzymane.|
|External_Start_ResourceGroupNames | Określa co najmniej jedną grupę zasobów, oddzielając wartości przy użyciu przecinka przeznaczonego do uruchamiania akcji.|
|External_Stop_ResourceGroupNames | Określa co najmniej jedną grupę zasobów, oddzielając wartości przy użyciu przecinka przeznaczonego dla akcji zatrzymania.|
|Internal_AutomationAccountName | Określa nazwę konta usługi Automation.|
|Internal_AutoSnooze_WebhookUri | Określa identyfikator URI elementu webhook wywołanego dla scenariusza zatrzymywania.|
|Internal_AzureSubscriptionId | Określa identyfikator subskrypcji platformy Azure.|
|Internal_ResourceGroupName | Określa nazwę grupy zasobów konta usługi Automation.|

We wszystkich scenariuszach zmienne **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**i **External_ExcludeVMNames** są niezbędne do określania docelowych maszyn wirtualnych, z wyjątkiem podawania rozdzielonych przecinkami Lista maszyn wirtualnych dla elementów Runbook **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**i **ScheduledStartStop_Parent** . Oznacza to, że maszyny wirtualne muszą znajdować się w docelowych grupach zasobów, aby akcje uruchamiania i zatrzymywania zostały wykonane. Logika działa podobnie do usługi Azure Policy, w której można kierować do subskrypcji lub grupy zasobów, a akcje są dziedziczone przez nowo utworzone maszyny wirtualne. Takie podejście pozwala uniknąć konieczności utrzymania oddzielnego harmonogramu dla każdej maszyny wirtualnej i zarządzanie rozpoczęciem i zatrzymaniem w skali.

### <a name="schedules"></a>Harmonogramy

Poniższa tabela zawiera listę domyślnych harmonogramów utworzonych w ramach konta usługi Automation. Można je modyfikować lub tworzyć własne harmonogramy niestandardowe. Domyślnie wszystkie harmonogramy są wyłączone z wyjątkiem **Scheduled_StartVM** i **Scheduled_StopVM**.

Nie należy włączać wszystkich harmonogramów, ponieważ mogą one tworzyć nakładające się akcje dotyczące harmonogramu. Najlepiej określić optymalizacje, które chcesz wykonać i odpowiednio zmodyfikować. Zapoznaj się z przykładowymi scenariuszami w sekcji Przegląd w celu uzyskania dalszych wyjaśnień.

|Nazwa harmonogramu | Częstotliwość | Opis|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Co 8 godzin | Uruchamia element Runbook AutoStop_CreateAlert_Parent co 8 godzin, co z kolei powoduje zatrzymanie wartości opartych na maszynie wirtualnej w External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames i External_ExcludeVMNames w zmiennych Azure Automation. Alternatywnie można określić rozdzieloną przecinkami listę maszyn wirtualnych przy użyciu parametru VMList.|
|Scheduled_StopVM | Zdefiniowany przez użytkownika, codziennie | Uruchamia element Runbook Scheduled_Parent z parametrem _stop_ każdego dnia o określonej godzinie. Automatycznie wstrzymuje wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez zmienne zasobów. Włącz harmonogram związany z **planowaną StartVM**.|
|Scheduled_StartVM | Zdefiniowany przez użytkownika, codziennie | Uruchamia element Runbook Scheduled_Parent z parametrem _Start_ każdego dnia o określonej godzinie. Automatycznie uruchamia wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez odpowiednie zmienne. Włącz harmonogram związany z **planowaną StopVM**.|
|Sequenced-StopVM | 1:00 AM (UTC), każdy piątek | Uruchamia element Runbook Sequenced_Parent z parametrem _stop_ co piątek o określonej godzinie. Sekwencyjnie (rosnąco) przerywa wszystkie maszyny wirtualne ze znacznikiem **SequenceStop** zdefiniowanym przez odpowiednie zmienne. Aby uzyskać więcej informacji na temat wartości tagów i zmiennych zasobów, zobacz sekcję elementy Runbook. Włącz harmonogram związany z **sekwencją-StartVM**.|
|Sequenced-StartVM | 1:00 PM (UTC), co poniedziałek | Uruchamia element Runbook Sequenced_Parent z parametrem _Start_ co poniedziałek o określonej godzinie. Sekwencyjnie (malejąco) uruchamia wszystkie maszyny wirtualne z tagiem **SequenceStart** zdefiniowanym przez odpowiednie zmienne. Aby uzyskać więcej informacji na temat wartości tagów i zmiennych zasobów, zobacz sekcję elementy Runbook. Włącz harmonogram związany z **sekwencją-StopVM**.|

## <a name="azure-monitor-logs-records"></a>Rekordy dzienników Azure Monitor

Automatyzacja tworzy dwa typy rekordów w obszarze roboczym Log Analytics: dzienniki zadań i strumienie zadań.

### <a name="job-logs"></a>Dzienniki zadań

|Właściwość | Opis|
|----------|----------|
|Obiekt wywołujący |  Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań.|
|Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobLogs.|
|CorrelationId | GUID, który jest IDENTYFIKATORem korelacji zadania elementu Runbook.|
|JobId | GUID, który jest IDENTYFIKATORem zadania elementu Runbook.|
|operationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest zadanie.|
|resourceId | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|ResourceGroup | Określa nazwę grupy zasobów zadania elementu Runbook.|
|ResourceProvider | Określa nazwę usługi platformy Azure, która zapewnia zasoby do wdrożenia i zarządzania. W przypadku usługi Automation wartością jest Azure Automation.|
|ResourceType | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|resultType | Stan zadania elementu Runbook. Możliwe wartości:<br>— Uruchomione<br>— Zatrzymane<br>— Wstrzymane<br>— Nie powiodło się<br>— Powiodło się|
|resultDescription | Opisuje stan wyniku zadania elementu Runbook. Możliwe wartości:<br>— Zadanie jest uruchomione<br>— Zadanie nie powiodło się<br>— Zadanie zostało ukończone|
|RunbookName | Określa nazwę elementu Runbook.|
|SourceSystem | Określa system źródłowy dla przesłanych danych. W przypadku usługi Automation wartością jest OpsManager|
|StreamType | Określa typ zdarzenia. Możliwe wartości:<br>— Pełne<br>— Dane wyjściowe<br>— Błąd<br>— Ostrzeżenie|
|SubscriptionId | Określa identyfikator subskrypcji zadania.
|Czas | Data i godzina dla wykonania zadania elementu Runbook.|

### <a name="job-streams"></a>Strumienie zadania

|Właściwość | Opis|
|----------|----------|
|Obiekt wywołujący |  Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań.|
|Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobStreams.|
|JobId | GUID, który jest IDENTYFIKATORem zadania elementu Runbook.|
|operationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest zadanie.|
|ResourceGroup | Określa nazwę grupy zasobów zadania elementu Runbook.|
|resourceId | Określa identyfikator zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|ResourceProvider | Określa nazwę usługi platformy Azure, która zapewnia zasoby do wdrożenia i zarządzania. W przypadku usługi Automation wartością jest Azure Automation.|
|ResourceType | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|resultType | Wynik zadania elementu Runbook w czasie wygenerowania zdarzenia. Możliwe wartości to:<br>— W toku|
|resultDescription | Obejmuje strumień wyjściowy z elementu Runbook.|
|RunbookName | Nazwa elementu Runbook.|
|SourceSystem | Określa system źródłowy dla przesłanych danych. W przypadku usługi Automation wartością jest OpsManager.|
|StreamType | Typ strumienia zadania. Możliwe wartości:<br>— Postęp<br>— Dane wyjściowe<br>— Ostrzeżenie<br>— Błąd<br>— Debugowanie<br>— Pełne|
|Czas | Data i godzina dla wykonania zadania elementu Runbook.|

Gdy wykonujesz każde wyszukiwanie w dzienniku, które zwraca rekordy kategorii **JobLogs** lub **JobStreams**, możesz wybrać widok **JobLogs** lub **JobStreams** , który wyświetla zestaw kafelków podsumowujących aktualizacje zwrócone przez wyszukiwanie.

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla rekordów dzienników zbieranych przez to rozwiązanie.

|Zapytanie | Opis|
|----------|----------|
|Znajdź zadania dla elementu Runbook ScheduledStartStop_Parent, które zakończyły się pomyślnie | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Znajdź zadania dla elementu Runbook SequencedStartStop_Parent, które zakończyły się pomyślnie | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>Wyświetlanie rozwiązania

Aby uzyskać dostęp do rozwiązania, przejdź do konta usługi Automation i wybierz pozycję **obszar roboczy** w obszarze **powiązane zasoby**. Na stronie usługi log Analytics wybierz pozycję **rozwiązania** w obszarze **Ogólne**. Na stronie **rozwiązania** wybierz z listy rozwiązanie **Start-Stop-VM [obszar roboczy]** .

Wybranie rozwiązania spowoduje wyświetlenie strony rozwiązania **Start-Stop-VM [obszar roboczy]** . W tym miejscu możesz przejrzeć ważne szczegóły, takie jak kafelek **StartStopVM** . Tak jak w obszarze roboczym Log Analytics, ten kafelek przedstawia liczbę i graficzną reprezentację zadań elementu Runbook dla rozwiązania, które zostały uruchomione i zakończyły się pomyślnie.

![Strona rozwiązania Update Management Automation](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

W tym miejscu możesz wykonać dalsze analizy rekordów zadań, klikając kafelek pierścieniowy. Pulpit nawigacyjny rozwiązania przedstawia historię zadań i wstępnie zdefiniowane zapytania przeszukiwania dzienników. Przejdź do portalu zaawansowanego usługi log Analytics, aby wyszukiwać na podstawie zapytań wyszukiwania.

## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

Aby zmienić powiadomienia e-mail po wdrożeniu rozwiązania, należy zmodyfikować grupę akcji utworzoną podczas wdrażania.  

> [!NOTE]
> Subskrypcje w chmurze Azure Government nie obsługują funkcji poczty e-mail tego rozwiązania.

W Azure Portal przejdź do > grupy akcji monitorowanie. Wybierz grupę akcji zatytułowaną **StartStop_VM_Notication**.

![Strona rozwiązania Update Management Automation](media/automation-solution-vm-management/azure-monitor.png)

Na stronie **StartStop_VM_Notification** kliknij pozycję **Edytuj szczegóły** w obszarze **szczegóły**. Spowoduje to otwarcie strony **wiadomości e-mail/SMS/wypychania/głosu** . Zaktualizuj adres e-mail, a następnie kliknij przycisk **OK** , aby zapisać zmiany.

![Strona rozwiązania Update Management Automation](media/automation-solution-vm-management/change-email.png)

Alternatywnie możesz dodać dodatkowe akcje do grupy akcji, aby dowiedzieć się więcej na temat grup akcji, zobacz [grupy akcji](../azure-monitor/platform/action-groups.md)

Poniżej przedstawiono przykładową wiadomość e-mail, która jest wysyłana, gdy rozwiązanie zamknie maszyny wirtualne.

![Strona rozwiązania Update Management Automation](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>Dodawanie/wykluczanie maszyn wirtualnych

Rozwiązanie zapewnia możliwość dodawania maszyn wirtualnych, których celem jest rozwiązanie, lub wykluczanie maszyn z rozwiązania.

### <a name="add-a-vm"></a>Dodawanie maszyny wirtualnej

Istnieje kilka opcji, których można użyć, aby upewnić się, że maszyna wirtualna jest uwzględniona w rozwiązaniu uruchamiania/zatrzymywania, gdy zostanie uruchomione.

* Każdy z nadrzędnych [elementów Runbook](#runbooks) rozwiązania ma parametr **VMList** . Można przekazać rozdzieloną przecinkami listę nazw maszyn wirtualnych do tego parametru podczas planowania odpowiedniego nadrzędnego elementu Runbook dla danej sytuacji. te maszyny wirtualne zostaną uwzględnione podczas uruchamiania rozwiązania.

* Aby wybrać wiele maszyn wirtualnych, należy ustawić **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames** przy użyciu nazw grup zasobów zawierających maszyny wirtualne, które mają zostać uruchomione lub zatrzymane. Możesz również ustawić tę wartość na `*`, aby rozwiązanie było uruchamiane dla wszystkich grup zasobów w subskrypcji.

### <a name="exclude-a-vm"></a>Wykluczanie maszyny wirtualnej

Aby wykluczyć maszynę wirtualną z rozwiązania, możesz dodać ją do zmiennej **External_ExcludeVMNames** . Ta zmienna jest rozdzielaną przecinkami listą maszyn wirtualnych, które mają zostać wykluczone z rozwiązania uruchamiania/zatrzymywania. Ta lista jest ograniczona do 140 maszyn wirtualnych. Jeśli dodasz więcej niż 140 maszyn wirtualnych do tej listy rozdzielanej przecinkami, maszyny wirtualne, które są wykluczone, mogą zostać przypadkowo uruchomione lub zatrzymane.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modyfikowanie harmonogramów uruchamiania i zamykania

Zarządzanie harmonogramami uruchamiania i zamykania w tym rozwiązaniu odbywa się zgodnie z tymi samymi krokami, które opisano w temacie [Planowanie elementu Runbook w Azure Automation](automation-schedules.md). Musi istnieć osobny harmonogram do uruchamiania i zatrzymywania maszyn wirtualnych.

Obsługiwane jest tylko skonfigurowanie rozwiązania do zatrzymywania maszyn wirtualnych w określonym czasie. W tym scenariuszu po prostu utworzysz harmonogram **zatrzymywania** i nie ma odpowiadającego jej **uruchomienia** . W tym celu należy:

1. Upewnij się, że dodano grupy zasobów do maszyn wirtualnych, które mają zostać zamknięte w zmiennej **External_Stop_ResourceGroupNames** .
2. Utwórz własny harmonogram dla czasu, w którym chcesz zamknąć maszyny wirtualne.
3. Przejdź do elementu Runbook **ScheduledStartStop_Parent** , a następnie kliknij pozycję **harmonogram**. Umożliwia to wybranie harmonogramu utworzonego w poprzednim kroku.
4. Wybierz **Parametry i Uruchom ustawienia** i ustaw parametr akcji na wartość "Zatrzymaj".
5. Kliknij przycisk **OK**, aby zapisać zmiany.

## <a name="update-the-solution"></a>Aktualizowanie rozwiązania

Jeśli wdrożono poprzednią wersję tego rozwiązania, należy najpierw usunąć ją z konta przed wdrożeniem zaktualizowanej wersji. Postępuj zgodnie z instrukcjami, aby [usunąć rozwiązanie](#remove-the-solution) , a następnie postępuj zgodnie z powyższymi krokami, aby [wdrożyć rozwiązanie](#deploy-the-solution).

## <a name="remove-the-solution"></a>Usuń rozwiązanie

Jeśli zdecydujesz, że nie musisz już korzystać z rozwiązania, możesz je usunąć z konta usługi Automation. Usunięcie rozwiązania spowoduje usunięcie tylko elementów Runbook. Nie powoduje usunięcia harmonogramów ani zmiennych, które zostały utworzone podczas dodawania rozwiązania. Te zasoby należy usunąć ręcznie, jeśli nie są używane z innymi elementami Runbook.

Aby usunąć rozwiązanie, wykonaj następujące czynności:

1. Na koncie usługi Automation w obszarze **powiązane zasoby**wybierz pozycję **połączony obszar roboczy**.
1. Wybierz pozycję **Przejdź do obszaru roboczego**.
1. W obszarze **Ogólne**wybierz pozycję **rozwiązania**. 
1. Na stronie **rozwiązania** wybierz pozycję **Start-Stop-VM [obszar roboczy]** rozwiązania. Na stronie **VMManagementSolution [obszar roboczy]** z menu wybierz pozycję **Usuń**.<br><br> @no__t — rozwiązanie do zarządzania MASZYNami wirtualnymi 0Delete @ no__t-1
1. W oknie **Usuń rozwiązanie** Potwierdź, że chcesz usunąć rozwiązanie.
1. Gdy informacje są weryfikowane i rozwiązanie zostało usunięte, możesz śledzić postęp w obszarze **powiadomienia** z menu. Po zakończeniu procesu usuwania rozwiązania nastąpi powrót do strony **rozwiązania** .

Konto usługi Automation i obszar roboczy Log Analytics nie są usuwane w ramach tego procesu. Jeśli nie chcesz zachować obszaru roboczego Log Analytics, musisz go ręcznie usunąć. Można to zrobić z poziomu Azure Portal:

1. Na ekranie głównym Azure Portal wybierz pozycję **log Analytics obszary robocze**.
1. Na stronie **obszary robocze log Analytics** wybierz obszar roboczy.
1. Na stronie Ustawienia obszaru roboczego wybierz pozycję **Usuń** z menu.

Jeśli nie chcesz zachować składników konta Azure Automation, możesz usunąć je ręcznie. Aby uzyskać listę elementów Runbook, zmiennych i harmonogramów utworzonych przez rozwiązanie, zobacz [składniki rozwiązania](#solution-components).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat tworzenia różnych zapytań wyszukiwania i przeglądania dzienników zadań usługi Automation z dziennikami Azure Monitor, zobacz [Wyszukiwanie w dzienniku w](../log-analytics/log-analytics-log-searches.md)dziennikach Azure monitor.
- Aby dowiedzieć się więcej o wykonywaniu elementów runbook, sposobie monitorowania zadań elementów runbook i innych szczegółach technicznych, zobacz [Track a runbook job](automation-runbook-execution.md) (Śledzenie zadania elementu runbook).
- Aby dowiedzieć się więcej na temat dzienników Azure Monitor i źródeł zbierania danych, zobacz [zbieranie danych usługi Azure Storage w dziennikach Azure monitor Omówienie](../azure-monitor/platform/collect-azure-metrics-logs.md).
