---
title: Eksportowanie przy użyciu usługi Stream Analytics z usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Stream Analytics może stale przekształcania, filtrowanie i kierować dane, które są eksportowane z usługi Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: mbullwin
ms.openlocfilehash: d4a4196aa601fc8da79da3962faec026eff5ec87
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625056"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Użyj usługi Stream Analytics, aby przetworzyć wyeksportowane dane z usługi Application Insights
[Usługa Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) jest idealnym narzędziem do przetwarzania danych [wyeksportowany z usługi Application Insights](export-telemetry.md). Stream Analytics można pobierają dane z różnych źródeł. Go przekształcić i filtrowania danych, a następnie kierować do różnych ujścia.

W tym przykładzie utworzymy adaptera, który pobiera dane z usługi Application Insights, zmiana nazwy, przetwarza niektóre pola i przekazuje go do usługi Power BI.

> [!WARNING]
> Są znacznie lepsze i łatwiejsze [zalecane sposoby, aby wyświetlić dane usługi Application Insights w usłudze Power BI](../../azure-monitor/app/export-power-bi.md ). Ścieżka przedstawionych w tym miejscu jest tylko przykład ilustrujący sposób przetwarzania wyeksportowane dane.
> 
> 

![Diagram blokowy eksportu przez administratora systemu do usługi PBI](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Tworzenie magazynu na platformie Azure
Eksport ciągły zawsze wysyła dane do konta usługi Azure Storage, dlatego musisz najpierw utworzyć magazynu.

1. Tworzenie konta magazynu "klasyczny" w Twojej subskrypcji w [witryny Azure portal](https://portal.azure.com).
   
   ![W witrynie Azure portal wybierz pozycję Nowy, danych, Magazyn](./media/export-stream-analytics/030.png)
2. Tworzenie kontenera
   
    ![W nowym magazynie wybierz kontenery, kliknij Kafelek kontenerów, a następnie dodaj](./media/export-stream-analytics/040.png)
3. Kopiowanie klucza dostępu do magazynu
   
    Będzie potrzebny wkrótce, aby skonfigurować dane wejściowe do usługi stream analytics.
   
    ![W magazynie Otwórz okno Ustawienia i klucze i utwórz jego kopię podstawowego klucza dostępu](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Rozpocząć eksport ciągły w usłudze Azure storage
[Eksport ciągły](export-telemetry.md) przenosi dane z usługi Application Insights do usługi Azure storage.

1. W witrynie Azure portal przejdź do zasobu usługi Application Insights, utworzonej aplikacji.
   
    ![Wybierz kolejno opcje Przeglądaj, Application Insights aplikacji](./media/export-stream-analytics/050.png)
2. Utwórz eksport ciągły.
   
    ![Wybierz pozycję Dodaj ustawienia eksportu ciągłego](./media/export-stream-analytics/060.png)

    Wybierz konto magazynu, która została utworzona wcześniej:

    ![Ustaw miejsce docelowe eksportu](./media/export-stream-analytics/070.png)

    Ustaw typy zdarzeń, które mają być wyświetlane:

    ![Wybierz typy zdarzeń](./media/export-stream-analytics/080.png)

1. Pozwól, niektóre dane są gromadzone. Zaczekaj i umożliwianie użytkownikom korzystania z aplikacji przez jakiś czas. Dane telemetryczne pojawią się, a zobaczysz statystyczne wykresów w [Eksplorator metryk](../../azure-monitor/app/metrics-explorer.md) i zdarzeń z [wyszukiwaniu diagnostycznym](../../azure-monitor/app/diagnostic-search.md). 
   
    A także dane zostaną wyeksportowane do usługi storage. 
2. Sprawdź, czy wyeksportowane dane. W programie Visual Studio, wybierz **wyświetlić / w chmurze Explorer**, a następnie otwórz Azure / Storage. (Jeśli nie masz tej opcji menu, należy zainstalować zestaw Azure SDK: Otwórz okno dialogowe Nowy projekt i otwórz Visual C# / w chmurze / uzyskiwanie Microsoft Azure SDK dla platformy .NET.)
   
    ![](./media/export-stream-analytics/04-data.png)
   
    Zanotuj części wspólnej nazwy ścieżki, która jest pochodną klucz nazwy i instrumentacji aplikacji. 

Zdarzenia są zapisywane do obiektu blob, pliki w formacie JSON. Każdy plik może zawierać jedno lub więcej zdarzeń. Dlatego chcielibyśmy odczytuje dane zdarzenia i filtrowanie pól, które chcemy. Istnieją wszelkiego rodzaju czynności, które firma Microsoft może wykonać przy użyciu danych, ale naszego planu jest obecnie przekazać dane do usługi Power BI za pomocą usługi Stream Analytics.

## <a name="create-an-azure-stream-analytics-instance"></a>Tworzenie wystąpienia usługi Azure Stream Analytics
Z [witryny Azure portal](https://portal.azure.com/), wybierz usługę Azure Stream Analytics i utworzyć nowe zadanie usługi Stream Analytics:

![](./media/export-stream-analytics/SA001.png)

![](./media/export-stream-analytics/SA002.png)

Gdy zostanie utworzone nowe zadanie, wybierz pozycję **przejdź do zasobu**.

![](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Dodaj nowe dane wejściowe

![](./media/export-stream-analytics/SA004.png)

Ustaw, aby pobrać dane wejściowe z obiektu blob z eksportu ciągłego:

![](./media/export-stream-analytics/SA0005.png)

Teraz będzie potrzebna podstawowy klucz dostępu z Twojego konta magazynu, możesz zauważyć, wcześniej. Ustaw jako klucz konta magazynu.

### <a name="set-path-prefix-pattern"></a>Wzorzec prefiksu ścieżki zestawu

**Pamiętaj ustawić Format daty RRRR-MM-DD (z kreskami).**

Wzorzec prefiksu ścieżki Określa, gdzie usługi Stream Analytics znajduje pliki wejściowe w magazynie. Musisz ustawić jego odpowiadają jak eksportu ciągłego przechowuje dane. Ustaw ją następująco:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

W tym przykładzie:

* `webapplication27` to nazwa zasobu usługi Application Insights **małymi literami**.
* `1234...` jest to klucz Instrumentacji zasobu usługi Application Insights **pominięcie kresek**. 
* `PageViews` jest to typ danych, które mają być analizowane. Dostępne typy są zależne od filtr, który zostało ustawiony w eksportu ciągłego. Sprawdź wyeksportowane dane, aby zobaczyć dostępne typy, a [eksportowanie modelu danych](export-data-model.md).
* `/{date}/{time}` wzorzec jest zapisywany bezpośrednio.

> [!NOTE]
> Sprawdź, czy Magazyn upewnij się, że otrzymujemy ścieżkę do prawej.
> 

## <a name="add-new-output"></a>Dodaj nowe dane wyjściowe
Teraz wybierz zadania > **dane wyjściowe** > **Dodaj**.

![](./media/export-stream-analytics/SA006.png)


![Wybierz nowy kanał, kliknij przycisk Dodaj dane wyjściowe, usługa Power BI](./media/export-stream-analytics/SA010.png)

Podaj swoje **konto służbowe** do autoryzowania Stream Analytics, aby uzyskać dostęp do zasobów usługi Power BI. Następnie Wymyśl nazwę dla danych wyjściowych, a dla zestawu danych usługi Power BI docelowego i tabeli.

## <a name="set-the-query"></a>Ustaw zapytanie
Zapytanie Określa tłumaczenie z danych wejściowych i wyjściowych.

Użyj funkcji testowej, aby sprawdzić, że otrzymasz odpowiednie dane wyjściowe. Nadaj przykładowych danych, która została przyjęta ze strony danych wejściowych. 

### <a name="query-to-display-counts-of-events"></a>Zapytanie, aby wyświetlić liczby zdarzeń
Wklej to zapytanie:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* dane wejściowe eksportu jest alias, którego firma Microsoft udostępniła do strumienia danych wejściowych
* dane wyjściowe pbi jest alias danych wyjściowych, który zdefiniowaliśmy
* Używamy [zewnętrzne zastosowania metody GetElements](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) ponieważ nazwa zdarzenia znajduje się w zagnieżdżonych tablic JSON. Następnie wybierz wybiera nazwę zdarzenia, wraz z liczbą wystąpień o tej nazwie w przedziale czasu. [Group By](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) klauzuli grupuje elementy w okresach czasu w jednej minuty.

### <a name="query-to-display-metric-values"></a>Zapytanie, aby wyświetlić wartości metryk
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* To zapytanie przejście do telemetrii metryk, które można pobrać czasu zdarzeń i wartość metryki. Wartości metryk są wewnątrz tablicy, więc używana wzorzec zewnętrznego zastosowania metody GetElements wyodrębniać wiersze. "myMetric" to nazwa metryki w tym przypadku. 

### <a name="query-to-include-values-of-dimension-properties"></a>Zapytanie, aby uwzględnić wartości właściwości wymiaru
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* To zapytanie zawiera wartości właściwości wymiaru nie w zależności od określonego wymiaru w stałym indeks w tablicy wymiaru.

## <a name="run-the-job"></a>Uruchamianie zadania
Możesz wybrać datę w przeszłości, aby uruchomić zadanie z. 

![Wybierz zadanie, a następnie kliknij przycisk zapytania. Wklej poniższy przykład.](./media/export-stream-analytics/SA008.png)

Zaczekaj, aż zadanie jest uruchomione.

## <a name="see-results-in-power-bi"></a>Wyświetlanie wyników w usłudze Power BI
> [!WARNING]
> Są znacznie lepsze i łatwiejsze [zalecane sposoby, aby wyświetlić dane usługi Application Insights w usłudze Power BI](../../azure-monitor/app/export-power-bi.md ). Ścieżka przedstawionych w tym miejscu jest tylko przykład ilustrujący sposób przetwarzania wyeksportowane dane.
> 
> 

Otwórz usługę Power BI przy użyciu konta służbowego lub wybierz zestaw danych i tabelę, która jest zdefiniowana jako dane wyjściowe zadania usługi Stream Analytics.

![W usłudze Power BI wybierz zestaw danych i pola.](./media/export-stream-analytics/200.png)

Teraz możesz używać tego zestawu danych w raportach i pulpitów nawigacyjnych w [usługi Power BI](https://powerbi.microsoft.com).

![W usłudze Power BI wybierz zestaw danych i pola.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Brak danych?
* Sprawdź, że [ustawić format daty](#set-path-prefix-pattern) poprawnie do RRRR-MM-DD (z kreskami).

## <a name="video"></a>Połączenia wideo
Noam Ben Zeev przedstawiono sposób przetwarzania wyeksportowane dane przy użyciu usługi Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* [Eksport ciągły](export-telemetry.md)
* [Szczegółowe dane modelu odwołań dla typów właściwości i wartości.](export-data-model.md)
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)

