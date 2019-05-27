---
title: Dowiedz się, jak dodawanie rozwiązań zarządzania aktualizacjami, śledzenie zmian i spisu w usłudze Azure Automation
description: Dowiedz się, jak dołączyć Azure maszynę wirtualną z rozwiązaniami do zarządzania aktualizacjami, śledzenie zmian i spisu, które są częścią usługi Azure Automation
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 4/11/2019
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: a1bb5534d2f98a4e5143038ab1d5fbbcc76184fe
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66133181"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Dołączanie rozwiązania Update Management, śledzenia zmian i spisu

Usługa Azure Automation oferuje rozwiązania do zarządzania aktualizacjami zabezpieczeń systemu operacyjnego, śledzenie zmian i spisu zainstalowanych na komputerach. Istnieje wiele sposobów dołączania maszyn, możesz dołączyć rozwiązanie [z maszyny wirtualnej](automation-onboard-solutions-from-vm.md), [z wieloma maszynami przeglądania](automation-onboard-solutions-from-browse.md), z poziomu konta usługi Automation lub przez [runbook](automation-onboard-solutions.md). W tym artykule opisano dołączania do tych rozwiązań z poziomu konta usługi Automation.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure w witrynie https://portal.azure.com

## <a name="enable-solutions"></a>Włączanie rozwiązań

Przejdź do swojego konta usługi Automation, a następnie wybierz opcję **spisu** lub **śledzenie zmian** w obszarze **zarządzania konfiguracją**.

Wybierz obszar roboczy usługi Log Analytics i konto usługi Automation, a następnie kliknij przycisk **Włącz** Aby włączyć rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

![Dołączanie rozwiązania spisu](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> W przypadku włączenia rozwiązań tylko w niektórych regionach jest obsługiwane łączenie obszaru roboczego usługi Log Analytics i konta usługi Automation.
>
> Aby uzyskać listę par mapowania obsługiwanych, zobacz [mapowania Region dla obszaru roboczego z konta usługi Automation i Log Analytics](how-to/region-mappings.md).

Rozwiązanie Change Tracking and Inventory umożliwia [śledzenie zmian](automation-vm-change-tracking.md) i tworzenie [spisu](automation-vm-inventory.md) na maszynach wirtualnych. W tym kroku włączysz rozwiązanie na maszynie wirtualnej.

Po zakończeniu powiadomienia o dołączeniu rozwiązania do śledzenia zmian i spisu kliknij pozycję **Update Management** w obszarze **ZARZĄDZANIE KONFIGURACJĄ**.

Rozwiązanie Update Management umożliwia zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows platformy Azure. Można ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji i przejrzeć wyniki wdrażania, aby sprawdzić, czy aktualizacje zostały zastosowane pomyślnie do maszyny wirtualnej. Ta akcja rozwiązanie jest włączone dla maszyny Wirtualnej.

Wybierz **rozwiązanie Update management** w obszarze **rozwiązania UPDATE MANAGEMENT**. Wybrany obszar roboczy usługi Log Analytics jest tym samym obszarze roboczym, używany w poprzednim kroku. Kliknij przycisk **Włącz**, aby dołączyć rozwiązanie Update management. Włączanie rozwiązania może trwać do 15 minut.

![Dodawanie aktualizacji rozwiązania](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Konfiguracja zakresu

Każde rozwiązanie używa konfiguracji zakresu wewnątrz obszaru roboczego pod kątem komputerów, które rozwiązanie. Konfiguracja zakresu jest grupy co najmniej jeden zapisane wyszukiwania, która jest używana do ograniczania zakresu rozwiązania do określonych komputerów. Konfiguracje zakresu, w ramach konta usługi Automation w ramach dostępu do **powiązane zasoby**, wybierz opcję **obszaru roboczego**. Następnie w obszarze roboczym **źródła danych obszaru roboczego**, wybierz opcję **konfiguracji zakresu**.

Jeśli wybrany obszar roboczy nie ma jeszcze rozwiązań zarządzania aktualizacjami lub śledzenia zmian, są tworzone następujące konfiguracje zakresu:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Jeśli wybrany obszar roboczy ma już rozwiązania, rozwiązanie nie jest ponownie wdrożone, a konfiguracja zakresu nie została dodana do niego.

## <a name="saved-searches"></a>Zapisane wyszukiwania

Po dodaniu komputera do zarządzania aktualizacjami lub śledzenia zmian i spisu rozwiązania są dodawane do jednej z dwóch zapisanych wyszukiwań, w obszarze roboczym. Zapisane wyszukiwanie są zapytania, które zawierają komputery, które są przeznaczone dla tych rozwiązań.

Przejdź do swojego konta usługi Automation, a następnie wybierz pozycję **zapisane wyszukiwania** w obszarze **ogólne**. Dwa zapisanych wyszukiwań, używane przez te rozwiązania są widoczne w poniższej tabeli:

|Name (Nazwa)     |Category  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aktualizacje        | Updates__MicrosoftDefaultComputerGroup         |

Wybierz albo zapisanego wyszukiwania, aby wyświetlić kwerendę używaną do wypełniania grupy. Na poniższej ilustracji przedstawiono zapytania i jego wyniki:

![Zapisane wyszukiwania](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Dołączanie maszyn wirtualnych platformy Azure

Z automatyzacji konta wybierz **spisu** lub **śledzenie zmian** w obszarze **zarządzania konfiguracją**, lub **zarządzanie aktualizacjami** w obszarze **rozwiązania UPDATE MANAGEMENT**.

Kliknij przycisk **+ Dodaj maszyny wirtualne platformy Azure**, wybierz co najmniej jedną maszynę wirtualną z listy. Maszyny wirtualne, których nie można włączyć jest wyszarzona, poza i nie można wybrać. Maszyny wirtualne platformy Azure może znajdować się w dowolnym regionie, bez względu na to lokalizacja konta usługi Automation. Na **Włączanie rozwiązania Update Management** kliknij **Włącz**. Spowoduje to dodanie wybranych maszyn wirtualnych do grupy komputerów zapisanego wyszukiwania dla rozwiązania.

![Włączanie maszyn wirtualnych platformy Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Dołączanie maszyny spoza platformy Azure

Maszyny nie na platformie Azure należy dodać ręcznie. Z automatyzacji konta wybierz **spisu** lub **śledzenie zmian** w obszarze **zarządzania konfiguracją**, lub **zarządzanie aktualizacjami** w obszarze **rozwiązania UPDATE MANAGEMENT**.

Kliknij przycisk **maszyny spoza platformy Azure Dodaj**. Ta akcja powoduje otwarcie się nowe okno przeglądarki z [instrukcje dotyczące sposobu instalowania i konfigurowania programu Microsoft Monitoring Agent na komputerze](../azure-monitor/platform/log-analytics-agent.md) tak maszyny można rozpocząć raportowania do rozwiązania. W przypadku dołączania komputerze, na którym obecnie zarządzane przez program System Center Operations Manager, nowy agent nie jest wymagane, informacje o obszarze roboczym jest wprowadzany do istniejącego agenta.

## <a name="onboard-machines-in-the-workspace"></a>Dołączanie maszyn w obszarze roboczym

Ręcznie zainstalować maszyny lub maszyny, na których już raporty do obszaru roboczego musi zostać dodane do usługi Azure Automation dla rozwiązania, aby włączyć. Z automatyzacji konta wybierz **spisu** lub **śledzenie zmian** w obszarze **zarządzania konfiguracją**, lub **zarządzanie aktualizacjami** w obszarze **rozwiązania UPDATE MANAGEMENT**.

Wybierz **zarządzać maszynami**. Ta akcja powoduje otwarcie **Zarządzaj maszynami** strony. Ta strona umożliwia włączenie rozwiązania w wybrany zestaw maszyn wirtualnych z wszystkich dostępnych maszynach, lub włączyć to rozwiązanie dla wszystkich maszyn w bieżącym i włączenie go dla wszystkich przyszłych maszynach. **Zarządzać maszynami** przycisk mogą być wyszarzone, jeśli wcześniej została wybrana opcja **Włącz na wszystkich dostępnych i przyszłych maszynach**.

![Zapisane wyszukiwania](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Wszystkich dostępnych maszynach

Aby włączyć rozwiązanie dla wszystkich dostępnych maszynach, zaznacz **Włącz na wszystkich dostępnych maszynach**. Ta akcja wyłącza kontroli w celu dodania maszyn indywidualnie. To zadanie dodaje wszystkie nazwy maszyn raportujących do obszaru roboczego do grupy komputerów, zapisane zapytania wyszukiwania. Po wybraniu ta akcja wyłącza **Zarządzaj maszynami** przycisku.

### <a name="all-available-and-future-machines"></a>Wszystkich dostępnych i przyszłych maszynach

Aby włączyć rozwiązanie dla wszystkich dostępnych i przyszłych maszynach, zaznacz **Włącz na wszystkich dostępnych i przyszłych maszynach**. Ta opcja usuwa zapisane wyszukiwania i konfiguracje zakresów z obszaru roboczego. Ta akcja powoduje otwarcie rozwiązania do wszystkich platformy Azure i maszyn nienależących do platformy Azure, które raportują do obszaru roboczego. Po wybraniu ta akcja wyłącza **Zarządzaj maszynami** przycisk trwale, ponieważ nie ma żadnych konfiguracji zakresu po lewej.

### <a name="selected-machines"></a>Wybrane maszyny

Aby włączyć rozwiązanie dla co najmniej jednej maszyny, wybierz **Włącz na wybranych maszynach** i kliknij przycisk **Dodaj** obok każdej maszynie, którą chcesz dodać do rozwiązania. To zadanie dodaje nazwy wybranej maszyny do grupy komputerów, zapisane zapytanie wyszukiwania dla rozwiązania.

## <a name="unlink-workspace"></a>Odłączanie obszaru roboczego

Następujące rozwiązania zależą od obszaru roboczego usługi Log Analytics:

* [Zarządzanie aktualizacjami](automation-update-management.md)
* [Śledzenie zmian](automation-change-tracking.md)
* [Uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu](automation-solution-vm-management.md)

Jeśli zdecydujesz, że już nie chcesz zintegrować konta usługi Automation z obszarem roboczym usługi Log Analytics, możesz odłączyć konta bezpośrednio w witrynie Azure portal.  Zanim będziesz kontynuować, musisz najpierw usunąć rozwiązania, o których wspomniano wcześniej, w przeciwnym razie ten proces nie będzie mógł kontynuować. Zapoznaj się z artykułem dla danego rozwiązania, które zostały zaimportowane, aby zrozumieć kroki wymagane do usunięcia go.

Po usunięciu tych rozwiązań, można wykonać poniższe kroki, aby odłączyć konto usługi Automation.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania do monitorowania usługi Azure SQL zostały utworzone zasoby usługi automation i może również muszą zostać usunięte przed odłączanie obszaru roboczego.

1. W witrynie Azure Portal otwórz konto usługi Automation, a na automatyzację konta wybierz stronę **połączony obszar roboczy** sekcji etykietą **powiązane zasoby** po lewej stronie.

2. Na stronie odłączania obszaru roboczego kliknij **odłączanie obszaru roboczego**.

   ![Odłącz strona obszaru roboczego](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Zostanie wyświetlony monit sprawdzający, czy chcesz kontynuować.

3. Gdy usługa Azure Automation próbuje odłączyć konto obszaru roboczego usługi Log Analytics, możesz śledzić postęp w obszarze **powiadomienia** z menu.

Jeśli używane jest rozwiązanie do zarządzania aktualizacjami, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Zaplanowanie aktualizacji — będzie mieć nazwy zgodne utworzonych wdrożeń aktualizacji)

* Grupy hybrydowych procesów roboczych utworzone dla rozwiązania — każda będzie miała podobnie do maszyna1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Jeśli została użyta podczas szczytu rozwiązania początkowego i zatrzymywanie maszyn wirtualnych, opcjonalnie może chcesz usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Uruchamianie i zatrzymywanie maszyn wirtualnych elementu runbook harmonogramy
* Uruchamianie i zatrzymywanie elementów runbook maszyny Wirtualnej
* Zmienne

Alternatywnie można również odłączyć obszaru roboczego z konta usługi Automation z obszaru roboczego usługi Log Analytics. W obszarze roboczym wybierz **konta usługi Automation** w obszarze **powiązane zasoby**. Na stronie konta automatyzacji wybierz **odłączyć konto**.

## <a name="next-steps"></a>Kolejne kroki

Przejdź do samouczków dotyczących rozwiązania, aby dowiedzieć się, jak z nich korzystać.

* [Samouczek — Zarządzanie aktualizacjami dla maszyny Wirtualnej](automation-tutorial-update-management.md)

* [Samouczek — identyfikowanie oprogramowania na maszynie Wirtualnej](automation-tutorial-installed-software.md)

* [Samouczek — Rozwiązywanie problemów dotyczących zmian na maszynie Wirtualnej](automation-tutorial-troubleshoot-changes.md)
