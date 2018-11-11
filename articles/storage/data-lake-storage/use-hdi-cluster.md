---
title: Użyj usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza) przy użyciu klastrów usługi Azure HDInsight
description: Dowiedz się, jak wykonywać zapytania na danych z usługi Azure Data Lake Gen2 — wersja zapoznawcza i przechowywać wyniki analiz.
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: 04e2e32de90283da2563395f8b24dbb4b1dab888
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241763"
---
# <a name="use-azure-data-lake-storage-gen2-preview-with-azure-hdinsight-clusters"></a>Użyj usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza) przy użyciu klastrów usługi Azure HDInsight

Aby analizować dane w klastrze HDInsight, można przechowywać dane w dowolnej kombinacji usługi Azure Storage, Azure Data Lake Storage Gen1 lub Azure Data Lake Gen2 — wersja zapoznawcza. Wszystkie opcje magazynowania pozwalają bezpiecznie usuwać klastry HDInsight, które są używane do obliczeń bez utraty danych użytkownika.

Platforma Hadoop obsługuje pojęcie domyślnego systemu plików. Domyślny system plików wyznacza domyślny schemat i element authority. Może również służyć do rozpoznawania ścieżek względnych. W trakcie procesu tworzenia klastra HDInsight można wskazać kontener obiektów blob w usłudze Azure Storage lub Azure Data Lake Storage jako domyślnego systemu plików. Można również HDInsight w wersji 3.5 usługi możesz można wybrać usługę Azure Storage lub Azure Data Lake Storage jako domyślny system plików z pewnymi wyjątkami.

W tym artykule dowiesz się, jak usługi Azure Data Lake Storage Gen2 działa z klastrami HDInsight. Aby uzyskać więcej informacji na temat tworzenia klastra usługi HDInsight, zobacz [Konfigurowanie HDInsight clusters, za pomocą usługi Azure Data Lake Storage za pomocą usługi Hadoop, Spark, Kafka i](quickstart-create-connect-hdi-cluster.md).

Usługa Azure Storage to niezawodne rozwiązanie ogólnego przeznaczenia, które bezproblemowo integruje się z usługą HDInsight. HDInsight można użyć usługi Azure Data Lake Storage jako domyślny system plików dla klastra. Korzystając Hadoop interfejsu rozproszonego systemu plików (hadoop HDFS) pełny zestaw składników w HDInsight może operować bezpośrednio na pliki w usłudze Azure Data Lake Storage.

Firma Microsoft nie zaleca się używać domyślnego systemu plików do przechowywania danych biznesowych. Usuwanie domyślnego systemu plików, po każdym użyciu, aby zmniejszyć koszt przechowywania jest dobrym rozwiązaniem. Należy pamiętać, że domyślny kontener zawiera aplikacji i systemu dzienniki. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Udostępnianie jednego systemu plików dla wielu klastrów nie jest obsługiwane.

## <a name="hdinsight-storage-architecture"></a>Architektura magazynu usługi HDInsight

Na poniższym diagramie przedstawiono schemat architektury magazynu usługi HDInsight dotyczący korzystania z usługi Azure Storage:

![Klastry Hadoop używają interfejsu API systemu plików HDFS w celu dostępu do danych strukturalnych i bez struktury oraz przechowywania ich w usłudze Blob Storage.](./media/use-hdi-cluster/HDI.ABFS.Arch.png "Architektura usługi DHInsight Storage")

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. Dostęp do tego systemu plików można uzyskać przy użyciu w pełni kwalifikowanego identyfikatora URI, na przykład:

    hdfs://<NAME_NODE_HOST>/<PATH>

Ponadto HDInsight umożliwia dostęp do danych przechowywanych w usłudze Azure Data Lake Storage. Składnia jest następująca:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<path>

Poniżej przedstawiono kilka kwestii, korzystając z konta usługi Azure Storage z klastrami HDInsight.

* **Pliki w ramach kont magazynu, które są podłączone do klastra** mają nazwę konta i klucz skojarzony z klastrem podczas tworzenia. Ta konfiguracja zapewnia pełny dostęp do plików w systemie plików.

* **Pliki publiczne w ramach kont magazynu, które nie są podłączone do klastra** udostępnić uprawnienia tylko do odczytu do plików w systemie plików.
  
  > [!NOTE]
  > Systemy plików publiczne pozwalają na pobranie listy wszystkich plików dostępnych w systemie plików, a dostęp do metadanych. Systemy plików publiczne pozwalają uzyskać dostęp do plików tylko wtedy, gdy znasz adres URL. Aby uzyskać więcej informacji, zobacz [ograniczyć dostęp do kontenerów i obiektów blob](https://msdn.microsoft.com/library/windowsazure/dd179354.aspx) (reguły dla kontenerów i obiektów blob pracy tego samego planu plików i systemu plików).
 
* **Systemy plików prywatne w ramach kont magazynu, które nie są podłączone do klastra** nie zezwalają na dostęp do plików w systemie plików, chyba że zdefiniujesz konto magazynu podczas przesyłania zadań WebHCat. Przyczyny tego ograniczenia zostały omówione w dalszej części tego artykułu.

Konta magazynu, które są zdefiniowane w procesie tworzenia oraz ich klucze są przechowywane w *%HADOOP_HOME%/conf/core-site.xml* w węzłach klastra. Domyślne zachowanie HDInsight jest użycie kont magazynu zdefiniowanych w *core-site.xml* pliku. To ustawienie możesz zmodyfikować przy użyciu narzędzia [Ambari](../../hdinsight/hdinsight-hadoop-manage-ambari.md)

Wiele zadań WebHCat, w tym Hive, MapReduce, przesyłanie strumieniowe Hadoop, a także Pig, może przenosić ze sobą opis kont magazynu i metadane. (To podejście obecnie działa dla technologii Pig z kontami magazynu, ale nie dla metadanych.) Aby uzyskać więcej informacji, zobacz [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (Używanie klastra usługi HDInsight z alternatywnymi kontami magazynu i magazynami metadanych).

## <a id="benefits"></a>Korzyści z usługi Azure Storage

Wynikowy koszt wydajności związany z brakiem łączenia klastrów obliczeniowych i zasobów magazynu jest zmniejszany przez sposób tworzenia klastrów obliczeniowych w pobliżu zasobów konta magazynu w obrębie regionu świadczenia usługi Azure, gdzie szybkie sieci zapewniają wydajny dostęp węzłów obliczeniowych do danych wewnątrz usługi Azure Storage.

Przechowywanie danych w usłudze Azure Storage zamiast w systemie plików HDFS ma wiele zalet:

* **Udostępnianie i ponowne użycie danych:** dane w systemie plików HDFS znajdują się wewnątrz klastra obliczeniowego. Tylko te aplikacje, które mają dostęp do klastra obliczeniowego mogą używać danych za pomocą interfejsów API systemu plików HDFS. Dane w usłudze Azure Storage są dostępne za pośrednictwem interfejsów API systemu plików HDFS lub za pomocą [interfejsów API REST usługi Blob Storage][blob-storage-restAPI]. W związku z tym większy zestaw narzędzi i aplikacji (w tym inne klastry HDInsight) może służyć do tworzenia i wykorzystywania danych.

* **Archiwizacja danych:** przechowywanie danych w usłudze Azure Storage pozwala bezpiecznie usuwać klastry usługi HDInsight używane do obliczeń bez utraty danych użytkownika.

* **Koszt magazynowania danych:** przechowywanie danych w natywnej systemu plików HDFS długoterminowe jest droższe niż przechowywanie danych w usłudze Azure storage, ponieważ koszt klastra obliczeniowego jest wyższy niż koszt usługi Azure storage. Ponadto ponieważ nie trzeba ponownie ładować danych przy każdej generacji klastra obliczeniowego, oszczędzamy również koszty ładowania danych.

* **Elastyczne skalowanie w poziomie:** chociaż system plików HDFS zapewnia skalowanie w poziomie, skala jest wyznaczana przez liczbę węzłów tworzonych dla klastra. Zmiana skali może stać się procesem bardziej skomplikowanym w porównaniu z elastycznymi możliwościami skalowania, które można uzyskać automatycznie w usłudze Azure Storage.

* **Replikacja geograficzna:** danych usługi Azure storage można replikować geograficznie. Chociaż ta możliwość udostępnia odzyskiwanie geograficzne i nadmiarowość danych, Obsługa trybu failover do lokalizacji zreplikowanych geograficznie poważnie wpływa na wydajność i może spowodować naliczenie dodatkowych kosztów. W związku z tym, wybierz opcję replikacji geograficznej, ostrożnie i tylko jeśli wartość danych jest ponoszenie dodatkowych kosztów.

* **Zarządzanie cyklem życia danych:** wszystkie dane w dowolnym systemie plików, przechodzi przez własny cyklu życia. Dane często rozpoczyna się bardzo cenne i rzadziej, przechodzi do mniej wartościowe co wymaga mniej dostępu i ostatecznie wymaga archiwum lub usunięcia. Usługa Azure Storage zapewnia obsługę warstw danych i cyklu życia zasad zarządzania, które dane mają być odpowiednio warstwy na etapie jej cyklu życia.

Niektóre zadania i pakiety MapReduce mogą tworzyć wyniki pośrednie, których nie potrzeba przechowywać w usłudze Azure Storage. W takim przypadku można zdecydować się na przechowywanie danych w lokalnym systemie plików HDFS. W rzeczywistości HDInsight używa natywnych implementacji systemu plików HDFS (określanych jako systemu plików DFS) dla wielu wyników pośrednich w zadaniach Hive i innych procesów.

> [!NOTE]
> Większość poleceń systemu plików HDFS (na przykład `ls`, `copyFromLocal` i `mkdir`) nadal działa zgodnie z oczekiwaniami. Tylko polecenia specyficzne dla systemu plików DFS, takich jak `fschk` i `dfsadmin`, Pokaż różne zachowanie w usłudze Azure storage.

## <a name="create-an-data-lake-storage-file-system"></a>Tworzenie system plików usługi Data Lake Storage

Aby korzystać z systemu plików, należy najpierw utworzyć [konta usługi Azure Storage][azure-storage-create]. W ramach tego procesu możesz określić region platformy Azure, w której jest tworzone konto magazynu. Klaster i konto magazynu muszą być hostowane w tym samym regionie. Baza danych SQL Server na potrzeby magazynu metadanych Hive i baza danych SQL Server na potrzeby magazynu metadanych Oozie również muszą znajdować się w tym samym regionie.

Wszędzie tam, gdzie go umieszczono, każdy utworzony obiekt blob należy do systemu plików na Twoim koncie usługi Azure Data Lake Storage. 

Domyślny system plików usługi Data Lake Storage przechowuje informacje specyficzne dla klastra, takie jak dzienniki i historię zadań. Nie udostępniaj domyślnego systemu plików usługi Data Lake Storage przy użyciu wielu klastrów HDInsight. Może to spowodować uszkodzenie historii zadań. Zalecane jest, aby użyć innego systemu plików dla każdego klastra i umieszczanie udostępnionych danych w połączonym koncie magazynu określonym we wdrożeniu wszystkich odpowiednich klastrów zamiast domyślnego konta magazynu. Aby uzyskać więcej informacji na temat konfigurowania połączonych kont magazynu, zobacz artykuł [Tworzenie klastrów usługi HDInsight][hdinsight-creation]. Jednak po usunięciu oryginalnego klastra HDInsight można ponownie użyć domyślnego systemu plików magazynu. W przypadku klastrów HBase można zachować schemat tabeli HBase i dane przez utworzenie nowego klastra HBase przy użyciu domyślnego kontenera obiektów blob, używanego przez usunięto klaster HBase, który został usunięty.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Podczas tworzenia klastra usługi HDInsight z poziomu portalu, musisz opcje (jak pokazano na poniższym zrzucie ekranu) podaj szczegóły konta magazynu. Można również określić, czy mają dodatkowe konto magazynu skojarzone z klastrem, a jeśli tak, wybierz opcję z dowolnej opcji dostępnego magazynu dodatkowego miejsca do magazynowania.

![HDInsight, hadoop, tworzenie źródła danych](./media/use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> Korzystanie z dodatkowego konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Jeśli użytkownik [zainstalowaniu i skonfigurowaniu programu Azure PowerShell][powershell-install], poniższy kod w wierszu polecenia programu Azure PowerShell można użyć, aby utworzyć konto magazynu i kontener:

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "WEST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
      -Name StorageAccountName `
      -Location $Location `
      -SkuName Standard_LRS `
      -Kind StorageV2 
      -HierarchialNamespace $True

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

> [!NOTE]
> Utworzenie kontenera jest synonimem tworzenie systemu plików w usłudze Azure Data Lake Storage.

### <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!INCLUDE [use-latest-version](../../../includes/hdinsight-use-latest-cli.md)]

Jeśli masz [zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure](../../cli-install-nodejs.md), możesz zastosować następujące polecenie do konta magazynu i kontenera.

```bash
az storage account create \
    --name <STORAGE_ACCOUNT_NAME> \
    --resource-group <RESOURCE_GROUP_NAME> \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --Enable-hierarchical-namespace true
```

> [!NOTE]
> W publicznej wersji zapoznawczej programu Data Lake Storage Gen2 tylko `--sku Standard_LRS` jest obsługiwana.

Pojawi się monit o region geograficzny, w którym zostanie utworzone konto magazynu. Utwórz konto magazynu, w tym samym regionie, w którym planujesz utworzenie klastra usługi HDInsight.

Po utworzeniu konta magazynu użyj następującego polecenia w celu uzyskania kluczy do konta magazynu:

    azure storage account keys list <STORAGE_ACCOUNT_NAME>

Aby utworzyć kontener, użyj następującego polecenia:

    azure storage container create <CONTAINER_NAME> --account-name <STORAGE_ACCOUNT_NAME> --account-key <STORAGE_ACCOUNT_KEY>

> [!NOTE]
> Utworzenie kontenera jest synonimem tworzenie systemu plików w usłudze Azure Data Lake Storage.

## <a name="address-files-in-azure-storage"></a>Adresowanie plików w usłudze Azure Storage

Schemat identyfikatora URI do uzyskiwania dostępu do plików w usłudze Azure Storage z usługi HDInsight to:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>

Schemat identyfikatora URI zapewnia nieszyfrowany dostęp (za pomocą *abfs:* prefiks) oraz szyfrowany dostęp SSL (z *abfss*). Firma Microsoft zaleca używanie *abfss* wszędzie tam, gdzie to możliwe, nawet w przypadku uzyskiwania dostępu do danych, który znajduje się w tym samym regionie platformy Azure.

* &lt;FILE_SYSTEM_NAME&gt; identyfikuje ścieżka systemu plików usługi Azure Data Lake Storage.
* &lt;ACCOUNT_NAME&gt; identyfikuje nazwę konta usługi Azure Storage. Wymagana jest w pełni kwalifikowana nazwa domeny (FQDN).

    Jeśli wartości &lt;FILE_SYSTEM_NAME&gt; ani &lt;ACCOUNT_NAME&gt; został określony, używany jest domyślny system plików. W przypadku plików w domyślnym systemie plików można używać ścieżki względnej lub bezwzględnej. Na przykład *hadoop-mapreduce-examples.jar* pliku, który jest dostarczany z klastrami HDInsight mogą się odwoływać przy użyciu jednej z następujących ścieżek:
    
        abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
        abfs:///example/jars/hadoop-mapreduce-examples.jar
        /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> Nazwa pliku to *hadoop-examples.jar* w klastrach usługi HDInsight w wersji 2.1 i 1.6.

* &lt;Ścieżki&gt; jest nazwą ścieżki pliku lub katalogu HDFS.

> [!NOTE]
> Podczas pracy z plikami poza HDInsight, większość narzędzi nie rozpoznają ABFS sformatowanie i zamiast tego oczekuje podstawowego formatu ścieżki, takich jak `example/jars/hadoop-mapreduce-examples.jar`.
 
## <a name="use-additional-storage-accounts"></a>Używanie dodatkowych kont magazynu

Podczas tworzenia klastra usługi HDInsight należy wskazać konto usługi Azure Storage, które ma zostać skojarzone z tym klastrem. Oprócz tego konta magazynu można dodać dodatkowe konta magazynu z tej samej subskrypcji platformy Azure lub różnych subskrypcji platformy Azure podczas procesu tworzenia lub po utworzeniu klastra. Aby uzyskać instrukcje dotyczące dodawania kolejnych kont magazynu, zobacz [Tworzenie klastrów usługi HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]
> Korzystanie z dodatkowego konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób korzystania z magazynu Azure zgodnego z systemem plików HDFS w połączeniu z usługą HDInsight. Takie podejście umożliwia tworzenie rozwiązań do pozyskiwania danych skalowalnych, długoterminowych archiwizacji i używanie HDInsight w celu efektywnego wykorzystywania informacji przechowywanych strukturalnych i danych bez struktury.

Aby uzyskać więcej informacji, zobacz:

* [Sterownik systemu plików Hadoop ABFS dla usługi Azure Data Lake Storage Gen2](abfs-driver.md)
* [Wprowadzenie do usługi Azure Data Lake Storage](introduction.md)
* [Konfigurowanie klastrów HDInsight za pomocą usługi Azure Data Lake Storage za pomocą usługi Hadoop, Spark, Kafka i więcej](quickstart-create-connect-hdi-cluster.md)
* [Pozyskiwanie danych do usługi Azure Data Lake Storage za pomocą narzędzia distcp](use-distcp.md)

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/use-hdi-cluster/HDI.PowerShell.BlobCommands.png
