---
title: Korzystanie z katalogu U-SQL w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak używać katalogu U-SQL do udostępniania kodu i danych. Tworzenie funkcji wycenianych w tabeli, tworzenie widoków, tworzenie tabel i wykonywanie ich kwerend.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: f3b9f14be4422373fb30f8c3d4909fd9c9546fdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672847"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Wprowadzenie do wykazu U-SQL w usłudze Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Tworzenie tvf

W poprzednim skrypcie U-SQL powtórzony użycie EXTRACT do odczytu z tego samego pliku źródłowego. Za pomocą U-SQL tabeli cenne funkcji (TVF), można hermetyzować dane do przyszłego ponownego użycia.  

Poniższy skrypt tworzy plik `Searchlog()` TVF wywoływany w domyślnej bazie danych i schemacie:

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

Poniższy skrypt pokazuje, jak używać TVF, który został zdefiniowany w poprzednim skrypcie:

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

Jeśli masz pojedyncze wyrażenie zapytania, zamiast TVF można użyć U-SQL VIEW do hermetyzacji tego wyrażenia.

Następujący skrypt tworzy widok `SearchlogView` wywoływany w domyślnej bazie danych i schemacie:

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

Poniższy skrypt pokazuje użycie zdefiniowanego widoku:

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
Podobnie jak w przypadku tabel relacyjnych bazy danych, za pomocą języka U-SQL można utworzyć tabelę ze wstępnie zdefiniowanym schematem lub utworzyć tabelę, która wylicza schemat z kwerendy, która wypełnia tabelę (nazywaną również CREATE TABLE AS SELECT lub CTAS).

Utwórz bazę danych i dwie tabele przy użyciu następującego skryptu:

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
Można wysyłać zapytania do tabel, takich jak utworzone w poprzednim skrypcie, w taki sam sposób, w jaki wysyłasz zapytania do plików danych. Zamiast tworzyć zestaw wierszy przy użyciu EXTRACT, można teraz odwołać się do nazwy tabeli.

Aby odczytać z tabel, zmodyfikuj skrypt przekształcania, który był wcześniej używany:

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
 >Obecnie nie można uruchomić select w tabeli w tym samym skrypcie, w którym utworzono tabelę.

## <a name="next-steps"></a>Następne kroki
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów przy użyciu witryny Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
