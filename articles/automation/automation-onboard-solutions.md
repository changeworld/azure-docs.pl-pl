---
title: Dołączanie rozwiązań aktualizacji i śledzenia zmian w usłudze Azure Automation
description: Dowiedz się, jak dołączać rozwiązania aktualizacji i śledzenia zmian w usłudze Azure Automation.
services: automation
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: d0024b8c43e76e3dd26b4b73c4ae0e09890b3b46
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421844"
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Dołączanie rozwiązań aktualizacji i śledzenia zmian w usłudze Azure Automation

W ramach tego samouczka nauczysz się automatycznie dołączać rozwiązania aktualizacji, śledzenia zmian i spisu dla maszyn wirtualnych do usługi Azure Automation:

> [!div class="checklist"]
> * Dołączanie maszyny wirtualnej platformy Azure
> * Włączanie rozwiązań
> * Instalowanie i aktualizowanie modułów
> * Importowanie elementu runbook dołączania
> * Uruchamianie elementu runbook

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności przedstawionych w tym samouczku są wymagane następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do zarządzania maszynami.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md) do dołączenia.

## <a name="onboard-an-azure-vm"></a>Dołączanie maszyny wirtualnej platformy Azure

Istnieje wiele sposobów na dołączenie maszyn, można dołączyć rozwiązanie [z maszyny wirtualnej](automation-onboard-solutions-from-vm.md), [przeglądać wiele maszyn](automation-onboard-solutions-from-browse.md) [z poziomu konta usługi Automation](automation-onboard-solutions-from-automation-account.md)lub przez element Runbook. W tym samouczki przedstawiono procedurę włączania rozwiązania Update Management przy użyciu elementu runbook. Aby można było dołączać maszyny wirtualne platformy Azure na dużą skalę, do istniejącej maszyny wirtualnej należy dołączyć rozwiązanie Change tracking lub Update management. W tym kroku dołączysz maszynę wirtualną z rozwiązaniami Update management i Change tracking.

### <a name="enable-change-tracking-and-inventory"></a>Włączanie rozwiązania Change Tracking and Inventory

Rozwiązanie Change Tracking and Inventory umożliwia [śledzenie zmian](automation-vm-change-tracking.md) i tworzenie [spisu](automation-vm-inventory.md) na maszynach wirtualnych. W tym kroku włączysz rozwiązanie na maszynie wirtualnej.

1. W menu po lewej stronie wybierz pozycję **Konta usługi Automation**, a następnie wybierz konto usługi Automation z listy.
1. Wybierz pozycję **Spis** w obszarze **ZARZĄDZANIE KONFIGURACJĄ**.
1. Wybierz istniejący obszar roboczy usługi Log Analytics lub utwórz nowy. Kliknij przycisk **Włącz**.

![Dołączanie rozwiązania Update](media/automation-onboard-solutions/inventory-onboard.png)

Po zakończeniu powiadomienia o dołączeniu rozwiązania do śledzenia zmian i spisu kliknij pozycję **Update Management** w obszarze **ZARZĄDZANIE KONFIGURACJĄ**.

### <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

Rozwiązanie Update Management umożliwia zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows platformy Azure. Można ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji i przejrzeć wyniki wdrażania, aby sprawdzić, czy aktualizacje zostały zastosowane pomyślnie do maszyny wirtualnej. W tym kroku włączysz rozwiązanie dla maszyny wirtualnej.

1. Z poziomu konta usługi Automation wybierz pozycję **Update management** w obszarze **UPDATE MANAGEMENT**.
1. Wybrany obszar roboczy usługi Log Analytics jest tym samym obszarem roboczym, który był używany w poprzednim kroku. Kliknij przycisk **Włącz**, aby dołączyć rozwiązanie Update management.

![Dołączanie rozwiązania Update](media/automation-onboard-solutions/update-onboard.png)

Podczas instalowania rozwiązania Update management jest wyświetlany niebieski baner. Po włączeniu rozwiązania wybierz pozycję **Change tracking** w obszarze **ZARZĄDZANIE KONFIGURACJĄ**, aby przejść do następnego kroku.

### <a name="select-azure-vm-to-be-managed"></a>Wybieranie maszyny wirtualnej platformy Azure, która ma być zarządzana

Teraz, gdy rozwiązania są włączone, można dołączyć maszynę wirtualną platformy Azure do tych rozwiązań.

1. Na koncie usługi Automation na stronie **Śledzenie zmian** wybierz pozycję **+ Dodaj maszynę wirtualną platformy Azure**, aby dodać maszynę wirtualną.

1. Wybierz maszynę wirtualną z listy i wybierz przycisk **Włącz**. Ta akcja powoduje włączenie rozwiązania Change tracking and Inventory dla maszyny wirtualnej.

   ![Włączanie rozwiązania Update dla maszyny wirtualnej](media/automation-onboard-solutions/enable-change-tracking.png)

1. Po zakończeniu powiadomienia o dołączeniu maszyny wirtualnej z poziomu konta usługi Automation wybierz pozycję **Update management** w obszarze **UPDATE MANAGEMENT**.

1. Wybierz pozycję **+ Dodaj maszynę wirtualną platformy Azure**, aby dodać maszynę wirtualną.

1. Wybierz maszynę wirtualną z listy i wybierz przycisk **Włącz**. Ta akcja powoduje włączenie rozwiązania Update management dla maszyny wirtualnej.

   ![Włączanie rozwiązania Update dla maszyny wirtualnej](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Jeśli nie poczekasz na zakończenie innych rozwiązań, podczas włączania następnego rozwiązania pojawi się komunikat z informacją: *Instalacja innego rozwiązania jest w toku na tej lub innej maszynie wirtualnej. Po zakończeniu tej instalacji przycisk Włącz jest włączony i można zażądać instalacji rozwiązania na tej maszynie wirtualnej.*

## <a name="install-and-update-modules"></a>Instalowanie i aktualizowanie modułów

Wymagana jest aktualizacja do najnowszych modułów platformy Azure i zaimportowanie modułu `AzureRM.OperationalInsights` w celu pomyślnej automatyzacji dołączania rozwiązań.

## <a name="update-azure-modules"></a>Aktualizowanie modułów platformy Azure

Z poziomu konta usługi Automation wybierz pozycję **Moduły** w obszarze **WSPÓŁDZIELONE ZASOBY**. Wybierz pozycję **Aktualizuj moduły platformy Azure**, aby zaktualizować moduły platformy Azure do najnowszej wersji. Wybierz przycisk **Tak** w monicie, aby zaktualizować wszystkie istniejące moduły platformy Azure do najnowszej wersji.

![Aktualizowanie modułów](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>Instalacja modułu AzureRM.OperationalInsights

Na stronie **Moduły** wybierz pozycję **Przeglądaj galerię**, aby otworzyć moduł galerii. Wyszukaj moduł AzureRM.OperationalInsights i zaimportuj ten moduł na konto usługi Automation.

![Importowanie modułu OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importowanie elementu runbook dołączania

1. Z poziomu konta usługi Automation wybierz pozycję **Elementy runbook** w obszarze **AUTOMATYZACJA PROCESÓW**.
1. Wybierz pozycję **Przeglądaj galerię**.
1. Wyszukaj frazę **update and change tracking**, kliknij element runbook i wybierz pozycję **Importuj** na stronie **Wyświetlanie źródła**. Wybierz przycisk **OK**, aby zaimportować element runbook na konto usługi Automation.

   ![Importowanie elementu runbook dołączania](media/automation-onboard-solutions/import-from-gallery.png)

1. Na stronie **Element runbook** wybierz pozycję **Edytuj**, a następnie wybierz pozycję **Publikuj**. W oknie dialogowym **Publikowanie elementu runbook** wybierz przycisk **Tak**, aby opublikować element runbook.

## <a name="start-the-runbook"></a>Uruchamianie elementu runbook

Aby można było uruchomić ten element runbook, musi być dołączone rozwiązanie śledzenia zmian lub aktualizacji. Wymaga istniejącej maszyny wirtualnej i grupy zasobów z rozwiązaniem dołączonym dla parametrów.

1. Otwórz element runbook Enable-MultipleSolution.

   ![Wiele elementów runbook rozwiązań](media/automation-onboard-solutions/runbook-overview.png)

1. Kliknij przycisk Start i wprowadź następujące wartości dla parametrów.

   * **VMNAME** — pozostaw puste. Nazwa istniejącej maszyny wirtualnej do dołączenia do rozwiązania aktualizacji lub śledzenia zmian. Pozostawienie tej wartości pustej spowoduje dołączenie wszystkich maszyn wirtualnych w grupie zasobów.
   * **VMRESOURCEGROUP** — nazwa grupy zasobów dla maszyn wirtualnych, które mają zostać dołączone.
   * **SUBSCRIPTIONID** — pozostaw puste. Identyfikator subskrypcji nowej maszyny wirtualnej, która ma zostać dołączona. Jeśli to pole pozostawiono puste, używana jest subskrypcja obszaru roboczego. Jeśli podano inny identyfikator subskrypcji, konto Uruchom jako dla tego konta należy dodać jako współautora również dla tej subskrypcji.
   * **ALREADYONBOARDEDVM** — nazwa maszyny wirtualnej, która została ręcznie dołączona do rozwiązania Updates lub ChangeTracking.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** — nazwa grupy zasobów, do której należy maszyna wirtualna.
   * **SOLUTIONTYPE** — wprowadź wartość **Updates** lub **ChangeTracking**

   ![Parametry elementu runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Wybierz przycisk **OK**, aby uruchomić zadanie elementu runbook.
1. Monitoruj postęp oraz wszelkie błędy na stronie zadania elementu runbook.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć maszynę wirtualną z Update Management:

* W obszarze roboczym Log Analytics Usuń maszynę wirtualną z zapisanego wyszukiwania `MicrosoftDefaultScopeConfig-Updates`konfiguracji zakresu. Zapisane wyszukiwania można znaleźć **ogólnie** w obszarze roboczym.
* Usuń [program Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) lub [agenta log Analytics dla systemu Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Ręczne dołączanie maszyny wirtualnej platformy Azure.
> * Instalowanie i aktualizowanie wymaganych modułów platformy Azure.
> * Importowanie elementu runbook, który służy do dołączania maszyn wirtualnych platformy Azure.
> * Uruchamianie elementu runbook, który automatycznie dołącza maszyny wirtualne platformy Azure.

Skorzystaj z tego linku, aby dowiedzieć się więcej o planowaniu elementów runbook.

> [!div class="nextstepaction"]
> [Planowanie elementów runbook](automation-schedules.md).
