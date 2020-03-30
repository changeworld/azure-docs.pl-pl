---
title: Uruchamianie zadań U-SQL w językach Python, R i C# — usługa Azure Data Lake Analytics
description: Dowiedz się, jak używać kodu za pomocą języka Python, R i C# do przesyłania zadania w usłudze Azure Data Lake.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: cb3ddf0c4147fa982e8ab0f9d440292d12803d35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309709"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Tworzenie języka U-SQL za pomocą języka Python, R i C# dla usługi Azure Data Lake Analytics w kodzie programu Visual Studio
Dowiedz się, jak używać kodu programu Visual Studio (VSCode) do pisania kodu Języka Python, Języka R i C# za pomocą języka U-SQL i przesyłania zadań do usługi Azure Data Lake. Aby uzyskać więcej informacji na temat narzędzi usługi Azure Data Lake dla vscode, zobacz [Korzystanie z narzędzi usługi Azure Data Lake dla kodu programu Visual Studio.](data-lake-analytics-data-lake-tools-for-vscode.md)

Przed napisaniem kodu niestandardowego, należy otworzyć folder lub obszar roboczy w vscode.


## <a name="prerequisites-for-python-and-r"></a>Wymagania wstępne dla Pythona i R
Zarejestruj zestawy rozszerzeń Pythona i R dla swojego konta ADL. 
1. Otwórz swoje konto w portalu.
   - Wybierz pozycję **Przegląd**. 
   - Kliknij **pozycję Przykładowy skrypt**.
2. Kliknij pozycję **Więcej**.
3. Wybierz **pozycję Zainstaluj rozszerzenia U-SQL**. 
4. Komunikat potwierdzenia jest wyświetlany po zainstalowaniu rozszerzeń U-SQL. 

   ![Konfigurowanie środowiska dla pythona i języka R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Aby uzyskać najlepsze doświadczenia w usłudze języka Python i R, zainstaluj rozszerzenie VSCode Python i R. 

## <a name="develop-python-file"></a>Tworzenie pliku Języka Python
1. Kliknij **nowy plik** w obszarze roboczym.
2. Napisz kod w U-SQL. Poniżej przedstawiono przykładowy kod.
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
    
3. Kliknij prawym przyciskiem myszy plik skryptu, a następnie wybierz polecenie **ADL: Generuj kod Pythona za plikiem**. 
4. Plik **xxx.usql.py** jest generowany w folderze roboczym. Napisz swój kod w pliku Pythona. Poniżej przedstawiono przykładowy kod.

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
5. Kliknij prawym przyciskiem myszy plik **USQL,** możesz kliknąć przycisk **Skompiluj skrypt** lub **Prześlij zadanie** do uruchomionego zadania.

## <a name="develop-r-file"></a>Tworzenie pliku R
1. Kliknij **nowy plik** w obszarze roboczym.
2. Napisz kod w pliku U-SQL. Poniżej przedstawiono przykładowy kod.
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
3. Kliknij prawym przyciskiem myszy plik **USQL,** a następnie wybierz polecenie **ADL: Generuj kod R za plikiem**. 
4. Plik **xxx.usql.r** jest generowany w folderze roboczym. Napisz kod w pliku R. Poniżej przedstawiono przykładowy kod.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Kliknij prawym przyciskiem myszy plik **USQL,** możesz kliknąć przycisk **Skompiluj skrypt** lub **Prześlij zadanie** do uruchomionego zadania.

## <a name="develop-c-file"></a>Tworzenie pliku języka C#
Plik związany z kodem jest plikiem języka C# skojarzonym z pojedynczym skryptem U-SQL. Skrypt dedykowany do UDO, UDA, UDT i UDF można zdefiniować w pliku związanym z kodem. UDO, UDA, UDT i UDF mogą być używane bezpośrednio w skrypcie bez rejestrowania zestawu po raz pierwszy. Plik związany z kodem jest umieszczany w tym samym folderze co jego równorzędny plik skryptu U-SQL. Jeśli skrypt nosi nazwę xxx.usql, kod jest nazwany jako xxx.usql.cs. Jeśli ręcznie usuniesz plik związany z kodem, funkcja związana z kodem jest wyłączona dla skojarzonego z nim skryptu U-SQL. Aby uzyskać więcej informacji na temat pisania kodu klienta dla skryptu U-SQL, zobacz [Pisanie i używanie kodu niestandardowego w U-SQL: Funkcje zdefiniowane przez użytkownika]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Kliknij **nowy plik** w obszarze roboczym.
2. Napisz kod w pliku U-SQL. Poniżej przedstawiono przykładowy kod.
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
3. Kliknij prawym przyciskiem myszy plik **USQL,** a następnie wybierz polecenie **ADL: Generuj kod CS za plikiem**. 
4. Plik **xxx.usql.cs** jest generowany w folderze roboczym. Napisz kod w pliku CS. Poniżej przedstawiono przykładowy kod.

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
5. Kliknij prawym przyciskiem myszy plik **USQL,** możesz kliknąć przycisk **Skompiluj skrypt** lub **Prześlij zadanie** do uruchomionego zadania.

## <a name="next-steps"></a>Następne kroki
* [Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Lokalne uruchomienie U-SQL i debugowanie lokalne za pomocą kodu programu Visual Studio](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Wprowadzenie do usługi Data Lake Analytics przy użyciu programu PowerShell](data-lake-analytics-get-started-powershell.md)
* [Wprowadzenie do usługi Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-get-started-portal.md)
* [Używanie narzędzi usługi Data Lake Tools dla programu Visual Studio do tworzenia aplikacji U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Korzystanie z katalogu usługi Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
