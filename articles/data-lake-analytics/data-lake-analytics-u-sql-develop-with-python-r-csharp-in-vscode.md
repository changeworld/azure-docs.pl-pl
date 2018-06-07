---
title: Tworzenie skryptu U-SQL, Python, R i C# dla usługi Azure Data Lake Analytics w kodzie programu Visual Studio
description: Dowiedz się, jak za pomocą kodu języka Python, R i C# można przesłać zadania w usłudze Azure Data Lake.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
manager: kfile
editor: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: 171aef186fd681adf9b3d92deb8691c852ea1038
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624911"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Tworzenie skryptu U-SQL, Python, R i C# dla usługi Azure Data Lake Analytics w kodzie programu Visual Studio
Dowiedz się, jak użyć programu Visual Studio Code (VSCode), aby zapisać Python, R i C# kodu za pomocą języka U-SQL i przesyłanie zadań do usługi Azure Data Lake. Aby uzyskać więcej informacji na temat usługi Azure Data Lake Tools dla VSCode, zobacz [użyj narzędzi Azure Data Lake Tools dla Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Przed zapisaniem CodeBehind kodu niestandardowego, należy otworzyć folder lub obszar roboczy w VSCode.


## <a name="prerequisites-for-python-and-r"></a>Wymagania wstępne dotyczące języka Python i R
Rejestrowanie Python i R zestawów rozszerzeń dla Twojego konta ADL. 
1. Otwórz konto w portalu.
   - Wybierz pozycję **Przegląd**. 
   - Kliknij przycisk **przykładowy skrypt**.
2. Kliknij przycisk **więcej**.
3. Wybierz **zainstalować rozszerzenia języka U-SQL**. 
4. Po zainstalowaniu rozszerzenia języka U-SQL, zostanie wyświetlony komunikat potwierdzenia. 

  ![Konfigurowanie środowiska python i R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

  > [!Note]
  > Aby uzyskać najlepsze doświadczenia w usłudze języka Python i R Zainstaluj rozszerzenie VSCode Python i R. 

## <a name="develop-python-file"></a>Tworzenie pliku Python
1. Kliknij przycisk **nowy plik** w obszarze roboczym.
2. Wpisz swój kod w języku U-SQL. Poniżej przedstawiono przykładowy kod.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
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
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. Kliknij prawym przyciskiem myszy plik skryptu, a następnie wybierz **ADL: generowanie kodu Python za pliku**. 
4. **Xxx.usql.py** plik został wygenerowany w folderu roboczego. Wpisz swój kod w pliku języka Python. Poniżej przedstawiono przykładowy kod.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. Kliknij prawym przyciskiem myszy **USQL** pliku, możesz kliknąć **kompilowania skryptu** lub **Prześlij zadanie** do uruchomienia zadania.

## <a name="develop-r-file"></a>Tworzenie pliku R
1. Kliknij przycisk **nowy plik** w obszarze roboczym.
2. Wpisz swój kod w pliku skryptu U-SQL. Poniżej przedstawiono przykładowy kod.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. Kliknij prawym przyciskiem myszy **USQL** pliku, a następnie wybierz **ADL: generowanie kodu języka R za pliku**. 
4. **Xxx.usql.r** plik został wygenerowany w folderu roboczego. Wpisz swój kod w pliku R. Poniżej przedstawiono przykładowy kod.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Kliknij prawym przyciskiem myszy **USQL** pliku, możesz kliknąć **kompilowania skryptu** lub **Prześlij zadanie** do uruchomienia zadania.

## <a name="develop-c-file"></a>Tworzenie pliku języka C#
Plik CodeBehind jest pliku C# skojarzonego z jednego skryptu U-SQL. Skrypt dedykowanych można zdefiniować UDO UDA, UDT i funkcji zdefiniowanej przez użytkownika w pliku CodeBehind. UDO, UDA, UDT i UDF można bezpośrednio w skrypcie bez rejestrowania najpierw zestaw. Plik CodeBehind jest umieszczany w tym samym folderze co jego komunikacji równorzędnej pliku skryptu U-SQL. Jeśli skrypt ma nazwę xxx.usql, jako xxx.usql.cs nosi nazwę CodeBehind. Jeśli ręcznie usuń plik CodeBehind, funkcja CodeBehind jest wyłączona dla jego skojarzony skrypt U-SQL. Aby uzyskać więcej informacji na temat pisania kodu klienta dla skryptu U-SQL, zobacz [pisania i przy użyciu niestandardowego kodu w języku U-SQL: funkcje zdefiniowane przez użytkownika]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Kliknij przycisk **nowy plik** w obszarze roboczym.
2. Wpisz swój kod w pliku skryptu U-SQL. Poniżej przedstawiono przykładowy kod.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. Kliknij prawym przyciskiem myszy **USQL** pliku, a następnie wybierz **ADL: generowanie kodu CS za pliku**. 
4. **Xxx.usql.cs** plik został wygenerowany w folderu roboczego. Wpisz swój kod w pliku CS. Poniżej przedstawiono przykładowy kod.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. Kliknij prawym przyciskiem myszy **USQL** pliku, możesz kliknąć **kompilowania skryptu** lub **Prześlij zadanie** do uruchomienia zadania.

## <a name="next-steps"></a>Kolejne kroki
* [Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Uruchamiania lokalnego skryptu U-SQL i debugowania lokalnego z kodem Visual Studio](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Wprowadzenie do usługi Data Lake Analytics przy użyciu programu PowerShell](data-lake-analytics-get-started-powershell.md)
* [Wprowadzenie do usługi Data Lake Analytics przy użyciu portalu Azure](data-lake-analytics-get-started-portal.md)
* [Użyj narzędzi Data Lake Tools dla programu Visual Studio do tworzenia aplikacji U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Użyj Data Lake Analytics(U-SQL) katalogu](data-lake-analytics-use-u-sql-catalog.md)
