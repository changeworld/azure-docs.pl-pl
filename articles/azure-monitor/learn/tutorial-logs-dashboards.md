---
title: Tworzenie i udostępnianie pulpitów nawigacyjnych z danymi usługi Azure Log Analytics | Microsoft Docs
description: Ten samouczek ułatwia zrozumienie sposobu wizualizacji wszystkich zapisanych przeszukiwań dzienników w pulpitach nawigacyjnych usługi Log Analytics, co zapewnia jedno miejsce do wglądu w środowisko.
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
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 5ed0cfba9abaed1f1fdbacc8fcf28918403b82f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589988"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Tworzenie i udostępnianie pulpitów nawigacyjnych z danymi usługi Log Analytics

Pulpity nawigacyjne usługi Log Analytics mogą wizualizować wszystkie zapisane przeszukiwania dzienników, co umożliwia znajdowanie, korelowanie i udostępnianie danych operacyjnych IT w organizacji.  Ten samouczek obejmuje tworzenie przeszukiwania dzienników, które będzie również wspierać udostępniony pulpit nawigacyjny dostępny dla zespołu pomocy technicznej ds. operacji IT.  Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie udostępnionego pulpitu nawigacyjnego w witrynie Azure Portal
> * Wizualizacja przeszukiwania dzienników wydajności 
> * Dodawanie przeszukiwania dzienników do udostępnionego pulpitu nawigacyjnego 
> * Dostosowywanie kafelka na udostępnionym pulpicie nawigacyjnym

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna [połączona z obszarem roboczym usługi Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md).  
 
## <a name="log-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Tworzenie udostępnionego pulpitu nawigacyjnego

Pierwszą rzeczą, jaką zobaczysz po zalogowaniu się do witryny Microsoft Azure Portal, będzie [pulpit nawigacyjny](../../azure-portal/azure-portal-dashboards.md).<br> ![Pulpit nawigacyjny witryny Azure Portal](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

W tym miejscu możesz zebrać najważniejsze dla działu IT dane operacyjne dotyczące wszystkich zasobów platformy Azure, w tym dane telemetryczne z usługi Azure Log Analytics.  Zanim przejdziemy do wizualizacji przeszukiwania dzienników, utwórzmy pulpit nawigacyjny i udostępnijmy go.  Dzięki temu będziemy mieli to z głowy przed przejściem do przykładowego przeszukiwania dzienników wydajności, które będzie renderowane jako wykres liniowy, oraz dodaniem go do pulpitu nawigacyjnego.  

Aby utworzyć pulpit nawigacyjny, wybierz przycisk **Nowy pulpit nawigacyjny** obok nazwy bieżącego pulpitu nawigacyjnego.<br> ![Tworzenie nowego pulpitu nawigacyjnego w witrynie Azure Portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Ta akcja tworzy nowy, pusty, prywatny pulpit nawigacyjny i przenosi użytkownika do trybu dostosowania, w którym można nazwać pulpit nawigacyjny oraz dodać kafelki i zmienić ich ułożenie. Edytuj nazwę pulpitu nawigacyjnego i podaj *Przykładowy pulpit nawigacyjny* na potrzeby tego samouczka, a następnie wybierz opcję **Zakończono dostosowywanie**.<br><br> ![Zapisywanie dostosowanego pulpitu nawigacyjnego platformy Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Podczas tworzenia pulpitu nawigacyjnego jest on domyślnie prywatny, co oznacza, że użytkownik jest jedyną osobą, która może go zobaczyć. Aby uwidocznić go dla innych osób, użyj przycisku **Udostępnij**, który jest wyświetlany wraz z innymi poleceniami pulpitu nawigacyjnego.<br> ![Udostępnianie nowego pulpitu nawigacyjnego w witrynie Azure Portal](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

Zostanie wyświetlona prośba o wybór subskrypcji i grupy zasobów, dla których zostanie opublikowany pulpit nawigacyjny. Dla wygody środowisko publikowania w portalu skieruje Cię do wzorca, w którym umieszcza się pulpity nawigacyjne w grupie zasobów o nazwie **pulpity nawigacyjne**.  Sprawdź wybraną subskrypcję, a następnie kliknij przycisk **Publikuj**.  Dostęp do informacji wyświetlanych na pulpicie nawigacyjnym jest kontrolowany przy użyciu [Kontroli dostępu opartej na zasobach platformy Azure](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-search"></a>Wizualizacja przeszukiwania dzienników

Możesz utworzyć podstawowe zapytania w jednym wierszu z portalu przeszukiwania dzienników w witrynie Azure Portal. Portalu przeszukiwania dzienników można używać bez uruchamiania portalu zewnętrznego. Można też użyć go do wykonywania różnych funkcji związanych z przeszukiwaniem dzienników, w tym do tworzenia reguł alertów, tworzenia grup komputerów oraz eksportowania wyników zapytania. 

[Portal usługi Log Analytics](../../azure-monitor/log-query/get-started-portal.md) jest specjalnym portalem zapewniającym zaawansowane funkcje, które nie są dostępne w portalu przeszukiwania dzienników. Funkcje obejmują możliwość edytowania zapytania w wielu wierszach, selektywnego wykonywania kodu, kontekstowego używania funkcji IntelliSense oraz korzystania z funkcji analizy inteligentnej. W portalu analizy zaawansowanej utworzysz widok wydajności w formie graficznej, zapiszesz go na potrzeby przyszłego wyszukiwania oraz przypniesz go do wcześniej utworzonego, udostępnionego pulpitu nawigacyjnego.   

Portal analizy zaawansowanej można uruchomić prz użyciu linku w portalu przeszukiwania dzienników.<br> ![Uruchamianie portalu analizy zaawansowanej](media/tutorial-logs-dashboards/log-analytics-advancedportal-01.png)

W portalu analizy wprowadź następujące zapytanie, aby zwrócić tylko rekordy związane z wykorzystaniem procesora dla komputerów z systemem Windows i Linux, pogrupowane według atrybutów Computer i TimeGenerated oraz wyświetlone na wykresie wizualnym:

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

Zapisz zapytanie, wybierając przycisk **Zapisz zapytanie** w prawym górnym rogu.<br> ![Zapisywanie zapytania z portalu analizy zaawansowanej](media/tutorial-logs-dashboards/log-analytics-advancedportal-02.png)<br><br> W panelu sterowania **Zapisz zapytanie** podaj nazwę, np. *Maszyny wirtualne Azure — użycie procesora*, i kliknij przycisk **Zapisz**.  W ten sposób możesz utworzyć bibliotekę typowych zapytań na potrzeby wyszukiwania lub ich modyfikowania bez konieczności ponownego pisania zapytań od początku.  Na koniec przypnij wykres do wcześniej utworzonego udostępnionego pulpitu nawigacyjnego, wybierając przycisk **Przypnij wykres do pulpitu nawigacyjnego platformy Azure** w środkowej części strony po prawej.  

Teraz, po przypięciu zapytania do pulpitu nawigacyjnego, zauważysz, że ma ogólny tytuł, a pod nim komentarz.<br> ![Przykładowy pulpit nawigacyjny platformy Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  Zmieńmy tytuł na coś znaczącego, co będzie łatwe do zrozumienia dla osób wyświetlających zapytanie.  Kliknij prawym przyciskiem myszy kafelek i wybierz polecenie **Edytuj tytuł**.  Po zakończeniu dostosowywania tytułu i podtytułu dla kafelka kliknij opcję **Zaktualizuj**.  Zostanie wyświetlony baner z prośbą o opublikowanie lub odrzucenie zmian.  Kliknij przycisk **Opublikuj zmiany**, a następnie zamknij okienko sterowania **Edytuj kafelek**.  

![Ukończona konfiguracja przykładowego pulpitu nawigacyjnego](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku omówiliśmy sposób tworzenia pulpitu nawigacyjnego w witrynie Azure Portal oraz dodawania do niego przeszukiwania dzienników.  Przejdź do kolejnego samouczka, aby poznać różne odpowiedzi, które możesz wdrożyć w oparciu o wyniki przeszukiwania dzienników.  

> [!div class="nextstepaction"]
> [Odpowiadanie na zdarzenia przy użyciu alertów usługi Log Analytics](tutorial-response.md)
