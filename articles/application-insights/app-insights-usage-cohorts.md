---
title: Azure Application Insights użycia stado | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 68453499cc6477cc079a342906614e6873938fc8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="application-insights-cohorts"></a>Application Insights stado

Kohorty to zbiór użytkowników, sesji, zdarzenia lub operacje, które są wspólne dla elementu. W usłudze Azure Application Insights stado są definiowane przez zapytanie Analytics. Jeśli okaże się, że analizowania określonych użytkowników lub zdarzenia będzie się powtarzać, stado można zapewniają większą elastyczność i możliwość express dokładnie zestaw Cię interesują.

![Okienko stado](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Stado i podstawowych filtrów

Stado są używane w podobny sposób jak filtry, fakt, że definicja kohorty składa się z niestandardowych kwerend Analytics umożliwia im to znacznie bardziej dostosowywalne i złożone. W przeciwieństwie do filtrów możesz zapisać stado tak innych członków zespołu może korzystać z nich.

Można zdefiniować kohorty użytkowników, którzy mają nastąpiła nową funkcją w aplikacji. Z tym kohorty zapisane w zasobu usługi Application Insights powoduje analizowanie grupę użytkowników w przyszłości jednego kliknięcia.

> [!NOTE]
> Po utworzeniu stado są dostępne narzędzia użytkowników, sesji zdarzeń i przepływu użytkownika.

## <a name="example-engaged-users"></a>Przykład: zaangażowane użytkowników

Zespół definiuje zaangażowani użytkownika jako dowolny użytkownik korzysta z aplikacji pięć lub więcej razy w danym miesiącu. Umożliwia definiowanie cech wspólnych zaangażowani użytkownicy.

1. Otwórz **stado** narzędzia.

2. Kliknij przycisk **galerię szablonów** kartę. Tutaj znajdziesz zbiór szablonów dla różnych stado.

3. Wybierz **zaangażowane użytkowników** — dni używane ".

    Istnieją trzy parametry dla tego kohorty:
      * **Działania** pozwalające wybrać, które zdarzenia i wyświetleń strony powinny liczone jako "użycia".
      * **Okres** definicji miesiąca.
      * **UsedAtleastCustom** liczba muszą umożliwia coś w przedziale czasu liczba jako użytkownik zaangażowani.

4. Zmień **UsedAtleastCustom** "5 + dni" i pozostaw **okres** domyślne 28 dni.

    ![Image (Obraz)](.\media\app-insights-usage-cohorts\003.png)

    Teraz ta kohorty reprezentuje wszystkie identyfikatory użytkowników, które zostały wysłane z dowolnego niestandardowy widok zdarzeń lub strony na pięć dni oddzielne w ciągu ostatnich 28 dni.

5. Kliknij pozycję **Zapisz**.

   > [!TIP]
   >  Nadaj nazwę, Twoje kohorty tak samo, jak "Engaged użytkownicy (w dniach 5 +)" i zapisz go do "Moje raporty" lub "Udostępniony raportów" zależnie od tego, jeśli będą mogli wyświetlić ten kohorty dostęp do tego zasobu wgląd w aplikację.

6. Kliknij przycisk **powrót do galerii**.

### <a name="what-can-you-do-with-this-cohort"></a>Co należy zrobić z tym kohorty

Otwórz **użytkowników** Narzędzia > w **Pokaż** listy rozwijanej > Wybierz kohorty utworzono w obszarze **użytkowników, którzy należą do...**

Teraz narzędzie użytkowników została odfiltrowana do tego kohorty użytkowników:

![Okienko użytkowników filtrowane do konkretnego kohorty](.\media\app-insights-usage-cohorts\004.png)

Kilka ważnych rzeczy do Zwróć uwagę:
   * Jest to zestaw, który nie został utworzony przez filtry normalnego. Logika Data jest bardziej zaawansowane.
   * Dodatkowo można filtrować tego kohorty przy użyciu normalnego filtrów w narzędziu użytkownicy. Dlatego podczas kohorty jest zdefiniowana w systemie windows 28 dni, nadal można dostosować zakres czasu za pomocą narzędzia użytkowników do 30, 60 lub 90 dni. 

Dzięki temu można zadawać pytania bardziej zaawansowanych, takich jak: _dla osób, które są zaangażowane w ciągu ostatnich 28 dni, tych samych osób zachowanie w ciągu ostatnich 60 dni?_ który byłoby niemożliwe express za pomocą konstruktora zapytań.

## <a name="example-events-cohort"></a>Przykład: zdarzenia kohorty

Możesz również wprowadzić stado zdarzeń. Umożliwia definiowanie cech wspólnych zdarzeń i wyświetleń strony, a następnie zapoznaj się z nich korzystać z innych narzędzi. Może to być przydatne do zdefiniowania zestawu zdarzeń, które uzna zespołu _użycia active_, lub do zdefiniowania zestawu zdarzeń związanych z niektórych nowych funkcji.

1. Otwórz **stado** narzędzia.

2. Kliknij przycisk **galerię szablonów** kartę. Tutaj znajdziesz zbiór szablonów dla różnych stado.

3. Wybierz **selektora zdarzenia**.

    ![Zrzut ekranu zdarzenia selektora](.\media\app-insights-usage-cohorts\006.png)

4. W **działania** listy rozwijanej wybierz zdarzenia chcesz mieć kohorty

5. Zapisz kohorty i nadaj mu nazwę.

## <a name="example-active-users-where-you-modify-query"></a>Przykład: aktywni użytkownicy, którym można zmodyfikować zapytanie

Poprzednich dwóch stado zostały zdefiniowane przy użyciu list rozwijanych. Ale stado z zapytaniami Analytics całkowita elastyczność można również zdefiniować. Zobaczmy, jak przez utworzenie kohorty użytkowników z Polski.

![Animowany obraz przejście za pomocą narzędzia stado](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Otwórz **stado** Narzędzia > kliknij **galerię szablonów** kartę > Wybierz **kohorty puste użytkowników**.

    ![Użytkownicy puste kohorty](.\media\app-insights-usage-cohorts\001.png)

    Istnieją trzy części:
       * Sekcja text Markdown gdzie opisano kohorty szczegółowo w innym członkom zespołu.

       * Sekcja Parametry, można użyć, aby własne parametry, takie jak **działania** i innych list rozwijanych w poprzednich przykładach dwa.

       * Sekcję zapytania, który służy do definiowania kohorty przy użyciu kwerendy Analytics.

    W sekcji zapytania można [zapisać kwerendę Analytics](https://docs.loganalytics.io/index) który wybiera określony zestaw wierszy, które opisują kohorty chcesz zdefiniować. Narzędzie stado niejawnie dodaje "| Klauzula podsumowania przez funkcja user_Id"w zapytaniu. To jest przeglądany poniżej zapytania w tabeli, możesz mieć pewność, że kwerenda zwraca wyniki.

    > [!NOTE]
    > Jeśli nie widzisz zapytanie, należy spróbować zmienić rozmiar sekcji, aby była większa i ujawnić zapytania. Animowany GIF na początku tej sekcji przedstawiono zachowania zmiany rozmiaru.

2. Kopiowania i wklejania w edytorze zapytań następujące:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Kliknij przycisk **kwerenda**. Powinny pojawić się identyfikatory użytkowników są wyświetlane w tabeli. Jeśli nie, zmień kraju, w którym aplikacja ma użytkowników.

4. Zapisz i nadaj nazwę kohorty.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

_Po zdefiniowaniu kohorty użytkowników od pewnego kraju. Gdy porównać tego kohorty w narzędziu użytkownicy można tylko ustawienie filtru na kraju w narzędziu użytkownicy są widoczne różne wyniki. Dlaczego?_

Stado i filtry są różne. Załóżmy, że masz kohorty użytkowników z Polski (zdefiniowany tak jak w powyższym przykładzie), a następnie porównaj wyniki z ustawieniem filtr "kraju lub regionu = na brytyjski."

* Wersja kohorty zostaną wyświetlone wszystkie zdarzenia od użytkowników, którzy wysłali co najmniej jedno zdarzenie z brytyjski w bieżącym zakresie czasu. Jeśli zostanie podzielona według kraju lub regionu, prawdopodobnie pojawi się wiele innych krajów i regionów.
* Wersja filtry będzie wyświetlana tylko zdarzenia z Polski. Natomiast jeśli podziału według kraju lub regionu, zostanie wyświetlone tylko Zjednoczone Królestwo.

## <a name="learn-more"></a>Dowiedz się więcej
- [Język zapytań analityka](https://go.microsoft.com/fwlink/?linkid=856587)
- [Użytkownicy, sesje, zdarzenia](app-insights-usage-segmentation.md)
- [User Flows (Przepływy użytkowników)](app-insights-usage-flows.md)
- [Przegląd wykorzystania](app-insights-usage-overview.md)