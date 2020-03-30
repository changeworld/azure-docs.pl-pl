---
title: Microsoft Cognitive Toolkit z apache Spark - Azure HDInsight
description: Dowiedz się, jak wyszkolony model uczenia głębokiego microsoft cognitive toolkit można zastosować do zestawu danych przy użyciu interfejsu API języka Spark Python w klastrze platformy Azure HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: 1933db624dfef2ffa747ecb043be6730b6b884b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206558"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Korzystanie z modelu uczenia głębokiego microsoft cognitive toolkit z klastrem Azure HDInsight Spark

W tym artykule wykonaj następujące kroki.

1. Uruchom skrypt niestandardowy, aby zainstalować [zestaw narzędzi Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/) w klastrze platformy Azure HDInsight Spark.

2. Przekaż [notes Jupyter](https://jupyter.org/) do klastra [Platformy Spark Apache,](https://spark.apache.org/) aby zobaczyć, jak zastosować przeszkolony model uczenia głębokiego Microsoft Cognitive Toolkit do plików na koncie usługi Azure Blob Storage przy użyciu [interfejsu API Języka Spark Python (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html)

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Zobacz [Tworzenie klastra Platformy Spark apache](./apache-spark-jupyter-spark-sql-use-portal.md).

* Znajomość zagadnień dotyczących używania notesów Jupyter za pomocą platformy Spark w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz [Ładowanie danych i uruchamianie zapytań za pomocą platformy Apache Spark w programie HDInsight](./apache-spark-load-data-run-query.md).

## <a name="how-does-this-solution-flow"></a>Jak przebiega to rozwiązanie?

To rozwiązanie jest podzielone między ten artykuł i notesu Jupyter, który można przesłać w ramach tego artykułu. W tym artykule wykonaj następujące kroki:

* Uruchom akcję skryptu w klastrze platformy SPARK usługi HDInsight, aby zainstalować pakiety Microsoft Cognitive Toolkit i Python.
* Przekaż notes Jupyter, który uruchamia rozwiązanie do klastra platformy SPARK usługi HDInsight.

Poniższe pozostałe kroki są opisane w notesie Jupyter.

* Załaduj przykładowe obrazy do rozproszonego zestawu danych spark resilient lub rdd.
  * Załaduj moduły i zdefiniuj ustawienia wstępne.
  * Pobierz zestaw danych lokalnie w klastrze platformy Spark.
  * Konwertuj zestaw danych na RDD.
* Ocena obrazów przy użyciu wyszkolonego modelu Cognitive Toolkit.
  * Pobierz przeszkolony model zestawu narzędzi Cognitive toolkit do klastra Platformy Spark.
  * Zdefiniuj funkcje, które mają być używane przez węzły procesu roboczego.
  * Ocena obrazów w węzłach procesu roboczego.
  * Oceń dokładność modelu.

## <a name="install-microsoft-cognitive-toolkit"></a>Instalowanie zestawu narzędzi Microsoft Cognitive

Zestaw narzędzi Microsoft Cognitive Toolkit można zainstalować w klastrze platformy Spark przy użyciu akcji skryptu. Akcja skryptu używa skryptów niestandardowych do instalowania składników w klastrze, które nie są domyślnie dostępne. Skryptu niestandardowego można użyć z witryny Azure portal, przy użyciu usługi HDInsight .NET SDK lub przy użyciu programu Azure PowerShell. Można również użyć skryptu, aby zainstalować zestaw narzędzi w ramach tworzenia klastra lub po uruchomieniu klastra.

W tym artykule używamy portalu do zainstalowania zestawu narzędzi po utworzeniu klastra. Aby uzyskać inne sposoby uruchamiania skryptu niestandardowego, zobacz [Dostosowywanie klastrów USŁUGI HDInsight przy użyciu akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby uzyskać instrukcje dotyczące używania witryny Azure Portal do uruchamiania akcji skryptu, zobacz [Dostosowywanie klastrów HDInsight przy użyciu akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation). Upewnij się, że podasz następujące dane wejściowe, aby zainstalować microsoft cognitive toolkit. Użyj następujących wartości dla akcji skryptu:

|Właściwość |Wartość |
|---|---|
|Typ skryptu|- Niestandardowe|
|Nazwa| Instalowanie MCT|
|Identyfikator URI skryptu bash|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Typ(-y) węzła:|Głowa, Pracownik|
|Parametry|Brak|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Przekazywanie notesu Jupyter do klastra platformy Azure HDInsight Spark

Aby użyć zestawu narzędzi Microsoft Cognitive Toolkit z klastrem Azure HDInsight Spark, należy załadować notes Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** do klastra platformy Azure HDInsight Spark. Ten notes jest dostępny na [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)GitHub pod adresem .

1. Pobierz i rozpaj [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net/jupyter`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.

1. W notesie Jupyter wybierz pozycję **Prześlij** w prawym górnym `CNTK_model_scoring_on_Spark_walkthrough.ipynb`rogu, a następnie przejdź do pliku pobierania i wybierz plik .

    ![Przekazywanie notesu jupyter do klastra platformy Azure HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Przekazywanie notesu jupyter do klastra platformy Azure HDInsight Spark")

1. Ponownie wybierz **pozycję Przekaż.**

1. Po przekazaniu notesu kliknij nazwę notesu, a następnie postępuj zgodnie z instrukcjami w samym notesie, jak załadować zestaw danych i wykonać artykuł.

## <a name="see-also"></a>Zobacz też

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z analizą biznesową: wykonywanie interaktywnej analizy danych przy użyciu funkcji Spark w funkcji HDInsight za pomocą narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z uczeniem maszynowym: użyj platformy Spark w hdinsight do analizowania temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika strony internetowej przy użyciu platformy Apache Spark w hdinsight](apache-spark-custom-library-website-log-analysis.md)
* [Analiza danych telemetrycznych usługi Application Insight przy użyciu platformy Spark apache w udziale hdinsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki HDInsight Tools dla IntelliJ IDEA do zdalnego debugowania aplikacji Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Używanie notebooków Apache Zeppelin z klastrem Apache Spark w programie HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notebooka Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
