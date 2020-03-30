---
title: Kohorty użycia usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Analizowanie różnych zestawów lub użytkowników, sesji, zdarzeń lub operacji, które mają coś wspólnego
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0c552e8f3e732c49da02b2f5704fb9cf312fb3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671090"
---
# <a name="application-insights-cohorts"></a>Kohorty usługi Application Insights

Kohorta to zestaw użytkowników, sesji, zdarzeń lub operacji, które mają coś wspólnego. W usłudze Azure Application Insights kohorty są definiowane przez kwerendę analityczną. W przypadkach, gdy trzeba wielokrotnie analizować określony zestaw użytkowników lub zdarzeń, kohorty mogą dać ci większą elastyczność, aby wyrazić dokładnie ten zestaw, który Cię interesuje.

![Okienko Kohorty](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorty a filtry podstawowe

Kohorty są stosowane w sposób podobny do filtrów. Ale definicje kohorty są zbudowane na podstawie niestandardowych zapytań analitycznych, więc są znacznie bardziej elastyczne i złożone. W przeciwieństwie do filtrów można zapisywać kohorty, aby inni członkowie zespołu mogli ich ponownie wykorzystać.

Można zdefiniować kohortę użytkowników, którzy wypróbowali nową funkcję w aplikacji. Tę kohortę można zapisać w zasobie usługi Application Insights. W przyszłości można łatwo analizować tę zapisaną grupę konkretnych użytkowników.

> [!NOTE]
> Po ich utworzeniu kohorty są dostępne w narzędziach Użytkownicy, Sesje, Zdarzenia i Przepływy użytkowników.

## <a name="example-engaged-users"></a>Przykład: Zaangażowani użytkownicy

Zespół definiuje zaangażowanego użytkownika jako każdego, kto korzysta z aplikacji pięć lub więcej razy w danym miesiącu. W tej sekcji można zdefiniować kohortę tych zaangażowanych użytkowników.

1. Otwórz narzędzie Kohorty.

2. Wybierz kartę **Galeria szablonów.** Zostanie wyświetlony zbiór szablonów dla różnych kohort.

3. Wybierz **zaangażowanych użytkowników - według używanych dni**.

    Istnieją trzy parametry dla tej kohorty:
    * **Działania**, gdzie można wybrać, które zdarzenia i widoki strony są liczone jako "użycie".
    * **Okres**, definicja miesiąca.
    * **UsedAtLeastCustom**, ile razy użytkownicy muszą użyć czegoś w okresie, aby liczyć jako zaangażowany.

4. Zmień **UsedAtLeastCustom** na **5+ dni**i pozostaw **okres** domyślnie 28 dni.

    ![Zaangażowani użytkownicy](./media/usage-cohorts/003.png)

    Teraz ta kohorta reprezentuje wszystkie identyfikatory użytkowników wysłane z dowolnym zdarzeniem niestandardowym lub widokiem strony w ciągu 5 oddzielnych dni w ciągu ostatnich 28.

5. Wybierz **pozycję Zapisz**.

   > [!TIP]
   > Nadaj swojej kohortie nazwę, na przykład "Zaangażowani użytkownicy (5+ dni)." Zapisz go w "Moje raporty" lub "Udostępnione raporty", w zależności od tego, czy chcesz, aby inne osoby, które mają dostęp do tego zasobu usługi Application Insights, aby zobaczyć tę kohortę.

6. Wybierz **pozycję Powrót do galerii**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Co można zrobić za pomocą tej kohorty?

Otwórz narzędzie Użytkownicy. Z listy rozwijanej **Pokaż** wybierz kohortę utworzoną w obszarze **Użytkownicy należący do**.

Teraz narzędzie Użytkownicy jest filtrowane do tej kohorty użytkowników:

![Okienko Użytkownicy filtrowane do określonej kohorty](./media/usage-cohorts/004.png)

Kilka ważnych rzeczy do zauważenia:

* Nie można utworzyć tego zestawu za pomocą zwykłych filtrów. Logika daty jest bardziej zaawansowana.
* Tę kohortę można dodatkowo filtrować, korzystając z zwykłych filtrów w narzędziu Użytkownicy. Tak więc chociaż kohorta jest zdefiniowana w oknach 28-dniowych, nadal można dostosować zakres czasu w narzędziu Użytkownicy do 30, 60 lub 90 dni.

Filtry te obsługują bardziej zaawansowane pytania, które są niemożliwe do wyrażenia za pośrednictwem konstruktora zapytań. Przykładem są _ludzie, którzy byli zaangażowani w ciągu ostatnich 28 dni. Jak zachowywali się ci sami ludzie w ciągu ostatnich 60 dni?_

## <a name="example-events-cohort"></a>Przykład: Kohorta zdarzeń

Można również tworzyć kohorty wydarzeń. W tej sekcji można zdefiniować kohortę zdarzeń i wyświetleń strony. Następnie widzisz, jak z nich korzystać z innych narzędzi. Ta kohorta może zdefiniować zestaw zdarzeń, które zespół uważa za _aktywne użycie_ lub zestaw związany z pewną nową funkcją.

1. Otwórz narzędzie Kohorty.

2. Wybierz kartę **Galeria szablonów.** Zobaczysz zbiór szablonów dla różnych kohort.

3. Wybierz **selektor zdarzeń**.

    ![Zrzut ekranu przedstawiający selektor zdarzeń](./media/usage-cohorts/006.png)

4. W polu rozwijany **Działania** wybierz zdarzenia, które mają znajdować się w kohorcie.

5. Zapisz kohortę i nadaj jej nazwę.

## <a name="example-active-users-where-you-modify-a-query"></a>Przykład: Aktywni użytkownicy, w których modyfikujesz kwerendę

Poprzednie dwie kohorty zostały zdefiniowane za pomocą pól rozwijanych. Ale można również zdefiniować kohorty za pomocą zapytań analitycznych dla całkowitej elastyczności. Aby zobaczyć, jak, utwórz kohortę użytkowników z Wielkiej Brytanii.

![Animowany obraz przechodzący przez narzędzie Kohorty](./media/usage-cohorts/cohorts0001.gif)

1. Otwórz narzędzie Kohorty, wybierz kartę **Galeria szablonów** i wybierz **pozycję Kohorta Użytkownicy puste**.

    ![Pusta kohorta użytkowników](./media/usage-cohorts/001.png)

    Istnieją trzy sekcje:
   * Sekcja tekstu Markdown, w której opisujesz kohortę bardziej szczegółowo dla innych osób w twoim zespole.

   * Sekcja parametry, w której można tworzyć własne parametry, takie jak **Działania** i inne pola rozwijane z poprzednich dwóch przykładów.

   * Sekcja kwerendy, w której kohorta jest definiowana przy użyciu kwerendy analitycznej.

     W sekcji kwerendy [piszesz kwerendę analityczną](/azure/kusto/query). Kwerenda wybiera określony zestaw wierszy, które opisują kohortę, którą chcesz zdefiniować. Następnie narzędzie Kohorty domyślnie dodaje "| podsumować przez user_Id" klauzuli do kwerendy. Te dane są wyświetlane pod zapytaniem poniżej kwerendy w tabeli, dzięki czemu można upewnić się, że kwerenda zwraca wyniki.

     > [!NOTE]
     > Jeśli kwerenda nie jest widoczna, spróbuj zwymiarować sekcję, aby była wyższa i wyświetlić kwerendę. Animowany plik gif na początku tej sekcji ilustruje zachowanie zmiany rozmiaru.

2. Skopiuj i wklej następujący tekst do edytora zapytań:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Wybierz pozycję **Uruchom zapytanie**. Jeśli nie widzisz identyfikatorów użytkowników wyświetlanych w tabeli, zmień ją na kraj/region, w którym aplikacja ma użytkowników.

4. Zapisz i nazwij kohortę.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

_Zdefiniowałem kohortę użytkowników z określonego kraju/regionu. Kiedy porównuję tę kohortę w narzędziu Użytkownicy, aby po prostu ustawiać filtr w tym kraju /regionie, widzę różne wyniki. Dlaczego?_

Kohorty i filtry są różne. Załóżmy, że masz kohortę użytkowników z Wielkiej Brytanii (zdefiniowaną jak w poprzednim przykładzie) i porównasz jej wyniki z ustawieniem filtru "Kraj lub region = Wielka Brytania".

* Wersja kohortowa zawiera wszystkie zdarzenia od użytkowników, którzy wysłali jedno lub więcej zdarzeń z Wielkiej Brytanii w bieżącym zakresie czasu. Jeśli dzielisz się według kraju lub regionu, prawdopodobnie zobaczysz wiele krajów i regionów.
* Wersja filtrów zawiera tylko zdarzenia z Wielkiej Brytanii. Ale jeśli podzielisz się według kraju lub regionu, zobaczysz tylko Wielką Brytanię.

## <a name="learn-more"></a>Dowiedz się więcej

* [Język zapytań usługi Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* [Użytkownicy, sesje, wydarzenia](usage-segmentation.md)
* [Przepływy użytkownika](usage-flows.md)
* [Omówienie użycia](usage-overview.md)
