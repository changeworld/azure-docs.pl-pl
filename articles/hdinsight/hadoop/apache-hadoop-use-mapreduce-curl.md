---
title: Korzystanie z MapReduce i Zwinięciea przy użyciu Apache Hadoop w usłudze HDInsight — Azure
description: Dowiedz się, jak zdalnie uruchamiać zadania MapReduce z Apache Hadoop w usłudze HDInsight przy użyciu programu zwinięcie.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/27/2018
ms.openlocfilehash: 274d8dc80d9318aa3ddf4a904a5b623319ea01f4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645008"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Uruchamianie zadań MapReduce za pomocą Apache Hadoop w usłudze HDInsight przy użyciu usługi REST

Dowiedz się, jak używać interfejsu API REST Apache Hive WebHCat do uruchamiania zadań MapReduce na Apache Hadoop w klastrze usługi HDInsight. Zwinięcie służy do zademonstrowania, w jaki sposób można korzystać z usługi HDInsight przy użyciu nieprzetworzonych żądań HTTP do uruchamiania zadań MapReduce.

> [!NOTE]  
> Jeśli masz już doświadczenie w korzystaniu z serwerów Hadoop opartych na systemie Linux, ale dopiero zaczynasz korzystać z usługi HDInsight, zapoznaj się z [informacjami o Apache Hadoop opartych na systemie Linux w](../hdinsight-hadoop-linux-information.md) dokumencie usługi HDInsight.


## <a id="prereq"></a>Wymagania wstępne

* Usługa Hadoop w klastrze usługi HDInsight
* Windows PowerShell lub [zwinięcie](https://curl.haxx.se/) i [JQ](https://stedolan.github.io/jq/)

## <a id="curl"></a>Uruchamianie zadania MapReduce

> [!NOTE]  
> W przypadku korzystania z zwinięcie lub innej komunikacji REST z WebHCat należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra usługi HDInsight. Należy użyć nazwy klastra jako części identyfikatora URI, który jest używany do wysyłania żądań do serwera.
>
> Interfejs API REST jest zabezpieczony przy użyciu [podstawowego uwierzytelniania dostępu](https://en.wikipedia.org/wiki/Basic_access_authentication). Należy zawsze tworzyć żądania przy użyciu protokołu HTTPS, aby upewnić się, że poświadczenia są bezpiecznie wysyłane do serwera.

1. Aby ustawić identyfikator logowania klastra używany przez skrypty w tym dokumencie, użyj jednego z następujących poleceń:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Aby ustawić nazwę klastra, użyj jednego z następujących poleceń:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. W wierszu polecenia wpisz następujące polecenie, aby sprawdzić możliwość nawiązania połączenia z klastrem usługi HDInsight:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Otrzymasz odpowiedź podobną do następującej:

        {"status":"ok","version":"v1"}

    W tym poleceniu są używane następujące parametry:

   * **-u**: wskazuje nazwę użytkownika i hasło używane do uwierzytelniania żądania
   * **-G**: wskazuje, że ta operacja jest żądaniem Get

   Początek identyfikatora URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`, jest taki sam dla wszystkich żądań.

4. Aby przesłać zadanie MapReduce, użyj następującego polecenia:

    ```bash
    JOBID=`curl -u $LOGIN -d user.name=$LOGIN -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Koniec identyfikatora URI (/MapReduce/jar) informuje WebHCat, że to żądanie uruchamia zadanie MapReduce z klasy w pliku JAR. W tym poleceniu są używane następujące parametry:

   * **-d**: `-G` nie jest używany, dlatego żądanie jest domyślnie zgodne z metodą post. `-d` określa wartości danych, które są wysyłane wraz z żądaniem.
     * **User.Name**: użytkownik, który uruchamia polecenie
     * **jar**: Lokalizacja pliku JAR, który zawiera klasę do uruchomienia
     * **Klasa**: Klasa, która zawiera logikę MapReduce
     * **ARG**: argumenty, które mają zostać przekazane do zadania MapReduce. W takim przypadku wejściowy plik tekstowy i katalog, które są używane na potrzeby danych wyjściowych

   To polecenie powinno zwrócić identyfikator zadania, którego można użyć do sprawdzenia stanu zadania:

       job_1415651640909_0026

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

    Jeśli zadanie zostało zakończone, zwracany jest stan `SUCCEEDED`.

   > [!NOTE]  
   > To żądanie zwinięcie zwraca dokument JSON zawierający informacje o zadaniu. JQ służy do pobierania tylko wartości stanu.

6. Gdy stan zadania został zmieniony na `SUCCEEDED`, wyniki zadania można pobrać z usługi Azure Blob Storage. Parametr `statusdir`, który jest przesyłany z zapytaniem, zawiera lokalizację pliku wyjściowego. W tym przykładzie lokalizacja jest `/example/curl`. Ten adres przechowuje dane wyjściowe zadania w domyślnym magazynie klastrów w `/example/curl`.

Możesz wyświetlić i pobrać te pliki przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Aby uzyskać więcej informacji na temat pracy z obiektami BLOB w interfejsie wiersza polecenia platformy Azure, zobacz artykuł [Używanie interfejsu wiersza polecenia platformy Azure z dokumentem usługi Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) .

## <a id="nextsteps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat zadań MapReduce w usłudze HDInsight:

* [Używanie MapReduce z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)

Aby uzyskać informacje o innych sposobach pracy z usługą Hadoop w usłudze HDInsight:

* [Używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
* [Korzystanie z usługi Apache świni z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-pig.md)

Aby uzyskać więcej informacji o interfejsie REST używanym w tym artykule, zobacz [odwołanie WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
