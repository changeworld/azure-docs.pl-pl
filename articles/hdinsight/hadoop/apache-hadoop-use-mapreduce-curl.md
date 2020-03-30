---
title: Użyj MapReduce i Curl z Apache Hadoop w HDInsight - Azure
description: Dowiedz się, jak zdalnie uruchamiać zadania MapReduce z Apache Hadoop w programie HDInsight za pomocą funkcji Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: abc3cc8c526e37e18f1e67b109a9a8e15ff8c989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302716"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Uruchamianie zadań MapReduce z Apache Hadoop na HDInsight przy użyciu REST

Dowiedz się, jak używać interfejsu API REST interfejsu Api Apache Hive WebHCat do uruchamiania zadań MapReduce w programie Apache Hadoop w klastrze HDInsight. Curl służy do wykazania, jak można wchodzić w interakcje z HDInsight przy użyciu nieprzetworzonych żądań HTTP do uruchamiania mapreduce zadań.

> [!NOTE]  
> Jeśli jesteś już zaznajomiony z używaniem serwerów Hadoop opartych na systemie Linux, ale jesteś nowy w HDInsight, [zobacz, co musisz wiedzieć o apache Hadoop opartym na Linuksie w dokumencie HDInsight.](../hdinsight-hadoop-linux-information.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w programie HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

Dostępne opcje:
  * Program Windows PowerShell lub
  * [Zwinąć](https://curl.haxx.se/) z [jq](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Uruchamianie zadania MapReduce

> [!NOTE]  
> Korzystając z funkcji Zwijanie lub innej komunikacji REST z webhcatem, należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra HDInsight. Należy użyć nazwy klastra jako części identyfikatora URI, który jest używany do wysyłania żądań do serwera.
>
> Interfejs API REST jest zabezpieczony przy użyciu [uwierzytelniania dostępu podstawowego.](https://en.wikipedia.org/wiki/Basic_access_authentication) Zawsze należy wysyłać żądania przy użyciu protokołu HTTPS, aby upewnić się, że poświadczenia są bezpiecznie wysyłane do serwera.

### <a name="curl"></a>Narzędzie Curl

1. Aby ułatwić obsługę, ustaw poniższe zmienne. W tym przykładzie jest oparty na środowisku systemu Windows, poprawić w razie potrzeby dla środowiska.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. W wierszu polecenia wpisz następujące polecenie, aby sprawdzić możliwość nawiązania połączenia z klastrem usługi HDInsight:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    W tym poleceniu są używane następujące parametry:

   * **-u**: Wskazuje nazwę użytkownika i hasło użyte do uwierzytelnienia żądania
   * **-G:** Wskazuje, że ta operacja jest żądaniem GET

   Początek identyfikatora URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`jest taka sama dla wszystkich żądań.

    Otrzymasz odpowiedź podobną do następującej JSON:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Aby przesłać zadanie MapReduce, użyj następującego polecenia. W razie potrzeby zmodyfikuj ścieżkę do **jq.**

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    Koniec identyfikatora URI (/mapreduce/jar) informuje WebHCat, że to żądanie rozpoczyna zadanie MapReduce z klasy w pliku jar. W tym poleceniu są używane następujące parametry:

   * **-d** `-G` : nie jest używany, więc żądanie domyślnie do POST metody. `-d`określa wartości danych, które są wysyłane z żądaniem.
     * **user.name**: Użytkownik, który uruchamia polecenie
     * **jar**: Lokalizacja pliku jar, który zawiera klasę, która ma być uruchomiony
     * **klasa**: Klasa zawierająca logikę MapReduce
     * **arg**: Argumenty, które mają być przekazywane do zadania MapReduce. W takim przypadku wejściowy plik tekstowy i katalog, które są używane do

    To polecenie powinno zwrócić identyfikator zadania, który może służyć do sprawdzania stanu zadania:

       job_1415651640909_0026

1. Aby sprawdzić stan zadania, użyj następującego polecenia. Zastąp `JOBID` wartość wartości **rzeczywistej** zwróconej w poprzednim kroku. W razie potrzeby zmień lokalizację **jq.**

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Aby ułatwić obsługę, ustaw poniższe zmienne. Zamień `CLUSTERNAME` na rzeczywistą nazwę klastra. Wykonaj polecenie i wprowadź hasło logowania klastra po wyświetleniu monitu.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. Użyj następującego polecenia, aby sprawdzić, czy można połączyć się z klastrem HDInsight:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Otrzymasz odpowiedź podobną do następującej JSON:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Aby przesłać zadanie MapReduce, użyj następującego polecenia:

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

    Koniec identyfikatora URI (/mapreduce/jar) informuje WebHCat, że to żądanie rozpoczyna zadanie MapReduce z klasy w pliku jar. W tym poleceniu są używane następujące parametry:

    * **user.name**: Użytkownik, który uruchamia polecenie
    * **jar**: Lokalizacja pliku jar, który zawiera klasę, która ma być uruchomiony
    * **klasa**: Klasa zawierająca logikę MapReduce
    * **arg**: Argumenty, które mają być przekazywane do zadania MapReduce. W takim przypadku wejściowy plik tekstowy i katalog, które są używane do

   To polecenie powinno zwrócić identyfikator zadania, który może służyć do sprawdzania stanu zadania:

       job_1415651640909_0026

1. Aby sprawdzić stan zadania, użyj następującego polecenia:

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

### <a name="both-methods"></a>Obie metody

1. Jeśli zadanie zostanie ukończone, `SUCCEEDED`zwracany stan to .

1. Po zmianie stanu zadania na `SUCCEEDED`, można pobrać wyniki zadania z magazynu obiektów Blob platformy Azure. Parametr, `statusdir` który jest przekazywany z kwerendą zawiera lokalizację pliku wyjściowego. W tym przykładzie `/example/curl`lokalizacja to . Ten adres przechowuje dane wyjściowe zadania w `/example/curl`domyślnym magazynie klastrów w .

Można wyświetlić listę i pobrać te pliki przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Aby uzyskać więcej informacji na temat korzystania z interfejsu wiersza polecenia platformy Azure do pracy z magazynem obiektów Blob platformy Azure, zobacz [Szybki start: Tworzenie, pobieranie i listy obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure.](../../storage/blobs/storage-quickstart-blobs-cli.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje o innych sposobach pracy z Hadoop w programie HDInsight:

* [Użyj MapReduce z Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md)
* [Użyj Apache Hive z Apache Hadoop na HDInsight](hdinsight-use-hive.md)

Aby uzyskać więcej informacji na temat interfejsu REST, który jest używany w tym artykule, zobacz [WebHCat Reference](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
