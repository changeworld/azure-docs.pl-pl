---
title: Rozszerzanie skryptów U-SQL przy użyciu języka Python w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak uruchamiać kod Python w skryptów U-SQL przy użyciu usługi Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 0a49cbdb4caf474d0628fea3679ce712d37886e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813403"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Rozszerzanie skryptów U-SQL przy użyciu kodu w języku Python w usłudze Azure Data Lake Analytics

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozszerzenia języka Python są zainstalowane na Twoim koncie usługi Azure Data Lake Analytics.

* Przejdź do konta usługi Data Lake Analytics w witrynie Azure portal
* W menu po lewej stronie w obszarze **wprowadzenie** kliknij **przykładowe skrypty**
* Kliknij przycisk **zainstalować rozszerzenia U-SQL** następnie **OK**

## <a name="overview"></a>Omówienie 

Rozszerzenia języka Python dla języka U-SQL umożliwiają deweloperom wykonywania równoległego wykonywania kodu w języku Python. Poniższy przykład ilustruje podstawowe kroki:

* Użyj `REFERENCE ASSEMBLY` instrukcję, aby włączyć rozszerzenia języka Python dla skryptów U-SQL
* Za pomocą `REDUCE` operacji w celu podzielenia danych wejściowych dla klucza
* Rozszerzenia języka Python dla języka U-SQL zawierają wbudowane reduktor (`Extension.Python.Reducer`), które jest uruchamiane kodu w języku Python na każdy wierzchołek przypisane do reduktor
* Skrypt U-SQL zawiera osadzony kod języka Python, który ma funkcję o nazwie `usqlml_main` , akceptuje pandas DataFrame jako dane wejściowe i zwraca pandas DataFrame jako dane wyjściowe.

--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-python-integrates-with-u-sql"></a>Jak Python integruje się z języka U-SQL

### <a name="datatypes"></a>Typy danych

* Kolumny ciągów i liczbowe z języka U-SQL są konwertowane odpowiednio — od Pandas do języka U-SQL
* U-SQL na wartości null są konwertowane do i z biblioteki Pandas `NA` wartości

### <a name="schemas"></a>Schematy

* Indeks wektorów w Pandas nie są obsługiwane w języku U-SQL. Wszystkie ramki danych wejściowych w funkcji języka Python zawsze mają indeksu numerycznego 64-bitowych, od 0 do liczby wierszy, minus 1. 
* Zestawy danych U-SQL nie może mieć zduplikowanych nazw kolumn
* Nazwy kolumn zestawów danych U-SQL, nie będących ciągami. 

### <a name="python-versions"></a>Wersje języka Python
Python 3.5.1 (skompilowane dla Windows) jest obsługiwana. 

### <a name="standard-python-modules"></a>Standardowe moduły języka Python
Uwzględniane są wszystkie standardowe moduły języka Python.

### <a name="additional-python-modules"></a>Dodatkowe moduły języka Python
Oprócz standardowych bibliotek języka Python uwzględniono kilka bibliotek języka python powszechnie używane:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Komunikaty o wyjątkach
Obecnie wyjątek w kodzie języka Python jest wyświetlany jako błąd rodzajowy wierzchołka. W przyszłości komunikaty o błędach zadania U-SQL wyświetli komunikat o wyjątku języka Python.

### <a name="input-and-output-size-limitations"></a>Dane wejściowe i ograniczenia rozmiaru danych wyjściowych
Każdy wierzchołek ma ograniczoną ilość pamięci przypisanej do niego. Obecnie ten limit jest 6 GB do korzystania z jednostki analizy. Ponieważ elementy Dataframe wejściowy i wyjściowy muszą istnieć w pamięci w kodzie języka Python, wówczas łączny rozmiar danych wejściowych i wyjściowych nie może przekroczyć 6 GB.

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Korzystanie z funkcji okien języka U-SQL dla zadań usługi Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
* [Użyj usługi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
