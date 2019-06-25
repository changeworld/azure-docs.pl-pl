---
title: Tworzenie i udostępnianie pulpitów nawigacyjnych z danymi usługi Azure Log Analytics | Microsoft Docs
description: Ten samouczek ułatwia zrozumienie, jak pulpity nawigacyjne usługi Log Analytics mogą wizualizować wszystkie zapisane dziennika zapytań, zapewniając pojedynczy obiektywy, aby wyświetlić swoje środowisko.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 93cda8680bc665055d449e86c24d6565f6fc525f
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296411"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Tworzenie i udostępnianie pulpitów nawigacyjnych z danymi usługi Log Analytics

Usługi log Analytics pulpity nawigacyjne mogą wizualizować wszystkie zapisane dziennika zapytań, dzięki czemu umożliwia znajdowanie, korelowanie i udostępnianie danych operacyjnych IT w organizacji.  W tym samouczku opisano tworzenie zapytań dziennika, która będzie służyć do obsługi udostępnionego pulpitu nawigacyjnego, który będzie dostępna przez Twój zespół pomocy technicznej operacji IT.  Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie udostępnionego pulpitu nawigacyjnego w witrynie Azure Portal
> * Wizualizuj zapytanie dziennika wydajności 
> * Dodaj zapytanie dziennika do udostępnionego pulpitu nawigacyjnego 
> * Dostosowywanie kafelka na udostępnionym pulpicie nawigacyjnym

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna [połączona z obszarem roboczym usługi Log Analytics](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Tworzenie udostępnionego pulpitu nawigacyjnego
Wybierz **pulpit nawigacyjny** można otworzyć domyślnej [pulpit nawigacyjny](../../azure-portal/azure-portal-dashboards.md). Pulpit nawigacyjny wygląda inaczej niż w poniższym przykładzie.

![Pulpit nawigacyjny witryny Azure portal](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

W tym miejscu możesz zebrać najważniejsze dla działu IT dane operacyjne dotyczące wszystkich zasobów platformy Azure, w tym dane telemetryczne z usługi Azure Log Analytics.  Zanim przejdziemy do wizualizacji zapytań log, możemy najpierw utworzyć pulpit nawigacyjny i udostępnić go.  Firma Microsoft następnie skoncentrować się na naszym przykładzie wydajności dziennika zapytania, które będzie renderowane jako wykres liniowy i dodać go do pulpitu nawigacyjnego.  

Aby utworzyć pulpit nawigacyjny, wybierz przycisk **Nowy pulpit nawigacyjny** obok nazwy bieżącego pulpitu nawigacyjnego.

![Tworzenie nowego pulpitu nawigacyjnego w witrynie Azure portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Ta akcja tworzy nowy, pusty, prywatny pulpit nawigacyjny i przenosi użytkownika do trybu dostosowania, w którym można nazwać pulpit nawigacyjny oraz dodać kafelki i zmienić ich ułożenie. Edytuj nazwę pulpitu nawigacyjnego, a następnie określ *przykładowy pulpit nawigacyjny* dla tego samouczka, a następnie wybierz pozycję **Zakończono dostosowywanie**.<br><br> ![Zapisywanie dostosowanego pulpitu nawigacyjnego platformy Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Podczas tworzenia pulpitu nawigacyjnego jest on domyślnie prywatny, co oznacza, że użytkownik jest jedyną osobą, która może go zobaczyć. Aby uwidocznić go dla innych osób, użyj przycisku **Udostępnij**, który jest wyświetlany wraz z innymi poleceniami pulpitu nawigacyjnego.

![Udostępnianie nowego pulpitu nawigacyjnego w witrynie Azure portal](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

Zostanie wyświetlona prośba o wybór subskrypcji i grupy zasobów, dla których zostanie opublikowany pulpit nawigacyjny. Dla wygody środowisko publikowania w portalu skieruje Cię do wzorca, w którym umieszcza się pulpity nawigacyjne w grupie zasobów o nazwie **pulpity nawigacyjne**.  Sprawdź wybraną subskrypcję, a następnie kliknij przycisk **Publikuj**.  Dostęp do informacji wyświetlanych na pulpicie nawigacyjnym jest kontrolowany przy użyciu [Kontroli dostępu opartej na zasobach platformy Azure](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Wizualizuj zapytanie dziennika
[Log Analytics](../log-query/get-started-portal.md) jest specjalnym portalem używanej do pracy z dziennika zapytań i ich wyniki. Funkcje obejmują możliwość edytowania zapytania w wielu wierszach, selektywnego wykonywania kodu, kontekstowego używania funkcji IntelliSense oraz korzystania z funkcji analizy inteligentnej. W tym samouczku użyjesz usługi Log Analytics można utworzyć widok wydajności w formie graficznej, zapisz go w przyszłości zapytania i Przypnij go do wcześniej utworzonego udostępnionego pulpitu nawigacyjnego.

Otwórz program Log Analytics, wybierając **dzienniki** w menu usługi Azure Monitor. Rozpoczynają się one od nowego pustego zapytania.

![Strona główna](media/tutorial-logs-dashboards/homepage.png)

Wprowadź następujące zapytanie, aby zwrócić procesora rekordy użycia dla komputerów z systemami Windows i Linux, pogrupowane według atrybutów Computer i TimeGenerated oraz wyświetlone na wykresie. Kliknij przycisk **Uruchom** Aby uruchomić zapytanie i wyświetlić wynikowy wykres.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Zapisz zapytanie, wybierając **Zapisz** przycisku w górnej części strony.

![Zapisz zapytanie](media/tutorial-logs-dashboards/save-query.png)

W **Zapisz zapytanie** panel sterowania, takich jak Podaj nazwę *maszyn wirtualnych platformy Azure — użycie procesora* i kategorii, takich jak *pulpity nawigacyjne* a następnie kliknij przycisk **Zapisz** .  W ten sposób można utworzyć bibliotekę typowych zapytań możesz używać i modyfikować.  Na koniec Przypnij to udostępniony pulpit nawigacyjny utworzony wcześniej, wybierając **numeru Pin** przycisk w prawym górnym rogu strony, a następnie wybierając nazwę pulpitu nawigacyjnego.

Teraz, po przypięciu zapytania do pulpitu nawigacyjnego, zauważysz, że ma ogólny tytuł, a pod nim komentarz.

![Przykładowy pulpit nawigacyjny platformy Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Zmieńmy tytuł na coś znaczącego, co będzie łatwe do zrozumienia dla osób wyświetlających zapytanie.  Kliknij przycisk Edytuj, aby dostosować tytułu i podtytułu dla kafelka, a następnie kliknij przycisk **aktualizacji**.  Zostanie wyświetlony baner z prośbą o opublikowanie lub odrzucenie zmian.  Kliknij przycisk **Zapisz kopię**.  

![Ukończona konfiguracja przykładowego pulpitu nawigacyjnego](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób tworzenia pulpitu nawigacyjnego w witrynie Azure portal i dodać zapytanie dziennika do niego.  Przejdź do następnego samouczka, aby poznać różne odpowiedzi, które można zaimplementować na podstawie wyników zapytania dziennika.  

> [!div class="nextstepaction"]
> [Odpowiadanie na zdarzenia przy użyciu alertów usługi Log Analytics](tutorial-response.md)
