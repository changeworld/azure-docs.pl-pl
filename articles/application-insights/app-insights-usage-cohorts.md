---
title: Azure stado użycia usługi Application Insights | Dokumentacja firmy Microsoft
description: Analizowanie różnych zestawów lub użytkowników, sesji, zdarzenia lub operacje, które są wspólne dla elementu
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/10/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: f8d566f552c86f749b914ffed70512209ad76ab7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="application-insights-cohorts"></a>Application Insights stado

Kohorty to zbiór użytkowników, sesji, zdarzenia lub operacje, które są wspólne dla elementu. W usłudze Azure Application Insights stado są definiowane przez zapytanie analytics. W przypadkach, gdy mają do analizowania określony zbiór użytkowników lub zdarzeń, stado można zapewniają większą elastyczność i Express dokładnie interesujący Cię w zestawie.

![Okienko stado](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Stado i podstawowych filtrów

Stado są używane w sposób podobny do filtrów. Jednak definicje stado są tworzone na podstawie zapytania analityczne niestandardowych, dzięki czemu są bardziej dostosowywalne i złożone. W przeciwieństwie do filtrów możesz zapisać stado tak innych członków zespołu może korzystać z nich.

Można zdefiniować kohorty użytkowników, którzy mają nastąpiła nową funkcją w aplikacji. Ta kohorty można zapisywać w zasobu usługi Application Insights. Jest łatwy do analizowania tej zapisane grupy użytkowników określonych w przyszłości.

> [!NOTE]
> Po są tworzone, stado są dostępne narzędzia użytkowników, sesji zdarzeń i przepływu użytkownika.

## <a name="example-engaged-users"></a>Przykład: Zaangażowane użytkowników

Zespół definiuje zaangażowani użytkownika jako dowolny użytkownik korzysta z aplikacji pięć lub więcej razy w danym miesiącu. W tej sekcji można zdefiniować kohorty zaangażowani użytkownicy.

1. Otwórz narzędzie stado.

2. Wybierz **galerię szablonów** kartę. Zobaczysz stado różnych kolekcji szablonów.

3. Wybierz **zaangażowani użytkownicy — dni używane**.

    Istnieją trzy parametry dla tego kohorty:
    * **Działania**, którym można określić które zdarzeń i wyświetleń strony są liczone jak "użycia."
    * **Okres**, definicji miesiąca.
    * **UsedAtleastCustom**, liczba razy użytkownicy musieli używać coś w terminie do liczby jako biorący udział.

4. Zmień **UsedAtleastCustom** do **5 + dni**i pozostawić **okres** na wartość domyślna wynosi 28 dni.

    ![Zaangażowani użytkowników](.\media\app-insights-usage-cohorts\003.png)

    Teraz ten kohorty reprezentuje wszystkie identyfikatory użytkowników wysłane z dowolnego niestandardowy widok zdarzeń lub strony na 5 dni w przeszłości oddzielnych 28.

5. Wybierz pozycję **Zapisz**.

   > [!TIP]
   >  Nadaj nazwę, Twoje kohorty tak samo, jak "Użytkownicy Engaged (5 + dni)." Zapisz go na "Moje raporty" lub "Udostępnione raporty," w zależności od tego, czy mają inne osoby, które mają dostęp do tego zasobu usługi Application Insights, aby wyświetlić ten kohorty.

6. Wybierz **powrót do galerii**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Co można zrobić za pomocą tego kohorty

Otwórz narzędzie Użytkownicy. W **Pokaż** listy rozwijanej wybierz kohorty utworzono w obszarze **użytkowników, którzy należą do**.

Teraz narzędzie użytkowników została odfiltrowana do tego kohorty użytkowników:

![Okienko użytkowników filtrowane do konkretnego kohorty](.\media\app-insights-usage-cohorts\004.png)

Kilka ważnych rzeczy do Zwróć uwagę:
* Nie można utworzyć tego zestawu filtry normalnego. Logika Data jest bardziej zaawansowane.
* Ta kohorty można dodatkowo filtrować przy użyciu normalnego filtrów w narzędziu użytkownicy. Dlatego chociaż kohorty jest zdefiniowana w systemie windows 28 dni, nadal Dostosuj zakres czasu za pomocą narzędzia użytkowników do 30, 60 lub 90 dni.

Te filtry obsługuje dokładniejsze pytania, by nie można wyrazić za pomocą konstruktora zapytań. Na przykład _osoby, które są zaangażowane w ciągu ostatnich 28 dni. Zachowanie tych samych osób w ciągu ostatnich 60 dni?_

## <a name="example-events-cohort"></a>Przykład: Zdarzenia kohorty

Możesz również wprowadzić stado zdarzeń. W tej sekcji można zdefiniować kohorty zdarzeń i wyświetleń strony. Następnie użytkownik wiedział, jak z nich korzystać z innych narzędzi. Ta kohorty zdefiniować zestaw zdarzeń, które uzna zespołu _użycia active_ lub zestawu związane z niektórych nowych funkcji.

1. Otwórz narzędzie stado.

2. Wybierz **galerię szablonów** kartę. Zobaczysz zbiór szablonów dla różnych stado.

3. Wybierz **selektora zdarzenia**.

    ![Zrzut ekranu przedstawiający selektora zdarzenia](.\media\app-insights-usage-cohorts\006.png)

4. W **działania** listy rozwijanej wybierz zdarzenia mają być w kohorty.

5. Zapisz kohorty i nadaj mu nazwę.

## <a name="example-active-users-where-you-modify-a-query"></a>Przykład: Aktywni użytkownicy, którym Modyfikowanie zapytania

Poprzednich dwóch stado zostały zdefiniowane przy użyciu list rozwijanych. Ale można również definiować stado przy użyciu zapytania analityczne całkowita elastyczność. Aby zobaczyć, jak to zrobić, Utwórz kohorty użytkowników na podstawie Zjednoczone Królestwo.

![Animowany obraz przejście za pomocą narzędzia stado](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Otwórz narzędzie stado, wybierz **galerię szablonów** , a następnie wybierz **kohorty puste użytkowników**.

    ![Użytkownicy puste kohorty](.\media\app-insights-usage-cohorts\001.png)

    Istnieją trzy części:
    * Markdown sekcją tekstu, których opisano kohorty bardziej szczegółowo innym członkom zespołu.

    * Sekcji Parametry, których upewnij własne parametry, takie jak **działania** i inne pola listy rozwijanej w poprzednich przykładach dwa.

    * Sekcja zapytania, gdzie kohorty został zdefiniowany za pomocą kwerendy analytics.

    W sekcji zapytania można [zapisać kwerendę analytics](https://docs.loganalytics.io/index). Zapytanie wybiera określony zestaw wierszy, które opisują kohorty, które chcesz zdefiniować. Narzędzie stado niejawnie dodaje "| Klauzula podsumowania przez funkcja user_Id"w zapytaniu. Te dane jest przeglądany poniżej zapytania w tabeli, możesz mieć pewność, że kwerenda zwraca wyniki.

    > [!NOTE]
    > Jeśli nie widzisz zapytanie, należy spróbować zmienić rozmiar sekcji, aby była większa i ujawnić zapytania. Animowany GIF na początku tej sekcji przedstawiono zachowania zmiany rozmiaru.

2. Skopiuj i wklej następujący tekst w edytorze zapytań:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Wybierz **kwerenda**. Jeśli nie widzisz identyfikatory użytkowników są wyświetlane w tabeli, należy zmienić kraju, w którym aplikacja ma użytkowników.

4. Zapisz i nadaj nazwę kohorty.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

_Po zdefiniowaniu kohorty użytkowników od pewnego kraju. Gdy porównać tego kohorty w narzędziu użytkownicy można tylko ustawienie filtru na kraju, są widoczne różne wyniki. Dlaczego?_

Stado i filtry są różne. Załóżmy, że masz kohorty użytkowników z Polski (zdefiniowany tak jak w poprzednim przykładzie), a następnie porównaj wyniki z ustawieniem filtr "kraju lub regionu = na brytyjski."

* Wersja kohorty zawiera wszystkie zdarzenia z użytkowników, którzy wysyłane co najmniej jednego zdarzenia z brytyjski w bieżącym zakresie czasu. Jeśli zostanie podzielona według kraju lub regionu, prawdopodobnie zobaczysz wiele innych krajów i regionów.
* Wersja filtrów wyświetlane są tylko zdarzenia z Polski. Ale jeśli podziału według kraju lub regionu, zostanie wyświetlony Zjednoczone Królestwo.

## <a name="learn-more"></a>Dowiedz się więcej
- [Język zapytań analityka](https://go.microsoft.com/fwlink/?linkid=856587)
- [Użytkowników, sesji, zdarzenia](app-insights-usage-segmentation.md)
- [Przepływy użytkownika](app-insights-usage-flows.md)
- [Przegląd wykorzystania](app-insights-usage-overview.md)