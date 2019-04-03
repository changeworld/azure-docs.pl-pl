---
title: Rozszerzanie skryptów U-SQL przy użyciu języka R w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak uruchomić kod języka R w skryptów U-SQL przy użyciu usługi Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 59a52b2aeb83732a608f1fcf5bc4de907d25dfd1
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885030"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Rozszerzanie skryptów U-SQL przy użyciu kodu języka R w usłudze Azure Data Lake Analytics

Poniższy przykład ilustruje podstawowe kroki wdrażania kodu R:
* Użyj `REFERENCE ASSEMBLY` instrukcję, aby włączyć rozszerzenia języka R dla skryptu U-SQL.
* Użyj `REDUCE` operacji w celu podzielenia danych wejściowych dla klucza.
* Rozszerzenia języka R dla języka U-SQL zawierają wbudowane reduktor (`Extension.R.Reducer`), które jest uruchamiane kod R na każdy wierzchołek przypisane do reduktor. 
* Użycie dedykowanych o nazwie ramki danych o nazwie `inputFromUSQL` i `outputToUSQL` odpowiednio do przekazywania danych między U-SQL i języka R. w danych wejściowych i wyjściowych ramkę danych są ustalone nazwy identyfikatorów (oznacza to, użytkownicy nie można zmienić tych wstępnie zdefiniowanych nazw dane wejściowe i wyjściowe ramkę danych identyfikatory).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Osadzanie kodu języka R w skrypcie U-SQL

Możesz wbudowane R kodu skryptu U-SQL za pomocą parametru polecenia `Extension.R.Reducer`. Na przykład można zadeklarować skrypt języka R jako zmienna ciągu i przekazać go jako parametr do reduktor.


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

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Aktualizowanie kodu języka R w oddzielnym pliku i odwoływać się do niego skrypt U-SQL

W poniższym przykładzie pokazano bardziej złożonych zastosowaniach. W takim wypadku kod języka R jest wdrażany jako zasób, który jest skrypt U-SQL.

Zapisz ten kod języka R w oddzielnym pliku.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Użyj skryptu U-SQL do wdrożenia tego skryptu języka R za pomocą instrukcji wdrażania zasobów.

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
* Kolumny ciągów i liczbowe z języka U-SQL są konwertowane odpowiednio-wynosi R DataFrame-U-SQL [obsługiwane typy: `double`, `string`, `bool`, `integer`, `byte`].
* `Factor` Jest nieobsługiwany w języku U-SQL.
* `byte[]` musi być serializowana jako kodowany algorytmem Base64 `string`.
* Ciągi języka U-SQL można przekonwertować na czynników, w kodzie języka R, gdy U-SQL, Utwórz ramkę danych wejściowych języka R lub przez ustawienie parametru reduktor `stringsAsFactors: true`.

### <a name="schemas"></a>Schematy
* Zestawy danych U-SQL nie może mieć zduplikowanych nazw kolumn.
* Nazwy kolumn zestawów danych U-SQL muszą być ciągami.
* Nazwy kolumn muszą być takie same, w języku U-SQL i skrypty języka R.
* Kolumny tylko do odczytu nie mogą być częścią dataframe danych wyjściowych. Ponieważ kolumny tylko do odczytu są automatycznie wprowadzane w tabeli U-SQL, jeśli jest on częścią schematu wyjściowego operatory zdefiniowane przez użytkownika.

### <a name="functional-limitations"></a>Ograniczenia funkcjonalności
* Nie można utworzyć wystąpienia aparatu R, dwa razy w tym samym procesie. 
* U-SQL nie obsługuje obecnie udo łączenia do przewidywania przy użyciu modeli podzielonym na partycje, wygenerowany za pomocą udo reduktor. Użytkownicy mogą zadeklarować modeli partycjonowane jako zasób i ich używać w swoich skrypt języka R (zobacz przykładowy kod `ExtR_PredictUsingLMRawStringReducer.usql`)

### <a name="r-versions"></a>Wersje języka R
R 3.2.2 jest obsługiwane.

### <a name="standard-r-modules"></a>Standardowe moduły języka R

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
Każdy wierzchołek ma ograniczoną ilość pamięci przypisanej do niego. Ponieważ elementy Dataframe wejściowy i wyjściowy muszą istnieć w pamięci w kodzie języka R, wówczas łączny rozmiar danych wejściowych i wyjściowych nie może przekraczać 500 MB.

### <a name="sample-code"></a>Przykładowy kod
Więcej przykładowego kodu jest dostępna na koncie usługi Data Lake Store, po zainstalowaniu rozszerzenia zaawansowanej analizy U-SQL. Ścieżka więcej przykładowego kodu jest: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Wdrażanie modułów R niestandardowego przy użyciu języka U-SQL

Najpierw należy utworzyć niestandardowego modułu R zip go i następnie przekaż plik zip niestandardowego modułu R ze swoim magazynem systemu plików ADL. W tym przykładzie firma Microsoft przekaże magittr_1.5.zip do katalogu głównego domyślnego konta ADLS konto ADLA, które są używane. Po przekazaniu moduł do magazynu usługi ADL, Zadeklaruj go jako umożliwia wdrażanie ZASOBU był dostępny w skrypcie U-SQL i wywołania `install.packages` go zainstalować.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";

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
* [Korzystanie z funkcji okien języka U-SQL dla zadań usługi Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
