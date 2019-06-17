---
title: Należy dodać konta dodatkowego magazynu usługi Azure HDInsight
description: Dowiedz się, jak dodać dodatkowe Azure konta magazynu do istniejącego klastra HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 6b9577bcf8b527abb0cb7b8720ed83ec8321655b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724469"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Dodawanie kolejnych kont magazynu do HDInsight

Dowiedz się, jak dodać dodatkowe usługi Azure storage za pomocą akcji skryptu *kont* do HDInsight. Kroki opisane w tym dokumencie dodać magazyn *konta* do istniejącego klastra HDInsight opartych na systemie Linux. Ten artykuł dotyczy magazynu *kont* (nie domyślnego klastra konta magazynu), a nie dodatkowego magazynu, takie jak [usługi Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) i [usługi Azure Data Lake Storage Gen2 ](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Informacje przedstawione w tym dokumencie jest o dodanie dodatkowego miejsca do magazynowania w klastrze, po jego utworzeniu. Aby uzyskać informacje na temat dodawania konta magazynu podczas tworzenia klastra, zobacz [konfigurowanie klastrów w HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Hadoop w HDInsight. Zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nazwa konta magazynu i klucza. Zobacz [Zarządzanie ustawieniami konta magazynu w witrynie Azure portal](../storage/common/storage-account-manage.md).
* [Nazwa klastra poprawnie z uwzględnieniem wielkości liter](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Jeśli przy użyciu programu PowerShell, należy moduł AZ.  Zobacz [Omówienie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure, zobacz [interfejsu wiersza polecenia platformy Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Jeśli używasz powłoki bash lub wiersza polecenia systemu windows, należy także **jq**, wiersza polecenia procesora w formacie JSON.  Zobacz [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/). Dla programu bash w systemie Ubuntu w systemie Windows 10 zobacz [podsystemu Windows dla systemu Linux instalacji Guide for Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Jak to działa

Ten skrypt przyjmuje następujące parametry:

* __Nazwa konta usługi Azure storage__: Nazwa konta magazynu, aby dodać do klastra HDInsight. Po uruchomieniu skryptu HDInsight może odczytywać i zapisać dane przechowywane na tym koncie magazynu.

* __Klucz konta usługi Azure storage__: Klucz, która udziela dostępu do konta magazynu.

* __-p__ (opcjonalnie): Jeśli zostanie określony, klucz nie jest zaszyfrowany i znajduje się w pliku core-site.xml jako zwykły tekst.

Podczas przetwarzania skrypt wykonuje następujące czynności:

* Jeśli konto magazynu już istnieje w konfiguracji core-site.xml dla klastra, skrypt zakończy pracę, i są wykonywane żadne dalsze akcje.

* Sprawdza, czy konto magazynu istnieje i czy jest możliwy przy użyciu klucza.

* Szyfruje klucz przy użyciu poświadczeń klastra.

* Dodaje konto magazynu do pliku core-site.xml.

* Zatrzymuje i uruchamia ponownie [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), i [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) usług. Zatrzymywanie i uruchamianie tych usług umożliwi to używanie nowego konta magazynu.

> [!WARNING]  
> Za pomocą konta magazynu w innej lokalizacji niż klaster HDInsight nie jest obsługiwana.

## <a name="the-script"></a>Skrypt

__Lokalizację skryptu__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Wymagania dotyczące__:  Skrypt musi zostać zastosowana na __węzłami głównymi__. Nie musisz oznaczyć ten skrypt jako __utrwalone__, jak bezpośrednio aktualizuje konfigurację Ambari klastra.

## <a name="to-use-the-script"></a>Aby użyć skryptu

Ten skrypt może służyć z programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub witryny Azure portal.

### <a name="powershell"></a>PowerShell

Za pomocą [przesłać AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Zastąp `CLUSTERNAME`, `ACCOUNTNAME`, i `ACCOUNTKEY` odpowiednimi wartościami.

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

Za pomocą [wykonywanie akcji skryptu az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Zastąp `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`, i `ACCOUNTKEY` odpowiednimi wartościami.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Azure Portal

Zobacz [dotyczą akcji skryptu działającego klastra](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Znane problemy

### <a name="storage-firewall"></a>Zapora magazynu

Jeśli chcesz zabezpieczyć swoje konto magazynu z **zapory i sieci wirtualne** ograniczenia dotyczące **wybrane sieci**, pamiętaj włączyć wyjątek **Zezwalaj na zaufane firmy Microsoft usługi...**  tak, aby HDInsight można uzyskać dostępu do konta magazynu.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Konta magazynu, które nie są wyświetlane w witrynie Azure portal lub narzędzi

Podczas przeglądania klastra HDInsight w witrynie Azure portal, wybierając __kont magazynu__ wpis w __właściwości__ kont magazynu dodanego przez tę akcję skryptu nie są wyświetlane. Azure PowerShell i wiersza polecenia platformy Azure nie są wyświetlane dodatkowe konto magazynu albo.

Informacje dotyczące magazynu nie jest wyświetlany, ponieważ skrypt modyfikuje tylko konfigurację core-site.xml klastra. Te informacje nie są używane podczas pobierania informacji klastra, za pomocą interfejsów API zarządzania platformy Azure.

Aby wyświetlić informacje o koncie magazynu dodane do klastra przy użyciu tego skryptu, użyj interfejsu API REST Ambari. Aby pobrać te informacje dotyczące klastra, użyj następujących poleceń:

### <a name="powershell"></a>PowerShell

Zastąp `CLUSTERNAME` o nazwie klastra prawidłowo z uwzględnieniem wielkości liter. Wersja konfiguracji usługi używana należy najpierw zidentyfikować, wprowadzając poniższe polecenie:

```powershell
# getting service_config_version
$clusterName = "CLUSTERNAME"

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version
```

Zastąp `ACCOUNTNAME` z rzeczywistymi nazwami. Następnie zastąp `4` z rzeczywistym wersja konfiguracji usługi, a następnie wprowadź polecenie. Po wyświetleniu monitu wprowadź hasło logowania klastra.

```powershell
# Update values
$accountName = "ACCOUNTNAME"
$version = 4

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$version" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>Bash
Zastąp `myCluster` o nazwie klastra prawidłowo z uwzględnieniem wielkości liter.

```bash
export CLUSTERNAME='myCluster'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Zastąp `myAccount` przy użyciu nazwy konta rzeczywistego magazynu. Następnie zastąp `4` z rzeczywistym wersja konfiguracji usługi, a następnie wprowadź polecenie:

```bash
export ACCOUNTNAME='"fs.azure.account.key.myAccount.blob.core.windows.net"'
export VERSION='4'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=$VERSION" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Zastąp `CLUSTERNAME` nazwą klastra prawidłowo z uwzględnieniem wielkości liter w obu skryptów. Wersja konfiguracji usługi używana należy najpierw zidentyfikować, wprowadzając poniższe polecenie:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Zastąp `ACCOUNTNAME` przy użyciu nazwy konta rzeczywistego magazynu. Następnie zastąp `4` z rzeczywistym wersja konfiguracji usługi, a następnie wprowadź polecenie:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```
---

 Informacje zwracane z tego polecenia zostanie wyświetlony podobny do następującego tekstu:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Ten tekst jest przykładem zaszyfrowanego klucza, który jest używany do uzyskania dostępu do konta magazynu.

### <a name="unable-to-access-storage-after-changing-key"></a>Nie można uzyskać dostępu do magazynu po zmianie klucza

W przypadku zmiany klucza konta magazynu HDInsight już dostępne na koncie magazynu. HDInsight używa pamięci podręcznej kopię klucza w core-site.xml dla klastra. Ta buforowana kopia należy zaktualizować w taki sposób, aby dopasować nowego klucza.

Uruchomione ponownie za pomocą akcji skryptu jest __nie__ zaktualizować klucza, ponieważ skrypt sprawdza, czy wpis dla konta magazynu już istnieje. Jeżeli wpis już istnieje, nie powoduje zmiany.

Aby obejść ten problem, należy usunąć istniejący wpis dla konta magazynu. Wykonaj następujące kroki, aby usunąć istniejący wpis:

1. W przeglądarce sieci web Otwórz Interfejsu sieci Web Ambari klastra usługi HDInsight. Identyfikator URI jest `https://CLUSTERNAME.azurehdinsight.net`. Zastąp ciąg `CLUSTERNAME` nazwą klastra.

    Po wyświetleniu monitu wprowadź nazwę użytkownika logowania do protokołu HTTP i hasło dla klastra.

2. Z listy usług w lewej części strony wybierz __HDFS__. Następnie wybierz pozycję __Configs__ kartę w środkowej części strony.

3. W __filtr...__  wprowadź wartość __fs.azure.account__. Spowoduje to zwrócenie wpisy dla wszystkich kont dodatkowego miejsca do magazynowania, które zostały dodane do klastra. Istnieją dwa typy wpisy. __keyprovider__ i __klucz__. Oba zawierają nazwę konta magazynu jako część nazwy kluczy.

    Poniżej przedstawiono przykładowe wpisy dla konta magazynu o nazwie __mój_magazyn__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Po zidentyfikowaniu kluczy dla konta magazynu, którą chcesz usunąć, należy użyć czerwoną "-" ikonę z prawej strony zapisu, można go usunąć. Następnie użyj __Zapisz__ przycisk, aby zapisać zmiany.

5. Po zapisaniu zmian można dodać konta magazynu i nową wartość klucza do klastra należy użyć akcji skryptu.

### <a name="poor-performance"></a>Niska wydajność

W przypadku konta magazynu w regionie innym niż klaster HDInsight, może wystąpić spadek wydajności. Uzyskiwanie dostępu do danych w innym regionie wysyła ruch sieciowy, poza centrum danych platformy Azure regionalnych i w publicznej sieci internet, który może prowadzić do opóźnienia.

### <a name="additional-charges"></a>Dodatkowe opłaty

W przypadku konta magazynu w regionie innym niż klaster HDInsight, możesz zauważyć opłaty za dodatkowy ruch wychodzący na rozliczenia platformy Azure. Opłata za ruch wychodzący jest stosowana, gdy opuszczają one regionalnym centrum danych. Ta opłata jest stosowana nawet wtedy, gdy ruch jest kierowany do innego centrum danych platformy Azure w innym regionie.

## <a name="next-steps"></a>Kolejne kroki

Masz przedstawiono sposób dodawania dodatkowych kont magazynu do istniejącego klastra HDInsight. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)
