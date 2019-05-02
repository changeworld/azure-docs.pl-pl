---
title: Zarządzanie klastrem usługi ML w HDInsight — Azure
description: Dowiedz się, jak Zarządzanie klastrem usługi ML w usłudze Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 607f85c10183366e88d597d84090f49fc30aff48
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687980"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Zarządzanie klastrem usługi ML w usłudze Azure HDInsight

W tym artykule dowiesz się, jak zarządzać istniejącego klastra usługi ML w usłudze Azure HDInsight do wykonywania zadań takich jak dodawanie wielu równoczesnych użytkowników, zdalnego łączenia się z klastrem usługi uczenie Maszynowe, zmiana kontekstu obliczeniowego itp.

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster usługi ML w HDInsight**: Aby uzyskać instrukcje, zobacz [Rozpocznij pracę z usługami uczenia Maszynowego na HDInsight](r-server-get-started.md).

* **Klient protokołu Secure Shell (SSH)**: Klient SSH jest używany do zdalnego łączenia z klastrem HDInsight i uruchamianie poleceń bezpośrednio w klastrze. Aby uzyskać więcej informacji, zobacz [użycia protokołu SSH w usłudze HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Włączanie obsługi równoczesnych użytkowników

Można włączyć dla klastra usługi ML na HDInsight jednoczesną pracę wielu użytkowników, dodając użytkowników do węzła krawędzi, na którym jest uruchamiany z programu rstudio community. Podczas tworzenia klastra usługi HDInsight musisz podać dwóch użytkowników: użytkownika HTTP i użytkownika SSH:

![Równoczesny użytkownik 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Nazwa użytkownika logowania klastra**: użytkownik HTTP uwierzytelniany za pośrednictwem bramy HDInsight, która umożliwia ochronę utworzonych klastrów usługi HDInsight. Ten użytkownik HTTP umożliwia dostęp do interfejsu użytkownika programu Apache Ambari, Apache Hadoop YARN w interfejsie użytkownika, a także innych składników interfejsu użytkownika.
- **Nazwa użytkownika protokołu SSH (Secure Shell)**: użytkownik SSH zapewniający dostęp do klastra za pośrednictwem protokołu Secure Shell. Jest to użytkownik systemu Linux, który ma dostęp do wszystkich węzłów głównych, węzłów procesu roboczego oraz węzłów krawędzi. Pozwala to na korzystanie z dowolnego węzła klastra zdalnego za pomocą protokołu Secure Shell.

Wersję R Studio Server Community używaną w klastrze usługi ML w HDInsight są akceptowane tylko nazwa użytkownika systemu Linux i hasło logowania w mechanizmie. Przekazywanie tokenów nie jest obsługiwane. Dlatego podczas próby dostępu do oprogramowania R Studio po raz pierwszy w klastrze usługi uczenie Maszynowe, należy zalogować się dwukrotnie.

- Najpierw zaloguj się przy użyciu poświadczeń użytkownika HTTP za pośrednictwem bramy HDInsight. 

- Następnie użyj poświadczeń użytkownika SSH do logowania do programu RStudio.
  
Aktualnie podczas aprowizowania klastra usługi HDInsight można utworzyć tylko jedno konto użytkownika SSH. Dlatego aby umożliwić wielu użytkownikom dostęp do klastra usługi ML w HDInsight, należy utworzyć dodatkowych użytkowników w systemie Linux.

Ponieważ program RStudio działa w węźle krawędzi klastra, wymagane jest kilka czynności:

1. Użyj istniejącego konta użytkownika SSH do logowania się na węźle krawędzi
2. Dodaj użytkowników systemu Linux w węźle krawędzi
3. Przy pomocy utworzonego użytkownika możesz korzystać z programu RStudio Community

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Krok 1: Zaloguj się do węzła krawędzi przy użyciu utworzonego użytkownika SSH

Postępuj zgodnie z instrukcjami w artykule [nawiązywanie połączenia z HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) dostępu do węzła krawędzi. Adres węzła krawędzi klastra usługi ML w HDInsight jest `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Krok 2: Dodaj użytkowników systemu Linux w węźle krawędzi

Aby dodać użytkownika do węzła krawędzi, uruchom te polecenia:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

Poniższy zrzut ekranu przedstawia dane wyjściowe.

![Równoczesny użytkownik 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

Po wyświetleniu monitu o "bieżącego hasła protokołu Kerberos:", po prostu naciśnij **Enter** można go zignorować. Podanie opcji `-m` w poleceniu `useradd` powoduje, że system utworzy folder macierzysty użytkownika, wymagany przez program RStudio Community.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Krok 3: Przy pomocy utworzonego użytkownika możesz korzystać z programu RStudio Community

Dostęp do programu RStudio z https://CLUSTERNAME.azurehdinsight.net/rstudio/. Jeśli logujesz się po raz pierwszy po utworzeniu klastra, wprowadź poświadczenia administratora klastra następuje poświadczeń użytkownika SSH, który został utworzony. Jeśli nie jest to pierwsze logowanie tylko wprowadź poświadczenia dla użytkownika SSH, który został utworzony.

Możesz również zalogować się przy użyciu oryginalnych poświadczeń (domyślnie jest *sshuser*) jednocześnie w innym oknie przeglądarki.

Pamiętaj, że nowo dodani użytkownicy nie mają uprawnień użytkownika root w systemie Linux, ale mają takie same prawa dostępu do wszystkich plików w magazynie zdalnym HDFS i WASB.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Zdalne ustanawianie połączenia z usługami uczenia Maszynowego firmy Microsoft

Możesz skonfigurować dostęp do kontekstu obliczeniowego aparatu HDInsight Spark ze zdalnego wystąpienia klienta ML uruchomiony na pulpicie. Aby to zrobić, należy określić opcje (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches i sshProfileScript) podczas definiowania programu RxSpark kontekstu obliczeniowego na komputerze: Na przykład:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

Aby uzyskać więcej informacji, zobacz sekcję "Przy użyciu klienta programu Microsoft Machine Learning Server jako Apache Hadoop" w [sposób użycia kolekcję funkcji RevoScaleR w kontekście obliczeniowym aparatu Apache Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Używanie kontekstu obliczeniowego

Kontekst obliczeniowy pozwala określić, czy obliczenia są wykonywane lokalnie w węźle krawędzi czy są rozproszone w węzłach klastra usługi HDInsight.

1. W programie RStudio Server lub konsoli R (w ramach sesji SSH) załaduj przykładowe dane do domyślnego magazynu usługi HDInsight za pomocą następującego kodu:

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
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

2. Następnie utwórz trochę informacji o danych i zdefiniujmy dwa źródła danych.

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

3. Uruchom regresję logistyczną na danych za pomocą lokalnego kontekstu obliczeniowego.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Powinny pojawić się dane wyjściowe kończące się wierszami podobnymi do następującego fragmentu kodu:

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

4. Uruchom tę samą regresję logistyczną za pomocą kontekstu aparatu Spark. Dzięki kontekstowi aparatu Spark przetwarzanie jest dystrybuowane do wszystkich węzłów procesu roboczego w klastrze usługi HDInsight.

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


## <a name="distribute-r-code-to-multiple-nodes"></a>Dystrybucja kodu R do wielu węzłów

Za pomocą usługi ML w HDInsight, można uruchomić istniejący kod R i uruchom go w wielu węzłach w klastrze za pomocą `rxExec`. Funkcja ta jest przydatna podczas czyszczenia parametrów lub przeprowadzania symulacji. Poniższy kod przedstawia przykładowe użycie programu `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Jeśli nadal używasz kontekstu Spark, to polecenie spowoduje zwrócenie wartości nodename dla węzłów procesu roboczego, kod `(Sys.info()["nodename"])` jest uruchamiany na. Na przykład w klastrze czterech węzłów spodziewasz się pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>Dostęp do danych w Apache Hive i Parquet

Usługi ML HDInsight umożliwia bezpośredni dostęp do danych w usługach Hive i Parquet w celu użycia w funkcjach programu ScaleR w kontekście obliczeniowym aparatu Spark. Te możliwości są dostępne za pomocą nowych funkcji źródła danych programu ScaleR o nazwie RxHiveData i RxParquetData, które używają kodu Spark SQL do ładowania danych bezpośrednio do elementów DataFrame aparatu Spark na potrzeby analizy przez program ScaleR.

Poniżej przedstawiono przykładowy kod korzystający z nowych funkcji:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Aby uzyskać dodatkowe informacje na temat tych nowych funkcji, zobacz w Pomocy online usługi ML za pośrednictwem `?RxHivedata` i `?RxParquetData` poleceń.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Zainstaluj dodatkowe pakiety R na klaster

### <a name="to-install-r-packages-on-the-edge-node"></a>Aby zainstalować pakiety R na węźle krawędzi

Jeśli chcesz zainstalować dodatkowe pakiety R na węźle krawędzi, możesz użyć `install.packages()` bezpośrednio z poziomu konsoli języka R po połączeniu z węzłem krawędzi za pośrednictwem protokołu SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Aby zainstalować pakiety R na węźle procesu roboczego

Aby zainstalować pakiety R na węzłach procesu roboczego klastra, należy użyć akcji skryptu. Akcje skryptu to skrypty powłoki Bash używane do wprowadzania zmian w konfiguracji klastra usługi HDInsight lub instalowania dodatkowego oprogramowania, np. pakietów R. 

> [!IMPORTANT]  
> Dodatkowe pakiety R można zainstalować przy użyciu akcji skryptu dopiero po utworzeniu klastra. Nie należy używać tej procedury podczas tworzenia klastra, ponieważ skrypt zależy od usługi ML są w pełni skonfigurowany.

1. Wykonaj kroki opisane w temacie [Dostosowywanie klastrów za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

3. Aby uzyskać **Prześlij akcję skryptu**, podaj następujące informacje:

   * Aby uzyskać **typ skryptu**, wybierz opcję **niestandardowe**.

   * Aby uzyskać **nazwa**, podaj nazwę dla akcji skryptu.

     * Aby uzyskać **identyfikator URI skryptu powłoki systemowej**, wprowadź `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Jest to skrypt, który instaluje dodatkowe pakiety R na węźle procesu roboczego

   * Zaznacz pole wyboru, tylko w przypadku **procesu roboczego**.

   * **Parametry**: Pakiety języka R do zainstalowania. Na przykład: `bitops stringr arules`

   * Zaznacz pole wyboru, aby **Utrwal tę akcję skryptu**.  

   > [!NOTE]
   > 1. Domyślnie wszystkie pakiety R są instalowane z migawki repozytorium Microsoft MRAN zgodne z wersją ML Server, który został zainstalowany. Jeśli chcesz zainstalować nowsze wersje pakietów, musisz uwzględnić pewne ryzyko niezgodności. Jednak możesz to zrobić za pomocą parametru `useCRAN` użytego jako pierwszy element listy pakietów, na przykład `useCRAN bitops, stringr, arules`.  
   > 2. Niektóre pakiety R wymagają dodatkowych bibliotek systemu Linux. Dla wygody usługi ML HDInsight zawiera wstępnie zainstalowane zależności wymagane przez górny 100 najpopularniejszych pakietów r. Jednak jeśli instalowane pakiety R wymagają jeszcze innych bibliotek, musisz pobrać skrypt podstawowy użyty tutaj i dodać kroki instalowania bibliotek systemowych. Następnie musisz przekazać zmodyfikowany skrypt do publicznego kontenera obiektów blob w usłudze Azure Storage i użyć zmodyfikowanego skryptu do zainstalowania pakietów.
   >    Aby uzyskać informacje na temat tworzenia akcji skryptu, zobacz [Script Action development](../hdinsight-hadoop-script-actions-linux.md) (Tworzenie akcji skryptu).  
   >
   >

   ![Dodawanie akcji skryptu](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Wybierz polecenie **Utwórz**, aby uruchomić skrypt. Po zakończeniu działania skryptu pakiety R będą dostępne we wszystkich węzłach procesu roboczego.

## <a name="next-steps"></a>Kolejne kroki

* [Operationalize ML Services cluster on HDInsight (Obsługa operacji klastra usług ML w usłudze HDInsight)](r-server-operationalize.md)
* [COMPUTE context options obliczeniowego usługi ML klastra na HDInsight](r-server-compute-contexts.md)
* [Azure Storage options for ML Services cluster on HDInsight (Opcje usługi Azure Storage dla klastra usług ML w usłudze HDInsight)](r-server-storage.md)
