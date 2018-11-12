---
title: Korzystanie z technologii Hadoop Pig z użyciem usług REST w HDInsight — Azure
description: Dowiedz się, jak uruchamiać zadania Pig Latin na klaster Hadoop w usłudze Azure HDInsight przy użyciu architektury REST.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: bd729d0d2cd095339a82b8a7f38a17a20b709c15
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51013092"
---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-rest"></a>Uruchamianie zadań Pig z platformą Hadoop w HDInsight za pomocą interfejsu REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Dowiedz się, jak uruchamiać zadania Pig Latin, wprowadzając żądania REST z klastrem usługi Azure HDInsight. Narzędzie curl jest używany do pokazują, jak możesz porozmawiać z HDInsight przy użyciu interfejsu API REST usługi WebHCat.

> [!NOTE]
> Jeśli są już zaznajomieni z używaniem serwerów opartą na systemie Linux platformą Hadoop, ale zaczynasz HDInsight, zobacz [porady HDInsight opartych na systemie Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Wymagania wstępne

* Klaster usługi Azure HDInsight (Hadoop w HDInsight) (opartych na systemie Linux lub systemem Windows)

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Uruchom zadania Pig, używając programu Curl

> [!NOTE]
> Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego dostępu](http://en.wikipedia.org/wiki/Basic_access_authentication). Zawsze tworzyć żądania przy użyciu HTTP Secure (HTTPS), aby upewnić się, że poświadczenia są bezpiecznie wysyłane do serwera.
>
> Podczas korzystania z poleceń w tej sekcji, Zastąp `USERNAME` nazwą użytkownika w celu uwierzytelniania w klastrze, a następnie zastąp `PASSWORD` przy użyciu hasła dla konta użytkownika. Zastąp ciąg `CLUSTERNAME` nazwą klastra.
>


1. W wierszu polecenia wpisz następujące polecenie, aby sprawdzić możliwość nawiązania połączenia z klastrem usługi HDInsight:

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Powinna pojawić się następujące odpowiedź JSON:

        {"status":"ok","version":"v1"}

    W tym poleceniu są używane następujące parametry:

    * **-u**: nazwa użytkownika i hasło używane do uwierzytelniania żądania
    * **-G**: oznacza, że to żądanie jest żądanie GET

     Adres URL, na początek **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, jest taka sama dla wszystkich żądań. Ścieżka, **status**, wskazuje, czy żądanie jest powoduje przywrócenie stanu usługi WebHCat (znanej także jako Templeton) dla serwera.

2. Użyj poniższego kodu, aby przesłać zadanie Pig Latin do klastra:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    W tym poleceniu są używane następujące parametry:

    * **-d**: ponieważ `-G` nie jest używany, żądanie domyślnie metody POST. `-d` Określa wartości danych, które są wysyłane z żądania.

    * **User.name**: użytkownik, który uruchamia polecenie
    * **Wykonaj**: instrukcji Pig Latin do wykonania
    * **statusdir**: katalog, w którym zapisywany jest stan dla tego zadania

    > [!NOTE]
    > Należy zauważyć, że miejsca do magazynowania w instrukcji Pig Latin są zastępowane przez `+` znaku w przypadku korzystania z programu Curl.

    To polecenie powinien zwrócić identyfikator zadania, który może służyć do sprawdzania stanu zadania, na przykład:

        {"id":"job_1415651640909_0026"}

3. Aby sprawdzić stan zadania, użyj następującego polecenia

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Zastąp `JOBID` przy użyciu wartości zwracanej w poprzednim kroku. Na przykład, jeśli wartość zwracaną `{"id":"job_1415651640909_0026"}`, następnie `JOBID` jest `job_1415651640909_0026`.

    Jeśli zadanie zostało zakończone, stan jest **Powodzenie**.

    > [!NOTE]
    > To żądanie Curl zwraca dokument JavaScript Object Notation (JSON), informacje o zadaniu i jq służy do pobierania wartości stan.

## <a id="results"></a>Wyświetl wyniki

Gdy stan zadania został zmieniony na **Powodzenie**, możesz pobrać wyniki zadania. `statusdir` Parametr przekazany z zapytaniem zawiera lokalizację pliku wyjściowego; w tym przypadku `/example/pigcurl`.

HDInsight można użyć usługi Azure Storage lub Azure Data Lake Store jako domyślnego magazynu danych. Istnieją różne sposoby, aby uzyskać dane, w zależności od tego, który z nich korzystać. Aby uzyskać więcej informacji, zobacz sekcję magazynu [informacji opartych na systemie Linux HDInsight](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store) dokumentu.

## <a id="summary"></a>Podsumowanie

Jak pokazano w tym dokumencie, można użyć pierwotne żądania HTTP można uruchamiać, monitorować i wyświetlić wyniki zadania Pig w klastrze usługi HDInsight.

Aby uzyskać więcej informacji na temat interfejsu REST używane w tym artykule, zobacz [odwołanie do usługi WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat Pig na HDInsight:

* [Korzystanie z języka Pig z platformą Hadoop w HDInsight](hdinsight-use-pig.md)

Aby uzyskać informacje o innych metodach można pracować z platformą Hadoop w HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w HDInsight](hdinsight-use-hive.md)
* [Korzystanie z technologii MapReduce z platformą Hadoop w HDInsight](hdinsight-use-mapreduce.md)
