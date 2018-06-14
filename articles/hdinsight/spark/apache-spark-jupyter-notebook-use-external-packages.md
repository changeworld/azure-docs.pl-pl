---
title: Niestandardowe pakiety narzędzia Maven za pomocą Jupyter w łączniku Spark on Azure HDInsight | Dokumentacja firmy Microsoft
description: Instrukcje krok po kroku dotyczące sposobu konfigurowania notesów Jupyter dostępne z klastrami Spark w usłudze HDInsight do używania niestandardowych pakietów Maven.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2a8bc545-064e-436f-8b5f-e67c26cfbf98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: dfecdd5c9399c1bd6eb021f097481e3c73f699ad
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31520827"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Korzystanie z zewnętrznych pakietów z notesami Jupyter w klastrach Apache Spark w usłudze HDInsight
> [!div class="op_single_selector"]
> * [Przy użyciu magic komórki](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Za pomocą akcji skryptu](apache-spark-python-package-installation.md)
>
>

Dowiedz się, jak skonfigurować notesu Jupyter w klastrze Apache Spark w usłudze HDInsight do korzystania z zewnętrznego, przyczyniły się społeczności **maven** pakiety, które nie są uwzględnione w poziomie pole w klastrze. 

Możesz przeszukać [repozytorium Maven](http://search.maven.org/) Aby uzyskać pełną listę pakietów, które są dostępne. Można również uzyskać listę dostępnych pakietów z innych źródeł. Na przykład pełną listę społeczności przyczyniły się do pakietów znajduje się w temacie [pakietów Spark](http://spark-packages.org/).

W w tym artykule przedstawiono sposób użycia [udostępnionego woluminu klastra spark](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pakietu z notesem Jupyter.

## <a name="prerequisites"></a>Wymagania wstępne
Należy dysponować następującymi elementami:

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [klastrów utworzyć Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Korzystanie z zewnętrznych pakietów z notesami Jupyter
1. W [Portalu Azure](https://portal.azure.com/) na tablicy startowej kliknij kafelek klastra Spark (jeśli został przypięty do tablicy startowej). Możesz także przejść do klastra, wybierając polecenia **Przeglądaj wszystko** > **Klastry usługi HDInsight**.   

2. W bloku klastra Spark kliknij pozycję **Szybkie linki**, a następnie w bloku **Pulpit nawigacyjny klastra** kliknij pozycję **Jupyter Notebook**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

    > [!NOTE]
    > Można również przejść do aplikacji Jupyter Notebook dla klastra, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
    > 

3. Utwórz nowy notes. Kliknij przycisk **nowy**, a następnie kliknij przycisk **Spark**.
   
    ![Tworzenie nowego notesu Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Tworzenie nowego notesu Jupyter")

4. Zostanie utworzony i otwarty nowy notes o nazwie Untitled.pynb. Kliknij nazwę notesu u góry, a następnie wprowadź przyjazną nazwę.
   
    ![Wprowadzanie nazwy notesu](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Wprowadzanie nazwy notesu")

5. Użyjesz `%%configure` magic do skonfigurowania, aby użyć pakietów zewnętrznych. W notesach, które używają pakiety zewnętrzne, upewnij się, należy wywołać `%%configure` magic w pierwszej komórki kodu. Dzięki temu, że jądra jest skonfigurowana do używania pakietu, przed rozpoczęciem sesji.

    >[!IMPORTANT] 
    >Jeśli zapomnisz do konfigurowania jądra w pierwszej komórki, możesz użyć `%%configure` z `-f` parametru, ale zostanie uruchomiona ponownie sesję i postępu wszystkich zostaną utracone.

    | Wersja usługi HDInsight | Polecenie |
    |-------------------|---------|
    |HDInsight 3.3 i HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | HDInsight 3.5 i HDInsight 3,6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

6. Fragment kodu powyżej oczekuje współrzędne maven pakietów zewnętrznych w centralnym repozytorium Maven. W tym fragmencie `com.databricks:spark-csv_2.10:1.4.0` jest Współrzędna maven dla **udostępnionego woluminu klastra spark** pakietu. Oto, jak utworzyć współrzędnych dla pakietu.
   
    a. Zlokalizuj pakiet w repozytorium Maven. W tym samouczku używamy [udostępnionego woluminu klastra spark](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Z repozytorium, Zbierz wartości **GroupId**, **ArtifactId**, i **wersji**. Upewnij się, że wartości, które należy zebrać są zgodne z klastrem. W tym przypadku użyto Scala 2.10 i Spark 1.4.0 pakietu, ale musisz wybrać różne wersje dla odpowiedniego języka Scala lub wersję platformy Spark w klastrze. Można sprawdzić wersji języka Scala w klastrze, uruchamiając `scala.util.Properties.versionString` jądra Spark Jupyter lub Prześlij Spark. Można sprawdzić wersji platformy Spark w klastrze, uruchamiając `sc.version` na notesów Jupyter.
   
    ![Pakiety zewnętrzne za pomocą notesu Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "pakiety zewnętrzne za pomocą notesu Jupyter")
   
    c. Łączenie trzech oddzieloną dwukropkiem (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

7. Uruchom komórki kodu z `%%configure` magic. Spowoduje to skonfigurowanie podstawowej sesję Livy za pomocą pakietu podane. W kolejnych komórek w notesie można teraz używać pakietu, jak pokazano poniżej.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Aby 3,6 HDInsight należy użyć następującego fragmentu kodu.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

8. Następnie możesz uruchomić fragmenty kodu, tak samo, jak pokazano poniżej, aby wyświetlić dane z dataframe utworzony w poprzednim kroku.
   
        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Zobacz też
* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Spark i analiza biznesowa: interakcyjna analiza danych na platformie Spark w usłudze HDInsight z użyciem narzędzi do analizy biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Użyj python zewnętrznych pakietów z notesami Jupyter w klastrach Apache Spark w usłudze HDInsight w systemie Linux](apache-spark-python-package-installation.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
