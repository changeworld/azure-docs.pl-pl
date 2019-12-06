---
title: Zarządzanie danymi usługi Azure Automation
description: Ten artykuł zawiera wiele tematów dotyczących zarządzania środowiskiem Azure Automation.  Obecnie obejmuje przechowywanie danych i wykonywanie kopii zapasowych Azure Automation odzyskiwania po awarii w Azure Automation.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: da1b151a150dfbf602593451d3d68043352b73eb
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850775"
---
# <a name="managing-azure-automation-data"></a>Zarządzanie danymi usługi Azure Automation
Ten artykuł zawiera wiele tematów dotyczących zarządzania środowiskiem Azure Automation.

## <a name="data-retention"></a>Przechowywanie danych
Po usunięciu zasobu w Azure Automation jest on zachowywany przez 90 dni do celów inspekcji przed ich trwałe usunięciem.  Nie można zobaczyć lub użyć zasobu w tym czasie.  Te zasady dotyczą także zasobów należących do konta usługi Automation, które zostało usunięte.

Azure Automation automatycznie usuwa i trwale usuwa zadania starsze niż 90 dni.

Poniższa tabela zawiera podsumowanie zasad przechowywania dla różnych zasobów.

| Dane | Zasady |
|:--- |:--- |
| Konta |Trwale usunięto 90 dni po usunięciu konta przez użytkownika. |
| Elementy zawartości |Trwale usunięto 90 dni po usunięciu zasobu przez użytkownika lub 90 dni po usunięciu zasobu przez użytkownika. |
| Moduły |Trwale usunięto 90 dni po usunięciu modułu przez użytkownika lub 90 dni po usunięciu przez użytkownika konta, które przechowuje moduł. |
| Elementy Runbook |Trwale usunięto 90 dni od momentu usunięcia zasobu przez użytkownika lub 90 dni po usunięciu zasobu przez użytkownika. |
| Stanowiska |Usunięte i trwale usunięte 90 dni po ostatniej modyfikacji. Może to być po zakończeniu zadania, zostanie zatrzymane lub wstrzymane. |
| Konfiguracje węzłów/pliki MOF |Konfiguracja starego węzła została trwale usunięta 90 dni po wygenerowaniu nowej konfiguracji węzła. |
| Węzły DSC |Trwale usunięto 90 dni po wyrejestrowaniu węzła z konta usługi Automation przy użyciu Azure Portal lub polecenia cmdlet [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) w programie Windows PowerShell. Węzły są również trwale usuwane 90 dni po usunięciu przez użytkownika konta, które przechowuje węzeł. |
| Raporty węzła |Trwale usunięto 90 dni od momentu wygenerowania nowego raportu dla tego węzła |

Zasady przechowywania są stosowane dla wszystkich użytkowników i obecnie nie można ich dostosowywać.

Jeśli jednak zachodzi potrzeba zachowania danych przez dłuższy czas, można przesłać dzienniki zadań elementu Runbook do dzienników Azure Monitor.  Aby uzyskać więcej informacji, przejrzyj [Azure Automation danych zadania w celu przeprowadzenia Azure monitor dzienników](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Tworzenie kopii zapasowych usługi Azure Automation
Po usunięciu konta usługi Automation w Microsoft Azure zostaną usunięte wszystkie obiekty w ramach konta, w tym elementy Runbook, moduły, konfiguracje, ustawienia, zadania i zasoby. Nie można odzyskać obiektów po usunięciu konta.  Poniższe informacje służą do utworzenia kopii zapasowej zawartości konta usługi Automation przed jego usunięciem. 

### <a name="runbooks"></a>Elementy Runbook
Elementy Runbook można eksportować do plików skryptów przy użyciu Azure Portal lub polecenia cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) w programie Windows PowerShell.  Te pliki skryptów można zaimportować do innego konta usługi Automation, jak opisano w temacie [Tworzenie lub importowanie elementu Runbook](/previous-versions/azure/dn643637(v=azure.100)).

### <a name="integration-modules"></a>Moduły integracji
Nie można eksportować modułów integracji z Azure Automation.  Musisz się upewnić, że są one dostępne poza kontem usługi Automation.

### <a name="assets"></a>Elementy zawartości
Nie można eksportować [zasobów](/previous-versions/azure/dn939988(v=azure.100)) z Azure Automation.  Przy użyciu Azure Portal należy zanotować szczegóły zmiennych, poświadczeń, certyfikatów, połączeń i harmonogramów.  Następnie należy ręcznie utworzyć wszystkie zasoby, które są używane przez elementy Runbook importowane do innej automatyzacji.

Za pomocą [poleceń cmdlet platformy Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) można pobrać szczegóły nieszyfrowanych zasobów i zapisać je do użycia w przyszłości lub utworzyć równoważne zasoby na innym koncie usługi Automation.

Nie można pobrać wartości szyfrowanych zmiennych lub pola hasła poświadczeń przy użyciu poleceń cmdlet.  Jeśli nie znasz tych wartości, możesz pobrać je z elementu Runbook za pomocą działań [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) i [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)) .

Nie można eksportować certyfikatów z Azure Automation.  Musisz się upewnić, że wszystkie certyfikaty są dostępne poza platformą Azure.

### <a name="dsc-configurations"></a>Konfiguracje DSC
Możesz wyeksportować konfiguracje do plików skryptów przy użyciu Azure Portal lub polecenia cmdlet [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) w programie Windows PowerShell. Te konfiguracje mogą być importowane i używane na innym koncie usługi Automation.

## <a name="geo-replication-in-azure-automation"></a>Replikacja geograficzna w Azure Automation
Replikacja geograficzna, standardowa na kontach Azure Automation, tworzy kopię zapasową danych konta w innym regionie geograficznym w celu zapewnienia nadmiarowości. Możesz wybrać region podstawowy podczas konfigurowania konta, a następnie automatycznie przypisywany region pomocniczy. Dane pomocnicze skopiowane z regionu podstawowego są stale aktualizowane na wypadek utraty danych.  

W poniższej tabeli przedstawiono listę dostępnych par regionów podstawowych i pomocniczych.

| Podstawowy | Pomocniczy |
| --- | --- |
| Południowo-środkowe stany USA |Północno-środkowe stany USA |
| Wschodnie stany USA 2 |Środkowe stany USA |
| Europa Zachodnia |Europa Północna |
| Azja Południowo-wschodnia |Azja Wschodnia |
| Japonia Wschodnia |Japonia Zachodnia |

W prawdopodobnym zdarzeniu, gdy dane regionu podstawowego zostaną utracone, firma Microsoft podejmie próbę jego odzyskania. Jeśli dane podstawowe nie mogą zostać odzyskane, wykonywana jest geograficzna przechodzenie w tryb failover, a klienci, których to dotyczy, otrzymają powiadomienie o tym w ramach subskrypcji.


