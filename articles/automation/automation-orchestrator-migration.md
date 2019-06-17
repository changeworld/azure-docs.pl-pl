---
title: Migrowanie z programu Orchestrator w usłudze Azure Automation
description: W tym artykule opisano sposób migracji pakietów elementów runbook i integracji z programu System Center Orchestrator do usługi Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae47cba8f8e9a7cdf914c0b3ea5dfb9fa6c259a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60738217"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrowanie z programu Orchestrator w usłudze Azure Automation (Beta)
Elementy Runbook w programie [programu System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) opierają się na działań z pakietów integracyjnych, napisanych specjalnie dla programu Orchestrator, podczas gdy elementy runbook w usłudze Azure Automation są oparte na programie Windows PowerShell.  [Graficzne elementy runbook](automation-runbook-types.md#graphical-runbooks) w usłudze Azure Automation mają podobne wygląd elementów runbook programu Orchestrator za pomocą ich działania, reprezentujący poleceń cmdlet programu PowerShell, podrzędne elementy runbook i zasoby.

[Zestawie narzędzi programu System Center Orchestrator migracji](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) obejmuje narzędzia pomocne podczas konwertowania elementów runbook z programu Orchestrator w usłudze Azure Automation.  Oprócz Konwertowanie elementów runbook, samodzielnie, należy konwertować Pakiety integracyjne z działaniami, korzystających z elementów runbook na moduły integracji przy użyciu poleceń cmdlet programu Windows PowerShell.  

Poniżej przedstawiono podstawowy proces podczas konwertowania elementy runbook programu Orchestrator w usłudze Azure Automation.  Każdy z tych kroków opisano szczegółowo w poniższych sekcjach.

1. Pobierz [zestawie narzędzi programu System Center Orchestrator migracji](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) zawierający narzędzia i moduły omówionych w tym artykule.
2. Importuj [modułu działań standardowych](#standard-activities-module) do usługi Azure Automation.  Obejmuje to przekonwertowana wersje standardowych działań programu Orchestrator, które mogą być używane przez elementy runbook przekonwertowana.
3. Importuj [moduły integracji programu System Center Orchestrator](#system-center-orchestrator-integration-modules) do usługi Azure Automation, aby uzyskać te pakiety integracyjne używany przez elementy runbook, dostęp do programu System Center.
4. Konwertuj niestandardowe i pakiety integracyjne innych firm przy użyciu [Integration Pack konwerter](#integration-pack-converter) i zaimportować do usługi Azure Automation.
5. Konwertowanie Orchestrator elementów runbook przy użyciu [konwerter Runbook](#runbook-converter) i instalowanie w usłudze Azure Automation.
6. Ręcznie utworzyć wymagane zasoby programu Orchestrator w usłudze Azure Automation, ponieważ konwerter elementu Runbook nie konwertuje te zasoby.
7. Konfigurowanie [hybrydowego procesu roboczego Runbook](#hybrid-runbook-worker) w lokalnym centrum danych do uruchomienia przekonwertowanego elementów runbook, który będzie uzyskiwać dostęp do zasobów lokalnych.

## <a name="service-management-automation"></a>Service Management Automation
[Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) są przechowywane i uruchamia elementy runbook w lokalnym centrum danych takich jak program Orchestrator i używa tych samych moduły integracji jako usługa Azure Automation. [Konwerter Runbook](#runbook-converter) konwertuje elementy runbook programu Orchestrator do graficznych elementów runbook mimo że nie są obsługiwane w programie SMA.  Nadal można zainstalować [standardowego modułu działania](#standard-activities-module) i [moduły integracji programu System Center Orchestrator](#system-center-orchestrator-integration-modules) do programu SMA, ale użytkownik będzie musiał ręcznie [ponownego zapisywania elementów runbook](https://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook
Elementy Runbook programu Orchestrator są przechowywane na serwerze bazy danych i uruchamiane na serwerach runbook, zarówno w lokalnym centrum danych.  Elementy Runbook w usłudze Azure Automation są przechowywane w chmurze platformy Azure i mogą być uruchamiane w swoje lokalne centrum danych za pomocą [hybrydowego procesu roboczego Runbook](automation-hybrid-runbook-worker.md).  Jest to, jak zwykle będzie uruchamiane elementy runbook konwertowanych z programu Orchestrator, ponieważ są one przeznaczone do uruchamiania na serwerach lokalnych.

## <a name="integration-pack-converter"></a>Integration Pack konwertera
Konwerter pakietu integracji konwertuje integracyjnych, które zostały utworzone przy użyciu [Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) z modułami integracji oparte na programie Windows PowerShell, który można zaimportować do usługi Azure Automation lub Automatyzacja zarządzania usługami.  

Po uruchomieniu konwerter pakietu integracji są prezentowane za pomocą kreatora, który pozwoli wybrać plik pakietu (oip) integracji.  Kreator następnie wyświetla działania zawarte w tym pakiecie integracyjnym i umożliwia wybranie, które zostaną poddane migracji.  Po zakończeniu działania kreatora, tworzy modułu integracji, zawierającego odpowiednie polecenie cmdlet dla każdego działania w oryginalnym pakietu integracyjnego.

### <a name="parameters"></a>Parametry
Wszystkie właściwości działania w pakiecie integracyjnym programu są konwertowane na parametry odpowiednie polecenie cmdlet w module usługi integracji.  Polecenia cmdlet programu Windows PowerShell ma zbiór [wspólne parametry](https://technet.microsoft.com/library/hh847884.aspx) , mogą być używane z wszystkie polecenia cmdlet.  Na przykład - Verbose parametru powoduje, że polecenia cmdlet, aby dane wyjściowe szczegółowe informacje na temat jej działania.  Polecenie cmdlet nie może mieć parametru z taką samą nazwę jak typowego parametru.  Jeśli działanie ma właściwość o tej samej nazwie jako typowego parametru, Kreator wyświetli monit podaj inną nazwę dla parametru.

### <a name="monitor-activities"></a>Monitorowanie działań
Monitorowanie elementów runbook w programie Orchestrator rozpoczyna się od [monitorować aktywność](https://technet.microsoft.com/library/hh403827.aspx) i uruchom stale oczekiwania do wywołania przez określonego zdarzenia.  Usługa Azure Automation nie obsługuje monitorowanie elementów runbook, więc żadnych działań monitorowania w pakiecie integracyjnym programu nie zostanie przekonwertowany.  Zamiast tego polecenia cmdlet symbol zastępczy jest tworzony w module integracji dla działania monitorowania.  To polecenie cmdlet nie ma żadnych funkcji, ale pozwala przekonwertowanego elementu runbook, który używa jej do zainstalowania.  Ten element runbook nie będzie można uruchamiać w usłudze Azure Automation, ale można go zainstalować, aby można go zmodyfikować.

### <a name="integration-packs-that-cannot-be-converted"></a>Pakiety integracyjne, których nie można przekonwertować
Nie można przekonwertować integracyjnych, które nie zostały utworzone przy użyciu OIT przy użyciu konwertera Integration Pack. Istnieją również pewne Pakiety integracyjne dostarczane przez firmę Microsoft, których nie można przekonwertować za pomocą tego narzędzia.  Zostały przekonwertowane wersjach te pakiety integracyjne [udostępniane do pobrania](#system-center-orchestrator-integration-modules) , dzięki czemu mogą być instalowane w usłudze Azure Automation i Service Management Automation.

## <a name="standard-activities-module"></a>Moduł działania standardowe
Program orchestrator zawiera zbiór [działań standardowych](https://technet.microsoft.com/library/hh403832.aspx) , nie są uwzględnione w pakiecie integracyjnym, ale są używane przez wielu elementach runbook.  Moduł standardowych działań jest modułu integracji, która zawiera równoważne polecenia cmdlet dla każdego z tych działań.  Przed zaimportowaniem przekonwertowanego elementów runbook, który użyć standardowego działania, należy zainstalować ten moduł integracji w usłudze Azure Automation.

Oprócz obsługi przekonwertowanego elementów runbook, polecenia cmdlet w module działania standardowe może służyć przez kogoś zapoznać się z programu Orchestrator do tworzenia nowych elementów runbook w usłudze Azure Automation.  Chociaż funkcje wszystkich działań standardowych można wykonać za pomocą poleceń cmdlet, może być działają inaczej.  Polecenia cmdlet w module przekonwertowanego standardowych działań będzie działać tak samo, jak ich odpowiednie działania i użyj te same parametry.  Może to ułatwić istniejących autor elementu runbook programu Orchestrator w ich trakcie przechodzenia do elementów runbook usługi Azure Automation.

## <a name="system-center-orchestrator-integration-modules"></a>Moduły integracji programu System Center Orchestrator
Firma Microsoft udostępnia [Pakiety integracyjne](https://technet.microsoft.com/library/hh295851.aspx) do tworzenia elementów runbook do automatyzacji składników programu System Center i innych produktów.  Niektóre z tych pakietów integracyjnych obecnie opierają się na OIT, ale obecnie nie można przekonwertować na moduły integracji ze względu na znane problemy.  [Moduły integracji programu System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) zawiera przekonwertowanego wersje te pakiety integracyjne, które mogą być importowane do usługi Azure Automation i Service Management Automation.  

W wersji RTM narzędzia zaktualizowanych wersji pakietu integracyjnego, w oparciu o OIT, który może zostać przekonwertowany przy użyciu konwertera Integration Pack zostaną opublikowane.  Wskazówki dotyczące zapewnia się ułatwienie konwersji przy użyciu działań Pakiety integracyjne nie są oparte na OIT elementów runbook.

## <a name="runbook-converter"></a>Konwerter elementu Runbook
Konwerter Runbook konwertuje elementy runbook programu Orchestrator do [graficznych elementów runbook](automation-runbook-types.md#graphical-runbooks) , mogą być importowane do usługi Azure Automation.  

Konwerter elementu Runbook jest implementowany jako moduł programu PowerShell z poleceniem cmdlet o nazwie **ConvertFrom SCORunbook** , wykonuje konwersję.  Podczas instalacji narzędzie utworzy skrót do sesji programu PowerShell, który ładuje polecenia cmdlet.   

Poniżej przedstawiono podstawowy proces konwersji elementu runbook programu Orchestrator i zaimportować go do usługi Azure Automation.  Poniższe sekcje zawierają więcej szczegółów przy użyciu narzędzia i pracy z elementami runbook przekonwertowany.

1. Jeden lub więcej elementów runbook są eksportowane z programu Orchestrator.
2. Uzyskaj moduły integracji dla wszystkich działań w elemencie runbook.
3. Konwertuj elementy runbook programu Orchestrator w wyeksportowanym pliku.
4. Przejrzyj informacje w dziennikach do weryfikacji konwersji i określić wszelkie wymagane działania ręcznego.
5. Przekonwertowana elementów runbook należy zaimportować do usługi Azure Automation.
6. Utwórz wszystkie wymagane zasoby w usłudze Azure Automation.
7. Edytowanie elementu runbook w usłudze Azure Automation, aby zmodyfikować wszelkie wymagane działania.

### <a name="using-runbook-converter"></a>Za pomocą konwertera elementu Runbook
Składnia **ConvertFrom SCORunbook** jest następująca:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath — ścieżka do pliku eksportu, zawierającego elementy runbook w celu konwersji.
* Moduł — rozdzielana przecinkami lista modułów integracji zawierających działania w elementach runbook.
* OutputFolder — ścieżka do folderu, aby utworzyć przekonwertować graficznych elementów runbook.

Następujące przykładowe polecenie konwertuje elementy runbook w pliku eksportu o nazwie **MyRunbooks.ois_export**.  Te elementy runbook, użyj Pakiety integracyjne usługi Active Directory i programu Data Protection Manager.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Pliki dziennika
Konwerter elementu Runbook utworzy następujące pliki dziennika w tej samej lokalizacji co przekonwertowany element runbook.  Jeśli pliki już istnieją, następnie zostaną one zastąpione przy użyciu informacji z ostatniej konwersji.

| Plik | Zawartość |
|:--- |:--- |
| Element Runbook konwertera - Progress.log |Szczegółowy opis kroków konwersji, w tym informacje o każdym działaniu pomyślnie przekonwertowany i ostrzeżenie dla każdego działania, które nie są konwertowane. |
| Element Runbook konwertera - Summary.log |Podsumowanie, ostatnie konwersji, w tym wszelkie ostrzeżenia i monitowanie zadania, które należy wykonać takie jak tworzenie zmiennej wymaganej przekonwertowanego elementu runbook. |

### <a name="exporting-runbooks-from-orchestrator"></a>Eksportowanie elementów runbook z programu Orchestrator
Konwerter element Runbook działa z pliku eksportu z programu Orchestrator, który zawiera jeden lub więcej elementów runbook.  Zostanie utworzony odpowiedni element runbook usługi Azure Automation dla każdego elementu runbook programu Orchestrator w pliku eksportu.  

Aby wyeksportować elementu runbook z programu Orchestrator, kliknij prawym przyciskiem myszy nazwę elementu runbook w programie Runbook Designer, a następnie wybierz **wyeksportować**.  Aby wyeksportować wszystkie elementy runbook w folderze, kliknij prawym przyciskiem myszy nazwę folderu, a następnie wybierz pozycję **wyeksportować**.

### <a name="runbook-activities"></a>Działania elementu Runbook
Konwerter Runbook konwertuje każde działanie w elemencie runbook programu Orchestrator do odpowiadającego im działania w usłudze Azure Automation.  Dla tych działań, które nie może zostać przekonwertowany działanie symbol zastępczy jest tworzony w elemencie runbook za pomocą tekst ostrzeżenia.  Po zaimportowaniu przekonwertowany element runbook do usługi Azure Automation, musisz zastąpić żadnego z tych działań z prawidłową działań, które uruchomienia wymaganych funkcji.

Wszelkie działania programu Orchestrator w [standardowego modułu działania](#standard-activities-module) zostanie przekonwertowany.  Istnieją pewne działania standardowe programu Orchestrator, nie znajdują się w tym module jednak, które nie są konwertowane.  Na przykład **wysyłania zdarzenia platformy** ma odpowiednika usługi Azure Automation, ponieważ zdarzenie jest specyficzne dla programu Orchestrator.

[Monitorowanie działań](https://technet.microsoft.com/library/hh403827.aspx) nie są konwertowane, ponieważ nie ma odpowiednika do nich w usłudze Azure Automation.  Wyjątek jest monitorowanie działań w [przekonwertować Pakiety integracyjne](#integration-pack-converter) , zostaną przekonwertowane na działanie symbol zastępczy.

Wszystkie działania z [konwertowany pakiet integracyjny](#integration-pack-converter) zostaną przekonwertowane, jeśli należy podać ścieżkę do modułu integracji z **modułów** parametru.  Pakiety integracyjne programu System Center, można użyć [moduły integracji programu System Center Orchestrator](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Zasoby programu orchestrator
Konwerter Runbook konwertuje tylko elementy runbook, a nie inne zasoby programu Orchestrator, takich jak liczniki, zmienne lub połączenia.  Liczniki nie są obsługiwane w usłudze Azure Automation.  Połączenia i zmienne są obsługiwane, ale należy je utworzyć ręcznie.  Pliki dziennika zostaną mówiąca, czy element runbook wymaga takich zasobów i określ odpowiednie zasoby, których potrzebujesz do tworzenia w usłudze Azure Automation do przekonwertowany element runbook, aby działać prawidłowo.

Na przykład element runbook może użyć zmiennej do wypełniania określoną wartość w działaniu.  Przekonwertowany element runbook będzie przekonwertować działanie i określić zasób zmiennej w usłudze Azure Automation z taką samą nazwę jak zmienna programu Orchestrator.  Ten adres zostanie zapisany w **konwertera Runbook - Summary.log** pliku, który jest tworzony po konwersji.  Należy ręcznie utworzyć ten zasób zmiennej w usłudze Azure Automation, przed rozpoczęciem korzystania z elementu runbook.

### <a name="input-parameters"></a>Parametry wejściowe
Elementy Runbook w programie Orchestrator akceptują parametrów wejściowych za pomocą **inicjalizowania danych** działania.  Jeśli element runbook konwersji zawiera to działanie, a następnie [parametr wejściowy](automation-graphical-authoring-intro.md#runbook-input-and-output) w usłudze Azure Automation runbook jest tworzony dla każdego parametru w działaniu.  A [kontroli przepływu pracy skryptu](automation-graphical-authoring-intro.md#activities) utworzeniu działania w elemencie runbook przekonwertowany, która pobiera i zwraca wartość każdego parametru.  Wszystkie działania elementu runbook, które używają parametru wejściowego można znaleźć w danych wyjściowych z tego działania.

Przyczyna, że jest używana ta strategia jest najlepiej duplikatów funkcjonalność w elemencie runbook programu Orchestrator.  Działania w nowych graficznych elementów runbook należy się bezpośrednio przy użyciu źródła danych wejściowych elementu Runbook parametry wejściowe.

### <a name="invoke-runbook-activity"></a>Działania wywołania elementu Runbook
Elementy Runbook w programie Orchestrator uruchamiać inne elementy runbook za pomocą **Wywołaj element Runbook** działania. Jeśli element runbook konwersji zawiera to działanie i **czekać na ukończenie** opcja jest ustawiona, a następnie działanie elementu runbook jest tworzone dla niego przekonwertowanego elementu runbook.  Jeśli **czekać na ukończenie** opcja nie jest zaznaczona, a następnie tworzony jest działanie dotyczące skryptu przepływu pracy, który używa **Start AzureAutomationRunbook** uruchomienia elementu runbook.  Po zaimportowaniu przekonwertowany element runbook do usługi Azure Automation, należy zmodyfikować to działanie za pomocą informacji określonego w działaniu.

## <a name="related-articles"></a>Pokrewne artykuły:
* [System Center 2012 — Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrydowego procesu roboczego Runbook](automation-hybrid-runbook-worker.md)
* [Działania standardowe programu orchestrator](https://technet.microsoft.com/library/hh403832.aspx)
* [Pobierz System Center Orchestrator Migration Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

