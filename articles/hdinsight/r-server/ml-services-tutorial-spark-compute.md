---
title: 'Samouczek: Używanie funkcji R w kontekście obliczeniowym platformy Spark w usłudze Azure HDInsight'
description: Samouczek — wprowadzenie do języka R i Spark w klastrze usług Azure HDInsight Machine Learning.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 73ca0d089ab758fb13e69d341337139d79194cc5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71121935"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Samouczek: Używanie funkcji R w kontekście obliczeniowym platformy Spark w usłudze Azure HDInsight

Ten samouczek zawiera wprowadzenie krok po kroku do korzystania z funkcji języka R w programie Apache Spark, które są uruchamiane w klastrze usług azure HDInsight Machine Learning.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Pobieranie przykładowych danych do magazynu lokalnego
> * Kopiowanie danych do magazynu domyślnego
> * Konfigurowanie zestawu danych
> * Tworzenie źródeł danych
> * Tworzenie kontekstu obliczeniowego dla platformy Spark
> * Dopasowywał model liniowy
> * Używanie złożonych plików XDF
> * Konwertowanie XDF na CSV

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usług usługi Azure HDInsight Machine Learning. Przejdź do [tworzenia klastrów Apache Hadoop przy użyciu witryny Azure portal,](../hdinsight-hadoop-create-linux-clusters-portal.md) a w przypadku **typu klastra**wybierz pozycję **Usługi ML**.

## <a name="connect-to-rstudio-server"></a>Łączenie z programem RStudio Server

Serwer RStudio działa w węźle brzegowym klastra. Przejdź do następującej witryny (gdzie *CLUSTERNAME* w adresie URL jest nazwą utworzonego klastra usług usługi usługi HDInsight Machine Learning):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Przy pierwszym logacji uwierzytelniasz się dwukrotnie. Przy pierwszym wierszu uwierzytelniania podaj nazwę użytkownika i hasło administratora klastra (domyślnie jest *to administrator*). W drugim wierszu uwierzytelniania podaj nazwę użytkownika i hasło SSH (domyślnie *jest sshuser*). Kolejne logowania wymagają tylko poświadczeń SSH.

## <a name="download-the-sample-data-to-local-storage"></a>Pobieranie przykładowych danych do magazynu lokalnego

*Zestaw danych na czas linii lotniczej 2012* składa się z 12 plików oddzielonych przecinkami, które zawierają szczegóły przylotu i odlotu dla wszystkich lotów komercyjnych na terenie USA za rok 2012. Ten zestaw danych jest duży, z ponad 6 milionami obserwacji.

1. Zainicjować kilka zmiennych środowiskowych. W konsoli serwera RStudio wprowadź następujący kod:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. W prawym okienku wybierz kartę **Środowisko.** Zmienne są wyświetlane w obszarze **Wartości**.

    ![Konsola internetowa HDInsight R studio](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. Utwórz katalog lokalny i pobierz przykładowe dane. W RStudio wprowadź następujący kod:

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

    Pobieranie powinno zakończyć się w około 9,5 minuty.

## <a name="copy-the-data-to-default-storage"></a>Kopiowanie danych do magazynu domyślnego

Lokalizacja rozproszonego systemu plików (HDFS) hadoop `airDataDir` jest określona ze zmienną. W RStudio wprowadź następujący kod:

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

Krok powinien być zakończony w około 10 sekund.

## <a name="set-up-a-dataset"></a>Konfigurowanie zestawu danych

1. Utwórz obiekt systemu plików, który używa wartości domyślnych. W RStudio wprowadź następujący kod:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Ponieważ oryginalne pliki CSV mają dość nieporęczne nazwy zmiennych, podajesz listę *colInfo,* aby były łatwiejsze w zarządzaniu. W RStudio wprowadź następujący kod:

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

## <a name="create-data-sources"></a>Tworzenie źródeł danych

W kontekście obliczeń platformy Spark można tworzyć źródła danych przy użyciu następujących funkcji:

|Funkcja | Opis |
|---------|-------------|
|`RxTextData` | Źródło danych tekstowych rozdzielanych przecinkami. |
|`RxXdfData` | Dane w formacie pliku danych XDF. W RevoScaleR format pliku XDF jest modyfikowany dla Hadoop do przechowywania danych w złożonym zestawie plików, a nie w jednym pliku. |
|`RxHiveData` | Generuje obiekt źródło danych hive.|
|`RxParquetData` | Generuje obiekt Źródło danych parkietu.|
|`RxOrcData` | Generuje obiekt Źródło danych orków.|

Utwórz obiekt [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) przy użyciu plików skopiowanych do usługi HDFS. W RStudio wprowadź następujący kod:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Tworzenie kontekstu obliczeniowego dla platformy Spark

Aby załadować dane i uruchomić analizy w węzłach procesu roboczego, należy ustawić kontekst obliczeniowy w skrypcie na [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). W tym kontekście funkcje R automatycznie rozdzielają obciążenie we wszystkich węzłach procesu roboczego, bez wbudowanego wymagania dotyczącego zarządzania zadaniami lub kolejką. Kontekst obliczeń platformy Spark `RxSpark` jest `rxSparkConnect()` ustanawiany za pośrednictwem lub do `rxSparkDisconnect()` tworzenia kontekstu obliczeń Platformy Spark i używa do powrotu do lokalnego kontekstu obliczeniowego. W RStudio wprowadź następujący kod:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Dopasowywał model liniowy

1. Funkcja [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) umożliwia dopasowanie modelu liniowego `airDS` przy użyciu źródła danych. W RStudio wprowadź następujący kod:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Ten krok powinien zostać ukończony w ciągu 2 do 3 minut.

1. Przejrzyj wyniki. W RStudio wprowadź następujący kod:

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

    Wyniki wskazują, że wszystkie dane zostały przetworzone, 6 milionów obserwacji, przy użyciu wszystkich plików CSV w określonym katalogu. Ponieważ określono, `cube = TRUE`masz szacowany współczynnik dla każdego dnia tygodnia (a nie przecięcia).

## <a name="use-composite-xdf-files"></a>Używanie złożonych plików XDF

Jak już widziałeś, możesz analizować pliki CSV bezpośrednio za pomocą R na Hadoop. Ale można wykonać analizę szybciej, jeśli dane są przechowywane w bardziej wydajnym formacie. Format pliku R XDF jest wydajny, ale jest nieco zmodyfikowany dla hdfs, dzięki czemu poszczególne pliki pozostają w jednym bloku HDFS. (Rozmiar bloku HDFS różni się w zależności od instalacji, ale zazwyczaj wynosi 64 MB lub 128 MB). 

Korzystając z [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) na Hadoop do utworzenia zestawu złożonych `RxTextData` plików XDF, należy określić źródło danych, takie jak `AirDS` inData i źródło `RxXdfData` danych z fileSystem ustawiony na system plików HDFS jako argument outFile. Następnie można użyć `RxXdfData` obiektu jako argumentu danych w kolejnych analizach R.

1. Zdefiniuj `RxXdfData` obiekt. W RStudio wprowadź następujący kod:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Ustaw rozmiar bloku 250000 wierszy i określ, że czytamy wszystkie dane. W RStudio wprowadź następujący kod:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importuj `rxImport`dane za pomocą pliku . W RStudio wprowadź następujący kod:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Ten krok powinien zostać ukończony w ciągu kilku minut.

1. Ponownie oszacuj ten sam model liniowy przy użyciu nowego, szybszego źródła danych. W RStudio wprowadź następujący kod:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Krok powinien zostać ukończony w mniej niż minutę.

1. Przejrzyj wyniki. Wyniki powinny być takie same jak z plików CSV. W RStudio wprowadź następujący kod:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Konwertowanie XDF na CSV

### <a name="in-a-spark-context"></a>W kontekście iskry

Jeśli pliki CSV zostały przekonwertowane na format pliku XDF w celu zwiększenia wydajności podczas uruchamiania analiz, ale teraz chcesz przekonwertować dane z powrotem na plik CSV, możesz to zrobić za pomocą [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Aby utworzyć folder plików CSV, `RxTextData` należy najpierw utworzyć obiekt przy użyciu nazwy katalogu jako argumentu pliku. Ten obiekt reprezentuje folder, w którym mają być utworzone pliki CSV. Ten katalog jest tworzony po `rxDataStep`uruchomieniu pliku . Następnie wskaż `RxTextData` ten `outFile` obiekt w `rxDataStep`argumentie . Każdy utworzony plik CSV jest nazwany na podstawie nazwy katalogu, a następnie liczba.

Załóżmy, że chcesz zapisać folder plików CSV w `airDataXdf` pliku HDFS z kompozytowego XDF po wykonaniu regresji logistycznej i przewidywania, tak aby nowe pliki CSV zawierały przewidywane wartości i pozostałości. W RStudio wprowadź następujący kod:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Ten krok powinien zostać ukończony w około 2,5 minuty.

Wypisał `rxDataStep` jeden plik CSV dla każdego pliku XDFD w wejściowym złożonym pliku XDF. Jest to domyślne zachowanie podczas zapisywania plików CSV z złożonych plików XDF do systemu plików HDFS, gdy kontekst obliczeniowy jest ustawiony na `RxSpark`.

### <a name="in-a-local-context"></a>W kontekście lokalnym

Alternatywnie, po zakończeniu wykonywania analiz, można przełączyć kontekst obliczeniowy `local` z powrotem do `RxTextData` skorzystania z dwóch argumentów w tym daje nieco `createFileSet` `rowsPerOutFile`większą kontrolę podczas zapisywania plików CSV do hdfs: i . Po `createFileSet` ustawieniu `TRUE`folderu plików CSV jest zapisywany w określonym katalogu. Po `createFileSet` ustawieniu `FALSE`jest zapisywany pojedynczy plik CSV. Można ustawić drugi argument `rowsPerOutFile`, na liczbę całkowitą, aby wskazać, ile wierszy `createFileSet` zapisać `TRUE`do każdego pliku CSV, gdy jest .

W RStudio wprowadź następujący kod:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Ten krok powinien zostać ukończony w około 10 minut.

W przypadku `RxSpark` korzystania z `createFileSet` kontekstu `TRUE` obliczeniowego, domyślnie i `rowsPerOutFile` nie ma wpływu. W związku z tym jeśli chcesz utworzyć pojedynczy plik CSV lub dostosować `rxDataStep` liczbę `local` wierszy na plik, wykonaj w kontekście obliczeniowym (dane mogą nadal znajdować się w systemie PLIKÓW HDFS).

## <a name="final-steps"></a>Ostatnie kroki

1. Wyczyść dane. W RStudio wprowadź następujący kod:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Zatrzymaj zdalną aplikację Spark. W RStudio wprowadź następujący kod:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Zamknij sesję R. W RStudio wprowadź następujący kod:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu samouczka można usunąć klaster. Dzięki funkcji HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Naliczana jest również opłata za klaster HDInsight, nawet jeśli nie jest używana. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ma ekonomiczny sens usuwanie klastrów, gdy nie są używane.

Aby usunąć klaster, zobacz [Usuwanie klastra HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

In this tutorial, you learned how to use R functions in Apache Spark that are running on an HDInsight Machine Learning services cluster. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Opcje kontekstu obliczania klastra usług Azure HDInsight Machine Learning](r-server-compute-contexts.md)
* [R Funkcje iskry na Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
