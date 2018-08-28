---
title: Przekazywanie danych dla zadań Hadoop w usłudze HDInsight
description: Dowiedz się, jak przekazywać i uzyskać dostęp do danych dla zadań Hadoop w HDInsight przy użyciu wiersza polecenia platformy Azure, Eksplorator usługi Azure Storage, programu Azure PowerShell, wiersza polecenia usługi Hadoop lub narzędzia Sqoop.
keywords: hadoop etl, pobieranie danych do platformy hadoop, hadoop ładowanie danych
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.author: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 838444898500ce7127e1a9e999d0b5ee7b1e3687
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051828"
---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Przekazywanie danych dla zadań Hadoop w usłudze HDInsight

Usługa Azure HDInsight udostępnia w pełni funkcjonalne Rozproszony system plików Hadoop (HDFS) za pośrednictwem usługi Azure Storage i Azure Data Lake Store. Usługi Azure Storage i Data lake Store są przeznaczone jako rozszerzenie systemu plików HDFS, aby zapewnić bezproblemowe środowisko dla klientów. Umożliwiają one pełny zestaw składników w ekosystemie usługi Hadoop do operować bezpośrednio na danych, którymi zarządza. Usługa Azure Storage i Data Lake Store są systemów różnych plików, które są zoptymalizowane pod kątem magazynowania danych oraz obliczenia na tych danych. Aby uzyskać informacje o zaletach przy użyciu usługi Azure Storage, zobacz [używanie usługi Azure Storage za pomocą HDInsight] [ hdinsight-storage] i [Użyj Data Lake Store za pomocą HDInsight](hdinsight-hadoop-use-data-lake-store.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy uwzględnić następujące wymagania:

* Klaster usługi Azure HDInsight. Aby uzyskać instrukcje, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight] [ hdinsight-get-started] lub [klastrów HDInsight tworzenie](hdinsight-hadoop-provision-linux-clusters.md).
* Znajomość dwa następujące artykuły:

    - [Usługa Azure Storage za pomocą HDInsight][hdinsight-storage]
    - [Użyj Data Lake Store za pomocą HDInsight](hdinsight-hadoop-use-data-lake-store.md)

## <a name="upload-data-to-azure-storage"></a>Przekazywanie danych do usługi Azure Storage

### <a name="command-line-utilities"></a>Narzędzia wiersza polecenia
Firma Microsoft udostępnia następujące narzędzia do pracy z usługą Azure Storage:

| Narzędzie | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Interfejs wiersza polecenia platformy Azure][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] |✔ | |✔ |
| [Polecenia usługi Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> Gdy wiersza polecenia platformy Azure, programu Azure PowerShell i AzCopy mogą wszystkie zostać użyte spoza platformy Azure, polecenia Hadoop jest dostępna tylko w klastrze HDInsight. I to polecenie może składać się podczas ładowania danych z lokalnego systemu plików do usługi Azure Storage.
>
>

#### <a id="xplatcli"></a>Wiersza polecenia platformy Azure
Wiersza polecenia platformy Azure jest narzędziem dla wielu platform, które umożliwia zarządzanie usługami platformy Azure. Poniższe kroki umożliwiają przekazywanie danych do usługi Azure Storage:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Instalowanie i Konfigurowanie interfejsu wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows](../cli-install-nodejs.md).
2. Otwórz wiersz polecenia, bash lub innego powłoki i służą do uwierzytelniania w Twojej subskrypcji platformy Azure.

    ```cli
    azure login
    ```

    Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło dla Twojej subskrypcji.
3. Wprowadź następujące polecenie, aby wyświetlić listę kont magazynu dla Twojej subskrypcji:

    ```cli
    azure storage account list
    ```

4. Wybierz konto magazynu, zawierający obiekt blob, który chcesz pracować, a następnie użyj następującego polecenia, aby pobrać klucz dla tego konta:

    ```cli
    azure storage account keys list <storage-account-name>
    ```

    To polecenie zwraca **podstawowego** i **dodatkowej** kluczy. Kopiuj **głównej** wartość klucza, ponieważ będzie on używany w następnych krokach.
5. Aby pobrać listę kontenerów obiektów blob na koncie magazynu, użyj następującego polecenia:

    ```cli
    azure storage container list -a <storage-account-name> -k <primary-key>
    ```

6. Do przekazywania i pobierania plików do obiektu blob, użyj następujących poleceń:

   * Aby przekazać plik:

        ```cli
        azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
        ```

   * Aby pobrać plik:

        ```cli
        azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>
        ```
    
> [!NOTE]
> Jeśli zawsze pracujesz z tego samego konta magazynu, można ustawić następujące zmienne środowiskowe zamiast określania konta i klucza dla każdego polecenia:
>
> * **AZURE\_MAGAZYNU\_konta**: Nazwa konta magazynu
> * **AZURE\_MAGAZYNU\_dostępu\_klucza**: klucz konta magazynu
>
>

#### <a id="powershell"></a>Azure PowerShell
Azure PowerShell to środowisko obsługi skryptów, który umożliwia sterowanie oraz jej automatyzację wdrażania i zarządzania obciążeń na platformie Azure. Aby uzyskać informacji na temat konfigurowania stacji roboczej do uruchamiania programu Azure PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Aby przekazać plik lokalny do usługi Azure Storage**

1. Otwórz konsolę programu Azure PowerShell, zgodnie z instrukcją w [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
2. Ustaw wartości pierwszych pięć zmiennych w poniższym skrypcie:

    ```powershell
    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $fileName ="<LocalFileName>"
    $blobName = "<BlobName>"

    # Get the storage account key
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    # Create the storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    # Copy the file from local workstation to the Blob container
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
    ```

3. Wklej skrypt do konsoli programu Azure PowerShell i uruchom go, aby skopiować plik.

Na przykład skrypty programu PowerShell utworzone w celu pracy z HDInsight, zobacz [narzędzi HDInsight](https://github.com/blackmist/hdinsight-tools).

#### <a id="azcopy"></a>AzCopy
AzCopy to narzędzie wiersza polecenia, które upraszcza zadanie przesyłania danych do i z konta usługi Azure Storage. Można jej używać jako samodzielnego narzędzia lub włączyć to narzędzie w istniejącej aplikacji. [Pobierz narzędzia AzCopy][azure-azcopy-download].

Składnia narzędzia AzCopy jest następująca:

```command
AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]
```

Aby uzyskać więcej informacji, zobacz [AzCopy — przekazywanie/pobieranie plików obiektów blob Azure][azure-azcopy].

Narzędzie Azcopy w systemie Linux w wersji zapoznawczej jest dostępna.  Zobacz [ogłoszenie narzędzie AzCopy w systemie Linux w wersji zapoznawczej](https://blogs.msdn.microsoft.com/windowsazurestorage/2017/05/16/announcing-azcopy-on-linux-preview/).

#### <a id="commandline"></a>Wiersza polecenia usługi Hadoop
Wiersza polecenia usługi Hadoop przydaje się tylko do przechowywania danych do obiektu blob magazynu platformy Azure, gdy dane znajdują się już na głównym węzłem klastra.

Aby można było używać poleceń platformy Hadoop, musisz najpierw połączyć do węzła głównego przy użyciu jednej z następujących metod:

* **HDInsight oparte na Windows**: [nawiązywanie połączenia przy użyciu pulpitu zdalnego](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **HDInsight opartych na systemie Linux**: nawiązywanie połączenia przy użyciu [SSH lub programu PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Po nawiązaniu połączenia można użyć następującej składni, aby przekazać plik do magazynu.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Na przykład: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Ponieważ jest domyślnego systemu plików usługi HDInsight w usłudze Azure Storage, /example/data.txt jest faktycznie w usłudze Azure Storage. Mogą także odwoływać się do pliku jako:

    wasb:///example/data/data.txt

lub

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Aby uzyskać listę innych poleceń platformy Hadoop, współpracujących z plików Zobacz [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]
> W klastrach HBase domyślnie Blokuj rozmiar używany podczas zapisywania danych to 256 KB. Gdy działa to prawidłowo w przypadku korzystania z bazy danych HBase interfejsów API ani nowych interfejsów API REST, za pomocą `hadoop` lub `hdfs dfs` polecenia, aby zapisać danych większych niż ~ 12 GB powoduje wystąpienie błędu. Aby uzyskać więcej informacji, zobacz [wyjątek magazynu do zapisu dla obiektu blob](#storageexception) sekcję w tym artykule.
>
>

### <a name="graphical-clients"></a>Graficzny klientów
Dostępne są także kilka aplikacji, które zapewniają interfejs graficzny do pracy z usługą Azure Storage. Poniższa tabela znajduje się lista niektóre z tych aplikacji:

| Klient | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](http://storageexplorer.com/) |✔ |✔ |✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

#### <a name="visual-studio-tools-for-hdinsight"></a>Visual Studio Tools for HDInsight
Aby uzyskać więcej informacji, zobacz [Nawigacja po połączonych zasobach](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources).

#### <a id="storageexplorer"></a>Eksplorator usługi Azure Storage
*Eksplorator usługi Azure Storage* jest użytecznym narzędziem do inspekcji i zmieniania danych w obiektach blob. Jest to narzędzie bezpłatnej, typu open source, które można pobrać z [ http://storageexplorer.com/ ](http://storageexplorer.com/). Kod źródłowy jest dostępny z tego linku, jak również.

Przed rozpoczęciem korzystania z narzędzia, musisz wiedzieć, usługa Azure storage konta nazwy i klucza konta usługi. Aby uzyskać instrukcje dotyczące uzyskiwania tych informacji, zobacz "porady: wyświetlanie, kopiowanie i ponowne generowanie magazynu, klucze dostępu" części [tworzenia, zarządzania i usuwania konta magazynu][azure-create-storage-account].

1. Uruchom Eksploratora usługi Azure Storage. Po pierwszym uruchomieniu Eksploratora usługi Storage, zostanie wyświetlony monit o **nazwa konta _magazynów** i **klucza konta magazynu**. Jeśli uruchamiasz go przed użyj **Dodaj** przycisk, aby dodać nową nazwę konta magazynu i klucza.

    Wprowadź nazwę i klucz konta magazynu używanego przez klaster usługi HDInsight, a następnie wybierz pozycję **Otwórz z & APISZ**.

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. Na liście kontenerów z lewej strony interfejsu kliknij nazwę kontenera, który jest skojarzony z klastrem usługi HDInsight. Domyślnie jej jest nazwą klastra HDInsight, ale może być inny, jeśli określona nazwa wprowadzona podczas tworzenia klastra.
3. Na pasku narzędzi wybierz ikonę przekazywania.

    ![Pasek narzędzi z wyróżnioną ikonę przekazywania](./media/hdinsight-upload-data/toolbar.png)
4. Określ plik do przekazania, a następnie kliknij przycisk **Otwórz**. Po wyświetleniu monitu wybierz **przekazywanie** można przekazać pliku w katalogu głównym kontenera magazynu. Jeśli chcesz przekazać plik do określonej ścieżki, wprowadź ścieżkę w **docelowy** pola, a następnie wybierz pozycję **przekazywanie**.

    ![Okna dialogowego przekazywania pliku](./media/hdinsight-upload-data/fileupload.png)

    Po zakończeniu plik podczas przekazywania, można użyć go z zadań w klastrze HDInsight.

### <a name="mount-azure-storage-as-local-drive"></a>Instalowanie usługi Azure Storage jako dysk lokalny
Zobacz [Instalowanie usługi Azure Storage jako dysk lokalny](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

### <a name="upload-using-services"></a>Przekazywanie przy użyciu usług
#### <a name="azure-data-factory"></a>Azure Data Factory
Usługa Azure Data Factory jest w pełni zarządzana Usługa do redagowania usługi przenoszenia danych przechowywania, przetwarzania danych i danych, potoki danych zoptymalizowane, skalowalnej i niezawodnej do produkcji.

Usługa Azure Data Factory może służyć do przenoszenia danych do usługi Azure Storage lub do tworzenia potoków danych, które bezpośrednio przy użyciu funkcji HDInsight, takich jak Hive i Pig.

Aby uzyskać więcej informacji, zobacz [dokumentacji usługi Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

#### <a id="sqoop"></a>Apache Sqoop
Sqoop to narzędzie do transferu danych pomiędzy platformą Hadoop a relacyjnymi bazami danych. Służy do importowania danych z system zarządzania relacyjnymi bazami danych (RDBMS), takie jak SQL Server, MySQL lub Oracle do rozproszonego systemu plików Hadoop (HDFS), przekształcania danych na platformie Hadoop MapReduce lub Hive i następnie wyeksportować dane z powrotem w systemie RDBMS.

Aby uzyskać więcej informacji, zobacz [Sqoop korzystanie z HDInsight][hdinsight-use-sqoop].

### <a name="development-sdks"></a>Tworzenie zestawów SDK
Usługa Azure Storage można także uzyskać dostęp przy użyciu zestawu Azure SDK w następujących językach programowania:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Aby uzyskać więcej informacji na temat instalowania zestawów SDK usługi Azure, zobacz [Azure pliki do pobrania.](https://azure.microsoft.com/downloads/)

### <a name="troubleshooting"></a>Rozwiązywanie problemów
#### <a id="storageexception"></a>Wyjątek magazynu do zapisu dla obiektu blob
**Objawy**: korzystając z `hadoop` lub `hdfs dfs` polecenia, aby zapisywać pliki, które są ~ 12 GB lub więcej w klaster HBase, może wystąpić następujący błąd:

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

**Przyczyna**: na HDInsight klastrów HBase domyślne do rozmiaru bloku 256 KB podczas zapisywania do magazynu platformy Azure. Gdy działa w przypadku bazy danych HBase interfejsów API ani nowych interfejsów API REST, powoduje błąd przy użyciu `hadoop` lub `hdfs dfs` narzędzia wiersza polecenia.

**Rozpoznawanie**: Użyj `fs.azure.write.request.size` do określenia większy rozmiar bloku. Można to zrobić na podstawie użycia za pomocą `-D` parametru. Następujące polecenie jest przykładem dotyczącym używania tego parametru z `hadoop` polecenia:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Można również zwiększyć wartość `fs.azure.write.request.size` globalnie, za pomocą systemu Ambari. Zmień wartość w Interfejsie użytkownika sieci Web Ambari można następujące kroki:

1. W przeglądarce przejdź do Interfejsu sieci Web Ambari, dla klastra. Jest to https://CLUSTERNAME.azurehdinsight.net, gdzie **CLUSTERNAME** jest nazwą klastra.

    Po wyświetleniu monitu wprowadź nazwę administratora i hasło dla klastra.
2. Po lewej stronie ekranu, wybierz **HDFS**, a następnie wybierz pozycję **Configs** kartę.
3. W **filtr...**  wprowadź `fs.azure.write.request.size`. Spowoduje to wyświetlenie, pola i bieżącą wartość pośrodku strony.
4. Zmień wartość z 262144 (256KB) na nową wartość. Na przykład 4194304 (4MB).

![Obraz przedstawiający zmianę wartości przy użyciu interfejsu użytkownika sieci Web systemu Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Aby uzyskać więcej informacji na temat korzystania z narzędzia Ambari, zobacz [HDInsight Zarządzanie klastrami za pomocą interfejsu użytkownika sieci Web Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Kolejne kroki
Skoro już wiesz, jak pobierać dane do HDInsight, przeczytaj następujące artykuły, aby dowiedzieć się, jak wykonywać analizy:

* [Wprowadzenie do usługi Azure HDInsight][hdinsight-get-started]
* [Przesyłanie zadań Hadoop programowe][hdinsight-submit-jobs]
* [Korzystanie z programu Hive z usługą HDInsight][hdinsight-use-hive]
* [Korzystanie z języka Pig z usługą HDInsight][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]:../storage/common/storage-create-storage-account.md
[azure-azcopy-download]:../storage/common/storage-use-azcopy.md
[azure-azcopy]:../storage/common/storage-use-azcopy.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
