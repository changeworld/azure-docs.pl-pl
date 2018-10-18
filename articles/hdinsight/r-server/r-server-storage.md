---
title: Rozwiązania magazynu platformy Azure dla usług HDInsight — usługi Azure ML
description: Dowiedz się więcej o różnych opcji magazynowania dostępnych za pomocą usługi ML w HDInsight
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 4c3b36891030ae97f382d5e1d0623fa7c698dbf6
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395093"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Rozwiązania magazynu platformy Azure dla usługi ML w usłudze Azure HDInsight

Usługi uczenie Maszynowe na HDInsight można użyć różnych systemów pamięci masowej można utrwalić dane, kod lub obiektów, które zawierają wyniki analizy. Obejmują one następujące opcje:

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/)
- [Usługa Azure File storage](https://azure.microsoft.com/services/storage/files/)

Istnieje również możliwość uzyskiwania dostępu do wielu kont usługi Azure storage lub kontenerów przy użyciu klastra usługi HDInsight. Usługa Azure File storage to opcja magazynowania danych wygodne do użytku w węźle krawędzi umożliwiające instalowanie udziału plików usługi Azure Storage, na przykład system plików w systemie Linux. Ale udziałów plików platformy Azure może być zainstalowany i używane przez system z obsługiwanym systemem operacyjnym, takich jak Windows lub Linux. 

Podczas tworzenia klastra usługi Hadoop w HDInsight, należy określić albo **usługi Azure storage** konta lub **usługa Data Lake store**. Z tego konta kontener określonego magazynu przechowuje systemu plików dla klastra, którą tworzysz (na przykład, rozproszony System plików Hadoop). Aby uzyskać więcej informacji i wskazówek zobacz:

- [Usługa Azure storage za pomocą HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Użyj Data Lake Store z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Korzystanie z kont magazynu obiektów Blob platformy Azure z klastrem usługi ML

Jeśli określono więcej niż jedno konto magazynu podczas tworzenia klastra usługi uczenie Maszynowe, zgodnie z poniższymi instrukcjami wyjaśniono, jak używać pomocniczego konta dla dostępu do danych i operacje w klastrze usługi ML. Przyjęto założenie, że następujących kont magazynu i kontener: **storage1** oraz powoduje używanie domyślnego kontenera o nazwie **container1**, i **storage2** z **container2**.

> [!WARNING]
> Ze względów wydajnościowych klaster HDInsight jest tworzony w tym samym centrum danych, co konto magazynu podstawowego, który określisz. Za pomocą konta magazynu w innej lokalizacji niż klaster HDInsight nie jest obsługiwana.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Użyj domyślnej pamięci masowej z usługami uczenia Maszynowego na HDInsight

1. Przy użyciu klienta SSH, łączenie z węzłem krawędzi klastra. Aby uzyskać informacje na temat korzystania z protokołu SSH z klastrami HDInsight, zobacz [użycia protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Skopiuj plik przykładowy mysamplefile.csv, do katalogu/Share. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

3. Przełącz się do programu R Studio lub innej konsoli R, a następnie napisać kod R, aby ustawić nazwę węzła **domyślne** i lokalizację pliku, którego chcesz uzyskać dostęp.  

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

Wszystkie odwołania katalogów i plików, wskaż na koncie magazynu `wasb://container1@storage1.blob.core.windows.net`. Jest to **domyślne konto magazynu** skojarzony z klastrem HDInsight.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Dodatkowego magazynu za pomocą usługi uczenie Maszynowe na HDInsight

Załóżmy, że do przetworzenia pliku o nazwie mysamplefile1.csv, który znajduje się w /private katalogu **container2** w **storage2**.

W kodzie R punkt nazwa węzła odwołania do **storage2** konta magazynu.

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

Wszystkie odwołania do plików i katalogów wskazywać na koncie magazynu `wasb://container2@storage2.blob.core.windows.net`. Jest to **nazwy węzła** określony.

Należy skonfigurować/User/RevoShare/<SSH username> katalogu **storage2** w następujący sposób:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-an-azure-data-lake-store-with-ml-services-cluster"></a>Użyj usługi Azure Data Lake Store z klastrem usługi ML 

Data Lake Store za pomocą klastra usługi HDInsight, musisz podać klastrowi dostęp do każdego Azure Store Lake danych, którego chcesz używać. Aby uzyskać instrukcje dotyczące sposobu używania witryny Azure portal do utworzenia klastra HDInsight z kontem usługi Azure Data Lake Store jako magazynem domyślnym lub jako dodatkowego magazynu, zobacz [Tworzenie klastra HDInsight z usługą Data Lake Store przy użyciu witryny Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Następnie należy użyć magazynu w skrypcie języka R podobnie jak konto magazynu pomocniczego platformy Azure zgodnie z opisem w poprzedniej procedurze.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Dodaj klastrowi dostęp do usługi Azure Data Lake Store
Usługa Data Lake store jest dostęp za pomocą jednostki usługi Azure Active Directory (Azure AD), skojarzonym z klastrem usługi HDInsight.

1. Podczas tworzenia klastra usługi HDInsight, wybrać **tożsamości usługi AAD klastra** z **źródła danych** kartę.

2. W **tożsamości usługi AAD klastra** dialogowego **wybierz jednostkę usługi AD**, wybierz opcję **Utwórz nową**.

Po nadaj nazwę jednostki usługi i Utwórz hasło dla niego, kliknij przycisk **zarządzanie dostępem do usługi ADLS** skojarzyć nazwę główną usługi za pomocą usługi Data Lake Store.

Istnieje również możliwość dodawania klastrowi dostęp do co najmniej jednego konta Data Lake Store, po utworzeniu klastra. Otwórz pozycji Azure portal for Data Lake Store i przejdź do **Eksplorator danych > dostępu > Dodaj**. 

### <a name="how-to-access-the-data-lake-store-from-ml-services-on-hdinsight"></a>Jak uzyskać dostęp do usługa Data Lake store z usługi uczenie Maszynowe na HDInsight

Po został przez Ciebie udzielony dostęp do Data Lake Store, służy do magazynu w klastrze usługi ML w HDInsight sposób, w jaki na koncie magazynu pomocniczego platformy Azure. Jedyną różnicą jest to, że prefiks **wasb: / /** zmieni się na **adl: / /** w następujący sposób:


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

Następujące polecenia służą do konfigurowania konta Data Lake Store z katalogiem RevoShare i Dodaj przykładowy plik CSV z poprzedniego przykładu:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Usługa Azure File storage za pomocą usługi uczenie Maszynowe na HDInsight

Jest również wygodne danych opcji magazynu do użycia w węźle krawędzi, o nazwie [usługi Azure Files](https://azure.microsoft.com/services/storage/files/). Umożliwia ona zainstalować udział plików usługi Azure Storage w systemie plików systemu Linux. Ta opcja może być przydatne do przechowywania plików danych, skryptów języka R i obiektów wynikowych, które mogą być potrzebne później, szczególnie w przypadku dobrym rozwiązaniem w systemie plików natywnych na węźle krawędzi, a nie systemu plików HDFS. 

Główną zaletą usługi Azure Files jest udziałów plików mogą być instalowane i używane przez system z obsługiwanych systemów operacyjnych, takich jak Windows lub Linux. Na przykład może służyć przez inny klaster HDInsight, który użytkownik lub ktoś z zespołu ma przez Maszynę wirtualną platformy Azure lub nawet przez system w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz:

- [Jak używać usługi Azure File Storage z systemem Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Jak używać usługi Azure File storage na Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Kolejne kroki

* [Omówienie usługi ML klastra w systemie HDInsight](r-server-overview.md)
* [Rozpoczynanie pracy z klastrem usługi ML w usłudze Hadoop](r-server-get-started.md)
* [Compute context options for ML Services cluster on HDInsight (Opcje kontekstu obliczeniowego dla klastra usług ML w usłudze HDInsight)](r-server-compute-contexts.md)

