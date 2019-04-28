---
title: Wprowadzenie do usług ML w usłudze HDInsight — Azure
description: Dowiedz się, jak utworzyć aparat Apache Spark w klastrze usługi HDInsight zawierającym usługi ML, a następnie przesłać skrypt języka R do klastra.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 465b53e1c5f56c5c05c860ebd69a825141f7e703
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124615"
---
# <a name="get-started-with-ml-services-on-azure-hdinsight"></a>Wprowadzenie do usług ML w usłudze Azure HDInsight

Usługa Azure HDInsight umożliwia tworzenie klastrów usług ML. Opcja ta pozwala skryptom języka R używać [platformy Apache Spark](https://spark.apache.org/) i [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) do wykonywania obliczeń rozproszonych. W tym artykule przedstawiono procedurę tworzenia klastra usług ML w usłudze HDInsight, a następnie uruchamiania skryptu R, który demonstruje sposób użycia aparatu Spark na potrzeby wykonywania rozproszonych obliczeń przez kod R.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: Przed rozpoczęciem tego samouczka musisz dysponować subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Get Microsoft Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Uzyskaj bezpłatną wersję próbną platformy Azure).
* **Klient protokołu Secure Shell (SSH)**: Klient SSH jest używany do zdalnego łączenia z klastrem HDInsight i uruchamianie poleceń bezpośrednio w klastrze. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Tworzenie klastra przy użyciu witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do **+ Utwórz zasób** > **Analytics** > **HDInsight**.

3. W bloku **Podstawowe** wprowadź następujące informacje:

    * **Nazwa klastra**: Nazwa klastra HDInsight.
    * **Subskrypcja**: Wybierz subskrypcję do użycia.
    * **Nazwa użytkownika logowania klastra** i **hasło logowania klastra**: Zaloguj się przy uzyskiwaniu dostępu do klastra za pośrednictwem protokołu HTTPS. Te poświadczenia umożliwiają uzyskiwanie dostępu do usług, takich jak Apache Ambari Web UI lub interfejsu API REST.
    * **Secure Shell (SSH), username**: Nazwa logowania używana podczas uzyskiwania dostępu do klastra za pośrednictwem protokołu SSH. Domyślnie hasło jest takie samo jak hasło logowania klastra.
    * **Grupa zasobów**: Grupa zasobów, w której ma zostać utworzony klaster.
    * **Lokalizacja**: Region świadczenia usługi Azure, w którym ma zostać utworzony klaster.

        ![Podstawowe szczegóły klastra](./media/r-server-get-started/clustername.png)

4. Wybierz pozycję **Typ klastra**, a następnie ustaw następujące wartości w sekcji **Konfiguracja klastra**:

    * **Typ klastra**: Usługi ML

    * **System operacyjny**: Linux

    * **Wersja**: ML Server 9.3 (HDI 3.6). Informacje o programie ML Server w wersji 9.3 są dostępne w witrynie [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-machine-learning-server).

    * **R Studio community edition dla systemów ML Server**: To środowisko IDE oparte na przeglądarce jest instalowany domyślnie w węźle brzegowym. Wyczyść pole wyboru, jeśli nie chcesz go instalować. Jeśli wybierzesz opcję instalacji, adres URL umożliwiający logowanie do programu RStudio Server będzie dostępny w bloku aplikacji portalu dla utworzonego klastra.

        ![Podstawowe szczegóły klastra](./media/r-server-get-started/clustertypeconfig.png)

4. Po wybraniu typu klastra ustaw typ klastra przy użyciu przycisku __Wybierz__. Następnie zakończ konfigurację podstawową za pomocą przycisku __Dalej__.

5. W sekcji **Magazyn** wybierz lub utwórz konto magazynu. Na potrzeby procedury w tym dokumencie pozostaw wartości domyślne w pozostałych polach w tej sekcji. Zapisz konfigurację magazynu za pomocą przycisku __Dalej__.

    ![Konfigurowanie ustawień konta magazynu dla usługi HDInsight](./media/r-server-get-started/cluster-storage.png)

6. W sekcji **Podsumowanie** przejrzyj konfigurację klastra. Zmień niepoprawne ustawienia przy użyciu linków __Edytuj__. Wreszcie utwórz klaster przy użyciu przycisku __Utwórz__.

    ![Konfigurowanie ustawień konta magazynu dla usługi HDInsight](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]  
    > Tworzenie klastra może potrwać do 20 minut.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Łączenie z programem RStudio Server

Jeśli wybierzesz opcję instalacji programu RStudio Server Community Edition w ramach klastra usługi HDInsight, możesz uzyskać dostęp do logowania do programu RStudio przy użyciu jednej z dwóch następujących metod:

* **Opcja 1** — przejdź do następującego adresu URL (gdzie **CLUSTERNAME** to nazwa utworzonego klastra usług ML):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Opcja 2** — witryna Azure Portal.
  Z poziomu portalu:
  1. Wybierz **wszystkich usług** menu po lewej stronie.
  2. W obszarze **ANALYTICS**, wybierz opcję **klastry HDInsight**.
  3. Wybierz nazwę klastra z **klastry HDInsight** strony.
  4. Z **pulpity nawigacyjne usługi ML**, wybierz opcję **programu R Studio server**. 

     ![Konfigurowanie ustawień konta magazynu dla usługi HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

     > [!IMPORTANT]  
     > Niezależnie od wybranej metody, pierwsze logowanie wymaga dwukrotnego uwierzytelnienia.  Po wyświetleniu pierwszego monitu dotyczącego uwierzytelniania podaj *identyfikator użytkownika administratora klastra* i *hasło*. Po wyświetleniu drugiego monitu dotyczącego uwierzytelniania podaj *identyfikator użytkownika protokołu SSH* i *hasło*. Kolejne dodatki dziennika wymagane tylko poświadczenia protokołu SSH.

Po nawiązaniu połączenia ekran powinien przypominać następujący zrzut ekranu:

![Łączenie z programem RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Uruchamianie przykładowego zadania

Zadanie można przesłać za pomocą funkcji programu ScaleR. Oto przykładowe polecenia służące do uruchamiania zadania:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
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

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Łączenie z węzłem brzegowym klastra

Z tej sekcji możesz dowiedzieć się, jak połączyć się z węzłem brzegowym klastra usług ML w usłudze HDInsight przy użyciu protokołu SSH. Aby zapoznać się z zastosowaniem protokołu SSH, zobacz [Korzystanie z protokołu SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Polecenie SSH służące do łączenia z węzłem brzegowym klastra usług ML to:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Aby znaleźć polecenie SSH klastra, w witrynie Azure Portal kliknij nazwę klastra, kliknij pozycję **SSH + dane logowania klastra**, a następnie w obszarze **Nazwa hosta** wybierz węzeł brzegowy. Spowoduje to wyświetlenie informacji o punkcie końcowym SSH dla węzła krawędzi.

![Obraz punktu końcowego SSH dla węzła krawędzi](./media/r-server-get-started/sshendpoint.png)

Jeśli do zabezpieczenia konta użytkownika SSH użyto hasła, zostanie wyświetlony monit o jego wprowadzenie. Jeśli używasz klucza publicznego, może być konieczne użycie parametru `-i` w celu określenia zgodnego klucza prywatnego. Na przykład:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Po nawiązaniu połączenia pojawi się monit podobny do następującego:

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Użycie konsoli R

1. W sesji SSH wpisz następujące polecenie, aby uruchomić konsolę R:  

        R

2. Oprócz innych informacji powinny pojawić się dane wyjściowe z wersją oprogramowania ML Server.
    
3. W monicie `>` możesz podać kod R. Usługi ML w usłudze HDInsight zawierają pakiety, które umożliwiają łatwą współpracę z usługą Hadoop i uruchamianie rozproszonych obliczeń. Na przykład następujące polecenie umożliwia wyświetlenie katalogu głównego domyślnego systemu plików klastra usługi HDInsight:

        rxHadoopListFiles("/")

4. Dostępne jest także adresowanie w stylu WASB.

        rxHadoopListFiles("wasb:///")

5. Aby zamknąć konsolę języka R, użyj następującego polecenia:

        quit()

## <a name="automated-cluster-creation"></a>Zautomatyzowane tworzenie klastra

Aby zautomatyzować tworzenie klastra usług ML w usłudze HDInsight, możesz użyć zestawu SDK oraz programu PowerShell.

<!---* To create an ML Server cluster using an Azure Resource Management template, see [Deploy an R Server for HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).--->
* Aby utworzyć klaster usług ML za pomocą zestawu .NET SDK, zobacz [Create Linux-based clusters in HDInsight using the .NET SDK (Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu zestawu .NET SDK)](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Aby utworzyć klaster usług ML przy użyciu programu PowerShell, zobacz artykuł [Create HDInsight clusters using Azure PowerShell (Tworzenie klastrów usługi HDInsight przy użyciu programu Azure PowerShell)](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób tworzenia nowego klastra usługi ML w usłudze Azure HDInsight i podstawy używania konsoli R w sesji SSH. W poniższych artykułach opisano inne sposoby korzystania z usług ML w usłudze HDInsight oraz zarządzania nimi:

* [Submit jobs from R Tools for Visual Studio (Przesyłanie zadań z narzędzi R Tools for Visual Studio)](r-server-submit-jobs-r-tools-vs.md)
* [Manage ML Services cluster on HDInsight (Zarządzanie klastrem usług ML w usłudze HDInsight)](r-server-hdinsight-manage.md)
* [Operationalize ML Services cluster on HDInsight (Obsługa operacji klastra usług ML w usłudze HDInsight)](r-server-operationalize.md)
* [Compute context options for ML Services cluster on HDInsight (Opcje kontekstu obliczeniowego dla klastra usług ML w usłudze HDInsight)](r-server-compute-contexts.md)
* [Azure Storage options for ML Services cluster on HDInsight (Opcje usługi Azure Storage dla klastra usług ML w usłudze HDInsight)](r-server-storage.md)
