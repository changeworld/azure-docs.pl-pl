---
title: Kognitywnych zestaw narzędzi firmy Microsoft z Azure HDInsight Spark dla głębokości learning | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uczonego modelu uczenia głębokie kognitywnych zestaw narzędzi firmy Microsoft może odnosić się do zestawu danych za pomocą interfejsu API języka Python Spark w klastrze usługi Azure HDInsight Spark.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: jgao
ms.openlocfilehash: 7afb891642e3e53da5eb1e17ee654fb5fb42c313
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Użyj głębokiego uczenie modelu z klastrem usługi Azure HDInsight Spark Toolkit kognitywnych firmy Microsoft

W tym artykule należy wykonać poniższe kroki.

1. Uruchom niestandardowy skrypt, aby zainstalować w klastrze Spark w usłudze HDInsight Azure kognitywnych zestaw narzędzi firmy Microsoft.

2. Przekaż notesu Jupyter do klastra Spark, aby zobaczyć, jak zastosować uczonego modelu uczenia głębokie kognitywnych zestaw narzędzi firmy Microsoft do plików za pomocą konta magazynu obiektów Blob Azure [interfejsu API języka Python Spark (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Przed rozpoczęciem tego samouczka musisz dysponować subskrypcją platformy Azure. Zobacz [Utwórz bezpłatne konto platformy Azure już dzisiaj](https://azure.microsoft.com/free).

* **Azure klastra Spark w usłudze HDInsight**. W tym artykule należy utworzyć klaster Spark 2.0. Aby uzyskać instrukcje, zobacz [klastra Utwórz Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Jak przepływu tego rozwiązania?

To rozwiązanie jest dzielona między w tym artykule i notesu Jupyter, który należy przekazać w ramach tego samouczka. W tym artykule należy wykonać następujące czynności:

* Uruchom akcję skryptu w klastrze Spark w usłudze HDInsight, aby zainstalować kognitywnych zestaw narzędzi firmy Microsoft i pakiety języka Python.
* Przekaż notesu Jupyter, który uruchamia rozwiązania do klastra Spark w usłudze HDInsight.

Następujące pozostałe kroki są objęte notesu Jupyter.

- Załaduj przykładowe obrazy do Spark Resiliant dystrybuowane w zestawie danych lub RDD
   - Ładowanie modułów i zdefiniować ustawienia
   - Pobierz zestaw danych lokalnie w klastrze Spark
   - Konwertowanie zestawu danych na RDD
- Wynik obrazów za pomocą uczonego modelu kognitywnych Toolkit
   - Pobierz trenowanego modelu kognitywnych zestawu narzędzi do klastra Spark
   - Definiowanie funkcji, które mają być używane przez węzłów procesu roboczego
   - Wynik obrazów na węzłów procesu roboczego
   - Ocena dokładności modelu


## <a name="install-microsoft-cognitive-toolkit"></a>Zainstaluj kognitywnych zestaw narzędzi firmy Microsoft

Kognitywnych zestaw narzędzi firmy Microsoft można zainstalować w klastrze Spark przy użyciu akcji skryptu. Akcja skryptu używa niestandardowych skryptów do zainstalowania składników w klastrze, które nie są domyślnie dostępne. Przy użyciu zestawu .NET SDK usługi HDInsight lub przy użyciu programu Azure PowerShell, można użyć niestandardowego skryptu z portalu Azure. Można także użyć skryptu do zainstalowania zestawu narzędzi albo w ramach tworzenia klastra, lub gdy klaster jest uruchomiona. 

W tym artykule używamy portalu do zainstalowania zestawu narzędzi, po utworzeniu klastra. Aby inne sposoby uruchamiania skryptu niestandardowego, zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Korzystanie z portalu Azure

Aby uzyskać instrukcje na temat korzystania z portalu Azure do uruchomienia akcji skryptu, zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Upewnij się, że należy podać następujące dane wejściowe, aby zainstalować kognitywnych zestaw narzędzi firmy Microsoft.

* Podaj wartość dla nazwy akcji skryptu.

* Aby uzyskać **Bash skryptu URI**, wprowadź `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Upewnij się, uruchom skrypt tylko w przypadku węzłów head i proces roboczy i usuń zaznaczenie wszystkich innych pól wyboru.

* Kliknij przycisk **Utwórz**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Przekaż notesu Jupyter do klastra Spark w usłudze HDInsight Azure

Aby użyć kognitywnych zestaw narzędzi firmy Microsoft z klastrem usługi Azure HDInsight Spark, należy załadować notesu Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** do klastra usługi Azure HDInsight Spark. Ten notes jest dostępna w witrynie GitHub pod [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Klonowanie repozytorium GitHub [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Aby uzyskać instrukcje dotyczące klonowania, zobacz [klonowanie repozytorium](https://help.github.com/articles/cloning-a-repository/).

2. Korzystając z portalu Azure Otwórz bloku klastra Spark, który już zainicjowano obsługę administracyjną, kliknij **pulpit nawigacyjny klastra**, a następnie kliknij przycisk **notesu Jupyter**.

    Można również uruchomić notesu Jupyter, przechodząc do adresu URL `https://<clustername>.azurehdinsight.net/jupyter/`. Zastąp \<clustername > o nazwie z klastrem usługi HDInsight.

3. Kliknij notesu Jupyter **przekazać** w prawym górnym rogu, a następnie przejdź do lokalizacji, w którym sklonować repozytorium GitHub.

    ![Przekaż notesu Jupyter do klastra Spark w usłudze HDInsight Azure](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "notesu Jupyter przekazać do usługi Azure HDInsight Spark klastra")

4. Kliknij przycisk **przekazać** ponownie.

5. Po przekazaniu notesu kliknij nazwę notesu, a następnie postępuj zgodnie z instrukcjami w notesie dotyczące ładowania zestawu danych i wykonywać samouczka.

## <a name="see-also"></a>Zobacz także
* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Spark i analiza biznesowa: interakcyjna analiza danych na platformie Spark w usłudze HDInsight z użyciem narzędzi do analizy biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analiza danych telemetrycznych usługi Application Insight przy użyciu platformy Spark w usłudze HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
