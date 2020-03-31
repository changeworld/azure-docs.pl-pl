---
title: Monitorowanie potoków danych i zarządzanie nimi — Azure
description: Dowiedz się, jak używać aplikacji Monitorowanie i zarządzanie do monitorowania fabryk danych i potoków platformy Azure oraz zarządzania nimi.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: de56ba1281d0f20c8be838fa1bc9ebc24905b26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260373"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Monitorowanie potoków usługi Azure Data Factory i zarządzanie nimi przy użyciu aplikacji Monitorowanie i zarządzanie
> [!div class="op_single_selector"]
> * [Korzystanie z portalu Azure/usługi Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Korzystanie z aplikacji Monitorowanie i zarządzanie](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [monitorowanie potoków fabryki danych i zarządzanie nimi w pliku](../monitor-visually.md).

W tym artykule opisano, jak używać aplikacji Monitorowanie i zarządzanie do monitorowania, zarządzania i debugowania potoków fabryki danych. Możesz rozpocząć korzystanie z aplikacji, oglądając następujący film:

> [!NOTE]
> Interfejs użytkownika pokazany w filmie może nie do końca odpowiadać temu, co widzisz w portalu. Jest nieco starszy, ale koncepcje pozostają takie same. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Uruchamianie aplikacji Monitorowanie i zarządzanie
Aby uruchomić aplikację Monitor i zarządzanie, kliknij kafelek **Monitor & Zarządzaj** na bloku **Fabryka danych** dla fabryki danych.

![Kafelek Monitorowania na stronie głównej fabryki danych](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Powinna zostać wyświetlona aplikacja Monitorowanie i zarządzanie otwarta w osobnym oknie.  

![Aplikacja do monitorowania i zarządzania](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Jeśli widzisz, że przeglądarka internetowa utknęła w polu "Autoryzowanie...", wyczyść pole wyboru **Blokuj pliki cookie i dane witryny** lub zachowaj je zaznaczone, utwórz wyjątek dla **login.microsoftonline.com**, a następnie spróbuj ponownie otworzyć aplikację.


Na liście System aktywności systemu Windows w środkowym okienku zostanie wyświetlona okno aktywności dla każdego uruchomienia działania. Na przykład jeśli działanie jest zaplanowane do uruchamiania co godzinę przez pięć godzin, zostanie wyświetlonych pięć okien aktywności skojarzonych z pięcioma plasterkami danych. Jeśli nie widzisz okien aktywności na liście u dołu, wykonaj następujące czynności:
 
- Zaktualizuj filtry **czasu rozpoczęcia** i **zakończenia** u góry, aby dopasować czas rozpoczęcia i zakończenia potoku, a następnie kliknij przycisk **Zastosuj.**  
- Lista System Windows działania nie jest automatycznie odświeżana. Kliknij przycisk **Odśwież** na pasku narzędzi na liście **Działania systemu Windows.**  

Jeśli nie masz aplikacji Data Factory do testowania tych kroków, wykonaj samouczek: [skopiuj dane z magazynu obiektów Blob do bazy danych SQL przy użyciu fabryki danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Opis aplikacji Monitorowanie i zarządzanie
Po lewej stronie znajdują się trzy karty: **Eksplorator zasobów,** **Widoki monitorowania**i **Alerty**. Pierwsza karta **(Eksplorator zasobów)** jest zaznaczona domyślnie.

### <a name="resource-explorer"></a>Eksplorator zasobów
Widzisz następujące elementy:

* **Widok drzewa** Eksploratora zasobów w lewym okienku.
* **Widok diagramu** u góry w środkowym okienku.
* Lista **System Windows aktywności** u dołu w środkowym okienku.
* Karty **Właściwości,** **Eksplorator okien aktywności**i **Skrypt** w prawym okienku.

W Eksploratorze zasobów w widoku drzewa są wyświetlane wszystkie zasoby (potoki, zestawy danych, połączone usługi). Po wybraniu obiektu w Eksploratorze zasobów:

* Skojarzona jednostka Data Factory jest wyróżniona w widoku diagramu.
* [Skojarzone okna aktywności](data-factory-scheduling-and-execution.md) są wyróżnione na liście System aktywności systemu Windows u dołu.  
* Właściwości zaznaczonego obiektu są wyświetlane w oknie Właściwości w prawym okienku.
* Definicja JSON wybranego obiektu jest wyświetlana, jeśli ma to zastosowanie. Na przykład: usługa połączona, zestaw danych lub potok.

![Eksplorator zasobów](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Zobacz [harmonogram i wykonanie](data-factory-scheduling-and-execution.md) artykułu szczegółowe informacje koncepcyjne dotyczące okien działania.

### <a name="diagram-view"></a>Widok diagramu
Widok diagramu fabryki danych udostępnia pojedynczą szybę w celu monitorowania fabryki danych i zarządzania nią oraz jej zasobów. Po wybraniu jednostki Data Factory (zestaw danych/potok) w widoku diagramu:

* Jednostka fabryki danych jest zaznaczona w widoku drzewa.
* Skojarzone okna aktywności są wyróżnione na liście System aktywności systemu Windows.
* Właściwości zaznaczonego obiektu są wyświetlane w oknie Właściwości.

Gdy potok jest włączony (nie w stanie wstrzymania), jest wyświetlany z zieloną linią:

![Rurociąg uruchomiony](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Można wstrzymać, wznowić lub zakończyć potok, zaznaczając go w widoku diagramu i używając przycisków na pasku poleceń.

![Wstrzymanie/wznowienie na pasku poleceń](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Istnieją trzy przyciski paska poleceń dla potoku w widoku diagramu. Można użyć drugiego przycisku, aby wstrzymać potok. Wstrzymanie nie kończy aktualnie uruchomionych działań i pozwala im przystąpić do zakończenia. Trzeci przycisk wstrzymuje potok i kończy jego istniejące działania wykonujące. Pierwszy przycisk wznawia potoku. Gdy potok jest wstrzymany, zmienia się kolor potoku. Na przykład wstrzymany potok wygląda na poniższej ilustracji: 

![Wstrzymany potok](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Za pomocą klawisza Ctrl można wybrać dwa lub więcej potoków. Za pomocą przycisków paska poleceń można wstrzymać/wznowić wiele potoków jednocześnie.

Można również kliknąć prawym przyciskiem myszy potok i wybrać opcje, aby zawiesić, wznowić lub zakończyć potok. 

![Menu kontekstowe dla potoku](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Kliknij opcję **Otwórz potok,** aby wyświetlić wszystkie działania w potoku. 

![Menu Otwórz potok](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

W widoku otwartego potoku zostaną wyświetlenia wszystkich działań w potoku. W tym przykładzie istnieje tylko jedno działanie: Działanie kopiowania. 

![Otwarty rurociąg](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Aby wrócić do poprzedniego widoku, kliknij nazwę fabryki danych w menu nasadowy u góry.

W widoku potoku po wybraniu wyjściowego zestawu danych lub przesunięciu wskaźnika myszy na wyjściowy zestaw danych zostanie wyświetlane okno podręczne System Windows działania dla tego zestawu danych.

![Wyskakujące okno systemu Windows aktywności](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Możesz kliknąć okno aktywności, aby wyświetlić szczegóły dla niego w oknie **Właściwości** w prawym okienku.

![Właściwości okna aktywności](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

W prawym okienku przełącz się na kartę **Eksploratora okien aktywności,** aby wyświetlić więcej szczegółów.

![Eksplorator okien aktywności](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Dostępne są również **zmienne rozwiązane** dla każdej próby uruchomienia dla działania w sekcji **Próby.**

![Rozwiązane zmienne](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Przełącz się na kartę **Skrypt,** aby wyświetlić definicję skryptu JSON dla zaznaczonego obiektu.   

![Karta Skrypt](./media/data-factory-monitor-manage-app/ScriptTab.png)

Okna aktywności można zobaczyć w trzech miejscach:

* Okno podręczne System Windows aktywności w widoku diagramu (środkowe okienko).
* Eksplorator okien działania w prawym okienku.
* Lista System Aktywności systemu Windows w dolnym okienku.

W oknie podręcznym aktywność systemu Windows i Eksploratorze okien aktywności możesz przewijać do poprzedniego tygodnia i następnego tygodnia za pomocą strzałek w lewo i w prawo.

![Explorer okno działania strzałki w lewo/w prawo](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

U dołu widoku diagramu widoczne są następujące przyciski: Powiększanie, Pomniejszanie, Powiększanie do dopasowania, Powiększanie 100%, Układ blokady. Przycisk **Zablokuj układ** zapobiega przypadkowemu przemieszczaniu tabel i potoków w widoku diagramu. Jest domyślnie włączony. Można go wyłączyć i przenieść elementy wokół na diagramie. Po wyłączeniu można użyć ostatniego przycisku, aby automatycznie ustawić tabele i potoki. Za pomocą kółka myszy można również powiększać lub pomniejszać widok.

![Polecenia powiększenia widoku diagramu](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista Okna działania
Lista System Aktywności w systemie Windows u dołu środkowego okienka wyświetla wszystkie okna aktywności dla zestawu danych wybranego w Eksploratorze zasobów lub widoku diagramu. Domyślnie lista jest w porządku malejącym, co oznacza, że u góry jest widoczne ostatnie okno aktywności.

![Lista Okna działania](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Ta lista nie odświeża się automatycznie, więc użyj przycisku odświeżania na pasku narzędzi, aby ręcznie ją odświeżyć.  

Okna aktywności mogą znajdować się w jednym z następujących stanów:

<table>
<tr>
    <th align="left">Stan</th><th align="left">Podstanu</th><th align="left">Opis</th>
</tr>
<tr>
    <td rowspan="8">Oczekiwanie</td><td>Czas harmonogramu</td><td>Nie nadszedł czas, aby uruchomić okno aktywności.</td>
</tr>
<tr>
<td>Zestaw danychZależności</td><td>Zależności nadrzędne nie są gotowe.</td>
</tr>
<tr>
<td>Zasoby obliczeniowe</td><td>Zasoby obliczeniowe nie są dostępne.</td>
</tr>
<tr>
<td>WspółbieżnośćLiczek</td> <td>Wszystkie wystąpienia działania są zajęte uruchamianiem innych okien aktywności.</td>
</tr>
<tr>
<td>AktywnośćResume</td><td>Działanie jest wstrzymane i nie można uruchomić okna działania, dopóki nie zostanie wznowione.</td>
</tr>
<tr>
<td>Ponawianie próby</td><td>Wykonanie działania jest ponowione.</td>
</tr>
<tr>
<td>Sprawdzanie poprawności</td><td>Sprawdzanie poprawności jeszcze się nie rozpoczęło.</td>
</tr>
<tr>
<td>Sprawdzanie poprawności</td><td>Sprawdzanie poprawności czeka na ponowne ponowione.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress (Ruch przychodzący)</td><td>Sprawdzanie poprawności</td><td>Trwa sprawdzanie poprawności.</td>
</tr>
<td>-</td>
<td>Okno działania jest przetwarzane.</td>
</tr>
<tr>
<td rowspan="4">Niepowodzenie</td><td>Limit czasu</td><td>Wykonanie działania trwało dłużej niż to, co jest dozwolone przez działanie.</td>
</tr>
<tr>
<td>Anulowane</td><td>Okno działania zostało anulowane przez akcję użytkownika.</td>
</tr>
<tr>
<td>Sprawdzanie poprawności</td><td>Sprawdzanie poprawności nie powiodło się.</td>
</tr>
<tr>
<td>-</td><td>Nie można wygenerować lub zweryfikować okna działania.</td>
</tr>
<td>Gotowy</td><td>-</td><td>Okno działania jest gotowe do użycia.</td>
</tr>
<tr>
<td>Pominięto</td><td>-</td><td>Okno działania nie zostało przetworzone.</td>
</tr>
<tr>
<td>Brak</td><td>-</td><td>Okno aktywności istniało w innym stanie, ale zostało zresetowane.</td>
</tr>
</table>


Po kliknięciu okna aktywności na liście, zobaczysz szczegóły na jego temat w **Eksploratorze windows działania** lub **właściwości** okna po prawej stronie.

![Eksplorator okien aktywności](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Odświeżanie okien aktywności
Szczegóły nie są automatycznie odświeżane, więc użyj przycisku odświeżania (drugiego przycisku) na pasku poleceń, aby ręcznie odświeżyć listę okien aktywności.  

### <a name="properties-window"></a>Okno właściwości
Okno Właściwości znajduje się w prawym okienku aplikacji Monitorowanie i zarządzanie.

![Okno właściwości](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Wyświetla właściwości elementu wybranego w Eksploratorze zasobów (widok drzewa), widoku diagramu lub aktywności systemu Windows.

### <a name="activity-window-explorer"></a>Eksplorator okien aktywności
Okno **Eksploratora okien działania** znajduje się w prawym okienku aplikacji Monitorowanie i zarządzanie. Wyświetla szczegółowe informacje o oknie działania wybranym w wyskakującym oknie aktywności systemu Windows lub na liście Aktywność systemu Windows.

![Eksplorator okien aktywności](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Możesz przełączyć się do innego okna aktywności, klikając je w widoku kalendarza u góry. Możesz również użyć przycisków strzałki w lewo/strzałki w prawo u góry, aby wyświetlić okna aktywności z poprzedniego tygodnia lub następnego tygodnia.

Za pomocą przycisków paska narzędzi w dolnym okienku można ponownie uruchomić okno aktywności lub odświeżyć szczegóły w okienku.

### <a name="script"></a>Skrypt
Za pomocą karty **Skrypt** można wyświetlić definicję JSON wybranej jednostki Data Factory (połączonej usługi, zestawu danych lub potoku).

![Karta Skrypt](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Korzystanie z widoków systemowych
Aplikacja Monitorowanie i zarządzanie zawiera wstępnie utworzone widoki systemowe **(Ostatnie okna aktywności,** **Okna działań nieudanych,** **Okna aktywności w toku),** które umożliwiają wyświetlanie okien działań ostatnich/nieudanych/w toku dla fabryki danych.

Przełącz się na kartę **Widoki monitorowania** po lewej stronie, klikając ją.

![Karta Monitorowanie widoków](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Obecnie istnieją trzy widoki systemu, które są obsługiwane. Wybierz opcję, aby wyświetlić okna ostatniej aktywności, okna działań, które nie powiodły się lub w toku na liście System aktywności systemu Windows (u dołu środkowego okienka).

Po wybraniu opcji **Ostatnie okna działania** są widoczne wszystkie ostatnie okna aktywności w kolejności malejącej czasu ostatniej **próby**.

Można użyć widoku **okna działań niepowodzeniem,** aby wyświetlić wszystkie okna działań, które nie powiodły się na liście. Wybierz okno aktywności, które nie powiodło się na liście, aby wyświetlić szczegółowe informacje na jego temat w oknie **Właściwości** lub **Eksploratorze okien działania**. Można również pobrać wszystkie dzienniki dla okna aktywności nie powiodło się.

## <a name="sort-and-filter-activity-windows"></a>Sortowanie i filtrowanie okien aktywności
Zmień **ustawienia czasu rozpoczęcia** i **zakończenia na** pasku poleceń, aby filtrować okna aktywności. Po zmianie czasu rozpoczęcia i zakończenia kliknij przycisk obok czasu zakończenia, aby odświeżyć listę Aktywności systemu Windows.

![Godziny rozpoczęcia i zakończenia](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Obecnie wszystkie czasy są w formacie UTC w aplikacji Monitorowanie i zarządzanie.
>
>

Na **liście System aktywności systemu Windows**kliknij nazwę kolumny (na przykład: Stan).

![Menu kolumny listy aktywności systemu Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Można wykonać następujące czynności:

* Sortuj w porządku rosnącym.
* Sortuj w porządku malejącym.
* Filtruj według co najmniej jednej wartości (Gotowe, Oczekiwanie itd.).

Po określeniu filtru w kolumnie zostanie wyświetlony przycisk filtru włączony dla tej kolumny, który wskazuje, że wartości w kolumnie są wartościami filtrowanymi.

![Filtrowanie w kolumnie listy System Windows działania](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Aby wyczyścić filtry, można użyć tego samego okna podręcznego. Aby wyczyścić wszystkie filtry listy System Windows działania, kliknij przycisk wyczyść filtr na pasku poleceń.

![Wyczyść wszystkie filtry dla listy System aktywności systemu Windows](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Wykonywanie akcji wsadowych
### <a name="rerun-selected-activity-windows"></a>Ponowne przygotranianie wybranych okien aktywności
Wybierz okno aktywności, kliknij strzałkę w dół dla pierwszego przycisku paska poleceń i wybierz pozycję **Uruchom ponownie** / **z potokiem.** Po wybraniu **opcji Uruchom ponownie z upstream w potoku,** ponownie wszystkie okna działania nadrzędnego, jak również.
    ![Ponowne uruchomienie okna działania](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Można również wybrać wiele okien aktywności na liście i uruchomić je ponownie w tym samym czasie. Można filtrować okna aktywności na podstawie stanu (na przykład: **Nie powiodło się**)-- a następnie ponownie uruchomić okna działań nie powiodło się po skorygowaniu problemu, który powoduje niepowodzenie okien aktywności. Zobacz poniższą sekcję, aby uzyskać szczegółowe informacje na temat filtrowania okien aktywności na liście.  

### <a name="pauseresume-multiple-pipelines"></a>Wstrzymaj/wznowić wiele potoków
Za pomocą klawisza Ctrl można wybrać wiele lub więcej potoków. Można użyć przycisków paska poleceń (które są wyróżnione czerwonym prostokątem na poniższej ilustracji), aby je wstrzymać/wznowić.

![Wstrzymanie/wznowienie na pasku poleceń](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
