---
title: 'Samouczek: Użyj języka R w platformie Spark obliczenia kontekstu w usłudze Azure HDInsight'
description: Samouczek — Wprowadzenie do języka R i Spark w klastrze usługi Azure HDInsight Machine Learning.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: f072b6905881da7b7854b0e51d690dbbd40dffb5
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227440"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Samouczek: Użyj języka R w platformie Spark obliczenia kontekstu w usłudze Azure HDInsight

Ten samouczek zawiera instrukcje krok po kroku wprowadzenie do korzystania z funkcji języka R w Apache Spark, działających w klastrze usługi Azure HDInsight Machine Learning.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Pobierz przykładowe dane do magazynu lokalnego
> * Kopiuj dane do magazynu domyślnego
> * Konfigurowanie zestawu danych
> * Tworzenie źródła danych
> * Tworzenie kontekstu obliczeniowego dla aparatu Spark
> * Dopasuj modelu liniowego
> * Użyj plików XDF złożone
> * Konwertuj XDF do formatu CSV

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi Azure HDInsight Machine Learning. Przejdź do [tworzenie technologii Apache Hadoop clusters przy użyciu witryny Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) a w przypadku **typ klastra**, wybierz opcję **usługi ML**.

## <a name="connect-to-rstudio-server"></a>Łączenie z programem RStudio Server

RStudio Server działa w węźle krawędzi klastra. Przejdź do następującej witryny (gdzie *CLUSTERNAME* w adresie URL to nazwa utworzonego klastra usługi HDInsight Machine Learning):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Podczas pierwszego logowania użytkownik dwukrotnego uwierzytelnienia. Podczas pierwszego monitu dotyczącego uwierzytelniania Podaj nazwę użytkownika administratora klastra i hasło (wartość domyślna to *administratora*). Podczas drugiego monitu dotyczącego uwierzytelniania Podaj nazwę użytkownika SSH i hasło (wartość domyślna to *sshuser*). Kolejne logowania wymagają tylko poświadczenia protokołu SSH.

## <a name="download-the-sample-data-to-local-storage"></a>Pobierz przykładowe dane do magazynu lokalnego

*Zestawu danych w czasie 2012 linii lotniczych* składa się z 12 rozdzielonych przecinkami plików, które zawierają szczegóły wejściu i wyjściu lotu dla wszystkich komercyjnych lotów na terenie Stanów Zjednoczonych w roku 2012. Ten zestaw danych jest duży, za pomocą ponad 6 mln uwagi.

1. Zainicjuj kilku zmiennych środowiskowych. W konsoli programu RStudio Server wprowadź następujący kod:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. W okienku po prawej stronie wybierz **środowiska** kartę. Zmienne są wyświetlane w obszarze **wartości**.

    ![Programu RStudio](./media/ml-services-tutorial-spark-compute/rstudio.png)

1.  Tworzenie katalogu lokalnego i Pobierz przykładowe dane. W programu RStudio wprowadź następujący kod:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    Pobieranie powinna być ukończone w ciągu okołu 9.5 minut.

## <a name="copy-the-data-to-default-storage"></a>Kopiuj dane do magazynu domyślnego

Lokalizacja pliku System (HDFS, Hadoop Distributed) jest określona za pomocą `airDataDir` zmiennej. W programu RStudio wprowadź następujący kod:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

Ten krok powinien być ukończone w ciągu około 10 sekund.

## <a name="set-up-a-dataset"></a>Konfigurowanie zestawu danych

1. Tworzenie obiektu systemu plików, który używa domyślnych wartości. W programu RStudio wprowadź następujący kod:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Ponieważ oryginalne pliki CSV zamiast niewygodna nazwy zmiennych, podaj *colInfo* listy, aby były one łatwiejsze w zarządzaniu. W programu RStudio wprowadź następujący kod:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Tworzenie źródła danych

W kontekście obliczeniowym aparatu Spark możesz tworzyć źródła danych za pomocą następujących funkcji:

|Funkcja | Opis |
|---------|-------------|
|`RxTextData` | Tekst rozdzielany przecinkami źródła danych. |
|`RxXdfData` | Dane w formacie pliku danych XDF. W kolekcję funkcji RevoScaleR format plików XDF jest modyfikowany dla platformy Hadoop do przechowywania danych w złożonego zestawu plików, a nie pojedynczego pliku. |
|`RxHiveData` | Generuje obiekt źródła danych programu Hive.|
|`RxParquetData` | Generuje obiekt Parquet źródła danych.|
|`RxOrcData` | Generuje obiekt źródła danych Orc.|

Tworzenie [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) obiektu przy użyciu plików zostało skopiowane do systemu plików HDFS. W programu RStudio wprowadź następujący kod:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Tworzenie kontekstu obliczeniowego dla aparatu Spark

Aby załadować dane i uruchamianie analiz na węzłach procesu roboczego, ustawić kontekstu obliczeniowego w skrypcie [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). W tym kontekście funkcji języka R automatycznie rozkłada obciążenie na wszystkich węzłach procesu roboczego, za pomocą wbudowanych wymagane do zarządzania zadaniami lub kolejki. Kontekstu obliczeniowego aparatu Spark zostanie nawiązane za pośrednictwem `RxSpark` lub `rxSparkConnect()` tworzenie kontekstu obliczeniowego aparatu Spark, przy czym `rxSparkDisconnect()` aby powrócić do lokalnym kontekście obliczeniowym. W programu RStudio wprowadź następujący kod:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Dopasuj modelu liniowego

1. Użyj [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) dopasowania modelu liniowego, w którym używana jest funkcja swoje `airDS` źródła danych. W programu RStudio wprowadź następujący kod:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Ten krok powinien być ukończone w ciągu 2 – 3 minut.

1. Przejrzyj wyniki. W programu RStudio wprowadź następujący kod:

    ```R
    summary(delayArr)
    ```

    Powinny zostać wyświetlone następujące wyniki:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    Wyniki wskazują, że zostało przetworzone wszystkie dane, obserwacje 6 mln, korzystanie z plików CSV w określonym katalogu. Ponieważ określone `cube = TRUE`, mieć szacowany współczynnik za każdy dzień tygodnia (i nie intercept).

## <a name="use-composite-xdf-files"></a>Użyj plików XDF złożone

Jak wiesz, można analizować pliki CSV bezpośrednio przy użyciu języka R w usłudze Hadoop. Jednak można wykonać analizy szybciej, gdy dane są przechowywane w postaci bardziej wydajne. Format pliku XDF języka R to wydajne, ale jej zmodyfikował nieco dla systemu plików HDFS, aby poszczególne pliki pozostają w jednym bloku systemu plików HDFS. (Rozmiar bloku systemu plików HDFS różni się od instalacji instalacji ale jest zwykle 64 MB lub 128 MB). 

Kiedy używasz [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) na platformie Hadoop, aby utworzyć zestaw plików XDF złożonego, należy określić `RxTextData` źródła danych, takich jak `AirDS` jako inData i `RxXdfData` źródła danych za pomocą systemu plików jest ustawiony na system plików HDFS jako outFile argumentu. Następnie można użyć `RxXdfData` obiekt jako argumentu danych w kolejnych analiz R.

1. Zdefiniuj `RxXdfData` obiektu. W programu RStudio wprowadź następujący kod:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Ustaw rozmiar bloku rozmiar 250 000 wierszy, a następnie określ, czy możemy odczytanie wszystkich danych. W programu RStudio wprowadź następujący kod:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importowanie danych przy użyciu `rxImport`. W programu RStudio wprowadź następujący kod:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Ten krok powinien być ukończone w ciągu kilku minut.

1. Ponowne szacowanie tego samego modelu liniowego, przy użyciu źródła danych nowych, szybszych. W programu RStudio wprowadź następujący kod:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Ten krok powinien być kompletny w mniej niż minutę.

1. Przejrzyj wyniki. Wyniki powinny być takie same, od plików CSV. W programu RStudio wprowadź następujący kod:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Konwertuj XDF do formatu CSV

### <a name="in-a-spark-context"></a>W kontekście usługi Spark

Przekonwertowanie własnych plików CSV na format plików XDF większą wydajność podczas uruchamiania analizy, ale teraz chcesz przekonwertować swoje dane CSV, możesz to zrobić za pomocą [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Aby utworzyć folder z plikami CSV, należy najpierw utworzyć `RxTextData` , używając nazwy katalogu jako argument pliku obiektu. Ten obiekt reprezentuje folder, w której chcesz utworzyć pliki CSV. Ten katalog jest tworzony po uruchomieniu `rxDataStep`. Następnie wskaż polecenie to `RxTextData` obiektu `outFile` argument `rxDataStep`. Każdy wolumin, który jest tworzony nosi nazwę na podstawie nazwy katalogu i następuje numer.

Załóżmy, że chcesz zapisać folder z plikami CSV w systemie plików HDFS z Twojej `airDataXdf` złożonego XDF po wykonaniu regresji logistycznej i prognozowania, tak aby nowe pliki CSV zawierają przewidywane wartości oraz reszt. W programu RStudio wprowadź następujący kod:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Ten krok powinien być ukończone w ciągu około 2,5 minut.

`rxDataStep` Napisał się jeden plik CSV dla każdego pliku XDFD w pliku wejściowym XDF złożonego. Jest to domyślne zachowanie dla zapisywania plików CSV z plików XDF złożonego systemu plików HDFS gdy ustawiono kontekstu obliczeniowego `RxSpark`.

### <a name="in-a-local-context"></a>W kontekście lokalnego

Alternatywnie, po zakończeniu wykonywania usługi analiz, można przełączyć kontekst obliczeń do `local` może korzystać z dwóch argumentów w ramach `RxTextData` które podają nieco większą kontrolę podczas zapisywania plików CSV do systemu plików HDFS: `createFileSet` i `rowsPerOutFile`. Po ustawieniu `createFileSet` do `TRUE`, folder z plikami CSV są zapisywane w katalogu, który określisz. Po ustawieniu `createFileSet` do `FALSE`, są zapisywane w pojedynczym pliku CSV. Możesz ustawić drugi argument `rowsPerOutFile`, na liczbę całkowitą, aby wskazać, ile wierszy chcesz zapisać każdy wolumin pliku, kiedy `createFileSet` jest `TRUE`.

W programu RStudio wprowadź następujący kod:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Ten krok powinien być ukończone w ciągu około 10 minut.

Kiedy używasz `RxSpark` kontekstu, obliczeniowego `createFileSet` wartość domyślna to `TRUE` i `rowsPerOutFile` nie ma wpływu. W związku z tym, jeśli chcesz utworzyć jeden wolumin CSV lub dostosować liczbę wierszy w pliku, należy wykonać `rxDataStep` w `local` kontekstu obliczeniowego (dane mogą być nadal w systemie plików HDFS).

## <a name="final-steps"></a>Ostatnie kroki

1. Czyszczenie danych. W programu RStudio wprowadź następujący kod:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Zatrzymaj zdalnej aplikacji aparatu Spark. W programu RStudio wprowadź następujący kod:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Zamknij sesję R. W programu RStudio wprowadź następujący kod:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego samouczka warto usunąć klaster. Za pomocą HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Możesz są również naliczane opłaty za klaster usługi HDInsight nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, zapewnia ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usunąć klaster usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia funkcji języka R w Apache Spark, które są uruchomione w klastrze usługi HDInsight Machine Learning. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [COMPUTE context options obliczeniowego dla klastra usługi Azure HDInsight Machine Learning](r-server-compute-contexts.md)
* [Funkcje języka R dla platformy Spark w usłudze Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
