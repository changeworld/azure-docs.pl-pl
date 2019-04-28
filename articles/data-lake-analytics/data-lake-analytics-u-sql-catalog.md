---
title: Rozpoczynanie pracy przy użyciu wykazu języka U-SQL w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak używać katalogu U-SQL do udostępniania kodu i danych.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: a6faa7037ccbacc0547401dd52bb3b19abd1c474
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813353"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Rozpoczynanie pracy przy użyciu wykazu języka U-SQL w usłudze Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Tworzenie funkcji TVF

W poprzednim skrypcie U-SQL powtarzany jest użycie WYODRĘBNIANIA można odczytać z tym samym pliku źródłowym. Przy użyciu języka U-SQL funkcji zwracającej tabelę (TVF) umożliwiająca Hermetyzowanie danych w celu wykorzystania w przyszłości.  

Poniższy skrypt tworzy funkcji TVF o nazwie `Searchlog()` w domyślnej bazy danych i schemat:

```
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
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
RETURN;
END;
```

Poniższy skrypt pokazuje, jak używać funkcji TVF, która została zdefiniowana w poprzednim skrypcie:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SearchLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Tworzenie widoków

Jeśli wyrażenie jedno zapytanie, zamiast funkcji TVF służy widok U-SQL do hermetyzacji to wyrażenie.

Poniższy skrypt tworzy widok o nazwie `SearchlogView` w domyślnej bazy danych i schemat:

```
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

Poniższy skrypt pokazuje użycie zdefiniowano widoku:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>Tworzenie tabel
Zgodnie z tabelami relacyjnymi bazami danych przy użyciu języka U-SQL można utworzyć tabeli ze wstępnie zdefiniowanym schematem lub utworzyć tabelę, która wnioskuje schemat z zapytania, który wypełnia tabeli (znany także jako CREATE TABLE AS SELECT lub CTAS).

Utwórz bazę danych i tabel za pomocą następującego skryptu:

```
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>Wykonywanie zapytań dotyczących tabel
Można tworzyć zapytania tabelami, takie jak te utworzone w poprzednim skrypcie tak samo jak zapytania plików danych. Zamiast tworzenia zestawu wierszy za pomocą WYODRĘBNIANIA, możesz teraz mogą odwoływać się do nazwy tabeli.

Aby zapoznać się z tabel, zmodyfikuj skryptu transformacji, której użyto poprzednio:

```
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >Obecnie nie można uruchomić SELECT w tabeli w ten sam skrypt, której została utworzona tabela.

## <a name="next-steps"></a>Następne kroki
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów przy użyciu witryny Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
