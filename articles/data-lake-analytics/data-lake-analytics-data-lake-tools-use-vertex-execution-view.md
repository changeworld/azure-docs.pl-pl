---
title: Korzystanie z widoku wykonania wierzchołka, w narzędziach Data Lake Tools for Visual Studio
description: W tym artykule opisano sposób korzystania z widoku wykonania wierzchołka do zadania usługi Data Lake Analytics egzamin.
services: data-lake-analytics
ms.service: data-lake-analytics
author: mumian
ms.author: jgao
ms.reviewer: jasonwhowell
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: 73314c5864e3036d102deee2792021345b80bf2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60687836"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Korzystanie z widoku wykonania wierzchołka, w narzędziach Data Lake Tools for Visual Studio
Dowiedz się, jak korzystanie z widoku wykonania wierzchołka do zadania usługi Data Lake Analytics egzamin.


## <a name="open-the-vertex-execution-view"></a>Otwórz widok wykonania wierzchołka
Otwórz zadania U-SQL w narzędziach Data Lake Tools for Visual Studio. Kliknij przycisk **widoku wykonania wierzchołka** w lewym dolnym rogu. Może pojawić się prośba najpierw załadować profilów i może potrwać pewien czas w zależności od połączenia sieciowego.

![Usługa Data Lake Analytics narzędzi widoku wykonania wierzchołka](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Zrozumienie widoku wykonania wierzchołka
Widok wykonywania wierzchołka ma trzy części:

![Usługa Data Lake Analytics narzędzi widoku wykonania wierzchołka](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

**Selektor wierzchołka** na umożliwia po lewej stronie wybierz wierzchołków przy użyciu funkcji (takie jak top 10 danych odczytu, lub wybierz według etapu). Jest jednym z najbardziej najczęściej używanych filtrów, aby zobaczyć **wierzchołków ścieżki krytycznej**. **Ścieżkę krytyczną** jest najdłuższym łańcuch wierzchołki zadania U-SQL. Zrozumienie ścieżkę krytyczną jest przydatne w przypadku optymalizacji zadań, sprawdzając wierzchołka, które zajmuje najwięcej czasu.
  
![Usługa Data Lake Analytics narzędzi widoku wykonania wierzchołka](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Pokazuje okienku u góry na środku **bieżący stan wszystkich wierzchołków**.
  
![Usługa Data Lake Analytics narzędzi widoku wykonania wierzchołka](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Dolne okienko center zostaną wyświetlone informacje dotyczące każdego wierzchołka:
* Nazwa procesu: Nazwa wystąpienia wierzchołków. Składa się z różnych części w Nazwa etapu | VertexName | VertexRunInstance. Na przykład wierzchołka [62] .v1 SV7_Split oznacza drugi uruchomionego wystąpienia (.v1, indeksem rozpoczynającym się od 0) 62 liczby wierzchołków w etapie SV7_Split.
* Łączna ilość danych odczytanych/zapisanych: Dane były odczytanych/zapisanych przez tego wierzchołka.
* Stan i wyjścia z niej stanu: Końcowy stan po zakończeniu wierzchołka.
* Zakończenia kodu/szczegóły błędu: Błąd podczas wierzchołka nie powiodło się.
* Przyczyna utworzenia: Dlaczego wierzchołka zostało utworzone.
* Opóźnienie kolejki opóźnienia/PN opóźnienia i przetwarzanie zasobów: czas wierzchołka oczekiwania na zasoby, do przetwarzania danych i pozostanie w kolejce.
* Identyfikator GUID procesu/Twórcy: Identyfikator GUID dla bieżącego wierzchołka uruchomionej lub twórcy.
* Wersja: N-tego wystąpienia uruchomionej wierzchołka (system może zaplanować nowe wystąpienia wierzchołek, wiele przyczyn, na przykład przejścia w tryb failover obliczeniowych nadmiarowości itp.)
* Czas utworzenia wersji.
* Przetwarzanie Utwórz rozpoczęcia procesu/czasu kolejce proces/czasu rozpoczęcia procesu/czasu Complete czasu: podczas uruchamiania procesu wierzchołka tworzenia; podczas uruchamiania procesu wierzchołków w kolejce podczas uruchamiania niektórych procesu wierzchołka; Po wykonaniu pewnych wierzchołka.

## <a name="next-steps"></a>Kolejne kroki
* Aby rejestrować informacje diagnostyczne, zobacz [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics)
* Aby uzyskać informacje na temat bardziej złożonego zapytania, zobacz temat [Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby wyświetlić szczegóły zadania, zobacz [korzystanie z przeglądarki zadań i widoku zadań dla zadań usługi Azure Data lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
