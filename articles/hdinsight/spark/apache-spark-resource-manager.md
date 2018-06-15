---
title: Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać zarządzanie zasobami klastry Spark w usłudze Azure HDInsight w celu zapewnienia lepszej wydajności.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: jgao
ms.openlocfilehash: afefec77d9d79ad97fa351bc8b0710894c242d76
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31520512"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight 

Dowiedz się, jak uzyskać dostępu do interfejsów, takich jak Ambari interfejsu użytkownika, interfejsie użytkownika YARN i serwer historii Spark skojarzony z klastrem Spark i dostrajania konfiguracji klastra, aby zapewnić optymalną wydajność.

**Wymagania wstępne:**

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [klastrów utworzyć Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Otwórz interfejs użytkownika sieci Web Ambari

Apache Ambari służy do monitorowania klastra i wprowadzania zmian w konfiguracji. Aby uzyskać więcej informacji, zobacz [klastrów zarządzania Hadoop w usłudze HDInsight przy użyciu portalu Azure](../hdinsight-administer-use-portal-linux.md#open-the-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>Otwórz okno Spark historii serwera

Serwer historii Spark jest interfejs użytkownika sieci web została zakończona i uruchomiona aplikacji Spark. To rozszerzenie interfejsu użytkownika sieci Web platforma Spark.

**Aby otworzyć Interfejs sieci Web serwera Spark historii**

1. Z [portalu Azure](https://portal.azure.com/), otwórz klaster Spark. Aby uzyskać więcej informacji, zobacz [klastrów listy i Pokaż](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. Z **szybkie linki**, kliknij przycisk **pulpit nawigacyjny klastra**, a następnie kliknij przycisk **Spark historii serwera**

    ![Platforma Spark jest serwer historii](./media/apache-spark-resource-manager/launch-history-server.png "Spark historii serwera")

    Po wyświetleniu monitu wprowadź poświadczenia administratora klastra Spark. Można również otworzyć serwera historii Spark, przechodząc pod następujący adres URL:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Zastąp <ClusterName> nazwą klastra Spark.

Platforma Spark historii serwera sieci web wygląda interfejs użytkownika:

![Serwer historii Spark w usłudze HDInsight](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Otwórz Yarn interfejsu użytkownika
Interfejs użytkownika YARN służy do monitorowania aplikacji, które są aktualnie uruchomione w klastrze Spark.

1. Z [portalu Azure](https://portal.azure.com/), otwórz klaster Spark. Aby uzyskać więcej informacji, zobacz [klastrów listy i Pokaż](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. Z **szybkie linki**, kliknij przycisk **pulpit nawigacyjny klastra**, a następnie kliknij przycisk **YARN**.

    ![Uruchom interfejs użytkownika YARN](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > Alternatywnie można również uruchomić interfejsie użytkownika YARN z poziomu interfejsu użytkownika narzędzia Ambari. Aby uruchomić interfejs użytkownika narzędzia Ambari, kliknij przycisk **pulpit nawigacyjny klastra**, a następnie kliknij przycisk **pulpit nawigacyjny klastra usługi HDInsight**. W interfejsie użytkownika narzędzia Ambari, kliknij przycisk **YARN**, kliknij przycisk **szybkie linki**, kliknij active Resource Manager, a następnie kliknij przycisk **interfejsu użytkownika Menedżera zasobów**.
   >
   >

## <a name="optimize-clusters-for-spark-applications"></a>Optymalizacja klastry Spark aplikacji
Są trzy parametry kluczy, które mogą służyć do konfiguracji platformy Spark w zależności od wymagań aplikacji `spark.executor.instances`, `spark.executor.cores`, i `spark.executor.memory`. Moduł wykonujący jest uruchomiona aplikacji Spark. Działa w węźle procesu roboczego, a odpowiada do wykonywania zadań dla aplikacji. Domyślna liczba modułów i rozmiary Moduł wykonujący dla każdego klastra jest obliczany na podstawie liczby węzłów procesu roboczego i rozmiaru węzła procesu roboczego. Te informacje są przechowywane w `spark-defaults.conf` na głównymi węzłami klastra.

Parametry trzech konfiguracji można skonfigurować na poziomie klastra (dla wszystkich aplikacji, które są uruchamiane w klastrze) lub można określić dla każdej poszczególnych aplikacji.

### <a name="change-the-parameters-using-ambari-ui"></a>Zmień parametry za pomocą interfejsu użytkownika narzędzia Ambari
1. W interfejsie użytkownika narzędzia Ambari kliknij **Spark**, kliknij przycisk **Configs**, a następnie rozwiń węzeł **niestandardowe spark — domyślne**.

    ![Ustawianie parametrów przy użyciu narzędzia Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Wartości domyślne są dobrym ma cztery aplikacji Spark jednocześnie uruchomione w klastrze. Te wartości można zmienić przy użyciu interfejsu użytkownika, jak pokazano na poniższym zrzucie ekranu:

    ![Ustawianie parametrów przy użyciu narzędzia Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Kliknij przycisk **zapisać** można zapisać zmian konfiguracji. W górnej części strony wyświetlany jest monit o ponowne uruchomienie wszystkich odpowiednich usług. Kliknij przycisk **ponownego uruchomienia**.

    ![Uruchom ponownie usługi](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Zmień parametry dla aplikacji działających w notesu Jupyter
W przypadku aplikacji uruchomionych w notesu Jupyter, można użyć `%%configure` magic zmian konfiguracji. W idealnym przypadku należy takie zmiany na początku aplikacji, przed uruchomieniem pierwszej komórki kodu. W ten sposób zapewnia, że konfiguracja zostanie zastosowana do sesji programu Livy, gdy zostanie utworzony. Jeśli chcesz zmienić konfigurację na późniejszym etapie w aplikacji, należy użyć `-f` parametru. Jednak w ten sposób wszystkie postęp w aplikacji zostaną utracone.

Poniższy fragment kodu przedstawia sposób zmiany konfiguracji dla aplikacji działających w oprogramowaniu Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Parametry konfiguracji muszą być przekazywane w postaci ciągu JSON i musi być w następnym wierszu po magic, jak pokazano w przykładzie kolumny.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Zmiana, którą przesłać spark parametry przesłane za pomocą aplikacji
Następujące polecenia są przykładem zmienić parametry konfiguracji, dla której zostało przesłane za pomocą aplikacji partii `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Zmień parametry dla aplikacji przesłane przy użyciu programu cURL
Polecenie jest przykładem zmienić parametry konfiguracji, dla której zostało przesłane przy użyciu programu cURL aplikacji partii.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Zmiany tych parametrów, na serwerze Spark Thrift
Spark Thrift Server JDBC/ODBC udostępnia klastra Spark i służy do zapytań Spark SQL usługi. Narzędzia takie jak usługi Power BI, Tableau itp. Używanie protokołu ODBC do komunikowania się z serwerem Thrift Spark do wykonywania zapytań Spark SQL jako aplikacji Spark. Po utworzeniu klastra Spark są uruchamiane dwa wystąpienia serwera Spark Thrift, jeden w każdym węźle głównym. Każdy serwer Thrift Spark jest widoczny jako aplikacji Spark w Interfejsie użytkownika YARN.

Platforma Spark Thrift serwer używa Spark Moduł wykonujący dynamicznej alokacji i dlatego `spark.executor.instances` nie jest używany. Zamiast tego Spark Thrift serwer używa `spark.dynamicAllocation.minExecutors` i `spark.dynamicAllocation.maxExecutors` można określić liczbę Moduł wykonujący. Parametry konfiguracji `spark.executor.cores` i `spark.executor.memory` służy do zmiany rozmiaru Moduł wykonujący. Te parametry można zmienić, jak pokazano w poniższych krokach:

* Rozwiń węzeł **zaawansowane spark-thrift-sparkconf** kategorię, aby zaktualizować parametry `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`, i `spark.executor.memory`.

    ![Konfigurowanie Spark thrift serwera](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Rozwiń węzeł **niestandardowe spark-thrift-sparkconf** kategorię, aby zaktualizować parametr `spark.executor.cores`.

    ![Konfigurowanie Spark thrift serwera](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Zmień rozmiar pamięci sterownika serwera Spark Thrift
Pamięć sterownik Spark Thrift serwera jest skonfigurowany do 25% rozmiar pamięci RAM węzła głównego, pod warunkiem że całkowity rozmiar pamięci RAM węzła głównego jest większa niż 14 GB. Interfejs użytkownika narzędzia Ambari służy do zmiany konfiguracji pamięci sterownika, jak pokazano na poniższym zrzucie ekranu:

* W interfejsie użytkownika narzędzia Ambari kliknij **Spark**, kliknij przycisk **Configs**, rozwiń węzeł **zaawansowane spark env**, a następnie podaj wartość dla **spark_thrift_cmd_opts**.

    ![Konfigurowanie Spark thrift serwera pamięci RAM](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Odzyskać zasoby klastra Spark
Z powodu Spark dynamiczna alokacja tylko zasoby, które są używane przez serwer thrift są zasoby wzorców dwóch aplikacji. Aby odzyskać tych zasobów, musisz zatrzymać usługi serwera Thrift działające w klastrze.

1. W interfejsie użytkownika narzędzia Ambari, w lewym okienku kliknij **Spark**.
2. Na następnej stronie kliknij **Spark Thrift serwerów**.

    ![Uruchom ponownie serwer thrift](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Powinny pojawić się dwa headnodes, na których działa serwera Spark Thrift. Kliknij jeden z headnodes.

    ![Uruchom ponownie serwer thrift](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. Dalej znajduje się lista wszystkich usług, które są uruchomione na tym headnode. Na liście kliknij przycisk listy rozwijanej obok Spark Thrift serwera, a następnie kliknij przycisk **zatrzymać**.

    ![Uruchom ponownie serwer thrift](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Powtórz te kroki dla innych headnode również.

## <a name="restart-the-jupyter-service"></a>Uruchom ponownie usługę Jupyter
Uruchamianie interfejsu użytkownika sieci Web Ambari, jak pokazano na początku tego artykułu. W okienku nawigacji po lewej stronie kliknij **Jupyter**, kliknij przycisk **akcji usługi**, a następnie kliknij przycisk **ponowne uruchomienie wszystkich**. Spowoduje to uruchomienie usługi Jupyter na wszystkich headnodes.

![Uruchom ponownie Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "Uruchom ponownie Jupyter")

## <a name="monitor-resources"></a>Monitorowanie zasobów
Uruchom interfejs użytkownika Yarn, jak pokazano na początku tego artykułu. W tabeli metrykę klastra u góry ekranu, sprawdź wartości **pamięć używana** i **całkowitej pamięci** kolumn. Jeśli te dwie wartości są Zamknij, może nie być wystarczającej ilości zasobów, aby uruchomić następnej aplikacji. To samo dotyczy **używane VCores** i **łącznie VCores** kolumn. Ponadto w widoku głównego, w przypadku aplikacji przebywanie w **ZAAKCEPTOWANE** stanu i nie są przenoszone do **systemem** ani **nie powiodło się** stanu, przyczyną może być również wskazanie, że nie otrzymuje wystarczającej liczby zasobów, aby uruchomić.

![Limit zasobów](./media/apache-spark-resource-manager/resource-limit.png "Limit zasobów")

## <a name="kill-running-applications"></a>Kasowanie uruchamianie aplikacji
1. W interfejsie użytkownika Yarn z lewego panelu, kliknij przycisk **systemem**. Z listy uruchomionych aplikacji, należy określić aplikację, aby skasowane i kliknij pozycję **identyfikator**.

    ![Kasowanie App1](./media/apache-spark-resource-manager/kill-app1.png "Kill App1")

2. Kliknij przycisk **Kill aplikacji** w prawym górnym rogu, następnie kliknij polecenie **OK**.

    ![Kasowanie App2](./media/apache-spark-resource-manager/kill-app2.png "Kill App2")

## <a name="see-also"></a>Zobacz także
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Dla analityków danych

* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analiza danych telemetrycznych usługi Application Insight przy użyciu platformy Spark w usłudze HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Użyj Caffe Azure HDInsight Spark dla rozproszonych learning bezpośrednich](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Dla deweloperów platformy Spark

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)
