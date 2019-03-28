---
title: Akcja skryptu — instalowanie pakietów języka Python z rozwiązaniem Jupyter w usłudze Azure HDInsight
description: Instrukcje krok po kroku dotyczące konfigurowania notesów programu Jupyter dostępne przy użyciu klastrów HDInsight Spark przy użyciu akcji skryptu za pomocą pakietów zewnętrznych języka python.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: hrasheed
ms.openlocfilehash: 8bc44949d804349de37796a2695edbdc64693edf
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518681"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Użyj akcji skryptu, aby zainstalować zewnętrznych pakietów języka Python dla notesów programu Jupyter w klastrach platformy Apache Spark w HDInsight
> [!div class="op_single_selector"]
> * [Przy użyciu funkcji cell magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Za pomocą akcji skryptu](apache-spark-python-package-installation.md)

Dowiedz się, jak skonfigurować przy użyciu akcji skryptu [platformy Apache Spark](https://spark.apache.org/) klastra w systemie HDInsight do korzystania z zewnętrznego, przez społeczność **python** pakiety, które nie są uwzględnione poza pole w klastrze.

> [!NOTE]  
> Można również skonfigurować za pomocą notesu Jupyter `%%configure` magic Aby korzystanie z zewnętrznych pakietów. Aby uzyskać instrukcje, zobacz [korzystanie z zewnętrznych pakietów z notesami Jupyter w klastrach platformy Apache Spark w HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).

Możesz wyszukiwać [indeksu pakietów](https://pypi.python.org/pypi) uzyskać pełną listę pakietów, które są dostępne. Możesz również uzyskać listę dostępnych pakietów z innych źródeł. Na przykład, można zainstalować pakiety udostępniane za pośrednictwem [conda forge](https://conda-forge.org/feedstocks/).

W tym artykule dowiesz się, jak zainstalować [TensorFlow](https://www.tensorflow.org/) pakietu przy użyciu akcji skryptów w klastrze i korzystać z niego za pomocą notesu Jupyter jako przykład.

## <a name="prerequisites"></a>Wymagania wstępne
Należy dysponować następującymi elementami:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Jeśli nie masz już klaster Spark w HDInsight w systemie Linux, możesz uruchomić akcji skryptów podczas tworzenia klastra. Odwiedź stronę dokumentacji na [sposób użyć niestandardowego skryptu akcji](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Pomoc techniczna dotycząca oprogramowania typu open-source używane w klastrach HDInsight

Usługa Microsoft Azure HDInsight korzysta z ekosystemu technologii open source utworzona wokół Apache Hadoop. Microsoft Azure oferuje ogólnego poziomu pomocy technicznej dla technologii open source. Aby uzyskać więcej informacji, zobacz **zakres pomocy technicznej** części [witryny sieci Web — często zadawane pytania dla pomocy technicznej Azure](https://azure.microsoft.com/support/faq/). Usługa HDInsight zapewnia dodatkowy poziom pomocy technicznej dla wbudowanych składników.

Istnieją dwa typy składników typu open source, które są dostępne w usłudze HDInsight:

* **Wbudowane składniki** — te składniki są wstępnie zainstalowane w klastrach HDInsight i zapewnia podstawowe funkcje klastra. Na przykład Apache Hadoop YARN ResourceManager, język zapytań technologii Hive (HiveQL) i biblioteki mahout dostępnych należą do tej kategorii. Pełną listę składniki klastra jest dostępna w [nowości w wersjach klastra platformy Apache Hadoop, dostarczone przez HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Składniki niestandardowe** —, jako użytkownik klastra, można zainstalować lub użyj w obciążenia dowolny składnik, dostępne w społeczności lub utworzonej przez użytkownika.

> [!IMPORTANT]   
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane. Microsoft Support pomaga wyizolować i rozwiązać problemy związane z tych składników.
>
> Składniki niestandardowe otrzymują uzasadnioną komercyjnie pomoc techniczną, aby pomóc rozwiązać ten problem. Pomoc techniczna firmy Microsoft może rozwiązać ten problem, lub ich może poprosić o angażowanie dostępne kanały dla technologii "open source", gdzie znajduje się specjalistyczna dla tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takie jak: [Forum MSDN dotyczące HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Projektów Apache mieć witryny projektu na [ https://apache.org ](https://apache.org), na przykład: [Hadoop](https://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Korzystanie z zewnętrznych pakietów z notesami Jupyter

1. Z [witryny Azure portal](https://portal.azure.com/), przejdź do klastra.  

2. Z klastrem usługi wybrane w lewym okienku w obszarze **ustawienia**, wybierz opcję **akcji skryptu**.

3. Wybierz **+ Prześlij nowe**.

4. Wprowadź następujące wartości w polach **Prześlij akcję skryptu** okna:  


    |Parametr | Wartość |
    |---|---|
    |Typ skryptu | Wybierz **- niestandardowa** z listy rozwijanej.|
    |Name (Nazwa) |Wprowadź `tensorflow` w polu tekstowym.|
    |Identyfikator URI skryptu powłoki systemowej |Wprowadź `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh` w polu tekstowym. |
    |Typy węzłów | Wybierz **Head**, i **procesu roboczego** pola wyboru. |

    `tensorflowinstall.sh` zawiera następujące polecenia:

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install -c conda-forge tensorflow
    ```

5. Wybierz pozycję **Utwórz**.  Odwiedź stronę dokumentacji na [sposób użyć niestandardowego skryptu akcji](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

6. Poczekaj na wykonanie skryptu.  **Akcji skryptu** okienko będzie zawierać informację **nowe akcje skryptów można przesłać po zakończeniu bieżącej operacji klastra** podczas wykonywania skryptu.  Pasek postępu, mogą być wyświetlane w interfejsie użytkownika Ambari **operacji w tle** okna.

7. Otwieranie notesu programu PySpark Jupyter.  Zobacz [tworzenie notesu Jupyter w HDInsight Spark](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) kroków.

    ![Tworzenie nowego notesu Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Tworzenie nowego notesu Jupyter")

8. Teraz należy `import tensorflow` i uruchomić przykład Witaj świecie. Wprowadź następujący kod:

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    Wynik wygląda następująco:
    
    ![Wykonywanie kodu TensorFlow](./media/apache-spark-python-package-installation/execution.png "TensorFlow wykonywanie kodu")

> [!NOTE]  
> Istnieją dwa python instalacji w klastrze. Spark użyje Anaconda instalację języka python, znajdujący się w `/usr/bin/anaconda/bin` i przywróci domyślną wartość dla środowiska Python 2.7. Aby użyć pakietów języka Python 3.x i instalowanie w jądrze PySpark3, użyj ścieżki do `conda` plik wykonywalny dla tego środowiska i użycia `-n` parametru, aby określić środowisko. Na przykład polecenie `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35`, instaluje `ggplot` pakietu za pomocą środowiska Python 3.5 `conda-forge` kanału.

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
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter w klastrach platformy Apache Spark w HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji platformy Apache Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
