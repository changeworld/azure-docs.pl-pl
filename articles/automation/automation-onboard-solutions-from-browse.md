---
title: Dowiedz się, jak dodawanie rozwiązań zarządzania aktualizacjami, śledzenie zmian i spisu dla wielu maszyn wirtualnych w usłudze Azure Automation
description: Dowiedz się, jak dołączyć Azure maszynę wirtualną z rozwiązaniami do zarządzania aktualizacjami, śledzenie zmian i spisu, które są częścią usługi Azure Automation
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/11/2019
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: e6359d57a1f4cce6ec89fd76ef343b515cafae6e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66133139"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Włączanie rozwiązania Update Management, śledzenia zmian i spis rozwiązań na wielu maszynach wirtualnych

Usługa Azure Automation oferuje rozwiązania do zarządzania aktualizacjami zabezpieczeń systemu operacyjnego, śledzenie zmian i spisu zainstalowanych na komputerach. Istnieje wiele sposobów dołączania maszyn, możesz dołączyć rozwiązanie [z maszyny wirtualnej](automation-onboard-solutions-from-vm.md), z Twojej [konta usługi Automation](automation-onboard-solutions-from-automation-account.md), podczas przeglądania maszyny wirtualne lub przez [elementurunbook](automation-onboard-solutions.md). W tym artykule opisano dołączania do tych rozwiązań podczas przeglądania maszyn wirtualnych na platformie Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure w witrynie https://portal.azure.com

## <a name="enable-solutions"></a>Włączanie rozwiązań

W witrynie Azure portal przejdź do **maszyn wirtualnych**.

Za pomocą pola wyboru, wybierz maszyny wirtualne, które chcesz dołączyć śledzenie zmian i spisu lub rozwiązania Update Management. Dołączania jest dostępna dla maksymalnie trzech różnych grupach zasobów w danym momencie. Maszyny wirtualne platformy Azure może znajdować się w dowolnym regionie, bez względu na to lokalizacja konta usługi Automation.

![Lista maszyn wirtualnych](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Użyj kontrolki filtrowania modyfikacji listy identyfikatorów maszyn wirtualnych, a następnie kliknij przycisk najważniejsze pole wyboru, aby wszystkie maszyny wirtualne na liście.

Na pasku poleceń kliknij **usług** i wybierz opcję **śledzenie zmian**, **spisu**, lub **rozwiązania Update Management**.

> [!NOTE]
> **Śledzenie zmian** i **spisu** używać tego samego rozwiązania, gdy jedna jest włączona innych także włączone.

Poniżej zilustrowano w zarządzania aktualizacjami. Śledzenie zmian i spisu mają ten sam układ i zachowanie.

Listę maszyn wirtualnych jest filtrowana, aby pokazać tylko maszyny wirtualne, które znajdują się w tej samej subskrypcji i lokalizacji. Jeśli maszyny wirtualne znajdują się w więcej niż trzy grupy zasobów, są wybierane pierwszej grupy zasobów.

### <a name="resource-group-limit"></a> Ograniczenia dotyczące dołączania

Liczba grup zasobów, które służy do dołączania jest ograniczona przez [limity wdrażania usługi Resource Manager](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md). Wdrożeń usługi Resource Manager, nie należy mylić z wdrożeniami aktualizacji mogą zawierać maksymalnie 5 grup zasobów na wdrożenie. Aby zapewnić integralność dołączania, 2 tych grup zasobów są zarezerwowane do skonfigurowania obszaru roboczego usługi Log Analytics, konto usługi Automation i powiązane zasoby. Pozostawia 3 grup zasobów, aby wybrać do wdrożenia.

Użyj kontrolki filtrowania, aby wybrać maszyny wirtualne z różnych subskrypcji, lokalizacji i grup zasobów.

![Dołączanie rozwiązania Update management](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Przejrzyj opcje dla obszaru roboczego usługi Log Analytics i konto usługi Automation. Domyślnie wybrany istniejący obszar roboczy i konto usługi Automation. Jeśli chcesz użyć innego obszaru roboczego usługi Log Analytics i konto usługi Automation, kliknij przycisk **niestandardowe** wybierać z **Konfiguracja niestandardowa** strony. Po wybraniu obszaru roboczego usługi Log Analytics, dokonuje do określenia, jeśli jest połączony z kontem usługi Automation. Jeśli połączonego konta usługi Automation zostanie znaleziony, zostanie wyświetlony następujący ekran. Gdy skończysz, kliknij pozycję **OK**.

![Wybierz obszar roboczy i konto](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Jeśli wybrany obszar roboczy nie został połączony z kontem usługi Automation, zobaczysz poniższy ekran. Wybierz konto usługi Automation i kliknij przycisk **OK** po zakończeniu.

![Brak obszaru roboczego](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> W przypadku włączenia rozwiązań tylko w niektórych regionach jest obsługiwane łączenie obszaru roboczego usługi Log Analytics i konta usługi Automation.
>
> Aby uzyskać listę par mapowania obsługiwanych, zobacz [mapowania Region dla obszaru roboczego z konta usługi Automation i Log Analytics](how-to/region-mappings.md).

Usuń zaznaczenie pola wyboru obok dowolnej maszyny wirtualnej, których nie chcesz, aby włączyć. Maszyny wirtualne, których nie można włączyć są już usunięte.

Kliknij przycisk **Włącz** Aby włączyć rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

## <a name="unlink-workspace"></a>Odłączanie obszaru roboczego

Następujące rozwiązania zależą od obszaru roboczego usługi Log Analytics:

* [Zarządzanie aktualizacjami](automation-update-management.md)
* [Śledzenie zmian](automation-change-tracking.md)
* [Uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu](automation-solution-vm-management.md)

Jeśli zdecydujesz, że już nie chcesz zintegrować konta usługi Automation z obszarem roboczym usługi Log Analytics, możesz odłączyć konta bezpośrednio w witrynie Azure portal. Przed kontynuowaniem należy najpierw usunąć rozwiązania, o których wspomniano wcześniej, w przeciwnym razie ten proces nie będzie mógł kontynuować. Zapoznaj się z artykułem dla danego rozwiązania, które zostały zaimportowane, aby zrozumieć kroki wymagane do usunięcia go.

Po usunięciu tych rozwiązań, można wykonać poniższe kroki, aby odłączyć konto usługi Automation.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania do monitorowania usługi Azure SQL zostały utworzone zasoby usługi automation i może również muszą zostać usunięte przed odłączanie obszaru roboczego.

1. W witrynie Azure Portal otwórz konto usługi Automation, a na automatyzację konta wybierz stronę **połączony obszar roboczy** sekcji **powiązane zasoby** po lewej stronie.

2. Na stronie odłączania obszaru roboczego kliknij **odłączanie obszaru roboczego**.

   ![Odłącz strona obszaru roboczego](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Zostanie wyświetlony monit sprawdzający, czy chcesz kontynuować.

3. Gdy usługa Azure Automation usiłuje odłączyć konto obszaru roboczego usługi Log Analytics, możesz śledzić postęp w obszarze **powiadomienia** z menu.

Jeśli używane jest rozwiązanie do zarządzania aktualizacjami, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Zaplanowanie aktualizacji — będzie mieć nazwy zgodne utworzonych wdrożeń aktualizacji)

* Grupy hybrydowych procesów roboczych utworzone dla rozwiązania — każda będzie miała podobnie do maszyna1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Jeśli uruchamianie/zatrzymywanie maszyn wirtualnych jest używana podczas szczytu rozwiązania, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Uruchamianie i zatrzymywanie maszyn wirtualnych elementu runbook harmonogramy
* Uruchamianie i zatrzymywanie elementów runbook maszyny Wirtualnej
* Zmienne

Alternatywnie można również odłączyć obszaru roboczego z konta usługi Automation z obszaru roboczego usługi Log Analytics. W obszarze roboczym wybierz **konta usługi Automation** w obszarze **powiązane zasoby**. Na stronie konta automatyzacji wybierz **odłączyć konto**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Podczas dołączania wielu maszyn, może być maszyn, które są wyświetlane jako **nie można włączyć**. Istnieją różne przyczyny, dlaczego niektóre maszyny nie może być włączone. W poniższych sekcjach opisano możliwe przyczyny **nie można włączyć** stanu na maszynie Wirtualnej, podczas próby dołączyć.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Maszyna wirtualna raportuje do innego obszaru roboczego: "\<workspaceName\>".  Zmienianie konfiguracji na potrzeby włączania

**Przyczyna**: Ten błąd wskazuje, że maszyna wirtualna, który próbujesz dodać raporty do innego obszaru roboczego.

**Rozwiązanie**: Kliknij przycisk **jako konfiguracji** zmiany docelowego obszaru roboczego konto usługi Automation i Log Analytics.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Maszyna wirtualna raporty do obszaru roboczego, który nie jest dostępny w ramach tej subskrypcji

**Przyczyna**: Obszar roboczy, któremu podlega maszyny wirtualnej:

* Znajduje się w innej subskrypcji lub
* Już nie istnieje, lub
* Znajduje się w grupie zasobów, której nie masz uprawnień dostępu do

**Rozwiązanie**: Znajdź konto usługi automation skojarzone z obszarem roboczym, któremu podlega maszyny Wirtualnej i dołączyć maszyny wirtualnej, zmieniając konfigurację zakresu.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Wersja systemu operacyjnego maszyny Wirtualnej lub dystrybucji nie jest obsługiwana.

**Przyczyna:** Rozwiązanie nie jest obsługiwana dla wszystkich dystrybucje systemu Linux i wszystkich wersji systemu Windows.

**Rozwiązanie:** Zapoznaj się [listę obsługiwanych klientów](automation-update-management.md#clients) dla rozwiązania.

### <a name="classic-vms-cannot-be-enabled"></a>Nie można włączyć klasycznych maszyn wirtualnych

**Przyczyna**: Maszyny wirtualne, które używają klasycznego modelu wdrażania nie są obsługiwane.

**Rozwiązanie**: Migruj maszynę wirtualną, do modelu wdrażania usługi Resource Manager. Aby dowiedzieć się, jak to zrobić, zobacz [migrację zasobów modelu wdrożenia klasycznego](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Maszyna wirtualna jest zatrzymana. (cofnięty przydział)

**Przyczyna**: Maszynę wirtualną w nie **systemem** stanu.

**Rozwiązanie**: W celu dołączenia maszyny Wirtualnej w celu rozwiązania maszyny Wirtualnej musi być uruchomiona. Kliknij przycisk **uruchamianie maszyny Wirtualnej** wbudowany link prowadzący do uruchomienia maszyny Wirtualnej bez konieczności opuszczania strony.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy rozwiązanie jest włączone dla maszyn wirtualnych, odwiedź stronę artykuł Omówienie zarządzania aktualizacjami, aby dowiedzieć się, jak i wyświetlanie oceny aktualizacji dla maszyn.

> [!div class="nextstepaction"]
> [Zarządzanie aktualizacjami — wyświetlanie oceny aktualizacji](./automation-update-management.md#viewing-update-assessments)

Dodawanie samouczków na temat rozwiązania i jak ich używać:

* [Samouczek — Zarządzanie aktualizacjami dla maszyny Wirtualnej](automation-tutorial-update-management.md)

* [Samouczek — identyfikowanie oprogramowania na maszynie Wirtualnej](automation-tutorial-installed-software.md)

* [Samouczek — Rozwiązywanie problemów dotyczących zmian na maszynie Wirtualnej](automation-tutorial-troubleshoot-changes.md)
