---
title: Uruchamianie/zatrzymywania maszyn wirtualnych poza godzinami pracy
description: To rozwiązanie do zarządzania maszynami wirtualnymi uruchamia i zatrzymuje maszyny wirtualne usługi Azure Resource Manager zgodnie z harmonogramem i proaktywnie monitoruje dzienniki usługi Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: cbf181b9a6d3860854c7b61cca0e6c50810cced9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278547"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Rozwiązanie umożliwiające uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu w usłudze Azure Automation

Maszyny wirtualne Start/Stop podczas uruchamiania rozwiązania poza godzinami pracy i zatrzymuje maszyny wirtualne platformy Azure zgodnie z harmonogramami zdefiniowanymi przez użytkownika, udostępnia szczegółowe informacje za pośrednictwem dzienników usługi Azure Monitor i wysyła opcjonalne wiadomości e-mail przy użyciu [grup akcji.](../azure-monitor/platform/action-groups.md) Obsługuje zarówno usługi Azure Resource Manager, jak i klasyczne maszyny wirtualne dla większości scenariuszy. Aby użyć tego rozwiązania z klasycznymi maszynami wirtualnymi, potrzebujesz klasycznego konta RunAs, które nie jest tworzone domyślnie. Aby uzyskać instrukcje dotyczące tworzenia klasycznego konta RunAs, zobacz [Klasyczne konta uruchamiane jako](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> Maszyny wirtualne Start/Stop podczas poza godzinami pracy zostały przetestowane przy testach modułów platformy Azure, które są importowane do konta automatyzacji podczas wdrażania rozwiązania. Rozwiązanie obecnie nie działa z nowszymi wersjami modułu platformy Azure. Dotyczy to tylko konta automatyzacji, którego używasz do uruchamiania maszyn wirtualnych Start/Stop podczas rozwiązania poza godzinami pracy. Nadal można używać nowszych wersji modułu Platformy Azure na innych kontach automatyzacji, zgodnie z opisem w [jak zaktualizować moduły usługi Azure PowerShell w usłudze Azure Automation](automation-update-azure-modules.md)

To rozwiązanie zapewnia zdecentralizowaną opcję automatyzacji o niskich kosztach dla użytkowników, którzy chcą zoptymalizować swoje koszty maszyny Wirtualnej. Dzięki temu rozwiązaniu możesz:

- Zaplanuj uruchamianie i zatrzymywania maszyn wirtualnych.
- Zaplanuj maszyny wirtualne, aby rozpocząć i zatrzymać w kolejności rosnącej przy użyciu tagów platformy Azure (nie są obsługiwane dla klasycznych maszyn wirtualnych).
- Autostop maszyn wirtualnych na podstawie niskiego użycia procesora CPU.

Poniżej przedstawiono ograniczenia dotyczące bieżącego rozwiązania:

- To rozwiązanie zarządza maszynami wirtualnymi w dowolnym regionie, ale może być używane tylko w tej samej subskrypcji co konto usługi Azure Automation.
- To rozwiązanie jest dostępne na platformie Azure i AzureGov w dowolnym regionie, który obsługuje obszar roboczy usługi Log Analytics, konto usługi Azure Automation i alerty. Regiony AzureGov obecnie nie obsługują funkcji poczty e-mail.

> [!NOTE]
> Jeśli używasz rozwiązania dla klasycznych maszyn wirtualnych, wszystkie maszyny wirtualne będą przetwarzane sekwencyjnie na usługę w chmurze. Maszyny wirtualne są nadal przetwarzane równolegle w różnych usługach w chmurze. Jeśli masz więcej niż 20 maszyn wirtualnych na usługę w chmurze, zaleca się utworzenie wielu harmonogramów z **nadrzędnym elementem** runbook ScheduledStartStop_Parent i określić 20 maszyn wirtualnych na harmonogram. We właściwościach harmonogramu określ jako listę oddzieloną przecinkami nazwy maszyn wirtualnych w parametrze **VMList.** W przeciwnym razie jeśli zadanie automatyzacji dla tego rozwiązania działa dłużej niż trzy godziny, jest tymczasowo zwalniane lub zatrzymywane zgodnie z limitem [sprawiedliwego udziału.](automation-runbook-execution.md#fair-share)
>
> Azure Cloud Solution Provider (Azure CSP) subscriptions support only the Azure Resource Manager model, non-Azure Resource Manager services are not available in the program. Po uruchomieniu rozwiązania Start/Stop mogą pojawić się błędy, ponieważ ma polecenia cmdlet do zarządzania zasobami klasycznymi. Aby dowiedzieć się więcej o CSP, zobacz [Dostępne usługi w subskrypcjach CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Jeśli używasz subskrypcji CSP, należy zmodyfikować zmienną [**External_EnableClassicVMs**](#variables) do **False** po wdrożeniu.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Podręczniki dla tego rozwiązania działają z [kontem Azure Run As](automation-create-runas-account.md). Konto Uruchom jako jest preferowaną metodą uwierzytelniania, ponieważ używa uwierzytelniania certyfikatu zamiast hasła, które może często wygasać lub zmieniać.

Zaleca się użycie oddzielnego konta automatyzacji dla rozwiązania Start/Stop VM. Dzieje się tak, ponieważ wersje modułów platformy Azure są często uaktualniane, a ich parametry mogą ulec zmianie. Rozwiązanie start/stop maszyny wirtualnej nie jest uaktualniane w tym samym rytmie, więc może nie działać z nowszymi wersjami poleceń cmdlet, których używa. Przed zaimportowanie ich na konta automatyzacji w produkcji zalecamy również przetestowanie aktualizacji modułu na koncie automatyzacji testowego.

### <a name="permissions-needed-to-deploy"></a>Uprawnienia potrzebne do wdrożenia

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

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Wykonaj następujące kroki, aby dodać maszyny wirtualne Start/Stop podczas poza godzinami pracy do konta automatyzacji, a następnie skonfiguruj zmienne, aby dostosować rozwiązanie.

1. Z konta automatyzacji wybierz pozycję **Start/Stop VM** w obszarze **Zasoby pokrewne**. W tym miejscu możesz kliknąć dowiedz **się więcej o rozwiązaniu i włączyć je**. Jeśli masz już wdrożone rozwiązanie Start/Stop VM, możesz je zaznaczyć, klikając **pozycję Zarządzaj rozwiązaniem** i znajdując je na liście.

   ![Włącz z konta automatyzacji](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Można go również utworzyć z dowolnego miejsca w witrynie Azure portal, klikając pozycję **Utwórz zasób**. Na stronie Marketplace wpisz słowo kluczowe, takie jak **Start** lub **Start/Stop**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Alternatywnie można wpisać jedno lub więcej słów kluczowych z pełnej nazwy rozwiązania, a następnie nacisnąć klawisz Enter. Z wyników wyszukiwania **wybierz pozycję Start/Stop VMs poza godzinami** pracy.

2. Na stronie **Start/Stop VMs poza godzinami pracy** dla wybranego rozwiązania przejrzyj informacje podsumowujące, a następnie kliknij przycisk **Utwórz**.

   ![Portal Azure](media/automation-solution-vm-management/azure-portal-01.png)

3. Zostanie wyświetlona strona **Dodaj rozwiązanie.** Zostanie wyświetlony monit o skonfigurowanie rozwiązania, zanim będzie można zaimportować je do subskrypcji automatyzacji.

   ![Strona Dodaj rozwiązanie dodawania do zarządzania maszynami wirtualnymi](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Na stronie **Dodawanie rozwiązania** wybierz pozycję **Obszar roboczy**. Wybierz obszar roboczy usługi Log Analytics, który jest połączony z tą samą subskrypcją platformy Azure, w których znajduje się konto automatyzacji. Jeśli nie masz obszaru roboczego, wybierz pozycję **Utwórz nowy obszar roboczy**. Na stronie **obszaru roboczego usługi Log Analytics** wykonaj następujące czynności:
   - Określ nazwę nowego **obszaru roboczego usługi Log Analytics,** takiego jak "ContosoLAWorkspace".
   - Wybierz **subskrypcję,** do z aby utworzyć łącze, wybierając z listy rozwijanej, jeśli wybrana wartość domyślna nie jest odpowiednia.
   - W przypadku **grupy zasobów**można utworzyć nową grupę zasobów lub wybrać istniejącą.
   - Wybierz **lokalizację**.
   - Wybierz **warstwę cenową**. Wybierz opcję **Na GB (autonomiczny).** Dzienniki usługi Azure Monitor mają [zaktualizowane ceny,](https://azure.microsoft.com/pricing/details/log-analytics/) a warstwa Na GB jest jedyną opcją.

   > [!NOTE]
   > W przypadku włączenia rozwiązań tylko w niektórych regionach jest obsługiwane łączenie obszaru roboczego usługi Log Analytics i konta usługi Automation.
   >
   > Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionu dla obszaru roboczego Konto automatyzacji i Usługa Analizy dzienników](how-to/region-mappings.md).

5. Po podaniu wymaganych informacji na stronie **obszaru roboczego usługi Log Analytics** kliknij przycisk **Utwórz**. Jego postęp można śledzić w obszarze **Powiadomienia** z menu, które zwraca się do strony **Dodaj rozwiązanie** po zakończeniu.
6. Na stronie **Dodawanie rozwiązania** wybierz pozycję **Konto automatyzacji**. Jeśli tworzysz nowy obszar roboczy usługi Log Analytics, możesz utworzyć nowe konto automatyzacji, które ma być z nim skojarzone, lub wybrać istniejące konto automatyzacji, które nie jest jeszcze połączone z obszarem roboczym usługi Log Analytics. Wybierz istniejące konto automatyzacji lub kliknij pozycję **Utwórz konto automatyzacji,** a na stronie **Dodaj konto automatyzacji** podaj następujące informacje:
   - W polu **Nazwa** wprowadź nazwę konta usługi Automation.

     Wszystkie inne opcje są automatycznie wypełniane na podstawie wybranego obszaru roboczego usługi Log Analytics. Tych opcji nie można modyfikować. Konto Uruchom jako platformy Azure jest domyślną metodą uwierzytelniania dla elementów Runbook zawartych w tym rozwiązaniu. Po kliknięciu przycisku **OK**opcje konfiguracji zostaną zweryfikowane i zostanie utworzone konto Automatyzacja. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

7. Na koniec na stronie **Dodawanie rozwiązania** wybierz pozycję **Konfiguracja**. Zostanie wyświetlona strona **Parametry.**

   ![Strona Parametry dla rozwiązania](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   W tym miejscu zostanie wyświetlony monit o:
   - Określ **docelowe nazwy grup zasobów**. Te wartości są nazwy grup zasobów, które zawierają maszyny wirtualne, które mają być zarządzane przez to rozwiązanie. Można wprowadzić więcej niż jedną nazwę i oddzielić każdą za pomocą przecinka (w wartościach nie rozróżnia się wielkość liter). Użycie symbolu wieloznacznego jest obsługiwane. Możesz skorzystać z tej opcji, jeśli chcesz uwzględnić maszyny wirtualne we wszystkich grupach zasobów w subskrypcji. Ta wartość jest przechowywana w **zmiennych External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames.**
   - Określ **listę wykluczeń maszyn wirtualnych (ciąg)**. Ta wartość jest nazwą co najmniej jednej maszyny wirtualnej z docelowej grupy zasobów. Można wprowadzić więcej niż jedną nazwę i oddzielić każdą za pomocą przecinka (w wartościach nie rozróżnia się wielkość liter). Korzystanie z symbolu wieloznacznego jest obsługiwane. Ta wartość jest przechowywana w **zmiennej External_ExcludeVMNames.**
   - Wybierz **harmonogram**. Wybierz datę i godzinę dla swojego harmonogramu. Ponownie powtarzającym się harmonogramem dziennym zostanie utworzony, począwszy od wybranego czasu. Wybranie innego regionu jest niedostępne. Aby skonfigurować harmonogram do określonej strefy czasowej po skonfigurowaniu rozwiązania, zobacz [Modyfikowanie harmonogramu uruchamiania i zamykania](#modify-the-startup-and-shutdown-schedules).
   - Aby otrzymywać **powiadomienia e-mail** od grupy akcji, zaakceptuj domyślną wartość **Tak** i podaj prawidłowy adres e-mail. Jeśli wybierzesz **opcję Nie,** ale w późniejszym terminie zdecydujesz, że chcesz otrzymywać powiadomienia e-mail, możesz zaktualizować [grupę akcji](../azure-monitor/platform/action-groups.md) utworzoną przy obliczu prawidłowych adresów e-mail oddzielonych przecinkiem. Należy również włączyć następujące reguły alertów:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Wartością domyślną dla nazw **&ast;** grup zasobów **docelowych** jest . Dotyczy to wszystkich maszyn wirtualnych w ramach subskrypcji. Jeśli nie chcesz, aby rozwiązanie docelowe wszystkie maszyny wirtualne w subskrypcji tej wartości musi zostać zaktualizowany do listy nazw grup zasobów przed włączeniem harmonogramów.

8. Po skonfigurowaniu ustawień początkowych wymaganych dla rozwiązania kliknij przycisk **OK,** aby zamknąć stronę **Parametry,** a następnie wybierz pozycję **Utwórz**. Po zatwierdzeniu wszystkich ustawień rozwiązanie jest wdrażane w ramach subskrypcji. Ten proces może potrwać kilka sekund, aby zakończyć i można śledzić jego **postępy** w obszarze Powiadomienia z menu.

> [!NOTE]
> Jeśli masz subskrypcję dostawcy rozwiązań w chmurze Azure (Azure CSP), po zakończeniu wdrażania, na **koncie** automatyzacji przejdź do zmiennych w obszarze **Zasoby udostępnione** i ustaw zmienną [**External_EnableClassicVMs**](#variables) na **False**. To zatrzymuje rozwiązanie od szukania zasobów klasycznej maszyny Wirtualnej.

## <a name="scenarios"></a>Scenariusze

Rozwiązanie zawiera trzy różne scenariusze. Te scenariusze są następujące:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scenariusz 1: Uruchamianie/zatrzymywania maszyn wirtualnych zgodnie z harmonogramem

W tym scenariuszu jest konfiguracja domyślna podczas pierwszego wdrożenia rozwiązania. Na przykład można skonfigurować go, aby zatrzymać wszystkie maszyny wirtualne w ramach subskrypcji po opuszczeniu pracy w godzinach wieczornych i uruchomić je rano, gdy jesteś z powrotem w biurze. Podczas konfigurowania harmonogramów **Scheduled-StartVM** i **Scheduled-StopVM** podczas wdrażania, uruchamiają i zatrzymują ukierunkowane maszyny wirtualne. Konfigurowanie tego rozwiązania tak, aby po prostu zatrzymać maszyny wirtualne jest obsługiwane, zobacz [Modyfikowanie harmonogramów uruchamiania i zamykania,](#modify-the-startup-and-shutdown-schedules) aby dowiedzieć się, jak skonfigurować harmonogram niestandardowy.

> [!NOTE]
> Strefa czasowa jest bieżącą strefą czasową podczas konfigurowania parametru czasu harmonogramu. Jednak jest przechowywany w formacie UTC w usłudze Azure Automation. Nie trzeba wykonywać żadnej konwersji strefy czasowej, ponieważ jest to obsługiwane podczas wdrażania.

Można kontrolować, które maszyny wirtualne są w zakresie, konfigurując następujące zmienne: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**i **External_ExcludeVMNames**.

Można włączyć kierowanie akcji względem subskrypcji i grupy zasobów lub kierowanie na określoną listę maszyn wirtualnych, ale nie obie.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Kierowanie akcji początkowych i zatrzymania względem subskrypcji i grupy zasobów

1. Skonfiguruj **zmienne External_Stop_ResourceGroupNames** i **External_ExcludeVMNames,** aby określić docelowe maszyny wirtualne.
2. Włącz i zaktualizuj **harmonogramy Scheduled-StartVM** i **Scheduled-StopVM.**
3. Uruchom **ScheduledStartStop_Parent** runbook z ustawionym parametrem ACTION **i** parametrem WHATIF ustawionym na **True,** aby wyświetlić podgląd zmian.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Kierowanie akcji początkowej i stop przez listę maszyn wirtualnych

1. Uruchom **ScheduledStartStop_Parent** runbook z parametrem ACTION ustawionym na **rozpoczęcie,** dodaj listę maszyn wirtualnych oddzielonych przecinkami w parametrze *VMList,* a następnie ustaw parametr WHATIF na **True**. Podgląd zmian.
1. Skonfiguruj parametr **External_ExcludeVMNames** za pomocą listy maszyn wirtualnych (VM1, VM2, VM3).
1. Ten scenariusz nie jest honorowane **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupnames** zmiennych. W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać szczegółowe informacje, zobacz [Planowanie uruchomieniu księgi ą w usłudze Azure Automation](../automation/automation-schedules.md).

> [!NOTE]
> Wartość **nazw grup zasobów docelowych** jest przechowywana jako wartość zarówno dla **External_Start_ResourceGroupNames,** jak i **External_Stop_ResourceGroupNames**. Aby uzyskać dalszą szczegółowość, można zmodyfikować każdą z tych zmiennych, aby kierować reklamy na różne grupy zasobów. Aby rozpocząć akcję, użyj **External_Start_ResourceGroupNames**, a do zatrzymania akcji użyj **External_Stop_ResourceGroupNames**. Maszyny wirtualne są automatycznie dodawane do harmonogramów uruchamiania i zatrzymywania.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scenariusz 2: Uruchamianie/zatrzymywać maszyny wirtualne w sekwencji przy użyciu tagów

W środowisku, które zawiera dwa lub więcej składników na wielu maszynach wirtualnych obsługujących obciążenie rozproszone, obsługa sekwencji, w której składniki są uruchamiane i zatrzymywane w kolejności, jest ważne. Ten scenariusz można wykonać, wykonując następujące kroki:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Kierowanie akcji początkowych i zatrzymania względem subskrypcji i grupy zasobów

1. Dodaj **sequencestart** i **sequencestop** tag z dodatnią wartością całkowitą do maszyn wirtualnych, które są ukierunkowane **na External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames** zmiennych. Akcje start i stop są wykonywane w porządku rosnącym. Aby dowiedzieć się, jak oznaczyć maszynę wirtualną, zobacz [Oznaczanie maszyny wirtualnej systemu Windows na platformie Azure](../virtual-machines/windows/tag.md) i [oznaczanie maszyny wirtualnej systemu Linux na platformie Azure](../virtual-machines/linux/tag.md).
1. Zmodyfikuj harmonogramy **Sequenced-StartVM** i **Sequenced-StopVM** do daty i godziny, które spełniają twoje wymagania i włącz harmonogram.
1. Uruchom **SequencedStartStop_Parent** runbook z ustawionym parametrem ACTION **i** parametrem WHATIF ustawionym na **True,** aby wyświetlić podgląd zmian.
1. Podgląd akcji i wprowadzić wszelkie niezbędne zmiany przed wdrożeniem przeciwko maszynom wirtualnym produkcji. Gdy jest to gotowe, ręcznie wykonaj runbook z parametrem **ustawionym**na False lub pozwól, aby harmonogram automatyzacji **Sequenced-StartVM** i **Sequenced-StopVM** działał automatycznie zgodnie z określonym harmonogramem.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Kierowanie akcji początkowej i stop przez listę maszyn wirtualnych

1. Dodaj **sequencestart** i **sequencestop** tag z dodatnią wartością całkowitą do maszyn wirtualnych, które zamierzasz dodać do **VMList** parametru.
1. Uruchom **SequencedStartStop_Parent** runbook z parametrem ACTION ustawionym na **rozpoczęcie,** dodaj listę maszyn wirtualnych oddzielonych przecinkami w parametrze *VMList,* a następnie ustaw parametr WHATIF na **True**. Podgląd zmian.
1. Skonfiguruj parametr **External_ExcludeVMNames** za pomocą listy maszyn wirtualnych (VM1, VM2, VM3).
1. Ten scenariusz nie jest honorowane **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupnames** zmiennych. W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać szczegółowe informacje, zobacz [Planowanie uruchomieniu księgi ą w usłudze Azure Automation](../automation/automation-schedules.md).
1. Podgląd akcji i wprowadzić wszelkie niezbędne zmiany przed wdrożeniem przeciwko maszynom wirtualnym produkcji. Gdy jest to gotowe, ręcznie wykonaj monitorowanie i diagnostykę/monitorowanie-action-groupsrunbook z parametrem **ustawionym**na False lub pozwól harmonogramowi automatyzacji **Sequenced-StartVM** i **Sequenced-StopVM** działać automatycznie zgodnie z określonym harmonogramem.

## <a name="solution-components"></a>Składniki rozwiązania

To rozwiązanie obejmuje wstępnie skonfigurowane elementy runbook, harmonogramy i integrację z dziennikami usługi Azure Monitor, dzięki czemu można dostosować uruchamianie i zamykanie maszyn wirtualnych do potrzeb biznesowych.

### <a name="runbooks"></a>Elementy Runbook

W poniższej tabeli wymieniono elementy runbook wdrożone na koncie automatyzacji przez to rozwiązanie. Nie należy wprowadzać zmian w kodzie uruchomieniu. . Zamiast tego napisz własny podręcznik runbook dla nowych funkcji.

> [!IMPORTANT]
> Nie należy bezpośrednio uruchamiać żadnego umnienia z "child" dołączone do jego nazwy.

Wszystkie nadrzędne elementy runbook zawierają parametr _WhatIf._ Po ustawieniu **true**, _WhatIf_ obsługuje szczegółowo dokładne zachowanie runbook trwa podczas uruchamiania bez _WhatIf_ parametru i sprawdza poprawność poprawne maszyny wirtualne są kierowane. Obiekt runbook wykonuje tylko zdefiniowane akcje, gdy parametr _WhatIf_ jest ustawiony na **False**.

|Element Runbook | Parametry | Opis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject (100) <br> Powiadomienie <br> Identyfikator elementów WebHookURI | Wywoływana z nadrzędnego egonatu. Ten element runbook tworzy alerty na podstawie dla zasobu dla scenariusza Autostop.|
|AutoStop_CreateAlert_Parent | Lista maszyn wirtualnych<br> CoIf: Prawda lub Fałsz  | Tworzy lub aktualizuje reguły alertów platformy Azure na maszynach wirtualnych w grupach subskrypcji lub zasobów docelowych. <br> Lista maszyn wirtualnych: oddzielona przecinkami lista maszyn wirtualnych. Na przykład _vm1, vm2, vm3_.<br> *WhatIf* sprawdza poprawność logiki uruchomieniu bez wykonywania.|
|AutoStop_Disable | brak | Wyłącza alerty Autostop i domyślny harmonogram.|
|AutoStop_StopVM_Child | Element WebHookData | Wywoływana z nadrzędnego egonatu. Reguły alertów wywołać ten projekt uruchamiany, aby zatrzymać maszynę wirtualną.|
|Bootstrap_Main | brak | Używane jeden raz do konfigurowania konfiguracji bootstrap, takich jak webhookURI, które zazwyczaj nie są dostępne z usługi Azure Resource Manager. Ten projekt runbook jest usuwany automatycznie po pomyślnym wdrożeniu.|
|ScheduledStartStop_Child | VMName <br> Działanie: Uruchamianie lub zatrzymywać <br> ResourceGroupName | Wywoływana z nadrzędnego egonatu. Wykonuje akcję rozpoczęcia lub zatrzymania dla zaplanowanego zatrzymania.|
|ScheduledStartStop_Parent | Działanie: Uruchamianie lub zatrzymywać <br>Lista maszyn wirtualnych <br> CoIf: Prawda lub Fałsz | To ustawienie ma wpływ na wszystkie maszyny wirtualne w subskrypcji. Edytuj **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames,** aby wykonywać tylko w tych docelowych grupach zasobów. Można również wykluczyć określonych maszyn wirtualnych, aktualizując **zmienną External_ExcludeVMNames.**<br> Lista maszyn wirtualnych: oddzielona przecinkami lista maszyn wirtualnych. Na przykład _vm1, vm2, vm3_.<br> _WhatIf_ sprawdza poprawność logiki uruchomieniu bez wykonywania.|
|SequencedStartStop_Parent | Działanie: Uruchamianie lub zatrzymywać <br> CoIf: Prawda lub Fałsz<br>Lista maszyn wirtualnych| Utwórz znaczniki o nazwie **sequencestart** i **sequencestop** na każdej maszynie wirtualnej, dla której chcesz sekwencjonować działanie start/stop. W tych nazwach tagów rozróżniana jest wielkość liter. Wartość znacznika powinna być dodatnią liczą całkowitą (1, 2, 3), która odpowiada kolejności, w jakiej chcesz rozpocząć lub zatrzymać. <br> Lista maszyn wirtualnych: oddzielona przecinkami lista maszyn wirtualnych. Na przykład _vm1, vm2, vm3_. <br> _WhatIf_ sprawdza poprawność logiki uruchomieniu bez wykonywania. <br> **Uwaga:** Maszyny wirtualne muszą znajdować się w grupach zasobów zdefiniowanych jako External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames i External_ExcludeVMNames w zmiennych usługi Azure Automation. Muszą mieć odpowiednie tagi, aby akcje zaczęły obowiązywać.|

### <a name="variables"></a>Zmienne

W poniższej tabeli wymieniono zmienne utworzone na koncie automatyzacji. Modyfikuj tylko zmienne poprzedzone **zewnętrznymi**. Modyfikowanie zmiennych poprzedzonych **wewnętrznymi** powoduje niepożądane skutki.

|Zmienna | Opis|
|---------|------------|
|External_AutoStop_Condition | Operator warunkowy wymagany do skonfigurowania warunku przed wyzwoleniem alertu. Dopuszczalne wartości to **GreaterThan**, **GreaterThanEqual**, **LessThan**i **LessThanEqual**.|
|External_AutoStop_Description | Alert, aby zatrzymać maszynę wirtualną, jeśli procent procesora CPU przekracza próg.|
|External_AutoStop_MetricName | Nazwa metryki wydajności, dla której ma być skonfigurowana reguła usługi Azure Alert.|
|External_AutoStop_Threshold | Próg dla reguły usługi Azure Alert określony w _zmiennej External_AutoStop_MetricName_. Wartości procentowe mogą wynosić od 1 do 100.|
|External_AutoStop_TimeAggregationOperator | Operator agregacji czasu, który jest stosowany do wybranego rozmiaru okna w celu oceny warunku. Dopuszczalne wartości **to Średnia,** **Minimalna,** **Maksymalna,** **Całkowita**i **Ostatnia**.|
|External_AutoStop_TimeWindow | Rozmiar okna, podczas którego platforma Azure analizuje wybrane metryki do wyzwalania alertu. Ten parametr akceptuje dane wejściowe w formacie timespan. Możliwe wartości to od 5 minut do 6 godzin.|
|External_EnableClassicVMs| Określa, czy klasyczne maszyny wirtualne są kierowane przez rozwiązanie. Wartością domyślną jest True. To powinno być ustawione na False dla subskrypcji CSP. Klasyczne maszyny wirtualne wymagają [klasycznego konta run-as.](automation-create-standalone-account.md#classic-run-as-accounts)|
|External_ExcludeVMNames | Wprowadź nazwy maszyn wirtualnych, które mają zostać wykluczone, oddzielając nazwy za pomocą przecinka bez spacji. Jest to ograniczone do 140 maszyn wirtualnych. Jeśli dodasz więcej niż 140 maszyn wirtualnych do tej listy oddzielone przecinkami, maszyny wirtualne, które są ustawione do wykluczenia, mogą zostać przypadkowo uruchomione lub zatrzymane.|
|External_Start_ResourceGroupNames | Określa jedną lub więcej grup zasobów, oddzielając wartości za pomocą przecinka, przeznaczonych do akcji początkowych.|
|External_Stop_ResourceGroupNames | Określa jedną lub więcej grup zasobów, oddzielając wartości za pomocą przecinka, przeznaczonych dla akcji zatrzymania.|
|Internal_AutomationAccountName | Określa nazwę konta automatyzacji.|
|Internal_AutoSnooze_WebhookUri | Określa identyfikator URI elementu webhook wywoływany dla scenariusza Autostop.|
|Internal_AzureSubscriptionId | Określa identyfikator subskrypcji platformy Azure.|
|Internal_ResourceGroupName | Określa nazwę grupy zasobów konta automatyzacji.|

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

## <a name="azure-monitor-logs-records"></a>Usługa Azure Monitor rejestruje rekordy

Automatyzacja tworzy dwa typy rekordów w obszarze roboczym usługi Log Analytics: dzienniki zadań i strumienie zadań.

### <a name="job-logs"></a>Dzienniki zadań

|Właściwość | Opis|
|----------|----------|
|Obiekt wywołujący |  Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań.|
|Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobLogs.|
|CorrelationId | Identyfikator GUID, który jest identyfikatorem korelacji zadania runbook.|
|JobId | Identyfikator GUID, który jest identyfikatorem zadania runbook.|
|operationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku automatyzacji wartością jest Zadanie.|
|resourceId | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|ResourceGroup | Określa nazwę grupy zasobów zadania elementu Runbook.|
|ResourceProvider | Określa nazwę usługi platformy Azure, która zapewnia zasoby do wdrożenia i zarządzania. W przypadku usługi Automation wartością jest Azure Automation.|
|ResourceType | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|resultType | Stan zadania elementu Runbook. Możliwe wartości:<br>— Uruchomione<br>— Zatrzymane<br>— Wstrzymane<br>— Nie powiodło się<br>— Powiodło się|
|resultDescription | Opisuje stan wyniku zadania elementu Runbook. Możliwe wartości:<br>— Zadanie jest uruchomione<br>— Zadanie nie powiodło się<br>— Zadanie zostało ukończone|
|RunbookName | Określa nazwę elementu Runbook.|
|SourceSystem | Określa system źródłowy dla przesłanych danych. W przypadku automatyzacji wartością jest OpsManager|
|StreamType | Określa typ zdarzenia. Możliwe wartości:<br>— Pełne<br>— Dane wyjściowe<br>— Błąd<br>— Ostrzeżenie|
|SubscriptionId | Określa identyfikator subskrypcji zadania.
|Time | Data i godzina dla wykonania zadania elementu Runbook.|

### <a name="job-streams"></a>Strumienie zadania

|Właściwość | Opis|
|----------|----------|
|Obiekt wywołujący |  Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań.|
|Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobStreams.|
|JobId | Identyfikator GUID, który jest identyfikatorem zadania runbook.|
|operationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku automatyzacji wartością jest Zadanie.|
|ResourceGroup | Określa nazwę grupy zasobów zadania elementu Runbook.|
|resourceId | Określa identyfikator zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|ResourceProvider | Określa nazwę usługi platformy Azure, która zapewnia zasoby do wdrożenia i zarządzania. W przypadku usługi Automation wartością jest Azure Automation.|
|ResourceType | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|resultType | Wynik zadania elementu Runbook w czasie wygenerowania zdarzenia. Możliwa wartość to:<br>— W toku|
|resultDescription | Obejmuje strumień wyjściowy z elementu Runbook.|
|RunbookName | Nazwa elementu Runbook.|
|SourceSystem | Określa system źródłowy dla przesłanych danych. W przypadku automatyzacji wartością jest OpsManager.|
|StreamType | Typ strumienia zadania. Możliwe wartości:<br>- Postępy<br>— Dane wyjściowe<br>— Ostrzeżenie<br>— Błąd<br>— Debugowanie<br>— Pełne|
|Time | Data i godzina dla wykonania zadania elementu Runbook.|

Podczas wyszukiwania dzienników, które zwraca rekordy kategorii **JobLogs** lub **JobStreams**, można wybrać **JobLogs** lub **JobStreams** widoku, który wyświetla zestaw kafelków podsumowujących aktualizacje zwrócone przez wyszukiwanie.

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla rekordów dzienników zbieranych przez to rozwiązanie.

|Zapytanie | Opis|
|----------|----------|
|Znajdowanie zadań dla ScheduledStartStop_Parent y śmięty, które zakończyły się pomyślnie | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Znajdowanie zadań dla SequencedStartStop_Parent y śmięty, które zakończyły się pomyślnie | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>Wyświetlanie rozwiązania

Aby uzyskać dostęp do rozwiązania, przejdź do konta automatyzacji, wybierz obszar **roboczy** w obszarze **POWIĄZANE ZASOBY**. Na stronie analizy dzienników wybierz pozycję **Rozwiązania** w obszarze **OGÓLNE**. Na stronie **Rozwiązania** wybierz rozwiązanie **Start-Stop-VM[workspace]** z listy.

Wybranie rozwiązania powoduje wyświetlenie strony rozwiązania **Start-Stop-Stop-VM[obszar roboczy].** W tym miejscu można przejrzeć ważne szczegóły, takie jak kafelek **StartStopVM.** Podobnie jak w obszarze roboczym usługi Log Analytics, ten kafelek wyświetla liczbę i graficzną reprezentację zadań runbook dla rozwiązania, które zostały uruchomione i zakończone pomyślnie.

![Strona rozwiązania do zarządzania aktualizacjami automatyzacji](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

W tym miejscu można przeprowadzić dalszą analizę rekordów zadań, klikając kafelek pierścienia. Pulpit nawigacyjny rozwiązania zawiera historię zadań i wstępnie zdefiniowane zapytania wyszukiwania dziennika. Przełącz się do zaawansowanego portalu analizy dzienników, aby wyszukiwać na podstawie zapytań wyszukiwania.

## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

Aby zmienić powiadomienia e-mail po wdrożeniu rozwiązania, zmodyfikuj grupę akcji utworzoną podczas wdrażania.  

> [!NOTE]
> Subskrypcje w chmurze azure dla instytucji rządowych nie obsługują funkcji poczty e-mail tego rozwiązania.

W witrynie Azure portal przejdź do grup akcji Monitor ->. Wybierz grupę akcji zatytułowaną **StartStop_VM_Notication**.

![Strona rozwiązania do zarządzania aktualizacjami automatyzacji](media/automation-solution-vm-management/azure-monitor.png)

Na **stronie StartStop_VM_Notification** kliknij pozycję **Edytuj szczegóły** w obszarze **Szczegóły**. Spowoduje to otwarcie strony **E-mail/SMS/Push/Voice.** Zaktualizuj adres e-mail i kliknij przycisk **OK,** aby zapisać zmiany.

![Strona rozwiązania do zarządzania aktualizacjami automatyzacji](media/automation-solution-vm-management/change-email.png)

Alternatywnie można dodać dodatkowe akcje do grupy akcji, aby dowiedzieć się więcej o grupach akcji, zobacz [grupy akcji](../azure-monitor/platform/action-groups.md)

Poniżej przedstawiono przykładową wiadomość e-mail, która jest wysyłana, gdy rozwiązanie zamyka maszyny wirtualne.

![Strona rozwiązania do zarządzania aktualizacjami automatyzacji](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Dodawanie/wykluczanie maszyn wirtualnych

Rozwiązanie zapewnia możliwość dodawania maszyn wirtualnych, które mają być kierowane przez rozwiązanie lub w szczególności wykluczyć maszyny z rozwiązania.

### <a name="add-a-vm"></a>Dodawanie maszyny wirtualnej

Istnieje kilka opcji, których można użyć, aby upewnić się, że maszyna wirtualna jest uwzględniona w rozwiązaniu Start/Stop po uruchomieniu.

* Każdy z [nadrzędnych kreśleń](#runbooks) rozwiązania ma parametr **VMList.** Można przekazać listę rozdzielonych przecinkami nazw maszyn wirtualnych do tego parametru podczas planowania odpowiedniego nadrzędnego uruchomieniu dla danego przypadku, a te maszyny wirtualne zostaną uwzględnione podczas uruchamiania rozwiązania.

* Aby wybrać wiele maszyn wirtualnych, należy ustawić **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames** z nazwami grup zasobów zawierającymi maszyny wirtualne, które chcesz uruchomić lub zatrzymać. Można również ustawić tę `*`wartość , aby rozwiązanie było uruchamiane dla wszystkich grup zasobów w ramach subskrypcji.

### <a name="exclude-a-vm"></a>Wykluczanie maszyny wirtualnej

Aby wykluczyć maszynę wirtualną z rozwiązania, można dodać ją do **zmiennej External_ExcludeVMNames.** Ta zmienna jest oddzieloną przecinkami listą określonych maszyn wirtualnych do wykluczenia z rozwiązania Start/Stop. Ta lista jest ograniczona do 140 maszyn wirtualnych. Jeśli dodasz więcej niż 140 maszyn wirtualnych do tej listy oddzielone przecinkami, maszyny wirtualne, które są ustawione do wykluczenia, mogą zostać przypadkowo uruchomione lub zatrzymane.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modyfikowanie harmonogramów uruchamiania i zamykania

Zarządzanie harmonogramami uruchamiania i zamykania w tym rozwiązaniu przebiega zgodnie z tymi samymi krokami, które opisano w [obszarze Planowanie wiązki uruchomieniu w usłudze Azure Automation](automation-schedules.md). Musi istnieć oddzielny harmonogram uruchamiania i zatrzymywania maszyn wirtualnych.

Konfigurowanie rozwiązania, aby po prostu zatrzymać maszyny wirtualne w określonym czasie jest obsługiwane. W tym scenariuszu po prostu utworzyć **harmonogram zatrzymania** i nie odpowiednie **Start** zaplanowane. W tym celu należy:

1. Upewnij się, że dodano grupy zasobów dla maszyn wirtualnych, aby zamknąć w **zmiennej External_Stop_ResourceGroupNames.**
2. Utwórz własny harmonogram dla czasu, który chcesz zamknąć maszyny wirtualne.
3. Przejdź do **ScheduledStartStop_Parent** uruchomieniu i kliknij pozycję **Zaplanuj**. Dzięki temu można wybrać harmonogram utworzony w poprzednim kroku.
4. Wybierz **parametry i uruchom ustawienia** i ustaw parametr ACTION na "Stop".
5. Kliknij przycisk **OK**, aby zapisać zmiany.

## <a name="update-the-solution"></a>Aktualizowanie rozwiązania

Jeśli wdrożono poprzednią wersję tego rozwiązania, należy najpierw usunąć go z konta przed wdrożeniem zaktualizowanej wersji. Wykonaj kroki, aby [usunąć rozwiązanie,](#remove-the-solution) a następnie wykonaj powyższe kroki, aby [wdrożyć rozwiązanie](#deploy-the-solution).

## <a name="remove-the-solution"></a>Usuwanie rozwiązania

Jeśli zdecydujesz, że nie trzeba już używać rozwiązania, można go usunąć z konta automatyzacji. Usunięcie rozwiązania usuwa tylko runbooki. Nie powoduje usunięcia harmonogramów ani zmiennych, które zostały utworzone podczas dodawania rozwiązania. Te zasoby, które należy usunąć ręcznie, jeśli nie używasz ich z innymi elementami runbook.

Aby usunąć rozwiązanie, wykonaj następujące czynności:

1. Z konta automatyzacji w obszarze **Powiązane zasoby**wybierz pozycję Połączony **obszar roboczy**.
1. Wybierz **pozycję Przejdź do obszaru roboczego**.
1. W **obszarze Ogólne**wybierz pozycję **Rozwiązania**. 
1. Na stronie **Rozwiązania** wybierz rozwiązanie **Start-Stop-VM[Obszar roboczy].** Na stronie **VMManagementSolution[Workspace]** z menu wybierz polecenie **Usuń**.<br><br> ![Usuń rozwiązanie VM Mgmt](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. W oknie **Usuń rozwiązanie** upewnij się, że chcesz usunąć rozwiązanie.
1. Gdy informacje są weryfikowane, a rozwiązanie jest usuwane, można śledzić jego **postępy** w obszarze Powiadomienia z menu. Po uruchomieniu procesu usuwania rozwiązania zostaniesz przywrócony do strony **Rozwiązania.**

Konto automatyzacji i obszar roboczy usługi Log Analytics nie są usuwane w ramach tego procesu. Jeśli nie chcesz zachować obszaru roboczego usługi Log Analytics, musisz go ręcznie usunąć. Można to osiągnąć za pomocą portalu Azure:

1. W witrynie Azure portal wyszukaj i wybierz **obszary robocze usługi Log Analytics**.
1. Na stronie **Obszary robocze usługi Log Analytics** wybierz obszar roboczy.
1. Z menu na stronie ustawienia obszaru roboczego wybierz **polecenie Usuń.**

Jeśli nie chcesz zachować składniki konta usługi Azure Automation, można ręcznie usunąć każdy z nich. Aby uzyskać listę elementów runbook, zmiennych i harmonogramów utworzonych przez rozwiązanie, zobacz [składniki rozwiązania](#solution-components).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat konstruowania różnych zapytań wyszukiwania i przeglądania dzienników zadań automatyzacji za pomocą dzienników usługi Azure Monitor, zobacz [Dzienniki wyszukiwania w dziennikach usługi Azure Monitor.](../log-analytics/log-analytics-log-searches.md)
- Aby dowiedzieć się więcej o wykonywaniu elementów runbook, sposobie monitorowania zadań elementów runbook i innych szczegółach technicznych, zobacz [Track a runbook job](automation-runbook-execution.md) (Śledzenie zadania elementu runbook).
- Aby dowiedzieć się więcej o dziennikach i źródłach zbierania danych usługi Azure Monitor, zobacz [Zbieranie danych magazynu platformy Azure w omówieniu dzienników usługi Azure Monitor.](../azure-monitor/platform/collect-azure-metrics-logs.md)
