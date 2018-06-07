---
title: Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics
description: Poznaj podstawy języka U-SQL w usłudze Azure Data Lake Analytics.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 2903046154808da5113f7b32a04bbfba254a07ae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623455"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics
U-SQL jest językiem, który łączy deklaratywne SQL z nadrzędnych C# umożliwia przetwarzanie danych w dowolnej skali. Za pomocą możliwości skalowalnych, rozproszonych zapytań U-SQL można efektywne analizowanie danych w sklepach relacyjne, takie jak bazy danych SQL Azure. Języku U-SQL może przetwarzać danych niestrukturalnych przy zastosowaniu schematu na odczyt i wstawianie niestandardowej logiki i funkcji UDF. Ponadto U-SQL obejmuje rozszerzalności, który zapewnia precyzyjną kontrolę nad jak wykonać na dużą skalę. 

## <a name="learning-resources"></a>Materiałów szkoleniowych

* [Samouczek U-SQL](http://aka.ms/usqltutorial) zawiera przewodnik krok po kroku większości języka U-SQL. Ten dokument jest zalecane odczytu dla wszystkich deweloperów pożądane dowiedzieć się więcej U-SQL.
* Aby uzyskać szczegółowe informacje o **składni języka U-SQL**, zobacz [dokumentację języka U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).
* Aby zrozumieć **zasady projektowania klas U-SQL**, zobacz Visual Studio blogu [wprowadzenie do języka U-SQL — A język, który ułatwia Big przetwarzania danych](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed przejściem przez próbek U-SQL, w tym dokumencie, odczytu i wykonania [samouczek: skrypty opracowanie U-SQL przy użyciu narzędzi Data Lake Tools dla programu Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Ten samouczek wyjaśnia sposób mechanika przy użyciu języka U-SQL z usługi Azure Data Lake Tools dla programu Visual Studio.

## <a name="your-first-u-sql-script"></a>Pierwszy skrypt U-SQL

Poniższy skrypt U-SQL jest prosta i ułatwiają badanie wiele aspektów języka U-SQL.

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

Ten skrypt nie ma żadnych kroków transformacji. Odczytuje ze źródłowego pliku o nazwie `SearchLog.tsv`schematizes go i ponownie zapisuje zestawu wierszy w pliku o nazwie SearchLog pierwszej u-sql.csv.

Zwróć uwagę, znakiem zapytania obok danych, wpisz w `Duration` pola. Oznacza, że `Duration` pole może mieć wartości null.

### <a name="key-concepts"></a>Kluczowe pojęcia
* **Zestaw wierszy zmiennych**: każde wyrażenie zapytania, który spowoduje utworzenie zestawu wierszy można przypisać do zmiennej. U-SQL jest zgodny ze zmiennej wzorcem nazw T-SQL (`@searchlog`, na przykład) w skrypcie.
* **WYODRĘBNIĆ** — słowo kluczowe odczytywać dane z pliku i definiuje schemat na odczyt. `Extractors.Tsv` jest wbudowane ekstraktor U-SQL dla plików kartę wartości rozdzielanych przecinkami. Można tworzyć niestandardowe ekstraktory.
* **Dane wyjściowe** zapisuje dane z zestawu wierszy w pliku. `Outputters.Csv()` jest wbudowane outputter U-SQL do utworzenia pliku przecinkami wartości rozdzielanych przecinkami. Można tworzyć niestandardowe outputters.

### <a name="file-paths"></a>Ścieżki do plików

Instrukcje WYODRĘBNIANIA i dane wyjściowe Użyj ścieżki do pliku. Ścieżki plików może być bezwzględny lub względny:

Ta następująca ścieżka bezwzględna do pliku, który odwołuje się do pliku w usłudze Data Lake Store o nazwie `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Rozpoczyna się od tego następująca ścieżka pliku `"/"`. Odnosi się do pliku w domyślnego konta usługi Data Lake Store:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Użyj zmienne skalara

Aby ułatwić programu obsługi skryptów można również używać zmiennych skalarne. Poprzedni skrypt U-SQL również może być zapisany jako:

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

## <a name="transform-rowsets"></a>Przekształć zestawy wierszy

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

Używa klauzuli WHERE [C# logicznego wyrażenia](https://msdn.microsoft.com/library/6a71f45d.aspx). Język wyrażeń C# można użyć własnych wyrażeń i funkcji. Można nawet wykonywać bardziej złożone filtrowanie łącząc je z spójników logiczne (i) i disjunctions (ORs).

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
 >Wynik pierwszy zestaw wierszy, który tworzy złożone dwa filtry działające drugiego zapytania. Można również użyć ponownie nazwę zmiennej, a lexically zakresu nazw.

## <a name="aggregate-rowsets"></a>Łączny zestawy wierszy
U-SQL umożliwia znanych ORDER BY, GROUP BY i agregacji.

Następujące zapytanie znajdzie całkowity czas trwania na region, a następnie pięć okresów Wyświetla górnej, w kolejności.

Zestawy wierszy U-SQL nie zachowuj ich kolejność dalej zapytania. W związku z tym aby uporządkować dane wyjściowe, należy dodać ORDER BY w danych wyjściowych instrukcji:

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

Klauzula o języku U-SQL można ograniczyć dane wyjściowe do grupy, które spełniają warunek HAVING:

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

W scenariuszach zaawansowanych agregacji, zobacz dokumentację referencyjną U-SQL, aby uzyskać [agregacji, analitycznych i referencyjne funkcji](https://msdn.microsoft.com/library/azure/mt621335.aspx)

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
