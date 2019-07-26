---
title: Przekazywanie danych dla zadań Apache Hadoop w usłudze HDInsight
description: Dowiedz się, jak przekazywać i uzyskiwać dostęp do danych dla Apache Hadoopych zadań w usłudze HDInsight przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure, Eksplorator usługi Azure Storage, Azure PowerShell, wiersza poleceń Hadoop lub Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdiseo17may2017
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f75933940aa97606ca33ab6bfc18fe5871811eef
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441974"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Przekazywanie danych dla zadań Apache Hadoop w usłudze HDInsight

Usługa Azure HDInsight udostępnia w pełni funkcjonalny rozproszony system plików Hadoop (HDFS) za pośrednictwem usługi Azure Storage i Azure Data Lake Storage (Gen1 i Gen2). Usługi Azure Storage i Data Lake Storage Gen1 i Gen2 zostały zaprojektowane jako rozszerzenia HDFS, aby zapewnić klientom bezproblemowe środowisko. Umożliwiają one korzystanie z pełnego zestawu składników w ekosystemie usługi Hadoop do działania bezpośrednio na danych, którymi zarządza. Usługa Azure Storage, Data Lake Storage Gen1 i Gen2 to różne systemy plików, które są zoptymalizowane pod kątem magazynowania danych i obliczeń na tych danych. Aby uzyskać informacje na temat korzyści z używania usługi Azure Storage, zobacz [Korzystanie z usługi Azure Storage z usługą HDInsight](hdinsight-hadoop-use-blob-storage.md), [Używanie Data Lake Storage Gen1 z usługą hdinsight](hdinsight-hadoop-use-data-lake-store.md)i [Używanie Data Lake Storage Gen2 z usługą HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy zwrócić uwagę na następujące wymagania:

* Klaster usługi Azure HDInsight. Aby uzyskać instrukcje, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) lub [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Znajomość następujących artykułów:

    - [Korzystanie z usługi Azure Storage z usługą HDInsight](hdinsight-hadoop-use-blob-storage.md)
    - [Korzystanie z Data Lake Storage Gen1 z usługą HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    - [Korzystanie z Data Lake Storage Gen2 z usługą HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Przekazywanie danych do usługi Azure Storage

## <a name="utilities"></a>Usługi użyteczności publicznej
Firma Microsoft udostępnia następujące narzędzia do pracy z usługą Azure Storage:

| Tool | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Interfejs wiersza polecenia platformy Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [Narzędzie AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop — polecenie](#commandline) |✔ |✔ |✔ |


> [!NOTE]  
> Polecenie Hadoop jest dostępne tylko w klastrze usługi HDInsight. Polecenie umożliwia tylko ładowanie danych z lokalnego systemu plików do usługi Azure Storage.  


## <a id="commandline"></a>Wiersz polecenia usługi Hadoop
Wiersz polecenia usługi Hadoop jest przydatny tylko w przypadku przechowywania danych w usłudze Azure Storage BLOB, gdy dane znajdują się już w węźle głównym klastra.

Aby można było użyć polecenia Hadoop, należy najpierw nawiązać połączenie z usługą węzła głównego przy użyciu protokołu [SSH lub](hdinsight-hadoop-linux-use-ssh-unix.md)wypełnień.

Po nawiązaniu połączenia możesz użyć następującej składni, aby przekazać plik do magazynu.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Na przykład: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Ponieważ domyślny system plików dla usługi HDInsight znajduje się w usłudze Azure Storage,/example/Data/Data.txt faktycznie znajduje się w usłudze Azure Storage. Można również odwołać się do pliku jako:

    wasbs:///example/data/data.txt

lub

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Aby zapoznać się z listą innych poleceń usługi Hadoop, które pracują z plikami, zobacz[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> W przypadku klastrów Apache HBase domyślny rozmiar bloku używany podczas pisania danych to 256 KB. Chociaż jest to dobre rozwiązanie w przypadku używania interfejsów API HBase lub interfejsów API `hadoop` REST `hdfs dfs` , użycie poleceń lub do zapisu danych o rozmiarze większym niż ~ 12 GB powoduje wystąpienie błędu. Aby uzyskać więcej informacji, zobacz sekcję [wyjątek magazynu dla funkcji Write on BLOB](#storageexception) w tym artykule.

## <a name="graphical-clients"></a>Klienci graficzną
Istnieje również kilka aplikacji, które udostępniają interfejs graficzny służący do pracy z usługą Azure Storage. Poniższa tabela zawiera listę kilku z następujących aplikacji:

| Klient | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Narzędzia Microsoft Visual Studio dla usługi HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer dla Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |


## <a name="mount-azure-storage-as-local-drive"></a>Instalowanie usługi Azure Storage jako dysku lokalnego
Zobacz [Instalowanie usługi Azure Storage jako dysku lokalnego](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Przekaż za pomocą usług
### <a name="azure-data-factory"></a>Azure Data Factory
Usługa Azure Data Factory to w pełni zarządzana usługa umożliwiająca tworzenie magazynów danych, przetwarzania danych i usług przenoszenia danych w usprawnionych, skalowalnych i niezawodnych potokach produkcyjnych danych.

|Typ magazynu|Dokumentacja|
|----|----|
|Azure Blob Storage|[Kopiowanie danych do lub z usługi Azure Blob Storage za pomocą Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Kopiowanie danych do lub z Azure Data Lake Storage Gen1 przy użyciu Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Załaduj dane do Azure Data Lake Storage Gen2 z Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop
Sqoop to narzędzie przeznaczone do przesyłania danych między usługą Hadoop a relacyjnymi bazami danych. Można go użyć do zaimportowania danych z systemu zarządzania relacyjnymi bazami danych (RDBMS), takiego jak SQL Server, MySQL lub Oracle do rozproszonego systemu plików Hadoop (HDFS), Przekształć dane w usłudze Hadoop z MapReduce lub Hive, a następnie wyeksportować dane z powrotem do RDBMS.

Aby uzyskać więcej informacji, zobacz [Korzystanie z Sqoop z usługą HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Zestawy SDK programistyczne
Dostęp do usługi Azure Storage można również uzyskać przy użyciu zestawu Azure SDK z następujących języków programowania:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Aby uzyskać więcej informacji na temat instalowania zestawów Azure SDK, zobacz [Azure downloads](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a id="storageexception"></a>Wyjątek magazynu do zapisu na obiekcie blob
**Objawy**: W przypadku używania `hadoop` poleceń `hdfs dfs` lub do zapisywania plików, które są ~ 12 GB lub większe w klastrze HBase, może wystąpić następujący błąd:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Przyczyna**: HBase w klastrach usługi HDInsight domyślnie ma rozmiar bloku 256 KB podczas zapisywania w usłudze Azure Storage. Chociaż działa w przypadku interfejsów API HBase lub interfejsów API REST, powoduje to błąd podczas korzystania z `hadoop` narzędzi `hdfs dfs` wiersza polecenia lub.

**Rozpoznawanie**: Użyj `fs.azure.write.request.size` , aby określić większy rozmiar bloku. Można to zrobić na zasadzie użycia przy użyciu `-D` parametru. Następujące polecenie jest przykładem przy użyciu tego parametru z `hadoop` poleceniem:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Możesz również zwiększyć wartość `fs.azure.write.request.size` globalnie za pomocą platformy Apache Ambari. Poniższe kroki umożliwiają zmianę wartości w interfejsie użytkownika sieci Web Ambari:

1. W przeglądarce przejdź do interfejsu użytkownika sieci Web Ambari dla klastra. Jest `https://CLUSTERNAME.azurehdinsight.net`to, gdzie `CLUSTERNAME` jest nazwą klastra.

    Po wyświetleniu monitu wprowadź nazwę i hasło administratora klastra.
2. Z lewej strony ekranu wybierz pozycję **HDFS**, a następnie **Wybierz kartę konfiguracje** .
3. W polu **Filtr...** wprowadź `fs.azure.write.request.size`wartość. Spowoduje to wyświetlenie pola i bieżącej wartości w środku strony.
4. Zmień wartość z 262144 (256 KB) na nową wartość. Na przykład 4194304 (4 MB).

    ![Obraz zmiany wartości za pomocą interfejsu użytkownika sieci Web Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Aby uzyskać więcej informacji na temat korzystania z programu Ambari, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Kolejne kroki
Teraz, po zrozumieniu sposobu pobierania danych do usługi HDInsight, przeczytaj następujące artykuły, aby dowiedzieć się, jak przeprowadzić analizę:

* [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Programowe przesyłanie Apache Hadoop zadań](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Korzystanie z Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z Apache świni z usługą HDInsight](hadoop/hdinsight-use-pig.md)
