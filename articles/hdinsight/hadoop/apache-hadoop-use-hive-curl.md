---
title: Użyj ula Apache Hadoop z curl w hdinsight - Azure
description: Dowiedz się, jak zdalnie przesyłać zadania apache pig do usługi Azure HDInsight przy użyciu curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 10a2f413142124db7547e68280a0d5e9abac9b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298754"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Uruchamianie zapytań aparu z Apache Hadoop w HDInsight przy użyciu REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dowiedz się, jak używać interfejsu API WebHCat REST do uruchamiania zapytań gałęzi Apache za pomocą apache Hadoop w klastrze usługi Azure HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w programie HDInsight. Zobacz [Wprowadzenie do HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Klient REST. Ten dokument używa [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) w programie Windows PowerShell i [Curl](https://curl.haxx.se/) on [Bash](https://docs.microsoft.com/windows/wsl/install-win10).

* Jeśli używasz Bash, będziesz również potrzebować jq, procesor JSON wiersza polecenia.  Zobacz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>Podstawowy interfejs API URI dla odpoczynku

Podstawowym jednolitym identyfikatorem zasobów (URI) dla interfejsu `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`API `CLUSTERNAME` REST w programie HDInsight jest nazwa klastra.  W nazwach klasterów w identyfikatorach URI **rozróżniana jest wielkość liter.**  Podczas gdy nazwa klastra w w pełni kwalifikowanej nazwy domeny`CLUSTERNAME.azurehdinsight.net`(FQDN) część identyfikatora URI ( ) jest bez uwzględniania wielkości liter, inne wystąpienia w identyfikatorze URI są uwzględniane wielkość liter.

## <a name="authentication"></a>Uwierzytelnianie

W przypadku korzystania z cURL lub innej komunikacji REST z WebHCat należy uwierzytelnić żądania, podając nazwę użytkownika i hasło dla administratora klastra HDInsight. Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](https://en.wikipedia.org/wiki/Basic_access_authentication). Aby upewnić się, że poświadczenia są bezpiecznie wysyłane do serwera, zawsze wysyłaj żądania przy użyciu bezpiecznego protokołu HTTP (HTTPS).

### <a name="setup-preserve-credentials"></a>Instalator (zachowywanie poświadczeń)

Zachowaj poświadczenia, aby uniknąć ponownego ich wniesienia dla każdego przykładu.  Nazwa klastra zostanie zachowana w osobnym kroku.

**A. Bash**  
Edytuj poniższy skrypt, zastępując `PASSWORD` swoim rzeczywistym hasłem.  Następnie wprowadź polecenie.

```bash
export password='PASSWORD'
```  

**B. Program PowerShell** Wykonaj poniższy kod i wprowadź poświadczenia w wyskakującym oknie:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identyfikowanie poprawnie obudowie nazwy klastra

Rzeczywista wielkość liter nazwy klastra może być inna niż oczekiwano, w zależności od sposobu utworzenia klastra.  Kroki w tym miejscu pokaże rzeczywistą wielkość liter, a następnie zapisać go w zmiennej dla wszystkich późniejszych przykładów.

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

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

1. Aby sprawdzić, czy można połączyć się z klastrem HDInsight, użyj jednego z następujących poleceń:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Otrzymasz odpowiedź podobną do następującego tekstu:

    ```json
    {"status":"ok","version":"v1"}
    ```

    W tym poleceniu są używane następujące parametry:

    * `-u`- Nazwa użytkownika i hasło używane do uwierzytelniania żądania.
    * `-G`- Wskazuje, że to żądanie jest operacją GET.

1. Początek adresu URL `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`jest taki sam dla wszystkich żądań. Ścieżka , `/status`wskazuje, że żądanie jest powrót stanu WebHCat (znany również jako Templeton) dla serwera. Można również zażądać wersji hive za pomocą następującego polecenia:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    To żądanie zwraca odpowiedź podobną do następującego tekstu:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Aby utworzyć tabelę o nazwie **log4jLogs,** użyj następujących zasad:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    To żądanie używa metody POST, która wysyła dane jako część żądania do interfejsu API REST. Z żądaniem są wysyłane następujące wartości danych:

     * `user.name`- Użytkownik, który uruchamia polecenie.
     * `execute`- Instrukcje HiveQL do wykonania.
     * `statusdir`- Katalog, do który jest zapisywany stan tego zadania.

   Te instrukcje wykonują następujące akcje:

   * `DROP TABLE`- Jeśli tabela już istnieje, zostanie usunięta.
   * `CREATE EXTERNAL TABLE`- Tworzy nową tabelę "zewnętrzną" w Hive. Tabele zewnętrzne przechowują tylko definicję tabeli w obszarze Gałęzi. Dane są pozostawione w oryginalnej lokalizacji.

     > [!NOTE]  
     > Tabele zewnętrzne powinny być używane, gdy oczekujesz, że dane źródłowe mają być aktualizowane przez źródło zewnętrzne. Na przykład zautomatyzowany proces przekazywania danych lub inna operacja MapReduce.
     >
     > Upuszczenie tabeli zewnętrznej **nie** powoduje usunięcia danych, a jedynie definicji tabeli.

   * `ROW FORMAT`- Sposób formatowania danych. Pola w każdym dzienniku są oddzielone spacją.
   * `STORED AS TEXTFILE LOCATION`- Gdzie dane są przechowywane (przykład/katalog danych) i że są przechowywane jako tekst.
   * `SELECT`- Wybiera liczbę wszystkich wierszy, w których kolumna **t4** zawiera wartość **[BŁĄD]**. Ta instrukcja zwraca wartość **3,** ponieważ istnieją trzy wiersze, które zawierają tę wartość.

     > [!NOTE]  
     > Należy zauważyć, że spacje między instrukcjami `+` HiveQL są zastępowane przez znak, gdy jest używany z Curl. Wartości cytowane, które zawierają spację, takie jak ogranicznik, nie powinny być zastępowane przez `+`.

      To polecenie zwraca identyfikator zadania, który może służyć do sprawdzania stanu zadania.

1. Aby sprawdzić stan zadania, użyj następującego polecenia:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Jeśli zadanie zostało zakończone, stan jest **zakończony pomyślnie**.

1. Po zmianie stanu zadania na **pomyślny,** można pobrać wyniki zadania z magazynu obiektów Blob platformy Azure. Parametr `statusdir` przekazany wraz z kwerendą zawiera lokalizację pliku wyjściowego; w tym `/example/rest`przypadku, . Ten adres przechowuje `example/curl` dane wyjściowe w katalogu w domyślnym magazynie klastrów.

    Można wyświetlić listę i pobrać te pliki przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Aby uzyskać więcej informacji na temat korzystania z interfejsu wiersza polecenia platformy Azure z usługą Azure Storage, zobacz [użyj dokumentu interfejsu wiersza polecenia platformy Azure z usługą Azure Storage.](https://docs.microsoft.com/azure/storage/storage-azure-cli)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat innych sposobów pracy z Hadoop na HDInsight:

* [Użyj Apache Hive z Apache Hadoop na HDInsight](hdinsight-use-hive.md)
* [Użyj MapReduce z Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md)

Aby uzyskać więcej informacji na temat interfejsu API REST używanego w tym dokumencie, zobacz dokument [referencyjny WebHCat.](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)