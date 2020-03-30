---
title: Rozszerzanie skryptów U-SQL za pomocą języka R w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak uruchomić kod języka R w skryptach U-SQL przy użyciu usługi Azure Data Lake Analytics. Osadź kod R wbudowany lub odwołanie z plików.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: c5dd3f493e85afc925b639c142a293eed1e8cbd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672700"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Rozszerzanie skryptów U-SQL za pomocą kodu języka R w usłudze Azure Data Lake Analytics

W poniższym przykładzie przedstawiono podstawowe kroki wdrażania kodu języka R:
* Użyj `REFERENCE ASSEMBLY` instrukcji, aby włączyć rozszerzenia języka R dla skryptu U-SQL.
* Użyj `REDUCE` operacji do partycjonowania danych wejściowych na klucz.
* Rozszerzenia języka R dla języka U-SQL zawierają`Extension.R.Reducer`wbudowany reduktor ( ), który uruchamia kod Języka R na każdym wierzchołku przypisanym do reduktora. 
* Użycie dedykowanych nazwanych `inputFromUSQL` ramek danych wywoływanych i `outputToUSQL` odpowiednio do przekazywania danych między U-SQL i R. Nazwy identyfikatorów danych wejściowych i wyjściowych DataFrame są stałe (oznacza to, że użytkownicy nie mogą zmienić tych wstępnie zdefiniowanych nazw wejściowych i wyjściowych identyfikatorów DataFrame).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Osadzanie kodu R w skrypcie U-SQL

Za pomocą parametru polecenia `Extension.R.Reducer`programu . Na przykład można zadeklarować skrypt Języka R jako zmienną ciągu i przekazać go jako parametr do reduktora.


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

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Przechowuj kod R w osobnym pliku i odwołuj się do niego do skryptu U-SQL

Poniższy przykład ilustruje bardziej złożone użycie. W takim przypadku kod Języka R jest wdrażany jako zasób, który jest skryptem U-SQL.

Zapisz ten kod R jako osobny plik.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Użyj skryptu U-SQL, aby wdrożyć ten skrypt języka R za pomocą instrukcji DEPLOY RESOURCE.

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

## <a name="how-r-integrates-with-u-sql"></a>Jak R integruje się z U-SQL

### <a name="datatypes"></a>Typy danych
* Kolumny ciągów i liczb z języka U-SQL są konwertowane w stanie as-is `bool` `integer`między `byte`ramami dataframe a U-SQL [obsługiwane typy: `double`, `string`, , , , ].
* Typ `Factor` danych nie jest obsługiwany w języku U-SQL.
* `byte[]`musi być serializowany jako kodowany `string`base64 .
* Ciągi U-SQL można przekonwertować na czynniki w kodzie R, po utworzeniu formatu danych wejściowych języka R przez U-SQL lub ustawieniu parametru `stringsAsFactors: true`reduktora .

### <a name="schemas"></a>Schematy
* Zestawy danych U-SQL nie mogą mieć zduplikowanych nazw kolumn.
* Nazwy kolumn zestawów danych U-SQL muszą być ciągami znaków.
* Nazwy kolumn muszą być takie same w skryptach U-SQL i R.
* Kolumna readonly nie może być częścią wyjściowej ramki danych. Ponieważ kolumny tylko do odczytu są automatycznie wstrzykiwane z powrotem do tabeli U-SQL, jeśli jest to część schematu wyjściowego UDO.

### <a name="functional-limitations"></a>Ograniczenia funkcjonalne
* Nie można utworzyć wystąpienia aparatu R dwa razy w tym samym procesie. 
* Obecnie U-SQL nie obsługuje UDO combiner do przewidywania przy użyciu modeli podzielonych na partycje generowane przy użyciu reduktora UDOs. Użytkownicy mogą deklarować modele podzielone na partycje jako zasób i używać ich w skrypcie języka R (patrz przykładowy kod) `ExtR_PredictUsingLMRawStringReducer.usql`

### <a name="r-versions"></a>Wersje R
Obsługiwany jest tylko R 3.2.2.

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

### <a name="input-and-output-size-limitations"></a>Ograniczenia rozmiaru wejścia i wyjścia
Każdy wierzchołek ma ograniczoną ilość pamięci przypisane do niego. Ponieważ input i output DataFrames musi istnieć w pamięci w kodzie R, całkowity rozmiar danych wejściowych i wyjściowych nie może przekraczać 500 MB.

### <a name="sample-code"></a>Przykładowy kod
Więcej przykładowego kodu jest dostępnych na koncie magazynu Data Lake Store po zainstalowaniu rozszerzeń zaawansowanej analizy U-SQL. Ścieżka dla więcej przykładowego `<your_account_address>/usqlext/samples/R`kodu to: . 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Wdrażanie niestandardowych modułów języka R za pomocą języka U-SQL

Najpierw utwórz niestandardowy moduł R i skompresuj go, a następnie prześlij spakowany plik niestandardowego modułu R do sklepu ADL. W tym przykładzie przekażemy magittr_1.5.zip do katalogu głównego domyślnego konta ADLS dla używanego konta ADLA. Po przekazaniu modułu do magazynu ADL, zadeklarować go jako użyj deploy resource, aby udostępnić go w skrypcie U-SQL i wywołać `install.packages` go zainstalować.

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
* [Korzystanie z funkcji okna U-SQL dla zadań usługi Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
