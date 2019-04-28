---
title: Przesyłanie zadań do klastra Spark w usłudze Azure HDInsight za pomocą platformy Spark usługi Livy
description: Dowiedz się, jak używać interfejsu API REST programu Apache Spark do przesyłania zadań platformy Spark zdalne z klastrem usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 99175f79e030a55991947313e4a7e32a738d6adb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097404"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Użyj interfejsu API REST programu Apache Spark, aby przesłać zdalnej obsługi zadań do klastra usługi HDInsight Spark

Dowiedz się, jak używać [Apache, usługi Livy](https://livy.incubator.apache.org/), [platformy Apache Spark](https://spark.apache.org/) interfejsu API REST, który jest używany do przesyłania zadań zdalnego w klastrze usługi HDInsight Spark. Aby uzyskać szczegółową dokumentację, zobacz [ https://livy.incubator.apache.org/ ](https://livy.incubator.apache.org/).

Za pomocą usługi Livy do uruchamiania interakcyjnego Spark powłoki lub przesyłania zadania usługi batch mają być uruchamiane na platformie Spark. Ten artykuł zawiera informacje o przy użyciu programu Livy można przesłać zadania usługi batch. Fragmenty kodu, w tym artykule używane jest narzędzie cURL do wykonywania wywołań interfejsu API REST do punktu końcowego usługi Livy platformy Spark.

**Wymagania wstępne:**

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [cURL](https://curl.haxx.se/). W tym artykule używa programu cURL w celu zademonstrowania sposobu wykonywania wywołań interfejsu API REST względem klastra usługi HDInsight Spark.

## <a name="submit-an-apache-livy-spark-batch-job"></a>Przesyłanie zadania usługi batch Apache Spark usługi Livy
Przed przesłaniem zadania usługi batch, należy przekazać plik jar aplikacji w magazynie klastra skojarzonego z klastrem. Możesz to zrobić za pomocą narzędzia wiersza polecenia [**AzCopy**](../../storage/common/storage-use-azcopy.md). Brak różnych klientów, których można użyć w celu przekazywania danych. Więcej informacji na ten temat można znaleźć w artykule [Przekazywanie danych dla zadań Apache Hadoop w usłudze HDInsight](../hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"

**Przykłady**:

* Jeśli plik jar znajduje się w magazynie klastra (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
* Jeśli chcesz przekazać pliku jar i classname jako część pliku wejściowego (w tym przykładzie input.txt)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Uzyskaj informacje na partiach Spark usługi Livy uruchomionego w klastrze
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Przykłady**:

* Jeśli chcesz pobrać wszystkie instancje Spark usługi Livy uruchomionego w klastrze:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches" 
* Jeśli chcesz pobrać określonej partii o dany identyfikator partii
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>Usuwanie zadania wsadowego Spark usługi Livy
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Przykład**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Usługi Livy Spark i wysokiej dostępności
Usługi Livy zapewnia wysoką dostępność Spark zadania uruchomione w klastrze. Oto kilka przykładów.

* Jeśli usługi Livy ulegnie awarii po przesłaniu zadania zdalne do klastra Spark, zadanie będzie nadal działać w tle. Gdy usługi Livy jest tworzenie kopii zapasowej, przywraca stan zadania i raporty, które je z powrotem.
* Notesy Jupyter notebook dla HDInsight są obsługiwane przez usługi Livy w wewnętrznej bazie danych. Jeśli notes zostało uruchomione zadanie Spark i ponownym pobiera usługi Livy, Notes będzie nadal działać komórki kodu. 

## <a name="show-me-an-example"></a>Pokaż przykład
W tej sekcji przyjrzymy się przykładów, aby przesłać zadanie usługi batch, monitorować postęp zadania i usunąć go za pomocą usługi Livy platformy Spark. Aplikacja używamy w tym przykładzie jest ten, który został opracowany w artykule [tworzenie autonomicznych aplikacji Scala i uruchomić w klastrze HDInsight Spark](apache-spark-create-standalone-application.md). Opisane w tym miejscu przyjęto założenie, że:

* Za pośrednictwem pliku jar aplikacji zostały już skopiowane do konta magazynu skojarzonego z klastrem.
* Masz CuRL zainstalowane na komputerze, na którym próbujesz następujące kroki.

Wykonaj poniższe czynności:

1. Daj nam najpierw sprawdzić, czy usługi Livy Spark działa w klastrze. Możemy to zrobić przez pobranie listy uruchomionych partii. Jeśli używasz zadania przy użyciu programu Livy po raz pierwszy, danych wyjściowych powinna zwrócić zero.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Powinna pojawić się dane wyjściowe podobne do następującego fragmentu kodu:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Zwróć uwagę, jak ostatni wiersz w danych wyjściowych jest wyświetlany komunikat **łącznie: 0**, co sugeruje nie uruchomionego partii.

2. Poinformuj nas teraz przesłać zadanie usługi batch. Poniższy fragment kodu używa pliku wejściowego (input.txt) do przekazania jako parametry nazwę pliku jar i nazwę klasy. Jeśli te kroki są uruchomione na komputerze Windows, przy użyciu pliku wejściowego jest zalecanym podejściem.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
   
    Parametry w pliku **input.txt** są zdefiniowane w następujący sposób:
   
        { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:
   
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
   
    Zwróć uwagę, jak ostatni wiersz danych wyjściowych jest wyświetlany komunikat **stan: uruchamianie**. Jest również, **identyfikator: 0**. W tym miejscu **0** jest identyfikator wsadu.

3. Teraz można pobrać stanu tej partii określone przy użyciu identyfikatora partii.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Dane wyjściowe pokazuje teraz **stan: powodzenie**, co sugeruje, że zadanie zostało pomyślnie ukończone.

4. Jeśli chcesz, możesz teraz usunąć usługi batch.
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Ostatni wiersz danych wyjściowych zawiera usługi batch został pomyślnie usunięty. Trwa usuwanie zadania, gdy uruchomiona jest również kasuje zadania. Jeśli usuniesz zadanie, które zostało ukończone pomyślnie, lub w przeciwnym razie usuwa informacje o zadaniu całkowicie.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Aktualizacje konfiguracji usługi Livy, począwszy od wersji HDInsight 3.5

HDInsight 3.5 klastrów i powyżej, domyślnie wyłączyć użycie lokalne ścieżki do plików do dostępu do przykładowych plików danych lub plikach JAR. Firma Microsoft zachęca do użycia `wasb://` ścieżkę zamiast tego dostępu plikach JAR i przykładowe dane plików z klastra. Jeśli chcesz użyć ścieżki lokalnej, należy odpowiednio Zaktualizuj konfigurację systemu Ambari. W tym celu:

1. Przejdź do portalu Ambari klastra. Interfejs użytkownika sieci Web Ambari jest dostępna w klastrze usługi HDInsight w https://**CLUSTERNAME**. azurehdidnsight.net, gdzie CLUSTERNAME jest nazwą klastra.

2. Na lewym pasku nawigacyjnym kliknij **Livy**, a następnie kliknij przycisk **Configs**.

3. W obszarze **domyślnej usługi livy** Dodaj nazwę właściwości `livy.file.local-dir-whitelist` i ustaw jej wartość na **"/"** aby zezwolenia na pełny dostęp do systemu plików. Jeśli chcesz zezwolić na dostęp tylko do określonego katalogu, należy podać ścieżkę do tego katalogu jako wartość.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Przesyłanie zadań usługi Livy klastra w ramach sieci wirtualnej platformy Azure

Jeśli łączysz się z klastrem usługi HDInsight Spark z w ramach usługi Azure Virtual Network możesz mogą łączyć się bezpośrednio do usługi Livy w klastrze. W takim przypadku adres URL punktu końcowego usługi Livy jest `http://<IP address of the headnode>:8998/batches`. W tym miejscu **8998** jest port, na którym usługi Livy działa na głównym węzłem klastra. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usług w niepublicznych portów, zobacz [porty używane przez usługi Apache Hadoop w HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej przedstawiono niektóre problemy, które możesz napotkać podczas przy użyciu programu Livy do przesłania zdalnych zadań do klastrów platformy Spark.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>Za pomocą zewnętrznego pliku jar z dodatkowego magazynu nie jest obsługiwana.

**Problem:** Jeśli zadanie platformy Spark usługi Livy odwołuje się do zewnętrznego pliku jar z konta magazynu dodatkowego skojarzonego z klastrem, zadanie kończy się niepowodzeniem.

**Rozwiązanie:** Upewnij się, że plik jar, którego chcesz użyć jest dostępny w domyślnego magazynu skojarzonego z klastrem HDInsight.





## <a name="next-step"></a>Następny krok

* [Dokumentacja interfejsu API REST usługi Livy Apache](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

