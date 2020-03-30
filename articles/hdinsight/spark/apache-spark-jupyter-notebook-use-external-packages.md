---
title: Używanie niestandardowych pakietów Maven z programem Jupyter w aplikacji Spark — Azure HDInsight
description: Instrukcje krok po kroku dotyczące konfigurowania notebooków Jupyter dostępnych w klastrach HDInsight Spark do używania niestandardowych pakietów Maven.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cec94b2ecb18bc9e8cceb24a21967a3c829d78a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74561722"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Używanie pakietów zewnętrznych z notesami Jupyter w klastrach Apache Spark w programie HDInsight

> [!div class="op_single_selector"]
> * [Korzystanie z magii komórek](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Korzystanie z akcji skryptu](apache-spark-python-package-installation.md)

Dowiedz się, jak skonfigurować [notes Jupyter](https://jupyter.org/) w klastrze Apache Spark w programie HDInsight do używania zewnętrznych pakietów Apache **maven,** które nie są zawierane w klastrze.

Pełną listę pakietów, które są dostępne, można przeszukać [repozytorium Maven.](https://search.maven.org/) Można również uzyskać listę dostępnych pakietów z innych źródeł. Na przykład pełna lista pakietów udostępnionych przez społeczność jest dostępna w [pakietach Spark Packages.](https://spark-packages.org/)

W tym artykule dowiesz się, jak korzystać z pakietu [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) z notesem Jupyter.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Znajomość zagadnień dotyczących używania notesów Jupyter za pomocą platformy Spark w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz [Ładowanie danych i uruchamianie zapytań za pomocą platformy Apache Spark w programie HDInsight](./apache-spark-load-data-run-query.md).

* [Schemat identyfikatorów URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) dla magazynu podstawowego klastrów. Dotyczy to `wasb://` usługi Azure `abfs://` Storage, usługi Azure `adl://` Data Lake Storage Gen2 lub usługi Azure Data Lake Storage Gen1. Jeśli bezpieczny transfer jest włączony dla usługi Azure Storage lub Data `wasbs://` `abfss://`Lake Storage Gen2, identyfikator URI będzie lub , odpowiednio, Zobacz też, [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Korzystanie z zewnętrznych pakietów z notesami Jupyter

1. Przejdź `https://CLUSTERNAME.azurehdinsight.net/jupyter` do `CLUSTERNAME` miejsca, w którym znajduje się nazwa klastra platformy Spark.

1. Utwórz nowy notes. Wybierz **pozycję Nowy**, a następnie wybierz pozycję **Iskra**.

    ![Tworzenie nowego notesu Spark Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Tworzenie nowego notesu Jupyter")

1. Zostanie utworzony i otwarty nowy notes o nazwie Untitled.pynb. Wybierz nazwę notesu u góry i wprowadź przyjazną nazwę.

    ![Wprowadzanie nazwy notesu](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Wprowadzanie nazwy notesu")

1. Użyjesz magii, `%%configure` aby skonfigurować notes do używania pakietu zewnętrznego. W notesach korzystających z pakietów `%%configure` zewnętrznych upewnij się, że wywoływałeś magię w pierwszej komórce kodu. Gwarantuje to, że jądro jest skonfigurowane do używania pakietu przed rozpoczęciem sesji.

    >[!IMPORTANT]  
    >Jeśli zapomnisz skonfigurować jądro w pierwszej komórce, możesz użyć parametru `%%configure` z parametrem, `-f` ale spowoduje to ponowne uruchomienie sesji i cały postęp zostanie utracony.

    | Wersja HDInsight | Polecenie |
    |-------------------|---------|
    | Dla HDInsight 3.5 i HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |Dla HDInsight 3.3 i HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. Powyższy fragment oczekuje współrzędnych maven dla pakietu zewnętrznego w repozytorium Centralnym Maven. W tym urywek jest `com.databricks:spark-csv_2.11:1.5.0` współrzędną maven dla pakietu **spark-csv.** Oto jak skonstruować współrzędne dla pakietu.

    a. Znajdź pakiet w repozytorium Maven. W tym artykule używamy [spark-csv](https://mvnrepository.com/artifact/com.databricks/spark-csv).

    b. Z repozytorium zebrać wartości dla **GroupId**, **ArtifactId**i **Version**. Upewnij się, że zebrane wartości są zgodne z klastrem. W takim przypadku używamy pakietu Scala 2.11 i Spark 1.5.0, ale może być konieczne wybranie różnych wersji dla odpowiedniej wersji Scala lub Spark w klastrze. Wersję scala można znaleźć w klastrze, uruchamiając `scala.util.Properties.versionString` jądro Spark Jupyter lub przesłać spark. Wersję platformy Spark można znaleźć w `sc.version` klastrze, uruchamiając je w notesach firmy Jupyter.

    ![Używanie pakietów zewnętrznych z notebookiem Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Używanie pakietów zewnętrznych z notebookiem Jupyter")

    d. Połącz trzy wartości, oddzielone dwukropkiem (**:**).

        com.databricks:spark-csv_2.11:1.5.0

1. Uruchom komórkę `%%configure` kodu za pomocą magii. Spowoduje to skonfigurowanie podstawowej sesji Livy do używania dostarczonego pakietu. W kolejnych komórkach notesu można teraz korzystać z pakietu, jak pokazano poniżej.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    W przypadku programu HDInsight 3.4 i poniżej należy użyć następującego fragmentu kodu.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Następnie można uruchomić fragmenty, jak pokazano poniżej, aby wyświetlić dane z ramki danych utworzonej w poprzednim kroku.

        df.show()
   
        df.select("Time").count()

## <a name="see-also"></a><a name="seealso"></a>Zobacz też

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z analizą biznesową: wykonywanie interaktywnej analizy danych przy użyciu funkcji Spark w funkcji HDInsight za pomocą narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z uczeniem maszynowym: użyj platformy Spark w hdinsight do analizowania temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika strony internetowej przy użyciu platformy Apache Spark w hdinsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Używanie zewnętrznych pakietów pythona z notebookami Jupyter w klastrach Apache Spark w systemie HDInsight Linux](apache-spark-python-package-installation.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki HDInsight Tools dla IntelliJ IDEA do zdalnego debugowania aplikacji Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Używanie notebooków Apache Zeppelin z klastrem Apache Spark w programie HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notebooka Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
