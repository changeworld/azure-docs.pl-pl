---
title: Umożliwia przesyłanie zadań do Spark klastra w usłudze Azure HDInsight Livy Spark | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Apache Spark REST API umożliwiają przesyłanie zadań Spark zdalnie do klastra usługi Azure HDInsight.
keywords: Platforma Apache spark rest api, livy spark
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: 29cf245a03b38be4f5396a3c83c966a27cf038f3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517781"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Użyj interfejsu API Apache Spark REST do przesyłania zadań zdalnego w klastrze Spark w usłudze HDInsight

Dowiedz się, jak używać programu Livy, Apache Spark interfejsu API REST, który służy do przesyłania zadań zdalnego klastra usługi Azure HDInsight Spark. Aby uzyskać szczegółową dokumentację, zobacz [ http://livy.incubator.apache.org/ ](http://livy.incubator.apache.org/).

Livy służy do uruchamiania interaktywnego Spark powłoki lub przesyłania zadania wsadowego mają być uruchamiane na Spark. Ten artykuł zawiera informacje o przy użyciu programu Livy do przesyłania zadań wsadowych. Fragmenty kodu, w tym artykule umożliwiają cURL wywołań interfejsu API REST do punktu końcowego programu Livy Spark.

**Wymagania wstępne:**

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [klastrów utworzyć Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [cURL](http://curl.haxx.se/). W tym artykule używane narzędzie cURL w celu zademonstrowania sposobu wykonywania wywołań interfejsu API REST względem klastra Spark w usłudze HDInsight.

## <a name="submit-a-livy-spark-batch-job"></a>Prześlij zadanie wsadowe Livy Spark
Przed wysłaniem wsadowym należy przekazać jar aplikacji w magazynie klastra skojarzony z klastrem. Można użyć [ **AzCopy**](../../storage/common/storage-use-azcopy.md), narzędzie wiersza polecenia, aby to zrobić. Istnieją różne klientów, których można użyć w celu przekazania danych. Można znaleźć więcej informacji na temat je pod adresem [przekazywanie danych dotyczących zadań Hadoop w usłudze HDInsight](../hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Przykłady**:

* Jeśli plik jar w magazynie klastra (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Jeśli chcesz przekazać pliku jar, a classname jako część pliku wejściowego (w tym przykładzie dane_wejściowe.txt zgodnie)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Uzyskać informacje o partii Livy Spark uruchomione w klastrze
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Przykłady**:

* Jeśli chcesz pobrać wszystkie instancje Livy Spark uruchomione w klastrze:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Jeśli chcesz pobrać określonej partii o dany identyfikator partii
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>Usuwanie zadania wsadowego Livy Spark
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Przykład**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Livy Spark i wysokiej dostępności
Livy zapewnia wysokiej dostępności Spark zadania uruchomione w klastrze. Oto kilka przykładów.

* Jeśli usługa programu Livy ulegnie awarii po przesłaniu zadania zdalnie do klastra Spark, zadanie nadal uruchomione w tle. W przypadku programu Livy kopii zapasowych, przywraca stan zadania i raporty go.
* Notesów Jupyter w usłudze HDInsight są obsługiwane przez Livy w wewnętrznej bazie danych. Jeśli notes działa zadanie Spark i usługa Livy pobiera uruchomiona ponownie, notesu kontynuuje działanie komórki kodu. 

## <a name="show-me-an-example"></a>Pokaż przykład
W tej sekcji opisano, przykłady użycia platformy Spark Livy, aby przesłać zadanie wsadowe, monitorować postęp zadania, a następnie usuń go. Aplikacja używamy w tym przykładzie jest ten, który został opracowany w artykule [tworzenie autonomicznych aplikacji Scala i uruchomić w klastrze Spark w usłudze HDInsight](apache-spark-create-standalone-application.md). Kroki opisane w tym miejscu założono, że:

* Jar aplikacja ma już kopiowane do konta magazynu skojarzone z klastrem.
* Masz CuRL zainstalowany na komputerze, na którym próbujesz następujące kroki.

Wykonaj następujące czynności:

1. Daj nam najpierw sprawdź, czy Livy Spark jest uruchomiona w klastrze. Firma Microsoft może zrobić, pobieranie listy uruchomionych partie. Jeśli korzystasz z zadania przy użyciu programu Livy po raz pierwszy, dane wyjściowe powinien zwrócić wartość zero.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Należy pobrać dane wyjściowe podobne do następującego fragmentu kodu:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Zwróć uwagę, jak ostatni wiersz w danych wyjściowych mówi **łącznie: 0**, które sugeruje nie uruchomionego partie.

2. Daj nam przesłać zadanie wsadowe. Poniższy fragment kodu używa pliku wejściowego (dane_wejściowe.txt zgodnie) przekazywane jako parametry nazwę jar oraz nazwę klasy. Jeśli używasz tych kroków z komputerem z systemem Windows, przy użyciu pliku wejściowego jest zalecanym podejściem.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Parametry w pliku **dane_wejściowe.txt zgodnie** są zdefiniowane w następujący sposób:
   
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
   
    Zwróć uwagę, jak ostatni wiersz danych wyjściowych mówi **stanu: uruchamianie**. Również mówi, **identyfikator: 0**. W tym miejscu **0** jest identyfikator wsadu.

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
   
    Pokazuje teraz dane wyjściowe **stanu: Sukces**, które sugeruje, że zadanie zostało wykonane pomyślnie.

4. Jeśli chcesz, można usunąć zadania wsadowego.
   
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
   
    Ostatni wiersz danych wyjściowych zawiera partii zostało pomyślnie usunięte. Również usunięcie zadania, gdy jest on uruchomiony, kasuje zadanie. W przypadku usunięcia zadania, które zostało zakończone pomyślnie, lub w przeciwnym razie usuwa informacje o zadaniu całkowicie.

## <a name="using-livy-spark-on-hdinsight-35-clusters"></a>Przy użyciu programu Livy Spark w klastrach HDInsight 3.5

Klaster HDInsight 3.5 domyślnie wyłącza użycie ścieżki pliku lokalnego do pliki danych przykładowych dostępu lub słoików. Firma Microsoft zachęca do użycia `wasb://` ścieżka zamiast niego uzyskać dostępu do słoików lub przykładowe dane plików z klastra. Jeśli chcesz użyć ścieżki lokalnej, należy odpowiednio zaktualizować konfiguracji narzędzia Ambari. W tym celu:

1. Przejdź do portalu Ambari dla klastra. Interfejs sieci Web Ambari jest dostępna w klastrze usługi HDInsight w https://**CLUSTERNAME**. azurehdidnsight.net, gdzie CLUSTERNAME jest nazwą klastra.

2. W lewym obszarze nawigacji, kliknij polecenie **Livy**, a następnie kliknij przycisk **Configs**.

3. W obszarze **domyślne livy** Dodaj nazwę właściwości `livy.file.local-dir-whitelist` i ustaw jej wartość **"/"** aby pełnego dostępu do systemu plików. Jeśli chcesz zezwolić na dostęp tylko do określonego katalogu, podaj ścieżkę do tego katalogu jako wartość.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Przesyłanie zadań Livy klastra w ramach sieci wirtualnej platformy Azure

Jeśli łączysz się z klastrem Spark w usłudze HDInsight z w ramach sieci wirtualnej platformy Azure, możesz bezpośrednio z programu Livy w klastrze. W takim przypadku jest adres URL punktu końcowego programu Livy `http://<IP address of the headnode>:8998/batches`. W tym miejscu **8998** jest port, na którym jest uruchomiony Livy na headnode klastra. Aby uzyskać więcej informacji na uzyskiwanie dostępu do usług na portach niepubliczne, zobacz [porty używane przez usługi Hadoop w usłudze HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej przedstawiono niektóre problemy, które możesz napotkać podczas składania zdalnego zadania do klastry Spark przy użyciu programu Livy.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>Za pomocą zewnętrznego jar z dodatkowe miejsce do magazynowania nie jest obsługiwane.

**Problem:** zadania Livy Spark odwołuje się do zewnętrznych słoik z konta dodatkowego magazynu skojarzone z klastrem, zadanie zakończy się niepowodzeniem.

**Rozdzielczość:** upewnij się, że słoik ma być używany jest dostępny w magazynie domyślne skojarzone z automatyzację.





## <a name="next-step"></a>Następny krok

* [Dokumentacja interfejsu API usługi REST Livy](http://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

