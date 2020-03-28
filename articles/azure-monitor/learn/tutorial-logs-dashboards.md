---
title: Tworzenie i udostępnianie pulpitów nawigacyjnych z danymi usługi Azure Log Analytics | Microsoft Docs
description: Ten samouczek pomaga zrozumieć, jak pulpity nawigacyjne usługi Log Analytics mogą wizualizować wszystkie zapisane zapytania dziennika, co daje jeden obiektyw do wyświetlania środowiska.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: 76ba79561df4a75004369d24c4c6af82de9b1cfc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77661536"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Tworzenie i udostępnianie pulpitów nawigacyjnych z danymi usługi Log Analytics

Pulpity nawigacyjne usługi Log Analytics mogą wizualizować wszystkie zapisane zapytania dziennika, co umożliwia znajdowanie, korelowanie i udostępnianie danych operacyjnych IT w organizacji.  W tym samouczku opisano tworzenie kwerendy dziennika, która będzie używana do obsługi udostępnionego pulpitu nawigacyjnego, który będzie dostępny dla zespołu pomocy technicznej operacji IT.  Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie udostępnionego pulpitu nawigacyjnego w witrynie Azure Portal
> * Wizualizuj kwerendę dziennika wydajności 
> * Dodawanie kwerendy dziennika do udostępnionego pulpitu nawigacyjnego 
> * Dostosowywanie kafelka na udostępnionym pulpicie nawigacyjnym

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna [połączona z obszarem roboczym usługi Log Analytics](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Tworzenie udostępnionego pulpitu nawigacyjnego
Wybierz **pulpit nawigacyjny,** aby otworzyć domyślny [pulpit nawigacyjny](../../azure-portal/azure-portal-dashboards.md). Pulpit nawigacyjny będzie wyglądał inaczej niż w poniższym przykładzie.

![Pulpit nawigacyjny witryny Azure Portal](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

W tym miejscu możesz zebrać najważniejsze dla działu IT dane operacyjne dotyczące wszystkich zasobów platformy Azure, w tym dane telemetryczne z usługi Azure Log Analytics.  Zanim zaczniemy wizualizować kwerendę dziennika, najpierw utwórzmy pulpit nawigacyjny i udostępnijmy go.  Następnie możemy skupić się na naszym przykładowym zapytaniu dziennika wydajności, które będzie renderowane jako wykres liniowy i dodać go do pulpitu nawigacyjnego.  

Aby utworzyć pulpit nawigacyjny, wybierz przycisk **Nowy pulpit nawigacyjny** obok nazwy bieżącego pulpitu nawigacyjnego.

![Tworzenie nowego pulpitu nawigacyjnego w witrynie Azure Portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Ta akcja tworzy nowy, pusty, prywatny pulpit nawigacyjny i przenosi użytkownika do trybu dostosowania, w którym można nazwać pulpit nawigacyjny oraz dodać kafelki i zmienić ich ułożenie. Edytuj nazwę pulpitu nawigacyjnego i określ *przykładowy pulpit nawigacyjny* dla tego samouczka, a następnie wybierz pozycję **Gotowe dostosowywanie**.<br><br> ![Zapisywanie dostosowanego pulpitu nawigacyjnego platformy Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Podczas tworzenia pulpitu nawigacyjnego jest on domyślnie prywatny, co oznacza, że użytkownik jest jedyną osobą, która może go zobaczyć. Aby uwidocznić go dla innych osób, użyj przycisku **Udostępnij**, który jest wyświetlany wraz z innymi poleceniami pulpitu nawigacyjnego.

![Udostępnianie nowego pulpitu nawigacyjnego w witrynie Azure Portal](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

Zostanie wyświetlona prośba o wybór subskrypcji i grupy zasobów, dla których zostanie opublikowany pulpit nawigacyjny. Dla wygody środowisko publikowania w portalu skieruje Cię do wzorca, w którym umieszcza się pulpity nawigacyjne w grupie zasobów o nazwie **pulpity nawigacyjne**.  Sprawdź wybraną subskrypcję, a następnie kliknij przycisk **Publikuj**.  Dostęp do informacji wyświetlanych na pulpicie nawigacyjnym jest kontrolowany przy użyciu [Kontroli dostępu opartej na zasobach platformy Azure](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Wizualizacja kwerendy dziennika
[Usługa Log Analytics](../log-query/get-started-portal.md) to dedykowany portal używany do pracy z zapytaniami dziennika i ich wynikami. Funkcje obejmują możliwość edytowania zapytania w wielu wierszach, selektywnego wykonywania kodu, kontekstowego używania funkcji IntelliSense oraz korzystania z funkcji analizy inteligentnej. W tym samouczku użyjesz usługi Log Analytics, aby utworzyć widok wydajności w formie graficznej, zapisać go dla przyszłej kwerendy i przypiąć do udostępnionego pulpitu nawigacyjnego utworzonego wcześniej.

Otwórz usługę Log Analytics, wybierając **dzienniki** w menu Usługi Azure Monitor. Zaczyna się od nowej pustej kwerendy.

![Strona główna](media/tutorial-logs-dashboards/homepage.png)

Wprowadź następującą kwerendę, aby zwrócić rekordy wykorzystania procesora dla komputerów z systemem Windows i Linux, pogrupowane według komputerów i czasu generowane i wyświetlane na wykresie wizualnym. Kliknij **przycisk Uruchom,** aby uruchomić kwerendę i wyświetlić wynikowy wykres.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Zapisz kwerendę, wybierając przycisk **Zapisz** u góry strony.

![Zapisz kwerendę](media/tutorial-logs-dashboards/save-query.png)

W panelu sterowania **Zapisywanie kwerend podaj** nazwę, taką jak *maszyny wirtualne platformy Azure — wykorzystanie procesora* i kategorię, taką jak *pulpity nawigacyjne,* a następnie kliknij przycisk **Zapisz**.  W ten sposób można utworzyć bibliotekę typowych zapytań, które można używać i modyfikować.  Na koniec przypnij to do udostępnionego pulpitu nawigacyjnego utworzonego wcześniej, wybierając przycisk **Przypnij do pulpitu nawigacyjnego** w prawym górnym rogu strony, a następnie wybierając nazwę pulpitu nawigacyjnego.

Teraz, po przypięciu zapytania do pulpitu nawigacyjnego, zauważysz, że ma ogólny tytuł, a pod nim komentarz.

![Przykładowy pulpit nawigacyjny platformy Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Zmieńmy tytuł na coś znaczącego, co będzie łatwe do zrozumienia dla osób wyświetlających zapytanie.  Kliknij przycisk edycji, aby dostosować tytuł i napisy kafelka, a następnie kliknij pozycję **Aktualizuj**.  Zostanie wyświetlony baner z prośbą o opublikowanie lub odrzucenie zmian.  Kliknij **pozycję Zapisz kopię**.  

![Ukończona konfiguracja przykładowego pulpitu nawigacyjnego](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Następne kroki
W tym samouczku dowiesz się, jak utworzyć pulpit nawigacyjny w witrynie Azure portal i dodać do niego kwerendę dziennika.  Przejdź do następnego samouczka, aby dowiedzieć się różnych odpowiedzi, które można zaimplementować na podstawie wyników kwerendy dziennika.  

> [!div class="nextstepaction"]
> [Odpowiadanie na zdarzenia przy użyciu alertów usługi Log Analytics](tutorial-response.md)
