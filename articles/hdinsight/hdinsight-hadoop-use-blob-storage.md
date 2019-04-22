---
title: Wykonywanie zapytań dla danych z usługi Azure Storage zgodnej z systemem plików HDFS — Azure HDInsight
description: Dowiedz się, jak wykonywać zapytania o dane z magazynu platformy Azure i usługi Azure Data Lake Storage do przechowywania wyników analiz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 3356d3eee00a640efe10e2d9f3aa4fa7be775995
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360778"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Korzystanie z usługi Azure Storage w połączeniu z klastrami usługi Azure HDInsight

Aby analizować dane w klastrze HDInsight, można przechowywać danych albo w [usługi Azure Storage](../storage/common/storage-introduction.md), [usługi Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[usługi Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md), lub w połączeniu. Te opcje magazynu pozwalają bezpiecznie usuwać klastry HDInsight, które są używane do obliczeń bez utraty danych użytkownika.

Apache Hadoop obsługuje pojęcie domyślnego systemu plików. Domyślny system plików wyznacza domyślny schemat i element authority. Może również służyć do rozpoznawania ścieżek względnych. W trakcie procesu tworzenia klastra HDInsight w usłudze Azure Storage jako domyślny system plików można wskazać kontener obiektów blob lub usługi HDInsight 3.6, można wybrać usługę Azure Storage lub Azure Data Lake Storage ogólnego 1 / usługi Azure Data Lake Storage Gen 2 jako domyślne pliki system z pewnymi wyjątkami. Aby uzyskać możliwość wykorzystania Data Lake Storage Gen 1 jako domyślnej i połączonego magazynu, zobacz [dostępność dla klastra HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

W tym artykule omówiono współdziałanie usługi Azure Storage z klastrami usługi HDInsight. Aby dowiedzieć się, jak Data Lake Storage Gen 1 działa z klastrami HDInsight, zobacz [usługi użycia usługi Azure Data Lake Storage za pomocą usługi Azure HDInsight clusters](hdinsight-hadoop-use-data-lake-store.md). Aby uzyskać więcej informacji na temat tworzenia klastra usługi HDInsight, zobacz [klastrów utworzyć Apache Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Usługa Azure Storage to niezawodne rozwiązanie ogólnego przeznaczenia, które bezproblemowo integruje się z usługą HDInsight. Usługa HDInsight może używać kontenera obiektów blob w usłudze Azure Storage jako domyślnego systemu plików dla klastra. Korzystając z interfejsu rozproszonego systemu plików Hadoop (HDFS), pełny zestaw składników usługi HDInsight może operować bezpośrednio na danych ze strukturą lub bez niej przechowywanych jako obiekty blob.

> [!WARNING]  
> Podczas tworzenia konta usługi Azure Storage jest dostępnych kilka opcji. W poniższej tabeli zawarto informacje na temat opcji obsługiwanych z usługą HDInsight:

| Typ konta magazynu | Obsługiwane usługi | Warstwy wydajności obsługiwane | Warstwy dostępu obsługiwane |
|----------------------|--------------------|-----------------------------|------------------------|
| Ogólnego przeznaczenia w wersji 2   | Obiekt blob               | Standardowa (Standard)                    | Gorąca, chłodna, archiwum\*    |
| Ogólnego przeznaczenia w wersji 1   | Obiekt blob               | Standardowa (Standard)                    | ND                    |
| Blob Storage         | Obiekt blob               | Standardowa (Standard)                    | Gorąca, chłodna, archiwum\*    |

Używanie domyślnego kontenera obiektów blob do przechowywania danych firmowych nie jest zalecane. Dobrym rozwiązaniem jest usunięcie domyślnego kontenera obiektów blob po każdym użyciu, aby obniżyć koszty magazynowania. Domyślny kontener zawiera aplikacji i systemu dzienniki. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Udostępnianie pojedynczego kontenera obiektów blob jako domyślnego systemu plików dla wielu klastrów nie jest obsługiwane.
 
 > [!NOTE]  
 > Warstwa dostępu archiwum jest w trybie offline warstwę, która ma kilka opóźnieniem godzinę i nie jest zalecane do użycia z usługą HDInsight. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier">warstwę dostępu archiwum</a>.

Jeśli chcesz zabezpieczyć swoje konto magazynu z **zapory i sieci wirtualne** ograniczenia dotyczące **wybrane sieci**, pamiętaj włączyć wyjątek **Zezwalaj na zaufane firmy Microsoft usługi...**  tak, aby HDInsight można uzyskać dostępu do konta magazynu.

## <a name="hdinsight-storage-architecture"></a>Architektura magazynu usługi HDInsight
Na poniższym diagramie przedstawiono schemat architektury magazynu usługi HDInsight dotyczący korzystania z usługi Azure Storage:

![Klastry Hadoop używają interfejsu API systemu plików HDFS w celu dostępu do danych strukturalnych i bez struktury oraz przechowywania ich w usłudze Blob Storage.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "Architektura usługi DHInsight Storage")

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. Dostęp do tego systemu plików można uzyskać przy użyciu w pełni kwalifikowanego identyfikatora URI, na przykład:

    hdfs://<namenodehost>/<path>

Ponadto usługa HDInsight zapewnia możliwość dostępu do danych przechowywanych w usłudze Azure Storage. Składnia jest następująca:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Poniżej przedstawiono kilka zagadnień dotyczących korzystania z konta usługi Azure Storage w połączeniu z klastrami usługi HDInsight.

* **Kontenery na kontach magazynu, które są podłączone do klastra:** Ponieważ nazwa konta i klucz są kojarzone z klastrem podczas tworzenia, masz pełny dostęp do obiektów blob w tych kontenerach.

* **Publiczne kontenery lub publiczne obiekty BLOB na kontach magazynu, które nie są podłączone do klastra:** Masz uprawnienia tylko do odczytu do obiektów blob w kontenerach.
  
  > [!NOTE]  
  > Kontenery publiczne pozwalają na pobranie listy wszystkich obiektów blob, które są dostępne w danym kontenerze, oraz pobranie metadanych kontenera. Publiczne obiekty blob umożliwiają dostęp do obiektów blob jedynie osobom znającym dokładny adres URL. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources">zarządzanie dostępem do kontenerów i obiektów blob</a>.

* **Prywatne kontenery na kontach magazynu, które nie są podłączone do klastra:** Nie masz dostępu do obiektów blob w kontenerach, chyba że zdefiniujesz konto magazynu podczas przesyłania zadań WebHCat. Wyjaśnienie jest zawarte w dalszej części tego artykułu.

Konta magazynu definiowane w procesie tworzenia oraz ich klucze są przechowywane w pliku %HADOOP_HOME%/conf/core-site.xml w węzłach klastra. Domyślne działanie usługi HDInsight polega na korzystaniu z kont magazynu zdefiniowanych w pliku core-site.xml. Możesz zmodyfikować to ustawienie przy użyciu [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Wiele zadań WebHCat, w tym Apache Hive, MapReduce, przesyłanie strumieniowe usługi Apache Hadoop i Apache Pig, może przenosić Opis kont magazynu i metadane z nimi. (W przypadku technologii Pig obecnie działa to z kontami magazynu, ale nie dla metadanych). Aby uzyskać więcej informacji, zobacz [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (Używanie klastra usługi HDInsight z alternatywnymi kontami magazynu i magazynami metadanych).

Obiekty blob mogą być używane z danymi ze strukturą i bez niej. Kontenery obiektów blob przechowują dane jako pary klucz/wartość, bez hierarchii katalogów. Jednak wewnątrz nazwy klucza można użyć znaku ukośnika (/), co sprawi, że będzie wyglądała, jakby plik był przechowywany w ramach struktury katalogów. Na przykład klucz obiektu blob może mieć postać *input/log1.txt*. Katalog *input* w rzeczywistości nie istnieje, ale z powodu obecności znaku ukośnika w nazwie klucza, klucz ma wygląd ścieżki do pliku.

## <a id="benefits"></a>Korzyści z usługi Azure Storage
Wynikowy koszt wydajności związany z brakiem łączenia klastrów obliczeniowych i zasobów magazynu jest zmniejszany przez sposób tworzenia klastrów obliczeniowych w pobliżu zasobów konta magazynu w obrębie regionu świadczenia usługi Azure, gdzie szybkie sieci zapewniają wydajny dostęp węzłów obliczeniowych do danych wewnątrz usługi Azure Storage.

Przechowywanie danych w usłudze Azure Storage zamiast w systemie plików HDFS ma wiele zalet:

* **Ponowne użycie danych i udostępniania:** Dane w systemie plików HDFS znajdują się wewnątrz klastra obliczeniowego. Tylko te aplikacje, które mają dostęp do klastra obliczeniowego mogą używać danych za pomocą interfejsów API systemu plików HDFS. Dane w usłudze Azure Storage są dostępne za pośrednictwem interfejsów API systemu plików HDFS lub za pomocą [interfejsów API REST usługi Blob Storage][blob-storage-restAPI]. W związku z tym większy zestaw narzędzi i aplikacji (w tym inne klastry HDInsight) może służyć do tworzenia i wykorzystywania danych.
* **Archiwizacja danych:** Przechowywanie danych w usłudze Azure storage pozwala klastry HDInsight używane do obliczeń, można bezpiecznie usunąć bez utraty danych użytkownika.
* **Koszt magazynowania danych:** Przechowywanie danych w systemie plików DFS w perspektywie długoterminowej jest droższe niż przechowywanie danych w usłudze Azure storage, ponieważ koszt klastra obliczeniowego jest wyższy niż koszt usługi Azure storage. Ponadto ponieważ nie trzeba ponownie ładować danych przy każdej generacji klastra obliczeniowego, oszczędzamy również koszty ładowania danych.
* **Elastyczne skalowanie w poziomie:** Mimo że system plików HDFS zapewnia system plików skalowanych w poziomie, skala jest wyznaczana przez liczbę węzłów tworzonych dla klastra. Zmiana skali może stać się procesem bardziej skomplikowanym w porównaniu z elastycznymi możliwościami skalowania, które można uzyskać automatycznie w usłudze Azure Storage.
* **Replikacja geograficzna:** Usługi Azure storage można replikować geograficznie. Chociaż zapewnia to odzyskiwanie geograficzne i nadmiarowość danych, praca w trybie failover w przypadku lokalizacji zreplikowanych geograficznie poważnie wpływa na wydajność i może pociągać za sobą dodatkowe koszty. Dlatego zalecamy, aby wybierać replikację geograficzną w rozsądny sposób i tylko wtedy, gdy wartość danych uzasadnia ponoszenie dodatkowych kosztów.

Niektóre zadania i pakiety MapReduce mogą tworzyć wyniki pośrednie, których nie potrzeba przechowywać w usłudze Azure Storage. W takim przypadku można zdecydować się na przechowywanie danych w lokalnym systemie plików HDFS. W rzeczywistości HDInsight używa systemu plików DFS dla wielu wyników pośrednich w zadaniach Hive i innych procesach.

> [!NOTE]  
> Większość poleceń systemu plików HDFS (na przykład <b>ls</b>, <b>copyFromLocal</b> i <b>mkdir</b>) nadal działa zgodnie z oczekiwaniami. Tylko polecenia specyficzne dla natywnych implementacji systemu plików HDFS (określanych jako systemy plików DFS), takie jak <b>fschk</b> i <b>dfsadmin</b>, będą działać inaczej w usłudze Azure Storage.


## <a name="create-blob-containers"></a>Tworzenie kontenerów obiektów blob
Aby użyć obiektów blob, należy najpierw utworzyć [konto usługi Azure Storage][azure-storage-create]. W ramach tego procesu należy wskazać region świadczenia usługi Azure, w którym zostanie utworzone konto magazynu. Klaster i konto magazynu muszą być hostowane w tym samym regionie. Bazy danych programu SQL Server magazynu metadanych Hive i baza danych SQL Server magazynu metadanych programu Apache Oozie również muszą znajdować się w tym samym regionie.

Wszędzie tam, gdzie go umieszczono, każdy utworzony obiekt blob należy do kontenera na koncie usługi Azure Storage. Ten kontener może być istniejącym obiektem blob utworzonym poza usługą HDInsight lub może być kontenerem, który jest tworzony dla klastra usługi HDInsight.

Domyślny kontener obiektów blob przechowuje informacje dotyczące klastra, takie jak dzienniki i historia zadań. Nie należy współużytkować domyślnego kontenera obiektów blob dla wielu klastrów usługi HDInsight. Może to spowodować uszkodzenie historii zadań. Zalecane jest stosowanie różnych kontenerów do każdego klastra i umieszczanie udostępnionych danych w połączonym koncie magazynu określonym we wdrożeniu wszystkich odpowiednich klastrów zamiast domyślnego konta magazynu. Aby uzyskać więcej informacji na temat konfigurowania połączonych kont magazynu, zobacz artykuł [Tworzenie klastrów usługi HDInsight][hdinsight-creation]. Jednak po usunięciu oryginalnego klastra usługi HDInsight można ponownie użyć domyślnego kontenera magazynu. W przypadku klastrów HBase można zachować schemat tabeli HBase i dane przez utworzenie nowego klastra HBase przy użyciu domyślnego kontenera obiektów blob, używanego przez klaster HBase, który został usunięty.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Podczas tworzenia klastra usługi HDInsight za pomocą witryny Portal masz do wyboru dwa sposoby podania szczegółów konta magazynu, które przedstawiono poniżej. Można również określić, czy mają dodatkowe konto magazynu skojarzonego z klastrem, a jeśli tak, wybierz z magazynu usługi Data Lake lub inną usługę Azure Storage blob jako magazynu dodatkowego.

![HDInsight, hadoop, tworzenie źródła danych](./media/hdinsight-hadoop-use-blob-storage/storage.png)

> [!WARNING]  
> Korzystanie z dodatkowego konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.


### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Po [zainstalowaniu i skonfigurowaniu programu Azure PowerShell][powershell-install] można użyć następujących poleceń w wierszu polecenia programu Azure PowerShell, aby utworzyć konto magazynu i kontener:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 

    # Create default blob containers
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzStorageContainer -Name $containerName -Context $destContext

### <a name="use-azure-classic-cli"></a>Użyj klasycznego wiersza polecenia platformy Azure

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Jeśli masz [zainstalowany i skonfigurowany interfejs wiersza polecenia Azure Classic](../cli-install-nodejs.md), następujące polecenie może służyć do konta magazynu i kontener.

```cli
azure storage account create <storageaccountname> --type LRS
```

> [!NOTE]  
> Parametr `--type` wskazuje sposób replikacji konta magazynu. Aby uzyskać więcej informacji, zobacz artykuł [Azure Storage Replication](../storage/storage-redundancy.md) (Replikacja usługi Azure Storage). Nie używaj magazynu strefowo nadmiarowego, ponieważ nie obsługuje on stronicowych obiektów blob, plików, tabel ani kolejek.

Pojawi się monit o region geograficzny, w którym zostanie utworzone konto magazynu. Należy utworzyć konto magazynu w tym samym regionie, w którym planujesz utworzenie klastra usługi HDInsight.

Po utworzeniu konta magazynu użyj następującego polecenia w celu uzyskania kluczy do konta magazynu:

```cli
azure storage account keys list <storageaccountname>
```

Aby utworzyć kontener, użyj następującego polecenia:

```cli
azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>
```

## <a name="address-files-in-azure-storage"></a>Adresowanie plików w usłudze Azure Storage
Schemat identyfikatora URI do uzyskiwania dostępu do plików w usłudze Azure Storage z usługi HDInsight to:

```config
wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Schemat identyfikatora URI zapewnia nieszyfrowany dostęp (z prefiksem *wasb:*) oraz szyfrowany dostęp SSL (z prefiksem *wasbs*). Zalecamy używanie prefiksu *wasbs* wszędzie tam, gdzie to możliwe, nawet w przypadku uzyskiwania dostępu do danych, które znajdują się wewnątrz tego samego regionu w systemie Azure.

&lt;BlobStorageContainerName&gt; identyfikuje nazwę kontenera obiektów blob w usłudze Azure Storage.
&lt;StorageAccountName&gt; identyfikuje nazwę konta Azure Storage. Wymagana jest w pełni kwalifikowana nazwa domeny (FQDN).

Jeśli żadna z nazw &lt;BlobStorageContainerName&gt; ani &lt;StorageAccountName&gt; nie zostanie określona, używany jest domyślny system plików. W przypadku plików w domyślnym systemie plików można używać ścieżki względnej lub bezwzględnej. Na przykład do pliku *hadoop-mapreduce-examples.jar* dostarczanego z klastrami usługi HDInsight można odwoływać się w jeden z następujących sposobów:

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Nazwa pliku to <i>hadoop-examples.jar</i> w klastrach usługi HDInsight w wersji 2.1 i 1.6.

&lt;path&gt; jest nazwą ścieżki do pliku lub katalogu w systemie plików HDFS. Ponieważ kontenery w usłudze Azure storage przechowują pary klucz wartość, nie ma żadnych prawdziwy hierarchiczny system plików. Znak ukośnika (/) wewnątrz klucza obiektu blob jest interpretowany jako separator katalogu. Na przykład nazwą obiektu blob dla pliku *hadoop-mapreduce-examples.jar* jest:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Podczas pracy z obiektami blob poza usługą HDInsight, większość narzędzi nie rozpoznaje formatu WASB i zamiast tego oczekuje podstawowego formatu ścieżki, takiego jak `example/jars/hadoop-mapreduce-examples.jar`.

## <a name="access-blobs"></a>Dostęp do obiektów blob

### <a name="access-blobs-using-azure-powershell"></a> Korzystanie z programu Azure PowerShell

> [!NOTE]
> 
> Polecenia w tej sekcji stanowią podstawowy przykład użycia programu PowerShell w celu dostępu do danych przechowywanych w obiektach blob. Obszerniejszy przykład dostosowany do pracy z usługą HDInsight znajdziesz w artykule [HDInsight Tools](https://github.com/Blackmist/hdinsight-tools) (Narzędzia usługi HDInsight).

Użyj następującego polecenia, aby wyświetlić listę poleceń cmdlet związanych z obiektami blob:

```powershell 
Get-Command *blob*
```

![Lista poleceń cmdlet programu PowerShell związanych z obiektami blob.][img-hdi-powershell-blobcommands]

#### <a name="upload-files"></a>Przekazywanie plików

Zobacz [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data].

#### <a name="download-files"></a>Pobieranie plików

Następujący skrypt pobiera blokowy obiekt blob do bieżącego folderu. Przed uruchomieniem skryptu zmień katalog na folder, w którym masz uprawnienia do zapisu.

```powershell
$resourceGroupName = "<AzureResourceGroupName>"
$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
$blob = "example/data/sample.log" # The name of the blob to be downloaded.

# Use Add-AzureAccount if you haven't connected to your Azure subscription
Connect-AzAccount 
Select-AzSubscription -SubscriptionID "<Your Azure Subscription ID>"

Write-Host "Create a context object ... " -ForegroundColor Green
$storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

Write-Host "Download the blob ..." -ForegroundColor Green
Get-AzStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

Write-Host "List the downloaded file ..." -ForegroundColor Green
cat "./$blob"
```

Po podaniu nazwy grupy zasobów i nazwy klastra możesz użyć następującego kodu:

```powershell
$resourceGroupName = "<AzureResourceGroupName>"
$clusterName = "<HDInsightClusterName>"
$blob = "example/data/sample.log" # The name of the blob to be downloaded.

$cluster = Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
$defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
$defaultStorageContainer = $cluster.DefaultStorageContainer
$storageContext = New-AzStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 

Write-Host "Download the blob ..." -ForegroundColor Green
Get-AzStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force
```

#### <a name="delete-files"></a>Usuwanie plików

```powershell
Remove-AzStorageBlob -Container $containerName -Context $storageContext -blob $blob
```

#### <a name="list-files"></a>Lista plików

```powershell
Get-AzStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"
```

#### <a name="run-hive-queries-using-an-undefined-storage-account"></a>Uruchamianie zapytań Hive przy użyciu niezdefiniowanego konta magazynu

Ten przykład przedstawia sposób wyświetlania zawartości folderu z konta magazynu, które nie zostało zdefiniowane podczas procesu tworzenia.

```powershell
$clusterName = "<HDInsightClusterName>"

$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

$undefinedStorageKey = Get-AzStorageKey $undefinedStorageAccount | %{ $_.Primary }

Use-AzHDInsightCluster $clusterName

$defines = @{}
$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

Invoke-AzHDInsightHiveJob -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
```

### <a name="use-azure-classic-cli"></a>Użyj klasycznego wiersza polecenia platformy Azure

Użyj następującego polecenia, aby wyświetlić listę poleceń związanych z obiektami blob:

```cli
azure storage blob
```

**Przykład użycia klasyczny interfejs wiersza polecenia platformy Azure, aby przekazać plik**

```cli
azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**Przykład użycia klasyczny interfejs wiersza polecenia platformy Azure, aby pobrać plik**

```cli
azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**Przykład użycia klasyczny interfejs wiersza polecenia platformy Azure, aby usunąć plik**

```cli
azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**Przykład użycia klasyczny interfejs wiersza polecenia platformy Azure, aby wyświetlić listę plików**

```cli
azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>
```

## <a name="use-additional-storage-accounts"></a>Używanie dodatkowych kont magazynu

Podczas tworzenia klastra usługi HDInsight należy wskazać konto usługi Azure Storage, które ma zostać skojarzone z tym klastrem. Oprócz tego konta magazynu można dodać dodatkowe konta magazynu z tej samej subskrypcji platformy Azure lub różnych subskrypcji platformy Azure podczas procesu tworzenia lub po utworzeniu klastra. Aby uzyskać instrukcje dotyczące dodawania kolejnych kont magazynu, zobacz [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Korzystanie z dodatkowego konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób korzystania z magazynu Azure zgodnego z systemem plików HDFS w połączeniu z usługą HDInsight. Podane tu informacje umożliwiają tworzenie skalowalnych, długoterminowych rozwiązań do pozyskiwania danych archiwalnych i używanie usługi HDInsight w celu efektywnego wykorzystywania informacji przechowywanych w postaci danych ze strukturą i bez niej.

Aby uzyskać więcej informacji, zobacz:

* [Wprowadzenie do usługi Azure HDInsight][hdinsight-get-started]
* [Wprowadzenie do usługi Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data]
* [Korzystanie z programu Apache Hive z usługą HDInsight][hdinsight-use-hive]
* [Korzystanie z programu Apache Pig z usługą HDInsight][hdinsight-use-pig]
* [Use Azure Storage Shared Access Signatures to restrict access to data with HDInsight][hdinsight-use-sas] (Używanie sygnatur dostępu współdzielonego do usługi Azure Storage, aby ograniczyć dostęp do danych za pomocą usługi HDInsight)
* [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
