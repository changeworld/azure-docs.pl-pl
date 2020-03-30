---
title: Eksportowanie danych za pomocą funkcji Apache Sqoop w usłudze Azure HDInsight za pomocą funkcji Zwijanie
description: Dowiedz się, jak zdalnie przesyłać zadania Apache Sqoop do usługi Azure HDInsight przy użyciu funkcji Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: da29785547d1b6eb4b38d07f020ba885dc5137ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767590"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Uruchamianie zadań Apache Sqoop w hdinsight z curl

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak używać funkcji Zwijanie do uruchamiania zadań Apache Sqoop w klastrze Apache Hadoop w programie HDInsight. W tym artykule pokazano, jak wyeksportować dane z usługi Azure Storage i zaimportować go do bazy danych programu SQL Server przy użyciu curl. Ten artykuł jest kontynuacją [Use Apache Sqoop z Hadoop w HDInsight](./hdinsight-use-sqoop.md).

Curl służy do wykazania, jak można wchodzić w interakcje z HDInsight przy użyciu nieprzetworzonych żądań HTTP do uruchamiania, monitorowania i pobierania wyników zadań Sqoop. Działa to przy użyciu interfejsu API WebHCat REST (wcześniej znanego jako Templeton) dostarczonego przez klaster HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie [konfigurowania środowiska testowego](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [Użyj Apache Sqoop z Hadoop w HDInsight](./hdinsight-use-sqoop.md).

* Klient do kwerendy usługi Azure SQL Database. Rozważ użycie [programu SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) lub programu Visual Studio [Code](../../sql-database/sql-database-connect-query-vscode.md).

* [Zwiń](https://curl.haxx.se/). Curl to narzędzie do przesyłania danych z lub do klastra HDInsight.

* [jq](https://stedolan.github.io/jq/). Narzędzie jq służy do przetwarzania danych JSON zwróconych z żądań REST.

* Znajomość Sqoop. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Prześlij zadania Apache Sqoop za pomocą Curl

Użyj Curl do eksportowania danych przy użyciu zadań Apache Sqoop z usługi Azure Storage do programu SQL Server.

> [!NOTE]  
> Używając programu Curl lub innego połączenia REST z usługą WebHCat, należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra usługi HDInsight. Należy również użyć nazwy klastra jako części identyfikatora URI stosowanego przy wysyłaniu żądań do serwera.

W przypadku poleceń w `USERNAME` tej sekcji zastąp go użytkownikiem, aby uwierzytelnić się w klastrze i zastąp `PASSWORD` hasłem dla konta użytkownika. Zastąp ciąg `CLUSTERNAME` nazwą klastra.

Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](https://en.wikipedia.org/wiki/Basic_access_authentication). Należy zawsze tworzyć żądania przy użyciu protokołu HTTPS (HTTP Secure), aby mieć pewność, że poświadczenia są bezpiecznie wysyłane do serwera.

1. Aby ułatwić obsługę, ustaw poniższe zmienne. W tym przykładzie jest oparty na środowisku systemu Windows, poprawić w razie potrzeby dla środowiska.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. W wierszu polecenia wpisz następujące polecenie, aby sprawdzić możliwość nawiązania połączenia z klastrem usługi HDInsight:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Użytkownik powinien otrzymywać odpowiedź podobną do następującej:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. Użyj następujących elementów, aby przesłać zadanie sqoop:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    W tym poleceniu są używane następujące parametry:

   * **-d** — `-G` ponieważ nie jest używany, domyślnie żądania do POST metody. `-d`określa wartości danych, które są wysyłane z żądaniem.

       * **user.name** — użytkownik, który uruchamia polecenie.

       * **polecenie** - Polecenie Sqoop do wykonania.

       * **statusdir** — katalog, do który zostanie zapisany stan tego zadania.

     To polecenie zwróci identyfikator zadania, który może służyć do sprawdzania stanu zadania.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Aby sprawdzić stan zadania, użyj następującego polecenia. Zamień `JOBID` na wartość zwróconą w poprzednim kroku. Na przykład, jeśli wartość `{"id":"job_1415651640909_0026"}`zwracana była , to `JOBID` będzie `job_1415651640909_0026`. W `jq` razie potrzeby zmień lokalizację.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Jeśli zadanie zostało zakończone, stan zostanie **pomyślnie .**

   > [!NOTE]  
   > To żądanie zwijania zwraca dokument notacji obiektu JavaScript (JSON) z informacjami o zadaniu; jq służy do pobierania tylko wartości stanu.

1. Po zmianie stanu zadania na **pomyślny,** można pobrać wyniki zadania z magazynu obiektów Blob platformy Azure. Parametr `statusdir` przekazany wraz z kwerendą zawiera lokalizację pliku wyjściowego; w tym `wasb:///example/data/sqoop/curl`przypadku, . Ten adres przechowuje dane wyjściowe zadania w `example/data/sqoop/curl` katalogu w domyślnym kontenerze magazynu używanym przez klaster HDInsight.

    Za pomocą witryny Azure portal można uzyskać dostęp do obiektów blob stderr i stdout.

1. Aby sprawdzić, czy dane zostały wyeksportowane, użyj następujących zapytań od klienta SQL, aby wyświetlić eksportowane dane:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Ograniczenia

* Eksport zbiorczy — w przypadku usługi HDInsight opartej na systemie Linux łącznik Sqoop używany do eksportowania danych do programu Microsoft SQL Server lub usługi Azure SQL Database nie obsługuje obecnie wstawiaków zbiorczych.
* Batching - Z systemem LINUX HDInsight, Podczas korzystania z `-batch` przełącznika podczas wykonywania wstawia, Sqoop wykona wiele wstawia zamiast wsadowego operacji wstawiania.

## <a name="summary"></a>Podsumowanie

Jak pokazano w tym dokumencie, można użyć nieprzetworzonego żądania HTTP do uruchamiania, monitorowania i wyświetlania wyników zadań Sqoop w klastrze HDInsight.

Aby uzyskać więcej informacji na temat interfejsu REST używanego w tym artykule, zobacz <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API guide</a>.

## <a name="next-steps"></a>Następne kroki

[Użyj Apache Sqoop z Apache Hadoop na HDInsight](hdinsight-use-sqoop.md)

W przypadku innych artykułów HDInsight obejmujących curl:

* [Tworzenie klastrów Apache Hadoop przy użyciu interfejsu API rest platformy Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Uruchamianie zapytań aparu z Apache Hadoop w HDInsight przy użyciu REST](apache-hadoop-use-hive-curl.md)
* [Uruchamianie zadań MapReduce z Apache Hadoop na HDInsight przy użyciu REST](apache-hadoop-use-mapreduce-curl.md)