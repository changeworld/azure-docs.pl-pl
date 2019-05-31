---
title: Eksplorowanie danych za pomocą Eksploratora usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można szybko wyświetlić globalny widok danych dotyczących danych big data, a następnie sprawdź środowisko IoT za pomocą Eksploratora usługi Azure Time Series Insights w przeglądarce sieci web.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: cbb6d75b2ed6b73ce7e3421596520f6a3f1bf3b1
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399815"
---
# <a name="azure-time-series-insights-explorer"></a>Eksplorator usługi Azure Time Series Insights

W tym artykule opisano funkcje i ogólnie rzecz biorąc opcji dostępności dla usługi Azure Time Series Insights [aplikacji sieci web w Eksploratorze](https://insights.timeseries.azure.com/). Eksplorator usługi Time Series Insights pokazuje możliwości wizualizacji zaawansowanych danych, które są udostępniane przez usługę i są dostępne w ramach własnego środowiska.

Azure Time Series Insights to w pełni zarządzana usługa związana z analizą, przechowywaniem i wizualizacją, dzięki której z łatwością można badać i analizować miliardy zdarzeń IoT jednocześnie. Daje ona globalny widok danych, które umożliwia szybkie weryfikowanie rozwiązań IoT i uniknięcie kosztownych przestojów krytycznych urządzeń. Można wykryć ukryte trendy, zauważyć anomalie i przeprowadzanie analiz głównych przyczyn niemal w czasie rzeczywistym. Eksplorator usługi Time Series Insights jest obecnie w publicznej wersji zapoznawczej.

> [!TIP]
> Aby uzyskać samouczek za pośrednictwem środowiska pokazu, przeczytaj [szybkiego startu usługi Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Połączenia wideo

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Dowiedz się więcej o odpytywaniu danych za pomocą Eksploratora usługi Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Zobacz poprzedni wideo <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Wprowadzenie do usługi Time Series Insights za pomocą akceleratora rozwiązań IoT platformy Azure."</a>

## <a name="prerequisites"></a>Wymagania wstępne

Korzystanie z Eksploratora usługi Time Series Insights, musisz mieć:

- Tworzenie środowiska usługi Time Series Insights. Aby uzyskać więcej informacji, zobacz [jak rozpocząć pracę przy użyciu usługi Time Series Insights](./time-series-insights-get-started.md).
- [Zapewniają dostęp](time-series-insights-data-access.md) do swojego konta w środowisku.
- Dodaj [usługi IoT hub](time-series-insights-how-to-add-an-event-source-iothub.md) lub [Centrum zdarzeń](time-series-insights-how-to-add-an-event-source-eventhub.md) źródła zdarzeń do niego.

## <a name="explore-and-query-data"></a>Eksploruj i wykonywanie zapytań dotyczących danych

W ciągu kilku minut nawiązywania połączenia z źródła zdarzeń do środowiska usługi Time Series Insights można eksplorować i zapytanie danych szeregów czasowych.

1. Aby rozpocząć, otwórz [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com/) w przeglądarce sieci web. W lewej części okna Wybierz środowisko. Wszystkie środowiska, które mają dostęp do są wymienione w kolejności alfabetycznej.

1. Po wybraniu środowiska, albo użyć **z** i **do** konfiguracje u góry strony, lub kliknij i przeciągnij za pośrednictwem timespan ma. Wybierz ikonę lupy w prawym górnym rogu, lub kliknij prawym przyciskiem myszy na wybrany przedział czasu i wybierz pozycję **wyszukiwania**.

1. Możesz również odświeżyć dostępności automatycznie co minutę, wybierając **automatycznie na** przycisku. **Automatycznie na** przycisk ma zastosowanie tylko do wykresu dostępności nie zawartości głównego wizualizacji.

1. Ikona chmury platformy Azure umożliwia przejście do środowiska w witrynie Azure portal.

   [![Środowisko usługi Time Series Insights](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Następnie zostanie wyświetlony wykres, który pokazuje liczbę wszystkich zdarzeń w czasie wybranego przedziału czasu. W tym miejscu masz wiele formantów:

    - **Panel Edytor warunków**: Miejsce termin jest, gdzie zapytanie środowiska. Znajduje się w lewej części ekranu:
      - **Miara**: Na tej liście rozwijanej wyświetlane wszystkie kolumny liczbowe (**wartości podwójnej precyzji**).
      - **Podziel według**: Na tej liście rozwijanej wyświetlane podzielone na kategorie kolumny (**ciągi**).
      - Można włączyć interpolacji stopniowej, Pokaż minimalne i maksymalne i dostosować obok osi y, z poziomu Panelu sterowania **miary**. Można również dostosować czy dane wyświetlane jest liczba, średnia lub suma danych.
      - Możesz dodać maksymalnie pięć warunki, aby wyświetlić na tej samej osi x. Użyj **kopiowania rozwijanej** przycisk, aby dodać dodatkowy termin lub wybierz **Dodaj** można dodać nowej termin.

        [![Panel edytora terminów](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predykatu**: Orzeczenie szybkie filtrowanie zdarzeń przy użyciu zestawu argumentów operacji wymienionych w poniższej tabeli. Jeśli wyszukiwania przez zaznaczenie lub klikając, predykat automatycznie aktualizacji na podstawie tego wyszukiwania. Operand obsługiwane typy:

         |Operacja  |Obsługiwane typy  |Uwagi  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | String, Bool, Double, DateTime, przedział czasu, wartość NULL        |         |
         |IN     | String, Bool, Double, DateTime, przedział czasu, wartość NULL        |  Wszystkie argumenty powinny być tego samego typu lub być stała NULL.        |
         |MA     | String        |  Po prawej stronie dozwolone są tylko stałe literałów. Pusty ciąg i wartości NULL nie są dozwolone.       |

      - **Przykłady zapytań**

         [![Przykładowe zapytania](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Możesz użyć **rozmiar interwału** narzędzia suwaka na powiększanie i pomniejszanie odstępach czasu za pośrednictwem tego samego przedziału czasu. Suwak zapewnia dokładniejszą kontrolę ruchu między dużych okresy przedstawiających smooth trendy w dół, aby wycinki tak małej, jak milisekund, które umożliwiają przeglądanie szczegółowe, wysokiej rozdzielczości odpowiednie dane. Domyślne suwaka punkt początkowy jest ustawiony jako najbardziej optymalną wgląd w dane z zaznaczenia równoważyć rozdzielczość, prędkość wysyłania zapytań i stopień szczegółowości.

1. **Czasu pędzla** narzędzie ułatwia przechodzenie z jednego przedział czasu do innego.

1. Użyj **Zapisz** polecenie, aby zapisać bieżącego zapytania i udostępnić go innym użytkownikom środowiska. Kiedy używasz **Otwórz**, możesz zobaczyć wszystkich zapisanych zapytań i wszelkie udostępnione zapytania innych użytkowników w środowiskach, masz dostęp do.

   [![Zapytania](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Wizualizowanie danych

1. Użyj **widoku perspektywy** narzędzie jednoczesnych widoku maksymalnie cztery kwerend unikatowy. **Widoku perspektywy** przycisk znajduje się w prawym górnym rogu wykresu.

   [![Widok perspektywy](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Wyświetl wykres wizualnie eksplorować dane i używać **wykresu** narzędzi:

    - **Wybierz** lub **kliknij** określonego przedziału czasu lub jedną serię danych.
    - W przedziale czasu można powiększać lub Eksploruj zdarzenia.
    - W serii danych można podzielić serii według innej kolumny, dodać serii jako nowy termin, Pokaż tylko wybrane serię, Wyklucz wybrane serii, ping tej serii; Eksploruj zdarzenia z wybranej serii
    - W obszarze filtru po lewej stronie wykresu można zobaczyć wszystkie szeregi wyświetlanych danych i zmiana kolejności według wartości lub nazwę. Również można wyświetlić wszystkich serii danych lub serii przypiętych lub odpięte. Można wybierz jedną serię danych i podzielić serii według innej kolumny, dodać serii jako nowy termin, Pokaż tylko wybrane serię, Wyklucz wybrane serii, przypiąć tej serii; Eksploruj zdarzenia z wybranej serii
    - Po wyświetleniu jednocześnie wiele wersji warunków, można stosu, anulowania, zobacz dodatkowe dane dotyczące serii danych i używać tej samej osi y we wszystkich warunków umowy. Przyciski w prawym górnym rogu wykresu.

    [![Narzędzia wykresów](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Użyj **mapy cieplnej** można szybko wychwycić serii danych unikatowego lub nietypowych w podanej kwerendy. Termin wyszukiwania tylko jeden może ich bezpośrednią wizualizację mapy cieplnej.

    [![Heatmap](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. Gdy Eksploruj zdarzenia przez zaznaczenie lub kliknij prawym przyciskiem myszy, **zdarzenia** panelu ma zostać udostępnione. Tutaj widać wszystkie nieprzetworzone zdarzenia i eksportowanie zdarzeń jako pliki JSON lub CSV. Usługa Time Series Insights są przechowywane wszystkie nieprzetworzone dane.

    [![Zdarzenia](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Wybierz **statystyki** karcie po Eksploruj zdarzenia do udostępnienia, wzorce i statystyki kolumn.

    - **Wzorce**: Ta funkcja i odpowiedzi proaktywnie udostępnia najbardziej są statystycznie istotne wzorców w regionie wybranych danych. Nie masz przyjrzeć się tysięcy zdarzeń, aby zrozumieć, jakie wzorców wymaga najwięcej czasu i energii. Za pomocą usługi Time Series Insights możesz przejść bezpośrednio do tych wzorców są statystycznie istotne, aby kontynuować, przeprowadzanie analizy. Ta funkcja jest również przydatne w przypadku późniejszej śledztwa w danych historycznych.
    - **Statystyki kolumn**: Statystyki kolumn zapewniają wykresów i tabel, które podzielenie danych z każdej kolumny wybrane serie danych za pośrednictwem wybranego przedziału czasu.

      [![STATYSTYKI](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Teraz wiesz, różnych funkcji i opcji, które są dostępne w ramach aplikacji internetowej w Eksploratorze usługi Time Series Insights.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [diagnozowanie i rozwiązywanie problemów](time-series-insights-diagnose-and-solve-problems.md) w danym środowisku usługi Time Series Insights.
- Wykonać krok po kroku [szybkiego startu usługi Azure Time Series Insights](time-series-quickstart.md) samouczka.
