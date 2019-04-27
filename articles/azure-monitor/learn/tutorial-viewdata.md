---
title: Wyświetlanie i analizowanie zebranych danych usługi Azure Log Analytics | Microsoft Docs
description: Ten artykuł zawiera samouczek, w którym opisano sposób tworzenia wyszukiwań w dziennikach i analizowania danych przechowywanych w zasobie usługi Log Analytics przy użyciu portalu do wyszukiwania w dziennikach.  Samouczek obejmuje uruchamianie pewnych prostych zapytań na potrzeby zwracania różnych typów danych i analizowanie wyników.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/31/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: c64c0837956c59cf7a810bb06ca63bc21c5158d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60590421"
---
# <a name="view-or-analyze-data-collected-with-log-analytics-log-search"></a>Wyświetlanie i analizowanie zebranych danych za pomocą wyszukiwania w dziennikach usługi Log Analytics

W usłudze Log Analytics można korzystać z wyszukiwań w dziennikach, tworząc zapytania służące do analizowania zebranych danych, a także używać już istniejących pulpitów nawigacyjnych, które można dostosować za pomocą widoków graficznych dotyczących najbardziej wartościowych wyszukiwań.  Zdefiniowano już zbieranie danych operacyjnych z maszyn wirtualnych platformy Azure i dzienników aktywności, natomiast w tym samouczku omawiane są następujące czynności:

> [!div class="checklist"]
> * Wykonywanie prostego wyszukiwania danych dotyczących zdarzeń i modyfikowanie oraz filtrowanie wyników za pomocą funkcji 
> * Informacje o pracy z danymi wydajności

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna [połączona z obszarem roboczym usługi Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md).  

Istnieją dwa sposoby na tworzenie i edytowanie zapytań oraz interakcyjną pracę z danymi zwrotnymi.  W przypadku zapytań podstawowych używaj strony Wyszukiwanie w dziennikach w witrynie Azure Portal, a w przypadku zapytań zaawansowanych możesz używać portalu analizy zaawansowanej. Aby dowiedzieć się więcej na temat różnic w funkcjach tych dwóch portali, zobacz [Portals for creating and editing log queries in Azure Log Analytics](../../azure-monitor/log-query/portals.md) (Portale do tworzenia i edytowania zapytań dzienników w usłudze Azure Log Analytics).

W tym samouczku będziemy używać strony Wyszukiwanie w dziennikach w witrynie Azure Portal. 

## <a name="log-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com). 

## <a name="open-the-log-search-portal"></a>Otwieranie portalu do wyszukiwania w dziennikach 
Zacznij od otwarcia portalu do wyszukiwania w dziennikach.   

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Monitor**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Monitor**.
2. Z menu nawigacji Monitor wybierz pozycję **Log Analytics**, a następnie wybierz obszar roboczy.

## <a name="create-a-simple-search"></a>Tworzenie prostego wyszukiwania
Najszybszym sposobem pobrania danych do pracy jest użycie prostego zapytania zwracającego wszystkie rekordy w tabeli.  Jeśli z obszarem roboczym są połączeni jacyś klienci z systemem Windows lub Linux, istnieją dane w tabeli Event („Zdarzenie”, system Windows) lub Syslog („Dziennik systemu”, system Linux).

Wpisz jedno z następujących zapytań w polu wyszukiwania, a następnie kliknij przycisk wyszukiwania.  

```
Event
```
```
Syslog
```

Dane są zwracane w domyślnym widoku listy i widać, ile zwrócono wszystkich rekordów.

![Proste zapytanie](media/tutorial-viewdata/log-analytics-portal-eventlist-01.png)

Wyświetlanych jest tylko kilka pierwszych właściwości każdego rekordu.  Kliknij pozycję **Pokaż więcej** w celu wyświetlenia wszystkich właściwości dla określonego rekordu.

## <a name="filter-results-of-the-query"></a>Filtrowanie wyników zapytania
Po lewej stronie ekranu znajduje się okienko filtrowania, które pozwala dodać filtrowanie do zapytania bez modyfikowania go bezpośrednio.  Wyświetlanych jest kilka właściwości rekordu dla danego typu rekordu i można wybrać jedną lub więcej wartości właściwości, aby zawęzić wyniki wyszukiwania.

Jeśli pracujesz z tabelą **Event** (Zdarzenie), zaznacz pole wyboru obok pozycji **Error** (Błąd) w obszarze **EVENTLEVELNAME** (Nazwa zdarzenia).   Jeśli pracujesz z tabelą **Syslog** (Dziennik systemu), zaznacz pole wyboru obok pozycji **err** (Błąd) w obszarze **SEVERITYLEVEL** (Poziom ważności).  Spowoduje to zmianę zapytania na jedno z następujących w celu ograniczenia wyników do zdarzeń błędów.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filtr](media/tutorial-viewdata/log-analytics-portal-eventlist-02.png)

Dodaj właściwości do okienka filtrowania, wybierając pozycję **Dodaj do filtrów** z menu właściwości jednego z rekordów.

![Menu dodawania do filtrów](media/tutorial-viewdata/log-analytics-portal-eventlist-03.png)

Ten sam filtr można ustawić, wybierając pozycję **Filtruj** z menu właściwości rekordu z wartością do odfiltrowania.  

Opcja **Filtruj** istnieje tylko dla właściwości, których nazwy są oznaczone kolorem niebieskim po umieszczeniu na nich wskaźnika.  Są to pola *z możliwością wyszukiwania*, które są indeksowane na potrzeby warunków wyszukiwania.  Pola oznaczone kolorem szarym są polami *wyszukiwania dowolnego tekstu*, dla których jest dostępna tylko opcja **Pokaż odwołania**.  Ta opcja zwraca rekordy, dla których ta wartość istnieje w dowolnej właściwości.

Wyniki można grupować według pojedynczej właściwości, wybierając opcję **Grupuj według** z menu rekordu.  Spowoduje to dodanie do zapytania operatora [summarize](/azure/kusto/query/summarizeoperator) wyświetlającego wyniki na wykresie.  Można grupować według więcej niż jednej właściwości, ale w tym celu trzeba edytować zapytanie bezpośrednio.  Wybierz menu rekordu obok właściwości **Computer** (Komputer) i wybierz pozycję **Grupuj według „Computer”**.  

![Grupowanie według komputerów](media/tutorial-viewdata/log-analytics-portal-eventlist-04.png)

## <a name="work-with-results"></a>Praca z wynikami
Portal do wyszukiwania w dziennikach udostępnia wiele funkcji do pracy z wynikami zapytania.  Można sortować, filtrować i grupować wyniki w celu analizowania danych bez modyfikowania samego zapytania.  Domyślnie wyniki zapytania nie są sortowane.

Aby wyświetlić dane w postaci tabeli, która zapewnia dodatkowe opcje filtrowania i sortowania, kliknij pozycję **Tabela**.  

![Widok tabeli](media/tutorial-viewdata/log-search-portal-table-01.png)

Kliknij strzałkę obok rekordu, aby wyświetlić szczegóły dla tego rekordu.

![Sortowanie wyników](media/tutorial-viewdata/log-search-portal-table-02.png)

Sortuj według dowolnego pola, klikając nagłówek kolumny.

![Sortowanie wyników](media/tutorial-viewdata/log-search-portal-table-03.png)

Filtruj wyniki według określonej wartości w kolumnie, klikając przycisk filtru i podając warunek filtru.

![Filtrowanie wyników](media/tutorial-viewdata/log-search-portal-table-04.png)

Grupuj według kolumny przez przeciągnięcie nagłówka kolumny do góry listy wyników.  Można grupować według wielu pól, przeciągając wiele kolumn do góry.

![Grupowanie wyników](media/tutorial-viewdata/log-search-portal-table-05.png)


## <a name="work-with-performance-data"></a>Praca z danymi wydajności
Dane wydajności dla agentów systemów Windows i Linux są przechowywane w obszarze roboczym usługi Log Analytics w tabeli **Perf**.  Rekordy wydajności wyglądają jak dowolne inne rekordy i napiszemy proste zapytanie, które zwraca wszystkie rekordy wydajności, podobnie jak miało to miejsce w przypadku zdarzeń.

```
Perf
```

![Dane wydajności](media/tutorial-viewdata/log-analytics-portal-perfsearch-01.png)

Jednak zwracanie milionów rekordów wszystkich obiektów i liczników wydajności nie jest bardzo przydatne.  Za pomocą tych samych metod, które zostały użyte powyżej, można przefiltrować dane, a można też po prostu wpisać poniższe zapytanie bezpośrednio w polu wyszukiwania w dziennikach.  Zostaną zwrócone tylko rekordy użycia procesorów dla komputerów z systemem zarówno Windows, jak i Linux.

```
Perf | where ObjectName == "Processor"  | where CounterName == "% Processor Time"
```

![Użycie procesorów](media/tutorial-viewdata/log-analytics-portal-perfsearch-02.png)

To ogranicza dane do określonego licznika, ale nadal nie udostępnia ich w szczególnie przydatnej postaci.  Można wyświetlić dane na wykresie liniowym, ale najpierw należy je zgrupować według właściwości Computer (Komputer) i TimeGenerated (Godzina wygenerowania).  Aby zgrupować według wielu pól, należy bezpośrednio zmodyfikować zapytanie. Zmodyfikuj je więc do następującej postaci.  Wykorzystywana jest tu funkcja [avg](/azure/kusto/query/avg-aggfunction) działająca na właściwości **CounterValue** w celu obliczania średniej wartości dla każdej godziny.

```
Perf  
| where ObjectName == "Processor"  | where CounterName == "% Processor Time"
| summarize avg(CounterValue) by Computer, TimeGenerated
```

![Wykres danych wydajności](media/tutorial-viewdata/log-analytics-portal-perfsearch-03.png)

Dane są już odpowiednio zgrupowane, można je więc wyświetlić na wykresie przez dodanie operatora [render](/azure/kusto/query/renderoperator).  

```
Perf  
| where ObjectName == "Processor" | where CounterName == "% Processor Time" 
| summarize avg(CounterValue) by Computer, TimeGenerated 
| render timechart
```

![Wykres liniowy](media/tutorial-viewdata/log-analytics-portal-linechart-01.png)

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób tworzenia podstawowych wyszukiwań w dziennikach w celu analizowania danych zdarzeń i wydajności.  Przejdź do następnego samouczka, aby dowiedzieć się, jak zwizualizować dane przez utworzenie pulpitu nawigacyjnego.

> [!div class="nextstepaction"]
> [Tworzenie i udostępnianie pulpitów nawigacyjnych usługi Log Analytics](tutorial-logs-dashboards.md)
