---
title: Rozszerzanie skryptów U-SQL z języka Python w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak uruchomić kod języka Python w skryptów U-SQL przy użyciu usługi Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 3e895a6ea9bccc0d210f43748edb3eea80ddc6ad
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625084"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Rozszerzanie skryptów U-SQL kodu języka Python z usługą Azure Data Lake Analytics

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że na koncie usługi Azure Data Lake Analytics są zainstalowane rozszerzenia języka Python.

* Przejdź do użytkownika konta usługi Data Lake Analytics w portalu Azure
* W menu po lewej stronie w obszarze **wprowadzenie** kliknij **przykładowe skrypty**
* Kliknij przycisk **zainstalować rozszerzenia języka U-SQL** następnie **OK**

## <a name="overview"></a>Przegląd 

Rozszerzenia języka Python dla U-SQL umożliwiają deweloperom wykonać równoległemu wykonywanie kodu języka Python. Poniższy przykład przedstawia podstawowe kroki:

* Użyj `REFERENCE ASSEMBLY` instrukcji, aby włączyć rozszerzenia języka Python dla skryptu U-SQL
* Przy użyciu `REDUCE` operacji do partycjonowania danych wejściowych dla klucza
* Rozszerzenia języka Python dla U-SQL to wbudowane reduktor (`Extension.Python.Reducer`) uruchomione na każdy wierzchołek przypisane do reduktor kodu języka Python
* Skrypt U-SQL zawiera osadzony kod języka Python, który ma funkcji o nazwie `usqlml_main` akceptuje pandas DataFrame jako dane wejściowe i zwraca pandas DataFrame jako dane wyjściowe.

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

* Ciąg, jak i numeryczne kolumny z U-SQL są konwertowane na — między Pandas i języka U-SQL
* U-SQL wartości null są konwertowane do i z Pandas `NA` wartości

### <a name="schemas"></a>Schematy

* Wektory indeksu w Pandas nie są obsługiwane w języku U-SQL. Wszystkie ramki danych wejściowych w funkcji języka Python zawsze ma 64-bitowych indeksu numerycznych od 0 do liczby wierszy pomniejszonej o 1. 
* Zestaw danych skryptu U-SQL nie mogą mieć zduplikowanych nazw kolumn
* U-SQL nazwy kolumn zestawów danych, które nie są ciągami. 

### <a name="python-versions"></a>Wersji języka Python
Obsługiwane jest tylko Python 3.5.1 (skompilowany dla systemu Windows). 

### <a name="standard-python-modules"></a>Standardowe moduły języka Python
Uwzględniane są wszystkie standardowe moduły języka Python.

### <a name="additional-python-modules"></a>Dodatkowe moduły języka Python
Oprócz standardowych bibliotek języka Python uwzględniono kilka bibliotek często używane python:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Komunikaty o wyjątkach
Obecnie Wystąpił wyjątek w kodzie języka Python jest wyświetlany jako błąd rodzajowy wierzchołka. Komunikaty o błędach zadań U-SQL w przyszłości, wyświetli komunikat o wyjątku języka Python.

### <a name="input-and-output-size-limitations"></a>Dane wejściowe i ograniczenia rozmiaru danych wyjściowych
Każdy wierzchołek ma ograniczoną ilość pamięci przypisanej do niego. Obecnie ten limit jest 6 GB dla funkcji Aktualizacje automatyczne. Ponieważ DataFrames wejściowych i wyjściowych musi istnieć w pamięci w kodzie języka Python, wówczas łączny rozmiar danych wejściowych i wyjściowych nie może przekroczyć 6 GB.

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Zadania usługi Azure Data Lake Analytics przy użyciu funkcji okna języka U-SQL](data-lake-analytics-use-window-functions.md)
* [Użyj usługi Azure Data Lake Tools dla programu Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
