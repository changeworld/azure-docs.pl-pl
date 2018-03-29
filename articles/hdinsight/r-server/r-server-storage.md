---
title: Azure rozwiązań magazynów na potrzeby R Server w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Informacje na temat opcji magazynu innego dostępne z serwerem R w usłudze HDInsight
services: HDInsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/22/2018
ms.author: nitinme
ms.openlocfilehash: f0333252e404c52f123da916df01d198234ef5af
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-storage-solutions-for-r-server-on-azure-hdinsight"></a>Azure rozwiązań magazynów na potrzeby R Server w usłudze Azure HDInsight

R Server w usłudze HDInsight zawiera różnych rozwiązań magazynu do utrwalenia danych, kodu lub obiektów zawierających wyniki analizy. Należą do nich następujące opcje:

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/)
- [Magazyn plików Azure](https://azure.microsoft.com/services/storage/files/)

Istnieje również możliwość uzyskiwania dostępu do wielu kont magazynu Azure lub kontenery z klastrem usługi HDInsight. Magazyn plików Azure to wygodny danych opcji magazynu do użycia na węzeł krawędzi, który umożliwia zainstalowanie udziału plików magazynu Azure, na przykład system plików systemu Linux. Jednak udziały plików platformy Azure można zainstalować i używane przez system z obsługiwanego systemu operacyjnego, takie jak Windows lub Linux. 

Podczas tworzenia klastra usługi Hadoop w usłudze HDInsight, należy określić albo **magazynu Azure** konta lub **Data Lake store**. Z tego konta kontener magazynu określonych przechowuje systemu plików dla klastra, który można utworzyć (na przykład rozproszonego systemu plików Hadoop). Aby uzyskać więcej informacji i wskazówek zobacz:

- [Użyj magazynu platformy Azure z usługą HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Użyj Data Lake Store z klastrami Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-r-server-cluster"></a>Użyj konta magazynu obiektów Blob platformy Azure z klastrem serwera R

Jeśli określono więcej niż jedno konto magazynu podczas tworzenia klastra serwerów R, poniższe instrukcje wyjaśniają sposób użycia dodatkowej konta dla dostępu do danych i operacji w klastrze serwera R. Załóżmy następujące konta magazynu i kontener: **storage1** i domyślny kontener o nazwie **container1**, i **storage2** z **container2**.

> [!WARNING]
> Ze względów wydajnościowych klastra usługi HDInsight jest tworzony w tym samym centrum danych, co konto magazynu podstawowego, który określisz. Używanie konta magazynu w innej lokalizacji niż klastra usługi HDInsight nie jest obsługiwane.

### <a name="use-the-default-storage-with-r-server-on-hdinsight"></a>Użyj domyślnego magazynu z serwerem R w usłudze HDInsight

1. Przy użyciu klienta SSH, połączenie z węzłem krawędzi klastra. Aby uzyskać informacji o korzystaniu z protokołu SSH z klastrami usługi HDInsight, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Skopiuj plik przykładowy mysamplefile.csv, w katalogu/Share. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

3. Przełącz się do R Studio lub innej konsoli R i napisać kod R, aby ustawić nazwę węzła **domyślne** i lokalizację pliku, którego chcesz uzyskać dostęp.  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

Wszystkie odwołania katalogów i plików wskaż konta magazynu `wasb://container1@storage1.blob.core.windows.net`. Jest to **domyślne konto magazynu** który jest skojarzony z klastrem usługi HDInsight.

### <a name="use-the-additional-storage-with-r-server-on-hdinsight"></a>Użyj dodatkowe miejsce do magazynowania z serwerem R w usłudze HDInsight

Załóżmy, że chcesz przetworzyć pliku o nazwie mysamplefile1.csv, który znajduje się w /private katalog **container2** w **storage2**.

W kodzie języka R punkt nazwa węzła odwołania do **storage2** konta magazynu.

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

    #Location of the data:
    bigDataDirRoot <- "/private"

    #Define Spark compute context:
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define HDFS file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")

Wszystkie odwołania do plików i katalogów wskazywać konto magazynu `wasb://container2@storage2.blob.core.windows.net`. Jest to **nazwa węzła** , który został określony.

Należy skonfigurować User/RevoShare/<SSH username> katalogu na **storage2** w następujący sposób:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-an-azure-data-lake-store-with-r-server-cluster"></a>Użyj usługi Azure Data Lake Store z klastrem serwera R 

Aby korzystać z usługi Data Lake Store z klastrem usługi HDInsight, należy zapewnić dostęp do sieci klastra do każdej usługi Azure Data Lake Store, którego chcesz używać. Aby uzyskać instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight przy użyciu konta usługi Azure Data Lake Store jako domyślnego magazynu lub dodatkowego magazynu za pomocą portalu Azure, zobacz [tworzenia klastra usługi HDInsight z Data Lake Store za pomocą portalu Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Następnie należy użyć magazynu w skrypcie R znacznie jak konto magazynu Azure dodatkowej zgodnie z opisem w poprzedniej procedurze.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Dodaj klaster dostęp do usługi Azure Data Lake sklepach
Data Lake store jest dostęp przy użyciu nazwy głównej usługi Azure Active Directory (Azure AD), który został skojarzony z klastrem usługi HDInsight.

1. Podczas tworzenia klastra usługi HDInsight, wybierz **tożsamość usługi AAD klastra** z **źródła danych** kartę.

2. W **tożsamość usługi AAD klastra** okna dialogowego, w obszarze **wybierz jednostkę usługi AD**, wybierz pozycję **Utwórz nowy**.

Po nadaj nazwę główną usługi i Utwórz hasło dla niego kliknij **zarządzać dostępem ADLS** do skojarzenia nazwy głównej usługi z usługi Data Lake Store.

Istnieje również możliwość można dodać klastra dostęp do co najmniej jednego konta usługi Data Lake Store, po utworzeniu klastra. Otwórz pozycję portalu usługi Azure Data Lake Store i przejdź do **Eksploratora danych > dostępu > Dodaj**. 

### <a name="how-to-access-the-data-lake-store-from-r-server-on-hdinsight"></a>Jak uzyskać dostęp do Data Lake store z serwerem R w usłudze HDInsight

Po którym został przyznany dostęp do usługi Data Lake Store, używając magazynu w klastrze serwerów R w usłudze HDInsight sposób, w jaki konto magazynu Azure dodatkowej. Jedyną różnicą jest to, że prefiks **wasb: / /** zmienia się na **adl: / /** w następujący sposób:


    # Point to the ADL store (e.g. ADLtest)
    myNameNode <- "adl://rkadl1.azuredatalakestore.net"
    myPort <- 0

    # Location of the data (assumes a /share directory on the ADL account)
    bigDataDirRoot <- "/share"  

    # Define Spark compute context
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    # Set compute context
    rxSetComputeContext(mySparkCluster)

    # Define HDFS file system
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    # Specify the input file in HDFS to analyze
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

Poniższe polecenia są używane do konfigurowania konta usługi Data Lake Store z katalogiem RevoShare i Dodaj przykładowy plik CSV z poprzedniego przykładu:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server-on-hdinsight"></a>Użyj usługi Magazyn plików Azure z serwerem R w usłudze HDInsight

Istnieje również wygodne danych opcji magazynu do użycia na węzeł krawędzi, o nazwie [pliki Azure] ((https://azure.microsoft.com/services/storage/files/). Umożliwia instalowanie udziału plików magazynu Azure w systemie Linux. Ta opcja może być przydatna do przechowywania plików danych, skrypty języka R i obiektów wynikowych, które mogą być wymagane później, szczególnie w przypadku warto system plików natywnych węzła krawędzi zamiast systemu plików HDFS. 

Główną zaletą usługi pliki Azure jest udziały plików może być zainstalowane i używane przez system z obsługiwane systemy operacyjne, takie jak Windows lub Linux. Na przykład można używany przez inny klaster usługi HDInsight, który użytkownik lub inna w zespole ma, maszyny Wirtualnej platformy Azure lub nawet przez system lokalny. Aby uzyskać więcej informacji, zobacz:

- [Jak używać usługi Azure File Storage z systemem Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Jak używać magazynu plików Azure w systemie Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Kolejne kroki

* [Omówienie klastrów R Server w usłudze HDInsight](r-server-overview.md)
* [Rozpoczynanie pracy z klastrem R Server na platformie Hadoop](r-server-get-started.md)
* [Obliczenia bazy danych opcji kontekstu dla klastra serwera R w usłudze HDInsight](r-server-compute-contexts.md)

