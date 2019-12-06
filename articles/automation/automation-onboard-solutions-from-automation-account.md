---
title: Dowiedz się, jak dołączyć rozwiązania Update Management, Change Tracking i spisu w Azure Automation
description: Dowiedz się, jak dołączyć maszynę wirtualną platformy Azure przy użyciu rozwiązań Update Management, Change Tracking i spisu, które są częścią Azure Automation
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 4/11/2019
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: ab934db026b598e80541f98d74df70c893692120
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849704"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Dołączanie rozwiązań Update Management, Change Tracking i spisu

Azure Automation udostępnia rozwiązania do zarządzania aktualizacjami zabezpieczeń systemu operacyjnego, śledzenia zmian i spisu, co jest zainstalowane na komputerach. Istnieje wiele sposobów na dołączenie maszyn, można dołączyć rozwiązanie [z maszyny wirtualnej](automation-onboard-solutions-from-vm.md), [przeglądać wiele maszyn](automation-onboard-solutions-from-browse.md), z poziomu konta usługi Automation lub przez [element Runbook](automation-onboard-solutions.md). W tym artykule opisano dołączanie tych rozwiązań z konta usługi Automation.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do platformy Azure w witrynie https://portal.azure.com

## <a name="enable-solutions"></a>Włączanie rozwiązań

Przejdź do konta usługi Automation i wybierz pozycję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.

Wybierz obszar roboczy Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz** , aby włączyć rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

![Rozwiązanie do spisu sprzętu](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> W przypadku włączenia rozwiązań tylko w niektórych regionach jest obsługiwane łączenie obszaru roboczego usługi Log Analytics i konta usługi Automation.
>
> Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

Rozwiązanie Change Tracking and Inventory umożliwia [śledzenie zmian](automation-vm-change-tracking.md) i tworzenie [spisu](automation-vm-inventory.md) na maszynach wirtualnych. W tym kroku włączysz rozwiązanie na maszynie wirtualnej.

Po zakończeniu powiadomienia o dołączeniu rozwiązania do śledzenia zmian i spisu kliknij pozycję **Update Management** w obszarze **ZARZĄDZANIE KONFIGURACJĄ**.

Rozwiązanie Update Management umożliwia zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows platformy Azure. Można ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji i przejrzeć wyniki wdrażania, aby sprawdzić, czy aktualizacje zostały zastosowane pomyślnie do maszyny wirtualnej. Ta akcja umożliwiła rozwiązanie dla maszyny wirtualnej.

Wybierz pozycję **Update Management** w obszarze **Update Management**. Wybrany obszar roboczy Log Analytics jest tym samym obszarem roboczym, który jest używany w poprzednim kroku. Kliknij przycisk **Włącz**, aby dołączyć rozwiązanie Update management. Włączanie rozwiązania może trwać do 15 minut.

![Dołączanie rozwiązania aktualizacji](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Konfiguracja zakresu

Każde rozwiązanie korzysta z konfiguracji zakresu w obszarze roboczym, aby kierować komputery, na których jest uzyskiwane rozwiązanie. Konfiguracja zakresu jest grupą co najmniej jednego zapisanego wyszukiwania, które jest używane do ograniczania zakresu rozwiązania do określonych komputerów. Aby uzyskać dostęp do konfiguracji zakresu, na koncie usługi Automation w obszarze **powiązane zasoby**wybierz pozycję **obszar roboczy**. Następnie w obszarze roboczym w obszarze **źródła danych obszaru roboczego**wybierz pozycję **konfiguracje zakresów**.

Jeśli wybrany obszar roboczy nie ma jeszcze rozwiązań Update Management ani Change Tracking, zostaną utworzone następujące konfiguracje zakresów:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Jeśli wybrany obszar roboczy ma już rozwiązanie, rozwiązanie nie zostanie jeszcze wdrożone i nie zostanie do niego dodana konfiguracja zakresu.

## <a name="saved-searches"></a>Zapisane wyszukiwania

Po dodaniu komputera do Update Management lub rozwiązań Change Tracking i spisu są one dodawane do jednego z dwóch zapisanych wyszukiwań w obszarze roboczym. Te zapisane wyszukiwania to zapytania zawierające komputery przeznaczone dla tych rozwiązań.

Przejdź do konta usługi Automation i wybierz pozycję **zapisane wyszukiwania** w obszarze **Ogólne**. Dwa zapisane wyszukiwania używane przez te rozwiązania można zobaczyć w poniższej tabeli:

|Nazwa     |Kategoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Śledzenia zmian       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aktualizacje        | Updates__MicrosoftDefaultComputerGroup         |

Wybierz opcję zapisane wyszukiwanie, aby wyświetlić zapytanie używane do wypełniania grupy. Na poniższej ilustracji przedstawiono zapytanie i jego wyniki:

![Zapisane wyszukiwania](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Dołączanie maszyn wirtualnych platformy Azure

Na koncie usługi Automation wybierz pozycję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**lub **Update Management** w obszarze **Update Management**.

Kliknij pozycję **+ Dodaj maszyny wirtualne platformy Azure**, wybierz co najmniej jedną maszynę wirtualną z listy. Maszyny wirtualne, których nie można włączyć, są wyszarzone i nie można ich wybrać. Maszyny wirtualne platformy Azure mogą istnieć w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation. Na stronie **włączanie Update Management** kliknij pozycję **Włącz**. Ta akcja powoduje dodanie wybranych maszyn wirtualnych do grupy komputerów zapisane wyszukiwanie rozwiązania.

![Włączanie maszyn wirtualnych platformy Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Dołączanie maszyny spoza platformy Azure

Nie trzeba ręcznie dodawać maszyn, które nie są na platformie Azure. Na koncie usługi Automation wybierz pozycję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**lub **Update Management** w obszarze **Update Management**.

Kliknij pozycję **Dodaj maszynę spoza platformy Azure**. Ta akcja powoduje otwarcie nowego okna przeglądarki z [instrukcjami dotyczącymi sposobu instalowania i konfigurowania Microsoft Monitoring Agent na komputerze](../azure-monitor/platform/log-analytics-agent.md) , aby umożliwić komputerowi rozpoczęcie raportowania do rozwiązania. W przypadku dołączania komputera, który jest obecnie zarządzany przez System Center Operations Manager, nowy Agent nie jest wymagany, informacje o obszarze roboczym są wprowadzane do istniejącego agenta.

## <a name="onboard-machines-in-the-workspace"></a>Dołączanie maszyn w obszarze roboczym

Ręcznie zainstalowane maszyny lub maszyny, które są już raportowane do obszaru roboczego, muszą zostać dodane do Azure Automation, aby można było włączyć rozwiązanie. Na koncie usługi Automation wybierz pozycję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**lub **Update Management** w obszarze **Update Management**.

Wybierz pozycję **Zarządzaj komputerami**. Ta akcja powoduje otwarcie strony **Zarządzanie komputerami** . Ta strona umożliwia włączenie rozwiązania na wybranych maszynach, wszystkich dostępnych maszynach lub włączenie rozwiązania dla wszystkich bieżących maszyn i włączenie go na wszystkich przyszłych maszynach. Przycisk **Zarządzaj maszynami** może być wyszarzony, jeśli wcześniej wybrano opcję **Włącz dla wszystkich dostępnych i przyszłych maszyn**.

![Zapisane wyszukiwania](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Wszystkie dostępne maszyny

Aby włączyć rozwiązanie dla wszystkich dostępnych maszyn, wybierz pozycję **Włącz na wszystkich dostępnych maszynach**. Ta akcja powoduje wyłączenie kontrolki do dodawania maszyn osobno. To zadanie dodaje wszystkie nazwy maszyn, które są raportowane do obszaru roboczego do zapisanego zapytania wyszukiwania grupy komputerów. Po wybraniu tej akcji program wyłącza przycisk **Zarządzaj maszynami** .

### <a name="all-available-and-future-machines"></a>Wszystkie dostępne i przyszłe maszyny

Aby włączyć rozwiązanie dla wszystkich dostępnych maszyn i przyszłych maszyn, wybierz pozycję **Włącz na wszystkich dostępnych i przyszłych maszynach**. Ta opcja usuwa zapisane wyszukiwania i konfiguracje zakresów z obszaru roboczego. Ta akcja otwiera rozwiązanie na wszystkich maszynach platformy Azure i poza platformą Azure, które są raportowane do obszaru roboczego. Po wybraniu tej akcji przycisk **Zarządzaj maszynami** zostaje trwale wyłączony, ponieważ nie ma żadnej konfiguracji zakresu.

Można dodać konfiguracje zakresów z powrotem, dodając początkowe zapisane wyszukiwania ponownie. Aby uzyskać więcej informacji, zobacz [zapisane wyszukiwania](#saved-searches).

### <a name="selected-machines"></a>Wybrane maszyny

Aby włączyć rozwiązanie dla co najmniej jednej maszyny, wybierz pozycję **Włącz na wybranych maszynach** , a następnie kliknij przycisk **Dodaj** obok każdego komputera, który ma zostać dodany do rozwiązania. To zadanie dodaje wybrane nazwy maszyn do grupy komputerów zapisane zapytanie wyszukiwania dla rozwiązania.

## <a name="unlink-workspace"></a>Odłącz obszar roboczy

Następujące rozwiązania zależą od obszaru roboczego Log Analytics:

* [Zarządzanie aktualizacjami](automation-update-management.md)
* [Śledzenie zmian](automation-change-tracking.md)
* [Start/Stop VMs during off-hours](automation-solution-vm-management.md)

Jeśli zdecydujesz, że nie chcesz już integrować konta usługi Automation z obszarem roboczym Log Analytics, możesz odłączyć swoje konto bezpośrednio od Azure Portal.  Przed kontynuowaniem należy najpierw usunąć wymienione wcześniej rozwiązania. w przeciwnym razie proces ten nie zostanie zablokowany. Zapoznaj się z artykułem dotyczącym określonego rozwiązania, które zostało zaimportowane, aby poznać kroki wymagane do jego usunięcia.

Po usunięciu tych rozwiązań możesz wykonać następujące czynności, aby odłączyć konto usługi Automation.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania Azure SQL monitoring, mogły utworzyć zasoby automatyzacji i mogą być również musiały zostać usunięte przed odłączeniem obszaru roboczego.

1. W Azure Portal Otwórz konto usługi Automation, a na stronie konto usługi Automation wybierz pozycję **połączony obszar roboczy** w sekcji zatytułowanej **powiązane zasoby** po lewej.

2. Na stronie Odłącz obszar roboczy kliknij pozycję **Odłącz obszar roboczy**.

   ![Odłącz stronę obszaru roboczego](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Zostanie wyświetlony monit o zweryfikowanie, czy chcesz kontynuować.

3. Gdy Azure Automation próbuje odłączyć konto do obszaru roboczego Log Analytics, możesz śledzić postęp w obszarze **powiadomienia** z menu.

Jeśli użyto rozwiązania Update Management, opcjonalnie możesz chcieć usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Harmonogramy aktualizacji — każda z nich będzie mieć nazwy zgodne z utworzonymi wdrożeniami aktualizacji)

* Grupy hybrydowych procesów roboczych utworzone dla rozwiązania — każda z nich będzie podobna do maszyna1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Jeśli użyto opcji Uruchom i Zatrzymaj maszyny wirtualne w rozwiązaniu off-godzinnym, opcjonalnie możesz chcieć usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Uruchamianie i zatrzymywanie harmonogramów elementów Runbook maszyny wirtualnej
* Uruchamianie i zatrzymywanie elementów Runbook maszyny wirtualnej
* Zmienne

Możesz również odłączyć obszar roboczy od konta usługi Automation z poziomu obszaru roboczego Log Analytics. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**. Na stronie konto usługi Automation wybierz opcję **Odłącz konto**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć maszynę wirtualną z Update Management:

* W obszarze roboczym Log Analytics Usuń maszynę wirtualną z zapisanego wyszukiwania `MicrosoftDefaultScopeConfig-Updates`konfiguracji zakresu. Zapisane wyszukiwania można znaleźć **ogólnie** w obszarze roboczym.
* Usuń [program Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) lub [agenta log Analytics dla systemu Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Przejdź do samouczków dotyczących rozwiązań, aby dowiedzieć się, jak z nich korzystać.

* [Samouczek — zarządzanie aktualizacjami dla maszyny wirtualnej](automation-tutorial-update-management.md)

* [Samouczek — identyfikowanie oprogramowania na maszynie wirtualnej](automation-tutorial-installed-software.md)

* [Samouczek — Rozwiązywanie problemów dotyczących zmian na maszynie wirtualnej](automation-tutorial-troubleshoot-changes.md)
