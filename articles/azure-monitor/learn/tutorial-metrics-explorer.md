---
title: Samouczek — tworzenie wykresu metryk w usłudze Azure Monitor
description: Dowiedz się, jak utworzyć pierwszy wykres metrykowy za pomocą Eksploratora metryk platformy Azure.
author: bwren
ms.author: bwren
ms.subservice: metrics
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: d8f72471dd3109bf584d18f032ec2f4d949a4993
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79082816"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Samouczek: tworzenie wykresu metryk w usłudze Azure Monitor
Eksplorator metryk jest funkcją usługi Azure Monitor w witrynie Azure portal, która umożliwia tworzenie wykresów na podstawie wartości metryk, wizualnie korelować trendy i badać skoki i spadki wartości metryk. Użyj Eksploratora metryk, aby zbadać kondycję i wykorzystanie zasobów platformy Azure lub wykreślić wykresy z metryk niestandardowych. 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wybierz metrykę, dla której chcesz wykreślić wykres
> * Wykonywanie różnych agregacji wartości metryk
> * Modyfikowanie zakresu czasu i szczegółowości wykresu

Poniżej znajduje się film, który pokazuje bardziej obszerny scenariusz niż procedura opisana w tym artykule. Jeśli jesteś nowy w metryki, zalecamy przeczytanie tego artykułu, a następnie obejrzeć film, aby zobaczyć więcej szczegółów. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebujesz zasobu platformy Azure do monitorowania. Można użyć dowolnego zasobu w ramach subskrypcji platformy Azure, który obsługuje metryki. Aby ustalić, czy zasób obsługuje metryki, przejdź do jego menu w witrynie Azure portal i sprawdź, czy istnieje opcja **Metryki** w sekcji **Monitorowanie** menu.


## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do portalu [https://portal.azure.com](https://portal.azure.com)Azure w .

## <a name="open-metrics-explorer-and-select-a-scope"></a>Otwórz eksploratora metryk i wybierz zakres
Eksplorator metryk można otworzyć z menu Usługi Azure Monitor lub z menu zasobu w witrynie Azure portal. Metryki ze wszystkich zasobów są dostępne niezależnie od używanej opcji. 

1. Wybierz **metryki** z menu **Usługi Azure Monitor** lub z sekcji **Monitorowanie** w menu zasobu.

1. Wybierz **zakres**, który jest zasobem, dla którego chcesz zobaczyć metryki. Zakres jest już wypełniony, jeśli otwarto eksploratora metryk z menu zasobu.

    ![Wybieranie zakresu](media/tutorial-metrics-explorer/scope-picker.png)

2. Wybierz **obszar nazw,** jeśli zakres ma więcej niż jeden. Obszar nazw jest tylko sposobem organizowania metryk, dzięki czemu można je łatwo znaleźć. Na przykład konta magazynu mają oddzielne przestrzenie nazw do przechowywania plików, tabel, obiektów blob i kolejek metryki. Wiele typów zasobów ma tylko jedną przestrzeń nazw.

3. Wybierz metrykę z listy dostępnych metryk dla wybranego zakresu i obszaru nazw.

    ![Wybieranie metryki](media/tutorial-metrics-explorer/metric-picker.png)

4. Opcjonalnie zmień **metrykę Agregacja**. Definiuje to, jak wartości metryki będą agregowane w czasie szczegółowości wykresu. Na przykład jeśli ziarnistość czasu jest ustawiona na 15 minut, a agregacja jest ustawiona na sumę, każdy punkt na wykresie będzie sumą wszystkich zebranych wartości w każdym segmencie 15 minut.

    ![Wykres](media/tutorial-metrics-explorer/chart.png)

5. Użyj przycisku **Dodaj metrykę** i powtórz te kroki, jeśli chcesz wyświetlić wiele metryk wykreślonych na tym samym wykresie. W przypadku wielu wykresów w jednym widoku wybierz przycisk **Nowy wykres.**

## <a name="select-a-time-range-and-granularity"></a>Wybierz zakres czasu i szczegółowość

Domyślnie wykres zawiera najnowsze dane 24 godzin metryki. Selektor czasu służy do zmiany **zakresu czasu** dla wykresu lub **szczegółowości czasu,** który definiuje zakres czasu dla każdego punktu danych. Wykres używa określonej agregacji do obliczania wszystkich próbkowanych wartości w określonym czasie ziarnistości.

![Panel Zmienianie zakresu czasu](media/tutorial-metrics-explorer/time-picker.png)


Użyj **pędzla czasu,** aby zbadać interesujący obszar wykresu, taki jak kolec lub dip. Umieść wskaźnik myszy na początku obszaru, kliknij i przytrzymaj lewy przycisk myszy, przeciągnij na drugą stronę obszaru i zwolnij przycisk. Wykres powiększy ten zakres czasu. 

![Szczotka czasowa](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Stosowanie filtrów wymiarów i dzielenia
Zobacz poniższe odwołania do zaawansowanych funkcji, które umożliwiają przeprowadzenie dodatkowej analizy metryk i zidentyfikowanie potencjalnych odstaczy w danych.

- [Filtrowanie](../platform/metrics-charts.md#apply-filters-to-charts) pozwala wybrać, które wartości wymiarów są uwzględniane na wykresie. Na przykład można wyświetlić tylko pomyślne żądania podczas tworzenia wykresów metryki *czasu odpowiedzi serwera.* 

- [Dzielenie](../platform/metrics-charts.md#apply-splitting-to-a-chart) określa, czy wykres wyświetla oddzielne linie dla każdej wartości wymiaru, czy agreguje wartości w jedną linię. Na przykład można wyświetlić jeden wiersz dla średniego czasu odpowiedzi we wszystkich wystąpieniach serwera lub można wybrać oddzielne wiersze dla każdego serwera. 

Zobacz [przykłady wykresów,](../platform/metric-chart-samples.md) na których zastosowano filtrowanie i dzielenie.

## <a name="advanced-chart-settings"></a>Zaawansowane ustawienia wykresu

Można dostosować styl wykresu, tytuł i zmodyfikować zaawansowane ustawienia wykresu. Po zakończeniu dostosowywania przypnij go do pulpitu nawigacyjnego, aby zapisać swoją pracę. Można również skonfigurować alerty metryk. Zobacz [Zaawansowane funkcje Eksploratora metryk platformy Azure,](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis) aby dowiedzieć się więcej o tych i innych zaawansowanych funkcjach Eksploratora metryk usługi Azure Monitor.


## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak pracować z metrykami w usłudze Azure Monitor, dowiedz się, jak używać metryk do wysyłania proaktywnych alertów.

> [!div class="nextstepaction"]
> [Tworzenie i wyświetlanie alertów metryk oraz zarządzanie nimi w usłudze Azure Monitor](../platform/alerts-metric.md)

