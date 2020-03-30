---
title: Dodawanie dodatkowych kont usługi Azure Storage do usługi HDInsight
description: Dowiedz się, jak dodać dodatkowe konta usługi Azure Storage do istniejącego klastra usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 87eb04b7323186175195babf6a602fa12d25176f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206711"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Dodawanie dodatkowych kont magazynu do usługi HDInsight

Dowiedz się, jak używać akcji skryptów do dodawania dodatkowych *kont* usługi Azure Storage do usługi HDInsight. Kroki opisane w tym dokumencie dodają *konto* magazynu do istniejącego klastra usługi HDInsight. Ten artykuł dotyczy *kont* magazynu (nie domyślnego konta magazynu klastra), a nie dodatkowego magazynu, takiego jak [Usługa Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) i Azure Data Lake Storage [Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Informacje zawarte w tym dokumencie dotyczą dodawania dodatkowych kont magazynu do klastra po jego utworzeniu. Aby uzyskać informacje na temat dodawania kont magazynu podczas tworzenia [klastra, zobacz Konfigurowanie klastrów w programie HDInsight za pomocą apache Hadoop, Apache Spark, Apache Kafka i innych](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Hadoop na HDInsight. Zobacz [Wprowadzenie do HDInsight w systemie Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nazwa i klucz konta magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md).
* Jeśli używasz programu PowerShell, potrzebny jest moduł AZ.  Zobacz [Omówienie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="how-it-works"></a>Jak to działa

Podczas przetwarzania skrypt wykonuje następujące akcje:

* Jeśli konto magazynu już istnieje w konfiguracji core-site.xml dla klastra, skrypt kończy działanie i nie są wykonywane żadne dalsze akcje.

* Sprawdza, czy konto magazynu istnieje i można uzyskać do niego dostęp za pomocą klucza.

* Szyfruje klucz przy użyciu poświadczeń klastra.

* Dodaje konto magazynu do pliku core-site.xml.

* Zatrzymuje i restartuje [apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)i [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) usług. Zatrzymywanie i uruchamianie tych usług umożliwia im korzystanie z nowego konta magazynu.

> [!WARNING]  
> Korzystanie z konta magazynu w innej lokalizacji niż klaster HDInsight nie jest obsługiwane.

## <a name="add-storage-account"></a>Dodaj konto magazynu

Użyj [akcji skryptu,](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) aby zastosować zmiany z następującymi zagadnieniami:

|Właściwość | Wartość |
|---|---|
|Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Typy węzłów|Head|
|Parametry|`ACCOUNTNAME``ACCOUNTKEY` `-p` (opcjonalnie)|

* `ACCOUNTNAME`to nazwa konta magazynu, które można dodać do klastra HDInsight.
* `ACCOUNTKEY`jest kluczem `ACCOUNTNAME`dostępu do pliku .
* Parametr `-p` jest opcjonalny. Jeśli zostanie określony, klucz nie jest zaszyfrowany i jest przechowywany w pliku core-site.xml jako zwykły tekst.

## <a name="verification"></a>Weryfikacja

Podczas wyświetlania klastra HDInsight w witrynie Azure portal, wybierając wpis __Konta magazynu__ w obszarze __Właściwości__ nie wyświetla kont magazynu dodanych za pośrednictwem tej akcji skryptu. Narzędzia Azure PowerShell i narzędzia Platformy Azure CLI również nie wyświetlają dodatkowego konta magazynu. Informacje o magazynie nie są wyświetlane, ponieważ `core-site.xml` skrypt modyfikuje tylko konfigurację klastra. Te informacje nie są używane podczas pobierania informacji klastra przy użyciu interfejsów API zarządzania platformy Azure.

Aby zweryfikować dodatkowe przechowywanie, należy użyć jednej z poniższych metod:

### <a name="powershell"></a>PowerShell

Skrypt zwróci nazwę konta magazynu skojarzoną z danym klastrem. Zamień `CLUSTERNAME` na rzeczywistą nazwę klastra, a następnie uruchom skrypt.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.

1. Przejdź do **witryny HDFS** > **Configs** > **Advanced** > Custom**core-site**.

1. Obserwuj klawisze, `fs.azure.account.key`które zaczynają się od . Nazwa konta będzie częścią klucza, jak widać na tym przykładzie:

   ![weryfikacji za pośrednictwem Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Usuwanie konta magazynu

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.

1. Przejdź do **witryny HDFS** > **Configs** > **Advanced** > Custom**core-site**.

1. Usuń następujące klawisze:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Po usunięciu tych klawiszy i zapisaniu konfiguracji należy ponownie uruchomić Oozie, Yarn, MapReduce2, HDFS i Hive jeden po drugim.

## <a name="known-issues"></a>Znane problemy

### <a name="storage-firewall"></a>Zapora magazynu

Jeśli zdecydujesz się zabezpieczyć swoje konto magazynu za pomocą ograniczeń **zapory i sieci wirtualnych** w **wybranych sieciach,** należy włączyć wyjątek **Zezwalaj na zaufane usługi firmy Microsoft...** aby usługa HDInsight mogła uzyskać dostęp do twojego konta magazynu.

### <a name="unable-to-access-storage-after-changing-key"></a>Nie można uzyskać dostępu do magazynu po zmianie klucza

Jeśli zmienisz klucz dla konta magazynu, usługa HDInsight nie będzie już mogła uzyskać dostępu do konta magazynu. Usługa HDInsight używa buforowanej kopii klucza w pliku core-site.xml dla klastra. Ta kopia buforowana musi zostać zaktualizowana, aby była zgodna z nowym kluczem.

Ponowne uruchomienie akcji skryptu __nie__ aktualizuje klucza, ponieważ skrypt sprawdza, czy wpis dla konta magazynu już istnieje. Jeśli wpis już istnieje, nie wprowadzać żadnych zmian.

Aby obejść ten problem:  
1. Usuń konto magazynu.
1. Dodaj konto magazynu.

> [!IMPORTANT]  
> Obracanie klucza magazynu dla podstawowego konta magazynu dołączonego do klastra nie jest obsługiwane.

### <a name="poor-performance"></a>Niska wydajność

Jeśli konto magazynu znajduje się w innym regionie niż klaster HDInsight, może wystąpić niska wydajność. Uzyskiwanie dostępu do danych w innym regionie wysyła ruch sieciowy poza regionalne centrum danych platformy Azure i przez publiczny internet, który może powodować opóźnienia.

### <a name="additional-charges"></a>Dodatkowe opłaty

Jeśli konto magazynu znajduje się w innym regionie niż klaster HDInsight, można zauważyć dodatkowe opłaty za ruch wychodzący na fakturze platformy Azure. Opłata za wyjście jest stosowana, gdy dane opuszczają regionalne centrum danych. Ta opłata jest stosowana nawet wtedy, gdy ruch jest przeznaczony dla innego centrum danych platformy Azure w innym regionie.

## <a name="next-steps"></a>Następne kroki

Dowiesz się, jak dodać dodatkowe konta magazynu do istniejącego klastra USŁUGI HDInsight. Aby uzyskać więcej informacji na temat akcji skryptów, zobacz [Dostosowywanie klastrów HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)
