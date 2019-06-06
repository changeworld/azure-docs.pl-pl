---
title: Przekazywanie danych na potrzeby zadań usługi Apache Hadoop w HDInsight
description: Dowiedz się, jak przekazywać i dostęp do danych dla zadań technologii Apache Hadoop w HDInsight przy użyciu platformy Azure klasyczny interfejs wiersza polecenia, Eksplorator usługi Azure Storage, programu Azure PowerShell, wiersza polecenia usługi Hadoop lub narzędzia Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdiseo17may2017
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 0dbd5a886e2369d29a568eca47dda5558f43c8cd
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479141"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Przekazywanie danych na potrzeby zadań usługi Apache Hadoop w HDInsight

Usługa Azure HDInsight udostępnia w pełni funkcjonalne Rozproszony system plików Hadoop (HDFS) za pośrednictwem usługi Azure Storage i usługi Azure Data Lake Storage (Gen1 i Gen2). Usługa Azure Storage i Data Lake Storage Gen1 i Gen2 mają jako rozszerzenia systemu plików HDFS zapewnia bezproblemową obsługę klientów. Umożliwiają one pełny zestaw składników w ekosystemie usługi Hadoop do operować bezpośrednio na danych, którymi zarządza. Usługi Azure Storage, Data Lake Storage Gen1 i Gen2 są systemów różnych plików, które są zoptymalizowane pod kątem magazynowania danych oraz obliczenia na tych danych. Aby uzyskać informacje o zaletach przy użyciu usługi Azure Storage, zobacz [używanie usługi Azure Storage za pomocą HDInsight](hdinsight-hadoop-use-blob-storage.md), [Użyj Data Lake Storage Gen1 z HDInsight](hdinsight-hadoop-use-data-lake-store.md), i [Użyj Data Lake magazynu Gen2 z HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy uwzględnić następujące wymagania:

* Klaster usługi Azure HDInsight. Aby uzyskać instrukcje, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) lub [klastrów HDInsight tworzenie](hdinsight-hadoop-provision-linux-clusters.md).
* Znajomość następujących artykułach:

    - [Usługa Azure Storage za pomocą HDInsight](hdinsight-hadoop-use-blob-storage.md)
    - [Za pomocą usług Data Lake Storage Gen1 HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    - [Za pomocą usług Data Lake Storage Gen2 HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Przekazywanie danych do usługi Azure Storage

## <a name="utilities"></a>Usługi użyteczności publicznej
Firma Microsoft udostępnia następujące narzędzia do pracy z usługą Azure Storage:

| Narzędzie | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Interfejs wiersza polecenia platformy Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [Narzędzie AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Polecenia usługi Hadoop](#commandline) |✔ |✔ |✔ |


> [!NOTE]  
> Polecenia usługi Hadoop jest dostępna tylko w klastrze HDInsight. Polecenie zezwala na tylko podczas ładowania danych z lokalnego systemu plików do usługi Azure Storage.  


## <a id="commandline"></a>Wiersza polecenia usługi Hadoop
Wiersza polecenia usługi Hadoop przydaje się tylko do przechowywania danych do obiektu blob magazynu platformy Azure, gdy dane znajdują się już na głównym węzłem klastra.

Aby można było używać poleceń platformy Hadoop, musisz najpierw połączyć do węzła głównego przy użyciu [SSH lub programu PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Po nawiązaniu połączenia można użyć następującej składni, aby przekazać plik do magazynu.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Na przykład: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Ponieważ jest domyślnego systemu plików usługi HDInsight w usłudze Azure Storage, /example/data.txt jest faktycznie w usłudze Azure Storage. Mogą także odwoływać się do pliku jako:

    wasbs:///example/data/data.txt

lub

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Aby uzyskać listę innych poleceń platformy Hadoop, współpracujących z plików Zobacz [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> W klastrach bazy danych Apache HBase domyślnie Blokuj rozmiar używany podczas zapisywania danych to 256 KB. Gdy działa to prawidłowo w przypadku korzystania z bazy danych HBase interfejsów API ani nowych interfejsów API REST, za pomocą `hadoop` lub `hdfs dfs` polecenia, aby zapisać danych większych niż ~ 12 GB powoduje wystąpienie błędu. Aby uzyskać więcej informacji, zobacz [wyjątek magazynu do zapisu dla obiektu blob](#storageexception) sekcję w tym artykule.

## <a name="graphical-clients"></a>Graficzny klientów
Dostępne są także kilka aplikacji, które zapewniają interfejs graficzny do pracy z usługą Azure Storage. Poniższa tabela znajduje się lista niektóre z tych aplikacji:

| Klient | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Eksplorator cloudBerry dla platformy Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |


## <a name="mount-azure-storage-as-local-drive"></a>Instalowanie usługi Azure Storage jako dysk lokalny
Zobacz [Instalowanie usługi Azure Storage jako dysk lokalny](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Przekazywanie przy użyciu usług
### <a name="azure-data-factory"></a>Azure Data Factory
Usługa Azure Data Factory jest w pełni zarządzana Usługa do redagowania usługi przenoszenia danych przechowywania, przetwarzania danych i danych, potoki danych zoptymalizowane, skalowalnej i niezawodnej do produkcji.

|Typ magazynu|Dokumentacja|
|----|----|
|Azure Blob Storage|[Kopiowanie danych do i z usługi Azure Blob storage za pomocą usługi Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Usługa Azure Data Lake Storage 1. generacji|[Kopiowanie danych z usługi Azure Data Lake Storage Gen1 lub za pomocą usługi Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Usługa Azure Data Lake Storage 2. generacji |[Ładowanie danych do usługi Azure Data Lake Storage Gen2 za pomocą usługi Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop
Sqoop to narzędzie do transferu danych pomiędzy platformą Hadoop a relacyjnymi bazami danych. Służy do importowania danych z system zarządzania relacyjnymi bazami danych (RDBMS), takie jak SQL Server, MySQL lub Oracle do rozproszonego systemu plików Hadoop (HDFS), przekształcania danych na platformie Hadoop MapReduce lub Hive i następnie wyeksportować dane z powrotem w systemie RDBMS.

Aby uzyskać więcej informacji, zobacz [Sqoop korzystanie z HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Tworzenie zestawów SDK
Usługa Azure Storage można także uzyskać dostęp przy użyciu zestawu Azure SDK w następujących językach programowania:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Aby uzyskać więcej informacji na temat instalowania zestawów SDK usługi Azure, zobacz [Azure pliki do pobrania.](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a id="storageexception"></a>Wyjątek magazynu do zapisu dla obiektu blob
**Objawy**: Korzystając z `hadoop` lub `hdfs dfs` polecenia, aby zapisywać pliki, które są ~ 12 GB lub więcej w klaster HBase, może wystąpić następujący błąd:

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

**Przyczyna**: Podczas zapisywania do magazynu platformy Azure, domyślne do rozmiaru bloku 256 KB klastrów HBase na HDInsight. Gdy działa w przypadku bazy danych HBase interfejsów API ani nowych interfejsów API REST, powoduje błąd przy użyciu `hadoop` lub `hdfs dfs` narzędzia wiersza polecenia.

**Rozpoznawanie**: Użyj `fs.azure.write.request.size` do określenia większy rozmiar bloku. Można to zrobić na podstawie użycia za pomocą `-D` parametru. Następujące polecenie jest przykładem dotyczącym używania tego parametru z `hadoop` polecenia:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Można również zwiększyć wartość `fs.azure.write.request.size` globalnie, przy użyciu Apache Ambari. Zmień wartość w Interfejsie użytkownika sieci Web Ambari można następujące kroki:

1. W przeglądarce przejdź do Interfejsu sieci Web Ambari, dla klastra. Jest to `https://CLUSTERNAME.azurehdinsight.net`, gdzie `CLUSTERNAME` jest nazwą klastra.

    Po wyświetleniu monitu wprowadź nazwę administratora i hasło dla klastra.
2. Po lewej stronie ekranu, wybierz **HDFS**, a następnie wybierz pozycję **Configs** kartę.
3. W **filtr...**  wprowadź `fs.azure.write.request.size`. Spowoduje to wyświetlenie, pola i bieżącą wartość pośrodku strony.
4. Zmień wartość z 262144 (256 KB) na nową wartość. Na przykład 4194304 (4 MB).

    ![Obraz przedstawiający zmianę wartości przy użyciu interfejsu użytkownika sieci Web systemu Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Aby uzyskać więcej informacji na temat korzystania z narzędzia Ambari, zobacz [HDInsight Zarządzanie klastrami przy użyciu Interfejsu sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Kolejne kroki
Skoro już wiesz, jak pobierać dane do HDInsight, przeczytaj następujące artykuły, aby dowiedzieć się, jak wykonywać analizy:

* [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Przesyłanie zadań Apache Hadoop programowe](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Use Apache Hive z HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hadoop/hdinsight-use-pig.md)
