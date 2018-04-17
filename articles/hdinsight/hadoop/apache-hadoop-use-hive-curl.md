---
title: Hadoop Hive za pomocą Curl w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zdalnie przesyłania zadań Pig do usługi HDInsight przy użyciu programu Curl.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/26/2018
ms.author: larryfr
ms.openlocfilehash: 8a7627e35a39dd4c9c94d273aa9f02c977dc88bf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Uruchamianie zapytań Hive z usługą Hadoop w usłudze HDInsight za pomocą usługi REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dowiedz się, jak uruchamianie zapytań Hive z usługą Hadoop w usłudze Azure HDInsight klastra za pomocą interfejsu API REST usługi WebHCat.

## <a name="prerequisites"></a>Wymagania wstępne

* Opartych na systemie Linux platformą Hadoop w klastrze usługi HDInsight w wersji 3.4 lub większą.

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Klient REST. Ten dokument używa środowiska Windows PowerShell i [Curl](http://curl.haxx.se/) przykłady.

    > [!NOTE]
    > Program Azure PowerShell zawiera dedykowany poleceń cmdlet do pracy z Hive w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz [używanie Hive przy użyciu programu Azure PowerShell](apache-hadoop-use-hive-powershell.md) dokumentu.

Ten dokument używa również środowiska Windows PowerShell i [Jq](http://stedolan.github.io/jq/) do procesu zwrócone dane JSON z pozostałych żądań.

## <a id="curl"></a>Uruchamianie zapytań Hive

> [!NOTE]
> Po użyciu cURL lub innego połączenia REST z usługą WebHCat, musi uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra usługi HDInsight.
>
> Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](http://en.wikipedia.org/wiki/Basic_access_authentication). Aby upewnić się, że poświadczenia są bezpiecznie wysyłane do serwera, należy zawsze tworzyć żądania przy użyciu protokołu HTTP Secure (HTTPS).

1. Aby ustawić logowania do klastra, który jest używany przez skrypty w tym dokumencie, użyj jednej z następujących poleceń:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Aby ustawić nazwę klastra, użyj jednej z następujących poleceń:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Aby zweryfikować, że można połączyć się z klastrem usługi HDInsight, użyj jednej z następujących poleceń:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status)
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

    * `-u` -Nazwę użytkownika i hasło używane do uwierzytelnienia żądania.
    * `-G` -Oznacza to żądanie operacji GET.

   Na początku adresu URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, jest taka sama dla wszystkich żądań. Ścieżka, `/status`, wskazuje, czy żądanie jest próbę zwrócenia stanu usługi WebHCat (znanej także jako Templeton) dla serwera. Możesz również poprosić o wersji programu Hive za pomocą następującego polecenia:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    To żądanie zwraca odpowiedź podobny do następującego tekstu:

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. Należy użyć następującego można utworzyć tabeli o nazwie **log4jLogs**:

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
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
     * `statusdir` -Katalog zapisane stanu dla tego zadania.

   Te instrukcje, wykonaj następujące czynności:
   
   * `DROP TABLE` — W przypadku tabela już istnieje, zostaną usunięte.
   * `CREATE EXTERNAL TABLE` -Tworzy nową tabelę "zewnętrzne" w gałęzi. Tabele zewnętrzne przechowywać tylko definicji tabeli w gałęzi. Dane pozostaną w oryginalnej lokalizacji.

     > [!NOTE]
     > Jeśli oczekujesz zaktualizowania za pomocą zewnętrznego źródła danych, należy użyć tabel zewnętrznych. Na przykład procesu przekazywania danych lub inna operacja MapReduce.
     >
     > Usunięcie tabeli zewnętrznej jest **nie** Usuń dane, definicję tabeli.

   * `ROW FORMAT` — Sposób formatowania danych. W każdym dzienniku pola są oddzielone spacją.
   * `STORED AS TEXTFILE LOCATION` -Gdy dane są przechowywane (katalogu przykładzie/danych) i które są przechowywane jako tekst.
   * `SELECT` -Wybiera liczbę wszystkich wierszy gdzie kolumna **t4** zawiera wartość **[Błąd]**. Ta instrukcja zwraca wartość **3** są trzy wiersze, które zawierają tę wartość.

     > [!NOTE]
     > Należy zauważyć, że odstępy między instrukcje HiveQL są zastępowane przez `+` znak, gdy jest używany z Curl. Wartości w cudzysłowie, które zawierać spacji, takich jak ogranicznik, nie powinna zostać zastąpiona przez `+`.

      To polecenie zwraca identyfikator zadania, który może służyć do sprawdzania stanu zadania.

5. Aby sprawdzić stan zadania, użyj następującego polecenia:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
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

    Jeśli zadanie zostało zakończone, stan jest **zakończyło się pomyślnie**.

6. Gdy stan zadania został zmieniony na **zakończyło się pomyślnie**, wyniki zadania można pobrać z magazynu obiektów Blob platformy Azure. `statusdir` Parametr przekazany z zapytaniem zawiera lokalizację pliku wyjściowego; w takim przypadku `/example/rest`. Ten adres przechowuje dane wyjściowe w `example/curl` katalogu w magazynie domyślne klastrów.

    Można wyświetlić listę i takie pliki należy pobierać za pomocą [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Aby uzyskać więcej informacji na temat używania interfejsu wiersza polecenia Azure z usługą Azure Storage, zobacz [Użyj Azure CLI 2.0 z usługą Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentu.

## <a id="nextsteps"></a>Następne kroki

Ogólne informacje na temat programu Hive z HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje o innych metodach pracy z platformą Hadoop w usłudze HDInsight:

* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)
* [Używanie MapReduce z usługą Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)

Jeśli używasz aplikacji Tez przy użyciu Hive, zobacz informacji o debugowaniu w następujących dokumentach:

* [Użyj widoku Ambari Tez w HDInsight opartych na systemie Linux](../hdinsight-debug-ambari-tez-view.md)

Aby uzyskać więcej informacji dotyczących interfejsu API REST używany w tym dokumencie, zobacz [odwołania WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentu.

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


