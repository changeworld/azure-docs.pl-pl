---
title: Microsoft Cognitive Toolkit z usługą Azure HDInsight Apache Spark
description: Dowiedz się, jak przeszkolony Microsoft Cognitive Toolkit model uczenia głębokiego może zostać zastosowany do zestawu danych przy użyciu interfejsu API platformy Spark w klastrze Azure HDInsight Spark.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: 0f4172c7a5b287c85c0548c7fe9812305a1ee1e6
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241536"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Używanie Microsoft Cognitive Toolkit modelu uczenia głębokiego z klastrem Azure HDInsight Spark

W tym artykule opisano następujące czynności.

1. Uruchamianie niestandardowego skryptu w celu zainstalowania [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) w klastrze Azure HDInsight Spark.

2. Przekaż [Jupyter Notebook](https://jupyter.org/) do klastra [Apache Spark](https://spark.apache.org/) , aby dowiedzieć się, jak zastosować szkolony Microsoft Cognitive Toolkit model uczenia głębokiego do plików na koncie usługi Azure Blob Storage przy użyciu [interfejsu API Spark Python (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Przed rozpoczęciem pracy z tym artykułem musisz mieć subskrypcję platformy Azure. Zobacz [Utwórz bezpłatne konto platformy Azure już dzisiaj](https://azure.microsoft.com/free).

* **Azure HDInsight Spark klaster**. W tym artykule Utwórz klaster Spark 2,0. Aby uzyskać instrukcje, zobacz [Tworzenie klastra Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Jak działa ten przepływ rozwiązania?

To rozwiązanie jest podzielone między tym artykułem a Jupyter notesem, który został przekazany w ramach tego artykułu. W tym artykule wykonaj następujące czynności:

* Uruchom akcję skryptu w klastrze usługi HDInsight Spark w celu zainstalowania pakietów Microsoft Cognitive Toolkit i języka Python.
* Przekaż Notes Jupyter, który uruchamia rozwiązanie w klastrze usługi HDInsight Spark.

Następujące pozostałe kroki zostały omówione w notesie Jupyter.

- Załaduj przykładowe obrazy do rozproszonego zestawu danych platformy Spark Resiliant lub RDD.
   - Ładowanie modułów i Definiowanie ustawień wstępnych.
   - Pobierz zestaw danych lokalnie w klastrze Spark.
   - Przekonwertuj zestaw danych na RDD.
- Ocena obrazów przy użyciu przeszkolonego modelu Cognitive Toolkit.
   - Pobierz przeszkolony model Cognitive Toolkit do klastra Spark.
   - Zdefiniuj funkcje, które mają być używane przez węzły procesu roboczego.
   - Wykreśl obrazy na węzłach procesu roboczego.
   - Oceń dokładność modelu.


## <a name="install-microsoft-cognitive-toolkit"></a>Zainstaluj Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit można zainstalować w klastrze Spark za pomocą akcji skryptu. Akcja skryptu używa niestandardowych skryptów do instalowania składników w klastrze, które nie są dostępne domyślnie. Możesz użyć niestandardowego skryptu z Azure Portal przy użyciu zestawu .NET SDK usługi HDInsight lub przy użyciu Azure PowerShell. Możesz również użyć skryptu, aby zainstalować zestaw narzędzi w ramach tworzenia klastra lub gdy klaster jest uruchomiony. 

W tym artykule używamy portalu do zainstalowania zestawu narzędzi po utworzeniu klastra. Aby poznać inne sposoby uruchamiania skryptu niestandardowego, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby uzyskać instrukcje dotyczące sposobu używania Azure Portal do uruchamiania akcji skryptu, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Upewnij się, że podano następujące dane wejściowe, aby zainstalować Microsoft Cognitive Toolkit.

* Podaj wartość dla nazwy akcji skryptu.

* Dla **identyfikatora URI skryptu bash**wprowadź `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Upewnij się, że skrypt jest uruchamiany tylko w węzłach głównych i procesów roboczych i wyczyść wszystkie inne pola wyboru.

* Kliknij przycisk **Utwórz**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Przekazywanie notesu Jupyter do klastra Azure HDInsight Spark

Aby użyć Microsoft Cognitive Toolkit z klastrem Azure HDInsight Spark, należy załadować Notes Jupyter **CNTK_model_scoring_on_Spark_walkthrough. ipynb** do klastra Azure HDInsight Spark. Ten Notes jest dostępny w witrynie GitHub w [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Sklonuj repozytorium GitHub [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Aby uzyskać instrukcje klonowania, zobacz [klonowanie repozytorium](https://help.github.com/articles/cloning-a-repository/).

2. W Azure Portal Otwórz blok klastra Spark, który został już zainicjowany, kliknij pozycję **pulpit nawigacyjny klastra**, a następnie kliknij pozycję **Notes Jupyter**.

    Możesz również uruchomić Notes Jupyter, przechodząc do adresu URL `https://<clustername>.azurehdinsight.net/jupyter/`. Zastąp zmienną \<ClusterName > nazwą klastra usługi HDInsight.

3. W notesie Jupyter kliknij pozycję **Przekaż** w prawym górnym rogu, a następnie przejdź do lokalizacji, w której Sklonowano repozytorium GitHub.

    ![Przekaż Notes Jupyter do klastra Azure HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Przekaż Notes Jupyter do klastra Azure HDInsight Spark")

4. Kliknij ponownie przycisk **Przekaż** .

5. Po przekazaniu notesu kliknij nazwę notesu, a następnie postępuj zgodnie z instrukcjami w notesie dotyczącym ładowania zestawu danych i wykonaj artykuł.

## <a name="see-also"></a>Zobacz także
* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Apache Spark z usługą BI: wykonywanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analiza danych telemetrycznych usługi Application Insight przy użyciu Apache Spark w usłudze HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki narzędzi HDInsight do IntelliJ pomysł, aby debugować aplikacje Apache Spark zdalnie](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
