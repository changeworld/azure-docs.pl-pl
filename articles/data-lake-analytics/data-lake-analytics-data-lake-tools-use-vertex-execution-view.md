---
title: Widok wykonywania wierzchołków w narzędziach usługi Data Lake dla programu Visual Studio
description: W tym artykule opisano, jak używać widoku wykonania wierzchołka do egzaminu zadań usługi Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: f5adbb75e6852551976aa040a1a1c723d2e3f59b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309725"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Użyj widoku wykonywania wierzchołków w narzędziach usługi Data Lake dla programu Visual Studio
Dowiedz się, jak używać widoku wykonania wierzchołka do egzaminu zadań usługi Data Lake Analytics.


## <a name="open-the-vertex-execution-view"></a>Otwieranie widoku wykonywania wierzchołka
Otwórz zadanie U-SQL w narzędziach usługi Data Lake Tools dla programu Visual Studio. Kliknij **pozycję Widok wykonania wierzchołka** w lewym dolnym rogu. Może zostać wyświetlony monit o załadowanie profili i może upłynąć trochę czasu w zależności od łączności sieciowej.

![Widok wykonywania wierzchołków narzędzi analizy danych](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Opis widoku wykonania wierzchołka
Widok wykonania wierzchołka ma trzy części:

![Widok wykonywania wierzchołków narzędzi analizy danych](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

Selektor **wierzchołka** po lewej stronie umożliwia wybranie wierzchołków według operacji (takich jak odczyt danych z pierwszej dziesiątki lub wybranie według etapu). Jednym z najczęściej używanych filtrów jest zobaczyć **wierzchołki na ścieżce krytycznej**. **Ścieżka krytyczna** jest najdłuższym łańcuchem wierzchołków zadania U-SQL. Zrozumienie ścieżki krytycznej jest przydatne do optymalizacji zadań przez sprawdzenie, który wierzchołek trwa najdłużej.
  
![Widok wykonywania wierzchołków narzędzi analizy danych](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Górne środkowe okienko pokazuje **stan bieżący wszystkich wierzchołków**.
  
![Widok wykonywania wierzchołków narzędzi analizy danych](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

W dolnym centralnym okienku znajdują się informacje o każdym wierzchołku:
* Nazwa procesu: Nazwa wystąpienia wierzchołka. Składa się z różnych części w StageName| Nazwa wierzchołka| VertexRunInstance. Na przykład SV7_Split[62].v1 wierzchołek oznacza drugie wystąpienie uruchomione (.v1, indeks począwszy od 0) nr wierzchołka 62 w SV7_Split.
* Całkowita liczba odczytu/zapisu danych: dane zostały odczytane/zapisane przez ten wierzchołek.
* Stan/Stan zakończenia: Stan końcowy po zakończeniu wierzchołka.
* Zamknij kod/typ błędu: Błąd, gdy wierzchołek nie powiódł się.
* Przyczyna tworzenia: Dlaczego utworzono wierzchołek.
* Opóźnienie zasobu/opóźnienie procesu/opóźnienie kolejki PN: czas oczekiwania na zasoby, przetwarzania danych i pozostania w kolejce.
* Identyfikator GUID procesu/twórcy: identyfikator GUID dla bieżącego wierzchołka lub jego twórcy.
* Wersja: N-to wystąpienie uruchomionego wierzchołka (system może zaplanować nowe wystąpienia wierzchołka z wielu powodów, na przykład pracy awaryjnej, nadmiarowości obliczeń itp.)
* Wersja czas utworu.
* Proces tworzenie czasu rozpoczęcia/czas oczekiwania w kolejce procesu/czas rozpoczęcia procesu/czas ukończenia procesu: po rozpoczęciu tworzenia procesu wierzchołka; gdy proces wierzchołka zaczyna być w kolejce; po rozpoczęciu określonego procesu wierzchołka; po zakończeniu określonego wierzchołka.

## <a name="next-steps"></a>Następne kroki
* Aby rejestrować informacje diagnostyczne, zobacz [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics)
* Aby uzyskać informacje na temat bardziej złożonego zapytania, zobacz temat [Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby wyświetlić szczegóły zadania, zobacz [Korzystanie z zadań Usługi Przeglądarka zadań i Widok zadań dla usługi Azure Data lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
