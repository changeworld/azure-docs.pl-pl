---
title: Monitorowanie i zarządzanie nimi platformy Hadoop za pomocą systemu Ambari API REST — Azure HDInsight
description: Dowiedz się, jak używać narzędzia Ambari do monitorowania i zarządzania klastrami Hadoop w usłudze Azure HDInsight. W tym dokumencie dowiesz się, jak używać interfejsu API REST Ambari, które zostały dołączone do klastrów HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: hrasheed
ms.openlocfilehash: 37d4c4dbbe623d68edf1de5454d7752a81fc2bd2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58801887"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Zarządzanie klastrami HDInsight przy użyciu interfejsu API Apache Ambari REST

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Dowiedz się, jak używać interfejsu API Apache Ambari REST do zarządzania i monitorowania klastrów Apache Hadoop w usłudze Azure HDInsight.

## <a id="whatis"></a>Co to jest Apache Ambari
[Apache Ambari](https://ambari.apache.org) upraszcza zarządzania i monitorowania klastrów platformy Hadoop, udostępniając łatwy w użyciu interfejs użytkownika wspierany przez sieci web jego [interfejsów API REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari znajduje się domyślnie z klastrami HDInsight opartych na systemie Linux.

## <a name="prerequisites"></a>Wymagania wstępne
* **Klaster Hadoop w HDInsight**. Zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Bash on Ubuntu on Windows 10**.  Przykłady w niniejszym artykule Użyj powłoki Bash w systemie Windows 10. Zobacz [podsystemu Windows dla systemu Linux instalacji Guide for Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) dla czynności instalacyjne.  Inne [powłoki systemu Unix](https://www.gnu.org/software/bash/) będzie działać tak dobrze.  Przykłady, za pomocą pewne niewielkie modyfikacje mogą pracować w wierszu polecenia Windows.  Alternatywnie można użyć programu Windows PowerShell.

* **jq**, wiersza polecenia procesora w formacie JSON.  Zobacz [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).

* **Program Windows PowerShell**.  Alternatywnie, można użyć [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uri-for-ambari-rest-api"></a>Podstawowy identyfikator URI dla interfejsu API Rest systemu Ambari

 Podstawowy identyfikator URI dla interfejsu API REST Ambari w HDInsight jest `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, gdzie `CLUSTERNAME` jest nazwą klastra.  Nazwy klastra w identyfikatory URI są **liter**.  W trakcie bez uwzględniania wielkości liter nazwy klastra, w pełni kwalifikowaną nazwę (FQDN) część identyfikatora URI (CLUSTERNAME.azurehdinsight.net) innych wystąpień w identyfikatorze URI jest rozróżniana wielkość liter.

## <a name="authentication"></a>Authentication

Nawiązywanie połączenia z systemu Ambari w HDInsight wymaga protokołu HTTPS. Użyj nazwy konta administratora (wartość domyślna to **administratora**) i hasło podane podczas tworzenia klastra.

## <a name="examples"></a>Przykłady

### <a name="setup-preserve-credentials"></a>Instalator (Zachowaj poświadczenia)
Zachowaj swoje poświadczenia, aby uniknąć ponownego wprowadzania ich na każdy przykład.  Nazwa klastra zostaną zachowane w osobnym kroku.

**A. Bash**  
Edytuj poniższy skrypt, zastępując `PASSWORD` przy użyciu rzeczywistego hasła.  Następnie wprowadź polecenie.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Określ nazwę klastra poprawnie z uwzględnieniem wielkości liter
Rzeczywiste wielkość liter w wyrazie nazwa klastra może być inny niż oczekujesz, że w zależności od sposobu utworzenia klastra.  Opisane w tym miejscu wyświetlić rzeczywiste małych i wielkich liter, a następnie zapisać ją w zmiennej o kolejne przykłady.

Edytuj skrypty poniżej, aby zastąpić `CLUSTERNAME` nazwą klastra. Następnie wprowadź polecenie. (Nazwa klastra dla nazwy FQDN nie jest rozróżniana wielkość liter).

**A. Bash**  

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>Podczas analizowania danych JSON

W poniższym przykładzie użyto [jq](https://stedolan.github.io/jq/) lub [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) analizy dokumentu odpowiedź JSON i wyświetlić tylko `health_report` informacji z wyników.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```   

### <a name="example-get-the-fqdn-of-cluster-nodes"></a> Uzyskaj nazwę FQDN węzłów klastra

Podczas pracy z HDInsight, trzeba znać w pełni kwalifikowana nazwa domeny (FQDN) węzła klastra. Można łatwo pobrać nazwy FQDN dla różnych węzłów w klastrze przy użyciu poniższych przykładach:

**Wszystkie węzły**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Węzły główne**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Węzły procesu roboczego**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Węzły dozorcy**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a> Pobierz wewnętrzny adres IP w węzłach klastra

Adresy IP zwrócona przez przykładów w tej sekcji nie są bezpośrednio dostępne za pośrednictwem Internetu. Tylko są one dostępne w ramach sieci wirtualnej Azure, która zawiera klaster HDInsight.

Aby uzyskać więcej informacji na temat pracy z sieci wirtualnej i HDInsight, zobacz [HDInsight rozszerzyć możliwości przy użyciu niestandardowych Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

Aby znaleźć adres IP, trzeba znać wewnętrzną w pełni kwalifikowaną nazwę domeny (FQDN) węzłów klastra. Po utworzeniu nazwy FQDN, możesz następnie Uzyskaj adres IP hosta. Poniższe przykłady najpierw wyszukać Ambari FQDN wszystkich węzłów hosta, a następnie zapytania Ambari dla adresu IP każdego hosta.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Pobierz magazyn domyślny

Podczas tworzenia klastra usługi HDInsight, możesz korzystać konta usługi Azure Storage lub usługi Data Lake Storage jako magazynu domyślnego dla klastra. Pobranie tych informacji, po utworzeniu klastra można używać narzędzia Ambari. Na przykład, jeśli chcesz odczytu/zapisu danych do kontenera poza HDInsight.

Poniższe przykłady pobrać domyślnej konfiguracji magazynu z klastra:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Te przykłady zwrócą to pierwsza Konfiguracja stosowany na serwerze (`service_config_version=1`) zawierającą te informacje. Jeśli pobierasz wartości, który został zmodyfikowany po utworzeniu klastra, może być konieczne Utwórz listę wersji konfiguracji i pobrać najnowsze.

Wartość zwracana jest podobny do jednego z poniższych przykładów:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` — Ta wartość wskazuje, że klaster używa konta usługi Azure Storage do magazynu domyślnego. `ACCOUNTNAME` Wartością jest nazwa konta magazynu. `CONTAINER` Fragment to nazwa kontenera obiektów blob na koncie magazynu. Kontener jest katalog główny systemu plików HDFS zgodną z magazynem klastra.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` — Ta wartość wskazuje, że klaster używa usługi Azure Data Lake Storage Gen2 do magazynu domyślnego. `ACCOUNTNAME` i `CONTAINER` wartości mają takie same znaczenia, jak wspomniano wcześniej w usłudze Azure Storage.

* `adl://home` — Ta wartość wskazuje, że klaster używa usługi Azure Data Lake Storage Gen1 do magazynu domyślnego.

    Aby znaleźć nazwę konta usługi Data Lake Storage, należy użyć poniższych przykładach:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Wartość zwracana jest podobne do `ACCOUNTNAME.azuredatalakestore.net`, gdzie `ACCOUNTNAME` jest nazwą konta usługi Data Lake Storage.

    Aby znaleźć katalogu, w ramach usługi Data Lake Storage, który zawiera magazyn dla klastra, należy użyć poniższych przykładach:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Wartość zwracana jest podobne do `/clusters/CLUSTERNAME/`. Ta wartość jest ścieżką, w ramach konta usługi Data Lake Storage. Ta ścieżka jest katalogiem głównym systemu zgodnych plików HDFS dla klastra.  

> [!NOTE]  
> [Get AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) udostępniane przez polecenia cmdlet [programu Azure PowerShell](/powershell/azure/overview) zwraca też wartość informacji magazynu dla klastra.


### <a name="get-all-configurations"></a> Pobierz wszystkie konfiguracje

Uzyskaj konfiguracje, które są dostępne dla klastra.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

W tym przykładzie zwraca dokument JSON zawierający bieżącą konfigurację (identyfikowane przez *tag* wartość) składniki zainstalowane w klastrze. Poniższy przykład jest fragment danych zwracanych przez typ klastra Spark.
   
```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Pobieranie konfiguracji dla określonego składnika

Pobierz konfigurację składnika, który Cię interesuje. W poniższym przykładzie Zastąp `INITIAL` z wartością tagu zwrócony z poprzedniego żądania.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

W tym przykładzie zwraca dokument JSON zawierający bieżącą konfigurację `livy2-conf` składnika.

### <a name="update-configuration"></a>Aktualizowanie konfiguracji

1. Utwórz `newconfig.json`.  
   Modyfikuj, a następnie wprowadź poniższe polecenia:

   * Zastąp `livy2-conf` przy użyciu odpowiedniego elementu.
   * Zastąp `INITIAL` z rzeczywistą wartością pobrane dla `tag` z [Pobierz wszystkie konfiguracje](#get-all-configurations).

     **A. Bash**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json

     ```

     **B. programu powershell**  
     Skrypt programu PowerShell używa [jq](https://stedolan.github.io/jq/).  Edytuj `C:\HD\jq\jq-win64` poniżej, aby odzwierciedlają rzeczywistej ścieżce i wersji [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

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
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Edytuj `newconfig.json`.  
   Otwórz `newconfig.json` dokument i zmodyfikować/Dodaj wartości w `properties` obiektu. Poniższy przykład zmienia wartość `"livy.server.csrf_protection.enabled"` z `"true"` do `"false"`.

        "livy.server.csrf_protection.enabled": "false",

    Po zakończeniu wprowadzania zmian, należy zapisać plik.

3. Prześlij `newconfig.json`.  
   Użyj następujących poleceń, aby przesłać zaktualizowanej konfiguracji do systemu Ambari.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Te polecenia Prześlij zawartość **newconfig.json** plików do klastra jako nowy wymaganą konfiguracją. Żądanie zwraca dokument JSON. **VersionTag** element w tym dokumencie powinny odpowiadać wersji przesłane, i **configs** obiekt zawiera zmiany konfiguracji żądanego.

### <a name="restart-a-service-component"></a>Uruchom ponownie składnik usługi

W tym momencie możesz wyświetlić interfejs użytkownika sieci web Ambari, usługę Spark wskazuje, że konieczne jest ponowne uruchomienie nowej konfiguracji zostały wprowadzone. Wykonaj następujące kroki, aby ponownie uruchomić usługę.

1. Włącz tryb obsługi dla usługi Spark2 należy wykonać następujące kroki:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```

2. Sprawdź tryb konserwacji  

    Te polecenia Wyślij dokument JSON do serwera, który włącza tryb konserwacji. Aby sprawdzić, czy usługa jest obecnie w trybie konserwacji za pomocą następujących żądania:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    Wartość zwracana jest `ON`.

3. Następnie użyj następujących czynności, aby wyłączyć usługę Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://girouxSpark.azurehdinsight.net/api/v1/clusters/girouxspark/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
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

4. Sprawdź żądanie.  
    Edytuj poniższe polecenie, zastępując `29` z rzeczywistą wartością dla `id` zwrócony z poprzedniego kroku.  Następujące polecenia powodują pobranie stan żądania:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Odpowiedź `COMPLETED` wskazuje, że żądanie zostało zakończone.

5. Po ukończeniu poprzedniego żądania, użyj następującego polecenia można uruchomić usługi Spark2.
   
    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```

    Usługa używa nowej konfiguracji.

6. Na koniec użyj następujące czynności, aby wyłączyć tryb konserwacji.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'

    ```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać pełną dokumentację interfejsu API REST, zobacz [Apache Ambari API odwołania V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

