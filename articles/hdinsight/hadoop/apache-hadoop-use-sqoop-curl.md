---
title: Za pomocą programu Curl w HDInsight — Azure przy użyciu narzędzia Apache Sqoop
description: Dowiedz się, jak zdalnie przesyłania zadań Apache Sqoop na HDInsight przy użyciu programu Curl.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: cf7c47e14ef41f58ca1d674521da669e56e7e892
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012185"
---
# <a name="run-apache-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Uruchamiać zadania Apache Sqoop z usługą Hadoop w HDInsight za pomocą programu Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak uruchamiać zadania Apache Sqoop w klastrze usługi Apache Hadoop w HDInsight przy użyciu programu Curl.

Narzędzie curl jest używany do pokazują, jak możesz porozmawiać z HDInsight przy użyciu surowego żądań HTTP uruchomić, monitorować i pobrać wyniki zadań narzędzia Sqoop. Działa to przy użyciu usługi WebHCat interfejsu API REST (wcześniej znane jako Templeton) dostarczonych przez klastra usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, potrzebne następujące elementy:

* Pełne [Sqoop użycia z usługą Hadoop w HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database) skonfigurowanie środowiska klastra HDInsight i Azure SQL database.
* [curl](https://curl.haxx.se/). Curl to narzędzie do transferu danych, z lub do klastra HDInsight.
* [jq](https://stedolan.github.io/jq/). Narzędzie jq jest używane do przetwarzania danych JSON, zwrócone w wyniku żądania REST.

## <a name="submit-sqoop-jobs-by-using-curl"></a>Przesyłanie zadań Sqoop przy użyciu programu Curl
> [!NOTE]
> Używając programu Curl lub innego połączenia REST z usługą WebHCat, należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra usługi HDInsight. Należy również użyć nazwy klastra jako części identyfikatora URI stosowanego przy wysyłaniu żądań do serwera.
> 
> W przypadku poleceń w tej sekcji należy zastąpić ciąg **USERNAME** nazwą użytkownika w celu dokonania uwierzytelnienia w klastrze oraz zastąpić ciąg **PASSWORD** hasłem do konta użytkownika. Zastąp ciąg **CLUSTERNAME** nazwą klastra.
> 
> Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](https://en.wikipedia.org/wiki/Basic_access_authentication). Należy zawsze tworzyć żądania przy użyciu protokołu HTTPS (HTTP Secure), aby mieć pewność, że poświadczenia są bezpiecznie wysyłane do serwera.
> 
> 

1. W wierszu polecenia wpisz następujące polecenie, aby sprawdzić możliwość nawiązania połączenia z klastrem usługi HDInsight:

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Użytkownik powinien otrzymywać odpowiedź podobną do następującej:

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    W tym poleceniu są używane następujące parametry:
   
   * **-u** — nazwa użytkownika i hasło używane do uwierzytelnienia żądania.
   * **-G** — parametr wskazujący, że jest to żądanie GET.
     
     Adres URL, na początek **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, jest taka sama dla wszystkich żądań. Ścieżka, **status**, wskazuje, czy żądanie jest próbę zwrócenia stanu usługi WebHCat (znanej także jako Templeton) dla serwera. 
2. Aby przesłać zadanie sqoop, należy użyć następujących:

    ```bash
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

3. Aby sprawdzić stan zadania, użyj następującego polecenia. Zastąp **JOBID** przy użyciu wartości zwracanej w poprzednim kroku. Na przykład, jeśli wartość zwracaną `{"id":"job_1415651640909_0026"}`, następnie **JOBID** będzie `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Jeśli zadanie zostało zakończone, stan będzie **Powodzenie**.
   
   > [!NOTE]
   > To żądanie programu Curl zwraca informacje o zadaniu; dokument z JavaScript Object Notation (JSON) jq służy do pobierania wartości stan.
   > 
   > 
4. Gdy stan zadania został zmieniony na **Powodzenie**, możesz pobrać wyniki zadania z usługi Azure Blob storage. `statusdir` Parametr przekazany z zapytaniem zawiera lokalizację pliku wyjściowego; w tym przypadku **wasb: / / / przykład/data/sqoop/curl**. Ten adres są przechowywane dane wyjściowe zadania w **przykład/data/sqoop/curl** katalogu domyślnego kontenera magazynu używany przez klaster usługi HDInsight.
   
    Dostęp do strumienia wyjściowego stderr i stdout obiektów blob, można użyć witryny Azure portal.  Microsoft SQL Server Management Studio umożliwia również sprawdzić dane, które zostanie przekazany do tabeli log4jlogs.

## <a name="limitations"></a>Ograniczenia
* Zbiorcze export - HDInsight opartych na systemie Linux za pomocą, łącznik Sqoop, używany do eksportowania danych do programu Microsoft SQL Server lub usługi Azure SQL Database nie obsługuje obecnie zbiorcze operacje wstawiania.
* Przetwarzanie wsadowe — za pomocą HDInsight opartych na systemie Linux, korzystając z `-batch` przełącznika podczas wykonywania operacji wstawienia, Sqoop będzie wykonywać wiele operacji wstawiania zamiast przetwarzanie wsadowe operacji wstawiania.

## <a name="summary"></a>Podsumowanie
Jak pokazano w tym dokumencie, można użyć pierwotne żądania HTTP, uruchomić, monitorować i wyświetlić wyniki narzędzia Sqoop zadań w klastrze usługi HDInsight.

Aby uzyskać więcej informacji na temat interfejsu REST używane w tym artykule, zobacz <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Podręcznik interfejsu API REST narzędzia Sqoop</a>.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać ogólne informacje na temat programu Hive z HDInsight:

* [Z platformą Hadoop w HDInsight przy użyciu narzędzia Sqoop](hdinsight-use-sqoop.md)

Więcej informacji dotyczących innych sposobów korzystania z usługi Hadoop w HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w HDInsight](hdinsight-use-hive.md)
* [Korzystanie z języka Pig z platformą Hadoop w HDInsight](hdinsight-use-pig.md)
* [Korzystanie z technologii MapReduce z platformą Hadoop w HDInsight](hdinsight-use-mapreduce.md)

Aby uzyskać inne HDInsight artykuły dotyczące curl:
 
* [Tworzenie klastrów Hadoop przy użyciu interfejsu API REST platformy Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Uruchamianie zapytań Hive z usługą Hadoop w HDInsight przy użyciu usługi REST](apache-hadoop-use-hive-curl.md)
* [Uruchamianie zadań MapReduce z usługą Hadoop w HDInsight przy użyciu usługi REST](apache-hadoop-use-mapreduce-curl.md)
* [Uruchamianie zadań Pig z usługą Hadoop w HDInsight przy użyciu programu cURL](apache-hadoop-use-pig-curl.md)



