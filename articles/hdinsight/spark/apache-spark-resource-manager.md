---
title: Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight
description: Dowiedz się, jak używać zarządzania zasobami dla klastrów Spark w usłudze Azure HDInsight w celu uzyskania lepszej wydajności.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: hrasheed
ms.openlocfilehash: ac0109ff8c5dd7f6013acefbe5ee08a13494cb77
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "71001767"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight 

Dowiedz się, jak uzyskać dostęp do interfejsów, takich jak interfejs użytkownika [Apache Ambari](https://ambari.apache.org/) Apache Hadoop, interfejs użytkownika [przędzy](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) oraz serwer historii platformy Spark skojarzony z klastrem [Apache Spark](https://spark.apache.org/) i jak dostosować konfigurację klastra pod kątem optymalnej wydajności.

**Wymagania wstępne:**

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Otwórz interfejs użytkownika sieci Web Ambari

Apache Ambari służy do monitorowania klastra i wprowadzania zmian w konfiguracji. Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure Portal](../hdinsight-administer-use-portal-linux.md#open-the-apache-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>Otwórz serwer historii platformy Spark

Serwer historii platformy Spark jest interfejsem użytkownika sieci Web do kończenia i uruchamiania aplikacji platformy Spark. Jest to rozszerzenie interfejsu użytkownika sieci Web platformy Spark.

**Aby otworzyć interfejs użytkownika sieci Web serwera historii platformy Spark**

1. W [Azure Portal](https://portal.azure.com/)Otwórz klaster Spark. Aby uzyskać więcej informacji, zobacz Wyświetlanie [i wyświetlanie klastrów](../hdinsight-administer-use-portal-linux.md#showClusters).
2. W obszarze **szybkie linki**kliknij pozycję **pulpit nawigacyjny klastra**, a następnie kliknij pozycję **serwer historii platformy Spark** .

    ![Serwer historii platformy Spark](./media/apache-spark-resource-manager/launch-history-server.png "Serwer historii platformy Spark")

    Po wyświetleniu monitu wprowadź poświadczenia administratora dla klastra Spark. Serwer historii platformy Spark można także otworzyć, przechodząc do następującego adresu URL:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Zastąp `<ClusterName>` nazwą klastra Spark.

Interfejs użytkownika sieci Web serwera historii platformy Spark wygląda następująco:

![Serwer historii usługi HDInsight Spark](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Otwórz interfejs użytkownika przędzy

Za pomocą interfejsu użytkownika PRZĘDZy można monitorować aplikacje, które są aktualnie uruchomione w klastrze Spark.

1. W [Azure Portal](https://portal.azure.com/)Otwórz klaster Spark. Aby uzyskać więcej informacji, zobacz Wyświetlanie [i wyświetlanie klastrów](../hdinsight-administer-use-portal-linux.md#showClusters).
2. W obszarze **szybkie linki**kliknij pozycję **pulpit nawigacyjny klastra**, a następnie kliknij pozycję **przędza**.

    ![Uruchom interfejs użytkownika PRZĘDZy](./media/apache-spark-resource-manager/hdi-launch-apache-yarn.png)

   > [!TIP]  
   > Alternatywnie można również uruchomić interfejs użytkownika PRZĘDZy z interfejsu użytkownika Ambari. Aby uruchomić interfejs użytkownika Ambari, kliknij pozycję **pulpit nawigacyjny klastra**, a następnie kliknij pozycję **pulpit nawigacyjny klastra usługi HDInsight**. W interfejsie użytkownika Ambari kliknij pozycję **przędza**, kliknij pozycję **szybkie linki**, kliknij pozycję aktywne Menedżer zasobów, a następnie kliknij pozycję **interfejs użytkownika Menedżer zasobów**.

## <a name="optimize-clusters-for-spark-applications"></a>Optymalizowanie klastrów dla aplikacji platformy Spark

Trzy kluczowe parametry, które mogą być używane do konfiguracji platformy Spark w zależności od wymagań aplikacji, są `spark.executor.instances`, `spark.executor.cores` i `spark.executor.memory`. Program wykonujący to proces uruchomiony dla aplikacji platformy Spark. Jest on uruchamiany w węźle procesu roboczego i jest odpowiedzialny za wykonywanie zadań aplikacji. Domyślna liczba modułów wykonujących i rozmiary wykonawców dla każdego klastra jest obliczana na podstawie liczby węzłów procesu roboczego i rozmiaru węzła procesu roboczego. Te informacje są przechowywane w `spark-defaults.conf` w węzłach głównych klastra.

Trzy parametry konfiguracji można skonfigurować na poziomie klastra (dla wszystkich aplikacji uruchamianych w klastrze) lub można je określić również dla poszczególnych aplikacji.

### <a name="change-the-parameters-using-ambari-ui"></a>Zmień parametry za pomocą interfejsu Ambari
1. W interfejsie użytkownika Ambari kliknij pozycję **Spark**, kliknij pozycję **konfiguracje, a**następnie rozwiń węzeł niestandardowe usługi **Spark-Defaults**.

    ![Ustawianie parametrów przy użyciu niestandardowych Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png "Ustawianie parametrów przy użyciu niestandardowych Ambari")
2. Wartości domyślne są dobrym sposobem, że cztery aplikacje Spark działają współbieżnie w klastrze. Można zmienić te wartości z interfejsu użytkownika, jak pokazano na poniższym zrzucie ekranu:

    ![Ustawianie parametrów przy użyciu Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png "Ustawianie parametrów przy użyciu Ambari")

3. Kliknij przycisk **Zapisz** , aby zapisać zmiany konfiguracji. W górnej części strony zostanie wyświetlony monit o ponowne uruchomienie wszystkich usług, których to dotyczy. Kliknij przycisk **Uruchom ponownie**.

    ![Ponowne uruchamianie usług](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Zmień parametry aplikacji działającej w notesie Jupyter
W przypadku aplikacji uruchamianych w notesie Jupyter można użyć Magic `%%configure`, aby wprowadzić zmiany w konfiguracji. W idealnym przypadku należy wprowadzić takie zmiany na początku aplikacji, zanim zaczniesz korzystać z pierwszej komórki kodu. W ten sposób zagwarantujemy, że konfiguracja zostanie zastosowana do sesji usługi Livy, gdy zostanie utworzona. Jeśli chcesz zmienić konfigurację na późniejszym etapie w aplikacji, należy użyć parametru `-f`. Jednak dzięki temu cały postęp w aplikacji zostanie utracony.

Poniższy fragment kodu przedstawia sposób zmiany konfiguracji aplikacji działającej w Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Parametry konfiguracji muszą zostać przesłane jako ciąg JSON i muszą znajdować się w następnym wierszu po Magic, jak pokazano w przykładowej kolumnie.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Zmiana parametrów dla aplikacji przesłanej przy użyciu platformy Spark — przesyłanie
Poniższe polecenie stanowi przykład zmiany parametrów konfiguracji dla aplikacji usługi Batch przesłanej przy użyciu `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Zmiana parametrów dla aplikacji przesłanej przy użyciu narzędzia zwinięcie
Poniższe polecenie stanowi przykład zmiany parametrów konfiguracji dla aplikacji usługi Batch przesłanej przy użyciu zwinięcia.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Zmień te parametry na serwerze Spark Thrift
Serwer Spark Thrift zapewnia dostęp JDBC/ODBC do klastra Spark i służy do obsługi zapytań Spark SQL. Narzędzia, takie jak Power BI, Tableau itp. Użyj protokołu ODBC, aby komunikować się z serwerem Spark Thrift w celu wykonywania zapytań Spark SQL jako aplikacji platformy Spark. Po utworzeniu klastra Spark dwa wystąpienia serwera Spark Thrift są uruchamiane, jeden w każdym węźle głównym. Każdy serwer Spark Thrift jest widoczny jako aplikacja Spark w interfejsie użytkownika z PRZĘDZą.

Serwer Spark Thrift korzysta z dynamicznej alokacji modułu wykonawczego platformy Spark, dlatego `spark.executor.instances` nie jest używany. Zamiast tego serwer Spark Thrift używa `spark.dynamicAllocation.minExecutors` i `spark.dynamicAllocation.maxExecutors` do określenia liczby programów wykonujących. Parametry konfiguracji `spark.executor.cores` i `spark.executor.memory` są używane do modyfikowania rozmiaru programu wykonującego. Te parametry można zmienić, jak pokazano w następujących krokach:

* Rozwiń kategorię **Advanced Spark-Thrift-sparkconf** , aby zaktualizować parametry `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors` i `spark.executor.memory`.

    ![Konfigurowanie serwera Spark Thrift](./media/apache-spark-resource-manager/spark-thrift-server-1.png "Konfigurowanie serwera Spark Thrift")
* Rozwiń **niestandardową kategorię Spark-Thrift-sparkconf** , aby zaktualizować parametr `spark.executor.cores`.

    ![Konfigurowanie parametru serwera Spark Thrift](./media/apache-spark-resource-manager/spark-thrift-server-2.png "Konfigurowanie parametru serwera Spark Thrift")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Zmiana pamięci sterownika serwera Spark Thrift
Pamięć sterownika serwera Spark Thrift jest skonfigurowana do 25% rozmiaru pamięci RAM węzła głównego, pod warunkiem, że całkowity rozmiar pamięci RAM węzła głównego jest większy niż 14 GB. Aby zmienić konfigurację pamięci sterownika, można użyć interfejsu użytkownika Ambari, jak pokazano na poniższym zrzucie ekranu:

* Z interfejsu użytkownika Ambari kliknij pozycję **Spark**, kliknij pozycję **konfiguracje, rozwiń**węzeł **Advanced Spark-ENV**, a następnie podaj wartość parametru **spark_thrift_cmd_opts**.

    ![Konfigurowanie pamięci RAM serwera Spark Thrift](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Odzyskiwanie zasobów klastra Spark
Ze względu na dynamiczną alokację Spark jedynymi zasobami, które są używane przez serwer Thrift, są zasoby dla dwóch wzorców aplikacji. Aby odzyskiwać te zasoby, należy zatrzymać usługi serwera Thrift uruchomione w klastrze.

1. Z poziomu interfejsu użytkownika Ambari w lewym okienku kliknij pozycję **Spark**.
2. Na następnej stronie kliknij pozycję **serwery Spark Thrift**.

    ![Uruchom ponownie Thrift serwer1](./media/apache-spark-resource-manager/restart-thrift-server-1.png "Uruchom ponownie Thrift serwer1")
3. Należy zobaczyć dwie węzłów głównych, na których jest uruchomiony serwer Spark Thrift. Kliknij jeden z węzłów głównych.

    ![Ponowne uruchamianie Thrift Serwer2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Ponowne uruchamianie Thrift Serwer2")
4. Na następnej stronie znajduje się lista wszystkich usług uruchomionych w ramach tego węzła głównego. Z listy kliknij przycisk listy rozwijanej obok pozycji Spark Thrift Server, a następnie kliknij przycisk **Zatrzymaj**.

    ![Uruchom ponownie Thrift serwer3](./media/apache-spark-resource-manager/restart-thrift-server-3.png "Uruchom ponownie Thrift serwer3")
5. Powtórz te czynności również na innych węzła głównegoach.

## <a name="restart-the-jupyter-service"></a>Uruchom ponownie usługę Jupyter
Uruchom interfejs użytkownika sieci Web Ambari, jak pokazano na początku artykułu. W okienku nawigacji po lewej stronie kliknij pozycję **Jupyter**, kliknij pozycję **Akcje usługi**, a następnie kliknij pozycję **Uruchom ponownie wszystkie**. Spowoduje to uruchomienie usługi Jupyter na wszystkich węzłów głównychach.

![Uruchom ponownie Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Uruchom ponownie program Jupyter")

## <a name="monitor-resources"></a>Monitorowanie zasobów
Uruchom interfejs użytkownika przędzy, jak pokazano na początku artykułu. W tabeli metryk klastra w górnej części ekranu Sprawdź wartości **używanej pamięci** i kolumny **łącznie z pamięcią** . Jeśli te dwie wartości są zamknięte, może nie być wystarczającej ilości zasobów do uruchomienia następnej aplikacji. To samo dotyczy **rdzeni wirtualnych używanych** i **rdzeni wirtualnych sum** kolumn. Ponadto w widoku głównym, jeśli istnieje aplikacja, która została **zaakceptowana** , a nie przeszedł w stan **uruchomienia** lub **niepowodzenia** , może to oznaczać, że nie jest dostępna wystarczająca ilość zasobów do uruchomienia.

![Limit zasobów](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Limit zasobów")

## <a name="kill-running-applications"></a>Kasuj uruchomione aplikacje
1. W interfejsie użytkownika przędzenia w lewym panelu kliknij pozycję **działa**. Z listy uruchomionych aplikacji Ustal, czy aplikacja ma zostać zainstalowana, a następnie kliknij **Identyfikator**.

    ![Kasuj APP1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Kasuj APP1")

2. Kliknij pozycję **Kasuj aplikację** w prawym górnym rogu, a następnie kliknij przycisk **OK**.

    ![Kasuj APP2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Kasuj APP2")

## <a name="see-also"></a>Zobacz także
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Dla analityków danych

* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analiza danych telemetrycznych usługi Application Insight przy użyciu Apache Spark w usłudze HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Użyj Caffe na Azure HDInsight Spark na potrzeby rozproszonej uczenia głębokiego](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>Dla deweloperów Apache Spark

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki narzędzi HDInsight do IntelliJ pomysł, aby debugować aplikacje Apache Spark zdalnie](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)
