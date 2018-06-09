---
title: Dowiedz się, jak dołączyć rozwiązań zarządzania aktualizacjami, śledzenia zmian i magazynu dla wielu maszyn wirtualnych w usłudze Automatyzacja Azure
description: Dowiedz się, jak można dołączyć wirtualnej Azure maszyny z rozwiązań zarządzania aktualizacjami, śledzenia zmian i magazynu, które są częścią usługi Automatyzacja Azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 0a624d850b8c3260acb24cb17566090e8ad0043e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233941"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Włącz zarządzanie aktualizacjami, śledzenia zmian i rozwiązań spisu na wiele maszyn wirtualnych

Automatyzacja Azure zapewnia rozwiązania do zarządzania aktualizacjami zabezpieczeń systemu operacyjnego, śledzenie zmian i magazynowych zainstalowanych w komputerach. Istnieje wiele sposobów, aby dołączyć maszyny, możesz dołączyć rozwiązania [z maszyny wirtualnej](automation-onboard-solutions-from-vm.md), z Twojego [konto automatyzacji](automation-onboard-solutions-from-automation-account.md)podczas przeglądania maszyn wirtualnych i przez [runbook](automation-onboard-solutions.md). W tym artykule omówiono dołączania tych rozwiązań podczas przeglądania maszynach wirtualnych platformy Azure.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do platformy Azure na stronie https://portal.azure.com

## <a name="enable-solutions"></a>Włączanie rozwiązań

W portalu Azure, przejdź do **maszyn wirtualnych**.

Za pomocą pola wyboru, wybierz maszyny wirtualne, które chcesz dołączyć śledzenia zmian i spisu lub zarządzania aktualizacjami. Dołączania jest dostępna dla maksymalnie trzech różnych grup zasobów w czasie.

![Lista maszyn wirtualnych](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Formanty filtru zmodyfikuj listę maszyn wirtualnych, a następnie kliknij przycisk najwyższego pole wyboru, aby wybrać wszystkie maszyny wirtualne na liście.

Na pasku poleceń kliknij **usług** i wybierz opcję **śledzenie zmian**, **spisu**, lub **zarządzania aktualizacjami**.

> [!NOTE]
> **Śledzenie zmian** i **spisu** używać tego samego rozwiązania, gdy jeden jest włączona innych także włączone.

Poniższa ilustracja jest do zarządzania aktualizacjami. Śledzenie zmian i spisu mają ten sam układ i zachowania.

Listę maszyn wirtualnych są filtrowane do wyświetlenia tylko maszyny wirtualne, które znajdują się w tej samej subskrypcji i lokalizacji. W przypadku maszyn wirtualnych w więcej niż trzech grup zasobów, wybrane zostaną pierwszej grupy zasobów.

Użyj opcji filtr, aby wybrać maszyny wirtualne z różnych subskrypcji, lokalizacji i grup zasobów.

![Dodaj rozwiązanie do zarządzania aktualizacji](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Przejrzyj opcje dla obszaru roboczego analizy dzienników i konta automatyzacji. Domyślnie wybrany nowy obszar roboczy i konta automatyzacji. Jeśli masz istniejący obszar roboczy analizy dzienników i konto automatyzacji chcesz używać, kliknij przycisk **zmienić** wybierać z **konfiguracji** strony. Gdy skończysz, kliknij pozycję **Zapisz**.

![Wybierz obszar roboczy i konta](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Usuń zaznaczenie pola wyboru obok żadnej maszyny wirtualnej, których nie chcesz, aby włączyć. Maszyny wirtualne, które nie mogą być włączone są już usunięte.

Kliknij przycisk **włączyć** umożliwiające rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Podczas dołączania wieloma maszynami, może być maszyny, które zawierają jako **nie można włączyć**. Brak przyczyn Dlaczego niektórych komputerów może nie być włączone. W poniższych sekcjach przedstawiono możliwe przyczyny **nie można włączyć** stanu na maszynie Wirtualnej podczas próby dołączenia.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Raporty maszyny Wirtualnej do innego obszaru roboczego: "\<workspaceName\>".  Zmień konfigurację, aby użyć jej do włączenia

**Przyczyna**: ten błąd wskazuje, że maszyna wirtualna, który próbujesz dodać raporty do innego obszaru roboczego.

**Rozwiązanie**: kliknij **jako konfiguracji** zmiany docelowego obszaru roboczego konto automatyzacji i analizy dzienników.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Maszyna wirtualna raporty do obszaru roboczego, który nie jest dostępny w tej subskrypcji

**Przyczyna**: obszar roboczy, który zgłasza maszyny wirtualnej:

* Znajduje się w innej subskrypcji lub
* Już nie istnieje, lub
* Znajduje się w grupie zasobów, który nie ma uprawnień dostępu do

**Rozwiązanie**: Znajdź konto automatyzacji skojarzone z obszaru roboczego, który zgłasza maszyny Wirtualnej i dołączyć maszynę wirtualną, zmieniając konfigurację zakresu.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Wersja systemu operacyjnego maszyny Wirtualnej lub dystrybucji nie jest obsługiwane.

**Przyczyna:** rozwiązania nie jest obsługiwana dla wszystkich dystrybucje systemu Linux lub wszystkich wersji systemu Windows.

**Rozwiązanie:** dotyczą [listę obsługiwanych klientów](automation-update-management.md#clients) dla rozwiązania.

### <a name="classic-vms-cannot-be-enabled"></a>Nie można włączyć klasycznych maszyn wirtualnych

**Przyczyna**: maszyn wirtualnych korzystających z klasycznym modelu wdrożenia nie są obsługiwane.

**Rozwiązanie**: Przeprowadź migrację maszyny wirtualnej do modelu wdrażania Menedżera zasobów. Aby dowiedzieć się, jak to zrobić, zobacz [migrować zasoby modelu wdrażania klasycznego](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Maszyna wirtualna została zatrzymana. (cofnięciu przydziału)

**Przyczyna**: maszyny wirtualnej nie znajduje się w **systemem** stanu.

**Rozwiązanie**: W celu dołączyć Maszynę wirtualną do rozwiązania maszyny Wirtualnej musi być uruchomiona. Kliknij przycisk **uruchamianie maszyny Wirtualnej** wbudowanego łącze, aby uruchomić maszyny Wirtualnej bez konieczności opuszczania strony.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy rozwiązanie jest włączona dla maszyn wirtualnych, odwiedź stronę artykuł — Omówienie aktualizacji zarządzania, aby dowiedzieć się, jak wyświetlić oceny aktualizacji dla maszyn.

> [!div class="nextstepaction"]
> [Zarządzanie aktualizacjami - widoku assessment aktualizacji](./automation-update-management.md#viewing-update-assessments)

Dodanie samouczków na rozwiązania i sposobu ich używania:

* [Samouczek — Zarządzanie aktualizacji dla maszyny Wirtualnej](automation-tutorial-update-management.md)

* [Samouczek — identyfikacji oprogramowania na maszynie Wirtualnej](automation-tutorial-installed-software.md)

* [Samouczek — Rozwiązywanie problemów z zmiany na maszynie Wirtualnej](automation-tutorial-troubleshoot-changes.md)