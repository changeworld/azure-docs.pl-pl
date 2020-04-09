---
title: Przesyłanie danych dla zadań Apache Hadoop w HDInsight
description: Dowiedz się, jak przesyłać i uzyskiwać dostęp do danych dla zadań Apache Hadoop w programie HDInsight. Użyj klasycznego interfejsu wiersza polecenia platformy Azure, Eksploratora usługi Azure Storage, usługi Azure PowerShell, wiersza polecenia Hadoop lub Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdiseo17may2017
ms.date: 04/07/2020
ms.openlocfilehash: c862633245e75613f9e4f9956486f872b96239f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875014"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Przesyłanie danych dla zadań Apache Hadoop w HDInsight

Usługa HDInsight udostępnia rozproszony system plików (HDFS) usługi Hadoop za pomocą usługi Azure Storage i usługi Azure Data Lake Storage. Ta pamięć masowa obejmuje Gen1 i Gen2. Usługi Azure Storage i Data Lake Storage Gen1 i Gen2 zostały zaprojektowane jako rozszerzenia hdfs. Umożliwiają one pełny zestaw składników w środowisku Hadoop do pracy bezpośrednio na danych, którymi zarządza. Usługa Azure Storage, Data Lake Storage Gen1 i Gen2 to odrębne systemy plików. Systemy są zoptymalizowane pod kątem przechowywania danych i obliczeń na tych danych. Aby uzyskać informacje na temat korzyści płynących z korzystania z usługi Azure Storage, zobacz [Korzystanie z usługi Azure Storage z usługą HDInsight](hdinsight-hadoop-use-blob-storage.md). Zobacz też, [Użyj funkcji Data Lake Storage Gen1 z hdinsight](hdinsight-hadoop-use-data-lake-store.md)i Użyj funkcji Data Lake Storage [Gen2 z hdinsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Wymagania wstępne

Należy zwrócić uwagę na następujące wymagania przed rozpoczęciem:

* Klaster usługi Azure HDInsight. Aby uzyskać instrukcje, zobacz [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) lub Tworzenie [klastrów HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Znajomość następujących artykułów:
    * [Korzystanie z usługi Azure Storage z usługą HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Korzystanie z usługi Data Lake Storage Gen1 z hdinsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Korzystanie z usługi Data Lake Storage Gen2 z hdinsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Przekazywanie danych do usługi Azure Storage

## <a name="utilities"></a>Narzędzia

Firma Microsoft udostępnia następujące narzędzia do pracy z usługą Azure Storage:

| Narzędzie | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Portal platformy Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Interfejs wiersza polecenia platformy Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy (Polski)](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Polecenie Hadoop](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Polecenie Hadoop jest dostępne tylko w klastrze HDInsight. Polecenie umożliwia tylko ładowanie danych z lokalnego systemu plików do usługi Azure Storage.  

## <a name="hadoop-command-line"></a>Wiersz polecenia Hadoop

Wiersz polecenia Hadoop jest przydatne tylko do przechowywania danych w obiekcie blob magazynu platformy Azure, gdy dane są już obecne w węźle głównym klastra.

Aby użyć polecenia Hadoop, należy najpierw podłączyć się do headnode za pomocą [SSH lub PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Po nawiązaniu połączenia można użyć następującej składni, aby przekazać plik do magazynu.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Na przykład: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Ponieważ domyślny system plików dla usługi HDInsight znajduje się w usłudze Azure Storage, /example/data/data.txt jest faktycznie w usłudze Azure Storage. Można również odwołać się do pliku jako:

    wasbs:///example/data/data.txt

lub

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Aby uzyskać listę innych poleceń Hadoop, które współpracują z plikami, zobacz[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> W klastrach Apache HBase domyślny rozmiar bloku używany podczas zapisywania danych wynosi 256 KB. Chociaż działa to poprawnie podczas korzystania z interfejsów API `hadoop` bazy `hdfs dfs` HBase lub interfejsów API REST, przy użyciu lub polecenia do zapisu danych większych niż ~12 GB powoduje błąd. Aby uzyskać więcej informacji, zobacz [wyjątek magazynu dla zapisu w obiekcie blob](#storage-exception-for-write-on-blob) sekcji w tym artykule.

## <a name="graphical-clients"></a>Klienci graficzni

Istnieje również kilka aplikacji, które zapewniają interfejs graficzny do pracy z usługą Azure Storage. Poniższa tabela zawiera listę kilku z tych aplikacji:

| Klient | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Narzędzia programu Microsoft Visual Studio dla usługi HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Eksplorator usługi Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer dla platformy Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Instalowanie usługi Azure Storage jako dysku lokalnego

Zobacz [Instalowanie usługi Azure Storage jako dysku lokalnego](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Przesyłanie za pomocą usług

### <a name="azure-data-factory"></a>Azure Data Factory

Usługa Azure Data Factory to w pełni zarządzana usługa do tworzenia danych: magazynowania, przetwarzania i przenoszenia usług w usprawnione, elastyczne i niezawodne potoki produkcji danych.

|Typ magazynu|Dokumentacja|
|----|----|
|Azure Blob Storage|[Kopiowanie danych do lub z magazynu obiektów Blob platformy Azure przy użyciu usługi Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Usługa Azure Data Lake Storage 1. generacji|[Kopiowanie danych do lub z usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Usługa Azure Data Lake Storage 2. generacji |[Ładowanie danych do usługi Azure Data Lake Storage Gen2 za pomocą usługi Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop to narzędzie przeznaczone do przesyłania danych między Hadoop i relacyjnych baz danych. Służy do importowania danych z relacyjnego systemu zarządzania bazami danych (RDBMS), takiego jak SQL Server, MySQL lub Oracle. Następnie do rozproszonego systemu plików Hadoop (HDFS). Przekształć dane w Hadoop z MapReduce lub Hive, a następnie wyeksportować dane z powrotem do RDBMS.

Aby uzyskać więcej informacji, zobacz [Korzystanie z sqoop z HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>SDK rozwoju

Usługa Azure Storage jest również dostępna przy użyciu narzędzia Azure SDK z następujących języków programowania:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Aby uzyskać więcej informacji na temat instalowania zestawów SDK platformy [Azure,](https://azure.microsoft.com/downloads/) zobacz Pobieranie platformy Azure

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="storage-exception-for-write-on-blob"></a>Wyjątek magazynu dla zapisu na obiekcie blob

**Symptomy**: Podczas `hadoop` `hdfs dfs` używania poleceń lub poleceń do zapisywania plików o masie ~12 GB lub większej w klastrze HBase może wystąpić następujący błąd:

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

**Przyczyna:** HBase w klastrach HDInsight domyślnie do rozmiaru bloku 256 KB podczas zapisywania do magazynu platformy Azure. Podczas gdy działa dla interfejsów API bazy HBase lub interfejsów `hadoop` API `hdfs dfs` REST, powoduje błąd podczas korzystania z narzędzi wiersza polecenia lub polecenia.

**Rozdzielczość:** `fs.azure.write.request.size` Służy do określania większego rozmiaru bloku. Tę modyfikację można wykonać na podstawie użycia `-D` przy użyciu parametru. Przykładem jest następujące polecenie używające `hadoop` tego parametru za pomocą polecenia:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Można również zwiększyć wartość `fs.azure.write.request.size` globalnie za pomocą Apache Ambari. Do zmiany wartości w interfejsie użytkownika sieci Web Ambari można wykonać następujące kroki:

1. W przeglądarce przejdź do interfejsu użytkownika sieci Web Ambari dla swojego klastra. Adres URL `https://CLUSTERNAME.azurehdinsight.net`to `CLUSTERNAME` , gdzie jest nazwa klastra. Po wyświetleniu monitu wprowadź nazwę administratora i hasło klastra.
2. Po lewej stronie ekranu wybierz pozycję **HDFS**, a następnie wybierz kartę **Konfiguracje.**
3. W polu **Filtr...** `fs.azure.write.request.size`wprowadź .
4. Zmień wartość z 262144 (256 KB) na nową wartość. Na przykład 4194304 (4 MB).

    ![Obraz zmiany wartości za pomocą interfejsu użytkownika sieci Web Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Aby uzyskać więcej informacji na temat korzystania z programu Ambari, zobacz [Zarządzanie klastrami HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zrozumiesz, jak uzyskać dane do usługi HDInsight, przeczytaj następujące artykuły, aby dowiedzieć się analizy:

* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Prześlij apache Hadoop oferty pracy programowo](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z programu Apache Pig z usługą HDInsight](./use-pig.md)
