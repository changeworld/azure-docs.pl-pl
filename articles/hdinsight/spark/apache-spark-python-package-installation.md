---
title: Akcja skryptu dla pakietów języka Python z programem Jupyter w usłudze Azure HDInsight
description: Instrukcje krok po kroku dotyczące konfigurowania notesów Jupyter dostępnych z klastrami usługi HDInsight Spark w celu korzystania z zewnętrznych pakietów języka Python.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: f80486758152c002762bbddd6ae97a2ce9468ccf
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241516"
---
# <a name="script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-on-hdinsight"></a>Akcja skryptu służąca do instalowania zewnętrznych pakietów języka Python dla notesów Jupyter w Apache Spark w usłudze HDInsight

> [!div class="op_single_selector"]
> * [Korzystanie z komórki Magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Używanie akcji skryptu](apache-spark-python-package-installation.md)

Dowiedz się, jak za pomocą akcji skryptu skonfigurować klaster [Apache Spark](https://spark.apache.org/) w usłudze HDInsight, aby korzystał z zewnętrznych, współtworzonych przez społeczność pakietów języka **Python** , które nie zostały dołączone do klastra.

> [!NOTE]  
> Notes Jupyter można również skonfigurować za pomocą polecenia `%%configure` Magic, aby używać pakietów zewnętrznych. Aby uzyskać instrukcje, zobacz [Korzystanie z zewnętrznych pakietów z notesami Jupyter w klastrach Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)w usłudze HDInsight.

Aby uzyskać pełną listę dostępnych pakietów, można wyszukać w [indeksie pakietu](https://pypi.python.org/pypi) . Możesz również uzyskać listę dostępnych pakietów z innych źródeł. Można na przykład zainstalować pakiety udostępniane za poorednictwem [Conda-fałszerstwa](https://conda-forge.org/feedstocks/).

W tym artykule dowiesz się, jak zainstalować pakiet [TensorFlow](https://www.tensorflow.org/) przy użyciu akcji skryptu w klastrze i użyć go za pośrednictwem notesu Jupyter.

## <a name="prerequisites"></a>Wymagania wstępne
Wymagane są następujące elementy:

* Subskrypcja platformy Azure. Zobacz artykuł [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Jeśli nie masz jeszcze klastra Spark w usłudze HDInsight w systemie Linux, możesz uruchamiać akcje skryptu podczas tworzenia klastra. Zapoznaj się z dokumentacją dotyczącą [korzystania z niestandardowych akcji skryptu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Obsługa oprogramowania Open Source używanego w klastrach usługi HDInsight

Usługa Microsoft Azure HDInsight używa ekosystemu technologii typu "open source" utworzonych wokół Apache Hadoop. Microsoft Azure zapewnia ogólny poziom wsparcia dla technologii typu open source. Aby uzyskać więcej informacji, zobacz sekcję **zakres pomocy technicznej** w [witrynie sieci Web pomocy technicznej dotyczącej platformy Azure](https://azure.microsoft.com/support/faq/). Usługa HDInsight zapewnia dodatkowy poziom wsparcia dla wbudowanych składników programu.

Istnieją dwa typy składników typu "open source", które są dostępne w usłudze HDInsight:

* **Składniki wbudowane** — te składniki są wstępnie zainstalowane w klastrach usługi HDInsight i zapewniają podstawowe funkcje klastra. Na przykład Apache Hadoopj ResourceManager, Apache Hive języka zapytań (HiveQL) i biblioteki Mahout należy do tej kategorii. Pełna lista składników klastra jest dostępna w temacie [co nowego w wersjach Apache Hadoop klastra dostarczonych przez usługi HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Składniki niestandardowe** — użytkownik, który jest użytkownikiem klastra, może instalować lub używać w obciążeniu dowolny składnik dostępny w społeczności lub utworzony przez użytkownika.

> [!IMPORTANT]   
> Składniki dostarczane z klastrem usługi HDInsight są w pełni obsługiwane. Pomoc techniczna firmy Microsoft pomaga wyizolować i rozwiązać problemy związane z tymi składnikami.
>
> Składniki niestandardowe otrzymują komercyjnie uzasadnioną pomoc techniczną, która ułatwia dalsze Rozwiązywanie problemu. Pomoc techniczna firmy Microsoft może być w stanie rozwiązać ten problem lub zażądać dostępnych kanałów dla technologii open source, w których znajduje się Szczegółowa wiedza dotycząca tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takich jak: [forum MSDN dla usługi HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Również projekty Apache mają witryny projektu na [https://apache.org](https://apache.org), na przykład: [Hadoop](https://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Korzystanie z zewnętrznych pakietów z notesami Jupyter

1. W [Azure Portal](https://portal.azure.com/)przejdź do klastra.  

2. Po wybraniu klastra w lewym okienku w obszarze **Ustawienia**wybierz pozycję **Akcje skryptu**.

3. Wybierz pozycję **+ Prześlij nowy**.

4. Wprowadź następujące wartości w oknie **Akcja przesyłania skryptu** :  


    |Parametr | Wartość |
    |---|---|
    |Typ skryptu | Z listy rozwijanej wybierz pozycję **— niestandardowa** .|
    |Nazwa |Wprowadź `tensorflow` w polu tekstowym.|
    |Identyfikator URI skryptu bash |Wprowadź `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh` w polu tekstowym. |
    |Typy węzłów | Zaznacz pola wyboru **nagłówek**i **proces roboczy** . |

    `tensorflowinstall.sh` zawiera następujące polecenia:

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install --yes tensorflow
    ```

5. Wybierz pozycję **Utwórz**.  Zapoznaj się z dokumentacją dotyczącą [korzystania z niestandardowych akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

6. Poczekaj na zakończenie skryptu.  W okienku **Akcje skryptu** będzie **można przesłać nowe akcje skryptu po zakończeniu bieżącej operacji klastra** podczas wykonywania skryptu.  Pasek postępu można wyświetlić w oknie **operacje w tle** interfejsu użytkownika Ambari.

7. Otwórz Notes programu PySpark Jupyter.  Aby uzyskać instrukcje, zobacz [Tworzenie notesu Jupyter w usłudze Spark HDInsight](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) .

    ![Tworzenie nowego notesu Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Tworzenie nowego notesu Jupyter")

8. Teraz `import tensorflow` i uruchomimy przykład Hello World. Wprowadź następujący kod:

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    Wynik będzie wyglądać następująco:
    
    ![TensorFlow wykonywanie kodu](./media/apache-spark-python-package-installation/tensorflow-execution.png "Wykonaj kod TensorFlow")

> [!NOTE]  
> W klastrze są zainstalowane dwie instalacje języka Python. Platforma Spark będzie używać instalacji języka Python Anaconda znajdującej się w `/usr/bin/anaconda/bin` i domyślnie będzie środowiskiem Python 2,7. Aby użyć języka Python 3. x i zainstalować pakiety w jądrze PySpark3, użyj ścieżki do pliku wykonywalnego `conda` dla tego środowiska i użyj parametru `-n` do określenia środowiska. Na przykład polecenie `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35`instaluje pakiet `ggplot` do środowiska Python 3,5 przy użyciu kanału `conda-forge`.

## <a name="seealso"></a>Zobacz też
* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Apache Spark z usługą BI: wykonywanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter w klastrach Apache Spark w usłudze HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki narzędzi HDInsight do IntelliJ pomysł, aby debugować aplikacje Apache Spark zdalnie](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
