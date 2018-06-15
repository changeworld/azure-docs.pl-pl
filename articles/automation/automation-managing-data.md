---
title: Zarządzanie danymi usługi Azure Automation
description: Ten artykuł zawiera wiele tematów do zarządzania środowiskiem usługi Automatyzacja Azure.  Obecnie obejmuje przechowywanie danych i tworzenie kopii zapasowej odzyskiwania po awarii usługi Automatyzacja Azure w automatyzacji Azure.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3de8711c2e99459ccf5d85dcb12d6ff2d38b700d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192976"
---
# <a name="managing-azure-automation-data"></a>Zarządzanie danymi usługi Azure Automation
Ten artykuł zawiera wiele tematów do zarządzania środowiskiem usługi Automatyzacja Azure.

## <a name="data-retention"></a>Przechowywanie danych
Kiedy usuniesz zasób w automatyzacji Azure, są przechowywane przez 90 dni na potrzeby inspekcji przed zostaną trwale usunięte.  Nie można wyświetlić lub użyj zasobu w tym czasie.  Ta zasada dotyczy również zasoby należące do konta automatyzacji, który zostanie usunięty.

Automatyzacja Azure automatycznie usuwa i trwale starsze niż 90 dni zadania.

W poniższej tabeli przedstawiono zasady przechowywania dla różnych zasobów.

| Dane | Zasady |
|:--- |:--- |
| Konta |Trwale usunięte po upływie 90 dni po usunięciu konta przez użytkownika. |
| Elementy zawartości |Trwale usunięte po upływie 90 dni po usunięciu elementu zawartości przez użytkownika lub 90 dni od konta, która przetrzymuje zasobów zostanie usunięta przez użytkownika. |
| Moduły |Trwale usunięte po upływie 90 dni po usunięciu przez użytkownika modułu lub 90 dni od konta, która przetrzymuje modułu zostanie usunięta przez użytkownika. |
| Elementy Runbook |Trwale usunąć 90 dni, po usunięciu zasobu przez użytkownika lub 90 dni od konta, które przechowuje się, że zasób zostanie usunięta przez użytkownika. |
| Zadania |Usunięte i trwale usunięte 90 dni od ostatniego jest modyfikowany. Może to być po zakończeniu zadania, jest zatrzymana lub jest wstrzymana. |
| Pliki konfiguracji/MOF węzła |Starą konfigurację węzła zostanie usunięty po upływie 90 dni po wygenerowaniu nowego konfiguracji węzła. |
| Węzły DSC |Trwale usunięte po upływie 90 dni po węzeł jest wyrejestrowany z konta automatyzacji za pośrednictwem portalu Azure lub [Unregister-AzureRMAutomationDscNode](https://msdn.microsoft.com/library/mt603500.aspx) polecenia cmdlet programu Windows PowerShell. Węzły są również trwale usuwane 90 dni od konta, która przetrzymuje węzeł zostanie usunięta przez użytkownika. |
| Raporty węzła |Trwale usunięte po upływie 90 dni po wygenerowaniu nowego raportu dla tego węzła |

Zasady przechowywania ma zastosowanie do wszystkich użytkowników i obecnie nie można dostosować.

Jednak jeśli chcesz zachować dane przez dłuższy okres czasu, można przesyłać dalej elementu runbook do analizy dzienników z dziennikami zadań.  Aby uzyskać więcej informacji, przejrzyj [przekazują dane zadanie usługi Automatyzacja Azure Log Analytics](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Tworzenie kopii zapasowych usługi Azure Automation
Podczas usuwania konta automatyzacji w systemie Microsoft Azure, wszystkie obiekty w ramach konta są usuwane w tym elementów runbook, modułów, konfiguracji, ustawienia, zadania i zasoby. Nie można odzyskać obiektów, po usunięciu konta.  Poniższe informacje służy do kopii zapasowej zawartość Twoje konto usługi Automatyzacja przed jego usunięciem. 

### <a name="runbooks"></a>Elementy Runbook
Elementy runbook można wyeksportować do plików skryptu za pomocą usługi Azure portalu lub [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) polecenia cmdlet programu Windows PowerShell.  Nie można zaimportować te pliki skryptów do innego konta automatyzacji, zgodnie z opisem w [Tworzenie lub importowanie elementu Runbook](https://msdn.microsoft.com/library/dn643637.aspx).

### <a name="integration-modules"></a>Moduły integracji
Moduły integracji nie można wyeksportować z usługi Automatyzacja Azure.  Pamiętaj, że są one dostępne poza konta automatyzacji.

### <a name="assets"></a>Elementy zawartości
Nie można wyeksportować [zasoby](https://msdn.microsoft.com/library/dn939988.aspx) usługi Automatyzacja Azure.  Przy użyciu portalu Azure, należy zaznaczyć szczegóły zmienne, poświadczenia, certyfikatów, połączeń i harmonogramy.  Następnie należy ręcznie utworzyć wszelkie zasoby używane przez elementy runbook, importowany do innego automatyzacji.

Można użyć [poleceń cmdlet systemu Azure](https://msdn.microsoft.com/library/dn690262.aspx) Pobieranie szczegółów niezaszyfrowane zasoby i zapisać je do użytku w przyszłości lub utworzyć zasoby równoważne na innym koncie automatyzacji.

Nie można pobrać wartości szyfrowane zmienne lub pole hasła poświadczeń przy użyciu poleceń cmdlet.  Jeśli nie znasz tych wartości, a następnie można je pobrać od elementu runbook za pomocą [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) i [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) działań.

Nie można wyeksportować certyfikaty z usługi Automatyzacja Azure.  Pamiętaj, że wszystkie certyfikaty są dostępne poza platformą Azure.

### <a name="dsc-configurations"></a>Konfiguracje DSC
Można wyeksportować konfiguracji do plików skryptu za pomocą usługi Azure portalu lub [AzureRmAutomationDscConfiguration eksportu](https://msdn.microsoft.com/library/mt603485.aspx) polecenia cmdlet programu Windows PowerShell. Te konfiguracje można zaimportować i użyć innego konta automatyzacji.

## <a name="geo-replication-in-azure-automation"></a>Replikacja geograficzna w automatyzacji Azure
Replikacja geograficzna, standardowego konta usługi Automatyzacja Azure wykonuje kopię zapasową danych konta w innym regionie geograficznym nadmiarowości. Można wybrać regionu podstawowego, gdy trwa konfigurowanie Twojego konta, a następnie region pomocniczy jest do niego przypisany automatycznie. Pomocniczego skopiowanych z regionu podstawowego, jest stale aktualizowany w przypadku utraty danych.  

W poniższej tabeli przedstawiono par dostępne regionu podstawowego i pomocniczego.

| Podstawowy | Pomocniczy |
| --- | --- |
| Środkowo-południowe stany USA |Środkowo-północne stany USA |
| Wschodnie stany USA 2 |Środkowe stany USA |
| Europa Zachodnia |Europa Północna |
| Azja Południowo-Wschodnia |Azja Wschodnia |
| Japonia Wschodnia |Japonia Zachodnia |

Na wypadek zgubienia danych regionu podstawowego Microsoft próbuje odzyskać. Jeśli nie można odzyskać danych pierwotnych, następnie trybu failover geograficzna jest wykonywane i użytkowników, których dotyczy zostanie poinformowany o to, za pośrednictwem ich subskrypcji.

