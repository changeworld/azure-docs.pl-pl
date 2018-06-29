---
title: Użyć wersji zapoznawczej Gen2 magazynu Azure Data Lake z klastrami Azure HDInsight
description: Dowiedz się, jak zapytania na danych z wersji zapoznawczej Gen2 magazynu Azure Data Lake i przechowywać wyniki analiz.
keywords: System plików hdfs, danych strukturalnych, danych bez struktury, usługi data lake store, Hadoop danych wejściowych, Hadoop danych wyjściowych, magazynu hadoop, system plików hdfs w danych wejściowych, system plików hdfs w danych wyjściowych, magazynu systemu plików hdfs, wasb azure
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: jamesbak
manager: jahogg
ms.component: data-lake-storage-gen2
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: 2797c9f18364a2321bea885592690793271d8b8e
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062193"
---
# <a name="use-azure-data-lake-storage-gen2-preview-with-azure-hdinsight-clusters"></a>Użyć wersji zapoznawczej Gen2 magazynu Azure Data Lake z klastrami Azure HDInsight

Do analizowania danych w klastrze usługi HDInsight, można przechowywać dane w dowolnej kombinacji magazynu Azure, Azure Data Lake magazynu Gen1 lub wersji zapoznawczej Gen2 magazynu Azure Data Lake. Wszystkie opcje magazynu pozwala bezpiecznie usuwać klastry usługi HDInsight, które są używane do obliczeń bez utraty danych użytkownika.

Platforma Hadoop obsługuje pojęcie domyślnego systemu plików. Domyślny system plików wyznacza domyślny schemat i element authority. Może również służyć do rozpoznawania ścieżek względnych. Podczas procesu tworzenia klastra usługi HDInsight można określić kontenera obiektów blob magazynu Azure lub usługi Azure Data Lake Storage jako domyślny system plików. Można również z HDInsight 3.5, można wybrać magazyn Azure lub usługi Azure Data Lake Storage jako domyślny system plików z kilkoma wyjątkami.

W tym artykule dowiesz się, jak Azure Data Lake magazynu Gen2 działa z klastrami usługi HDInsight. Aby uzyskać więcej informacji na temat tworzenia klastra usługi HDInsight, zobacz [— Konfiguracja usługi HDInsight clusters przy użyciu usługi Azure Data Lake Storage z platformy Hadoop, Spark, Kafka i](quickstart-create-connect-hdi-cluster.md).

Usługa Azure Storage to niezawodne rozwiązanie ogólnego przeznaczenia, które bezproblemowo integruje się z usługą HDInsight. HDInsight można użyć usługi Azure Data Lake Storage jako domyślnego systemu plików dla klastra. Za pomocą interfejsu systemu (HDFS) DFS Hadoop pełny zestaw składników usługi HDInsight może operować bezpośrednio na pliki w usłudze Azure Data Lake Storage.

Nie zaleca się użycie domyślnego systemu plików do przechowywania danych biznesowych. Usuwanie domyślnego systemu plików po każdym użyciu będzie zmniejszenie kosztów magazynowania jest dobrym rozwiązaniem. Należy pamiętać, że domyślny kontener zawiera aplikacji i systemu dzienników. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Udostępnianie jeden system plików dla wielu klastrów nie jest obsługiwane.

## <a name="hdinsight-storage-architecture"></a>Architektura magazynu usługi HDInsight

Na poniższym diagramie przedstawiono schemat architektury magazynu usługi HDInsight dotyczący korzystania z usługi Azure Storage:

![Klastry Hadoop używają interfejsu API systemu plików HDFS w celu dostępu do danych strukturalnych i bez struktury oraz przechowywania ich w usłudze Blob Storage.](./media/use-hdi-cluster/HDI.ABFS.Arch.png "Architektura usługi DHInsight Storage")

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. Dostęp do tego systemu plików można uzyskać przy użyciu w pełni kwalifikowanego identyfikatora URI, na przykład:

    hdfs://<namenodehost>/<path>

Ponadto HDInsight umożliwia dostęp do danych przechowywanych w usłudze Azure Data Lake Storage. Składnia jest następująca:

    abfs[s]://<file_system>@<accountname>.dfs.core.widows.net/<path>

Poniżej przedstawiono pewne zagadnienia, korzystając z konta usługi Azure Storage z klastrami usługi HDInsight.

* **Pliki na kontach magazynu, które są podłączone do klastra** ma nazwę konta i klucz skojarzony z klastrem podczas tworzenia. Ta konfiguracja daje pełny dostęp do plików w systemie plików.

* **Pliki publiczne na kontach magazynu, które nie są podłączone do klastra** ujawnia uprawnienia tylko do odczytu do plików w systemie plików.
  
  > [!NOTE]
  > Systemy plików publiczne pozwalają na pobranie listy wszystkich plików dostępnych w systemie plików i uzyskuje dostęp do metadanych. Systemy plików publiczne pozwalają na dostęp do plików tylko wtedy, gdy wiesz, dokładny adres URL. Aby uzyskać więcej informacji, zobacz [ograniczanie dostępu do kontenerów i obiektów blob](http://msdn.microsoft.com/library/windowsazure/dd179354.aspx) (reguły dla kontenerów i obiektów blob działają tego samego planu plików i systemu plików).
 
* **Systemy plików prywatnych na kontach magazynu, które nie są podłączone do klastra** nie zezwolić na dostęp do plików w systemie plików, chyba że zdefiniujesz konto magazynu podczas przesyłania zadań WebHCat. Przyczyny tego ograniczenia zostały omówione w dalszej części tego artykułu.

Konta magazynu definiowane w procesie tworzenia oraz ich klucze są przechowywane w pliku %HADOOP_HOME%/conf/core-site.xml w węzłach klastra. Domyślne działanie usługi HDInsight polega na korzystaniu z kont magazynu zdefiniowanych w pliku core-site.xml. To ustawienie możesz zmodyfikować przy użyciu narzędzia [Ambari](/hdinsight/hdinsight-hadoop-manage-ambari.md)

Wiele zadań WebHCat, w tym Hive, MapReduce, przesyłanie strumieniowe Hadoop, a także Pig, może przenosić ze sobą opis kont magazynu i metadane. (To rozwiązanie jest obecnie obsługiwane dla języka Pig z kontami magazynu, ale nie dla metadanych.) Aby uzyskać więcej informacji, zobacz [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (Używanie klastra usługi HDInsight z alternatywnymi kontami magazynu i magazynami metadanych).

## <a id="benefits"></a>Korzyści z usługi Azure Storage

Wynikowy koszt wydajności związany z brakiem łączenia klastrów obliczeniowych i zasobów magazynu jest zmniejszany przez sposób tworzenia klastrów obliczeniowych w pobliżu zasobów konta magazynu w obrębie regionu świadczenia usługi Azure, gdzie szybkie sieci zapewniają wydajny dostęp węzłów obliczeniowych do danych wewnątrz usługi Azure Storage.

Przechowywanie danych w usłudze Azure Storage zamiast w systemie plików HDFS ma wiele zalet:

* **Udostępnianie i ponowne użycie danych:** dane w systemie plików HDFS znajdują się wewnątrz klastra obliczeniowego. Tylko te aplikacje, które mają dostęp do klastra obliczeniowego mogą używać danych za pomocą interfejsów API systemu plików HDFS. Dane w usłudze Azure Storage są dostępne za pośrednictwem interfejsów API systemu plików HDFS lub za pomocą [interfejsów API REST usługi Blob Storage][blob-storage-restAPI]. W związku z tym większy zestaw narzędzi i aplikacji (w tym inne klastry HDInsight) może służyć do tworzenia i wykorzystywania danych.
* **Archiwizacja danych:** przechowywanie danych w usłudze Azure Storage pozwala bezpiecznie usuwać klastry usługi HDInsight używane do obliczeń bez utraty danych użytkownika.
* **Koszt magazynowania danych:** długoterminowe przechowywanie danych w systemie plików DFS jest droższe niż przechowywanie danych w usłudze Azure Storage, ponieważ koszt klastra obliczeniowego jest wyższy niż koszt usługi Azure Storage. Ponadto ponieważ nie trzeba ponownie ładować danych przy każdej generacji klastra obliczeniowego, oszczędzamy również koszty ładowania danych.
* **Elastyczne skalowanie w poziomie:** chociaż system plików HDFS zapewnia skalowanie w poziomie, skala jest wyznaczana przez liczbę węzłów tworzonych dla klastra. Zmiana skali może stać się procesem bardziej skomplikowanym w porównaniu z elastycznymi możliwościami skalowania, które można uzyskać automatycznie w usłudze Azure Storage.
* **Replikacja geograficzna:** usługę Azure Storage można replikować geograficznie. Chociaż zapewnia to odzyskiwanie geograficzne i nadmiarowość danych, praca w trybie failover w przypadku lokalizacji zreplikowanych geograficznie poważnie wpływa na wydajność i może pociągać za sobą dodatkowe koszty. Dlatego zalecane jest, aby wybrać replikację geograficzną w rozsądny sposób i tylko wtedy, gdy wartość danych uzasadnia ponoszenie dodatkowych kosztów.
* **Zarządzanie cyklem życia danych:** wszystkie dane w każdym systemie plików przechodzi przez jego własnej cyklem życia są bardzo przydatne i często używanych przez mniejszą wartość, mniejszą dostępne za pośrednictwem archiwum lub usunięcia. Magazyn Azure obejmuje warstwy danych i cyklem życia zasad zarządzania, które warstwy danych odpowiednio dla etapu cyklu życia.

Niektóre zadania i pakiety MapReduce mogą tworzyć wyniki pośrednie, których nie potrzeba przechowywać w usłudze Azure Storage. W takim przypadku można zdecydować się na przechowywanie danych w lokalnym systemie plików HDFS. W rzeczywistości HDInsight używa systemu plików DFS dla wielu wyników pośrednich w zadaniach Hive i innych procesach.

> [!NOTE]
> Większość poleceń systemu plików HDFS (na przykład `ls`, `copyFromLocal` i `mkdir`) nadal działa zgodnie z oczekiwaniami. Tylko polecenia specyficzne dla natywnych implementacji systemu plików HDFS (określanych jako systemu plików DFS), takich jak `fschk` i `dfsadmin`, Pokaż inaczej w magazynie Azure.

## <a name="create-an-data-lake-storage-file-system"></a>Tworzenie usługi Data Lake Storage systemu plików

Aby użyć systemu plików, należy najpierw utworzyć [konta magazynu Azure][azure-storage-create]. W ramach tego procesu możesz określić region platformy Azure, w którym utworzono konto magazynu. Klaster i konto magazynu muszą być hostowane w tym samym regionie. Baza danych SQL Server na potrzeby magazynu metadanych Hive i baza danych SQL Server na potrzeby magazynu metadanych Oozie również muszą znajdować się w tym samym regionie.

Wszędzie tam, gdzie go umieszczono, każdy utworzony obiekt blob należy do systemu plików na Twoim koncie usługi Azure Data Lake Storage. 

Domyślny system plików usługi Data Lake Storage przechowuje informacje specyficzne dla klastra, takie jak dzienniki i historię zadań. Nie należy współużytkować domyślnego systemu plików usługi Data Lake Storage z wielu klastrów usługi HDInsight. Może to spowodować uszkodzenie historii zadań. Zalecane jest, aby używać innego systemu plików dla każdego klastra i umieszczanie udostępnionych danych w połączonym koncie magazynu określonym we wdrożeniu wszystkich odpowiednich klastrów zamiast domyślnego konta magazynu. Aby uzyskać więcej informacji na temat konfigurowania połączonych kont magazynu, zobacz artykuł [Tworzenie klastrów usługi HDInsight][hdinsight-creation]. Jednak po usunięciu oryginalnego klastra usługi HDInsight można ponownie użyć domyślnego systemu plików magazynu. W przypadku klastrów HBase można zachować schemat tabeli HBase i dane przez utworzenie nowego klastra HBase przy użyciu domyślnego kontenera obiektów blob, używanego przez klaster HBase, który został usunięty.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Podczas tworzenia klastra usługi HDInsight w portalu, należy opcje (jak pokazano na poniższym zrzucie ekranu) podaj szczegóły konta magazynu. Można również określić, czy mają konta dodatkowego magazynu skojarzone z klastrem i jeśli tak, wybierz jedną z opcji dostępny magazyn dla dodatkowego miejsca do magazynowania.

![HDInsight, hadoop, tworzenie źródła danych](./media/use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> Korzystanie z dodatkowego konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Jeśli użytkownik [zainstalowaniu i skonfigurowaniu programu Azure PowerShell][powershell-install], następujący kod w wierszu polecenia programu Azure PowerShell umożliwia tworzenie konta magazynu i kontener:

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
> Utworzenie kontenera jest synonimem tworzenia systemu plików w usłudze Azure Data Lake Storage.

### <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!INCLUDE [use-latest-version](../../../includes/hdinsight-use-latest-cli.md)]

Jeśli masz [zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure](../../cli-install-nodejs.md), możesz zastosować następujące polecenie do konta magazynu i kontenera.

    azure storage account create <storageaccountname> --type LRS --is-hns-enabled true

> [!NOTE]
> W publicznej wersji zapoznawczej Data Lake magazynu Gen2 tylko `--type LRS` jest obsługiwana. Dodatkowa Redundancja opcje staną się dostępne w programie wersji zapoznawczej.

Pojawi się monit o region geograficzny, w którym zostanie utworzone konto magazynu. Utwórz konto magazynu w tym samym regionie, w którym planujesz utworzenie klastra usługi HDInsight.

Po utworzeniu konta magazynu użyj następującego polecenia w celu uzyskania kluczy do konta magazynu:

    azure storage account keys list <storageaccountname>

Aby utworzyć kontener, użyj następującego polecenia:

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

> [!NOTE]
> Utworzenie kontenera jest synonimem tworzenia systemu plików w usłudze Azure Data Lake Storage.

## <a name="address-files-in-azure-storage"></a>Adresowanie plików w usłudze Azure Storage

Schemat identyfikatora URI do uzyskiwania dostępu do plików w usłudze Azure Storage z usługi HDInsight to:

    abfs[s]://<FileSystem>@<AccountName>.dfs.core.widows.net/<path>

Schemat identyfikatora URI zapewnia nieszyfrowany dostęp (z *abfs:* prefiks) oraz szyfrowany dostęp SSL (z *abfss*). Firma Microsoft zaleca używanie *abfss* wszędzie tam, gdzie to możliwe, nawet w przypadku uzyskiwania dostępu do danych, które znajdują się wewnątrz tego samego regionu platformy Azure.

&lt;FileSystem&gt; identyfikuje ścieżki systemu plików usługi Azure Data Lake Storage.
&lt;AccountName&gt; identyfikuje nazwę konta magazynu Azure. Wymagana jest w pełni kwalifikowana nazwa domeny (FQDN).

Jeśli wartości &lt;FileSystem&gt; ani &lt;AccountName&gt; został określony, używany jest domyślny system plików. W przypadku plików w domyślnym systemie plików można używać ścieżki względnej lub bezwzględnej. Na przykład *hadoop-mapreduce-examples.jar* dostarczanego z klastrami usługi HDInsight można odwoływać się przy użyciu jednej z następujących ścieżek:

    abfs://myfilesystempath@myaccount.dfs.core.widows.net/example/jars/hadoop-mapreduce-examples.jar
    abfs:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> Nazwa pliku to *hadoop-examples.jar* w klastrach usługi HDInsight w wersji 2.1 i 1.6.

&lt;path&gt; jest nazwą ścieżki do pliku lub katalogu w systemie plików HDFS.

> [!NOTE]
> Podczas pracy z plikami poza usługą HDInsight, większość narzędzi nie rozpoznają ABFS sformatowanie i zamiast tego oczekuje podstawowego formatu ścieżki, takie jak `example/jars/hadoop-mapreduce-examples.jar`.
> 

## <a name="use-additional-storage-accounts"></a>Używanie dodatkowych kont magazynu

Podczas tworzenia klastra usługi HDInsight należy wskazać konto usługi Azure Storage, które ma zostać skojarzone z tym klastrem. Oprócz tego konta magazynu można dodać dodatkowe konta magazynu z tej samej subskrypcji platformy Azure lub różnych subskrypcji platformy Azure podczas procesu tworzenia lub po utworzeniu klastra. Aby uzyskać instrukcje dotyczące dodawania kolejnych kont magazynu, zobacz [Tworzenie klastrów usługi HDInsight](/hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]
> Korzystanie z dodatkowego konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób korzystania z magazynu Azure zgodnego z systemem plików HDFS w połączeniu z usługą HDInsight. Ta metoda służy do tworzenia rozwiązań do pozyskiwania danych skalowalnych, długoterminowych archiwizacji i używanie usługi HDInsight, aby odblokować informacje wewnątrz przechowywanych strukturalnych i danych bez struktury.

Aby uzyskać więcej informacji, zobacz:

* [Sterownik ABFS plików usługi Hadoop dla usługi Azure Data Lake magazynu Gen2](abfs-driver.md)
* [Wprowadzenie do usługi Azure Data Lake Storage](introduction.md)
* [Konfigurowanie klastrów usługi HDInsight przy użyciu usługi Azure Data Lake Storage z platformy Hadoop, Spark, Kafka i](quickstart-create-connect-hdi-cluster.md)
* [Pozyskiwania danych do usługi Azure Data Lake Storage za pomocą narzędzia distcp](use-distcp.md)

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: /hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: /storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/use-hdi-cluster/HDI.PowerShell.BlobCommands.png