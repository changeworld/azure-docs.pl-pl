---
title: Hadoop Pig za pomocą REST w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać REST do uruchomienia zadań Pig Latin na klastra usługi Hadoop w usłudze Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: ed5e10d1-4f47-459c-a0d6-7ff967b468c4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 4883794261116abf4925e7e4e9a8df14626c7a71
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31403512"
---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-rest"></a>Uruchamianie zadań Pig z usługą Hadoop w usłudze HDInsight przy użyciu REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Informacje o sposobie uruchamiania zadań Pig Latin dokonując REST żądań do klastra usługi Azure HDInsight. Zwinięcie służy do pokazują, jak można interakcję z usługą HDInsight przy użyciu interfejsu API REST usługi WebHCat.

> [!NOTE]
> Jeśli znasz już przy użyciu serwerów opartych na systemie Linux platformą Hadoop, ale dopiero zaczynasz korzystać z usługi HDInsight, zobacz [porady HDInsight opartych na systemie Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Wymagania wstępne

* Klaster HDInsight Azure (na platformie Hadoop w usłudze HDInsight) (opartych na systemie Linux lub z systemem Windows)

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Uruchamianie zadań Pig przy użyciu Curl

> [!NOTE]
> Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego dostępu](http://en.wikipedia.org/wiki/Basic_access_authentication). Zawsze tworzyć żądania przy użyciu HTTP Secure (HTTPS), aby upewnić się, że poświadczenia są bezpiecznie wysyłane do serwera.
>
> Korzystając z polecenia w tej sekcji, Zastąp `USERNAME` z użytkownikiem w celu uwierzytelniania w klastrze i Zastąp `PASSWORD` hasłem do konta użytkownika. Zastąp ciąg `CLUSTERNAME` nazwą klastra.
>


1. W wierszu polecenia wpisz następujące polecenie, aby sprawdzić możliwość nawiązania połączenia z klastrem usługi HDInsight:

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Powinien zostać wyświetlony następujący odpowiedź w formacie JSON:

        {"status":"ok","version":"v1"}

    W tym poleceniu są używane następujące parametry:

    * **-u**: nazwa użytkownika i hasło używane do uwierzytelniania żądania
    * **-G**: wskazuje, że to żądanie jest żądanie pobrania

     Adres URL, na początek **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, jest taka sama dla wszystkich żądań. Ścieżka, **/status**, wskazuje, czy żądanie jest powoduje przywrócenie stanu usługi WebHCat (znanej także jako Templeton) dla serwera.

2. Aby przesłać zadanie Pig Latin do klastra, należy użyć poniższego kodu:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    W tym poleceniu są używane następujące parametry:

    * **-d**: ponieważ `-G` nie jest używany domyślnie żądania metody POST. `-d` Określa dane, które są wysyłane z żądania.

    * **User.name**: użytkownik, który uruchamia polecenie
    * **wykonanie**: instrukcje Pig Latin wykonanie
    * **statusdir**: stan tego zadania jest zapisywany w katalogu

    > [!NOTE]
    > Należy zauważyć, że zastępuje spacje w instrukcjach Pig Latin `+` znak, gdy jest używany z Curl.

    To polecenie powinny zostać zwrócone identyfikator zadania, który może służyć do sprawdzania stanu zadania, na przykład:

        {"id":"job_1415651640909_0026"}

3. Aby sprawdzić stan zadania, użyj następującego polecenia

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Zastąp `JOBID` o wartości zwracanej w poprzednim kroku. Na przykład, jeśli była zwracana wartość `{"id":"job_1415651640909_0026"}`, następnie `JOBID` jest `job_1415651640909_0026`.

    Jeśli zadanie zostało zakończone, stan jest **zakończyło się pomyślnie**.

    > [!NOTE]
    > To żądanie Curl zwraca dokument JavaScript Object Notation (JSON), informacje o zadaniu i jq służy do pobierania wartości stan.

## <a id="results"></a>Wyświetl wyniki

Gdy stan zadania został zmieniony na **zakończyło się pomyślnie**, można pobrać wyniki zadania. `statusdir` Parametr przekazany z zapytaniem zawiera lokalizację pliku wyjściowego; w takim przypadku `/example/pigcurl`.

HDInsight można użyć usługi Azure Storage lub usługi Azure Data Lake Store jako domyślnego magazynu danych. Istnieją różne sposoby uzyskania danych, w zależności od tego, która z nich korzystać. Aby uzyskać więcej informacji, zobacz sekcję magazynu [informacji opartą na systemie Linux usługą HDInsight](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store) dokumentu.

## <a id="summary"></a>Podsumowanie

Jak pokazano w tym dokumencie, można użyć raw żądania HTTP można uruchamiać, monitorować i wyświetlić wyniki zadań Pig w klastrze usługi HDInsight.

Aby uzyskać więcej informacji na temat interfejsu REST używane w tym artykule, zobacz [odwołania WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat Pig w usłudze HDInsight:

* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)

Aby uzyskać informacje o innych metodach pracy z platformą Hadoop w usłudze HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
* [Używanie MapReduce z usługą Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)
