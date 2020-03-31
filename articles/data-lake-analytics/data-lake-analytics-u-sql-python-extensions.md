---
title: Rozszerzanie skryptów U-SQL za pomocą języka Python w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak uruchomić kod języka Python w skryptach U-SQL przy użyciu usługi Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 0a49cbdb4caf474d0628fea3679ce712d37886e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60813403"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Rozszerzanie skryptów U-SQL za pomocą kodu języka Python w usłudze Azure Data Lake Analytics

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozszerzenia języka Python są zainstalowane na koncie usługi Azure Data Lake Analytics.

* Przejdź do konta usługi Data Lake Analytics w witrynie Azure portal
* W menu po lewej stronie w obszarze **WPROWADZENIE** kliknij **przykładowe skrypty**
* Kliknij **pozycję Zainstaluj rozszerzenia U-SQL,** a następnie przycisk **OK**

## <a name="overview"></a>Omówienie 

Rozszerzenia Języka Python dla języka U-SQL umożliwiają deweloperom masowe równoległe wykonywanie kodu Języka Python. Poniższy przykład ilustruje podstawowe kroki:

* Użyj `REFERENCE ASSEMBLY` instrukcji, aby włączyć rozszerzenia języka Python dla skryptu U-SQL
* Używanie `REDUCE` operacji do partycjonowania danych wejściowych na kluczu
* Rozszerzenia Języka Python dla języka U-SQL zawierają`Extension.Python.Reducer`wbudowany reduktor ( ), który uruchamia kod Języka Python na każdym wierzchołku przypisanym do reduktora
* Skrypt U-SQL zawiera osadzony kod języka Python, który ma funkcję o nazwie, `usqlml_main` która akceptuje pandas DataFrame jako dane wejściowe i zwraca pandas DataFrame jako dane wyjściowe.

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

## <a name="how-python-integrates-with-u-sql"></a>Jak Python integruje się z U-SQL

### <a name="datatypes"></a>Typy danych

* Kolumny ciągów i liczb z języka U-SQL są konwertowane jako —jest między pandami a U-SQL
* Wartości null U-SQL są konwertowane na i z wartości Pandas `NA`

### <a name="schemas"></a>Schematy

* Wektory indeksu w pandach nie są obsługiwane w języku U-SQL. Wszystkie klatki danych wejściowych w funkcji Python zawsze mają 64-bitowy indeks numeryczny od 0 do liczby wierszy minus 1. 
* Zestawy danych U-SQL nie mogą mieć zduplikowanych nazw kolumn
* Nazwy kolumn zestawów danych U-SQL, które nie są ciągami. 

### <a name="python-versions"></a>Wersje Pythona
Obsługiwany jest tylko Python 3.5.1 (skompilowany dla systemu Windows). 

### <a name="standard-python-modules"></a>Standardowe moduły Pythona
Wszystkie standardowe moduły Pythona są dołączone.

### <a name="additional-python-modules"></a>Dodatkowe moduły Języka Python
Oprócz standardowych bibliotek Pythona zawiera kilka powszechnie używanych bibliotek python:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Komunikaty o wyjątkach
Obecnie wyjątek w kodzie języka Python pojawia się jako ogólny błąd wierzchołka. W przyszłości komunikaty o błędach zadania U-SQL będą wyświetlać komunikat o wyjątku języka Python.

### <a name="input-and-output-size-limitations"></a>Ograniczenia rozmiaru wejścia i wyjścia
Każdy wierzchołek ma ograniczoną ilość pamięci przypisane do niego. Obecnie limit ten wynosi 6 GB dla AU. Ponieważ input i output DataFrames musi istnieć w pamięci w kodzie języka Python, całkowity rozmiar danych wejściowych i wyjściowych nie może przekraczać 6 GB.

## <a name="see-also"></a>Zobacz też
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Korzystanie z funkcji okna U-SQL dla zadań usługi Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
* [Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
