---
title: Monitorowanie i zarządzanie nimi na błędy potoków danych — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować i zarządzać nimi potoków i fabryki danych platformy Azure za pomocą aplikacji monitorowanie i zarządzanie.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 5b70edd4f65538b52c70881258bc500a34b04d80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826751"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Monitorowanie potoków i zarządzanie nimi usługi Azure Data Factory przy użyciu aplikacji monitorowanie i zarządzanie
> [!div class="op_single_selector"]
> * [Przy użyciu portalu Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Za pomocą monitorowania i zarządzania aplikacjami](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [monitorowanie i zarządzanie nimi potoków usługi Data Factory w](../monitor-visually.md).

W tym artykule opisano, jak korzystać z aplikacji monitorowanie i zarządzanie do monitorowania i debugowania potoków usługi Data Factory oraz zarządzanie nimi. Możesz rozpocząć pracę z korzystania z aplikacji, obejrzyj poniższy film wideo:

> [!NOTE]
> Interfejs użytkownika przedstawiony w wideo może nie pasować widoczny w portalu. Jest nieco starszy, ale pojęcia pozostają takie same. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Uruchamianie monitorowania i zarządzania aplikacjami
Można uruchomić aplikacji monitorowanie i zarządzanie, kliknij przycisk **monitorowanie i zarządzanie** kafelków na **usługi Data Factory** bloku fabryki danych.

![Monitorowanie kafelka na stronie głównej fabryki danych](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Powinien zostać wyświetlony aplikacji monitorowanie i zarządzanie, Otwórz w osobnym oknie.  

![Aplikacja do monitorowania i zarządzania](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Jeśli zobaczysz, że przeglądarki sieci web jest zablokowany na "Autoryzowanie...", wyczyść **zablokować pliki cookie innych firm, a dane lokacji** pole wyboru--lub Zachowaj ono zaznaczone, utworzyć wyjątek **login.microsoftonline.com**, i Spróbuj ponownie otworzyć aplikację.


Na liście Windows działanie w środkowym okienku zobaczysz okno działania z dla każdego uruchomienia działania. Na przykład jeśli masz działania zaplanowane do uruchomienia co godzinę przez pięć godzin, zobaczysz pięć okien działania skojarzone z pięciu wycinki danych. Jeśli nie widzisz okna działania na liście u dołu wykonaj następujące czynności:
 
- Aktualizacja **czas rozpoczęcia** i **czas zakończenia** filtrów u góry, aby dopasować czas rozpoczęcia i zakończenia potoku, a następnie kliknij przycisk **Zastosuj** przycisku.  
- Na liście działań Windows nie są automatycznie odświeżane. Kliknij przycisk **Odśwież** przycisk na pasku narzędzi w **Windows działanie** listy.  

Jeśli masz aplikację usługi Data Factory, aby przetestować te czynności przy użyciu wykonać instrukcje z samouczka: [kopiowanie danych z magazynu obiektów Blob do usługi SQL Database przy użyciu usługi Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Informacje o monitorowaniu i aplikacji do zarządzania
Po lewej stronie znajdują się trzy karty: **Eksplorator zasobów**, **widoków monitorowania**, i **alerty**. Pierwsza karta (**Eksploratora zasobów**) jest domyślnie zaznaczone.

### <a name="resource-explorer"></a>Eksplorator zasobów
Zostaną wyświetlone następujące czynności:

* Eksplorator zasobów **widoku drzewa** w okienku po lewej stronie.
* **Widoku diagramu** u góry strony, w środkowym okienku.
* **Windows działanie** u dołu w środkowym okienku.
* **Właściwości**, **Eksploratorze okien działania**, i **skryptu** kart w okienku po prawej stronie.

W Eksploratorze zasobów zobaczysz wszystkie zasoby (potoki, zestawy danych, połączonych usług) w usłudze data factory w widoku drzewa. Po wybraniu obiektu, w Eksploratorze zasobów:

* Skojarzone jednostki usługi fabryka danych jest wyróżniony w widoku diagramu.
* [Skojarzone okien działania](data-factory-scheduling-and-execution.md) zostały wyróżnione na liście działań Windows u dołu.  
* Właściwości wybranego obiektu są wyświetlane w oknie dialogowym właściwości w okienku po prawej stronie.
* Definicji JSON wybranego obiektu jest wyświetlany, jeśli ma to zastosowanie. Na przykład: połączonej usługi, zestaw danych lub potoku.

![Eksplorator zasobów](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Zobacz [planowanie i wykonywanie](data-factory-scheduling-and-execution.md) artykuł, aby uzyskać szczegółowe informacje o pojęciach dotyczących okien działania.

### <a name="diagram-view"></a>Widok diagramu
Widok diagramu fabryki danych zapewnia jedną taflę szkła do monitorowania i zarządzania usługi data factory i jej zasobów. Po wybraniu jednostki fabryki danych (zestawu danych/potoku) w widoku diagramu:

* Jednostki fabryki danych jest wybrany w widoku drzewa.
* Skojarzone działanie systemu windows zostały wyróżnione na liście działań Windows.
* Właściwości wybranego obiektu są wyświetlane w oknie dialogowym właściwości.

Po włączeniu potoku (nie jest w stanie wstrzymania) przedstawiono z zielonym wierszu:

![Uruchamianie potoku](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Możesz wstrzymać, wznowić lub zakończenia potoku, wybierając ją w widoku diagramu i za pomocą przycisków na pasku poleceń.

![Wstrzymywanie i wznawianie, na pasku poleceń](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Istnieją trzy przyciski paska poleceń dla potoku w widoku diagramu. Drugi przycisk służy do wstrzymywania potoku. Wstrzymywanie nie zakończyć aktualnie uruchomionych działań i pozwala przejść do ukończenia. Trzeci przycisk wstrzymuje potok, a kończy, istniejące wykonywania działań. Pierwszy przycisk wznawia potoku. Jeśli potok jest wstrzymany, zmienia kolor potoku. Na przykład wstrzymania potok wyglądał jak na poniższej ilustracji: 

![Wstrzymano potoku](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Możesz wielokrotnego wyboru co najmniej dwóch potoków przy użyciu klawisza Ctrl. Przyciski paska poleceń umożliwia wstrzymywanie i wznawianie wiele potoków w danym momencie.

Możesz również kliknąć prawym przyciskiem myszy potok i wybierz opcje, aby wstrzymać, wznowić lub zakończenia potoku. 

![Menu kontekstowe dla potoku](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Kliknij przycisk **Otwórz potok** opcje, aby wyświetlić wszystkie działania w potoku. 

![Menu Otwórz potok](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

Widok otwartego potoku zobaczysz wszystkie działania w potoku. W tym przykładzie jest tylko jedno działanie: działaniem kopiowania. 

![Otwartego potoku](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Aby wrócić do poprzedniego widoku, kliknij nazwę fabryki danych, w menu linków do stron nadrzędnych u góry.

W widoku potoku po wybraniu wyjściowy zestaw danych lub gdy wskaźnik myszy przesuwa się nad wyjściowy zestaw danych, zobaczysz okno wyskakujące Windows działanie dla tego zestawu danych.

![Okno podręczne Windows działanie](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Możesz kliknąć pozycję okna działania, aby wyświetlić szczegóły dla niego w **właściwości** okna w okienku po prawej stronie.

![Właściwości okna działania](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

W okienku po prawej stronie, przełącz się do **Eksploratorze okien działania** kartę, aby zobaczyć więcej szczegółów.

![Eksploratorze okna działania](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Zobacz też **rozwiązane zmienne** dla każdej próby uruchomienia dla działania w **prób** sekcji.

![Zmienne rozwiązane](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Przełącz się do **skryptu** kartę, aby zobaczyć definicję skrypt JSON dla wybranego obiektu.   

![Karta skryptu](./media/data-factory-monitor-manage-app/ScriptTab.png)

Można wyświetlić okna działania w trzech miejscach:

* Okno podręczne Windows działanie w widoku diagramu (w środkowym okienku).
* Eksploratorze okien działania w okienku po prawej stronie.
* Lista Windows aktywności w dolnym okienku.

W oknie podręcznym Windows działanie i Eksploratorze okien działania można przewijać w poprzednim tygodniu i następnym tygodniu za pomocą strzałek w lewo i w prawo.

![Strzałki lewego/prawego Eksploratorze okna działania](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

W dolnej części widoku diagramu zostanie wyświetlony tych przycisków: Powiększanie, zmniejszanie, powiększenie do rozmiaru, Powiększ 100%, Zablokuj układ. **Układ blokady** przycisk zapobiega przypadkowemu przenoszeniu tabel i potoków w widoku diagramu. Jest ona włączona domyślnie. Można ją wyłączyć i poruszać jednostek na diagramie. Po wyłączeniu trybu, można użyć przycisku ostatniej ma automatycznie tabel i potoków. Przy użyciu kółka myszy, można powiększyć lub pomniejszyć.

![Polecenia powiększenia widoku diagramu](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista Okna działania
Na liście Windows działania, w dolnej części w środkowym okienku zostaną wyświetlone wszystkie okna działania dla zestawu danych, które wybrano w Eksploratorze zasobów lub w widoku diagramu. Domyślnie lista jest w kolejności malejącej, co oznacza, że widzieć najnowsze okna działania u góry.

![Lista Okna działania](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Ta lista nie odświeżyć się automatycznie, więc użyj przycisku Odśwież na pasku narzędzi, aby ręcznie odświeżyć je.  

Okien działania może mieć jeden z następujących stanów:

<table>
<tr>
    <th align="left">Stan</th><th align="left">Stan podrzędny</th><th align="left">Opis</th>
</tr>
<tr>
    <td rowspan="8">Oczekiwanie</td><td>ScheduleTime</td><td>Czas nie pochodzą okna działania do uruchomienia.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Zależności strumienia wychodzącego nie są gotowe.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Zasoby obliczeniowe nie są dostępne.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Wszystkie wystąpienia działania są zajęte uruchamianiem innych okien działania.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Działanie jest wstrzymane i nie można uruchomić okna działania, dopóki nie zostanie wznowione.</td>
</tr>
<tr>
<td>Ponawianie próby</td><td>Ponawiane wykonywania działania.</td>
</tr>
<tr>
<td>Walidacja</td><td>Weryfikacja jeszcze się nie rozpoczął.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Walidacja Trwa oczekiwanie na ponowienie próby.</td>
</tr>
<tr>
<tr>
<td rowspan="2">W toku</td><td>Sprawdzanie poprawności</td><td>Weryfikacja jest w toku.</td>
</tr>
<td>-</td>
<td>Okno działania jest przetwarzana.</td>
</tr>
<tr>
<td rowspan="4">Niepowodzenie</td><td>Przekroczono limit czasu</td><td>Wykonanie działanie trwało dłużej niż dozwolona przez działanie.</td>
</tr>
<tr>
<td>Anulowane</td><td>W oknie działanie zostało anulowane przez akcji przez użytkownika.</td>
</tr>
<tr>
<td>Walidacja</td><td>Weryfikacja nie powiodła się.</td>
</tr>
<tr>
<td>-</td><td>Okna działania nie może być wygenerowany zweryfikowane.</td>
</tr>
<td>Gotowy</td><td>-</td><td>Okno to działanie jest gotowe do użycia.</td>
</tr>
<tr>
<td>Pominięte</td><td>-</td><td>Okno działania nie został przetworzony.</td>
</tr>
<tr>
<td>Brak</td><td>-</td><td>Okno działania z miał poprzednio inny stan, ale zostało zresetowane.</td>
</tr>
</table>


Po kliknięciu okno działania z listy, zobacz szczegółowe informacje na temat **Eksplorator Windows działanie** lub **właściwości** okno po prawej stronie.

![Eksploratorze okna działania](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Odświeżanie okna działania
Szczegóły nie są automatycznie odświeżane, a więc przycisk odświeżania (drugi przycisk) na pasku poleceń, aby ręcznie odświeżyć lista okna działania.  

### <a name="properties-window"></a>Okno właściwości
W oknie właściwości znajduje się w okienku najdalej z prawej strony aplikacji monitorowanie i zarządzanie.

![Okno właściwości](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Wyświetla właściwości dla elementu, który wybrano w Eksploratorze zasobów (w widoku drzewa), w widoku diagramu lub na liście Windows działanie.

### <a name="activity-window-explorer"></a>Eksploratorze okna działania
**Eksploratorze okien działania** okna znajduje się w okienku najdalej z prawej strony w aplikacji monitorowanie i zarządzanie. Wyświetla szczegóły okna działania, które są wybrane w oknie podręcznym działania Windows lub na liście Windows działanie.

![Eksploratorze okna działania](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Przejście do innego okna działania, klikając go w widoku kalendarza u góry. Umożliwia także przycisków strzałek w lewo/Strzałka w prawo na górze będzie okien działania z poprzedniego tygodnia lub następnym tygodniu.

Przyciski paska narzędzi w dolnym okienku umożliwia ponowne uruchomienie okna działania ani nie odświeżaj szczegółowe informacje w okienku.

### <a name="script"></a>Skrypt
Możesz użyć **skryptu** kartę, aby wyświetlić definicję formatu JSON wybranej jednostki usługi Data Factory (połączone usługi, zestaw danych lub potoku).

![Karta skryptu](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Widoki systemowe
Aplikacja monitorowanie i zarządzanie zawiera widoki systemowe wbudowanych (**ostatnie okien działania**, **nie powiodło się okien działania**, **okien działania w toku**) umożliwiające Umożliwia wyświetlanie okien działania ostatnie/Niepowodzenie/w toku dla fabryki danych.

Przełącz się do **widoki monitorowania** karty po lewej stronie, klikając go.

![Karta widoków monitorowania](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Obecnie istnieją trzy widoki systemowe, które są obsługiwane. Wybierz opcję, aby zobaczyć najnowsze działania systemu windows, windows działanie zakończone niepowodzeniem lub okien działania w toku na liście działań Windows (w dolnej części okienka środkowej).

Po wybraniu **ostatnie okien działania** wyświetlona wszystkie ostatnie okien działania w kolejności malejącej **ostatnia próba czasu**.

Możesz użyć **nie powiodło się okien działania** widok, aby wyświetlić wszystkich okien działania nie powiodło się na liście. Wybierz okno działania nie powiodło się na liście, aby zobaczyć szczegółowe informacje na temat **właściwości** okna lub **Eksploratorze okien działania**. Można również pobrać wszystkie dzienniki w oknie działanie zakończone niepowodzeniem.

## <a name="sort-and-filter-activity-windows"></a>Sortowanie i filtrowanie okna działania
Zmiana **czas rozpoczęcia** i **czas zakończenia** ustawienia paska poleceń w systemie windows działanie filtru. Po zmianie godziny rozpoczęcia i czas zakończenia, kliknij przycisk obok godziny zakończenia, aby odświeżyć listę działań Windows.

![Czas rozpoczęcia i zakończenia](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Obecnie wszystkie godziny są w formacie UTC w aplikacji monitorowanie i zarządzanie.
>
>

W **listy Windows działanie**, kliknij nazwę kolumny (na przykład: Stan).

![Menu kolumny listy Windows działanie](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Możesz wykonać następujące czynności:

* Sortowanie w kolejności rosnącej.
* Sortowanie w kolejności malejącej.
* Filtruj według jedną lub więcej wartości (gotowe, oczekiwania i tak dalej).

Po określeniu filtru w kolumnie, zostanie wyświetlony przycisk filtru włączone dla tej kolumny, co oznacza, że wartości w kolumnie są filtrowane wartości.

![Filtruj według kolumny na liście działań Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Aby wyczyścić filtry, można użyć tego samego okna podręcznego. Aby wyczyścić wszystkie filtry dla listy Windows działanie, kliknij przycisk Wyczyść filtr na pasku poleceń.

![Wyczyść wszystkie filtry dla listy Windows działanie](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Akcje usługi batch
### <a name="rerun-selected-activity-windows"></a>Uruchom ponownie wybrane działanie systemu windows
Wybierz okno działania z, kliknij strzałkę w dół, aby dla pierwszego przycisku paska poleceń, a następnie wybierz **ponownie** / **ponownie przy użyciu nadrzędne w potoku**. Po wybraniu **ponownie przy użyciu nadrzędne w potoku** opcja umożliwia ponowne wykonanie wszystkich okien również działanie w strumieniu przychodzącym.
    ![Uruchom ponownie okno działania z](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Można również wybrać wiele okien działania na liście i uruchom je ponownie w tym samym czasie. Być może chcesz filtrować okien działania na podstawie stanu (na przykład: **Nie powiodło się**) — a następnie ponownie uruchom okien działania nie powiodło się po usunięciu problemu, który powoduje, że okien działania nie powiedzie się. Zobacz sekcję następujące szczegółowe informacje na temat filtrowania okien działania na liście.  

### <a name="pauseresume-multiple-pipelines"></a>Wstrzymywanie i wznawianie wiele potoków
Możesz wyboru wielokrotnego co najmniej dwóch potoków przy użyciu klawisza Ctrl. Przyciski paska poleceń, (które są wyróżnione kolorem czerwonym prostokąt na poniższej ilustracji) umożliwia wstrzymywanie i wznawianie ich.

![Wstrzymywanie i wznawianie, na pasku poleceń](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
