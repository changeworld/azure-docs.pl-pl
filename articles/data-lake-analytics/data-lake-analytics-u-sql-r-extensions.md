---
title: Rozszerzanie skryptów U-SQL z języka R w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak uruchomić kod języka R w skryptów U-SQL przy użyciu usługi Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 8b22b4238b20f56727d1c7858094328ab8817dad
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624928"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Rozszerzanie skryptów U-SQL z kodem R w usłudze Azure Data Lake Analytics

Poniższy przykład przedstawia podstawowe kroki wdrażania R kodu:
* Użyj `REFERENCE ASSEMBLY` instrukcji, aby włączyć rozszerzenia R skryptu U-SQL.
* Użyj` REDUCE` operacji do partycjonowania danych wejściowych dla klucza.
* Rozszerzenia R U-SQL zawierają wbudowane reduktor (`Extension.R.Reducer`) uruchomione na każdy wierzchołek przypisane do reduktor kodu języka R. 
* Użycie dedykowanych o nazwie ramek danych o nazwie `inputFromUSQL` i `outputToUSQL `odpowiednio do przekazywania danych między U-SQL i R. dane wejściowe i wyjściowe DataFrame rozwiązane nazwy identyfikatorów (oznacza to, użytkownicy nie można zmienić tych wstępnie zdefiniowanych nazw danych wejściowych i wyjściowych DataFrame identyfikatory).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Osadzanie kodu języka R w skryptu U-SQL

Można R kodu skryptu U-SQL przy użyciu parametru polecenia wbudowanym `Extension.R.Reducer`. Na przykład można zadeklarować skrypt języka R jako zmienna typu ciąg i przekaż go jako parametru reduktor.


    REFERENCE ASSEMBLY [ExtR];
    
    DECLARE @myRScript = @"
    inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
    lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
    #do not return readonly columns and make sure that the column names are the same in usql and r scripts,
    outputToUSQL=data.frame(summary(lm.fit)$coefficients)
    colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
    outputToUSQL
    ";
    
    @RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, rReturnType:"dataframe");

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Zachowaj w osobnym pliku kodu języka R i odwołaj się skrypt U-SQL

Poniższy przykład przedstawia użycie bardziej złożonych. W takim przypadku kodu języka R jest wdrażany jako zasób, który jest skrypt U-SQL.

Zapisz ten kod R jako oddzielny plik.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Aby wdrożyć ten skrypt języka R z instrukcji wdrażania zasobów za pomocą skryptu U-SQL.

    REFERENCE ASSEMBLY [ExtR];

    DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT 
            SepalLength double,
            SepalWidth double,
            PetalLength double,
            PetalWidth double,
            Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT 
            Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput = REDUCE @ExtendedData ON Par
        PRODUCE Par, fit double, lwr double, upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
        OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();

## <a name="how-r-integrates-with-u-sql"></a>Jak R integruje się z języka U-SQL

### <a name="datatypes"></a>Typy danych
* Ciąg, jak i numeryczne kolumny z U-SQL są konwertowane na — między R DataFrame i języka U-SQL [obsługiwane typy: `double`, `string`, `bool`, `integer`, `byte`].
* `Factor` Typ danych nie jest obsługiwane w języku U-SQL.
* `byte[]` musi być Zserializowany jako algorytmem base64 `string`.
* Ciągi języka U-SQL może zostać przekonwertowany na czynniki w kodzie języka R, kiedy U-SQL utworzony dataframe wejściowych R lub przez ustawienie dla parametru reduktor `stringsAsFactors: true`.

### <a name="schemas"></a>Schematy
* Zestaw danych skryptu U-SQL nie mogą mieć zduplikowanych nazw kolumn.
* Nazwy kolumn U-SQL zestawów danych muszą być ciągami.
* Nazwy kolumn muszą być takie same w języku U-SQL i skrypty R.
* Kolumny tylko do odczytu nie może być częścią dataframe danych wyjściowych. Ponieważ kolumny tylko do odczytu są automatycznie wprowadzić Wstecz w tabeli U-SQL, jeśli jest częścią UDO schemat danych wyjściowych.

### <a name="functional-limitations"></a>Ograniczenia funkcjonalności
* Nie można utworzyć wystąpienia aparatu R, dwukrotnie w tym samym procesie. 
* U-SQL nie obsługuje obecnie udo łączenia do przewidywania za pomocą partycjonowanego modeli wygenerowanych przy użyciu udo reduktor. Użytkownicy mogą zadeklarować modeli partycjonowane jako zasób i używać ich w ich skrypt języka R (zobacz przykładowy kod `ExtR_PredictUsingLMRawStringReducer.usql`)

### <a name="r-versions"></a>Wersje R
R 3.2.2 jest obsługiwana.

### <a name="standard-r-modules"></a>Standardowe moduły R

    base
    boot
    Class
    Cluster
    codetools
    compiler
    datasets
    doParallel
    doRSR
    foreach
    foreign
    Graphics
    grDevices
    grid
    iterators
    KernSmooth
    lattice
    MASS
    Matrix
    Methods
    mgcv
    nlme
    Nnet
    Parallel
    pkgXMLBuilder
    RevoIOQ
    revoIpe
    RevoMods
    RevoPemaR
    RevoRpeConnector
    RevoRsrConnector
    RevoScaleR
    RevoTreeView
    RevoUtils
    RevoUtilsMath
    Rpart
    RUnit
    spatial
    splines
    Stats
    stats4
    survival
    Tcltk
    Tools
    translations
    utils
    XML

### <a name="input-and-output-size-limitations"></a>Dane wejściowe i ograniczenia rozmiaru danych wyjściowych
Każdy wierzchołek ma ograniczoną ilość pamięci przypisanej do niego. Ponieważ DataFrames wejściowych i wyjściowych musi istnieć w pamięci w kodzie języka R, wówczas łączny rozmiar danych wejściowych i wyjściowych nie może przekraczać 500 MB.

### <a name="sample-code"></a>Przykładowy kod
Więcej przykładowy kod jest dostępny na koncie usługi Data Lake Store, po zainstalowaniu rozszerzenia Analytics zaawansowane U-SQL. Ścieżka więcej przykładowy kod jest: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Wdrożenie modułów R niestandardowe w języku U-SQL

Najpierw należy utworzyć niestandardowego modułu R i zip go, a następnie przekaż plik zip niestandardowego modułu R do magazynu ADL. W tym przykładzie możemy przekazać magittr_1.5.zip do katalogu głównego koncie ADLS domyślnego konta ADLA, który jest używany. Po przekazaniu modułu do magazynu ADL, Zadeklaruj ją jako umożliwia wdrażanie zasobów udostępnić skryptu U-SQL i wywołanie `install.packages` go zainstalować.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomePackages.txt";

    // R script to run
    DECLARE @myRScript = @"
    # install the magrittr package,
    install.packages('magrittr_1.5.zip', repos = NULL),
    # load the magrittr package,
    require(magrittr),
    # demonstrate use of the magrittr package,
    2 %>% sqrt
    ";

    @InputData =
    EXTRACT SepalLength double,
    SepalWidth double,
    PetalLength double,
    PetalWidth double,
    Species string
    FROM @IrisData
    USING Extractors.Csv();

    @ExtendedData =
    SELECT 0 AS Par,
    *
    FROM @InputData;

    @RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, RowId int, ROutput string
    READONLY Par
    USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");

    OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();

## <a name="next-steps"></a>Następne kroki
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Zadania usługi Azure Data Lake Analytics przy użyciu funkcji okna języka U-SQL](data-lake-analytics-use-window-functions.md)
