---
title: Należy dodać konta dodatkowego magazynu usługi Azure HDInsight
description: Dowiedz się, jak dodać dodatkowe Azure konta magazynu do istniejącego klastra HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 833198f3b5dd07988bcb5fc85f815ae2c12f1197
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481930"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Dodawanie kolejnych kont magazynu do HDInsight

Dowiedz się, jak dodać konta dodatkowego magazynu usługi Azure HDInsight przy użyciu akcji skryptu. Kroki opisane w tym dokumencie Dodaj konto magazynu do istniejącego klastra HDInsight opartych na systemie Linux. Ten artykuł dotyczy [usługi Azure Storage](hdinsight-hadoop-use-blob-storage.md) i tylko dodatkowych kont magazynu (nie domyślnego klastra konta magazynu). W tym artykule nie ma zastosowania do [usługi Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) i [usługi Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Informacje przedstawione w tym dokumencie jest o dodanie dodatkowego miejsca do magazynowania w klastrze, po jego utworzeniu. Aby uzyskać informacje na temat dodawania konta magazynu podczas tworzenia klastra, zobacz [konfigurowanie klastrów w HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i](hdinsight-hadoop-provision-linux-clusters.md).

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

__Wymagania dotyczące__:

* Skrypt musi zostać zastosowana na __węzłami głównymi__.

## <a name="to-use-the-script"></a>Aby użyć skryptu

Ten skrypt może służyć z witryny Azure portal, programu Azure PowerShell lub interfejsu wiersza polecenia do klasycznego Azure. Aby uzyskać więcej informacji, zobacz [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentu.

> [!IMPORTANT]  
> Podczas korzystania z krokami opisanymi w dokumencie dostosowywania, aby zastosować ten skrypt należy użyć następujące informacje:
>
> * Zamień na identyfikator URI dla tego skryptu żadnych akcji skryptu przykład identyfikatora URI (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * Wszelkie parametry przykład zastąpić nazwę konta usługi Azure storage i klucz konta magazynu, które mają zostać dodane do klastra. Jeśli przy użyciu witryny Azure portal, te parametry muszą być oddzielone spacją.
> * Nie musisz oznaczyć ten skrypt jako __utrwalone__, jak bezpośrednio aktualizuje konfigurację Ambari klastra.

## <a name="known-issues"></a>Znane problemy

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Konta magazynu, które nie są wyświetlane w witrynie Azure portal lub narzędzi

Podczas przeglądania klastra HDInsight w witrynie Azure portal, wybierając __kont magazynu__ wpis w __właściwości__ kont magazynu dodanego przez tę akcję skryptu nie są wyświetlane. Klasyczny interfejs wiersza polecenia platformy Azure i programu PowerShell Azure nie są wyświetlane dodatkowe konto magazynu albo.

Informacje dotyczące magazynu nie jest wyświetlany, ponieważ skrypt modyfikuje tylko konfigurację core-site.xml klastra. Te informacje nie są używane podczas pobierania informacji klastra, za pomocą interfejsów API zarządzania platformy Azure.

Aby wyświetlić informacje o koncie magazynu dodane do klastra przy użyciu tego skryptu, użyj interfejsu API REST Ambari. Aby pobrać te informacje dotyczące klastra, użyj następujących poleceń:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]  
> Ustaw `$clusterName` na nazwę klastra HDInsight. Ustaw `$storageAccountName` do nazwy konta magazynu. Po wyświetleniu monitu wprowadź nazwę użytkownika klastra (administratora) i hasło.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]  
> Ustaw `$PASSWORD` hasło do konta logowania (administratora) klastra. Ustaw `$CLUSTERNAME` na nazwę klastra HDInsight. Ustaw `$STORAGEACCOUNTNAME` do nazwy konta magazynu.
>
> W tym przykładzie użyto [curl (https://curl.haxx.se/) ](https://curl.haxx.se/) i [jq (https://stedolan.github.io/jq/) ](https://stedolan.github.io/jq/) pobierania i analizowania danych JSON.

Korzystając z tego polecenia, należy zastąpić __CLUSTERNAME__ o nazwie klastra HDInsight. Zastąp __hasło__ przy użyciu protokołu HTTP hasło logowania klastra. Zastąp __STORAGEACCOUNT__ nazwą konta magazynu, dodać za pomocą akcji skryptu. Informacje zwracane z tego polecenia zostanie wyświetlony podobny do następującego tekstu:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Ten tekst jest przykładem zaszyfrowanego klucza, który jest używany do uzyskania dostępu do konta magazynu.

### <a name="unable-to-access-storage-after-changing-key"></a>Nie można uzyskać dostępu do magazynu po zmianie klucza

W przypadku zmiany klucza konta magazynu HDInsight już dostępne na koncie magazynu. HDInsight używa pamięci podręcznej kopię klucza w core-site.xml dla klastra. Ta buforowana kopia należy zaktualizować w taki sposób, aby dopasować nowego klucza.

Uruchomione ponownie za pomocą akcji skryptu jest __nie__ zaktualizować klucza, ponieważ skrypt sprawdza, czy wpis dla konta magazynu już istnieje. Jeżeli wpis już istnieje, nie powoduje zmiany.

Aby obejść ten problem, należy usunąć istniejący wpis dla konta magazynu. Wykonaj następujące kroki, aby usunąć istniejący wpis:

1. W przeglądarce sieci web Otwórz Interfejsu sieci Web Ambari klastra usługi HDInsight. Identyfikator URI jest https://CLUSTERNAME.azurehdinsight.net. Zastąp ciąg __CLUSTERNAME__ nazwą klastra.

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

> [!WARNING]  
> Przy użyciu konta magazynu w regionie innym niż klaster HDInsight nie jest obsługiwana.

### <a name="additional-charges"></a>Dodatkowe opłaty

W przypadku konta magazynu w regionie innym niż klaster HDInsight, możesz zauważyć opłaty za dodatkowy ruch wychodzący na rozliczenia platformy Azure. Opłata za ruch wychodzący jest stosowana, gdy opuszczają one regionalnym centrum danych. Ta opłata jest stosowana nawet wtedy, gdy ruch jest kierowany do innego centrum danych platformy Azure w innym regionie.

> [!WARNING]  
> Przy użyciu konta magazynu w regionie innym niż klaster HDInsight nie jest obsługiwana.

## <a name="next-steps"></a>Kolejne kroki

Masz przedstawiono sposób dodawania dodatkowych kont magazynu do istniejącego klastra HDInsight. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)
