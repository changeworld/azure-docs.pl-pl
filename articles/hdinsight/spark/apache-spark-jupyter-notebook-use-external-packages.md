---
title: Niestandardowe pakiety narzędzia Maven za pomocą programu Jupyter w usłudze Spark w usłudze Azure HDInsight
description: Instrukcje krok po kroku dotyczące sposobu konfigurowania notesów programu Jupyter dostępne przy użyciu klastrów HDInsight Spark za pomocą niestandardowych pakietów Maven.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: hrasheed
ms.openlocfilehash: 6af25b95aa3a38c4a2e9f3bd8147604dccae0abb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64715154"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Korzystanie z zewnętrznych pakietów z notesami Jupyter w klastrach platformy Apache Spark w HDInsight
> [!div class="op_single_selector"]
> * [Przy użyciu funkcji cell magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Za pomocą akcji skryptu](apache-spark-python-package-installation.md)

Dowiedz się, jak skonfigurować [notesu programu Jupyter](https://jupyter.org/) w klastrze Apache Spark w HDInsight służące zewnętrznego przez społeczność Apache **maven** pakiety, które nie są uwzględnione poza pole w klastrze. 

Możesz wyszukiwać [repozytorium Maven](https://search.maven.org/) uzyskać pełną listę pakietów, które są dostępne. Możesz również uzyskać listę dostępnych pakietów z innych źródeł. Na przykład pełną listę pakietów przez społeczność jest dostępne pod adresem [pakietów platformy Spark](https://spark-packages.org/).

W tym artykule nauczysz się używać [spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pakietu z notesem Jupyter.

## <a name="prerequisites"></a>Wymagania wstępne
Należy dysponować następującymi elementami:

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Korzystanie z zewnętrznych pakietów z notesami Jupyter
1. W [Portalu Azure](https://portal.azure.com/) na tablicy startowej kliknij kafelek klastra Spark (jeśli został przypięty do tablicy startowej). Możesz także przejść do klastra, wybierając polecenia **Przeglądaj wszystko** > **Klastry usługi HDInsight**.   

1. W bloku klastra Spark kliknij pozycję **Szybkie linki**, a następnie w bloku **Pulpit nawigacyjny klastra** kliknij pozycję **Jupyter Notebook**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

    > [!NOTE]  
    > Można również przejść do aplikacji Jupyter Notebook dla klastra, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Utwórz nowy notes. Kliknij przycisk **New**, a następnie kliknij przycisk **Spark**.
   
    ![Tworzenie nowego notesu Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Tworzenie nowego notesu Jupyter")

1. Zostanie utworzony i otwarty nowy notes o nazwie Untitled.pynb. Kliknij nazwę notesu u góry, a następnie wprowadź przyjazną nazwę.
   
    ![Wprowadzanie nazwy notesu](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Wprowadzanie nazwy notesu")

1. Użyjesz `%%configure` magic można skonfigurować do użycia pakietu zewnętrznego. W notesach korzystających z zewnętrznych pakietów, upewnij się, należy wywołać `%%configure` magic w pierwszej komórki kodu. Daje to gwarancję, że jądro jest skonfigurowany do używania pakietu, przed rozpoczęciem sesji.

    >[!IMPORTANT]  
    >Jeśli użytkownik zapomni skonfigurować jądra w pierwszej komórki, można użyć `%%configure` z `-f` parametru, ale spowoduje ponowne uruchomienie sesji i zostanie utracone.

    | HDInsight w wersji | Polecenie |
    |-------------------|---------|
    |HDInsight 3.3 i HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | HDInsight 3.5 i HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

1. Fragment kodu powyżej oczekuje współrzędne maven zewnętrznych pakietów w centralnym repozytorium Maven. W tym fragmencie kodu `com.databricks:spark-csv_2.10:1.4.0` jest Współrzędna maven dla **spark csv** pakietu. Oto, jak skonstruować współrzędnych dla pakietu.
   
    a. Zlokalizuj pakiet w repozytorium narzędzia Maven. W tym samouczku używamy [spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Z repozytorium, Zbierz wartości **GroupId**, **ArtifactId**, i **wersji**. Upewnij się, że wartości, które zostały zebrane zgodne klastra. W tym przypadku używamy Scala 2.10 i aparatu Spark 1.4.0 pakietu, ale musisz wybrać różne wersje dla odpowiedniego języka Scala lub wersji platformy Spark w klastrze. Można sprawdzić wersji Scala w klastrze, uruchamiając `scala.util.Properties.versionString` na jądra programu Jupyter Spark lub Prześlij platformy Spark. Można sprawdzić wersji platformy Spark w klastrze, uruchamiając `sc.version` na notesów programu Jupyter.
   
    ![Korzystanie z zewnętrznych pakietów z notesem Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "korzystanie z zewnętrznych pakietów z notesu programu Jupyter")
   
    c. Łączenie z trzech wartości, rozdzielone średnikiem (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

1. Uruchomić komórkę kodu za pomocą `%%configure` magic. To spowoduje skonfigurowanie podstawowego sesji usługi Livy umożliwia użycie pakietu, które podałeś. W kolejnych komórek w notesie można teraz używać pakietu, jak pokazano poniżej.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Aby HDInsight 3.6 należy użyć następującego fragmentu kodu.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Następnie możesz uruchomić fragmenty kodu, jak pokazano poniżej, aby wyświetlić dane z ramkę danych został utworzony w poprzednim kroku.
   
        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Zobacz też
* [Omówienie: Platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Apache Spark przy użyciu Power BI: Interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika witryny sieci Web przy użyciu platformy Apache Spark w HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Pakiety zewnętrzne języka python za pomocą notesów programu Jupyter w klastrach platformy Apache Spark w HDInsight w systemie Linux](apache-spark-python-package-installation.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji platformy Apache Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
