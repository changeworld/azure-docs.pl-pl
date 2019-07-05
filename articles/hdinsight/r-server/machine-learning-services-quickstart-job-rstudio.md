---
title: 'Szybki start: Wykonywanie skryptu języka R w klastrze usługi ML w usłudze Azure HDInsight przy użyciu programu RStudio Server'
description: W przewodniku Szybki Start można wykonać skryptu języka R w klastrze usługi ML w usłudze Azure HDInsight przy użyciu programu RStudio Server.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 484763adfa154dcdf226b03f1f591d248532ee35
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450919"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-rstudio-server"></a>Szybki start: Wykonywanie skryptu języka R w klastrze usługi ML w usłudze Azure HDInsight przy użyciu programu RStudio Server

Usługi ML w usłudze Azure HDInsight pozwala skryptom języka R używać platformy Apache Spark i Apache Hadoop MapReduce do wykonywania obliczeń rozproszonych. Usługi ML Określa, jak wywołania są wykonywane przez ustawienie kontekstu obliczeniowego. Węzeł krawędzi klastra zapewnia wygodne miejsce do łączenia z klastrem i do uruchamiania skryptów języka R. Z węzłem krawędzi istnieje możliwość uruchamiania równoległego rozproszonego funkcje kolekcję funkcji RevoScaleR między rdzeniami serwera węzła krawędzi. Można również uruchomić je w węzłach klastra za pomocą jego RevoScaleR Hadoop Mapreduce lub konteksty wystąpień obliczeniowych platformy Apache Spark.

W tym przewodniku Szybki Start dowiesz się, jak uruchomić skrypt języka R z programu RStudio Server, który demonstruje sposób użycia platformy Spark dla rozproszone obliczenia R. Określi kontekst obliczeniowy do wykonywania obliczeń lokalnie w węźle krawędzi, a następnie ponownie rozłożona na węzły w klastrze HDInsight.

## <a name="prerequisite"></a>Wymagania wstępne

Klaster usługi ML HDInsight. Zobacz [tworzenie technologii Apache Hadoop clusters, przy użyciu witryny Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz **usługi ML** dla **typ klastra**.

## <a name="connect-to-rstudio-server"></a>Łączenie z programem RStudio Server

RStudio Server działa w węźle krawędzi klastra. Przejdź do następującego adresu URL gdzie `CLUSTERNAME` to nazwa utworzonego klastra usługi ML:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Podczas pierwszego logowania należy dwukrotnego uwierzytelnienia. Dla pierwszego monitu dotyczącego uwierzytelniania Podaj identyfikator logowania administratora klastra i hasło, wartością domyślną jest `admin`. Dla drugiego monitu dotyczącego uwierzytelniania Podaj logowania SSH i hasło, wartością domyślną jest `sshuser`. Kolejne logowania wymagane tylko poświadczenia protokołu SSH.

Po nawiązaniu połączenia ekran powinien przypominać następujący zrzut ekranu:

![Podstawowe szczegóły klastra](./media/ml-services-quickstart-job-rstudio/connect-to-r-studio.png)

## <a name="use-a-compute-context"></a>Używanie kontekstu obliczeniowego

1. Z programu RStudio Server Użyj poniższego kodu, aby załadować przykładowe dane do domyślnego magazynu dla HDInsight:

    ```RStudio
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    Ten krok może zająć około 8 minut.

1. Utwórz trochę informacji o danych i zdefiniujmy dwa źródła danych. Wprowadź następujący kod w RStudio:

    ```RStudio
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. Uruchom regresję logistyczną na danych przy użyciu **lokalnego** kontekstu obliczeniowego. Wprowadź następujący kod w RStudio:

    ```RStudio
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    Obliczenia powinno zająć około 7 minut. Powinny pojawić się dane wyjściowe kończące się wierszami podobnymi do następującego fragmentu kodu:

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Uruchom ten sam przy użyciu regresji logistycznej **Spark** kontekstu. Dzięki kontekstowi aparatu Spark przetwarzanie jest dystrybuowane do wszystkich węzłów procesu roboczego w klastrze usługi HDInsight. Wprowadź następujący kod w RStudio:

    ```RStudio
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    Obliczenia powinno zająć około 5 minut.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego przewodnika Szybki Start możesz usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usunąć klaster usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start przedstawiono sposób uruchamiania skryptu R, za pomocą programu RStudio Server, który pokazano przy użyciu platformy Spark dla rozproszone obliczenia R.  Przejdź do następnego artykułu, aby dowiedzieć się, opcje, które są dostępne określić, czy i w jaki sposób wykonywania jest zrównoleglona na rdzeni węzła krawędzi lub klastra HDInsight.

> [!div class="nextstepaction"]
>[COMPUTE context options obliczeniowego usługi ML w HDInsight](./r-server-compute-contexts.md)