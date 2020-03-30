---
title: Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics
description: Poznaj podstawy języka U-SQL w usłudze Azure Data Lake Analytics. Napisz pierwszą kwerendę przy użyciu zmiennych do dodatkowych danych z plików, przekształcić zestaw wierszy i zagregować dane.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 8130679dcc519cecd25abf43902c003ad8047df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672833"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Wprowadzenie do u-SQL w usłudze Azure Data Lake Analytics
U-SQL to język, który łączy deklaratywne SQL z imperatywnym C#, aby umożliwić przetwarzanie danych w dowolnej skali. Dzięki skalowalnej, rozproszonej funkcji zapytań U-SQL można wydajnie analizować dane w magazynach relacyjnych, takich jak usługa Azure SQL Database. Za pomocą języka U-SQL można przetwarzać dane nieustrukturyzowane, stosując schemat na odczyt i wstawianie logiki niestandardowej i plików UDF. Ponadto U-SQL zawiera rozszerzalność, która zapewnia szczegółową kontrolę nad tym, jak wykonać na dużą skalę. 

## <a name="learning-resources"></a>Zasoby szkoleniowe

* [Samouczek U-SQL](https://aka.ms/usqltutorial) zawiera przewodnik przewodnik większości języka U-SQL. Ten dokument jest zalecane czytanie dla wszystkich deweloperów, którzy chcą nauczyć się U-SQL.
* Aby uzyskać szczegółowe informacje na temat **składni języka U-SQL,** zobacz [odwołanie do języka U-SQL](https://docs.microsoft.com/u-sql/).
* Aby zrozumieć **filozofię projektowania U-SQL,** zobacz wpis w blogu programu Visual Studio [Wprowadzenie U-SQL - Język, który sprawia, że przetwarzanie dużych zbiorów danych jest łatwe.](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)

## <a name="prerequisites"></a>Wymagania wstępne

Przed przejściem przez przykłady U-SQL w tym dokumencie, przeczytaj i ukończ [samouczek: Opracowanie skryptów U-SQL przy użyciu narzędzi usługi Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). W tym samouczku wyjaśniono mechanikę używania języka U-SQL za pomocą narzędzia usługi Azure Data Lake Tools for Visual Studio.

## <a name="your-first-u-sql-script"></a>Pierwszy skrypt U-SQL

Poniższy skrypt U-SQL jest prosty i pozwala nam zbadać wiele aspektów języka U-SQL.

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Ten skrypt nie ma żadnych kroków transformacji. Odczytuje z pliku `SearchLog.tsv`źródłowego o nazwie , schematyzuje go i zapisuje zestaw wierszy z powrotem do pliku o nazwie SearchLog-first-u-sql.csv.

Zwróć uwagę na znak zapytania obok `Duration` typu danych w polu. Oznacza to, `Duration` że pole może mieć wartość null.

### <a name="key-concepts"></a>Kluczowe pojęcia
* **Zmienne zestawu wierszy:** Każde wyrażenie kwerendy, które tworzy zestaw wierszy, można przypisać do zmiennej. U-SQL następuje wzorzec nazewnictwa zmiennej T-SQL (,`@searchlog`na przykład) w skrypcie.
* **Extract** Słowo kluczowe odczytuje dane z pliku i definiuje schemat na odczyt. `Extractors.Tsv`jest wbudowanym ekstraktorem U-SQL dla plików o wartości oddzielonych kartami. Można tworzyć niestandardowe ekstraktory.
* **DANE WYJŚCIOWE** zapisuje dane z zestawu wierszy do pliku. `Outputters.Csv()`jest wbudowanym wyprowadzaczem U-SQL w celu utworzenia pliku o wartości oddzielonej przecinkami. Można tworzyć niestandardowe outputters.

### <a name="file-paths"></a>Ścieżki plików

Instrukcje EXTRACT i OUTPUT używają ścieżek plików. Ścieżki plików mogą być bezwzględne lub względne:

Ta następująca ścieżka bezwzględnego pliku odnosi się `mystore`do pliku w magazynie Data Lake o nazwie:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Ta następująca ścieżka `"/"`pliku rozpoczyna się od . Odnosi się do pliku na domyślnym koncie Usługi Data Lake Store:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Użyj zmiennych skalarnych

Można również użyć zmiennych skalarnych, aby ułatwić konserwację skryptu. Poprzedni skrypt U-SQL można również zapisać jako:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Przekształcanie zestawów wierszy

Użyj **select** do przekształcania zestawów wierszy:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

Klauzula WHERE używa [wyrażenia logicznego języka C#.](/dotnet/csharp/language-reference/operators/index) Można użyć języka wyrażenia C#, aby wykonać własne wyrażenia i funkcje. Można nawet wykonać bardziej złożone filtrowanie, łącząc je z logicznymi spójnikami (AND) i rozłączami (REGIONÓW).

Poniższy skrypt używa metody DateTime.Parse() i spójników.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datetime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >Druga kwerenda działa na wynik pierwszego zestawu wierszy, który tworzy złożony z dwóch filtrów. Można również użyć ponownie nazwę zmiennej, a nazwy są o zakresie leksykalnym.

## <a name="aggregate-rowsets"></a>Agreguj zestawy wierszy
U-SQL daje znane KOLEJNOŚĆ WEDŁUG, GRUPA WEDŁUG I agregacji.

Poniższa kwerenda znajduje całkowity czas trwania dla regionu, a następnie wyświetla pięć pierwszych czasów trwania w kolejności.

Zestawy wierszy U-SQL nie zachowują kolejności dla następnej kwerendy. W związku z tym, aby zamówić dane wyjściowe, należy dodać ORDER BY do OUTPUT instrukcji:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Klauzula U-SQL ORDER BY wymaga użycia klauzuli FETCH w wyrażeniu SELECT.

Klauzula U-SQL HAVING może służyć do ograniczania danych wyjściowych do grup spełniających warunek HAVING:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
        GROUP BY Region
        HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Aby zapoznać się ze scenariuszami agregacji zaawansowanych, zobacz dokumentację referencyjną U-SQL dla [funkcji agregacji, analizy i odwołań](/u-sql/built-in-functions)

## <a name="next-steps"></a>Następne kroki
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
