---
title: Eksportowanie danych z wykorzystaniem narzędzia Apache Sqoop w usłudze Azure HDInsight za pomocą programu Curl
description: Dowiedz się, jak zdalnie przesyłania zadań Apache Sqoop na HDInsight przy użyciu programu Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 345f492c5b2c754cbbcfa150561ee06b5a4154a5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718688"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Uruchamianie zadań z wykorzystaniem narzędzia Apache Sqoop w HDInsight za pomocą programu Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak uruchamiać zadania Apache Sqoop w klastrze usługi Apache Hadoop w HDInsight przy użyciu programu Curl. W tym artykule pokazano, jak eksportować dane z usługi Azure storage i zaimportuj go do bazy danych programu SQL Server przy użyciu programu Curl. Ten artykuł jest kontynuacją [Użyj Apache Sqoop z usługą Hadoop w HDInsight](./hdinsight-use-sqoop.md).

Narzędzie curl jest używany do pokazują, jak możesz porozmawiać z HDInsight przy użyciu surowego żądań HTTP uruchomić, monitorować i pobrać wyniki zadań narzędzia Sqoop. Działa to przy użyciu usługi WebHCat interfejsu API REST (wcześniej znane jako Templeton) dostarczonych przez klastra usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończenie [Konfiguracja środowiska testowego](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [Użyj Apache Sqoop z usługą Hadoop w HDInsight](./hdinsight-use-sqoop.md).

* Klient do wykonywania zapytań w bazie danych Azure SQL. Należy rozważyć użycie [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) lub [programu Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* [curl](https://curl.haxx.se/). Curl to narzędzie do transferu danych, z lub do klastra HDInsight.

* [jq](https://stedolan.github.io/jq/). Narzędzie jq jest używane do przetwarzania danych JSON, zwrócone w wyniku żądania REST.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Przesyłanie zadań z wykorzystaniem narzędzia Apache Sqoop przy użyciu programu Curl

Korzystanie z programu Curl, aby wyeksportować dane przy użyciu narzędzia Apache Sqoop zadań z usługi Azure storage do programu SQL Server.

> [!NOTE]  
> Używając programu Curl lub innego połączenia REST z usługą WebHCat, należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra usługi HDInsight. Należy również użyć nazwy klastra jako części identyfikatora URI stosowanego przy wysyłaniu żądań do serwera.

Dla poleceń w tej sekcji należy zastąpić `USERNAME` nazwą użytkownika w celu uwierzytelniania w klastrze, a następnie zastąp `PASSWORD` przy użyciu hasła dla konta użytkownika. Zastąp ciąg `CLUSTERNAME` nazwą klastra.
 
Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](https://en.wikipedia.org/wiki/Basic_access_authentication). Należy zawsze tworzyć żądania przy użyciu protokołu HTTPS (HTTP Secure), aby mieć pewność, że poświadczenia są bezpiecznie wysyłane do serwera.

1. W wierszu polecenia wpisz następujące polecenie, aby sprawdzić możliwość nawiązania połączenia z klastrem usługi HDInsight:

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Użytkownik powinien otrzymywać odpowiedź podobną do następującej:

    ```json
    {"status":"ok","version":"v1"}
    ```

2. Zastąp `SQLDATABASESERVERNAME`, `USERNAME@SQLDATABASESERVERNAME`, `PASSWORD`, `SQLDATABASENAME` odpowiednimi wartościami z wymagań wstępnych. Aby przesłać zadanie sqoop, należy użyć następujących:

    ```cmd
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    W tym poleceniu są używane następujące parametry:

   * **-d** — od `-G` nie jest używany, żądanie domyślnie metody POST. `-d` Określa wartości danych, które są wysyłane z żądania.

       * **User.name** — użytkownik, który uruchamia polecenie.

       * **polecenie** — polecenie narzędzia Sqoop do wykonania.

       * **statusdir** — katalog, w którym zostanie zapisany stan dla tego zadania.

     To polecenie zwróci identyfikator zadania, który może służyć do sprawdzania stanu zadania.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. Aby sprawdzić stan zadania, użyj następującego polecenia. Zastąp `JOBID` przy użyciu wartości zwracanej w poprzednim kroku. Na przykład, jeśli wartość zwracaną `{"id":"job_1415651640909_0026"}`, następnie `JOBID` będzie `job_1415651640909_0026`.

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Jeśli zadanie zostało zakończone, stan będzie **Powodzenie**.
   
   > [!NOTE]  
   > To żądanie programu Curl zwraca informacje o zadaniu; dokument z JavaScript Object Notation (JSON) jq służy do pobierania wartości stan.

4. Gdy stan zadania został zmieniony na **Powodzenie**, możesz pobrać wyniki zadania z usługi Azure Blob storage. `statusdir` Parametr przekazany z zapytaniem zawiera lokalizację pliku wyjściowego; w tym przypadku `wasb:///example/data/sqoop/curl`. Ten adres są przechowywane dane wyjściowe zadania w `example/data/sqoop/curl` katalogu domyślnego kontenera magazynu używany przez klaster usługi HDInsight.

    Dostęp do strumienia wyjściowego stderr i stdout obiektów blob, można użyć witryny Azure portal.

5. Aby sprawdzić, czy dane zostały wyeksportowane, należy użyć następujących zapytań z klienta SQL w celu wyświetlenia wyeksportowane dane:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Ograniczenia
* Zbiorcze export - HDInsight opartych na systemie Linux za pomocą, łącznik Sqoop, używany do eksportowania danych do programu Microsoft SQL Server lub usługi Azure SQL Database nie obsługuje obecnie zbiorcze operacje wstawiania.
* Przetwarzanie wsadowe — za pomocą HDInsight opartych na systemie Linux, korzystając z `-batch` przełącznika podczas wykonywania operacji wstawienia, Sqoop będzie wykonywać wiele operacji wstawiania zamiast przetwarzanie wsadowe operacji wstawiania.

## <a name="summary"></a>Podsumowanie
Jak pokazano w tym dokumencie, można użyć pierwotne żądania HTTP, uruchomić, monitorować i wyświetlić wyniki narzędzia Sqoop zadań w klastrze usługi HDInsight.

Aby uzyskać więcej informacji na temat interfejsu REST używane w tym artykule, zobacz <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Podręcznik interfejsu API REST programu Apache Sqoop</a>.

## <a name="next-steps"></a>Kolejne kroki
[Z platformą Apache Hadoop w HDInsight przy użyciu narzędzia Apache Sqoop](hdinsight-use-sqoop.md)

Aby uzyskać inne HDInsight artykuły dotyczące curl:
 
* [Tworzenie klastrów usługi Apache Hadoop przy użyciu interfejsu API REST platformy Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Uruchamianie zapytania usługi Apache Hive z usługą Apache Hadoop w HDInsight przy użyciu usługi REST](apache-hadoop-use-hive-curl.md)
* [Wykonywanie zadań MapReduce z usługą Apache Hadoop w HDInsight przy użyciu interfejsu REST](apache-hadoop-use-mapreduce-curl.md)
* [Wykonywanie zadań Apache Pig z usługą Apache Hadoop w HDInsight przy użyciu programu cURL](apache-hadoop-use-pig-curl.md)
