---
title: Migrowanie z programu Orchestrator do usługi Automatyzacja Azure
description: Opisuje sposób migracji pakietów elementów runbook i integracja z programu System Center Orchestrator do automatyzacji Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 4d692e4dc639cbd290b465980da2319ac590627c
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrowanie z programu Orchestrator do automatyzacji Azure (wersja Beta)
Elementy Runbook w [programu System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) są oparte na działań z pakietów integracyjnych, które zostały napisane specjalnie dla programu Orchestrator, gdy elementy runbook automatyzacji Azure są oparte na programie Windows PowerShell.  [Graficznych elementów runbook](automation-runbook-types.md#graphical-runbooks) automatyzacji Azure mają podobne wygląd elementów runbook programu Orchestrator z ich działania polecenia cmdlet programu PowerShell, podrzędnych elementów runbook i zasoby.

[System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) zawiera narzędzia, aby ułatwić konwertowania Orchestrator elementów runbook automatyzacji Azure.  Pakiety integracyjne z działaniami, które elementy runbook, użyj oprócz konwertowanie się elementy runbook, muszą być konwertowane do moduły integracji z poleceniami cmdlet programu Windows PowerShell.  

Poniżej przedstawiono podstawowy proces podczas konwertowania elementy runbook programu Orchestrator do automatyzacji Azure.  Każda z tych czynności opisano szczegółowo w poniższych sekcjach.

1. Pobierz [System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) zawierający narzędzia i moduły omówione w tym artykule.
2. Importuj [modułu działań standardowych](#standard-activities-module) w automatyzacji Azure.  W tym przekonwertowanego wersje standardowych działań programu Orchestrator, które mogą być używane przez elementy runbook przekonwertowany.
3. Importuj [moduły integracji programu System Center Orchestrator](#system-center-orchestrator-integration-modules) w automatyzacji Azure dla tych pakietów integracyjnych, używany przez elementy runbook dostępnym dla programu System Center.
4. Konwertuj niestandardowy i pakiety integracyjne innych firm przy użyciu [Integration Pack konwertera](#integration-pack-converter) i zaimportować do usługi Automatyzacja Azure.
5. Konwertowanie Orchestrator elementów runbook przy użyciu [konwertera Runbook](#runbook-converter) i zainstalować automatyzacji Azure.
6. Ręcznie utworzyć wymagane zasoby programu Orchestrator w automatyzacji Azure, ponieważ konwerter elementu Runbook nie konwertuje tych zasobów.
7. Skonfiguruj [hybrydowy proces roboczy elementu Runbook](#hybrid-runbook-worker) w centrum danych lokalnych do uruchomienia przekonwertowanego elementów runbook, które będą uzyskiwać dostęp do zasobów lokalnych.

## <a name="service-management-automation"></a>Service Management Automation
[Automatyzacja zarządzania usługami](http://technet.microsoft.com/library/dn469260.aspx) (SMA) są przechowywane i uruchamia elementy runbook w centrum danych lokalnych jak Orchestrator i używa tego samego moduły integracji jako automatyzacji Azure. [Konwertera Runbook](#runbook-converter) konwertuje elementy runbook programu Orchestrator do graficznych elementów runbook mimo że nie są obsługiwane w programie SMA.  Można nadal zainstalować [standardowy moduł działania](#standard-activities-module) i [moduły integracji programu System Center Orchestrator](#system-center-orchestrator-integration-modules) do programu SMA, ale należy ręcznie [przepisywania elementy runbook](http://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook
Elementy Runbook w programie Orchestrator są przechowywane na serwerze bazy danych i uruchamiać na serwerach runbook, zarówno w centrum danych lokalnych.  Elementy Runbook automatyzacji Azure są przechowywane w chmurze Azure i można uruchomić w centrum danych lokalnych za pomocą [hybrydowy proces roboczy elementu Runbook](automation-hybrid-runbook-worker.md).  Jest to, jak zwykle będzie uruchamiane elementy runbook skonwertowane z programu Orchestrator, ponieważ są one przeznaczone do uruchamiania na serwerach lokalnych.

## <a name="integration-pack-converter"></a>Konwerter pakietu integracyjnego
Konwerter pakiet integracji konwertuje pakietów integracyjnych, które zostały utworzone przy użyciu [Orchestrator Integration Toolkit (OIT)](http://technet.microsoft.com/library/hh855853.aspx) z modułami integracji oparte na programie Windows PowerShell, który można zaimportować do usługi Automatyzacja Azure lub Automatyzacja zarządzania usługami.  

Po uruchomieniu konwertera pakiet integracji są prezentowane za pomocą kreatora, co umożliwi wybranie integracji pliku pakietu (oip).  Następnie Kreator zawiera listę czynności związane z tego pakietu i pozwala wybrać, które zostaną poddane migracji.  Po zakończeniu pracy Kreatora tworzy moduł integracji, który zawiera odpowiednie polecenie cmdlet dla każdego działania w oryginalnej pakietu integracyjnego.

### <a name="parameters"></a>Parametry
Właściwości działania w pakiecie integracyjnym są konwertowane na parametry odpowiednie polecenie cmdlet w module integracji.  Polecenia cmdlet programu Windows PowerShell ma zestaw [wspólne parametry](http://technet.microsoft.com/library/hh847884.aspx) które mogą być używane wszystkie polecenia cmdlet.  Na przykład Verbose parametru powoduje, że polecenia cmdlet, aby szczegółowe informacje na temat jej działania.  Polecenie cmdlet nie może mieć parametru o takiej samej nazwie jak wspólnego parametru.  Jeśli działanie ma właściwości o tej samej nazwy co parametr wspólne, Kreator wyświetli monit o Podaj inną nazwę dla parametru.

### <a name="monitor-activities"></a>Działań monitorowania
Elementy runbook w programie Orchestrator rozpoczyna się od monitorowania [monitorowania aktywności](http://technet.microsoft.com/library/hh403827.aspx) i uruchom stale oczekiwania do wywołania przez konkretnego zdarzenia.  Usługi Automatyzacja Azure nie obsługuje elementów runbook monitora, więc żadnych działań monitorowania w pakiecie integracyjnym programu nie zostanie przekonwertowany.  Zamiast tego polecenia cmdlet symbol zastępczy jest tworzony w moduł integracji dla działania monitorowania.  To polecenie cmdlet nie ma żadnych funkcji, ale pozwala przekonwertowany element runbook, który korzysta z niego do zainstalowania.  Ten element runbook nie będzie można uruchomić w automatyzacji Azure, ale można ją zainstalować, aby można go zmodyfikować.

### <a name="integration-packs-that-cannot-be-converted"></a>Pakiety integracyjne, których nie można przekonwertować
Nie można przekonwertować pakietów integracyjnych, które nie zostały utworzone z OIT konwerterze Integration Pack. Brak niektórych pakietów integracyjnych firmy Microsoft, nie można przekonwertować z tego narzędzia.  Zostały przekonwertowane wersje te pakiety integracyjne [dostępne do pobrania](#system-center-orchestrator-integration-modules) , dzięki czemu mogą być instalowane w automatyzacji Azure lub Service Management Automation.

## <a name="standard-activities-module"></a>Działania standardowe modułu
Orchestrator zawiera zestaw [działań standardowych](http://technet.microsoft.com/library/hh403832.aspx) które nie są uwzględnione w pakiecie integracyjnym, ale są używane przez wiele elementów runbook.  Działania standardowe jest moduł integracji, który zawiera równoważne polecenia cmdlet dla każdego z tych działań.  Przed zaimportowaniem przekonwertowanego elementów runbook, który użyć standardowego działania, należy zainstalować ten moduł integracji automatyzacji Azure.

Oprócz obsługi przekonwertowanego elementów runbook, poleceń cmdlet w module standardowych działań mogą posłużyć znajomych z programem Orchestrator do tworzenia nowych elementów runbook automatyzacji Azure.  Gdy funkcje wszystkich działań standardowych można wykonywać za pomocą polecenia cmdlet, mogą one działają inaczej.  Poleceń cmdlet w module przekonwertowanego działań standardowych pracy taki sam, jak ich odpowiednie działania, a następnie używać tych samych parametrach.  Może to ułatwić istniejących Autor elementów runbook programu Orchestrator w ich przejście do elementu runbook usługi Automatyzacja Azure.

## <a name="system-center-orchestrator-integration-modules"></a>Moduły integracji programu System Center Orchestrator
Firma Microsoft udostępnia [Pakiety integracyjne](http://technet.microsoft.com/library/hh295851.aspx) przy konstruowaniu elementów runbook do automatyzacji składników programu System Center i innych produktów.  Niektóre z tych pakietów integracyjnych obecnie są oparte na OIT, ale obecnie nie można przekonwertować na moduły integracji z powodu znanych problemów.  [Moduły integracji programu System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) obejmuje przekonwertowanego wersje tych pakietów integracyjnych, które mogą być importowane do automatyzacji Azure i automatyzacja zarządzania usługami.  

W wersji RTM narzędzia zaktualizowane wersje pakietów integracyjnych oparte na OIT, który może zostać przekonwertowany przy użyciu konwertera pakiet integracji zostanie opublikowana.  Wskazówki dotyczące zapewnia się ułatwi konwersji przy użyciu działań z pakietów integracyjnych nie jest oparty na OIT elementów runbook.

## <a name="runbook-converter"></a>Konwerter elementu Runbook
Konwerter Runbook konwertuje elementy runbook programu Orchestrator do [graficznych elementów runbook](automation-runbook-types.md#graphical-runbooks) który można zaimportować do usługi Automatyzacja Azure.  

Konwerter elementu Runbook jest zaimplementowany jako moduł programu PowerShell za pomocą polecenia cmdlet, o nazwie **ConvertFrom SCORunbook** wykonująca konwersji.  Po zainstalowaniu narzędzia zostanie utworzony skrót do sesji programu PowerShell, który ładuje polecenia cmdlet.   

Poniżej przedstawiono podstawowy proces konwersji elementu runbook programu Orchestrator i zaimportuj go do automatyzacji Azure.  Poniższe sekcje zawierają dodatkowe szczegóły za pomocą narzędzia i Praca z przekonwertowanego elementów runbook.

1. Eksportuj co najmniej jeden element runbook z programu Orchestrator.
2. Uzyskaj moduły integracji dla wszystkich działań w elemencie runbook.
3. Konwertuj elementy runbook programu Orchestrator w wyeksportowanym pliku.
4. Przejrzyj informacje w dziennikach, aby zweryfikować konwersji i określić wszelkie wymagane działania ręcznego.
5. Zaimportuj przekonwertowany elementów runbook do automatyzacji Azure.
6. Utwórz wszystkie wymagane zasoby w automatyzacji Azure.
7. Edytuj element runbook automatyzacji Azure, aby zmodyfikować wszelkie wymagane działania.

### <a name="using-runbook-converter"></a>Za pomocą konwertera elementu Runbook
Składnia **ConvertFrom SCORunbook** wygląda następująco:

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>

* RunbookPath — ścieżka do pliku eksportu, zawierającego elementy runbook, aby przekonwertować.
* Moduł — rozdzielana przecinkami lista moduły integracji zawierających działania w elementach runbook.
* OutputFolder — ścieżka do folderu, aby utworzyć przekonwertować graficznych elementów runbook.

Następujące przykładowe polecenie konwertuje elementy runbook w pliku eksportu o nazwie **MyRunbooks.ois_export**.  Te elementy runbook używać pakietów integracji usługi Active Directory i programu Data Protection Manager.

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"


### <a name="log-files"></a>Pliki dziennika
Konwerter Runbook utworzy następujące pliki dziennika w tej samej lokalizacji co przekonwertowanego elementu runbook.  Jeśli pliki już istnieją, następnie zostaną one zastąpione informacjami uzyskanymi z ostatnich konwersji.

| Plik | Zawartość |
|:--- |:--- |
| Konwerter Runbook - Progress.log |Szczegółowy opis kroków w tym informacje o każdym działaniu pomyślnie przekonwertowany i ostrzeżenie dla każdego działania nieprzekonwertowane konwersji. |
| Konwerter Runbook - Summary.log |Podsumowanie ostatniego konwersji, w tym wszelkie ostrzeżenia i postępuj zgodnie z zadań, które należy wykonać, takich jak tworzenie zmienna jest wymagana dla przekonwertowanego elementu runbook. |

### <a name="exporting-runbooks-from-orchestrator"></a>Eksportowanie elementów runbook z programu Orchestrator
Konwerter Runbook współpracuje z pliku eksportu programu Orchestrator, który zawiera jeden lub więcej elementów runbook.  Utworzy odpowiedni element runbook usługi Automatyzacja Azure dla każdego elementu runbook programu Orchestrator w pliku eksportu.  

Aby wyeksportować elementu runbook z programu Orchestrator, kliknij prawym przyciskiem myszy nazwę elementu runbook w programie Runbook Designer, a następnie wybierz **wyeksportować**.  Aby wyeksportować wszystkie elementy runbook w folderze, kliknij prawym przyciskiem myszy nazwę folderu i wybierz **wyeksportować**.

### <a name="runbook-activities"></a>Działania elementu Runbook
Konwerter Runbook konwertuje każde działanie w elemencie runbook programu Orchestrator do odpowiadającego im działania automatyzacji Azure.  Dla tych działań, których nie można przekonwertować aktywności symbol zastępczy jest tworzony w elementu runbook o tekst ostrzeżenia.  Po zaimportowaniu przekonwertowany element runbook do automatyzacji Azure należy zastąpić żadnego z tych działań z prawidłową działaniami, które uruchomienia wymaganych funkcji.

Wszystkie działania programu Orchestrator w [standardowy moduł działania](#standard-activities-module) zostanie przekonwertowany.  Brak niektórych standardowych działań programu Orchestrator, które nie znajdują się w tym module jednak i nie są konwertowane.  Na przykład **wysyłania zdarzenia platformy** nie zawiera usługi Automatyzacja Azure odpowiednika ponieważ zdarzenie jest specyficzne dla programu Orchestrator.

[Monitorowanie działań](https://technet.microsoft.com/library/hh403827.aspx) nie są konwertowane, ponieważ nie ma odpowiednika im w automatyzacji Azure.  Wyjątkiem są monitor działania w [konwertować Pakiety integracyjne](#integration-pack-converter) który zostanie przekonwertowany na działanie symbolu zastępczego.

Wszystkie działania z [przekonwertować pakiet integracyjny](#integration-pack-converter) zostaną przekonwertowane, podając ścieżkę do modułu integracji z **modułów** parametru.  Pakiety integracyjne programu System Center, można użyć [moduły integracji programu System Center Orchestrator](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Zasoby programu orchestrator
Konwerter Runbook konwertuje tylko elementy runbook, a nie inne zasoby programu Orchestrator liczników, zmiennych lub połączeń.  Liczniki są nieobsługiwane w automatyzacji Azure.  Połączenia i zmienne są obsługiwane, ale należy je utworzyć ręcznie.  Pliki dziennika zostaną informujące, jeśli element runbook wymaga tych zasobów i określ odpowiednie zasoby, które należy utworzyć w na może działać prawidłowo przekonwertowana elementu runbook usługi Automatyzacja Azure.

Na przykład element runbook może użyć zmiennej do wypełnienia konkretną wartość w działaniu.  Przekonwertowany element runbook będzie Konwertuj działania i określ zasób zmiennej automatyzacji Azure o takiej samej nazwie jako zmiennej Orchestrator.  Będą to wymienione w **Runbook konwertera - Summary.log** pliku, który jest tworzony po konwersji.  Należy ręcznie utworzyć ten zasób zmiennej automatyzacji Azure przed użyciem elementu runbook.

### <a name="input-parameters"></a>Parametry wejściowe
Elementy Runbook w programie Orchestrator akceptują parametrów wejściowych z **inicjalizowania danych** działania.  Jeśli element runbook konwertowanej zawiera to działanie, a następnie [parametru wejściowego](automation-graphical-authoring-intro.md#runbook-input-and-output) automatyzacji Azure element runbook został utworzony dla każdego parametru w działaniu.  A [kontroli przepływu pracy skryptu](automation-graphical-authoring-intro.md#activities) działania jest tworzony w przekonwertowany element runbook, który pobiera i zwraca każdego parametru.  Działaniami w elemencie runbook, które używają parametru wejściowego odwoływać się do danych wyjściowych z tego działania.

Przyczyna służy ta strategia jest najlepiej duplikatów funkcjonalność w elemencie runbook programu Orchestrator.  Działania w nowych graficznych elementów runbook, przejrzyj bezpośrednio przy użyciu źródła danych wejściowych Runbook parametrów wejściowych.

### <a name="invoke-runbook-activity"></a>Działania wywołania elementu Runbook
Elementy Runbook w programie Orchestrator uruchamiać inne elementy runbook z **Wywołaj element Runbook** działania. Jeśli konwertowanej element runbook zawiera działanie i **czekać na ukończenie** opcja jest ustawiona, a następnie działania elementu runbook jest tworzony dla niego w elemencie runbook przekonwertowany.  Jeśli **czekać na ukończenie** opcja nie jest ustawiona, a następnie tworzony jest działanie skryptu przepływu pracy, który używa **Start AzureAutomationRunbook** można uruchomić elementu runbook.  Po zaimportowaniu przekonwertowany element runbook do automatyzacji Azure należy zmodyfikować to działanie przy użyciu informacji określony w działaniu.

## <a name="related-articles"></a>Pokrewne artykuły:
* [System Center 2012 — Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
* [Automatyzacja zarządzania usługami](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrydowy proces roboczy elementu Runbook](automation-hybrid-runbook-worker.md)
* [Działania standardowe programu orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
* [Pobierz System Center Orchestrator Migration Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
