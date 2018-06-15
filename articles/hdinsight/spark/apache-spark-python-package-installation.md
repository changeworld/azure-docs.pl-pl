---
title: Skrypt Akcja — Python Zainstaluj pakiety z Jupyter w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Instrukcje krok po kroku skonfigurować dostępne notesów Jupyter z klastrami Spark w usłudze HDInsight przy użyciu akcji skryptu do używania pakietów języka python zewnętrznych.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21978b71-eb53-480b-a3d1-c5d428a7eb5b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 4d9d1e0aaf6a1c0155f9ab74a5e63302635a0c11
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517413"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Aby zainstalować pakiety języka Python zewnętrzne notesów Jupyter w klastrach Apache Spark w usłudze HDInsight, użyj akcji skryptu
> [!div class="op_single_selector"]
> * [Przy użyciu magic komórki](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Za pomocą akcji skryptu](apache-spark-python-package-installation.md)
>
>

Dowiedz się, jak użyć akcji skryptu do skonfigurowania klastra Apache Spark w usłudze HDInsight (Linux), do korzystania z zewnętrznego, przyczyniły się społeczności **python** pakiety, które nie są uwzględnione w poziomie pole w klastrze.

> [!NOTE]
> Można również skonfigurować za pomocą notesu Jupyter `%%configure` magic na korzystanie z zewnętrznych pakietów. Aby uzyskać instrukcje, zobacz [korzystanie z zewnętrznych pakietów z notesami Jupyter w klastrach Apache Spark w usłudze HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).
> 
> 

Możesz przeszukać [indeksu pakietów](https://pypi.python.org/pypi) Aby uzyskać pełną listę pakietów, które są dostępne. Można również uzyskać listę dostępnych pakietów z innych źródeł. Na przykład można zainstalować pakietów dostępne za pośrednictwem [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) lub [conda forge](https://conda-forge.org/feedstocks/).

W tym artykule dowiesz się jak zainstalować [TensorFlow](https://www.tensorflow.org/) pakietu przy użyciu akcji skryptu w klastrze i używać go za pomocą notesu Jupyter.

## <a name="prerequisites"></a>Wymagania wstępne
Należy dysponować następującymi elementami:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [klastrów utworzyć Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Jeśli nie masz już klastra Spark w usłudze HDInsight w systemie Linux, można uruchomić akcji skryptu podczas tworzenia klastra. Odwiedź stronę dokumentacji na [sposób użycia akcji skryptu niestandardowego](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 

## <a name="use-external-packages-with-jupyter-notebooks"></a>Korzystanie z zewnętrznych pakietów z notesami Jupyter

1. W [Portalu Azure](https://portal.azure.com/) na tablicy startowej kliknij kafelek klastra Spark (jeśli został przypięty do tablicy startowej). Możesz także przejść do klastra, wybierając polecenia **Przeglądaj wszystko** > **Klastry usługi HDInsight**.   

2. W bloku klastra Spark kliknij **akcji skryptu** w lewym okienku. Uruchomienie akcji niestandardowej, która instaluje TensorFlow węzłów głównych i węzłów procesu roboczego. Skrypt bash mogą być przywoływane z: https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh można znaleźć w dokumentacji na [sposób użycia akcji skryptu niestandardowego](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > Istnieją dwa python instalacje w klastrze. Platforma Spark użyje instalacji języka python Anaconda znajdujący się w `/usr/bin/anaconda/bin`. Odwołanie tę instalację w akcje niestandardowe za pośrednictwem `/usr/bin/anaconda/bin/pip` i `/usr/bin/anaconda/bin/conda`.
   > 
   > 

3. Otwórz notesu PySpark Jupyter

    ![Tworzenie nowego notesu Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Tworzenie nowego notesu Jupyter")

4. Zostanie utworzony i otwarty nowy notes o nazwie Untitled.pynb. Kliknij nazwę notesu u góry, a następnie wprowadź przyjazną nazwę.

    ![Wprowadzanie nazwy notesu](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Wprowadzanie nazwy notesu")

5. Możesz teraz będzie `import tensorflow` i uruchom przykład Witaj świecie. 

    Kod do skopiowania:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    Wynik wygląda następująco:
    
    ![Wykonanie kodu TensorFlow](./media/apache-spark-python-package-installation/execution.png "TensorFlow wykonanie kodu")

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
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter w klastrach Apache Spark w usłudze HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
