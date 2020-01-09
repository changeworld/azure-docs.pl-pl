---
title: Dodawanie dodatkowych kont magazynu platformy Azure do usługi HDInsight
description: Dowiedz się, jak dodać dodatkowe konta magazynu platformy Azure do istniejącego klastra usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 86b9230dbdca82c5599c1839fd64bd3df4725051
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435575"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Dodawanie dodatkowych kont magazynu do usługi HDInsight

Dowiedz się, jak za pomocą akcji skryptu dodać dodatkowe *konta* magazynu platformy Azure do usługi HDInsight. Kroki opisane w tym dokumencie umożliwiają dodanie *konta* magazynu do istniejącego klastra usługi HDInsight opartego na systemie Linux. Ten artykuł dotyczy *kont* magazynu (nie domyślnego konta magazynu klastra), a nie dodatkowego magazynu, takiego jak [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) i [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Informacje przedstawione w tym dokumencie dotyczą dodawania dodatkowych kont magazynu do klastra po jego utworzeniu. Aby uzyskać informacje na temat dodawania kont magazynu podczas tworzenia klastra, zobacz [Konfigurowanie klastrów w usłudze HDInsight przy użyciu Apache Hadoop, Apache Spark, Apache Kafka i innych](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nazwa i klucz konta magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md).
* [Nazwa klastra jest poprawnie uwzględniana](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* W przypadku korzystania z programu PowerShell należy użyć polecenia AZ module.  Zobacz [omówienie Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure, zobacz [Azure Command Line Interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Jeśli jest używany bash lub wiersz polecenia systemu Windows, potrzebny jest również **JQ**, procesor JSON w wierszu polecenia.  Zobacz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). W przypadku usługi Bash on Ubuntu w systemie Windows 10 zapoznaj się z artykułem systemowy [podsystem instalacji systemu Linux dla systemu Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Zasady działania

Ten skrypt przyjmuje następujące parametry:

* __Nazwa konta magazynu Azure__: nazwa konta magazynu, które ma zostać dodane do klastra usługi HDInsight. Po uruchomieniu skryptu Usługa HDInsight może odczytywać i zapisywać dane przechowywane na tym koncie magazynu.

* __Klucz konta usługi Azure Storage__: klucz, który udziela dostępu do konta magazynu.

* __-p__ (opcjonalnie): Jeśli jest określony, klucz nie jest szyfrowany i jest przechowywany w pliku pliku Core-site. XML jako zwykły tekst.

Podczas przetwarzania skrypt wykonuje następujące czynności:

* Jeśli konto magazynu istnieje już w konfiguracji pliku Core-site. XML klastra, skrypt kończy działanie i nie są wykonywane żadne dalsze działania.

* Sprawdza, czy konto magazynu istnieje i czy można uzyskać do niego dostęp przy użyciu klucza.

* Szyfruje klucz przy użyciu poświadczeń klastra.

* Dodaje konto magazynu do pliku pliku Core-site. XML.

* Powoduje zatrzymanie i ponowne uruchomienie usługi [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop przędzy](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)i [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) . Zatrzymywanie i uruchamianie tych usług pozwala im na korzystanie z nowego konta magazynu.

> [!WARNING]  
> Użycie konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

## <a name="the-script"></a>Skrypt

__Lokalizacja skryptu__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Wymagania__: skrypt należy zastosować w __węzłach głównych__. Nie musisz oznaczać tego skryptu jako __utrwalonego__, ponieważ bezpośrednio aktualizuje konfigurację Ambari klastra.

## <a name="to-use-the-script"></a>Aby użyć skryptu

Tego skryptu można użyć z Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Portal.

### <a name="powershell"></a>PowerShell

Za pomocą polecenia [Submit-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Zastąp `CLUSTERNAME`, `ACCOUNTNAME`i `ACCOUNTKEY` odpowiednimi wartościami.

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Za pomocą polecenia [AZ HDInsight Script-Action Execute](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Zastąp `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`i `ACCOUNTKEY` odpowiednimi wartościami.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Portal Azure

Zobacz [Zastosuj akcję skryptu do działającego klastra](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Znane problemy

### <a name="storage-firewall"></a>Zapora magazynu

Jeśli wybierzesz opcję zabezpieczenia konta magazynu za pomocą ograniczeń **zapory i sieci wirtualnych** w **wybranych sieciach**, należy włączyć wyjątek **Zezwalaj na zaufane usługi firmy Microsoft...** , aby Usługa HDInsight mogła uzyskać dostęp do konta magazynu.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Konta magazynu nie są wyświetlane w Azure Portal lub narzędziach

Podczas wyświetlania klastra usługi HDInsight w Azure Portal wybranie pozycji __konta magazynu__ w obszarze __Właściwości__ nie powoduje wyświetlenia kont magazynu dodanych za pomocą tej akcji skryptu. W przypadku Azure PowerShell i interfejsu wiersza polecenia platformy Azure nie jest wyświetlane dodatkowe konto magazynu.

Informacje o magazynie nie są wyświetlane, ponieważ skrypt modyfikuje konfigurację pliku Core-site. XML dla klastra. Te informacje nie są używane podczas pobierania informacji o klastrze przy użyciu interfejsów API zarządzania platformy Azure.

Aby wyświetlić informacje o koncie magazynu dodawane do klastra przy użyciu tego skryptu, użyj interfejsu API REST Ambari. Użyj następujących poleceń, aby pobrać te informacje dla klastra:

### <a name="powershell"></a>PowerShell

Zastąp `CLUSTERNAME` nazwą klastra o prawidłowej wielkości liter. Zastąp `ACCOUNTNAME` nazwami rzeczywistymi. Po wyświetleniu monitu wprowadź hasło logowania klastra.

```powershell
# Update values
$clusterName = "CLUSTERNAME"
$accountName = "ACCOUNTNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>bash

Zastąp `CLUSTERNAME` nazwą klastra o prawidłowej wielkości liter. Zastąp `PASSWORD` hasłem administratora klastra. Zastąp `STORAGEACCOUNT` nazwą rzeczywistego konta magazynu.

```bash
export clusterName="CLUSTERNAME"
export password='PASSWORD'
export storageAccount="STORAGEACCOUNT"

export ACCOUNTNAME='"'fs.azure.account.key.$storageAccount.blob.core.windows.net'"'

export configVersion=$(curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version")

curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Zastąp `CLUSTERNAME` nazwą klastra o prawidłowej wielkości liter w obu skryptach. Najpierw Zidentyfikuj używaną wersję konfiguracji usługi, wprowadzając następujące polecenie:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version"
```

Zastąp `ACCOUNTNAME` nazwą rzeczywistego konta magazynu. Następnie zastąp `4` rzeczywistą wersją konfiguracji usługi i wprowadź polecenie:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```

---

Informacje zwracane z tego polecenia pojawiają się podobnie do następującego tekstu:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Ten tekst to przykład zaszyfrowanego klucza, który służy do uzyskiwania dostępu do konta magazynu.

### <a name="unable-to-access-storage-after-changing-key"></a>Nie można uzyskać dostępu do magazynu po zmianie klucza

Jeśli zmienisz klucz dla konta magazynu, Usługa HDInsight nie będzie już uzyskiwać dostępu do konta magazynu. Usługa HDInsight używa zapisanej w pamięci podręcznej kopii klucza w pliku Core-site. XML dla klastra. Tę kopię w pamięci podręcznej należy zaktualizować, aby odpowiadała nowemu kluczowi.

Wykonanie akcji skryptu ponownie __nie aktualizuje klucza__ , ponieważ skrypt sprawdza, czy wpis dla konta magazynu już istnieje. Jeśli wpis już istnieje, nie wprowadza żadnych zmian.

Aby obejść ten problem, należy usunąć istniejący wpis dla konta magazynu. Wykonaj następujące kroki, aby usunąć istniejący wpis:

> [!IMPORTANT]  
> Obracanie klucza magazynu dla konta magazynu podstawowego dołączonego do klastra nie jest obsługiwane.

1. W przeglądarce sieci Web Otwórz interfejs użytkownika sieci Web Ambari dla klastra usługi HDInsight. Identyfikator URI jest `https://CLUSTERNAME.azurehdinsight.net`. Zastąp ciąg `CLUSTERNAME` nazwą klastra.

    Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło logowania HTTP dla klastra.

2. Z listy usług po lewej stronie wybierz pozycję __HDFS__. Następnie __Wybierz kartę konfiguracje na środku__ strony.

3. W polu __Filtr...__ wprowadź wartość __FS. Azure. Account__. Spowoduje to zwrócenie wpisów dla dowolnych dodatkowych kont magazynu, które zostały dodane do klastra. Istnieją dwa typy wpisów: klucz __, który__ jest __kluczem__. Oba elementy zawierają nazwę konta magazynu w ramach nazwy klucza.

    Poniżej przedstawiono przykładowe wpisy dla konta magazynu o nazwie Moja __Magazyn__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Po zidentyfikowaniu kluczy dla konta magazynu, które należy usunąć, użyj czerwonej ikony "-" z prawej strony wpisu, aby go usunąć. Następnie użyj przycisku __Zapisz__ , aby zapisać zmiany.

5. Po zapisaniu zmian Użyj akcji skryptu, aby dodać konto magazynu i nową wartość klucza do klastra.

### <a name="poor-performance"></a>Niska wydajność

Jeśli konto magazynu znajduje się w innym regionie niż klaster usługi HDInsight, może wystąpić niska wydajność. Dostęp do danych w innym regionie wysyła ruch sieciowy poza regionalnymi centrami danych platformy Azure i przez publiczny Internet, co może spowodować opóźnienia.

### <a name="additional-charges"></a>Dodatkowe opłaty

Jeśli konto magazynu znajduje się w innym regionie niż klaster usługi HDInsight, możesz zauważyć dodatkowe opłaty za ruch wychodzący w ramach rozliczeń na platformie Azure. Opłata za ruch wychodzący jest stosowana, gdy dane opuszczają regionalne centrum danych. Ta opłata jest stosowana, nawet jeśli ruch jest przeznaczony dla innego centrum danych platformy Azure w innym regionie.

## <a name="next-steps"></a>Następne kroki

Dowiesz się, jak dodać dodatkowe konta magazynu do istniejącego klastra usługi HDInsight. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)
