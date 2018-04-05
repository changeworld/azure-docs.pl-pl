---
title: Wprowadzenie do oprogramowania R Server w usłudze HDInsight — platforma Azure | Microsoft Docs
description: Dowiedz się, jak utworzyć aparat Apache Spark w klastrze usługi HDInsight zawierającym oprogramowanie R Server, a następnie jak przesłać skrypt języka R do klastra.
services: HDInsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: f4265ce7370542d8253222a5e268ea9cde7fd36e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="get-started-with-r-server-cluster-on-azure-hdinsight"></a>Wprowadzenie do klastra oprogramowania R Server w usłudze Azure HDInsight

Usługa Azure HDInsight obejmuje opcję oprogramowania R Server, którą można zintegrować z klastrem usługi HDInsight. Opcja ta pozwala skryptom języka R używać aparatu Spark i funkcji MapReduce do wykonywania obliczeń rozproszonych. W tym artykule omówiono sposób tworzenia wystąpienia oprogramowania R Server w klastrze usługi HDInsight. Następnie omówiono uruchamianie skryptu R, który demonstruje sposób użycia aparatu Spark na potrzeby wykonywania rozproszonych obliczeń przez kod R.


## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: przed rozpoczęciem tego samouczka musisz mieć subskrypcję platformy Azure. Aby uzyskać więcej informacji, zobacz [Get Microsoft Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Uzyskaj bezpłatną wersję próbną platformy Azure).
* **Klient protokołu Secure Shell (SSH)**: klient SSH jest używany do zdalnego łączenia z klastrem usługi HDInsight i uruchamiania poleceń bezpośrednio w klastrze. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Tworzenie klastra przy użyciu witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Kliknij kolejno pozycje **Utwórz zasób** > **Dane + analiza** > **HDInsight**.

3. W bloku **Podstawowe** wprowadź następujące informacje:

    * **Nazwa klastra**: nazwa klastra usługi HDInsight.
    * **Subskrypcja**: wybierz subskrypcję, której chcesz użyć.
    * **Nazwa użytkownika logowania klastra** i **Hasło logowania klastra**: dane logowania podczas uzyskiwania dostępu do klastra przy użyciu protokołu HTTPS. Te poświadczenia umożliwiają dostęp do takich usług jak interfejs użytkownika sieci Web Ambari lub interfejs API REST.
    * **Nazwa użytkownika SSH (Secure Shell)**: nazwa logowania używana podczas uzyskiwania dostępu do klastra za pośrednictwem protokołu SSH. Domyślnie hasło jest takie samo jak hasło logowania klastra.
    * **Grupa zasobów**: grupa zasobów, w której ma zostać utworzony klaster.
    * **Lokalizacja**: region platformy Azure, w którym ma zostać utworzony klaster.

        ![Podstawowe szczegóły klastra](./media/r-server-get-started/clustername.png)

4. Wybierz pozycję **Typ klastra**, a następnie ustaw następujące wartości w sekcji **Konfiguracja klastra**:

    * **Typ klastra**: R Server

    * **System operacyjny**: Linux

    * **Wersja**: R Server 9.1 (HDI 3.6). Informacje o wersji dotyczące dostępnych wersji oprogramowania R Server można znaleźć w witrynie [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-r-server#r-server-91).

    * **Program R Studio Community Edition for R Server**: to środowisko IDE oparte na przeglądarce, które jest instalowane domyślnie w węźle brzegowym. Wyczyść pole wyboru, jeśli nie chcesz go instalować. Jeśli wybierzesz opcję instalacji, adres URL umożliwiający logowanie do programu RStudio Server będzie dostępny w bloku aplikacji portalu dla utworzonego klastra.

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

* **Opcja 1** — przejdź do następującego adresu URL (gdzie **NAZWA_KLASTRA** to nazwa utworzonego klastra programu R Server):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Opcja 2** — otwórz klaster programu R Server w witrynie Azure Portal w obszarze **Szybkie linki** i kliknij pozycję **Pulpity nawigacyjne programu R Server**.

     ![Konfigurowanie ustawień konta magazynu dla usługi HDInsight](./media/r-server-get-started/dashboard-quick-links.png)

    W obszarze **Pulpity nawigacyjne klastra** kliknij pozycję **R Studio Server**.

    ![Konfigurowanie ustawień konta magazynu dla usługi HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]
   > Niezależnie od wybranej metody, pierwsze logowanie wymaga dwukrotnego uwierzytelnienia.  Po wyświetleniu pierwszego monitu dotyczącego uwierzytelniania podaj *identyfikator użytkownika administratora klastra* i *hasło*. Po wyświetleniu drugiego monitu dotyczącego uwierzytelniania podaj *identyfikator użytkownika protokołu SSH* i *hasło*. Podczas kolejnych logowań będą wymagane tylko poświadczenia protokołu SSH.

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

Z tej sekcji możesz dowiedzieć się, jak połączyć się z węzłem brzegowym klastra oprogramowania R Server w usłudze HDInsight przy użyciu protokołu SSH. Aby zapoznać się z zastosowaniem protokołu SSH, zobacz [Korzystanie z protokołu SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Polecenie SSH służące do łączenia z węzłem brzegowym klastra R Server to:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Aby znaleźć polecenie SSH klastra, w witrynie Azure Portal kliknij nazwę klastra, kliknij pozycję **SSH + dane logowania klastra**, a następnie w obszarze **Nazwa hosta** wybierz węzeł brzegowy. Spowoduje to wyświetlenie informacji o punkcie końcowym SSH dla węzła krawędzi.

![Obraz punktu końcowego SSH dla węzła krawędzi](./media/r-server-get-started/sshendpoint.png)

Jeśli do zabezpieczenia konta użytkownika SSH użyto hasła, zostanie wyświetlony monit o jego wprowadzenie. Jeśli używasz klucza publicznego, może być konieczne użycie parametru `-i` w celu określenia zgodnego klucza prywatnego. Na przykład:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Po nawiązaniu połączenia pojawi się monit podobny do następującego:

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-server-console"></a>Korzystanie z konsoli oprogramowania R Server

1. W sesji SSH wpisz następujące polecenie, aby uruchomić konsolę R:  

        R

2. Oprócz innych informacji powinny pojawić się dane wyjściowe z wersją oprogramowania R Server.
    
3. W monicie `>` możesz podać kod R. Oprogramowanie R Server w usłudze HDInsight zawiera pakiety, które umożliwiają łatwą współpracę z usługą Hadoop i uruchamianie rozproszonych obliczeń. Na przykład następujące polecenie umożliwia wyświetlenie katalogu głównego domyślnego systemu plików klastra usługi HDInsight:

        rxHadoopListFiles("/")

4. Dostępne jest także adresowanie w stylu WASB.

        rxHadoopListFiles("wasb:///")

5. Aby zamknąć konsolę języka R, użyj następującego polecenia:

        quit()

## <a name="automated-cluster-creation"></a>Zautomatyzowane tworzenie klastra

Aby zautomatyzować tworzenie klastra oprogramowania R Server dla usługi HDInsight, możesz użyć szablonów usługi Azure Resource Manager, zestawu SDK oraz programu PowerShell.

* Aby utworzyć klaster oprogramowania R Server za pomocą szablonu usługi Azure Resource Management, zobacz [Deploy an R server HDInsight cluster (Wdrażanie klastra usługi HDInsight oprogramowania R Server)](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).
* Aby utworzyć klaster oprogramowania R Server za pomocą zestawu .NET SDK, zobacz [Create Linux-based clusters in HDInsight using the .NET SDK (Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu zestawu .NET SDK)](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Aby utworzyć klaster oprogramowania R Server przy użyciu programu PowerShell, zobacz artykuł [Create HDInsight clusters using Azure PowerShell (Tworzenie klastrów usługi HDInsight przy użyciu programu Azure PowerShell)](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia nowego klastra oprogramowania R Server w usłudze Azure HDInsight i podstawy korzystania z konsoli języka R w sesji protokołu SSH. W poniższych artykułach opisano inne sposoby korzystania z oprogramowania R Server w usłudze HDInsight oraz zarządzania nim:

* [Submit jobs from R Tools for Visual Studio (Przesyłanie zadań z narzędzi R Tools for Visual Studio)](r-server-submit-jobs-r-tools-vs.md)
* [Manage R Server cluster on HDInsight (Zarządzanie klastrem oprogramowania R Server w usłudze HDInsight)](r-server-hdinsight-manage.md)
* [Operationalize R Server cluster on HDInsight (Operacjonalizowanie klastra oprogramowania R Server w usłudze HDInsight)](r-server-operationalize.md)
* [Compute context options for R Server on HDInsight (Opcje kontekstu obliczeniowego dla oprogramowania R Server w usłudze HDInsight)](r-server-compute-contexts.md)
* [Azure Storage options for R Server on HDInsight (Opcje usługi Azure Storage dla oprogramowania R Server w usłudze HDInsight)](r-server-storage.md)
