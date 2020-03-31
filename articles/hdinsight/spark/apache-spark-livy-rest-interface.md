---
title: Livy Spark umożliwia przesyłanie zadań do klastra platformy Spark w usłudze Azure HDInsight
description: Dowiedz się, jak zdalnie przesyłać zadania programu Spark do klastra usługi Azure HDInsight za pomocą interfejsu API apache Spark REST.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: ac3904284ebf20fa1d5e75f9249732be3963f677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206286"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Przesyłanie zadań zdalnych do klastra usługi HDInsight Spark przy użyciu interfejsu API REST platformy Apache Spark

Dowiedz się, jak używać [apache Livy](https://livy.incubator.apache.org/), interfejsu API Apache Spark REST, który jest używany do przesyłania zadań zdalnych do klastra platformy Azure HDInsight Spark. Aby uzyskać szczegółową dokumentację, zobacz [Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html).

Livy służy do uruchamiania interaktywnych powłok Spark lub przesłania zadań wsadowych do uruchomienia w programie Spark. W tym artykule o mówi o użyciu Livy do przesyłania zadań wsadowych. Fragmenty kodu w tym artykule używają cURL do wywołania interfejsu API REST do punktu końcowego livy spark.

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="submit-an-apache-livy-spark-batch-job"></a>Prześlij zadanie wsadowe Apache Livy Spark

Przed przesłaniem zadania wsadowego należy przekazać słoik aplikacji do magazynu klastra skojarzonego z klastrem. Możesz to zrobić za pomocą narzędzia wiersza polecenia [AzCopy](../../storage/common/storage-use-azcopy.md). Istnieją różne inne klienty, których można użyć do przekazywania danych. Więcej informacji na ten temat można znaleźć w artykule [Przekazywanie danych dla zadań Apache Hadoop w usłudze HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Przykłady

* Jeśli plik jar znajduje się w magazynie klastra (WASBS)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Jeśli chcesz przekazać nazwę pliku jar i nazwę klasy jako część pliku wejściowego (w tym przykładzie input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Uzyskaj informacje o partiach Livy Spark uruchomionych w klastrze

Składnia:

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Przykłady

* Jeśli chcesz pobrać wszystkie partie Livy Spark uruchomione w klastrze:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

* Jeśli chcesz pobrać określoną partię o danym identyfikatorze partii

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Usuwanie zadania wsadowego livy spark

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Przykład

Usuwanie zadania wsadowego o `5`identyfikatorze partii .

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark i wysoka dostępność

Livy zapewnia wysoką dostępność dla zadań platformy Spark uruchomionych w klastrze. Oto kilka przykładów.

* Jeśli usługa Livy ustępuje po przesłaniu zadania zdalnie do klastra platformy Spark, zadanie będzie nadal działać w tle. Gdy Livy jest z powrotem, przywraca stan zadania i zgłasza go z powrotem.
* Notebooki Jupyter dla HDInsight są zasilane przez Livy w wewnętrznej cenie. Jeśli notes jest uruchomiony zadanie Platformy Spark i usługa Livy zostanie ponownie uruchomiona, notes nadal uruchamia komórki kodu.

## <a name="show-me-an-example"></a>Pokaż mi przykład

W tej sekcji przyjrzymy się przykładom użycia livy spark do przesyłania zadania wsadowego, monitorowania postępu zadania, a następnie usuwania go. Aplikacja, której używamy w tym przykładzie, jest aplikacją opracowaną w artykule [Tworzenie autonomicznej aplikacji Scala i uruchamianie w klastrze HDInsight Spark](apache-spark-create-standalone-application.md). Kroki w tym miejscu zakładają:

* Plik został już skopiowany przez słoik aplikacji do konta magazynu skojarzonego z klastrem.
* CuRL został zainstalowany na komputerze, na którym próbujesz wykonać te czynności.

Wykonaj poniższe czynności:

1. Aby ułatwić obsługę, należy ustawić zmienne środowiskowe. W tym przykładzie jest oparty na środowisku systemu Windows, poprawić zmienne w razie potrzeby w swoim środowisku. Zastąp `CLUSTERNAME`i `PASSWORD` odpowiednie wartości.

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. Sprawdź, czy livy spark jest uruchomiony w klastrze. Możemy to zrobić, uzyskując listę uruchomionych partii. Jeśli używasz zadania przy użyciu Livy po raz pierwszy, dane wyjściowe powinny zwracać zero.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
    ```

    Powinieneś uzyskać dane wyjściowe podobne do następującego fragmentu kodu:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Zwróć uwagę, jak ostatni wiersz w wyjściu mówi **total:0**, co sugeruje brak uruchomionych partii.

1. Prześlijmy teraz zadanie wsadowe. Poniższy fragment kodu używa pliku wejściowego (input.txt) do przekazania nazwy jar i nazwy klasy jako parametrów. Jeśli używasz tych kroków z komputera z systemem Windows, zalecanym rozwiązaniem jest użycie pliku wejściowego.

    ```cmd
    curl -k --user "admin:%password%" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://%clustername%.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    Parametry pliku **input.txt** są zdefiniowane w następujący sposób:

    ```text
    { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Zwróć uwagę, jak ostatni wiersz wyjścia mówi **stan:starting**. Mówi również, **id:0**. W tym miejscu **0** jest identyfikator partii.

1. Teraz można pobrać stan tej konkretnej partii przy użyciu identyfikatora partii.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Dane wyjściowe teraz pokazuje **state:success**, co sugeruje, że zadanie zostało pomyślnie ukończone.

1. Jeśli chcesz, możesz teraz usunąć partię.

    ```cmd
    curl -k --user "admin:%password%" -v -X DELETE "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Ostatni wiersz danych wyjściowych pokazuje, że partia została pomyślnie usunięta. Usunięcie zadania, gdy jest uruchomione, również zabija zadanie. Jeśli usuniesz zadanie, które zostało ukończone, pomyślnie lub w inny sposób, spowoduje całkowite usunięcie informacji o zadaniu.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Aktualizacje konfiguracji Livy począwszy od wersji HDInsight 3.5

Klastry HDInsight 3.5 i powyżej domyślnie wyłączają korzystanie z lokalnych ścieżek plików w celu uzyskania dostępu do przykładowych plików danych lub słoików. Zachęcamy do użycia `wasbs://` ścieżki zamiast dostępu do słoików lub przykładowych plików danych z klastra.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Przesyłanie zadań Livy dla klastra w sieci wirtualnej platformy Azure

Jeśli łączysz się z klastrem platformy SPARK usługi HDInsight z poziomu sieci wirtualnej platformy Azure, możesz bezpośrednio połączyć się z livy w klastrze. W takim przypadku adresem URL punktu końcowego Livy jest `http://<IP address of the headnode>:8998/batches`. Tutaj **8998** to port, na którym Livy działa na headnode klastra. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usług w portach niepublicznych, zobacz [Porty używane przez usługi Apache Hadoop w programie HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Następne kroki

* [Apache Livy REST Dokumentacja API](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
