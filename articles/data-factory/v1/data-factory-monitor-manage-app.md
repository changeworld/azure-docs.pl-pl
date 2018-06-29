---
title: Monitorowanie i zarządzanie nimi w potokach danych - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować i zarządzać nimi fabryki danych Azure i potoki przy użyciu aplikacji zarządzania i monitorowania.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3f234e49f1a28fd0881e47ede13ae72483ed31f3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053120"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Monitorowanie i zarządzanie nimi potoki fabryki danych Azure przy użyciu aplikacji monitorowanie i zarządzanie
> [!div class="op_single_selector"]
> * [Przy użyciu portalu Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Przy użyciu monitorowania i zarządzania aplikacjami](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [monitorować i zarządzać nimi potoków fabryki danych w](../monitor-visually.md).

W tym artykule opisano sposób użycia aplikacji zarządzania i monitorowania do monitorowania, zarządzania i debugowania potoki z fabryki danych. Możesz rozpocząć pracę z przy użyciu aplikacji od obejrzenia poniższego klipu wideo:

> [!NOTE]
> Interfejs użytkownika pokazano wideo może nie pasować zobaczyć w portalu. Jest nieco starszy, ale pojęcia pozostają takie same. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Uruchom aplikacji do zarządzania i monitorowania
Aby uruchomić Monitor i zarządzania aplikacji, kliknij przycisk **Monitor & Zarządzaj** Kafelek na **fabryki danych** bloku fabryką danych.

![Monitorowanie kafelka na stronie głównej fabryki danych](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Monitorowanie i zarządzanie aplikacji, Otwórz w osobnym oknie powinny być widoczne.  

![Aplikacja do monitorowania i zarządzania](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Jeśli zobaczysz, że przeglądarki sieci web jest zablokowany na "Autoryzowanie...", wyczyść **zablokować pliki cookie innych firm, a dane lokacji** pole wyboru--lub Zachowaj ono zaznaczone, utworzyć wyjątek **login.microsoftonline.com**, i Spróbuj ponownie otworzyć aplikację.


Na liście okien działania w środkowym okienku zobaczysz okno działania przy każdym uruchomieniu działania. Na przykład jeśli masz działania zaplanowane co godzinę przez pięć godzin, zobacz pięć okien działania związane z pięciu wycinków danych. Jeśli nie widzisz okien działania na liście u dołu, wykonaj następujące czynności:
 
- Aktualizacja **godzina rozpoczęcia** i **czas zakończenia** filtrów w górnej odpowiada godziny rozpoczęcia i zakończenia potoku sieci, a następnie kliknij przycisk **Zastosuj** przycisku.  
- Lista okien działania nie zostanie automatycznie odświeżony. Kliknij przycisk **Odśwież** przycisk na pasku narzędzi w **okien działania** listy.  

Jeśli nie masz aplikacji fabryki danych, aby przetestować te kroki samouczka wykonaj: [skopiowanie danych z magazynu obiektów Blob do bazy danych SQL przy użyciu fabryki danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Informacje o monitorowaniu i aplikacji do zarządzania
Dostępne są trzy karty po lewej stronie: **Eksploratora zasobów**, **widoków monitorowania**, i **alerty**. Pierwszej karcie (**Eksploratora zasobów**) jest domyślnie zaznaczona.

### <a name="resource-explorer"></a>Eksplorator zasobów
Zostanie wyświetlony poniżej:

* Eksplorator zasobów **widok drzewa** w okienku po lewej stronie.
* **Widoku diagramu** u góry w środkowym okienku.
* **Okien działania** listy u dołu w środkowym okienku.
* **Właściwości**, **działania okna Eksploratora**, i **skryptu** karty w okienku po prawej stronie.

W Eksploratorze zasobów zobacz temat wszystkie zasoby (potoki, zestawy danych połączonych usług) w fabryce danych w widoku drzewa. Po wybraniu obiektu, w Eksploratorze zasobów:

* Skojarzonej jednostki fabryki danych zostanie wyróżniona w widoku diagramu.
* [Skojarzone działanie windows](data-factory-scheduling-and-execution.md) zostały wyróżnione na liście okien działania u dołu.  
* Właściwości wybranego obiektu są wyświetlane w oknie właściwości w okienku po prawej stronie.
* Definicja JSON wybranego obiektu jest wyświetlany, jeśli ma to zastosowanie. Na przykład: połączonej usługi, zestawu danych lub potoku.

![Eksplorator zasobów](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Zobacz [planowania i wykonywania](data-factory-scheduling-and-execution.md) artykułu, aby uzyskać szczegółowe informacje o pojęciach dotyczących działania systemu windows.

### <a name="diagram-view"></a>Widok diagramu
Widok diagramu fabryki danych zapewnia jeden szkła monitorować i zarządzać fabryki danych i jego zasoby. Po wybraniu jednostki fabryki danych (dataset/potoku) w widoku diagramu:

* Obiekt fabryki danych wybrano w widoku drzewa.
* Skojarzone działanie systemu windows są wyróżnione na liście działania systemu Windows.
* Właściwości wybranego obiektu są wyświetlane w oknie właściwości.

Po włączeniu potoku (nie jest w stanie wstrzymania) jest wyświetlany zielony linią:

![Potok uruchomiona](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Można wstrzymać, wznowić lub zakończenie potoku, wybierając ją w widoku diagramu i za pomocą przycisków na pasku poleceń.

![Wstrzymanie/wznowienie na pasku poleceń](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Istnieją trzy przyciski paska poleceń dla potoku, w widoku diagramu. Drugi przycisk służy do wstrzymywania potoku. Wstrzymywanie nie przerwanie działania aktualnie uruchomione i pozwala przejść do zakończenia. Trzeci przycisk potoku wstrzymuje i kończy istniejące wykonywanych działań. Pierwszy przycisk wznawia potoku. Wstrzymanie planowaną zmienia kolor potoku. Na przykład wstrzymanie potoku wygląda jak na poniższej ilustracji: 

![Wstrzymane potoku](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Możesz wybrać wiele potoki dwóch lub więcej za pomocą klawisza Ctrl. Przyciski paska poleceń służy do wstrzymanie/wznowienie potoki wielu naraz.

Można również kliknij prawym przyciskiem myszy potoku i wybierz opcję, aby wstrzymać, wznowić lub przerywania potoku. 

![Menu kontekstowe dla potoku](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Kliknij przycisk **Otwórz potoku** opcji, aby zobaczyć wszystkie działania w potoku. 

![Menu Otwórz potok](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

W widoku otwartą potoku wyświetlane są wszystkie działania w potoku. W tym przykładzie jest tylko jedno działanie: działanie kopiowania. 

![Otwarty potoku](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Aby powrócić do poprzedniego widoku, kliknij przycisk Nazwa fabryki danych w menu stron nadrzędnych u góry.

W widoku potoku po wybraniu wyjściowy zestaw danych lub gdy mysz przesuwa się nad wyjściowy zestaw danych, zobaczysz okno podręczne działania systemu Windows dla tego zestawu danych.

![Okno podręczne działania systemu Windows](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Możesz kliknąć okno działania, aby wyświetlić szczegóły dla niego w **właściwości** okna w okienku po prawej stronie.

![Działanie właściwości okna](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

W okienku po prawej stronie, przełącz się do **działania okna Eksploratora** kartę, aby zobaczyć więcej szczegółów.

![Działanie okno Eksploratora](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Zobacz też **rozpoznać zmienne** dla każdego próba uruchomienia działania w **prób** sekcji.

![Zmienne rozwiązany](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Przełącz się do **skryptu** kartę, aby wyświetlić definicji skryptu JSON dla wybranego obiektu.   

![Karta skryptu](./media/data-factory-monitor-manage-app/ScriptTab.png)

Można wyświetlić okien działania w trzech miejscach:

* Okienko wyskakujące okien działania w widoku diagramu (w środkowym okienku).
* Eksplorator okna działania w okienku po prawej stronie.
* Lista okien działania w dolnym okienku.

W oknie podręcznym okien działania i działania okna Eksploratora można przewijać w poprzednim tygodniu i następnym tygodniu za pomocą strzałki w lewo i w prawo.

![Strzałki prawej/lewej strony okna Eksploratora działania](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

W dolnej części widoku diagramu, zobacz tych przycisków: powiększenia powiększanie, zmniejszanie, aby dopasować, powiększenie 100%, Zablokuj układ. **Układu blokady** przycisk zapobiega przypadkowemu przenoszeniu tabel i potoków w widoku diagramu. Jest on domyślnie. Można ją wyłączyć i przenosić jednostek na diagramie. Wyłączenie opcji, można użyć przycisku ostatniej ma automatycznie tabele i potoki. Za pomocą kółka myszy, można powiększyć lub pomniejszyć.

![Diagram przedstawiający polecenia powiększenia widoku](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista Okna działania
Na liście okien działania w dolnej części środkowym okienku zostaną wyświetlone wszystkie okna działania dla zestawu danych, który wybrano w widoku diagramu lub Eksploratora zasobów. Domyślnie lista jest w kolejności malejącej, co oznacza, że wyświetlane najnowsze okna działanie u góry.

![Lista Okna działania](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Ta lista nie automatycznego odświeżania, należy więc przycisk Odśwież na pasku narzędzi go ręcznie odświeżyć.  

Działanie systemu windows może być w jednym z następujących stanów:

<table>
<tr>
    <th align="left">Stan</th><th align="left">Podstan</th><th align="left">Opis</th>
</tr>
<tr>
    <td rowspan="8">Oczekiwanie</td><td>ScheduleTime</td><td>Czas nie pochodzą okna działania do uruchamiania.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Zależności strumienia wychodzącego nie są gotowe.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Zasoby obliczeniowe są niedostępne.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Wszystkie wystąpienia działania są zajęte uruchamianiem innych okien działania.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Działanie jest wstrzymane i nie można uruchomić okien działania, dopóki nie zostanie wznowione.</td>
</tr>
<tr>
<td>Ponawianie próby</td><td>Ponawiane wykonania działania.</td>
</tr>
<tr>
<td>Walidacja</td><td>Weryfikacja jeszcze się nie rozpoczął.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Sprawdzanie poprawności oczekuje na ponowienie próby.</td>
</tr>
<tr>
<tr>
<td rowspan="2">W toku</td><td>Sprawdzanie poprawności</td><td>Trwa sprawdzanie poprawności.</td>
</tr>
<td>-</td>
<td>Okno działania jest przetwarzana.</td>
</tr>
<tr>
<td rowspan="4">Niepowodzenie</td><td>Upłynął limit czasu</td><td>Wykonywanie działania trwało dłużej niż jest dozwolonych przez działanie.</td>
</tr>
<tr>
<td>Anulowane</td><td>Okno działanie zostało anulowane przez akcję użytkownika.</td>
</tr>
<tr>
<td>Walidacja</td><td>Weryfikacja nie powiodła się.</td>
</tr>
<tr>
<td>-</td><td>Okno działania nie powiodło się można wygenerować lub sprawdzania poprawności.</td>
</tr>
<td>Gotowe</td><td>-</td><td>Okno działania jest gotowy do użycia.</td>
</tr>
<tr>
<td>Pominięte</td><td>-</td><td>Okno działania nie został przetworzony.</td>
</tr>
<tr>
<td>Brak</td><td>-</td><td>Okno działania miał poprzednio inny stan, ale został zresetowany.</td>
</tr>
</table>


Po kliknięciu okno działania na liście, zobacz szczegóły w **Eksploratora Windows działania** lub **właściwości** okna po prawej stronie.

![Działanie okno Eksploratora](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Odśwież działania systemu windows
Szczegóły nie są automatycznie odświeżane, należy więc przycisk Odśwież (drugi przycisk) na pasku poleceń, aby ręcznie odświeżyć listę działania systemu windows.  

### <a name="properties-window"></a>Okno właściwości
Okno właściwości znajduje się w okienku prawej zarządzania i monitorowania aplikacji.

![Okno właściwości](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Wyświetla właściwości dla elementu wybranego w Eksploratorze zasobów (widok drzewa), widok diagramu lub listę okien działania.

### <a name="activity-window-explorer"></a>Działanie okno Eksploratora
**Działania okna Eksploratora** okna znajduje się w okienku prawej zarządzania i monitorowania aplikacji. Wyświetla szczegółowe informacje o oknie działania wybranego w oknie podręcznym okien działania lub listę okien działania.

![Działanie okno Eksploratora](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Można przełączyć do innego okna działania, klikając go w widoku kalendarza u góry. Umożliwia także przycisk strzałki w lewo/Strzałka w prawo na górze wyświetlić okien działania z poprzedniego tygodnia lub następnym tygodniu.

Przyciski paska narzędzi w okienku u dołu umożliwia ponownie uruchom okno działania lub Odśwież dane w okienku.

### <a name="script"></a>Skrypt
Można użyć **skryptu** kartę, aby wyświetlić definicji JSON wybranej jednostki fabryki danych (połączonej usługi, zestawu danych lub potoku).

![Karta skryptu](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Używanie widoków systemu
Monitorowanie i zarządzanie aplikacja zawiera widoki wbudowanych systemu (**ostatnie okien działania**, **nie powiodło się okien działania**, **okien działania w toku**) zezwalające na Możesz wyświetlić fabrykę danych w oknie ostatnie/nie powiodło się/w trakcie działania.

Przełącz się do **widoków monitorowania** karcie po lewej stronie, klikając go.

![Karta widoków monitorowania](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Obecnie istnieją trzy widoki systemowe, które są obsługiwane. Wybierz opcję, aby wyświetlić ostatnie działanie systemu windows, windows działanie nie powiodło się lub okien działania w toku na liście działania systemu Windows (w dolnej części środkowym okienku).

Po wybraniu **ostatnie okien działania** opcji, zobacz wszystkie ostatnie okien działania malejąco **ostatniej próby czasu**.

Można użyć **nie powiodło się okien działania** widok, aby wyświetlić wszystkich okien działania nie powiodło się na liście. Wybierz przedział działanie nie powiodło się na liście, aby zobaczyć szczegółowe informacje o nim w **właściwości** okna lub **działania okna Eksploratora**. Możesz również pobrać wszystkie dzienniki dla okna działanie nie powiodło się.

## <a name="sort-and-filter-activity-windows"></a>Sortuj i Filtruj działania systemu windows
Zmień **godzina rozpoczęcia** i **czas zakończenia** ustawienia paska poleceń do systemu windows działanie filtru. Po zmianie godziny rozpoczęcia i godzina zakończenia, kliknij przycisk Dalej, aby odświeżyć listę okien działania czas zakończenia.

![Czas rozpoczęcia i zakończenia](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Obecnie wszystkie godziny są w formacie UTC w aplikacji zarządzania i monitorowania.
>
>

W **listę okien działania**, kliknij nazwę kolumny (na przykład: stanu).

![Działanie systemu Windows lista kolumn menu](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Można wykonać następujące czynności:

* Sortowanie w kolejności rosnącej.
* Sortowanie w kolejności malejącej.
* Filtruj według jednego lub więcej wartości (gotowe, oczekiwania itd.).

Po określeniu filtru w kolumnie jest wyświetlany przycisk filtru włączone dla tej kolumny, co oznacza, że wartości w kolumnie są filtrowane wartości.

![Filtrować według kolumny listy działania systemu Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Aby wyczyścić filtry służy tym samym oknie podręcznym. Aby wyczyścić wszystkie filtry listę okien działania, kliknij przycisk Wyczyść filtr na pasku poleceń.

![Wyczyść wszystkie filtry listę okien działania](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Akcje usługi partia zadań
### <a name="rerun-selected-activity-windows"></a>Uruchom ponownie wybrane działanie systemu windows
Wybierz przedział działania, kliknij strzałkę w dół dla przycisku pierwszej pasek poleceń, a następnie wybierz **Uruchom ponownie** / **ponownie z powyżej w potoku**. Po wybraniu **ponownie z powyżej w potoku** opcji zwracające on również wszystkich okien działania nadrzędnego.
    ![Ponownie uruchom okno działania](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Można również wybrać wiele okien działania na liście i uruchom je ponownie w tym samym czasie. Można filtrować na podstawie stanu działania z systemem windows (na przykład:) — a następnie ponownie uruchom okien działania nie powiodło się po usunięciu problemu, który powoduje, że okien działania się niepowodzeniem. Zobacz sekcję poniżej, aby uzyskać więcej informacji dotyczących filtrowania okien działania na liście.  

### <a name="pauseresume-multiple-pipelines"></a>Wstrzymanie/wznowienie wielu potoki
Możesz multiselect potoki dwóch lub więcej za pomocą klawisza Ctrl. Przyciski paska poleceń (które są wyróżnione kolorem czerwonym prostokątem na poniższej ilustracji) służy do wstrzymanie/wznowienie je.

![Wstrzymanie/wznowienie na pasku poleceń](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
