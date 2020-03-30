---
title: Zarządzanie zasobami klastra Platformy Spark apache w usłudze Azure HDInsight
description: Dowiedz się, jak zarządzać zasobami klastrów platformy Spark w usłudze Azure HDInsight, aby uzyskać lepszą wydajność.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 3aab89f86dcd48328771cd0fda03d1c9de4bc2c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932105"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Zarządzanie zasobami klastra Platformy Spark apache w usłudze Azure HDInsight

Dowiedz się, jak uzyskać dostęp do interfejsów, takich jak [Apache Ambari](https://ambari.apache.org/) UI, [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI i [Spark History Server](./apache-azure-spark-history-server.md) skojarzone z klastrem [Apache Spark](https://spark.apache.org/) i jak dostroić konfigurację klastra w celu uzyskania optymalnej wydajności.

## <a name="open-the-spark-history-server"></a>Otwórz serwer historii platformy Spark

Spark History Server to internetowy interfejs użytkownika dla ukończonych i uruchomionych aplikacji Platformy Spark. Jest to rozszerzenie interfejsu użytkownika sieci Web platformy Spark. Aby uzyskać pełne informacje, zobacz [Spark History Server](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Otwieranie interfejsu użytkownika przędzy

Interfejs użytkownika YARN służy do monitorowania aplikacji, które są aktualnie uruchomione w klastrze Platformy Spark.

1. Z [witryny Azure portal](https://portal.azure.com/)otwórz klaster platformy Spark. Aby uzyskać więcej informacji, zobacz [Lista i pokaż klastry](../hdinsight-administer-use-portal-linux.md#showClusters).

2. Z **pulpitów nawigacyjnych klastra**wybierz pozycję **Przędza**. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra platformy Spark.

    ![Uruchom interfejs użytkownika przędzy](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Alternatywnie, można również uruchomić YARN UI z interfejsu użytkownika Ambari. W interfejsie użytkownika Ambari przejdź do interfejsu użytkownika **YARN** > **Quick Links** > **Active** > Resource**Manager**.

## <a name="optimize-clusters-for-spark-applications"></a>Optymalizacja klastrów pod kątem aplikacji Spark

Trzy kluczowe parametry, które mogą być używane do `spark.executor.instances`konfiguracji `spark.executor.cores`platformy `spark.executor.memory`Spark w zależności od wymagań aplikacji to , i . Executor to proces uruchomiony dla aplikacji Spark. Działa w węźle procesu roboczego i jest odpowiedzialny za wykonywanie zadań dla aplikacji. Domyślna liczba wykonawców i rozmiary executor dla każdego klastra jest obliczana na podstawie liczby węzłów procesu roboczego i rozmiaru węzła procesu roboczego. Te informacje są `spark-defaults.conf` przechowywane w węzłach głównego klastra.

Trzy parametry konfiguracji można skonfigurować na poziomie klastra (dla wszystkich aplikacji, które działają w klastrze) lub można określić dla każdej aplikacji, jak również.

### <a name="change-the-parameters-using-ambari-ui"></a>Zmienianie parametrów przy użyciu interfejsu użytkownika Ambari

1. Z interfejsu użytkownika Ambari przejdź do **Spark2** > **Configs Niestandardowe** > **spark2-defaults**.

    ![Ustawianie parametrów przy użyciu niestandardowych parametrów Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Ustawianie parametrów przy użyciu niestandardowych parametrów Ambari")

1. Wartości domyślne są dobre, aby cztery aplikacje Platformy Spark były uruchamiane jednocześnie w klastrze. Wartości te można zmienić w interfejsie użytkownika, jak pokazano na poniższym zrzucie ekranu:

    ![Ustawianie parametrów za pomocą ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Ustawianie parametrów za pomocą ambari")

1. Wybierz **pozycję Zapisz,** aby zapisać zmiany konfiguracji. W górnej części strony zostanie wyświetlony monit o ponowne uruchomienie wszystkich usług, których dotyczy problem. Wybierz pozycję **Uruchom ponownie**.

    ![Ponowne uruchomienie usług](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Zmienianie parametrów aplikacji uruchomionej w notesie Jupyter

W przypadku aplikacji działających w notesie Jupyter można użyć `%%configure` magii, aby wprowadzić zmiany konfiguracji. W idealnym przypadku należy wprowadzić takie zmiany na początku aplikacji, przed uruchomieniem pierwszej komórki kodu. W ten sposób gwarantuje, że konfiguracja jest stosowana do sesji Livy, gdy zostanie utworzona. Jeśli chcesz zmienić konfigurację na późniejszym etapie w aplikacji, należy użyć parametru. `-f` Jednak w ten sposób wszystkie postępy w aplikacji jest tracona.

Poniższy fragment kodu pokazuje, jak zmienić konfigurację aplikacji uruchomionej w jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Parametry konfiguracji muszą być przekazywane jako ciąg JSON i musi znajdować się w następnym wierszu po magii, jak pokazano w przykładowej kolumnie.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Zmienianie parametrów aplikacji przesłanej za pomocą spark-submit

Następujące polecenie jest przykładem jak zmienić parametry konfiguracji dla aplikacji `spark-submit`wsadowej, która jest przesyłana za pomocą .

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Zmienianie parametrów aplikacji przesłanej za pomocą cURL

Następujące polecenie jest przykładem jak zmienić parametry konfiguracji dla aplikacji wsadowej, która jest przesyłana przy użyciu cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Zmienianie tych parametrów na serwerze Spark Thrift

Spark Thrift Server zapewnia dostęp JDBC/ODBC do klastra Platformy Spark i jest używany do obsługi zapytań spark SQL. Narzędzia takie jak Power BI, Tableau itd. Po utworzeniu klastra platformy Spark uruchamiane są dwa wystąpienia serwera spark Thrift, po jednym w każdym węźle głównym. Każdy serwer Spark Thrift jest widoczny jako aplikacja Spark w interfejsie użytkownika YARN.

Spark Thrift Server używa alokacji dynamicznego `spark.executor.instances` executora platformy Spark i dlatego nie jest używany. Zamiast tego spark Thrift `spark.dynamicAllocation.maxExecutors` Server `spark.dynamicAllocation.minExecutors` używa i określić liczbę executor. Parametry `spark.executor.cores`konfiguracji i `spark.executor.memory` są używane do modyfikowania rozmiaru wykonawcy. Można zmienić te parametry, jak pokazano w następujących krokach:

* Rozwiń kategorię **Zaawansowane iskry2-thrift-sparkconf,** aby zaktualizować parametry `spark.dynamicAllocation.maxExecutors`, i `spark.dynamicAllocation.minExecutors`.

    ![Konfigurowanie serwera oszczędności spark](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Konfigurowanie serwera oszczędności spark")

* Rozwiń **kategorię Niestandardowa iskra2-thrift-sparkconf,** aby zaktualizować parametry `spark.executor.cores`, i `spark.executor.memory`.

    ![Konfigurowanie parametru serwera oszczędności platformy Spark](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Konfigurowanie parametru serwera oszczędności platformy Spark")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Zmienianie pamięci sterowników serwera Spark Thrift Server

Pamięć sterownika programu Spark Thrift Server jest skonfigurowana do 25% rozmiaru pamięci RAM węzła głównego, pod warunkiem że całkowity rozmiar pamięci RAM węzła głównego jest większy niż 14 GB. Za pomocą interfejsu użytkownika Ambari można zmienić konfigurację pamięci sterownika, jak pokazano na poniższym zrzucie ekranu:

Z interfejsu użytkownika Ambari przejdź do **spark2** > **Configs** > **Advanced spark2-env**. Następnie podaj wartość **spark_thrift_cmd_opts**.

## <a name="reclaim-spark-cluster-resources"></a>Odzyskiwanie zasobów klastra Spark

Ze względu na alokację dynamiczną platformy Spark jedynymi zasobami, które są używane przez serwer oszczędności, są zasoby dla dwóch wzorców aplikacji. Aby odzyskać te zasoby, należy zatrzymać usługi thrift Server uruchomione w klastrze.

1. Z interfejsu użytkownika Ambari z lewego okienka wybierz **spark2**.

2. Na następnej stronie wybierz **spark2 Thrift Servers**.

    ![Uruchom ponownie serwer oszczędności1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Uruchom ponownie serwer oszczędności1")

3. Powinny być widoczne dwa headnodes, na którym spark2 Thrift Server jest uruchomiony. Wybierz jeden z głów.

    ![Ponowne uruchamianie serwera oszczędności2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Ponowne uruchamianie serwera oszczędności2")

4. Następna strona zawiera listę wszystkich usług uruchomionych na tym headnode. Z listy wybierz przycisk rozwijany obok pozycji Spark2 Thrift Server, a następnie wybierz pozycję **Zatrzymaj**.

    ![Ponowne uruchamianie serwera oszczędności3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Ponowne uruchamianie serwera oszczędności3")
5. Powtórz te kroki również na drugim końcu głowy.

## <a name="restart-the-jupyter-service"></a>Ponowne uruchamianie usługi Jupyter

Uruchom interfejs użytkownika sieci Web Ambari, jak pokazano na początku artykułu. W lewym okienku nawigacji wybierz pozycję **Jupyter**, wybierz pozycję **Akcje usługi**, a następnie wybierz pozycję Uruchom **ponownie wszystkie**. To uruchamia usługę Jupyter na wszystkich headnodes.

![Uruchom ponownie program Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Uruchom ponownie program Jupyter")

## <a name="monitor-resources"></a>Monitorowanie zasobów

Uruchom interfejs użytkownika przędzy, jak pokazano na początku artykułu. W tabeli Metryki klastra u góry ekranu sprawdź wartości kolumn **Używane i** **Całkowita pamięć.** Jeśli dwie wartości są blisko, może nie być wystarczającej ilości zasobów, aby uruchomić następną aplikację. To samo dotyczy kolumn **Używane i** **VCores Total.** Ponadto w widoku głównym, jeśli istnieje aplikacja pozostała w stanie **ACCEPTED** i nie przejście do **bieżącego** ani **nie powiodło się,** może to być również wskazanie, że nie jest uzyskanie wystarczającej ilości zasobów, aby rozpocząć.

![Limit zasobów](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Limit zasobów")

## <a name="kill-running-applications"></a>Zabij uruchomione aplikacje

1. W interfejsie użytkownika przędzy z lewego panelu wybierz pozycję **Uruchomiona**. Z listy uruchomionych aplikacji określ aplikację, która ma zostać zabita i wybierz **identyfikator**.

    ![Zabić App1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Zabić App1")

2. Wybierz **pozycję Zabij aplikację** w prawym górnym rogu, a następnie wybierz przycisk **OK**.

    ![Zabić App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Zabić App2")

## <a name="see-also"></a>Zobacz też

* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Dla analityków danych

* [Apache Spark z uczeniem maszynowym: użyj platformy Spark w hdinsight do analizowania temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika strony internetowej przy użyciu platformy Apache Spark w hdinsight](apache-spark-custom-library-website-log-analysis.md)
* [Analiza danych telemetrycznych usługi Application Insight przy użyciu platformy Spark apache w udziale hdinsight](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Dla programistów Apache Spark

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki HDInsight Tools dla IntelliJ IDEA do zdalnego debugowania aplikacji Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Używanie notebooków Apache Zeppelin z klastrem Apache Spark w programie HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notebooka Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)
