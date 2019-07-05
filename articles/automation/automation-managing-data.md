---
title: Zarządzanie danymi usługi Azure Automation
description: Ten artykuł zawiera wiele tematów do zarządzania środowiskiem usługi Azure Automation.  Obecnie dotyczy to retencji danych i tworzenia kopii zapasowych usługi Azure Automation odzyskiwaniem w usłudze Azure Automation.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9de5909ddca5fd36f3fafcb79e2a4ad519402c9c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476583"
---
# <a name="managing-azure-automation-data"></a>Zarządzanie danymi usługi Azure Automation
Ten artykuł zawiera wiele tematów do zarządzania środowiskiem usługi Azure Automation.

## <a name="data-retention"></a>Przechowywanie danych
Jeśli usuniesz zasób w usłudze Azure Automation, wartość jest zachowywana przez 90 dni na potrzeby inspekcji są trwale usuwane.  Nie można wyświetlić ani użyć zasobu w tym czasie.  Te zasady mają zastosowanie również do zasobów, które należą do konta usługi automation, która jest usuwana.

Usługa Azure Automation automatycznie usuwa i trwale zadania starsze niż 90 dni.

W poniższej tabeli przedstawiono zasady przechowywania dla różnych zasobów.

| Data | Zasady |
|:--- |:--- |
| Konta |Trwale usunięte po upływie 90 dni po usunięciu konta przez użytkownika. |
| Elementy zawartości |Trwale usunięte po upływie 90 dni po usunięciu elementu zawartości przez użytkownika lub 90 dni od konta, który przechowuje, że zasób zostanie usunięty przez użytkownika. |
| Moduły |Trwale usunięte po upływie 90 dni po usunięciu przez użytkownika modułu lub 90 dni od konta, które przechowuje moduł zostanie usunięty przez użytkownika. |
| Elementy Runbook |Trwale usunięte po upływie 90 dni po usunięciu zasobu przez użytkownika lub 90 dni od konta, który przechowuje, że zasób zostanie usunięty przez użytkownika. |
| Zadania |Usunięte i trwale usunięte 90 dni po ostatnio modyfikowany. Może to być po zadanie kończy, został zatrzymany lub jest wstrzymana. |
| Pliki MOF/konfiguracji węzła |Starej konfiguracji węzła jest trwale usunięte 90 dni, po wygenerowaniu nową konfigurację węzła. |
| Węzły DSC |Trwale usunięte 90 dni po węzeł, który jest wyrejestrowywany z konta usługi Automation przy użyciu witryny Azure portal lub [AzureRMAutomationDscNode Wyrejestruj](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) polecenia cmdlet programu Windows PowerShell. Węzły są również trwale usunięte 90 dni od konta, które zawiera węzeł jest usunięte przez użytkownika. |
| Raporty węzła |Trwale usunięte 90 dni, po wygenerowaniu nowego raportu dla tego węzła |

Zasady przechowywania ma zastosowanie do wszystkich użytkowników i obecnie nie można dostosować.

Jednak jeśli zachodzi potrzeba przechowywać dane przez dłuższy czas, możesz przekazywać elementu runbook z dziennikami zadań do dzienników usługi Azure Monitor.  Aby uzyskać więcej informacji, przejrzyj [przekazywanie danych zadania usługi Azure Automation do usługi Azure Monitor dzienników](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Tworzenie kopii zapasowych usługi Azure Automation
Po usunięciu konta usługi automation w systemie Microsoft Azure, wszystkie obiekty w ramach konta są usuwane, łącznie z elementami runbook, moduły, konfiguracje, ustawienia, zadaniami i zasobami. Nie można odzyskać obiekty, po usunięciu konta.  Skorzystaj z poniższych informacji, aby utworzyć kopię zapasową zawartości konta usługi automation, przed jego usunięciem. 

### <a name="runbooks"></a>Elementy Runbook
Możesz wyeksportować elementy runbook do plików skryptu za pomocą witryny Azure portal lub [Get AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) polecenia cmdlet programu Windows PowerShell.  Nie można zaimportować te pliki skryptów do innego konta usługi automation, zgodnie z opisem w [Tworzenie lub importowanie elementu Runbook](/previous-versions/azure/dn643637(v=azure.100)).

### <a name="integration-modules"></a>Moduły integracji
Moduły integracji nie można wyeksportować z usługi Azure Automation.  Upewnij się, że są one dostępne poza konta usługi automation.

### <a name="assets"></a>Elementy zawartości
Nie można wyeksportować [zasoby](/previous-versions/azure/dn939988(v=azure.100)) z usługi Azure Automation.  W witrynie Azure portal, należy zaznaczyć szczegóły zmiennych, poświadczenia, certyfikatów, połączeń i harmonogramy.  Następnie należy ręcznie utworzyć wszelkie zasoby, które są używane przez elementy runbook, który można zaimportować do innej usługi automation.

Możesz użyć [polecenia cmdlet programu Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) można pobrać szczegółów niezaszyfrowanych zasobów, a następnie zapisz je w przyszłości lub twórz równoważne zasoby na innym koncie usługi automation.

Nie można pobrać wartości szyfrowane zmienne lub pole hasła poświadczeń przy użyciu poleceń cmdlet.  Jeśli nie znasz tych wartości, a następnie pobierać je z elementu runbook za pomocą [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) i [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)) działań.

Nie można wyeksportować certyfikaty z usługi Azure Automation.  Upewnij się, że wszystkie certyfikaty są dostępne spoza platformy Azure.

### <a name="dsc-configurations"></a>Konfiguracje DSC
Konfiguracje można wyeksportować do plików skryptów za pomocą witryny Azure portal lub [AzureRmAutomationDscConfiguration eksportu](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) polecenia cmdlet programu Windows PowerShell. Te konfiguracje można importowane i używane na innym koncie usługi automation.

## <a name="geo-replication-in-azure-automation"></a>Replikacja geograficzna w usłudze Azure Automation
Replikacja geograficzna, standard, w ramach kont usługi Azure Automation tworzy kopie zapasowe danych konta do innego regionu geograficznego nadmiarowości. Można wybrać regionu podstawowego, podczas konfigurowania konta, a następnie Azure w regionie pomocniczym jest do niej przypisany automatycznie. Pomocniczy, kopiować dane z regionu podstawowego, są stale aktualizowane w przypadku utraty danych.  

W poniższej tabeli przedstawiono sparowania dostępnego regionu podstawowego i pomocniczego.

| Podstawowy | Pomocniczy |
| --- | --- |
| Środkowo-południowe stany USA |Środkowo-północne stany USA |
| Wschodnie stany USA 2 |Środkowe stany USA |
| Europa Zachodnia |Europa Północna |
| Azja Południowo-Wschodnia |Azja Wschodnia |
| Japonia Wschodnia |Japonia Zachodnia |

W tym przypadku mało prawdopodobne, że dane regionu podstawowego zostaną utracone Microsoft próbuje go odzyskać. Jeśli nie można odzyskać danych pierwotnych, jest przeprowadzane pracy awaryjnej geo i dotyczy klienci zostaną powiadomieni o tym za pośrednictwem swojej subskrypcji.


