---
title: Monitorowanie i zarządzanie nimi platformy Hadoop za pomocą systemu Ambari API REST — Azure HDInsight
description: Dowiedz się, jak używać narzędzia Ambari do monitorowania i zarządzania klastrami Hadoop w usłudze Azure HDInsight. W tym dokumencie dowiesz się, jak używać interfejsu API REST Ambari, które zostały dołączone do klastrów HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 03b4cc919086ff2a8eb038ad9c4f45200e9a6246
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715113"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Zarządzanie klastrami HDInsight przy użyciu interfejsu API Apache Ambari REST

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Dowiedz się, jak używać interfejsu API Apache Ambari REST do zarządzania i monitorowania klastrów Apache Hadoop w usłudze Azure HDInsight.

Apache Ambari upraszcza zarządzanie i monitorowanie klastra usługi Hadoop, zapewniając łatwy do użycia interfejsu użytkownika sieci web i interfejsu API REST. Ambari znajduje się w klastrach HDInsight, które używają systemu operacyjnego Linux. Ambari służy do monitorowania klastra i wprowadzania zmian w konfiguracji.

## <a id="whatis"></a>Co to jest Apache Ambari

[Apache Ambari](https://ambari.apache.org) zawiera internetowy interfejs użytkownika, który może służyć do zarządzania i monitorowania klastrów platformy Hadoop. Deweloperzy mogą integrować możliwości swoich aplikacji za pomocą [interfejsów API REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari znajduje się domyślnie z klastrami HDInsight opartych na systemie Linux.

## <a name="how-to-use-the-ambari-rest-api"></a>Jak używać interfejsu API REST systemu Ambari

> [!IMPORTANT]  
> Informacje i przykłady, w tym dokumencie wymagają klastra usługi HDInsight, który używa systemu operacyjnego Linux. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

W przykładach w tym dokumencie podano Bourne powłoki (powłoki bash) i programu PowerShell. W przykładach bash zostały przetestowane z wersją bash GNU 4.3.11, ale powinny współpracować z innymi powłoki systemu Unix. Przykłady programu PowerShell zostały przetestowane za pomocą programu PowerShell w wersji 5.0, ale powinny działać z programem PowerShell 3.0 lub nowszej.

Jeśli przy użyciu __powłoki Bourne__ (Bash), muszą być zainstalowane następujące oprogramowanie:

* [cURL](https://curl.haxx.se/): cURL to narzędzie, który może służyć do pracy z interfejsami API REST z poziomu wiersza polecenia. W tym dokumencie jest używany do komunikowania się z interfejsu API REST Ambari.

Czy za pomocą powłoki Bash lub programu PowerShell, musisz również posiadać [jq](https://stedolan.github.io/jq/) zainstalowane. Jq to narzędzie do pracy z dokumentów JSON. Jest on używany w **wszystkich** przykłady powłoki Bash, i **jeden** przykładów programu PowerShell.

### <a name="base-uri-for-ambari-rest-api"></a>Podstawowy identyfikator URI dla interfejsu API Rest systemu Ambari

Podstawowy identyfikator URI dla interfejsu API REST Ambari w HDInsight jest https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, gdzie **CLUSTERNAME** jest nazwą klastra.

> [!IMPORTANT]  
> W trakcie bez uwzględniania wielkości liter nazwy klastra, w pełni kwalifikowaną nazwę (FQDN) część identyfikatora URI (CLUSTERNAME.azurehdinsight.net) innych wystąpień w identyfikatorze URI jest rozróżniana wielkość liter. Na przykład, jeśli klaster o nazwie `MyCluster`, prawidłowe identyfikatory URI są następujące:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> Następujące identyfikatory URI zwróci błąd, ponieważ drugie wystąpienie nazwy nie jest poprawną wielkość.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>Authentication

Nawiązywanie połączenia z systemu Ambari w HDInsight wymaga protokołu HTTPS. Użyj nazwy konta administratora (wartość domyślna to **administratora**) i hasło podane podczas tworzenia klastra.

## <a name="examples-authentication-and-parsing-json"></a>Przykłady: Uwierzytelnianie i analizowania danych JSON

W poniższych przykładach pokazano, jak utworzyć żądanie GET względem podstawowego interfejsu API REST Ambari:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]  
> W przykładach powłoki Bash, w tym dokumencie upewnij następujące założenia:
>
> * Nazwa logowania dla klastra jest wartością domyślną `admin`.
> * `$CLUSTERNAME` zawiera nazwę klastra. Tę wartość można ustawić przy użyciu `set CLUSTERNAME='clustername'`
> * Po wyświetleniu monitu wprowadź hasło dla danych logowania klastra (administratora).

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]  
> Przykłady programu PowerShell, w tym dokumencie upewnij następujące założenia:
>
> * `$creds` jest obiekt poświadczeń zawierający identyfikator logowania administratora i hasło klastra. Tę wartość można ustawić przy użyciu `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"` i podając poświadczenia po wyświetleniu monitu.
> * `$clusterName` to jest ciąg zawierający nazwę klastra. Tę wartość można ustawić przy użyciu `$clusterName="clustername"`.

Oba przykłady zwrócą dokument JSON, który rozpoczyna się od informacje podobne do poniższego przykładu:

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

### <a name="parsing-json-data"></a>Podczas analizowania danych JSON

W poniższym przykładzie użyto `jq` analizy dokumentu odpowiedź JSON i wyświetlić tylko `health_report` informacji z wyników.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

Program PowerShell 3.0 lub nowszej udostępnia `ConvertFrom-Json` polecenia cmdlet, które konwertuje dokument JSON na obiekt, który jest łatwiej pracować z poziomu programu PowerShell. W poniższym przykładzie użyto `ConvertFrom-Json` mają być wyświetlane tylko `health_report` informacji z wyników.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]  
> Podczas gdy większość przykładów w tym dokumencie używane `ConvertFrom-Json` do wyświetlania elementów w dokumencie odpowiedzi [Ambari aktualizacji konfiguracji](#example-update-ambari-configuration) przykładzie użyto jq. Jq jest używany w tym przykładzie do utworzenia nowego szablonu z dokumentu odpowiedź JSON.

Aby uzyskać pełną dokumentację interfejsu API REST, zobacz [Apache Ambari API odwołania V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Przykład: Uzyskaj nazwę FQDN węzłów klastra

Podczas pracy z HDInsight, trzeba znać w pełni kwalifikowana nazwa domeny (FQDN) węzła klastra. Można łatwo pobrać nazwy FQDN dla różnych węzłów w klastrze przy użyciu poniższych przykładach:

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

* **Węzły główne**

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

* **Węzły procesu roboczego**

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

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Przykład: Pobierz wewnętrzny adres IP w węzłach klastra

> [!IMPORTANT]  
> Adresy IP zwrócona przez przykładów w tej sekcji nie są bezpośrednio dostępne za pośrednictwem Internetu. Tylko są one dostępne w ramach sieci wirtualnej Azure, która zawiera klaster HDInsight.
>
> Aby uzyskać więcej informacji na temat pracy z sieci wirtualnej i HDInsight, zobacz [HDInsight rozszerzyć możliwości przy użyciu niestandardowych Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

Aby znaleźć adres IP, trzeba znać wewnętrzną w pełni kwalifikowaną nazwę domeny (FQDN) węzłów klastra. Po utworzeniu nazwy FQDN, możesz następnie Uzyskaj adres IP hosta. Poniższe przykłady najpierw wyszukać Ambari FQDN wszystkich węzłów hosta, a następnie zapytania Ambari dla adresu IP każdego hosta.

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

> [!TIP]  
> Poprzednie przykłady monit o podanie hasła. W tym przykładzie jest uruchamiany `curl` polecenie wiele razy, aby hasła jest dostarczana jako `$PASSWORD` do wielu monity.

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

## <a name="example-get-the-default-storage"></a>Przykład: Pobierz magazyn domyślny

Podczas tworzenia klastra usługi HDInsight, możesz korzystać konta usługi Azure Storage lub usługi Data Lake Storage jako magazynu domyślnego dla klastra. Pobranie tych informacji, po utworzeniu klastra można używać narzędzia Ambari. Na przykład, jeśli chcesz odczytu/zapisu danych do kontenera poza HDInsight.

Poniższe przykłady pobrać domyślnej konfiguracji magazynu z klastra:

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
> Te przykłady zwrócą to pierwsza Konfiguracja stosowany na serwerze (`service_config_version=1`) zawierającą te informacje. Jeśli pobierasz wartości, który został zmodyfikowany po utworzeniu klastra, może być konieczne Utwórz listę wersji konfiguracji i pobrać najnowsze.

Wartość zwracana jest podobny do jednego z poniższych przykładów:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` — Ta wartość wskazuje, że klaster używa konta usługi Azure Storage do magazynu domyślnego. `ACCOUNTNAME` Wartością jest nazwa konta magazynu. `CONTAINER` Fragment to nazwa kontenera obiektów blob na koncie magazynu. Kontener jest katalog główny systemu plików HDFS zgodną z magazynem klastra.

* `adl://home` — Ta wartość wskazuje, że klaster używa usługi Azure Data Lake Storage do magazynu domyślnego.

    Aby znaleźć nazwę konta usługi Data Lake Storage, należy użyć poniższych przykładach:

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

    Wartość zwracana jest podobne do `ACCOUNTNAME.azuredatalakestore.net`, gdzie `ACCOUNTNAME` jest nazwą konta usługi Data Lake Storage.

    Aby znaleźć katalogu, w ramach usługi Data Lake Storage, który zawiera magazyn dla klastra, należy użyć poniższych przykładach:

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

    Wartość zwracana jest podobne do `/clusters/CLUSTERNAME/`. Ta wartość jest ścieżką, w ramach konta usługi Data Lake Storage. Ta ścieżka jest katalogiem głównym systemu zgodnych plików HDFS dla klastra. 

> [!NOTE]  
> `Get-AzureRmHDInsightCluster` Udostępniane przez polecenia cmdlet [programu Azure PowerShell](/powershell/azure/overview) zwraca też wartość informacji magazynu dla klastra.


## <a name="example-get-configuration"></a>Przykład: Pobierz konfigurację

1. Uzyskaj konfiguracje, które są dostępne dla klastra.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    $respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    $respObj.Content
    ```

    W tym przykładzie zwraca dokument JSON zawierający bieżącą konfigurację (identyfikowane przez *tag* wartość) składniki zainstalowane w klastrze. Poniższy przykład jest fragment danych zwracanych przez typ klastra Spark.
   
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

2. Pobierz konfigurację składnika, który Cię interesuje. W poniższym przykładzie Zastąp `INITIAL` z wartością tagu zwrócony z poprzedniego żądania.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    W tym przykładzie zwraca dokument JSON zawierający bieżącą konfigurację `core-site` składnika.

## <a name="example-update-configuration"></a>Przykład: Zaktualizuj konfigurację

1. Pobierz bieżącą konfigurację systemu Ambari, są przechowywane jako "wymaganą konfiguracją":

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    W tym przykładzie zwraca dokument JSON zawierający bieżącą konfigurację (identyfikowane przez *tag* wartość) składniki zainstalowane w klastrze. Poniższy przykład jest fragment danych zwracanych przez typ klastra Spark.
   
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

2. Pobieranie konfiguracji dla składnika i tagów przy użyciu następujących poleceń:
   
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
    > Zastąp **spark-thrift-sparkconf** i **początkowej** za pomocą składnika i tag, który chcesz pobrać konfiguracji.
   
    Jq umożliwia włączanie danych pobranych z HDInsight do nowego szablonu konfiguracji. W szczególności te przykłady, wykonaj następujące czynności:
   
    * Tworzy unikatową wartość zawierającą ciąg "wersja" i daty, która jest przechowywana w `newtag`.

    * Tworzy dokument głównej do nowej pożądanej konfiguracji.

    * Pobiera zawartość `.items[]` macierz, a następnie dodaje go w folderze **desired_config** elementu.

    * Usuwa `href`, `version`, i `Config` elementy, jak te elementy nie są wymagane do przesyłania nowej konfiguracji.

    * Dodaje `tag` element z wartością `version#################`. Część numeryczna opiera się na bieżącą datę. Każda konfiguracja musi mieć unikatowy tag.
     
    Na koniec dane są zapisywane do `newconfig.json` dokumentu. Strukturę dokumentu powinny wyglądać podobnie do poniższego przykładu:
     
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

3. Otwórz `newconfig.json` dokument i zmodyfikować/Dodaj wartości w `properties` obiektu. Poniższy przykład zmienia wartość `"spark.yarn.am.memory"` z `"1g"` do `"3g"`. Dodaje także `"spark.kryoserializer.buffer.max"` o wartości `"256m"`.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    Po zakończeniu wprowadzania zmian, należy zapisać plik.

4. Użyj następujących poleceń, aby przesłać zaktualizowanej konfiguracji do systemu Ambari.
   
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
   
    Te polecenia Prześlij zawartość **newconfig.json** plików do klastra jako nowy wymaganą konfiguracją. Żądanie zwraca dokument JSON. **VersionTag** element w tym dokumencie powinny odpowiadać wersji przesłane, i **configs** obiekt zawiera zmiany konfiguracji żądanego.

### <a name="example-restart-a-service-component"></a>Przykład: Uruchom ponownie składnik usługi

W tym momencie możesz wyświetlić interfejs użytkownika sieci web Ambari, usługę Spark wskazuje, że konieczne jest ponowne uruchomienie nowej konfiguracji zostały wprowadzone. Wykonaj następujące kroki, aby ponownie uruchomić usługę.

1. Włącz tryb obsługi dla usługi Spark, należy wykonać następujące kroki:

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
   
    Te polecenia Wyślij dokument JSON do serwera, który włącza tryb konserwacji. Aby sprawdzić, czy usługa jest obecnie w trybie konserwacji za pomocą następujących żądania:
   
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

2. Następnie użyj następujących czynności, aby wyłączyć usługę:

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
    > `href` Wartość zwrócona przez ten identyfikator URI jest używany wewnętrzny adres IP w węzła klastra. Aby korzystać z poza klastrem, Zastąp fragment "10.0.0.18:8080" nazwy FQDN klastra. 
    
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

    Odpowiedź `COMPLETED` wskazuje, że żądanie zostało zakończone.

3. Po ukończeniu poprzedniego żądania, użyj następującego polecenia do uruchomienia usługi.
   
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
    Usługa używa nowej konfiguracji.

4. Na koniec użyj następujące czynności, aby wyłączyć tryb konserwacji.
   
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

Aby uzyskać pełną dokumentację interfejsu API REST, zobacz [Apache Ambari API odwołania V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

