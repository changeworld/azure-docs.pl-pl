---
title: Tworzenie języka U-SQL, Python, R w języku C# dla usługi Azure Data Lake Analytics w programie Visual Studio Code
description: Dowiedz się, jak za pomocą kodu języka Python, R i języku C# można przesłać zadania w usłudze Azure Data Lake.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: 6c234ad6756f4e65e172bf0ffc0ae5a1d35d109b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814065"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Tworzenie języka U-SQL, Python, R w języku C# dla usługi Azure Data Lake Analytics w programie Visual Studio Code
Dowiedz się, jak używać programu Visual Studio Code (VSCode) do pisania języka Python, R i języku C# kodu za zaporą za pomocą języka U-SQL oraz przesyłania zadań do usługi Azure Data Lake. Aby uzyskać więcej informacji na temat usługi Azure Data Lake Tools dla programu VSCode zobacz [używać usługi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Przed napisaniem kodu niestandardowego związanym z kodem, należy otworzyć folder lub obszaru roboczego w VSCode.


## <a name="prerequisites-for-python-and-r"></a>Wymagania wstępne dotyczące języka Python i R
Zarejestruj języka Python i R zestawów rozszerzeń dla konta usługi ADL. 
1. Otwórz swoje konto w portalu.
   - Wybierz pozycję **Przegląd**. 
   - Kliknij przycisk **przykładowy skrypt**.
2. Kliknij przycisk **więcej**.
3. Wybierz **zainstalować rozszerzenia U-SQL**. 
4. Po zainstalowaniu rozszerzenia U-SQL, zostanie wyświetlony komunikat potwierdzenia. 

   ![Konfigurowanie środowiska języka python i R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Aby uzyskać najlepsze doświadczenia na usłudze języka Python i R Zainstaluj rozszerzeń programu VSCode języka Python i R. 

## <a name="develop-python-file"></a>Tworzenie pliku Python
1. Kliknij przycisk **nowy plik** w obszarze roboczym.
2. Napisz swój kod w języku U-SQL. Poniżej przedstawiono przykładowy kod.
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
    
3. Kliknij prawym przyciskiem myszy plik skryptu, a następnie wybierz **ADL: Generowanie pliku CodeBehind języka Python**. 
4. **Xxx.usql.py** generowany jest plik w folderze roboczym. Napisz swój kod w pliku języka Python. Poniżej przedstawiono przykładowy kod.

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
5. Kliknij prawym przyciskiem myszy **USQL** pliku, możesz kliknąć pozycję **skryptu kompilacji** lub **Prześlij zadanie** do uruchomienia zadania.

## <a name="develop-r-file"></a>Tworzenie pliku języka R
1. Kliknij przycisk **nowy plik** w obszarze roboczym.
2. Napisz swój kod w pliku języka U-SQL. Poniżej przedstawiono przykładowy kod.
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
3. Kliknij prawym przyciskiem myszy **USQL** pliku, a następnie wybierz **ADL: Generowanie pliku CodeBehind języka R**. 
4. **Xxx.usql.r** generowany jest plik w folderze roboczym. Napisz swój kod w pliku języka R. Poniżej przedstawiono przykładowy kod.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Kliknij prawym przyciskiem myszy **USQL** pliku, możesz kliknąć pozycję **skryptu kompilacji** lub **Prześlij zadanie** do uruchomienia zadania.

## <a name="develop-c-file"></a>Tworzenie pliku języka C#
Plik związany z kodem jest plik C# skojarzony z jednego skryptu U-SQL. Operatory zdefiniowane przez użytkownika Agregacja uda w JĘZYKU, UDT oraz funkcji zdefiniowanej przez użytkownika w pliku związanym z kodem, można zdefiniować dedykowany skrypt. Operatory zdefiniowane przez użytkownika, UDA, UDT i funkcji definiowanych przez użytkownika można bezpośrednio w skrypcie bez rejestrowania najpierw zestaw. Plik związany z kodem jest umieszczany w tym samym folderze co jego komunikacji równorzędnej pliku skryptu U-SQL. Jeśli skrypt ma nazwę xxx.usql, jako xxx.usql.cs nosi nazwę związanym z kodem. Jeśli usuniesz ręcznie pliku związanego z kodem, funkcja związanym z kodem jest wyłączona dla jego skojarzony skrypt U-SQL. Aby uzyskać więcej informacji na temat pisania kodu klienta dla skryptu U-SQL, zobacz [pisanie i przy użyciu niestandardowego kodu w języku U-SQL: Funkcje zdefiniowane przez użytkownika]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Kliknij przycisk **nowy plik** w obszarze roboczym.
2. Napisz swój kod w pliku języka U-SQL. Poniżej przedstawiono przykładowy kod.
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
3. Kliknij prawym przyciskiem myszy **USQL** pliku, a następnie wybierz **ADL: Generowanie pliku CodeBehind CS**. 
4. **Xxx.usql.cs** generowany jest plik w folderze roboczym. Napisz swój kod w pliku CS. Poniżej przedstawiono przykładowy kod.

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
5. Kliknij prawym przyciskiem myszy **USQL** pliku, możesz kliknąć pozycję **skryptu kompilacji** lub **Prześlij zadanie** do uruchomienia zadania.

## <a name="next-steps"></a>Kolejne kroki
* [Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Uruchamiania lokalnego języka U-SQL i debugowania lokalnego przy użyciu programu Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu programu PowerShell](data-lake-analytics-get-started-powershell.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-get-started-portal.md)
* [Użyj narzędzi Data Lake Tools for Visual Studio do tworzenia aplikacji w języku U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Korzystanie z Data Lake Analytics(U-SQL) katalogu](data-lake-analytics-use-u-sql-catalog.md)
