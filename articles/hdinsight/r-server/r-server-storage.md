---
title: Rozwiązania magazynu platformy Azure dla usług ML na platformie HDInsight — Azure
description: Dowiedz się więcej o różnych opcjach pamięci masowej dostępnych w usługach ML w programie HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: 1c79d0390a80a1358ddb09707fbabf6a5a2affdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660243"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Rozwiązania usługi azure storage dla usług ML w usłudze Azure HDInsight

Usługi ML w programie HDInsight mogą używać różnych rozwiązań magazynu do utrwalania danych, kodu lub obiektów zawierających wyniki analizy. Rozwiązania te obejmują następujące opcje:

- [Obiekt bob Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Magazyn plików platformy Azure](https://azure.microsoft.com/services/storage/files/)

Masz również możliwość uzyskania dostępu do wielu kont magazynu platformy Azure lub kontenerów z klastra HDInsight. Usługa Azure File Storage to wygodna opcja przechowywania danych do użycia w węźle brzegowym, która umożliwia zainstalowanie udziału plików magazynu platformy Azure, na przykład do systemu plików systemu linux. Ale udziały usługi Azure File mogą być montowane i używane przez dowolny system, który ma obsługiwany system operacyjny, taki jak Windows lub Linux.

Podczas tworzenia klastra Apache Hadoop w usłudze HDInsight należy określić konto **usługi Azure Storage** lub magazyn usługi Data **Lake**. Określony kontener magazynu z tego konta przechowuje system plików dla utworzonego klastra (na przykład rozproszony system plików Hadoop). Aby uzyskać więcej informacji i wskazówek, zobacz:

- [Korzystanie z usługi Azure Storage z usługą HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Korzystanie z magazynu usługi Data Lake w klastrach usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Używanie kont magazynu obiektów Blob platformy Azure w klastrze usług ML

Jeśli podczas tworzenia klastra usług ML określono więcej niż jedno konto magazynu, poniższe instrukcje wyjaśniają, jak używać konta pomocniczego do dostępu do danych i operacji w klastrze usług ML. Załóżmy następujące konta magazynu i kontener: **storage1** i domyślny kontener o nazwie **container1**i **storage2** z **container2**.

> [!WARNING]  
> Na potrzeby wydajności klaster HDInsight jest tworzony w tym samym centrum danych, co określone konto magazynu podstawowego. Korzystanie z konta magazynu w innej lokalizacji niż klaster HDInsight nie jest obsługiwane.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Używanie domyślnej pamięci masowej z usługami ML w programie HDInsight

1. Za pomocą klienta SSH połącz się z węzłem brzegowym klastra. Aby uzyskać informacje na temat używania SSH z klastrami HDInsight, zobacz [Używanie SSH z HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Skopiuj przykładowy plik mysamplefile.csv do katalogu /share.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Przełącz się do R Studio lub innej konsoli R i napisz kod R, aby ustawić węzeł nazwy **na domyślny** i lokalizację pliku, do którego chcesz uzyskać dostęp.  

    ```R
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
    ```

Wszystkie odwołania do katalogu i plików wskazują `wasbs://container1@storage1.blob.core.windows.net`na konto magazynu . Jest to **domyślne konto magazynu** skojarzone z klastrem HDInsight.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Korzystanie z dodatkowej pamięci masowej z usługami ML w programie HDInsight

Teraz załóżmy, że chcesz przetworzyć plik o nazwie mysamplefile1.csv, który znajduje się w /private katalogu **container2** w **storage2**.

W kodzie R skieruj odwołanie do węzła nazwy na konto magazynu **storage storage2.**

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
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
```

Wszystkie odwołania do katalogu i plików wskazują teraz `wasbs://container2@storage2.blob.core.windows.net`konto magazynu . Jest to **węzeł nazwy,** który został określony.

Skonfiguruj `/user/RevoShare/<SSH username>` katalog w **magazynie2** w następujący sposób:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-with-ml-services-cluster"></a>Korzystanie z usługi Azure Data Lake Storage w klastrze usług ML

Aby używać magazynu usługi Data Lake z klastrem HDInsight, musisz udzielić klastrowi dostępu do każdego magazynu usługi Azure Data Lake, którego chcesz użyć. Aby uzyskać instrukcje dotyczące używania witryny Azure Portal do tworzenia klastra usługi HDInsight z kontem usługi Azure Data Lake Storage jako magazynu domyślnego lub dodatkowego magazynu, zobacz [Tworzenie klastra USŁUGI HDInsight z usługą Data Lake Storage przy użyciu portalu Azure.](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)

Następnie użyj magazynu w skrypcie języka R, podobnie jak miało to miejsce w przypadku dodatkowego konta magazynu platformy Azure, zgodnie z opisem w poprzedniej procedurze.

### <a name="add-cluster-access-to-your-azure-data-lake-storage"></a>Dodawanie dostępu do klastra do magazynu usługi Azure Data Lake

Dostęp do magazynu usługi Data Lake można uzyskać przy użyciu jednostki usługi Azure Active Directory (Azure AD), która jest skojarzona z klastrem usługi HDInsight.

1. Podczas tworzenia klastra HDInsight wybierz pozycję **Tożsamość usługi Cluster AAD** na karcie **Źródło danych.**

2. W oknie dialogowym **Tożsamość usługi AAD klastra** w obszarze **Wybierz jednostkę usługi AD**wybierz pozycję **Utwórz nowy**.

Po nałowiuj nazwę podmiotu usługi i utworzysz dla niej hasło, kliknij pozycję **Zarządzaj dostępem ADLS,** aby skojarzyć jednostkę usługi z magazynem usługi.

Istnieje również możliwość dodania dostępu do klastra do jednego lub więcej kont magazynu usługi Data Lake po utworzeniu klastra. Otwórz wpis portalu Azure dla magazynu usługi Data Lake i przejdź do **programu Data Explorer > Access > Add**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Jak uzyskać dostęp do pamięci masowej Data Lake Gen1 z usług ML w u hdinsight

Po uzyskaniu dostępu do usługi Data Lake Storage Gen1 można użyć magazynu w klastrze usług ML w usłudze HDInsight w taki sposób, w jaki ma być dodatkowym kontem magazynu platformy Azure. Jedyną różnicą jest to, że prefiks **wasbs://** zmienia się **adl://** w następujący sposób:

```R
# Point to the ADL Storage (e.g. ADLtest)
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
```

Następujące polecenia są używane do konfigurowania konta Data Lake Storage Gen1 w katalogu RevoShare i dodawania przykładowego pliku csv z poprzedniego przykładu:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Korzystanie z magazynu plików platformy Azure z usługami ML w programie HDInsight

Istnieje również wygodna opcja przechowywania danych do użycia w węźle brzegowym o nazwie [Usługi Azure Files.](https://azure.microsoft.com/services/storage/files/) Umożliwia zainstalowanie udziału plików usługi Azure Storage w systemie plików systemu Linux. Ta opcja może być przydatna do przechowywania plików danych, skryptów Języka R i obiektów wyników, które mogą być potrzebne później, zwłaszcza gdy ma sens używanie macierzystego systemu plików w węźle krawędzi, a nie w systemie PLIKÓW HDFS.

Główną zaletą usługi Azure Files jest to, że udziały plików mogą być montowane i używane przez dowolny system, który ma obsługiwany system operacyjny, taki jak Windows lub Linux. Na przykład może służyć przez inny klaster HDInsight, który ty lub ktoś w zespole ma, przez maszynę wirtualną platformy Azure, a nawet przez system lokalny. Aby uzyskać więcej informacji, zobacz:

- [Jak używać usługi Azure File Storage z systemem Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Jak korzystać z magazynu plików platformy Azure w systemie Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Następne kroki

- [Omówienie klastra usług ML w programie HDInsight](r-server-overview.md)
- [Compute context options for ML Services cluster on HDInsight (Opcje kontekstu obliczeniowego dla klastra usług ML w usłudze HDInsight)](r-server-compute-contexts.md)
- [Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
