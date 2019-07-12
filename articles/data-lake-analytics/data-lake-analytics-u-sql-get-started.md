---
title: Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics
description: Poznaj podstawy języka U-SQL w usłudze Azure Data Lake Analytics.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 2a138801ba13c6008880e3d24c89d1c23323b853
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626215"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics
U-SQL jest językiem, który łączy deklaratywne SQL za pomocą imperatywne C# umożliwia przetwarzanie danych w dowolnej skali. Za pomocą funkcji skalowalnych, rozproszonych zapytań U-SQL można efektywnie analizować dane w magazynach relacyjnych, takich jak Azure SQL Database. Przy użyciu języka U-SQL może przetwarzać dane bez określonej struktury, stosując schematu przy odczycie oraz Wstawianie niestandardowej logiki i funkcji zdefiniowanych przez użytkownika. Ponadto U-SQL zawiera rozszerzalności, który zapewnia precyzyjną kontrolę nad jak wykonać na dużą skalę. 

## <a name="learning-resources"></a>Zasoby szkoleniowe

* [Samouczka języka U-SQL](https://aka.ms/usqltutorial) zawiera przewodnik krok po kroku większości języka U-SQL. Ten dokument jest zalecane dla wszystkich deweloperów, która chce dowiedzieć się więcej U-SQL do czytania.
* Aby uzyskać szczegółowe informacje na temat **składni języka U-SQL**, zobacz [dokumentację języka U-SQL](https://docs.microsoft.com/u-sql/).
* Aby zrozumieć **zasady projektowania klas języka U-SQL**, zobacz wpis w blogu programu Visual Studio [wprowadzenie do języka U-SQL — języka, który umożliwia łatwe przetwarzanie danych big data](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem przykłady języka U-SQL, w tym dokumencie, przeczytaj i wykonaj [samouczka: Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Ten samouczek wyjaśnia mechanika przy użyciu języka U-SQL przy użyciu narzędzi Azure Data Lake Tools for Visual Studio.

## <a name="your-first-u-sql-script"></a>Pierwszy skrypt U-SQL

Poniższy skrypt U-SQL jest proste, a także ułatwiają badanie wielu aspektów języka U-SQL.

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

Ten skrypt nie ma żadnych kroków przekształcania. Odczytuje ze źródłowego pliku o nazwie `SearchLog.tsv`schematizes go i zapisuje zestaw wierszy z powrotem do pliku o nazwie SearchLog pierwszej u-sql.csv.

Zwróć uwagę, znaku zapytania obok danych, wpisz `Duration` pola. Oznacza to, że `Duration` pole może mieć wartości null.

### <a name="key-concepts"></a>Kluczowe pojęcia
* **Zestaw wierszy zmienne**: Każde wyrażenie zapytania, który produkuje zestawu wierszy można przypisać do zmiennej. U-SQL jest zgodny ze zmiennej wzorcem nazewnictwa języka T-SQL (`@searchlog`, na przykład) w skrypcie.
* **WYODRĘBNIĆ** — słowo kluczowe odczytuje dane z pliku i definiuje schematu przy odczycie. `Extractors.Tsv` to wbudowane ekstraktor U-SQL dla plików kartę wartości rozdzielanych przecinkami. Można tworzyć niestandardowe ekstraktory.
* **Dane wyjściowe** zapisuje dane z zestawu wierszy w pliku. `Outputters.Csv()` to wbudowane outputter U-SQL, aby utworzyć plik przecinkami wartości rozdzielanych przecinkami. Można tworzyć niestandardowe outputters.

### <a name="file-paths"></a>Ścieżki plików

Instrukcji EXTRACT i danych wyjściowych, użyj ścieżki pliku. Ścieżki plików może być bezwzględny lub względny:

To następujące ścieżki bezwzględnej odwołuje się do pliku o nazwie Store jeziora danych `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Ta następującą ścieżkę pliku, który rozpoczyna się od `"/"`. Odwołuje się do pliku w domyślnego konta Data Lake Store:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Użyj zmienne skalarne

Zmienne skalarne można użyć również, aby ułatwić Twojej obsługi skryptów. Poprzedni skrypt U-SQL, również może być zapisana jako:

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

## <a name="transform-rowsets"></a>Przekształcanie zestawy wierszy

Użyj **wybierz** do przekształcania zestawów wierszy:

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

Używa klauzuli WHERE [wyrażenie języka C# logiczne](/dotnet/csharp/language-reference/operators/index). Język wyrażeń języka C# można użyć w celu własnych wyrażeń i funkcji. Możesz nawet wykonywać bardziej złożone filtrowanie, łącząc je z spójniki logiczne (i) i disjunctions (ORs).

Poniższy skrypt używa metody DateTime.Parse() i połączeniu.

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
 >Drugie zapytanie działa na wynik pierwszego zestawu wierszy, który tworzy złożonego dwa filtry. Można także ponownie użyć nazwy zmiennej, a nazwy są w zakresie leksykalnie.

## <a name="aggregate-rowsets"></a>Łączny zestawy wierszy
U-SQL umożliwia znanych klauzuli ORDER BY, GROUP BY i agregacji.

Następujące zapytanie znajdzie całkowity czas trwania na region i wyświetla górnej pięć czasów trwania w kolejności.

Zestawy wierszy U-SQL nie zachowuj ich kolejność, w następnym zapytaniu. W związku z tym aby uporządkować dane wyjściowe, należy dodać klauzuli ORDER BY w niniejszych zasadach dane wyjściowe:

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

U-SQL klauzuli ORDER wymaga przy użyciu klauzuli FETCH w wyrażeniu SELECT.

Klauzula o języku U-SQL może służyć do dane wyjściowe zostaną ograniczone do grup, które spełniają warunek HAVING:

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

W przypadku scenariuszy zaawansowanych agregacji, zobacz dokumentację referencyjną języka U-SQL [agregacji, analitycznych i odwoływać się do funkcji](/u-sql/built-in-functions)

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
