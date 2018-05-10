---
title: Eksportowanie przy użyciu usługi Stream Analytics z usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Analiza strumienia stale można przekształcić, filtrować i przekazywanie danych, które możesz wyeksportować z usługi Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: mbullwin
ms.openlocfilehash: c898e43cb1334bf7fb1836554fb92708033d3f7d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Używać usługi Stream Analytics do przetworzenia wyeksportowane dane z usługi Application Insights
[Usługa Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) to idealne narzędzie do przetwarzania danych [wyeksportowany z usługi Application Insights](app-insights-export-telemetry.md). Analiza strumienia może pobierają dane z różnych źródeł. Go transformacji i filtrowanie danych i kierowania go do różnych sink.

W tym przykładzie utworzymy Adapter pobiera dane z usługi Application Insights, zmiana nazwy i przetwarza część pól, która powoduje przekazanie w potoku go do usługi Power BI.

> [!WARNING]
> Są znacznie lepsze i łatwiejsze [zalecane sposoby wyświetlania danych usługi Application Insights w usłudze Power BI](app-insights-export-power-bi.md). Ścieżka przedstawione w tym miejscu jest po prostu przykład ilustrujący sposób przetwarzania wyeksportowane dane.
> 
> 

![Diagram blokowy eksportu przez administratora systemu do PBI](./media/app-insights-export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Tworzenie magazynu na platformie Azure
Eksport ciągły zawsze generuje dane do konta usługi Azure Storage, należy najpierw utworzyć magazyn.

1. Utwórz konto magazynu "klasycznym" w ramach subskrypcji w [portalu Azure](https://portal.azure.com).
   
   ![W portalu Azure wybierz opcję Nowy, danych, magazynu](./media/app-insights-export-stream-analytics/030.png)
2. Tworzenie kontenera
   
    ![W nowym magazynie wybierz kontenery, kliknij Kafelek kontenerów, a następnie dodaj](./media/app-insights-export-stream-analytics/040.png)
3. Skopiuj klucz dostępu do magazynu
   
    Należy ją szybko, aby skonfigurować dane wejściowe usługi analiza strumienia.
   
    ![W magazynie Otwórz ustawienia kluczy i wykonać kopię podstawowego klucza dostępu](./media/app-insights-export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Rozpocząć eksport ciągły w celu magazynu Azure
[Eksport ciągły](app-insights-export-telemetry.md) przenosi dane z usługi Application Insights do magazynu Azure.

1. W portalu Azure przejdź do zasobu usługi Application Insights, utworzone dla aplikacji.
   
    ![Kliknij przycisk Przeglądaj, usługi Application Insights aplikacji](./media/app-insights-export-stream-analytics/050.png)
2. Utwórz eksport ciągły.
   
    ![Wybierz ustawienia, Eksport ciągły](./media/app-insights-export-stream-analytics/060.png)

    Wybierz utworzone wcześniej konto magazynu:

    ![Skonfiguruj docelowego eksportu](./media/app-insights-export-stream-analytics/070.png)

    Ustaw typy zdarzeń, które chcesz wyświetlić:

    ![Wybierz typy zdarzeń](./media/app-insights-export-stream-analytics/080.png)

1. Let niektóre dane gromadzone. Zaczekaj i innym użytkownikom za pomocą aplikacji przez pewien czas. Dane telemetryczne wejdzie i zobaczysz statystyczne wykresów w [Eksploratora metryk](app-insights-metrics-explorer.md) i zdarzeń z [diagnostycznych wyszukiwania](app-insights-diagnostic-search.md). 
   
    A także będzie eksportować dane do usługi magazynu. 
2. Sprawdź, czy wyeksportowane dane. W programie Visual Studio, wybierz **wyświetlić / w chmurze Explorer**i otwórz Azure / magazynu. (Jeśli nie masz tej opcji menu, należy zainstalować zestaw Azure SDK: Otwórz okno dialogowe nowego projektu i Otwórz program Visual C# / w chmurze / Get Microsoft Azure SDK dla platformy .NET.)
   
    ![](./media/app-insights-export-stream-analytics/04-data.png)
   
    Zanotuj części wspólnej nazwy ścieżki, która jest pochodną klucz nazwy i instrumentacji aplikacji. 

Zdarzenia są zapisywane do obiektu blob pliki w formacie JSON. Każdy plik może zawierać co najmniej jednego zdarzenia. Dlatego chcemy odczytuje dane zdarzenia i filtrować polami, którą chcemy udostępnić. Wszystkie rodzaje czynności, które firma Microsoft może wykonywać z danymi, ale naszego planu jest obecnie przekazać dane do usługi Power BI za pomocą usługi analiza strumienia.

## <a name="create-an-azure-stream-analytics-instance"></a>Utwórz wystąpienie usługi Azure Stream Analytics
Z [portalu Azure](https://portal.azure.com/), wybierz usługę Azure Stream Analytics i utworzyć nowe zadanie usługi Stream Analytics:

![](./media/app-insights-export-stream-analytics/SA001.png)

![](./media/app-insights-export-stream-analytics/SA002.png)

Po utworzeniu nowego zadania, wybierz **przejdź do zasobu**.

![](./media/app-insights-export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Dodaj nowe dane wejściowe

![](./media/app-insights-export-stream-analytics/SA004.png)

Ustaw, aby pobrać dane wejściowe z obiektu blob z eksportu ciągłego:

![](./media/app-insights-export-stream-analytics/SA005.png)

Teraz musisz podstawowy klucz dostępu z konta magazynu, który wcześniej zapisany. Ustaw jako klucz konta magazynu.

### <a name="set-path-prefix-pattern"></a>Wzorzec prefiksu ścieżki zestawu

**Pamiętaj ustawienie formatu RRRR-MM-DD (Łączniki).**

Ścieżka prefiksu wzorzec Określa, gdzie Stream Analytics wyszukuje pliki wejściowe w magazynie. Należy ustawić odpowiadają jak eksportu ciągłego przechowuje dane. Ustaw go następująco:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

W tym przykładzie:

* `webapplication27` jest to nazwa zasobu usługi Application Insights **małe litery**.
* `1234...` jest to klucz Instrumentacji zasobu usługi Application Insights **pominięcie łączniki**. 
* `PageViews` jest to typ danych, które mają być analizowane. Dostępne typy są zależne od filtr ustawionych w eksportu ciągłego. Sprawdź wyeksportowane dane na temat dostępnych typów oraz temat [wyeksportować modelu danych](app-insights-export-data-model.md).
* `/{date}/{time}` wzorzec są zapisywane jako literału.

> [!NOTE]
> Sprawdź, czy Magazyn upewnij się, że można uzyskać ścieżki prawo.
> 

## <a name="add-new-output"></a>Dodaj nowe dane wyjściowe
Teraz wybierz zadanie > **dane wyjściowe** > **Dodaj**.

![](./media/app-insights-export-stream-analytics/SA006.png)


![Wybierz nowy kanał, kliknij pozycję dane wyjściowe, Dodaj usługi Power BI](./media/app-insights-export-stream-analytics/SA010.png)

Podaj Twojej **konto służbowe** do autoryzacji Stream Analytics, aby uzyskać dostęp do zasobów usługi Power BI. Następnie magazynowa nazwę dla danych wyjściowych i docelowy zestaw danych z usługi Power BI i tabeli.

## <a name="set-the-query"></a>Ustawianie zapytania
Zapytanie podlega translacji z danych wejściowych i wyjściowych.

Funkcja testu Aby sprawdzić, czy możesz uzyskać prawo danych wyjściowych. Nadaj przykładowych danych, które miał na stronie dane wejściowe. 

### <a name="query-to-display-counts-of-events"></a>Aby wyświetlić liczby zdarzeń
Wklej tego zapytania:

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

* dane wejściowe eksportu jest alias, który firma Microsoft udostępniła do strumienia danych wejściowych
* dane wyjściowe pbi jest alias wyjściowy, który zdefiniowanego
* Używamy [zewnętrzne GetElements ZASTOSOWAĆ](https://msdn.microsoft.com/library/azure/dn706229.aspx) ponieważ nazwa zdarzenia jest zagnieżdżony tablicy JSON. Następnie wybierz wybiera nazwę zdarzenia, wraz z licznik wystąpienia o tej nazwie w przedziale czasu. [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) klauzuli grupuje elementy w okresach jednej minuty.

### <a name="query-to-display-metric-values"></a>Aby wyświetlić wartości metryki
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

* To zapytanie bardziej szczegółowy na dane telemetryczne metryki można pobrać czasu zdarzenia i wartość metryki. Wartości metryki znajdują się wewnątrz tablicy, więc używamy zewnętrzne GetElements Zastosuj wzorzec wyodrębniać wiersze. "myMetric" w tym przypadku jest nazwa metryki. 

### <a name="query-to-include-values-of-dimension-properties"></a>Zapytanie zawierało wartości właściwości wymiaru
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

* To zapytanie zawiera wartości bez właściwości wymiaru, w zależności od określonego wymiaru w stałej indeks w tablicy wymiaru.

## <a name="run-the-job"></a>Uruchom zadanie
W przeszłości, aby uruchomić zadanie z można wybrać datę. 

![Wybierz zadanie, a następnie kliknij przycisk zapytanie. Wklej poniższy przykład.](./media/app-insights-export-stream-analytics/SA008.png)

Zaczekaj, aż zadanie jest uruchomione.

## <a name="see-results-in-power-bi"></a>Zobacz wyniki w usłudze Power BI
> [!WARNING]
> Są znacznie lepsze i łatwiejsze [zalecane sposoby wyświetlania danych usługi Application Insights w usłudze Power BI](app-insights-export-power-bi.md). Ścieżka przedstawione w tym miejscu jest po prostu przykład ilustrujący sposób przetwarzania wyeksportowane dane.
> 
> 

Otwórz usługę Power BI pracy lub konta służbowego, a następnie wybierz zestaw danych i tabeli, która jest zdefiniowana jako dane wyjściowe zadania usługi analiza strumienia.

![W usłudze Power BI wybierz pola i zestawu danych.](./media/app-insights-export-stream-analytics/200.png)

Teraz można używać tego zestawu danych, raportów i pulpitów nawigacyjnych w [usługi Power BI](https://powerbi.microsoft.com).

![W usłudze Power BI wybierz pola i zestawu danych.](./media/app-insights-export-stream-analytics/210.png)

## <a name="no-data"></a>Brak danych?
* Sprawdź, że [ustawić format daty](#set-path-prefix-pattern) poprawnie do RRRR-MM-DD (z kreskami).

## <a name="video"></a>Połączenia wideo
Noam Ben Zeev pokazuje, jak można przetworzyć wyeksportowane dane przy użyciu usługi Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* [Eksport ciągły](app-insights-export-telemetry.md)
* [Szczegółowe dane modelu odwołania dla typów właściwości i wartości.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)

