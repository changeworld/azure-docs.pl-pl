---
title: Używanie MapReduce i połączeniu z usługą Hadoop w HDInsight — Azure
description: Dowiedz się, jak i zdalne uruchamianie zadań MapReduce z usługą Hadoop w HDInsight przy użyciu programu Curl.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 496b7dacdb30507fe5eb2d4d7c5602003a944269
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010902"
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>Uruchamianie zadań MapReduce z usługą Hadoop w HDInsight przy użyciu usługi REST

Dowiedz się, jak uruchamiać zadania MapReduce w usłudze Hadoop w klastrze HDInsight za pomocą interfejsu API REST usługi WebHCat. Narzędzie curl jest używany do pokazują, jak możesz porozmawiać z HDInsight przy użyciu surowego żądania HTTP do uruchamiania zadań MapReduce.

> [!NOTE]
> Jeśli znasz już przy użyciu serwerów opartą na systemie Linux platformą Hadoop, ale dopiero zaczynasz korzystać z HDInsight, zobacz [co musisz wiedzieć o opartych na systemie Linux z platformą Hadoop w HDInsight](../hdinsight-hadoop-linux-information.md) dokumentu.


## <a id="prereq"></a>Wymagania wstępne

* W klastrze HDInsight Hadoop
* Program Windows PowerShell lub [Curl](http://curl.haxx.se/) i [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Uruchom zadanie MapReduce

> [!NOTE]
> Używając programu Curl lub innego połączenia REST z usługą WebHCat, należy uwierzytelnić żądania, podając nazwę użytkownika administratora klastra HDInsight i hasło. Należy użyć nazwy klastra jako części identyfikatora URI, który jest używany do wysyłania żądań do serwera.
>
> Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego dostępu](http://en.wikipedia.org/wiki/Basic_access_authentication). Należy zawsze tworzyć żądania przy użyciu protokołu HTTPS, aby upewnić się, że poświadczenia są bezpiecznie wysyłane do serwera.

1. Aby ustawić logowania do klastra, który jest używany przez skrypty w tym dokumencie, użyj jednego z poleceń followig:

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

    Pojawi się odpowiedź podobna do następujące dane JSON:

        {"status":"ok","version":"v1"}

    W tym poleceniu są używane następujące parametry:

   * **-u**: Określa nazwę użytkownika i hasło używane do uwierzytelniania żądania
   * **-G**: wskazuje, że ta operacja jest żądanie GET

   Początek identyfikatora URI **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, jest taka sama dla wszystkich żądań.

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

    Koniec identyfikatora URI (/ mapreduce/jar) informuje usługi WebHCat, to żądanie rozpoczęcia zadania MapReduce z klasy w pliku jar. W tym poleceniu są używane następujące parametry:

   * **-d**: `-G` nie jest używana, więc żądanie domyślnie metody POST. `-d` Określa wartości danych, które są wysyłane z żądania.
    * **User.name**: użytkownik, który uruchamia polecenie
    * **Plik JAR**: uruchomiono lokalizację pliku jar, który zawiera klasę jako
    * **Klasa**: klasa, która zawiera logikę MapReduce
    * **ARG**: argumenty do przekazania do zadania MapReduce. W tym przypadku, wejściowy plik tekstowy i katalog, w którym są używane dla danych wyjściowych

   To polecenie powinien zwrócić identyfikator zadania, który może służyć do sprawdzania stanu zadania:

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

    Jeśli zadanie zostało ukończone, zwracany jest stan `SUCCEEDED`.

   > [!NOTE]
   > To żądanie Curl zwraca dokument JSON przy użyciu informacji o zadaniu. Jq służy do pobierania wartości stan.

6. Gdy stan zadania został zmieniony na `SUCCEEDED`, możesz pobrać wyniki zadania z usługi Azure Blob storage. `statusdir` Parametr, który jest przekazywany z zapytaniem zawiera lokalizację pliku wyjściowego. W tym przykładzie lokalizacja to `/example/curl`. Ten adres przechowuje dane wyjściowe zadania w klastrach domyślnego magazynu w `/example/curl`.

Można wyświetlić listę i pobierać te pliki przy użyciu [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Aby uzyskać więcej informacji na temat pracy z obiektami blob z wiersza polecenia platformy Azure, zobacz [przy użyciu wiersza polecenia platformy Azure z usługą Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) dokumentu.

## <a id="nextsteps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat zadań MapReduce w HDInsight:

* [Korzystanie z technologii MapReduce z platformą Hadoop w HDInsight](hdinsight-use-mapreduce.md)

Aby uzyskać informacje o innych metodach można pracować z platformą Hadoop w HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w HDInsight](hdinsight-use-hive.md)
* [Korzystanie z języka Pig z platformą Hadoop w HDInsight](hdinsight-use-pig.md)

Aby uzyskać więcej informacji na temat interfejsu REST, który jest używany w tym artykule, zobacz [odwołanie do usługi WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
