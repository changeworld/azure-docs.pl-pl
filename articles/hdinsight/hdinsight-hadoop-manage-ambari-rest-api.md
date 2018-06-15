---
title: Monitorowanie i zarządzanie nimi platformy Hadoop za pomocą narzędzia Ambari API REST - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać narzędzia Ambari do monitorowania i zarządzania klastrami Hadoop w usłudze Azure HDInsight. W tym dokumencie dowiesz się, jak używać interfejsu API REST Ambari, które zostały dołączone do klastrów usługi HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 55017b139ce89fa74a8105da05792024ecee86b2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177958"
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu API REST Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Dowiedz się, jak za pomocą interfejsu API REST Ambari do zarządzania i monitorowania klastrów platformy Hadoop w usłudze Azure HDInsight.

Apache Ambari upraszcza zarządzanie i monitorowanie klastra usługi Hadoop zapewniając łatwy do interfejsu użytkownika sieci web i interfejsu API REST. Ambari znajduje się w klastrach HDInsight, które używają systemu operacyjnego Linux. Ambari służy do monitorowania klastra i wprowadzania zmian w konfiguracji.

## <a id="whatis"></a>Co to jest Ambari

[Apache Ambari](http://ambari.apache.org) udostępnia interfejs użytkownika, który może służyć do zarządzania i monitorowania klastrów platformy Hadoop sieci web. Deweloperzy mogą integrować te możliwości w swoich aplikacjach przy użyciu [interfejsów API REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari jest udostępniana domyślnie z klastrami HDInsight opartych na systemie Linux.

## <a name="how-to-use-the-ambari-rest-api"></a>Sposób użycia interfejsu API REST Ambari

> [!IMPORTANT]
> Informacje i przykłady w tym dokumencie wymagają klastra usługi HDInsight, który korzysta z systemu operacyjnego Linux. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

Przykłady w tym dokumencie znajdują się Bourne powłoki (bash) i programu PowerShell. Przykłady bash zostały przetestowane z wersją bash GNU 4.3.11, ale powinien współpracować z innymi powłoki systemu Unix. Przykłady środowiska PowerShell zostały przetestowane z programu PowerShell 5.0, ale powinien współpracować z programem PowerShell 3.0 lub nowszej.

Jeśli przy użyciu __powłoki Bourne__ (Bash), muszą być zainstalowane następujące oprogramowanie:

* [cURL](http://curl.haxx.se/): cURL to narzędzie, które mogą służyć do pracy z interfejsami API REST z poziomu wiersza polecenia. W tym dokumencie jest używana do komunikacji przy użyciu interfejsu API REST Ambari.

Czy przy użyciu Bash lub programu PowerShell, musi również mieć [jq](https://stedolan.github.io/jq/) zainstalowane. Jq jest narzędziem do pracy z dokumentów JSON. Jest on używany w **wszystkie** przykłady Bash i **jeden** przykładów programu PowerShell.

### <a name="base-uri-for-ambari-rest-api"></a>Podstawowy identyfikator URI dla Ambari Rest API

Podstawowy identyfikator URI dla interfejsu API REST Ambari w usłudze HDInsight jest https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, gdzie **CLUSTERNAME** jest nazwą klastra.

> [!IMPORTANT]
> Gdy nazwa klastra, w pełni kwalifikowaną nazwę (FQDN) części identyfikatora URI (CLUSTERNAME.azurehdinsight.net) nie uwzględnia wielkości liter, inne zdarzenia w identyfikatorze URI jest rozróżniana wielkość liter. Na przykład, jeśli klaster o nazwie `MyCluster`, prawidłowymi identyfikatorami URI są następujące:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> Następujące identyfikatory URI zwraca błąd, ponieważ drugie wystąpienie o tej nazwie nie jest poprawną wielkość.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>Authentication

Połączenie z Ambari w usłudze HDInsight wymaga protokołu HTTPS. Nazwa konta administratora (wartość domyślna to **admin**) i hasło podane podczas tworzenia klastra.

## <a name="examples-authentication-and-parsing-json"></a>Przykłady: Uwierzytelnianie i analizowania JSON

Poniższe przykłady przedstawiają sposób żądania GET do podstawowej interfejsu API REST Ambari:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> Przykłady Bash, w tym dokumencie mieć następujące wartości domyślne:
>
> * Nazwa logowania dla klastra jest domyślna wartość `admin`.
> * `$CLUSTERNAME` zawiera nazwę klastra. Tę wartość można ustawić za pomocą `set CLUSTERNAME='clustername'`
> * Po wyświetleniu monitu wprowadź hasło do logowania do klastra (admin).

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> Przykłady programu PowerShell w tym dokumencie mieć następujące wartości domyślne:
>
> * `$creds` jest obiekt poświadczeń, który zawiera identyfikator logowania administratora i hasła dla klastra. Tę wartość można ustawić za pomocą `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"` i podawania poświadczeń po wyświetleniu monitu.
> * `$clusterName` jest ciąg znaków zawierający nazwę klastra. Tę wartość można ustawić za pomocą `$clusterName="clustername"`.

Oba przykłady zwracają dokument JSON, który rozpoczyna się od informacje podobne do poniższego przykładu:

```json
{
"href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
"Clusters" : {
    "cluster_id" : 2,
    "cluster_name" : "CLUSTERNAME",
    "health_report" : {
    "Host/stale_config" : 0,
    "Host/maintenance_state" : 0,
    "Host/host_state/HEALTHY" : 7,
    "Host/host_state/UNHEALTHY" : 0,
    "Host/host_state/HEARTBEAT_LOST" : 0,
    "Host/host_state/INIT" : 0,
    "Host/host_status/HEALTHY" : 7,
    "Host/host_status/UNHEALTHY" : 0,
    "Host/host_status/UNKNOWN" : 0,
    "Host/host_status/ALERT" : 0
    ...
```

### <a name="parsing-json-data"></a>Analizowanie danych JSON

W poniższym przykładzie użyto `jq` można przeanalizować dokumentu JSON odpowiedzi i wyświetlać tylko `health_report` informacji z wyników.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

Udostępnia programu PowerShell 3.0 i wyższe `ConvertFrom-Json` polecenia cmdlet, który konwertuje dokument JSON na obiekt, który jest łatwiej współpracować z programu PowerShell. W poniższym przykładzie użyto `ConvertFrom-Json` mają być wyświetlane tylko `health_report` informacji z wyników.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> Podczas gdy większość przykłady w tym dokumencie `ConvertFrom-Json` do wyświetlania elementów w dokumencie odpowiedzi [Ambari aktualizacji konfiguracji](#example-update-ambari-configuration) przykładzie użyto jq. Jq jest używany w tym przykładzie do utworzenia nowego szablonu z dokumentu JSON odpowiedzi.

Pełną dokumentację interfejsu API REST, zobacz [Ambari API odwołanie V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Przykład: Pobieranie nazwy FQDN węzłów klastra

Podczas pracy z usługą HDInsight, należy znać w pełni kwalifikowaną nazwę (FQDN) węzła klastra. Można łatwo pobrać nazwę FQDN dla różnych węzłów w klastrze za pomocą poniższych przykładów:

* **Wszystkie węzły**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" \
    | jq '.items[].Hosts.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.Hosts.host_name
    ```

* **HEAD węzłów**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/NAMENODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Węzłów procesu roboczego**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/DATANODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Węzły dozorcy**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Przykład: Pobieranie wewnętrznego adresu IP z węzłów klastra

> [!IMPORTANT]
> Adresy IP zwrócona przez przykłady w tej sekcji nie ma bezpośredniego połączenia za pośrednictwem Internetu. Tylko są one dostępne w ramach sieci wirtualnej Azure, zawierającą klaster usługi HDInsight.
>
> Aby uzyskać więcej informacji na temat pracy z usługi HDInsight i sieci wirtualnych, zobacz [możliwości rozszerzania HDInsight przy użyciu niestandardowych sieci wirtualnej Azure](hdinsight-extend-hadoop-virtual-network.md).

Aby znaleźć adres IP, musisz znać wewnętrzna nazwa FQDN domeny (FQDN) węzły klastra. Po utworzeniu nazwy FQDN, możesz uzyskać adres IP hosta. Poniższe przykłady najpierw zapytania Ambari dla nazwy FQDN wszystkie węzły na hoście, a następnie wyszukać Ambari adres IP każdego hosta.

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

> [!TIP]
> Poprzednich przykładach monit o hasło. W tym przykładzie jest uruchamiany `curl` polecenia wielokrotnie, więc hasło jest dostarczane jako `$PASSWORD` do wielu monity.

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts"
$resp = Invoke-WebRequest -Uri $uri -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

## <a name="example-get-the-default-storage"></a>Przykład: Pobieranie magazynu domyślne

Podczas tworzenia klastra usługi HDInsight, musisz użyć konta magazynu Azure lub usługi Data Lake Store jako domyślny magazyn dla klastra. Ambari służy do pobrania informacji o tym, po utworzeniu klastra. Na przykład, jeśli chcesz odczytu/zapisu danych do kontenera poza HDInsight.

Poniższe przykłady pobrać domyślną konfigurację magazynu z klastra:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]
> Te przykłady zwraca pierwszy konfiguracji stosowany na serwerze (`service_config_version=1`) zawierającą te informacje. Możesz pobrać wartość, która została zmodyfikowana po utworzeniu klastra, konieczne może być listę wersji konfiguracji i pobrać najnowszy numer.

Wartość zwracana jest podobny do jednego z poniższych przykładach:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` — Ta wartość wskazuje, że klaster używa konta usługi Azure Storage do przechowywania domyślne. `ACCOUNTNAME` Wartość jest nazwą konta magazynu. `CONTAINER` Fragment jest nazwa kontenera obiektów blob na koncie magazynu. Kontener jest katalog główny systemu plików HDFS zgodne magazyn dla klastra.

* `adl://home` — Ta wartość wskazuje, że klaster używa usługi Azure Data Lake Store dla domyślnego magazynu.

    Aby znaleźć nazwę konta usługi Data Lake Store, wykonaj następujące przykłady:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Wartość zwracana jest podobny do `ACCOUNTNAME.azuredatalakestore.net`, gdzie `ACCOUNTNAME` jest nazwa konta usługi Data Lake Store.

    Aby znaleźć katalogu w ramach usługi Data Lake Store, który zawiera magazyn dla klastra, wykonaj następujące przykłady:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Wartość zwracana jest podobny do `/clusters/CLUSTERNAME/`. Ta wartość jest ścieżki w ramach konta usługi Data Lake Store. Ta ścieżka jest katalogiem głównym systemu plików zgodny system plików HDFS dla klastra. 

> [!NOTE]
> `Get-AzureRmHDInsightCluster` Udostępniane przez polecenia cmdlet [programu Azure PowerShell](/powershell/azure/overview) zwraca też wartość informacji magazynu dla klastra.


## <a name="example-get-configuration"></a>Przykład: Get konfiguracji

1. Pobierz konfiguracje, które są dostępne dla klastra.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    $respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    $respObj.Content
    ```

    W tym przykładzie zwraca dokument JSON zawierający bieżącą konfigurację (identyfikowane przez *tag* wartość) dla składniki zainstalowane w klastrze. Poniższy przykład zawiera fragment danych zwróconych z typem klastra Spark.
   
   ```json
   "spark-metrics-properties" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-fairscheduler" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-sparkconf" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   }
   ```

2. Pobierz konfigurację dla składnika, który chcesz. W poniższym przykładzie Zastąp `INITIAL` o wartości tagu zwrócony z poprzedniego żądania.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    W tym przykładzie zwraca dokument JSON zawierający bieżącą konfigurację `core-site` składnika.

## <a name="example-update-configuration"></a>Przykład: Aktualizacji konfiguracji

1. Pobierz bieżącą konfigurację, Ambari są przechowywane jako "żądaną konfiguracją":

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    W tym przykładzie zwraca dokument JSON zawierający bieżącą konfigurację (identyfikowane przez *tag* wartość) dla składniki zainstalowane w klastrze. Poniższy przykład zawiera fragment danych zwróconych z typem klastra Spark.
   
    ```json
    "spark-metrics-properties" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-fairscheduler" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-sparkconf" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    }
    ```
   
    Z tej listy, należy skopiować nazwy składnika (na przykład **spark\_thrift\_sparkconf** i **tag** wartość.

2. Pobierz konfigurację dla składnika i tag przy użyciu następujących poleceń:
   
    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" \
    | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```powershell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=spark-thrift-sparkconf&tag=INITIAL" `
        -Credential $creds
    $resp.Content | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    > [!NOTE]
    > Zastąp **spark-thrift-sparkconf** i **początkowej** składnika i tag, który chcesz pobrać konfiguracji.
   
    Jq umożliwia włączanie dane pobrane z usługi HDInsight do nowego szablonu konfiguracji. W szczególności te przykłady, wykonaj następujące czynności:
   
    * Tworzy unikatową wartość zawierających ciąg "version" i daty, który jest przechowywany w `newtag`.

    * Tworzy głównego dokumentu do nowego wymaganą konfiguracją.

    * Pobiera zawartość `.items[]` tablicy i dodanie go w obszarze **desired_config** elementu.

    * Usuwa `href`, `version`, i `Config` elementy, jak te elementy nie są potrzebne do przesyłania nowej konfiguracji.

    * Dodaje `tag` element o wartości `version#################`. Część numeryczna jest oparta na bieżącą datę. Każdej konfiguracji musi mieć unikatowy tag.
     
    Na koniec, dane są zapisywane do `newconfig.json` dokumentu. Struktura dokumentu powinna wyglądać podobnie do poniższego przykładu:
     
     ```json
    {
        "Clusters": {
            "desired_config": {
            "tag": "version1459260185774265400",
            "type": "spark-thrift-sparkconf",
            "properties": {
                ....
            },
            "properties_attributes": {
                ....
            }
        }
    }
    ```

3. Otwórz `newconfig.json` dokument i zmodyfikować dodać wartości w `properties` obiektu. Poniższy przykład umożliwia zmianę wartości `"spark.yarn.am.memory"` z `"1g"` do `"3g"`. Dodano również `"spark.kryoserializer.buffer.max"` o wartości `"256m"`.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    Zapisz plik, po zakończeniu wprowadzania zmian.

4. Użyj następujących poleceń, aby przesłać zaktualizowanej konfiguracji do narzędzia Ambari.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```
   
    Te polecenia przesłać zawartość **newconfig.json** pliku do klastra jako nowe wymaganą konfiguracją. Żądanie zwraca dokumentu JSON. **VersionTag** element w tym dokumencie powinna być zgodna z wersją przesłane, i **configs** obiekt zawiera żądanego zmiany konfiguracji.

### <a name="example-restart-a-service-component"></a>Przykład: Ponowne uruchomienie składnika usługi

W tym momencie podczas przeglądania w interfejsie użytkownika sieci web Ambari, usługę Spark wskazuje, że konieczne jest ponowne uruchomienie nowej konfiguracji zostały wprowadzone. Wykonaj następujące kroki, aby ponownie uruchomić usługę.

1. Aby włączyć tryb konserwacji usługi Spark, skorzystaj z następujących:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```
   
    Te polecenia wysłanie dokumentu JSON do serwera, który włącza tryb konserwacji. Można sprawdzić, czy usługa jest teraz w trybie konserwacji, za pomocą następujących żądania:
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```
   
    Wartość zwracana jest `ON`.

2. Aby wyłączyć usługę obok, skorzystaj z następujących:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```
    
    Odpowiedź jest podobna do poniższego przykładu:
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    > [!IMPORTANT]
    > `href` Wartość zwrócona przez ten identyfikator URI używa wewnętrznego adresu IP węzła klastra. Aby korzystać z poza klastrem, części "10.0.0.18:8080" należy zastąpić nazwę FQDN klastra. 
    
    Następujące polecenia powodują pobranie stan żądania:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Odpowiedź `COMPLETED` wskazuje, że żądanie zostało ukończone.

3. Po ukończeniu poprzedniego żądania, użyj następującego polecenia można uruchomić usługi.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```
   
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```
    Usługa jest teraz przy użyciu nowej konfiguracji.

4. Na koniec użyj następującego polecenia, aby wyłączyć tryb konserwacji.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Kolejne kroki

Pełną dokumentację interfejsu API REST, zobacz [Ambari API odwołanie V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

