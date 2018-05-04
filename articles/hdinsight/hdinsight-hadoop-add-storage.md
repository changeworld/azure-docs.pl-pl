---
title: Dodaj konta dodatkowego magazynu Azure do usługi HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać dodatkowe Azure konta magazynu w istniejącym klastrze usługi HDInsight.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 1404b37725362a71ccb4a0a84dff0c7c4ca591e2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Dodać dodatkowe konta magazynu w usłudze HDInsight

Dowiedz się, jak dodać konta dodatkowego magazynu Azure do usługi HDInsight za pomocą akcji skryptu. Kroki opisane w tym dokumencie Dodaj konto magazynu w istniejącym klastrze usługi HDInsight opartej na systemie Linux.

> [!IMPORTANT]
> Informacje przedstawione w tym dokumencie jest o dodanie dodatkowego magazynu do klastra, po jego utworzeniu. Aby uzyskać informacje na temat dodawania konta magazynu podczas tworzenia klastra, zobacz [Ustawianie klastrów w usłudze HDInsight Hadoop, Spark, Kafka i](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="how-it-works"></a>Jak to działa

Ten skrypt przyjmuje następujące parametry:

* __Nazwa konta magazynu Azure__: Nazwa konta magazynu, aby dodać do klastra usługi HDInsight. Po uruchomieniu skryptu HDInsight można odczytu i zapisu, dane przechowywane na tym koncie magazynu.

* __Klucz konta magazynu Azure__: klucz, która udziela dostępu do konta magazynu.

* __-p__ (opcjonalnie): Jeśli zostanie określona, klucza nie są szyfrowane i są przechowywane w pliku core-site.xml jako zwykły tekst.

Podczas przetwarzania skryptu wykonuje następujące czynności:

* Jeśli konto magazynu już istnieje w konfiguracji klastra core-site.xml, skrypt zakończy pracę, i są wykonywane żadne dalsze akcje.

* Sprawdza, czy konto magazynu istnieje i jest możliwy przy użyciu klucza.

* Szyfruje klucz przy użyciu poświadczeń klastra.

* Dodaje konto magazynu do pliku core-site.xml.

* Zatrzymuje i uruchamia ponownie usługi Oozie, YARN MapReduce2 i system plików HDFS. Zatrzymywanie i uruchamianie tych usług pozwala na użycie nowego konta magazynu.

> [!WARNING]
> Używanie konta magazynu w innej lokalizacji niż klastra usługi HDInsight nie jest obsługiwane.

## <a name="the-script"></a>Skrypt

__Lokalizacja skryptu__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Wymagania dotyczące__:

* Skrypt musi zostać zastosowana na __węzły główne__.

## <a name="to-use-the-script"></a>Aby użyć skryptu

Ten skrypt można z portalu Azure, programu Azure PowerShell lub 1.0 interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [klastrów usługi HDInsight opartej na dostosowanie systemu Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentu.

> [!IMPORTANT]
> Korzystając z krokami opisanymi w dokumencie dostosowania, skorzystaj z poniższych informacji, aby zastosować ten skrypt:
>
> * Zamień na identyfikator URI dla tego skryptu żadnych akcji skryptu przykład identyfikatora URI (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * Wszystkie parametry przykład zastąpić nazwę konta magazynu platformy Azure i klucza konta magazynu, które mają zostać dodane do klastra. Jeśli używasz portalu Azure, te parametry muszą być oddzielone spacją.
> * Nie należy oznaczyć skrypt jako __Persisted__, bezpośrednio aktualizacji konfiguracji Ambari w klastrze.

## <a name="known-issues"></a>Znane problemy

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Konta magazynu nie są wyświetlane w portalu Azure lub narzędzia

Podczas przeglądania klastra usługi HDInsight w portalu Azure, wybierając __kont magazynu__ wpis w __właściwości__ kont magazynu dodanego przez tę akcję skryptu nie są wyświetlane. Azure PowerShell i interfejsu wiersza polecenia Azure nie są wyświetlane na koncie magazynu dodatkowe albo.

Informacje o magazynu nie są wyświetlane, ponieważ skrypt tylko Modyfikuje konfigurację core-site.xml dla klastra. Te informacje nie jest używany podczas pobierania informacji o klastra przy użyciu interfejsów API zarządzania platformy Azure.

Aby wyświetlić informacje o koncie magazynu dodane do klastra przy użyciu tego skryptu, użyj interfejsu API REST Ambari. Aby pobrać te informacje dotyczące klastra, użyj następujących poleceń:

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> Ustaw `$clusterName` do nazwy klastra usługi HDInsight. Ustaw `$storageAccountName` do nazwy konta magazynu. Po wyświetleniu monitu wprowadź logowania do klastra (admin) i hasło.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> Ustaw `$PASSWORD` hasło konta logowania (Administrator) klastra. Ustaw `$CLUSTERNAME` do nazwy klastra usługi HDInsight. Ustaw `$STORAGEACCOUNTNAME` do nazwy konta magazynu.
>
> W tym przykładzie użyto [curl (http://curl.haxx.se/) ](http://curl.haxx.se/) i [jq (https://stedolan.github.io/jq/) ](https://stedolan.github.io/jq/) pobierania i analizy danych JSON.

Korzystając z tego polecenia, Zastąp __CLUSTERNAME__ z nazwą klastra usługi HDInsight. Zastąp __hasło__ przy użyciu hasła logowania HTTP dla klastra. Zastąp __STORAGEACCOUNT__ z nazwą konta magazynu, dodać za pomocą akcji skryptu. Informacje zwrócone z tego polecenia jest podobny do następującego tekstu:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Ten tekst jest przykładem zaszyfrowanego klucza, który jest używany do uzyskania dostępu do konta magazynu.

### <a name="unable-to-access-storage-after-changing-key"></a>Nie można uzyskać dostępu do magazynu po zmianie klucza

W przypadku zmiany klucza dla konta magazynu, HDInsight nie może uzyskać dostępu do konta magazynu. HDInsight używa pamięci podręcznej kopię klucza core-site.xml dla klastra. Aby dopasować nowego klucza należy zaktualizować tej kopii.

Uruchomiony ponownie za pomocą akcji skryptu jest __nie__ zaktualizować klucza, ponieważ skrypt sprawdza, czy wpis dla konta magazynu już istnieje. Jeżeli wpis już istnieje, nie powoduje żadnych zmian.

Aby obejść ten problem, należy usunąć istniejący wpis dla konta magazynu. Aby usunąć istniejący wpis, wykonaj następujące kroki:

1. W przeglądarce sieci web Otwórz interfejs sieci Web Ambari dla klastra usługi HDInsight. Identyfikator URI jest https://CLUSTERNAME.azurehdinsight.net. Zastąp ciąg __CLUSTERNAME__ nazwą klastra.

    Po wyświetleniu monitu wprowadź HTTP logowania użytkownika i hasło dla klastra.

2. Wybierz z listy usług w lewej części strony, __HDFS__. Następnie wybierz __Configs__ kartę w środku strony.

3. W __filtru...__  wprowadź wartość __fs.azure.account__. To polecenie zwróci wpisy dla wszystkich kont dodatkowego miejsca do magazynowania, które zostały dodane do klastra. Istnieją dwa typy wpisy. __keyprovider__ i __klucza__. Zawierają nazwę konta magazynu w ramach nazwę klucza.

    Poniżej przedstawiono przykładowe wpisy dla konta magazynu o nazwie __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Po zidentyfikowaniu kluczy dla konta magazynu, należy usunąć za pomocą czerwony "-" ikonę prawej wpisu go usunąć. Następnie użyj __zapisać__ przycisk, aby zapisać zmiany.

5. Po zapisaniu zmian można dodać konto magazynu i nowa wartość klucza do klastra, użyj akcji skryptu.

### <a name="poor-performance"></a>Niska wydajność

Jeśli konto magazynu znajduje się w regionie innym niż klaster usługi HDInsight, może wystąpić pogorszenie wydajności. Uzyskiwanie dostępu do danych w innym regionie wysyła ruch sieciowy poza centrum danych Azure regionalnych i w Internecie publicznego, który może powodować opóźnienia.

> [!WARNING]
> Używanie konta magazynu w regionie innym niż klaster usługi HDInsight nie jest obsługiwane.

### <a name="additional-charges"></a>Dodatkowe opłaty.

Jeśli konto magazynu znajduje się w regionie innym niż klaster usługi HDInsight, opłaty za wyjście dodatkowe mogą pojawić się na Azure rozliczeniowego. Dodatkowy ruch wychodzący jest stosowana, gdy dane pozostawia centrum danych regionalnych. Opłata została zastosowana, nawet jeśli ruch jest przeznaczony dla innej centrum danych Azure w innym regionie.

> [!WARNING]
> Używanie konta magazynu w regionie innym niż klaster usługi HDInsight nie jest obsługiwane.

## <a name="next-steps"></a>Kolejne kroki

Znasz sposób dodawania dodatkowych kont magazynu w istniejącym klastrze usługi HDInsight. Aby uzyskać więcej informacji dotyczących akcji skryptu, zobacz [klastrów usługi HDInsight opartej na dostosowanie systemu Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)
