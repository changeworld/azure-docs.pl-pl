---
title: Eksplorowanie danych za pomocą Eksploratora usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można szybko wyświetlić globalny widok danych dotyczących danych big data, a następnie sprawdź środowisko IoT za pomocą Eksploratora usługi Azure Time Series Insights w przeglądarce sieci web.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 0f22a0245d002b94d9fc0004214c37944350e262
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412926"
---
# <a name="azure-time-series-insights-explorer"></a>Eksplorator usługi Azure Time Series Insights

W tym artykule opisano funkcje i ogólnie rzecz biorąc opcji dostępności dla usługi Azure Time Series Insights [aplikacji sieci web w Eksploratorze](https://insights.timeseries.azure.com/). Eksplorator usługi Time Series Insights pokazuje możliwości wizualizacji zaawansowanych danych, które są udostępniane przez usługę i są dostępne w ramach własnego środowiska.

Azure Time Series Insights to w pełni zarządzana usługa związana z analizą, przechowywaniem i wizualizacją, dzięki której z łatwością można badać i analizować miliardy zdarzeń IoT jednocześnie. Daje ona globalny widok danych, które umożliwia szybkie weryfikowanie rozwiązań IoT i uniknięcie kosztownych przestojów krytycznych urządzeń. Można wykryć ukryte trendy, zauważyć anomalie i przeprowadzanie analiz głównych przyczyn niemal w czasie rzeczywistym. Eksplorator usługi Time Series Insights jest obecnie w publicznej wersji zapoznawczej.

> [!TIP]
> Aby uzyskać samouczek za pośrednictwem środowiska pokazu, przeczytaj [Insights serii czasu pakietu Azure Quickstart](time-series-quickstart.md).

## <a name="video"></a>Wideo

### <a name="learn-about-querying-data-using-the-time-series-insights-explorer-br"></a>Więcej informacji na temat wykonywanie zapytań o dane za pomocą Eksploratora usługi Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Zobacz poprzedni wideo <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Wprowadzenie do usługi TSI za pomocą akceleratora rozwiązań IoT platformy Azure"</a>.

## <a name="prerequisites"></a>Wymagania wstępne

Korzystanie z Eksploratora usługi Time Series Insights, musisz mieć:

- Tworzenie środowiska usługi Time Series Insights. Aby uzyskać więcej informacji, zobacz [jak rozpocząć pracę przy użyciu usługi Time Series Insights](./time-series-insights-get-started.md).
- [Zapewniają dostęp](time-series-insights-data-access.md) do swojego konta w środowisku.
- Dodaj [usługi IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md) lub [Centrum zdarzeń](time-series-insights-how-to-add-an-event-source-eventhub.md) źródła zdarzeń do niego.

## <a name="explore-and-query-data"></a>Eksploruj i wykonywanie zapytań dotyczących danych

W ciągu kilku minut nawiązywania połączenia z źródła zdarzeń do środowiska usługi Time Series Insights można eksplorować i zapytanie danych szeregów czasowych.

1. Aby rozpocząć, otwórz [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com/) w przeglądarce sieci web i wybierz środowisko po lewej stronie okna. Wszystkie środowiska, które mają dostęp do są wymienione w kolejności alfabetycznej.

1. Po wybraniu środowiska, albo użyć **FROM** i **na** konfiguracje u góry strony, lub kliknij i przeciągnij za pośrednictwem usługi zakres żądany czas.  Kliknij przycisk lupy w prawym górnym rogu, lub kliknij prawym przyciskiem myszy, za pośrednictwem wybranego przedziału czasu i wybierz pozycję **wyszukiwania**.  

1. Możesz też odświeżyć dostępności automatycznie co minutę, wybierając **automatycznie na** przycisku. **Automatycznie na** przycisk ma zastosowanie tylko do wykresu dostępności nie zawartości głównego wizualizacji.

1. Zwróć uwagę, że ikona chmury platformy Azure umożliwia przejście do środowiska w witrynie Azure portal.

   [![Środowisko usługi Time Series Insights](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Następnie zostanie wyświetlony wykres, który pokazuje liczbę wszystkich zdarzeń w czasie wybranego przedziału czasu.  W tym miejscu masz wiele formantów:

    **Warunki Panel Edytor**:  Miejsce termin jest, gdzie zapytanie środowiska.  Znajduje się w lewej części ekranu:
      - **Miara**:  Ta lista rozwijana pokazuje wszystkie kolumny liczbowe (**wartości podwójnej precyzji**)
      - **Podziel według**: Tej listy rozwijanej pokazuje podzielone na kategorie kolumny (**ciągi**)
      - Można włączyć interpolacji stopniowej, Pokaż minimalne i maksymalne i dostosowania osi y w Panelu sterowania dalej, aby zmierzyć.  Ponadto można dostosować, czy dane wyświetlane jest liczba, średnia lub suma danych.
      - Możesz dodać maksymalnie pięć warunki, aby wyświetlić na tej samej osi x.  Użyj **kopiowania rozwijanej** przycisk, aby dodać dodatkowy termin lub kliknij **Dodaj** przycisk, aby dodać świeże termin.

        [![Panel edytora terminów](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predykatu**:  Predykat umożliwia szybkie filtrowanie zdarzeń przy użyciu zestawu argumentów wymienione poniżej. Jeśli wyszukiwania, wybierając klikając, predykatu będą automatycznie aktualizowane na podstawie tego wyszukiwania. Operand obsługiwane typy:

         |Operacja  |Obsługiwane typy  |Uwagi  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | String, Bool, Double, DateTime, przedział czasu, wartość NULL        |         |
         |IN     | String, Bool, Double, DateTime, przedział czasu, wartość NULL        |  Wszystkie argumenty powinny być tego samego typu lub być stała NULL.        |
         |MA     | String        |  Po prawej stronie dozwolone są tylko stałe literałów. Pusty ciąg i wartości NULL są niedozwolone.       |

      - **Przykłady zapytań**

         [![Przykładowe zapytania](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. **Rozmiar interwału** suwak umożliwia powiększanie i pomniejszanie odstępach czasu za pośrednictwem tego samego przedziału czasu.  Zapewnia to dokładniejszą kontrolę ruchu pomiędzy dużych okresy przedstawiających smooth trendy w dół wycinki tak małej, jak milisekund, dzięki czemu możesz widzieć cenowe szczegółowe, wysokiej rozdzielczości, dane. Domyślne suwaka punkt początkowy jest ustawiony jako najbardziej optymalną wgląd w dane z zaznaczenia; Równoważenie rozdzielczość, prędkość wysyłania zapytań i stopień szczegółowości.

1. **Czasu pędzla** narzędzia można łatwo przejść z jednego przedział czasu do innego, umieszczając intuicyjnego interfejsu użytkownika frontonu i Centrum bezproblemowe przenoszenie między zakresami czasu.

1. **Zapisz** polecenie pozwala zapisać bieżącego zapytania, a następnie włączyć ją na potrzeby udostępniania go innym użytkownikom środowiska. Za pomocą **Otwórz**, możesz zobaczyć wszystkich zapisanych zapytań i wszelkie udostępnione zapytania innych użytkowników w środowiskach, masz dostęp do.

   [![Zapytania](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Wizualizowanie danych

1. **Widoku perspektywy** narzędzie wyświetla jednoczesnych maksymalnie cztery kwerend unikatowy. Przycisk widoku perspektywy można znaleźć w prawym górnym rogu wykresu.  

   [![Widok perspektywy](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. **Wykresu** pozwala wizualnie eksplorować dane. Narzędzia wykresów obejmują:

    - **Wybierz opcję/kliknij**, które umożliwia wybór określonego przedziału czasu lub jedną serię danych.  
    - W czasie span zaznaczenia, można powiększać lub Eksploruj zdarzenia.  
    - W serii danych można podzielić serii według innej kolumny, dodać serii jako nowy termin, Pokaż tylko wybrane serię, Wyklucz wybrane serii, ping tej serii; Eksploruj zdarzenia z wybranej serii
    - W obszarze filtru po lewej stronie wykresu można zobaczyć wszystkie szeregi wyświetlanych danych i kolejności według wartości lub nazwa, wyświetlanie wszystkich serii danych lub serii przypiętych lub odpięte.  Można również wybierz jedną serię danych i podzielić serii według innej kolumny, dodać serii jako nowy termin, Pokaż tylko wybrane serię, Wyklucz wybrane serii, przypiąć tej serii; Eksploruj zdarzenia z wybranej serii
    - Podczas przeglądania jednocześnie wiele wersji warunków, można stosu, anulowania, zobacz dodatkowe dane dotyczące serii danych i używać tej samej osi y we wszystkich warunków umowy za pomocą przycisków w prawym górnym rogu wykresu.

    [![Narzędzia wykresów](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. **Mapy cieplnej** pozwala szybko wychwycić serii danych unikatowego lub nietypowych w podanej kwerendy. Termin wyszukiwania tylko jeden może ich bezpośrednią wizualizację mapy cieplnej.

    [![Heatmap](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. **Zdarzenia**:  Po wybraniu Eksploruj zdarzenia podczas wybierania lub kliknij prawym przyciskiem myszy, powyżej, w panelu zdarzeń jest udostępniana.  Tutaj widać wszystkie nieprzetworzone zdarzenia i eksportowanie zdarzeń jako pliki JSON lub CSV. Usługa Time Series Insights są przechowywane wszystkie nieprzetworzone dane.

    [![Zdarzenia](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Kliknij przycisk **statystyki** kartę po eksplorację zdarzeń w celu udostępnienia wzorce i statystyki kolumn.  

    - **Wzorce**: tej funkcji i odpowiedzi proaktywnie udostępnia najbardziej są statystycznie istotne wzorców w regionie wybranych danych. To zwalnia z konieczności Przyjrzyj się tysięcy zdarzeń, aby zrozumieć, jakie wzorców najbardziej oświadcza, czasu i energii. Ponadto usługi Time Series Insights pozwala przejść bezpośrednio do tych wzorców są statystycznie istotne, aby kontynuować, przeprowadzanie analizy. Ta funkcja jest również przydatne w przypadku późniejszej śledztwa w danych historycznych.

    - **Statystyki kolumn**:  Statystyki kolumn zapewniają wykresów i tabel, które podzielenie danych z każdej kolumny w serii danych wybranego przez wybrany okres.  

      [![STATYSTYKI](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Teraz wiesz, różnych funkcji i opcji dostępnych w ramach aplikacji internetowej w Eksploratorze usługi Time Series Insights.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [diagnozowania i rozwiązywania problemów](time-series-insights-diagnose-and-solve-problems.md) w danym środowisku usługi Time Series Insights.

- Wykonać krok po kroku [Insights serii czasu pakietu Azure Quickstart](time-series-quickstart.md) samouczka.
