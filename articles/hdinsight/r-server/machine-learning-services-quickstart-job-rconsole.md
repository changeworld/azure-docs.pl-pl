---
title: 'Szybki start: skrypt języka R w konsoli usługi ML & R — Usługa Azure HDInsight'
description: W przewodniku Szybki start można wykonać skrypt języka R w klastrze usług ML w usłudze Azure HDInsight przy użyciu konsoli języka R.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: af41a7569dedc9a56f67be8ae791d7185e0c7489
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73241604"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-r-console"></a>Szybki start: wykonywanie skryptu języka R w klastrze usług ML w usłudze Azure HDInsight przy użyciu konsoli języka R

Usługi ML w usłudze Azure HDInsight umożliwiają skryptom języka R używanie programów Apache Spark i Apache Hadoop MapReduce do uruchamiania obliczeń rozproszonych. Usługi ML steruje sposób wykonywania wywołań przez ustawienie kontekstu obliczeniowego. Węzeł brzegowy klastra zapewnia wygodne miejsce do łączenia się z klastrem i uruchamiania skryptów języka R. W węźle brzegowym można uruchamiać równoległe funkcje rozproszone RevoScaleR na rdzeniach serwera węzła brzegowego. Można również uruchomić je w węzłach klastra przy użyciu RevoScaleR's Hadoop Map Reduce lub Apache Spark obliczeń kontekstów.

W tym przewodniku Szybki start dowiesz się, jak uruchomić skrypt języka R za pomocą konsoli języka R, która pokazuje, że używa platformy Spark do rozproszonych obliczeń R. Zdefiniujesz kontekst obliczeniowy do wykonywania obliczeń lokalnie w węźle brzegowym i ponownie rozłożone między węzłami w klastrze HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usług ML w programie HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz pozycję Usługi **ML** dla **typu klastra**.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="connect-to-r-console"></a>Łączenie się z konsolą R

1. Połącz się z węzłem brzegowym klastra HDInsight usług ML za pomocą funkcji SSH. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

1. W sesji SSH wpisz następujące polecenie, aby uruchomić konsolę R:

    ```
    R
    ```

    Oprócz innych informacji powinny pojawić się dane wyjściowe z wersją oprogramowania ML Server.


## <a name="use-a-compute-context"></a>Używanie kontekstu obliczeniowego

1. W monicie `>` możesz podać kod R. Użyj następującego kodu, aby załadować przykładowe dane do domyślnego magazynu dla usługi HDInsight:

    ```R
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

    Ten krok może potrwać około 10 minut.

1. Utwórz informacje o danych i zdefiniuj dwa źródła danych. Wprowadź następujący kod w konsoli R:

    ```R
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

1. Uruchom regresję logistyczną nad danymi przy użyciu **lokalnego** kontekstu obliczeniowego. Wprowadź następujący kod w konsoli R:

    ```R
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    Obliczenia powinny zakończyć się w około 7 minut. Powinno być widoczne dane wyjściowe, które kończą się liniami podobnymi do następującego fragmentu:

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

1. Uruchom tę samą regresję logistyczną przy użyciu kontekstu **Platformy Spark.** Dzięki kontekstowi aparatu Spark przetwarzanie jest dystrybuowane do wszystkich węzłów procesu roboczego w klastrze usługi HDInsight. Wprowadź następujący kod w konsoli R:

    ```R
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

    Obliczenia powinny zakończyć się w około 5 minut.

1. Aby zamknąć konsolę języka R, użyj następującego polecenia:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu szybkiego startu można usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [Usuwanie klastra HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano, jak uruchomić skrypt języka R za pomocą konsoli języka R, która została zademonstrowana przy użyciu platformy Spark dla rozproszonych obliczeń R.  Przejdź do następnego artykułu, aby dowiedzieć się opcje, które są dostępne, aby określić, czy i jak wykonanie jest równoległe między rdzeniami węzła krawędzi lub klastra HDInsight.

> [!div class="nextstepaction"]
>[Opcje kontekstu obliczania usług ML w programie HDInsight](./r-server-compute-contexts.md)