---
title: Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight
description: Dowiedz się, jak używać zarządzanie zasobami klastry Spark w usłudze Azure HDInsight w celu zapewnienia lepszej wydajności.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: hrasheed
ms.openlocfilehash: dfd926f3e091f4516685fd73f3a6b87949330afb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706353"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight 

Dowiedz się, jak dostęp do interfejsów, takich jak [Apache Ambari](https://ambari.apache.org/) interfejsu użytkownika, [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) interfejsu użytkownika, a serwer historii platformy Spark, które są skojarzone z Twojej [platformy Apache Spark](https://spark.apache.org/) klastra oraz sposób Dostosowywanie konfiguracji klastra w celu uzyskania optymalnej wydajności.

**Wymagania wstępne:**

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Otwórz interfejs użytkownika sieci Web systemu Ambari

Apache Ambari służy do monitorowania klastra i wprowadzania zmian w konfiguracji. Aby uzyskać więcej informacji, zobacz [Zarządzanie Apache Hadoop clusters w HDInsight przy użyciu witryny Azure portal](../hdinsight-administer-use-portal-linux.md#open-the-apache-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>Otwórz serwer historii platformy Spark

Serwer historii platformy Spark jest interfejs użytkownika sieci web dla aplikacji platformy Spark ukończone i uruchomione. Jest rozszerzeniem interfejsu użytkownika sieci Web platforma Spark.

**Aby otworzyć Interfejs sieci Web uzyskiwania informacji na temat serwer historii platformy Spark**

1. Z [witryny Azure portal](https://portal.azure.com/), otwórz klaster Spark. Aby uzyskać więcej informacji, zobacz [listy i wyświetlaniu klastrów](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Z **szybkich łączy**, kliknij przycisk **pulpit nawigacyjny klastra**, a następnie kliknij przycisk **serwer historii platformy Spark**

    ![Platforma Spark jest serwer historii](./media/apache-spark-resource-manager/launch-history-server.png "Spark serwer historii")

    Po wyświetleniu monitu wprowadź poświadczenia administratora klastra Spark. Serwer historii platformy Spark można również otworzyć, przechodząc do następującego adresu URL:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Zastąp `<ClusterName>` nazwą klastra Spark.

Serwer historii platformy Spark w sieci web UI wygląda następująco:

![Serwer historii platformy HDInsight Spark](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Otwórz interfejs użytkownika usługi Yarn
Interfejsie użytkownika YARN służy do monitorowania aplikacji, które są aktualnie uruchomione w klastrze Spark.

1. Z [witryny Azure portal](https://portal.azure.com/), otwórz klaster Spark. Aby uzyskać więcej informacji, zobacz [listy i wyświetlaniu klastrów](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Z **szybkich łączy**, kliknij przycisk **pulpit nawigacyjny klastra**, a następnie kliknij przycisk **YARN**.

    ![Uruchom interfejs użytkownika usługi YARN](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]  
   > Alternatywnie można również uruchomić Interfejsie użytkownika YARN z poziomu interfejsu użytkownika Ambari. Aby uruchomić interfejs użytkownika systemu Ambari, kliknij przycisk **pulpit nawigacyjny klastra**, a następnie kliknij przycisk **pulpit nawigacyjny klastra HDInsight**. W Interfejsie użytkownika Ambari, kliknij **YARN**, kliknij przycisk **szybkich łączy**, kliknij active usługi Resource Manager, a następnie kliknij przycisk **interfejsu użytkownika Menedżera zasobów**.

## <a name="optimize-clusters-for-spark-applications"></a>Optymalizowanie klastrów dla aplikacji platformy Spark
Są trzy kluczowe parametry, które mogą służyć do konfiguracji platformy Spark w zależności od wymagań aplikacji `spark.executor.instances`, `spark.executor.cores`, i `spark.executor.memory`. Program wykonujący to proces uruchamiany dla aplikacji platformy Spark. Ona działa w węźle procesu roboczego i odpowiada do wykonywania zadań w aplikacji. Domyślna liczba executors i rozmiary przetwarzania dla każdego klastra jest obliczany na podstawie liczby węzłów procesu roboczego i rozmiar węzła procesu roboczego. Te informacje są przechowywane w `spark-defaults.conf` na głównymi węzłami klastra.

Parametry konfiguracji trzy można skonfigurować na poziomie klastra (dla wszystkich aplikacji, które działają w klastrze) lub można określić dla każdego poszczególnych aplikacji.

### <a name="change-the-parameters-using-ambari-ui"></a>Zmień parametry za pomocą interfejsu użytkownika systemu Ambari
1. Kliknij przycisk interfejsu użytkownika Ambari **Spark**, kliknij przycisk **Configs**, a następnie rozwiń węzeł **niestandardowe platformy spark — domyślne**.

    ![Ustaw parametry przy użyciu narzędzia Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Wartości domyślne są dobrze cztery aplikacji Spark jednocześnie uruchomione w klastrze. Te wartości można zmienić z poziomu interfejsu użytkownika, jak pokazano na poniższym zrzucie ekranu:

    ![Ustaw parametry przy użyciu narzędzia Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Kliknij przycisk **Zapisz** można zapisać zmian konfiguracji. W górnej części strony wyświetlany jest monit o ponowne uruchomienie wszystkich odpowiednich usług. Kliknij przycisk **Uruchom ponownie**.

    ![Uruchom ponownie usługi](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Zmień parametry dla aplikacji uruchomionej w aplikacji Jupyter notebook
Aplikacje działające na platformie notesu programu Jupyter, można użyć `%%configure` magic Aby wprowadzić zmiany w konfiguracji. W idealnym przypadku należy wprowadzić takie zmiany na początku aplikacji, przed uruchomieniem swojej pierwszej komórki kodu. W ten sposób zapewnia, że konfiguracja jest stosowana do sesji usługi Livy, gdy zostanie utworzona. Jeśli chcesz zmienić konfigurację na późniejszym etapie w aplikacji, należy użyć `-f` parametru. Jednak w ten sposób wszystkie postęp w aplikacji zostaną utracone.

Poniższy fragment kodu pokazuje, jak zmienić konfigurację dla aplikacji działającej w programie Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Parametry konfiguracji muszą być przekazywane w postaci ciągu JSON i musi być w następnym wierszu po magic, jak pokazano w przykładzie kolumna.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Zmiana parametrów dla aplikacji przesłane za pomocą skryptu spark-submit
Następujące polecenie znajduje się przykład jak zmienić parametry konfiguracji aplikacji usługi batch, który jest przesyłany za pomocą `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Zmień parametry dla aplikacji przesłane za pomocą programu cURL
Następujące polecenie jest przykładem zmienić parametry konfiguracji aplikacji usługi batch, który jest przesyłany przy użyciu programu cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Zmiany tych parametrów w Spark Thrift Server
Spark Thrift Server zapewnia JDBC/ODBC dostęp do klastra Spark i jest używana do zapytań Spark SQL usługi. Narzędzia takie jak Power BI, Tableau itp. Użyj protokołu ODBC nawiązać połączenia z usługą Spark Thrift Server do wykonywania zapytań Spark SQL jako aplikację aparatu Spark. Podczas tworzenia klastra platformy Spark, Spark Thrift Server dwa wystąpienia są uruchomione, jeden na każdy węzeł główny. Każdy Spark Thrift Server jest widoczny jako aplikację aparatu Spark w interfejsie użytkownika YARN.

Spark Thrift Server korzysta z platformy Spark wykonawca dynamicznej alokacji i dlatego `spark.executor.instances` nie jest używany. Zamiast tego używa Spark Thrift Server `spark.dynamicAllocation.minExecutors` i `spark.dynamicAllocation.maxExecutors` można określić liczbę funkcji wykonawczej. Parametry konfiguracji `spark.executor.cores` i `spark.executor.memory` służy do modyfikowania rozmiaru wykonawcy. Te parametry można zmienić, jak pokazano w poniższych krokach:

* Rozwiń **zaawansowane spark-thrift-sparkconf** kategorię, aby zaktualizować parametry `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`, i `spark.executor.memory`.

    ![Konfigurowanie platformy Spark thrift server](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Rozwiń **niestandardowe spark-thrift-sparkconf** kategorię, aby zaktualizować parametru `spark.executor.cores`.

    ![Konfigurowanie platformy Spark thrift server](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Zmień pamięć sterownik Spark Thrift Server
Pamięć sterownik Spark Thrift Server jest skonfigurowany do 25% pamięci RAM rozmiar węzła głównego, pod warunkiem, że całkowity rozmiar pamięci RAM węzła głównego jest większy niż 14 GB. Do zmiany konfiguracji pamięci sterownika, można użyć interfejsu użytkownika Ambari, jak pokazano na poniższym zrzucie ekranu:

* Kliknij przycisk interfejsu użytkownika Ambari **Spark**, kliknij przycisk **konfiguracje**, rozwiń węzeł **zaawansowane środowisko spark**, a następnie podaj wartość dla **spark_thrift_cmd_opts**.

    ![Konfigurowanie platformy Spark thrift server pamięci RAM](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Odzyskiwanie z zasobów klastra Spark
Ze względu na Spark dynamicznej alokacji jedyne zasoby, które są używane przez serwer thrift są zasoby dla wzorców dwóch aplikacji. Aby odzyskać te zasoby, należy zatrzymać usługi Thrift Server uruchomiony w klastrze.

1. W Interfejsie użytkownika Ambari, w lewym okienku kliknij **Spark**.
2. Na następnej stronie kliknij pozycję **Spark Thrift serwerów**.

    ![Uruchom ponownie serwer thrift](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Powinien zostać wyświetlony dwóch węzłów głównych, na których uruchomiono Spark Thrift Server. Kliknij jeden z węzłów głównych.

    ![Uruchom ponownie serwer thrift](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. Następnej strony Wyświetla listę wszystkich usług działających na tym węzeł główny. Z listy kliknij przycisk listy rozwijanej obok Spark Thrift Server, a następnie kliknij przycisk **zatrzymać**.

    ![Uruchom ponownie serwer thrift](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Powtórz te kroki dla innych węzła głównego także.

## <a name="restart-the-jupyter-service"></a>Uruchom ponownie usługę programu Jupyter
Uruchom interfejs użytkownika sieci Web Ambari, jak pokazano na początku tego artykułu. W okienku nawigacji po lewej stronie kliknij **Jupyter**, kliknij przycisk **działania usługi**, a następnie kliknij przycisk **ponowne uruchomienie wszystkich**. Spowoduje to uruchomienie usługi programu Jupyter na wszystkich węzłów głównych.

![Ponowne uruchomienie programu Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "ponowne uruchomienie programu Jupyter")

## <a name="monitor-resources"></a>Monitorowanie zasobów
Uruchom interfejs użytkownika usługi Yarn, jak pokazano na początku tego artykułu. W tabeli metryk klastra na górze ekranu, sprawdź wartości **pamięć używana** i **całkowita pamięć** kolumn. Jeśli dwie wartości są bliskie, nie może być za mało zasobów, aby uruchomić aplikację dalej. To samo dotyczy **liczby używanych rdzeni wirtualnych** i **łącznej liczby rdzeni wirtualnych** kolumn. Również w widoku głównego, w przypadku aplikacji zostali w **ZAAKCEPTOWANO** stan i nie są przenoszone do **systemem** ani **nie powiodło się** stanu, przyczyną może być również wskazanie, nie jest to pobieranie wystarczającej liczby zasobów, aby uruchomić.

![Limit zasobów](./media/apache-spark-resource-manager/resource-limit.png "Limit zasobów")

## <a name="kill-running-applications"></a>Zakończ uruchomione aplikacje
1. W interfejsie użytkownika Yarn z panelu po lewej stronie kliknij pozycję **systemem**. Z listy uruchomionych aplikacji, należy określić aplikację skasowane i kliknąć **identyfikator**.

    ![Kill App1](./media/apache-spark-resource-manager/kill-app1.png "Kill komputera App1")

2. Kliknij przycisk **Kill aplikacji** w prawym górnym rogu, następnie kliknij polecenie **OK**.

    ![Kill App2](./media/apache-spark-resource-manager/kill-app2.png "Kill App2")

## <a name="see-also"></a>Zobacz także
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Dla analityków danych

* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika witryny sieci Web przy użyciu platformy Apache Spark w HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Dane telemetryczne analiza danych Application Insight przy użyciu platformy Apache Spark w HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Używanie platformy Caffe na platformie Azure HDInsight Spark na potrzeby rozproszonej uczenia głębokiego](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>Dla deweloperów platformy Apache Spark

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji platformy Apache Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)
