---
title: Akcja skryptu — instalowanie pakietów języka Python z rozwiązaniem Jupyter w usłudze Azure HDInsight
description: Instrukcje krok po kroku dotyczące konfigurowania notesów programu Jupyter dostępne przy użyciu klastrów HDInsight Spark przy użyciu akcji skryptu za pomocą pakietów zewnętrznych języka python.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: jasonh
ms.openlocfilehash: 36e727a59b91303c8c62c5525f72c328e2792ad6
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619179"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Użyj akcji skryptu, aby zainstalować zewnętrznych pakietów języka Python dla notesów programu Jupyter w klastrach platformy Apache Spark w HDInsight
> [!div class="op_single_selector"]
> * [Przy użyciu funkcji cell magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Za pomocą akcji skryptu](apache-spark-python-package-installation.md)
>
>

Dowiedz się, jak użyć akcji skryptu, aby skonfigurować klaster Apache Spark w HDInsight (Linux), aby używać zewnętrznych, przez społeczność **python** pakiety, które nie są uwzględnione poza pole w klastrze.

> [!NOTE]
> Można również skonfigurować za pomocą notesu Jupyter `%%configure` magic Aby korzystanie z zewnętrznych pakietów. Aby uzyskać instrukcje, zobacz [korzystanie z zewnętrznych pakietów z notesami Jupyter w klastrach platformy Apache Spark w HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).
> 
> 

Możesz wyszukiwać [indeksu pakietów](https://pypi.python.org/pypi) uzyskać pełną listę pakietów, które są dostępne. Możesz również uzyskać listę dostępnych pakietów z innych źródeł. Na przykład, można zainstalować pakiety udostępniane za pośrednictwem [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) lub [conda forge](https://conda-forge.org/feedstocks/).

W tym artykule dowiesz się, jak zainstalować [TensorFlow](https://www.tensorflow.org/) pakietu przy użyciu akcji skryptów w klastrze i przy jego użyciu za pomocą notesu Jupyter.

## <a name="prerequisites"></a>Wymagania wstępne
Należy dysponować następującymi elementami:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Jeśli nie masz już klaster Spark w HDInsight w systemie Linux, możesz uruchomić akcji skryptów podczas tworzenia klastra. Odwiedź stronę dokumentacji na [sposób użyć niestandardowego skryptu akcji](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 

## <a name="use-external-packages-with-jupyter-notebooks"></a>Korzystanie z zewnętrznych pakietów z notesami Jupyter

1. W [Portalu Azure](https://portal.azure.com/) na tablicy startowej kliknij kafelek klastra Spark (jeśli został przypięty do tablicy startowej). Możesz także przejść do klastra, wybierając polecenia **Przeglądaj wszystko** > **Klastry usługi HDInsight**.   

2. W bloku klastra Spark kliknij **akcji skryptu** z okienka po lewej stronie. Uruchom niestandardową akcję, która instaluje TensorFlow w węzły główne i węzły procesu roboczego. Skrypt powłoki bash, mogą być przywoływane z: https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh odwiedź stronę dokumentacji na [sposób użyć niestandardowego skryptu akcji](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > Istnieją dwa python instalacji w klastrze. Spark użyje Anaconda instalację języka python, znajdujący się w `/usr/bin/anaconda/bin`. Odwoływać się do tej instalacji w akcje niestandardowe za pomocą `/usr/bin/anaconda/bin/pip` i `/usr/bin/anaconda/bin/conda`.
   > 
   > 

3. Otwieranie notesu programu PySpark Jupyter

    ![Tworzenie nowego notesu Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Tworzenie nowego notesu Jupyter")

4. Zostanie utworzony i otwarty nowy notes o nazwie Untitled.pynb. Kliknij nazwę notesu u góry, a następnie wprowadź przyjazną nazwę.

    ![Wprowadzanie nazwy notesu](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Wprowadzanie nazwy notesu")

5. Teraz należy `import tensorflow` i uruchomić przykład Witaj świecie. 

    Skopiuj kod:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    Wynik wygląda następująco:
    
    ![Wykonywanie kodu TensorFlow](./media/apache-spark-python-package-installation/execution.png "TensorFlow wykonywanie kodu")

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
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter w klastrach platformy Apache Spark w HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
