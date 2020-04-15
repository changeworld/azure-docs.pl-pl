---
title: Monitoruj i zarządzaj Hadoop za pomocą interfejsu API Ambari REST — Azure HDInsight
description: Dowiedz się, jak używać ambari do monitorowania klastrów Hadoop i zarządzania nimi w usłudze Azure HDInsight. W tym dokumencie dowiesz się, jak korzystać z interfejsu API AMbari REST dołączonego do klastrów HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 317d12f6d5dee92d998266d4e9b6d52e6ef9c7a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381378"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Zarządzanie klastrami HDInsight przy użyciu interfejsu API Apache Ambari REST

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Dowiedz się, jak używać interfejsu API Apache Ambari REST do zarządzania klastrami Apache Hadoop i monitorowania ich w usłudze Azure HDInsight.

## <a name="what-is-apache-ambari"></a>Co to jest Apache Ambari

[Apache Ambari](https://ambari.apache.org) upraszcza zarządzanie i monitorowanie klastrów Hadoop, zapewniając łatwy w użyciu interfejs użytkownika sieci web wspierany przez [interfejsy API REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari jest domyślnie dostarczany z klastrami HDInsight opartymi na systemie Linux.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Hadoop na HDInsight. Zobacz [Wprowadzenie do HDInsight w systemie Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Bash na Ubuntu na Windows 10.  Przykłady w tym artykule używają powłoki Bash w systemie Windows 10. Aby uzyskać instrukcje instalacji, zobacz [Podręcznik instalacji systemu Windows dla systemu Linux dla systemu Windows 10.](https://docs.microsoft.com/windows/wsl/install-win10)  Inne [powłoki Unix](https://www.gnu.org/software/bash/) będzie działać, jak również.  Przykłady, z niewielkimi modyfikacjami, mogą działać w wierszu polecenia systemu Windows.  Można też użyć programu Windows PowerShell.

* jq, procesor JSON wiersza polecenia.  Zobacz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Windows PowerShell.  Możesz też użyć [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Podstawowy jednolity identyfikator zasobu dla interfejsu API odpoczynku Ambari

 Podstawowym jednolitym identyfikatorem zasobów (URI) dla interfejsu API AMbari REST w programie HDInsight jest `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`nazwa `CLUSTERNAME` klastra.  W nazwach klasterów w identyfikatorach URI **rozróżniana jest wielkość liter.**  Podczas gdy nazwa klastra w w pełni kwalifikowanej nazwy domeny`CLUSTERNAME.azurehdinsight.net`(FQDN) część identyfikatora URI ( ) jest bez uwzględniania wielkości liter, inne wystąpienia w identyfikatorze URI są uwzględniane wielkość liter.

## <a name="authentication"></a>Authentication

Łączenie się z Ambari na HDInsight wymaga protokołu HTTPS. Użyj nazwy konta administratora (domyślnie jest **to administrator)** i hasła podanego podczas tworzenia klastra.

W przypadku klastrów pakietów `admin`zabezpieczeń przedsiębiorstwa zamiast `username@domain.onmicrosoft.com`użyj w pełni kwalifikowanej nazwy użytkownika, takiej jak .

## <a name="examples"></a>Przykłady

### <a name="setup-preserve-credentials"></a>Instalator (zachowywanie poświadczeń)

Zachowaj poświadczenia, aby uniknąć ponownego ich wniesienia dla każdego przykładu.  Nazwa klastra zostanie zachowana w osobnym kroku.

**A. Bash**  
Edytuj poniższy skrypt, zastępując `PASSWORD` swoim rzeczywistym hasłem.  Następnie wprowadź polecenie.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identyfikowanie poprawnie obudowie nazwy klastra

Rzeczywista wielkość liter nazwy klastra może być inna niż oczekiwano.  Kroki w tym miejscu pokaże rzeczywistą wielkość liter, a następnie zapisać go w zmiennej dla wszystkich późniejszych przykładów.

Edytuj poniższe skrypty, aby zastąpić `CLUSTERNAME` je nazwą klastra. Następnie wprowadź polecenie. (W nazwie klastra nazwy FQDN nie jest rozróżniana wielkość liter).

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

### <a name="parsing-json-data"></a>Analizowanie danych JSON

W poniższym przykładzie użyto [jq](https://stedolan.github.io/jq/) lub [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) do przeanalizowania `health_report` dokumentu odpowiedzi JSON i wyświetlenia tylko informacji z wyników.

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

### <a name="get-the-fqdn-of-cluster-nodes"></a>Pobierz numer FQDN węzłów klastra

Może być konieczne poznanie w pełni kwalifikowanej nazwy domeny (FQDN) węzła klastra. Można łatwo pobrać FQDN dla różnych węzłów w klastrze, korzystając z następujących przykładów:

**Wszystkie węzły**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Węzły głowy**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq -r '.host_components[].HostRoles.host_name'
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
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Węzły zookeeper**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>Uzyskaj wewnętrzny adres IP węzłów klastra

Adresy IP zwracane przez przykłady w tej sekcji nie są bezpośrednio dostępne przez Internet. Są one dostępne tylko w ramach sieci wirtualnej platformy Azure, która zawiera klaster HDInsight.

Aby uzyskać więcej informacji na temat pracy z sieciami HDInsight i sieciami wirtualnymi, zobacz [Planowanie sieci wirtualnej dla usługi HDInsight](hdinsight-plan-virtual-network-deployment.md).

Aby znaleźć adres IP, musisz znać wewnętrzną w pełni kwalifikowaną nazwę domeny (FQDN) węzłów klastra. Po uzyskaniu sieci FQDN można uzyskać adres IP hosta. Poniższe przykłady najpierw kwerendy Ambari dla FQDN wszystkich węzłów hosta. Następnie wysyła zapytanie ambari dla adresu IP każdego hosta.

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

### <a name="get-the-default-storage"></a>Pobierz domyślną pamięć masową

Klastry usługi HDInsight muszą używać konta usługi Azure Storage lub magazynu usługi Data Lake jako magazynu domyślnego. Za pomocą programu Ambari można pobrać te informacje po utworzeniu klastra. Na przykład, jeśli chcesz odczytać/zapisywać dane do kontenera poza HDInsight.

Poniższe przykłady pobierają domyślną konfigurację magazynu z klastra:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Te przykłady zwracają pierwszą konfigurację`service_config_version=1`zastosowaną do serwera ( ), która zawiera te informacje. Jeśli pobierasz wartość, która została zmodyfikowana po utworzeniu klastra, może być konieczne wyświetlenie listy wersji konfiguracji i pobranie najnowszej.

Zwracana wartość jest podobna do jednego z następujących przykładów:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net`- Ta wartość wskazuje, że klaster używa konta usługi Azure Storage dla magazynu domyślnego. Wartość `ACCOUNTNAME` to nazwa konta magazynu. Część `CONTAINER` jest nazwą kontenera obiektów blob na koncie magazynu. Kontener jest katalogiem głównym pamięci masowej zgodnej z systemem plików HDFS dla klastra.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net`- Ta wartość wskazuje, że klaster używa usługi Azure Data Lake Storage Gen2 dla magazynu domyślnego. I `ACCOUNTNAME` `CONTAINER` wartości mają takie same znaczenia jak dla usługi Azure Storage wspomniano wcześniej.

* `adl://home`- Ta wartość wskazuje, że klaster używa usługi Azure Data Lake Storage Gen1 dla magazynu domyślnego.

    Aby znaleźć nazwę konta usługi Data Lake Storage, użyj następujących przykładów:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Zwracana wartość jest `ACCOUNTNAME.azuredatalakestore.net`podobna do , gdzie `ACCOUNTNAME` jest nazwa konta magazynu usługi Data Lake.

    Aby znaleźć katalog w magazynie usługi Data Lake, który zawiera magazyn dla klastra, użyj następujących przykładów:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Zwracana wartość jest `/clusters/CLUSTERNAME/`podobna do . Ta wartość jest ścieżką w ramach konta usługi Data Lake Storage. Ta ścieżka jest katalogiem głównym systemu plików zgodnego z systemem plików HDFS dla klastra.  

> [!NOTE]  
> Polecenie cmdlet [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) dostarczone przez [usługę Azure PowerShell](/powershell/azure/overview) zwraca również informacje o magazynie dla klastra.

### <a name="get-all-configurations"></a>Pobierz wszystkie konfiguracje

Pobierz konfiguracje, które są dostępne dla klastra.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

W tym przykładzie zwraca dokument JSON zawierający bieżącą konfigurację zainstalowanych składników. Zobacz wartość *znacznika.* Poniższy przykład jest fragment danych zwróconych z typu klastra platformy Spark.

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

### <a name="get-configuration-for-specific-component"></a>Pobierz konfigurację dla określonego składnika

Pobierz konfigurację składnika, który Cię interesuje. W poniższym przykładzie zastąp `INITIAL` wartością znacznika zwróconą z poprzedniego żądania.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

W tym przykładzie zwraca dokument JSON `livy2-conf` zawierający bieżącą konfigurację składnika.

### <a name="update-configuration"></a>Aktualizowanie konfiguracji

1. Utwórz `newconfig.json`plik .  
   Modyfikuj, a następnie wprowadź poniższe polecenia:

   * Wymień `livy2-conf` na nowy komponent.
   * Zamień `INITIAL` na `tag` rzeczywistą wartość pobraną z Pobierz wszystkie [konfiguracje](#get-all-configurations).

     **A. Bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     Skrypt programu PowerShell używa [pliku jq](https://stedolan.github.io/jq/).  Edytuj `C:\HD\jq\jq-win64` poniżej, aby odzwierciedlić rzeczywistą ścieżkę i wersję [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq służy do przekształcania danych pobranych z usługi HDInsight w nowy szablon konfiguracji. W szczególności te przykłady wykonać następujące akcje:

   * Tworzy unikatową wartość zawierającą ciąg "version" i datę, która jest przechowywana w `newtag`pliku .

   * Tworzy dokument główny dla nowej konfiguracji.

   * Pobiera zawartość tablicy `.items[]` i dodaje go w desired_config element. **desired_config**

   * Usuwa elementy `href` `version`, `Config` i elementy, ponieważ te elementy nie są potrzebne do przesłania nowej konfiguracji.

   * Dodaje `tag` element o wartości `version#################`. Część liczbowa jest oparta na bieżącej dacie. Każda konfiguracja musi mieć unikatowy tag.

     Na koniec dane są zapisywane w dokumencie. `newconfig.json` Struktura dokumentu powinna wyglądać podobnie do następującego przykładu:

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
   Otwórz `newconfig.json` dokument i modyfikuj/dodaj `properties` wartości w obiekcie. Poniższy przykład zmienia `"livy.server.csrf_protection.enabled"` wartość `"true"` `"false"`z do .

        "livy.server.csrf_protection.enabled": "false",

    Zapisz plik po zakończeniu wprowadzania zmian.

3. Prześlij `newconfig.json`.  
   Użyj następujących poleceń, aby przesłać zaktualizowaną konfigurację do Ambari.

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

    Te polecenia przesłać zawartość pliku **newconfig.json** do klastra jako nowej konfiguracji. Żądanie zwraca dokument JSON. Element **versionTag** w tym dokumencie powinien być zgodny z przesłanym wersją, a obiekt **configs** zawiera żądane zmiany konfiguracji.

### <a name="restart-a-service-component"></a>Ponowne uruchamianie składnika usługi

W tym momencie interfejs użytkownika sieci Web Ambari wskazuje, że usługa Spark musi zostać ponownie uruchomiona, zanim nowa konfiguracja może zostać zaa urzeczywnienia. Aby ponownie uruchomić usługę, należy wykonać następujące kroki.

1. Aby włączyć tryb konserwacji usługi Spark2, należy użyć następujących czynności:

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
    ```

2. Weryfikowanie trybu konserwacji  

    Te polecenia wysyłają dokument JSON do serwera, który włącza tryb konserwacji. Można sprawdzić, czy usługa jest teraz w trybie konserwacji przy użyciu następującego żądania:

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

    Zwracana wartość `ON`to .

3. Następnie użyj następujących opcji, aby wyłączyć usługę Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
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
    > Wartość `href` zwracana przez ten identyfikator URI używa wewnętrznego adresu IP węzła klastra. Aby użyć go spoza klastra, zastąp `10.0.0.18:8080` część fqdn klastra.  

4. Sprawdź żądanie.  
    Edytuj poniższe polecenie, zastępując `29` rzeczywistą wartością `id` zwróconą z poprzedniego kroku.  Następujące polecenia pobierają stan żądania:

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

5. Po zakończeniu poprzedniego żądania należy użyć następujących czynności, aby uruchomić usługę Spark2.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    Usługa korzysta teraz z nowej konfiguracji.

6. Na koniec użyj poniższego, aby wyłączyć tryb konserwacji.

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

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełne odwołanie do interfejsu API REST, zobacz [Apache Ambari API Reference V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Zobacz też, [Autoryzuj użytkowników dla Widoków Apache Ambari](./hdinsight-authorize-users-to-ambari.md)
