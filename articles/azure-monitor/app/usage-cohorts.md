---
title: Kohorty użycia usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Analizować różne zestawy lub użytkownicy, sesje, zdarzenia lub operacji, które mają wspólne
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 263316028e7b35a1a515322dddc4ee867011dcac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65604107"
---
# <a name="application-insights-cohorts"></a>Application Insights kohorty

Kohorty to zbiór użytkowników, sesje, zdarzenia lub operacji, które są wspólne dla elementu. W usłudze Azure Application Insights kohorty są definiowane przez zapytania usługi analytics. W przypadkach, gdy istnieje przeanalizować określony zbiór użytkowników lub zdarzenia wielokrotnie, kohorty można zapewniają większą elastyczność i wyrażenia interesujących Cię w zestaw.

![W okienku kohorty](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorty i filtry podstawowe

Kohorty są używane w sposób podobny do filtrów. Ale definicji kohorty są tworzone na podstawie zapytań analitycznych niestandardowych, dzięki czemu są one znacznie bardziej adaptujące się i złożone. W przeciwieństwie do filtrów można zapisać kohorty, aby inni członkowie zespołu mogą użyć ich ponownie.

Można zdefiniować kohorty użytkowników, którzy mają próbowała nowa funkcja w aplikacji. Ta kohorta można zapisać w zasobie usługi Application Insights. To ułatwia analizowanie w przyszłości ten zapisana grupa określonych użytkowników.

> [!NOTE]
> Po ich utworzeniu kohorty są dostępne z poziomu narzędzi użytkownicy, sesje, zdarzenia i przepływy użytkownika.

## <a name="example-engaged-users"></a>Przykład: Oznacza zaangażowanie użytkowników

Zespół definiuje zaangażowania użytkowników jako dowolny użytkownik użyje aplikacji pięć lub więcej razy w danym miesiącu. W tej sekcji można zdefiniować kohorty te oznacza zaangażowanie użytkowników.

1. Otwórz w narzędziu kohorty.

2. Wybierz **galerii szablonów** kartę. Zostanie wyświetlona Kolekcja szablonów dla różnych kohorty.

3. Wybierz **oznacza zaangażowanie użytkowników — dni użył**.

    Istnieją trzy parametry, ta kohorta:
    * **Działania**, gdy wybierzesz, które zdarzenia i wyświetlenia stron liczone jako "użycie".
    * **Okres**, definicja miesiąca.
    * **UsedAtLeastCustom**, liczba razy użytkownicy musieli używać coś w przedziale czasu, zliczania jako biorący udział.

4. Zmiana **UsedAtLeastCustom** do **5 + dni**i pozostawić **okres** domyślny 28 dni.

    ![Oznacza zaangażowanie użytkowników](./media/usage-cohorts/003.png)

    Teraz ta kohorta reprezentuje wszystkie identyfikatory użytkowników wysyłane z dowolnego niestandardowe zdarzenie lub wyświetlenie strony na 5 oddzielnych dni w przeszłość 28.

5. Wybierz pozycję **Zapisz**.

   > [!TIP]
   > Nazwij Twoją kohortę, takie jak "Engaged użytkowników (w dniach 5 +)." Zapisz go na "Moje raporty" lub "Udostępnione raporty," w zależności od tego, czy chcesz, aby inne osoby, które mają dostęp do tego zasobu usługi Application Insights, aby zobaczyć ta kohorta.

6. Wybierz **powrót do galerii**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Co można zrobić za pomocą ta kohorta?

Otwórz narzędzie Użytkownicy. W **Pokaż** listy rozwijanej wybierz kohortę utworzone w ramach **użytkowników, którzy należą do**.

Narzędzie Użytkownicy jest teraz filtrowana w celu ta kohorta użytkowników:

![Okienko użytkowników filtrowana w celu określonego kohorty](./media/usage-cohorts/004.png)

Jest kilka ważnych rzeczy należy zwrócić uwagę:

* Ten zestaw nie można utworzyć za pomocą filtrów normalne. Logika Data jest bardziej zaawansowane.
* Ta kohorta można dalej filtrować przy użyciu normalnych filtrów w narzędziu użytkownicy. Więc mimo że kohorty jest zdefiniowana w systemie windows 28 dni, nadal można dostosować zakres czasu w narzędziu użytkownicy za 30, 60 lub 90 dni.

Te filtry obsługuje bardziej zaawansowane pytania, które są niemożliwe do express przy użyciu konstruktora zapytań. Na przykład _osób, które były zaangażowane w ciągu ostatnich 28 dni. Jak tymi samymi osobami działać w ciągu ostatnich 60 dni_

## <a name="example-events-cohort"></a>Przykład: Kohorta zdarzeń

Można również ustawić kohorty zdarzeń. W tej sekcji można zdefiniować kohorta zdarzeń i wyświetleń stron. Następnie zobaczysz, jak z nich korzystać z innych narzędzi. Ta kohorta może zdefiniować zestaw zdarzeń, które uwzględnia zespołowi _active użycia_ lub zestaw związane z niektórych nowych funkcji.

1. Otwórz w narzędziu kohorty.

2. Wybierz **galerii szablonów** kartę. Zostanie wyświetlona Kolekcja szablonów dla różnych kohorty.

3. Wybierz **selektora zdarzenia**.

    ![Zrzut ekranu przedstawiający wybór zdarzeń](./media/usage-cohorts/006.png)

4. W **działania** listy rozwijanej wybierz zdarzenia mają być w kohorcie.

5. Zapisz kohorty i nadaj mu nazwę.

## <a name="example-active-users-where-you-modify-a-query"></a>Przykład: Aktywni użytkownicy, którym można zmodyfikować zapytanie

Poprzednie dwa kohorty zostały zdefiniowane przy użyciu list rozwijanych. Ale kohorty można również definiować przy użyciu zapytań analitycznych dla całkowitej elastyczność. Aby zobaczyć, jak, utworzyć kohorta użytkowników ze Zjednoczonego Królestwa.

![Animowany obraz zalet stosowania narzędziu kohorty](./media/usage-cohorts/cohorts0001.gif)

1. Otwórz w narzędziu kohorty, wybierz opcję **galerii szablonów** , a następnie wybierz pozycję **kohorta użytkowników puste**.

    ![Kohorta użytkowników puste](./media/usage-cohorts/001.png)

    Istnieją trzy sekcje:
   * Markdown sekcją tekstu, gdzie opisują kohorty bardziej szczegółowo innym członkom swojego zespołu.

   * Sekcji parametrów, gdzie wprowadzisz własnymi parametrami, takich jak **działania** i pozostałe pola listy rozwijanej z poprzednich dwóch przykładach.

   * Sekcja zapytań, gdzie kohorty możesz definiować za pomocą zapytania usługi analytics.

     W sekcji zapytania możesz [napisać zapytanie analizy](/azure/kusto/query). Zapytanie wybiera niektórych zestawu wierszy, które opisują kohorty, które chcesz zdefiniować. W narzędziu kohorty niejawnie dodaje "| Podsumowanie, definiując pole user_Id"klauzula zapytania. Tych danych jest przeglądany poniżej zapytania w tabeli, dzięki czemu możesz upewnić się, że zapytanie zwraca wyniki.

     > [!NOTE]
     > Jeśli nie widzisz zapytania, spróbuj zmienić rozmiar sekcji, aby wprowadzić na wyższy i ujawniają ukryte fakty zapytania. Animowany obraz GIF na początku tej sekcji przedstawiono zmiany rozmiaru zachowanie.

2. Skopiuj i wklej następujący tekst w edytorze zapytań:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Wybierz **uruchom zapytanie**. Jeśli nie widać nazwy użytkowników są wyświetlane w tabeli, Zmień kraj/region, gdzie aplikacja ma użytkowników.

4. Zapisz i nadaj kohorty.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

_Czy mogę zdefiniowane kohorta użytkowników z określonych kraj/region. Gdy porównać ta kohorta w narzędziu użytkownicy, można po prostu ustawienie filtru na tego kraju/regionu, czy mogę zobaczyć różne wyniki. Dlaczego?_

Kohorty i filtry są różne. Załóżmy, że masz kohorta użytkowników z Wielka Brytania (zdefiniowane tak jak w poprzednim przykładzie) i porównaj wyniki z ustawieniem dla filtru "kraj lub region = Wielkiej Brytanii."

* Wersja kohorty pokazuje wszystkie zdarzenia od użytkowników, którzy wysłali jednego lub wielu zdarzeń z Wielkiej Brytanii w bieżącym zakresie czasu. Jeśli podzieli według kraju lub regionu, zostanie wyświetlony ekran podobny do wielu innych krajów i regionów.
* Wersja filtrów wyświetlane są tylko zdarzenia z Zjednoczonego Królestwa. Ale jeśli podzieli według kraju lub regionu, zostanie wyświetlony Zjednoczonego Królestwa.

## <a name="learn-more"></a>Dowiedz się więcej

* [Język zapytań usługi Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* [Użytkownicy, sesje, zdarzenia](usage-segmentation.md)
* [Przepływy użytkownika](usage-flows.md)
* [Przegląd wykorzystania](usage-overview.md)