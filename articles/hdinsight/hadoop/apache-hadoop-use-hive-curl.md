---
title: Apache Hadoop Hive za pomocą programu Curl w HDInsight — Azure
description: Dowiedz się, jak zdalnie przesyłania zadań Apache Pig na HDInsight przy użyciu programu Curl.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: hrasheed
ms.openlocfilehash: 334d7b886aa4e2130a12f0c8a7919986fdac55d1
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508123"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Uruchamianie zapytania usługi Apache Hive z usługą Apache Hadoop w HDInsight przy użyciu usługi REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dowiedz się, jak uruchomić zapytania usługi Apache Hive przy użyciu technologii Apache Hadoop w klastrze Azure HDInsight za pomocą interfejsu API REST usługi WebHCat.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster platformy Apache Hadoop w HDInsight. Zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Klient REST. Ten dokument używa [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) na programie Windows PowerShell i [Curl](https://curl.haxx.se/) na [Bash](https://docs.microsoft.com/windows/wsl/install-win10).

* Jeśli używasz powłoki Bash, konieczne będzie również jq, wiersza polecenia procesora w formacie JSON.  Zobacz [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>Podstawowy identyfikator URI dla interfejsu API Rest

Podstawowy identyfikator URI (Uniform Resource) interfejsu API REST na HDInsight jest `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, gdzie `CLUSTERNAME` jest nazwą klastra.  Nazwy klastra w identyfikatory URI są **liter**.  Podczas gdy nazwa klastra, w pełni kwalifikowaną nazwę (FQDN) części identyfikatora URI (`CLUSTERNAME.azurehdinsight.net`) nie uwzględnia wielkości liter, inne wystąpienia w identyfikatorze URI jest rozróżniana wielkość liter.

## <a name="authentication"></a>Authentication

Używając programu cURL lub innego połączenia REST z usługą WebHCat, należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra HDInsight. Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](https://en.wikipedia.org/wiki/Basic_access_authentication). Aby upewnić się, że poświadczenia są bezpiecznie wysyłane do serwera, należy zawsze tworzyć żądania przy użyciu protokołu HTTP Secure (HTTPS).

### <a name="setup-preserve-credentials"></a>Instalator (Zachowaj poświadczenia)
Zachowaj swoje poświadczenia, aby uniknąć ponownego wprowadzania ich na każdy przykład.  Nazwa klastra zostaną zachowane w osobnym kroku.

**A. Bash**  
Edytuj poniższy skrypt, zastępując `PASSWORD` przy użyciu rzeczywistego hasła.  Następnie wprowadź polecenie.

```bash
export password='PASSWORD'
```  

**B. Program PowerShell** wykonaj poniższy kod, a następnie wprowadź swoje poświadczenia w wyskakującym oknie:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Określ nazwę klastra poprawnie z uwzględnieniem wielkości liter
Rzeczywiste wielkość liter w wyrazie nazwy klastra mogą być inne, niż jest to oczekiwane, w zależności od sposobu utworzenia klastra.  Opisane w tym miejscu wyświetlić rzeczywiste małych i wielkich liter, a następnie zapisać ją w zmiennej o kolejne przykłady.

Edytuj skrypty poniżej, aby zastąpić `CLUSTERNAME` nazwą klastra. Następnie wprowadź polecenie. (Nazwa klastra dla nazwy FQDN nie jest rozróżniana wielkość liter).

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

## <a id="curl"></a>Uruchomienie zapytania programu Hive

1. Aby sprawdzić, czy możesz nawiązać połączenie klastra usługi HDInsight, użyj jednej z następujących poleceń:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Pojawi się odpowiedź podobna do następującego tekstu:

    ```json
    {"status":"ok","version":"v1"}
    ```

    W tym poleceniu są używane następujące parametry:

    * `-u` Nazwa użytkownika i hasło używane do uwierzytelnienia żądania.
    * `-G` — Wskazuje, że to żądanie jest operację pobierania.

1. Adres URL, na początek `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, jest taka sama dla wszystkich żądań. Ścieżka, `/status`, wskazuje, czy żądanie jest próbę zwrócenia stanu usługi WebHCat (znanej także jako Templeton) dla serwera. Możesz również poprosić o wersji programu Hive przy użyciu następującego polecenia:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    To żądanie zwraca odpowiedź podobna do następującego tekstu:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Aby utworzyć tabelę o nazwie należy użyć następujących **log4jLogs**:

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

    To żądanie używa metody POST, który wysyła dane jako część żądania interfejsu API REST. Następujące wartości danych są wysyłane z żądaniem:

     * `user.name` Użytkownik, który uruchamia polecenie.
     * `execute` Instrukcje HiveQL do wykonania.
     * `statusdir` -Katalogu, który stan dla tego zadania jest zapisywany.

   Te instrukcje, wykonaj następujące czynności:

   * `DROP TABLE` -Jeśli tabela już istnieje, zostanie usunięta.
   * `CREATE EXTERNAL TABLE` -Tworzy nową tabelę "external" w gałęzi. Tabele zewnętrzne przechowywać w definicji tabeli w gałęzi. Dane pozostaną w oryginalnej lokalizacji.

     > [!NOTE]  
     > Jeśli potrzebujesz danych bazowych do zaktualizowania za pomocą zewnętrznego źródła, należy używać tabel zewnętrznych. Na przykład proces przekazywania danych lub inna operacja MapReduce.
     >
     > Usunięcie tabeli zewnętrznej jest **nie** usunąć dane w definicji tabeli.

   * `ROW FORMAT` — W jaki sposób dane są sformatowane. Pola w każdym dzienniku są oddzielone spacją.
   * `STORED AS TEXTFILE LOCATION` — Gdzie dane są przechowywane (katalog przykład/danych) i które są przechowywane jako tekst.
   * `SELECT` -Wybiera liczbę wszystkich wierszy gdzie kolumna **t4** zawiera wartość **[Błąd]** . Ta instrukcja zwraca wartość **3** , jak istnieją trzy wiersze, które zawierają tę wartość.

     > [!NOTE]  
     > Należy zauważyć, że spacji między instrukcje HiveQL są zastępowane przez `+` znaku w przypadku korzystania z programu Curl. Wartości w cudzysłowie, zawierające spację, takich jak ogranicznik, nie powinna zostać zastąpiona przez `+`.

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

    Jeśli zadanie zostało zakończone, stan jest **Powodzenie**.

1. Gdy stan zadania został zmieniony na **Powodzenie**, możesz pobrać wyniki zadania z usługi Azure Blob storage. `statusdir` Parametr przekazany z zapytaniem zawiera lokalizację pliku wyjściowego; w tym przypadku `/example/rest`. Ten adres są przechowywane dane wyjściowe w `example/curl` katalogu w klastrach domyślny magazyn.

    Można wyświetlić listę i pobierać te pliki przy użyciu [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Aby uzyskać więcej informacji na temat korzystania z wiersza polecenia platformy Azure z usługą Azure Storage, zobacz [wiersza polecenia platformy Azure w użycia z usługą Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentu.

## <a id="nextsteps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat programu Hive z HDInsight:

* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight](hdinsight-use-hive.md)

Więcej informacji dotyczących innych sposobów korzystania z usługi Hadoop w HDInsight:

* [Use Apache Pig z platformą Apache Hadoop w HDInsight](hdinsight-use-pig.md)
* [Korzystanie z technologii MapReduce z platformą Apache Hadoop w HDInsight](hdinsight-use-mapreduce.md)

Aby uzyskać więcej informacji na temat interfejsu API REST używanego w tym dokumencie, zobacz [odwołanie do usługi WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentu.