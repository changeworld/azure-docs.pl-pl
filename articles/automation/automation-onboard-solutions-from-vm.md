---
title: Dołączanie rozwiązań zarządzania aktualizacjami, śledzenie zmian i spisu z Maszyną wirtualną platformy Azure
description: Dowiedz się, jak można dołączyć maszyny wirtualnej platformy Azure z rozwiązaniami do zarządzania aktualizacjami, śledzenie zmian i spisu które są częścią usługi Azure Automation.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: bf81b862f978d4baab0907dc9002564062ec5228
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619734"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Dołączanie rozwiązań zarządzania aktualizacjami, śledzenie zmian i spisu z maszyną wirtualną platformy Azure

Usługa Azure Automation oferuje rozwiązania, które ułatwiają zarządzanie aktualizacjami zabezpieczeń systemu operacyjnego, śledzenie zmian i spisu zainstalowanych na komputerach. Istnieje wiele sposobów dołączania maszyn. Można dołączyć rozwiązanie na maszynie wirtualnej [na koncie usługi Automation](automation-onboard-solutions-from-automation-account.md), [z wieloma maszynami przeglądania](automation-onboard-solutions-from-browse.md), lub za pomocą [elementu runbook](automation-onboard-solutions.md). W tym artykule opisano dołączania do tych rozwiązań z poziomu maszyny wirtualnej platformy Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="enable-the-solutions"></a>Włączanie rozwiązania

Przejdź do istniejącej maszyny wirtualnej. W obszarze **operacji**, wybierz opcję **rozwiązanie Update management**, **spisu**, lub **śledzenie zmian**. Maszyna wirtualna może znajdować się w dowolnym regionie, bez względu na to lokalizacja konta usługi Automation. Podczas dołączania do rozwiązania z maszyny Wirtualnej musisz mieć `Microsoft.OperationalInsights/workspaces/read` uprawnień do ustalenia, czy maszyna wirtualna jest dołączona do obszaru roboczego. Aby dowiedzieć się o dodatkowe uprawnienia, które są potrzebne ogólnie rzecz biorąc, zobacz [uprawnienia wymagane do dołączania maszyn](automation-role-based-access-control.md#onboarding).

Aby włączyć rozwiązanie tylko maszyny wirtualnej, upewnij się, że **Włącz dla tej maszyny Wirtualnej** jest zaznaczone. Aby dołączyć wiele maszyn do rozwiązania, wybierz pozycję **włączanie dla maszyn wirtualnych w ramach tej subskrypcji**, a następnie wybierz pozycję **kliknij, aby wybrać maszyny do włączenia**. Aby dowiedzieć się, aby dołączyć wiele maszyn jednocześnie, zobacz temat [rozwiązania dołączanie rozwiązania Update Management, śledzenia zmian i spisu](automation-onboard-solutions-from-automation-account.md).

Wybierz obszar roboczy usługi Azure Log Analytics i konto usługi Automation, a następnie wybierz **Włącz** Aby włączyć rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

![Dodawanie rozwiązania do zarządzania aktualizacjami](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Przejdź do innych rozwiązań, a następnie wybierz **Włącz**. Obszar roboczy usługi Log Analytics i listy rozwijanej konto usługi Automation są wyłączone, ponieważ te rozwiązania używają tego samego obszaru roboczego i konta usługi Automation jako rozwiązanie wcześniej włączony.

> [!NOTE]
> **Śledzenie zmian** i **spisu** używać tego samego rozwiązania. Gdy jeden z tych rozwiązań jest włączone, druga również jest włączona.

## <a name="scope-configuration"></a>Konfiguracja zakresu

Każde rozwiązanie korzysta z konfiguracji zakresu, a w obszarze roboczym docelowych komputerów, które rozwiązanie. Konfiguracja zakresu jest grupą co najmniej jeden zapisane wyszukiwania, które są używane do ograniczenia zakresu rozwiązania do określonych komputerów. W obszarze dostępu konfiguracji zakresu, w ramach konta usługi Automation do **powiązane zasoby**, wybierz opcję **obszaru roboczego**. W obszarze roboczym w obszarze **źródła danych obszaru roboczego**, wybierz opcję **konfiguracji zakresu**.

Jeśli wybrany obszar roboczy nie ma jeszcze rozwiązań zarządzania aktualizacjami lub śledzenia zmian, są tworzone następujące konfiguracje zakresu:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Jeśli wybrany obszar roboczy ma już rozwiązania, rozwiązanie nie jest ponownie wdrażana i konfigurację zakresu nie została dodana.

Wybierz wielokropek (**...** ) na każdym z konfiguracji, a następnie wybierz **Edytuj**. W **konfiguracji zakresu edycji** okienku wybierz **wybierz grupy komputerów**. **Grup komputerów** okienko zawiera zapisane wyszukiwania, które są używane do tworzenia konfiguracji zakresu.

## <a name="saved-searches"></a>Zapisane wyszukiwania

Po dodaniu komputera do zarządzania aktualizacjami, śledzenia zmian lub rozwiązań magazynu, komputer jest dodawany do jednej z dwóch zapisanych wyszukiwań, w obszarze roboczym. Zapisane wyszukiwania są zapytania, które zawierają komputery, które są przeznaczone dla tych rozwiązań.

Przejdź do swojego obszaru roboczego. W obszarze **ogólne**, wybierz opcję **zapisane wyszukiwania**. Dwa zapisane wyszukiwania, które są używane przez te rozwiązania są wyświetlane w poniższej tabeli:

|Name (Nazwa)     |Kategoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aktualizacje        | Updates__MicrosoftDefaultComputerGroup         |

Wybierz jedną z zapisane wyszukiwania, aby wyświetlić zapytanie, które jest używane do wypełniania grupy. Na poniższej ilustracji przedstawiono zapytania i jego wyniki:

![Zapisane wyszukiwania](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Odłączanie obszaru roboczego

Następujące rozwiązania zależą od obszaru roboczego usługi Log Analytics:

* [Zarządzanie aktualizacjami](automation-update-management.md)
* [Śledzenie zmian](automation-change-tracking.md)
* [Uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu](automation-solution-vm-management.md)

Jeśli zdecydujesz, że już nie chcesz zintegrować konta usługi Automation z obszarem roboczym usługi Log Analytics, możesz odłączyć konta bezpośrednio w witrynie Azure portal.  Przed kontynuowaniem należy najpierw usunąć rozwiązania, o których wspomniano wcześniej, w przeciwnym razie ten proces nie będzie mógł kontynuować. Zapoznaj się z artykułem dla danego rozwiązania, które zostały zaimportowane, aby zrozumieć kroki wymagane do usunięcia go.

Po usunięciu tych rozwiązań, można wykonać poniższe kroki, aby odłączyć konto usługi Automation.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania do monitorowania usługi Azure SQL zostały utworzone zasoby usługi automation i może również muszą zostać usunięte przed odłączanie obszaru roboczego.

1. W witrynie Azure Portal otwórz konto usługi Automation, a na automatyzację konta wybierz stronę **połączony obszar roboczy** sekcji **powiązane zasoby** po lewej stronie.

2. Na stronie odłączania obszaru roboczego kliknij **odłączanie obszaru roboczego**.

   ![Odłącz strona obszaru roboczego](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Zostanie wyświetlony monit sprawdzający, czy chcesz kontynuować.

3. Gdy usługa Azure Automation usiłuje odłączyć konto obszaru roboczego usługi Log Analytics, możesz śledzić postęp w obszarze **powiadomienia** z menu.

Jeśli używane jest rozwiązanie do zarządzania aktualizacjami, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Zaplanowanie aktualizacji — będzie mieć nazwy zgodne utworzonych wdrożeń aktualizacji)

* Grupy hybrydowych procesów roboczych utworzone dla rozwiązania — każda będzie miała podobnie do maszyna1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Jeśli uruchamianie/zatrzymywanie maszyn wirtualnych jest używana podczas szczytu rozwiązania, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Uruchamianie i zatrzymywanie maszyn wirtualnych elementu runbook harmonogramy
* Uruchamianie i zatrzymywanie elementów runbook maszyny Wirtualnej
* Zmienne

## <a name="next-steps"></a>Kolejne kroki

Przejdź do samouczków dotyczących rozwiązania, aby dowiedzieć się, jak ich używać:

* [Samouczek — Zarządzanie aktualizacjami dla maszyny Wirtualnej](automation-tutorial-update-management.md)
* [Samouczek — identyfikowanie oprogramowania na maszynie Wirtualnej](automation-tutorial-installed-software.md)
* [Samouczek — Rozwiązywanie problemów dotyczących zmian na maszynie Wirtualnej](automation-tutorial-troubleshoot-changes.md)
