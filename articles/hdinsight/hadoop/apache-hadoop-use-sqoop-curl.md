---
title: Hadoop Sqoop za pomocą Curl w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zdalnie przesyłania zadań Sqoop do usługi HDInsight przy użyciu programu Curl.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: a83b87f1ed052c6d21d337eb37bc560efbf118ba
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202149"
---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Uruchamianie zadań Sqoop z platformą Hadoop w usłudze HDInsight z Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak używać Curl do uruchomienia zadań Sqoop na klastra usługi Hadoop w usłudze HDInsight.

Zwinięcie służy do pokazują, jak można interakcję z usługą HDInsight przy użyciu pierwotne żądania HTTP można uruchamiać, monitorować i pobrać wyniki zadań Sqoop. To działanie przy użyciu usługi WebHCat interfejsu API REST (wcześniej znane jako Templeton) pochodzącymi z klastrem usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, będą potrzebne następujące czynności:

* Pełne [Sqoop użycia z usługą Hadoop w usłudze HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database) skonfigurować środowisko z klastrem usługi HDInsight i bazy danych Azure SQL.
* [Curl](http://curl.haxx.se/). Zwinięcie jest narzędziem do transferu danych z lub do klastra usługi HDInsight.
* [jq](http://stedolan.github.io/jq/). Narzędzie jq służy do przetwarzania danych JSON zwróconych z pozostałych żądań.

## <a name="submit-sqoop-jobs-by-using-curl"></a>Przesyłanie zadań Sqoop przy użyciu Curl
> [!NOTE]
> Używając programu Curl lub innego połączenia REST z usługą WebHCat, należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra usługi HDInsight. Należy również użyć nazwy klastra jako części identyfikatora URI stosowanego przy wysyłaniu żądań do serwera.
> 
> W przypadku poleceń w tej sekcji należy zastąpić ciąg **USERNAME** nazwą użytkownika w celu dokonania uwierzytelnienia w klastrze oraz zastąpić ciąg **PASSWORD** hasłem do konta użytkownika. Zastąp ciąg **CLUSTERNAME** nazwą klastra.
> 
> Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](http://en.wikipedia.org/wiki/Basic_access_authentication). Należy zawsze tworzyć żądania przy użyciu protokołu HTTPS (HTTP Secure), aby mieć pewność, że poświadczenia są bezpiecznie wysyłane do serwera.
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
     
     Adres URL, na początek **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, jest taka sama dla wszystkich żądań. Ścieżka, **/status**, wskazuje, czy żądanie jest próbę zwrócenia stanu usługi WebHCat (znanej także jako Templeton) dla serwera. 
2. Poniższa tabela przedstawia zadania sqoop:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    W tym poleceniu są używane następujące parametry:

    * **-d** — od `-G` nie jest używany domyślnie żądania metody POST. `-d` Określa dane, które są wysyłane z żądania.

        * **User.name** — użytkownik, który uruchamia polecenie.

        * **polecenie** -Sqoop polecenie do wykonania.

        * **statusdir** -katalog, w którym zostanie zapisany stan dla tego zadania.

    To polecenie zwraca identyfikator zadania, który może służyć do sprawdzania stanu zadania.

        ```json
        {"id":"job_1415651640909_0026"}
        ```

3. Aby sprawdzić stan zadania, użyj następującego polecenia. Zastąp **JOBID** o wartości zwracanej w poprzednim kroku. Na przykład, jeśli była zwracana wartość `{"id":"job_1415651640909_0026"}`, następnie **JOBID** będzie `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Jeśli zadanie zostało zakończone, stan będzie **zakończyło się pomyślnie**.
   
   > [!NOTE]
   > To żądanie Curl zwraca dokument JavaScript Object Notation (JSON), informacje o zadaniu; jq służy do pobierania wartości stan.
   > 
   > 
4. Gdy stan zadania został zmieniony na **zakończyło się pomyślnie**, wyniki zadania można pobrać z magazynu obiektów Blob platformy Azure. `statusdir` Parametr przekazany z zapytaniem zawiera lokalizację pliku wyjściowego; w takim przypadku **wasb: / / / przykład/data/sqoop/curl**. Ten adres są przechowywane dane wyjściowe zadania w **przykład/data/sqoop/zwinięcie** katalogu na domyślnego kontenera magazynu używane przez klaster usługi HDInsight.
   
    Azure portal umożliwia dostęp do strumienia wyjściowego stderr i stdout obiektów blob.  Microsoft SQL Server Management Studio umożliwia też sprawdzenie, czy przekazywany do tabeli log4jlogs dane.

## <a name="limitations"></a>Ograniczenia
* Zbiorcze export - opartych na systemie Linux z usługi HDInsight, łącznik Sqoop, używany do eksportowania danych do programu Microsoft SQL Server lub bazy danych SQL Azure nie obsługuje obecnie zbiorcze operacje wstawiania.
* Przetwarzanie wsadowe — z opartą na systemie Linux usługą HDInsight przy użyciu `-batch` przełączyć podczas wykonywania operacji wstawienia, Sqoop będzie wykonywać wiele operacji wstawienia zamiast przetwarzanie wsadowe operacji insert.

## <a name="summary"></a>Podsumowanie
Jak pokazano w tym dokumencie, można użyć raw żądania HTTP można uruchamiać, monitorować i wyświetlić wyniki zadania Sqoop w klastrze usługi HDInsight.

Aby uzyskać więcej informacji dotyczących interfejsu REST używane w tym artykule, zobacz <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Podręcznik interfejsu API REST Sqoop</a>.

## <a name="next-steps"></a>Kolejne kroki
Ogólne informacje na temat programu Hive z HDInsight:

* [Używanie Sqoop z platformą Hadoop w usłudze HDInsight](hdinsight-use-sqoop.md)

Aby uzyskać informacje o innych metodach pracy z platformą Hadoop w usłudze HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)
* [Używanie MapReduce z usługą Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)

Dla innych HDInsight artykuły dotyczące curl:
 
* [Tworzenie klastrów Hadoop przy użyciu interfejsu API REST Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Uruchamianie zapytań Hive z usługą Hadoop w usłudze HDInsight za pomocą usługi REST](apache-hadoop-use-hive-curl.md)
* [Uruchamianie zadań MapReduce z Hadoop w usłudze HDInsight za pomocą usługi REST](apache-hadoop-use-mapreduce-curl.md)
* [Uruchamianie zadań Pig z usługą Hadoop w usłudze HDInsight przy użyciu programu cURL](apache-hadoop-use-pig-curl.md)



