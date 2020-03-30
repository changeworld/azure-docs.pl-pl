---
title: Zarządzanie danymi usługi Azure Automation
description: Ten artykuł zawiera wiele tematów dotyczących zarządzania środowiskiem automatyzacji platformy Azure.  Obecnie obejmuje przechowywanie danych i tworzenie kopii zapasowych odzyskiwania po awarii usługi Azure Automation w usłudze Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 340fa19b6f9f07e192123900bc96b07bb016542f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132898"
---
# <a name="managing-azure-automation-data"></a>Zarządzanie danymi usługi Azure Automation

Ten artykuł zawiera wiele tematów dotyczących zarządzania środowiskiem automatyzacji platformy Azure.

## <a name="data-retention"></a>Przechowywanie danych

Po usunięciu zasobu w usłudze Azure Automation jest on zachowywany przez 30 dni do celów inspekcji przed trwałym usunięciem. W tym czasie nie można go zobaczyć ani użyć. Ta zasada ma również zastosowanie do zasobów, które należą do konta automatyzacji, który jest usuwany.

Usługa Azure Automation automatycznie usuwa i trwale usuwa zadania starsze niż 30 dni.

W poniższej tabeli podsumowano zasady przechowywania dla różnych zasobów.

| Dane | Zasady |
|:--- |:--- |
| Konta |Trwale usunięte 30 dni po usunięciu konta przez użytkownika. |
| Elementy zawartości |Trwale usunięte 30 dni po usunięciu zasobu przez użytkownika lub 30 dni po usunięciu konta, na które znajduje się zasób przez użytkownika. |
| Moduły |Trwale usunięte 30 dni po usunięciu modułu przez użytkownika lub 30 dni po usunięciu konta, które przechowuje moduł przez użytkownika. |
| Elementy Runbook |Trwale usunięte 30 dni po usunięciu zasobu przez użytkownika lub 30 dni po usunięciu konta, na które znajduje się zasób przez użytkownika. |
| Stanowiska |Usunięte i trwale usunięte 30 dni po ostatniej modyfikacji. Może to być po zakończeniu zadania, jest zatrzymany lub jest zawieszony. |
| Konfiguracje węzłów/pliki MOF |Stara konfiguracja węzła jest trwale usuwana 30 dni po wygenerowaniu nowej konfiguracji węzła. |
| Węzły DSC |Trwale usunięte 30 dni po węzła jest wyrejestrowany z konta automatyzacji przy użyciu witryny Azure portal lub [unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) pokrętło w programie Windows PowerShell. Węzły są również trwale usuwane 30 dni po usunięciu konta, które przechowuje węzeł przez użytkownika. |
| Raporty węzłów |Trwale usunięte 90 dni po wygenerowaniu nowego raportu dla tego węzła |

Zasady przechowywania mają zastosowanie do wszystkich użytkowników i obecnie nie można ich dostosować.

Jednak jeśli trzeba zachować dane przez dłuższy okres czasu, można przesłać dalej dzienniki zadań księgi uruchomiowej do dzienników usługi Azure Monitor. Aby uzyskać więcej informacji, przejrzyj [przesyłanie danych zadań usługi Azure Automation do dzienników usługi Azure Monitor.](automation-manage-send-joblogs-log-analytics.md)

## <a name="backing-up-azure-automation"></a>Tworzenie kopii zapasowych usługi Azure Automation

Po usunięciu konta automatyzacji na platformie Microsoft Azure wszystkie obiekty na koncie są usuwane, w tym elementy runbook, moduły, konfiguracje, ustawienia, zadania i zasoby. Nie można odzyskać obiektów po usunięciu konta.  Możesz użyć następujących informacji, aby wykonać kopię zapasową zawartości konta automatyzacji przed jego usunięciem.

### <a name="runbooks"></a>Elementy Runbook

Można wyeksportować pliki runbook do plików skryptów przy użyciu portalu Azure lub polecenia cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) w programie Windows PowerShell. Te pliki skryptów można zaimportować na inne konto automatyzacji, jak omówiono w [temacie Tworzenie lub importowanie ekwiwania ekwiwania.](/previous-versions/azure/dn643637(v=azure.100))

### <a name="integration-modules"></a>Moduły integracji

Nie można eksportować modułów integracji z usługi Azure Automation. Należy upewnić się, że są one dostępne poza kontem automatyzacji.

### <a name="assets"></a>Elementy zawartości

Nie można eksportować [zasobów](/previous-versions/azure/dn939988(v=azure.100)) z usługi Azure Automation.  Za pomocą witryny Azure portal, należy zanotować szczegóły zmiennych, poświadczenia, certyfikaty, połączenia i harmonogramy.  Następnie należy ręcznie utworzyć wszystkie zasoby, które są używane przez elementy runbook, które są importowane do innej automatyzacji.

[Polecenia cmdlet platformy Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) umożliwiają pobieranie szczegółów niezaszyfrowanych zasobów i zapisywanie ich do wykorzystania w przyszłości lub tworzenie równoważnych zasobów na innym koncie automatyzacji.

Nie można pobrać wartości zaszyfrowanych zmiennych ani pola hasła poświadczeń przy użyciu poleceń cmdlet. Jeśli nie znasz tych wartości, następnie można pobrać je z uruchomieniu przy użyciu [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) i [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)) działań.

Nie można eksportować certyfikatów z usługi Azure Automation. Należy upewnić się, że wszystkie certyfikaty są dostępne poza platformą Azure.

### <a name="dsc-configurations"></a>Konfiguracje DSC

Konfiguracje można wyeksportować do plików skryptów przy użyciu portalu Azure portal lub polecenia cmddfiguration [eksportu azurermautomationdscconfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) w programie Windows PowerShell. Te konfiguracje mogą być importowane i używane na innym koncie automatyzacji.

## <a name="geo-replication-in-azure-automation"></a>Replikacja geograficzna w usłudze Azure Automation

Replikacja geograficzna, standard na kontach usługi Azure Automation, kopii zapasowej danych konta do innego regionu geograficznego w celu zapewnienia nadmiarowości. Podczas konfigurowania konta można wybrać region podstawowy, a następnie automatycznie przypisać do niego region pomocniczy. Dane pomocnicze, kopiowane z regionu podstawowego, są stale aktualizowane w przypadku utraty danych.  

W poniższej tabeli przedstawiono dostępne pary regionów podstawowych i pomocniczych.

| Podstawowy | Pomocniczy |
| --- | --- |
| Południowo-środkowe stany USA |Północno-środkowe stany USA |
| Wschodnie stany USA 2 |Środkowe stany USA |
| Europa Zachodnia |Europa Północna |
| Azja Południowo-Wschodnia |Azja Wschodnia |
| Japonia Wschodnia |Japonia Zachodnia |

W mało prawdopodobnym przypadku utraty danych regionu podstawowego firma Microsoft próbuje je odzyskać. Jeśli nie można odzyskać danych podstawowych, zostanie wykonana usługa geograficzna trybu failover, a klienci, których dotyczy problem, zostaną powiadomieni o tym za pośrednictwem subskrypcji.
