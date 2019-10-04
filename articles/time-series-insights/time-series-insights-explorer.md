---
title: Eksplorowanie danych za pomocą Eksploratora Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano, jak używać Eksploratora Azure Time Series Insights w przeglądarce sieci Web, aby szybko zobaczyć globalny widok danych Big Data i zweryfikować środowisko IoT.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 3a6bebfa11294821ff717f221e3e0ddfd68cd65c
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948224"
---
# <a name="azure-time-series-insights-explorer"></a>Eksplorator Azure Time Series Insights

W tym artykule opisano funkcje i opcje ogólnie dostępne dla [aplikacji sieci Web](https://insights.timeseries.azure.com/)programu Azure Time Series Insights Explorer. W Eksploratorze Time Series Insights przedstawiono zaawansowane możliwości wizualizacji danych zapewniane przez usługę i można uzyskać do nich dostęp w ramach własnego środowiska.

Azure Time Series Insights to w pełni zarządzana usługa związana z analizą, przechowywaniem i wizualizacją, dzięki której z łatwością można badać i analizować w tym samym momencie miliardy zdarzeń IoT. Udostępnia on globalny widok danych, który umożliwia szybkie Weryfikowanie rozwiązania IoT i uniknięcie kosztownych przestojów w przypadku urządzeń o krytycznym znaczeniu. Można odkrywać ukryte trendy, wykrycia i przeprowadzać analizę głównych przyczyn niemal w czasie rzeczywistym. Eksplorator Time Series Insights jest obecnie w publicznej wersji zapoznawczej.

> [!TIP]
> Aby zapoznać się z przewodnikiem w środowisku demonstracyjnym, Przeczytaj [Azure Time Series Insights przewodniku szybki start](time-series-quickstart.md).

## <a name="video"></a>Wideo

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Dowiedz się więcej o wysyłaniu zapytań do danych za pomocą Eksploratora Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Zobacz poprzednie wideo <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"wprowadzenie do Time Series Insights przy użyciu akceleratora rozwiązań usługi Azure IoT".</a>

## <a name="prerequisites"></a>Wymagania wstępne

Aby móc korzystać z Eksploratora Time Series Insights, musisz:

- Utwórz środowisko Time Series Insights. Aby uzyskać więcej informacji, zobacz [jak rozpocząć pracę z Time Series Insights](./time-series-insights-get-started.md).
- [Zapewnianie dostępu](time-series-insights-data-access.md) do Twojego konta w środowisku.
- Dodaj do niego źródło zdarzeń [Centrum IoT](time-series-insights-how-to-add-an-event-source-iothub.md) lub [centrum zdarzeń](time-series-insights-how-to-add-an-event-source-eventhub.md) .

## <a name="explore-and-query-data"></a>Eksplorowanie i wykonywanie zapytań dotyczących danych

W ciągu kilku minut od podłączenia źródła zdarzeń do środowiska Time Series Insights można eksplorować dane szeregów czasowych i wysyłać do nich zapytania.

1. Aby rozpocząć, Otwórz [eksploratora Time Series Insights](https://insights.timeseries.azure.com/) w przeglądarce internetowej. Po lewej stronie okna wybierz środowisko. Wszystkie środowiska, do których masz dostęp, są wyświetlane w kolejności alfabetycznej.

1. Po wybraniu środowiska Użyj opcji **od** i **do** w górnej części strony lub kliknij i przeciągnij kursor w żądanym przedziale czasu. Wybierz lupę w prawym górnym rogu lub kliknij prawym przyciskiem myszy wybrany przedział czasu i wybierz pozycję **Wyszukaj**.

1. Dostępność będzie również automatycznie odświeżana co minutę przez wybranie przycisku **automatycznie włączone** . Przycisk **Włącz** opcję dotyczy tylko wykresu dostępności, a nie zawartości głównej wizualizacji.

1. Ikona chmury platformy Azure przenosi do środowiska w Azure Portal.

   [@no__t — środowisko usługi 1Time Series Insights](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Następnie zobaczysz wykres pokazujący liczbę wszystkich zdarzeń w wybranym przedziałie czasu. Oto kilka kontrolek:

    - **Panel edytorów terminów**: termin jest miejscem, w którym można wykonywać zapytania dotyczące środowiska. Znajduje się po lewej stronie ekranu:
      - **Miara**: Ta lista rozwijana zawiera wszystkie kolumny liczbowe (**podwaja**).
      - **Podział według**: Ta lista rozwijana zawiera kolumny kategorii (**ciągi**).
      - Możesz włączyć interpolację krokową, pokazać wartość minimalną i maksymalną, a następnie dostosować oś y w panelu sterowania obok pozycji **pomiar**. Można również określić, czy pokazywane dane są liczbami, średnimi czy sumą danych.
      - Można dodać maksymalnie pięć wyrazów, aby wyświetlić je na tej samej osi x. Użyj przycisku **kopiowania** , aby dodać dodatkowy termin, lub wybierz pozycję **Dodaj** , aby dodać nowy termin.

        [@no__t — panel edytora 1Terms](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predykat**: Użyj predykatu, aby szybko filtrować zdarzenia przy użyciu zestawu argumentów operacji wymienionych w poniższej tabeli. W przypadku przeprowadzania wyszukiwania przez wybranie lub kliknięcie, predykat jest automatycznie aktualizowany na podstawie tego wyszukiwania. Obsługiwane typy operandów to:

         |Operacja  |Obsługiwane typy  |Uwagi  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | Ciąg, bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | Ciąg, bool, Double, DateTime, TimeSpan, NULL        |  Wszystkie operandy powinny być tego samego typu lub być stałe o wartości NULL.        |
         |MA     | Ciąg        |  Tylko stałe literały ciągu są dozwolone po prawej stronie. Pusty ciąg i wartość NULL są niedozwolone.       |

      - **Przykłady zapytań**

         [@no__t — zapytania 1Example](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Za pomocą suwaka **rozmiar interwału** można powiększać i pomniejszać interwały dla tego samego przedziału czasu. Suwak zapewnia dokładniejszą kontrolę nad przenoszeniem między dużymi wycinkami czasu, które pokazują gładkie trendy w dół do wycinków, tak jak w milisekundach, co pozwala na wyświetlanie szczegółowych i wysokiej rozdzielczości kawałków danych. Domyślny punkt początkowy suwaka jest ustawiany jako najbardziej optymalny widok danych z wyboru w celu zrównoważenia rozdzielczości, szybkości zapytania i stopnia szczegółowości.

1. Narzędzie **pędzel czasu** ułatwia nawigowanie z jednego przedziału czasowego do innego.

1. Użyj polecenia **Zapisz** , aby zapisać bieżące zapytanie i udostępnić je innym użytkownikom środowiska. W przypadku korzystania z programu **Open**można zobaczyć wszystkie zapisane zapytania i wszystkie zapytania udostępnione innym użytkownikom w środowiskach, do których masz dostęp.

   [@no__t — 1Queries](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Wizualizowanie danych

1. Użyj narzędzia **widok perspektywy** , aby zobaczyć równoczesny widok maksymalnie czterech unikatowych zapytań. Przycisk **widok perspektywy** znajduje się w prawym górnym rogu wykresu.

   [@no__t — widok 1Perspective](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Wyświetl wykres, aby wizualnie eksplorować dane, i użyj narzędzi **wykresu** :

    - **Zaznacz** lub **kliknij** określony przedział czasu lub jedną serię danych.
    - W ramach zaznaczenia TimeSpan możesz powiększać lub eksplorować zdarzenia.
    - W ramach serii danych można podzielić Serie według innej kolumny, dodać serię jako nowy termin, wyświetlić tylko wybraną serię, wykluczyć wybraną serię, wysłać polecenie ping do tej serii lub eksplorować zdarzenia z wybranej serii.
    - W obszarze filtru z lewej strony wykresu można zobaczyć wszystkie wyświetlane serie danych i zmienić kolejność według wartości lub nazwy. Można również wyświetlić wszystkie serie danych lub przypięte lub odpięte serie. Można wybrać jedną serię danych i podzielić serię według innej kolumny, dodać serię jako nowy termin, wyświetlić tylko wybraną serię, wykluczyć wybraną serię, przypiąć tę serię lub eksplorować zdarzenia z wybranej serii.
    - Gdy przeglądasz wiele warunków jednocześnie, możesz układać, rozstosować, wyświetlać dodatkowe dane dotyczące serii danych i używać tej samej osi y dla wszystkich warunków. Użyj przycisków w prawym górnym rogu wykresu.

    [@no__t — narzędzie 1Chart](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Użyj **mapę cieplną** , aby szybko wyszukiwać unikatowe lub nietypowe serie danych w danym zapytaniu. Tylko jeden termin wyszukiwania może być wizualny jako mapę cieplną.

    [@no__t — 1Heatmap](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. Gdy eksplorujesz zdarzenia, zaznaczając lub klikając prawym przyciskiem myszy, panel **zdarzenia** jest dostępny. W tym miejscu można zobaczyć wszystkie zdarzenia pierwotne i wyeksportować zdarzenia jako pliki JSON lub CSV. Time Series Insights przechowuje wszystkie nieprzetworzone dane.

    [@no__t — 1Events](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Wybierz kartę **statystyki** po eksplorowaniu zdarzeń w celu uwidocznienia wzorców i statystyk kolumn.

    - **Wzorce**: Ta funkcja aktywnie przydzieli najbardziej statystycznie znaczące wzorce w wybranym regionie danych. Nie trzeba przeglądać tysięcy zdarzeń, aby zrozumieć, jakie wzorce wymagają najwięcej czasu i energii. Za pomocą Time Series Insights możesz przejść bezpośrednio do tych statystycznie znaczących wzorców, aby kontynuować przeprowadzanie analizy. Ta funkcja jest również przydatna w przypadku badań pośmiertnych w danych historycznych.
    - **Statystyki kolumn**: statystyki kolumn zawierają wykresy i tabele dzielące dane z każdej kolumny wybranej serii danych na wybrany przedział czasu.

      [@no__t — 1STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Teraz widzisz różne funkcje i opcje, które są dostępne w aplikacji sieci Web programu Time Series Insights Explorer.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak diagnozować i rozwiązywać problemy](time-series-insights-diagnose-and-solve-problems.md) w środowisku Time Series Insights.
- Zapoznaj się z przewodnikiem [szybki start Azure Time Series Insights](time-series-quickstart.md) .
