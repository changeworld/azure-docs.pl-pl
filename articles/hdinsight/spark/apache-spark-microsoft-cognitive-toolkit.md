---
title: Microsoft Cognitive Toolkit za pomocą usługi Azure HDInsight Spark do uczenia głębokiego
description: Dowiedz się, jak uczonego modelu uczenia głębokiego firmy Microsoft, Cognitive Toolkit, można zastosować do zestawu danych za pomocą interfejsu API Python platformy Spark w klastrze usługi Azure HDInsight Spark.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: 3462255311eaa6e418f97de5da598eb985b2a935
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097290"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Użyj Microsoft Cognitive Toolkit głębokie uczenie modelu przy użyciu klastra usługi HDInsight Spark

W tym artykule należy wykonać poniższe kroki.

1. Uruchom niestandardowy skrypt, aby zainstalować [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) w klastrze usługi Azure HDInsight Spark.

2. Przekaż [notesu programu Jupyter](https://jupyter.org/) do [platformy Apache Spark](https://spark.apache.org/) klastra, aby zobaczyć, jak stosowanie uczonego modelu uczenia głębokiego firmy Microsoft, Cognitive Toolkit do plików na konto usługi Azure Blob Storage za pomocą [ Spark (PySpark) interfejs API języka Python](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Przed rozpoczęciem tego samouczka musisz dysponować subskrypcją platformy Azure. Zobacz [Utwórz bezpłatne konto platformy Azure już dzisiaj](https://azure.microsoft.com/free).

* **Klaster usługi Azure HDInsight Spark**. W tym artykule należy utworzyć klaster Spark w wersji 2.0. Aby uzyskać instrukcje, zobacz [tworzenie Apache klastra Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>To rozwiązanie przepływ?

To rozwiązanie jest podzielony między w tym artykule i notesu programu Jupyter, który przekazany w ramach tego samouczka. W tym artykule wykonasz następujące czynności:

* Uruchom akcję skryptu w klastrze usługi HDInsight Spark, aby zainstalować Microsoft Cognitive Toolkit i pakiety języka Python.
* Przekazywanie notesu programu Jupyter, z systemem rozwiązania do klastra platformy HDInsight Spark.

Następujące pozostałe kroki zostały uwzględnione w notesie Jupyter.

- Załaduj przykładowe obrazy do platformy Spark Resiliant dystrybuowane w zestawie danych lub RDD.
   - Ładowanie modułów i zdefiniuj ustawienia wstępne.
   - Pobierz zestaw danych lokalnie w klastrze Spark.
   - Konwertuj RDD zestawu danych.
- Wynik obrazów za pomocą uczonego modelu zestawu narzędzi Cognitive Toolkit.
   - Pobierz uczonego modelu zestawu narzędzi Cognitive Toolkit do klastra Spark.
   - Definiowanie funkcji, który będzie używany przez węzły procesu roboczego.
   - Wynik obrazów na węzłach procesu roboczego.
   - Oceny dokładności modelu.


## <a name="install-microsoft-cognitive-toolkit"></a>Instalowanie zestawu narzędzi Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit można zainstalować w klastrze Spark przy użyciu akcji skryptu. Akcja skryptu używa niestandardowych skryptów do instalowania składników w klastrze, które nie są domyślnie dostępne. Można użyć niestandardowego skryptu w witrynie Azure portal, za pomocą zestawu .NET SDK HDInsight lub przy użyciu programu Azure PowerShell. Umożliwia także skrypt do zainstalowania narzędzi albo, jako część tworzenia klastra, lub gdy klaster jest uruchomiona. 

W tym artykule używamy portalu do zainstalowania zestawu narzędzi, po utworzeniu klastra. Aby poznać inne sposoby uruchamia skrypt niestandardowy, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby uzyskać instrukcje, jak uruchomić akcji skryptu za pomocą witryny Azure portal, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Upewnij się, że możesz podać następujące dane wejściowe, aby zainstalować Microsoft Cognitive Toolkit.

* Podaj wartość dla nazwy akcji skryptu.

* Aby uzyskać **identyfikator URI skryptu powłoki systemowej**, wprowadź `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Upewnij się tylko na węzły główne i proces roboczy należy uruchomić skrypt i wyczyść wszystkie pozostałe pola wyboru.

* Kliknij pozycję **Utwórz**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Przekazywanie notesu programu Jupyter do klastra usługi HDInsight Spark

Microsoft Cognitive Toolkit za pomocą klastra Azure HDInsight Spark, należy załadować notesu programu Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** do klastra usługi HDInsight Spark. Ten notes jest dostępna w witrynie GitHub pod [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Sklonuj repozytorium GitHub [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Aby uzyskać instrukcje sklonować, zobacz [klonowanie repozytorium](https://help.github.com/articles/cloning-a-repository/).

2. W witrynie Azure portal, otwórz bloku klastra Spark, który już zainicjowano obsługę administracyjną, kliknąć **pulpit nawigacyjny klastra**, a następnie kliknij przycisk **notesu programu Jupyter**.

    Można również uruchomić notesu programu Jupyter, przechodząc do adresu URL `https://<clustername>.azurehdinsight.net/jupyter/`. Zastąp \<nazwa_klastra > nazwą klastra usługi HDInsight.

3. Z notesu programu Jupyter, kliknij przycisk **przekazywanie** w prawym górnym rogu, a następnie przejdź do lokalizacji, do którego zostało sklonowane repozytorium GitHub.

    ![Przekazywanie notesu programu Jupyter do klastra usługi HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "przekazywanie notesu programu Jupyter do klastra usługi HDInsight Spark")

4. Kliknij przycisk **przekazywanie** ponownie.

5. Po przekazaniu Notes kliknij nazwę notesu, a następnie postępuj zgodnie z instrukcjami w notesie na temat sposobu ładowania zestawu danych i wykonywania tego samouczka.

## <a name="see-also"></a>Zobacz także
* [Omówienie: Platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Apache Spark przy użyciu Power BI: Interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika witryny sieci Web przy użyciu platformy Apache Spark w HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Dane telemetryczne analiza danych Application Insight przy użyciu platformy Apache Spark w HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji platformy Apache Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](apache-spark-jupyter-notebook-kernels.md)
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
