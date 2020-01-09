---
title: Akcja skryptu dla pakietów języka Python z programem Jupyter w usłudze Azure HDInsight
description: Instrukcje krok po kroku dotyczące konfigurowania notesów Jupyter dostępnych z klastrami usługi HDInsight Spark w celu korzystania z zewnętrznych pakietów języka Python.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 109ac20d8a3d3dc87b4a83165c0e6c24808c1340
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529647"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Bezpieczne zarządzanie środowiskiem Python w usłudze Azure HDInsight za pomocą akcji skryptu

> [!div class="op_single_selector"]
> * [Korzystanie z komórki Magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Używanie akcji skryptu](apache-spark-python-package-installation.md)

Usługa HDInsight ma dwie wbudowane instalacje języka Python w klastrze Spark, Anaconda Python 2,7 i Python 3,5. W niektórych przypadkach klienci muszą dostosować środowisko Python, na przykład instalując zewnętrzne pakiety Python lub inną wersję języka Python. W tym artykule przedstawiono najlepsze rozwiązanie bezpiecznego zarządzania środowiskami języka Python dla klastra [Apache Spark](https://spark.apache.org/) w usłudze HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Jeśli nie masz jeszcze klastra Spark w usłudze HDInsight w systemie Linux, możesz uruchamiać akcje skryptu podczas tworzenia klastra. Zapoznaj się z dokumentacją dotyczącą [korzystania z niestandardowych akcji skryptu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Obsługa oprogramowania Open Source używanego w klastrach usługi HDInsight

Usługa Microsoft Azure HDInsight używa ekosystemu technologii typu "open source" utworzonych wokół Apache Hadoop. Microsoft Azure zapewnia ogólny poziom wsparcia dla technologii typu open source. Aby uzyskać więcej informacji, zobacz [witrynę sieci Web pomocy technicznej dla systemu Azure](https://azure.microsoft.com/support/faq/). Usługa HDInsight zapewnia dodatkowy poziom wsparcia dla wbudowanych składników programu.

Istnieją dwa typy składników typu "open source", które są dostępne w usłudze HDInsight:

* **Składniki wbudowane** — te składniki są wstępnie zainstalowane w klastrach usługi HDInsight i zapewniają podstawowe funkcje klastra. Na przykład, Apache Hadoop Menedżer zasobów PRZĘDZenia, Apache Hive języka zapytań (HiveQL) i Biblioteka Mahout należy do tej kategorii. Pełna lista składników klastra jest dostępna w temacie [co nowego w wersjach Apache Hadoop klastra dostarczonych przez usługi HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Składniki niestandardowe** — użytkownik, który jest użytkownikiem klastra, może instalować lub używać w obciążeniu dowolny składnik dostępny w społeczności lub utworzony przez użytkownika.

> [!IMPORTANT]
> Składniki dostarczane z klastrem usługi HDInsight są w pełni obsługiwane. Pomoc techniczna firmy Microsoft pomaga wyizolować i rozwiązać problemy związane z tymi składnikami.
>
> Składniki niestandardowe otrzymują komercyjnie uzasadnioną pomoc techniczną, która ułatwia dalsze Rozwiązywanie problemu. Pomoc techniczna firmy Microsoft może być w stanie rozwiązać ten problem lub zażądać dostępnych kanałów dla technologii open source, w których znajduje się Szczegółowa wiedza dotycząca tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takich jak: [forum MSDN dla usługi HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Również projekty Apache mają witryny projektu na [https://apache.org](https://apache.org), na przykład: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Opis domyślnej instalacji języka Python

Klaster usługi HDInsight Spark jest tworzony z instalacją Anaconda. W klastrze są zainstalowane dwie instalacje języka Python, Anaconda Python 2,7 i Python 3,5. W poniższej tabeli przedstawiono domyślne ustawienia języka Python dla platformy Spark, usługi Livy i Jupyter.

| |Python 2.7|Python 3,5|
|----|----|----|
|Ścieżka|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Domyślnie ustawiona na 2,7|ND|
|Livy|Domyślnie ustawiona na 2,7|ND|
|Jupyter|Jądro PySpark|Jądro PySpark3|

## <a name="safely-install-external-python-packages"></a>Bezpieczne Instalowanie zewnętrznych pakietów języka Python

Klaster usługi HDInsight zależy od wbudowanego środowiska języka Python, zarówno Python 2,7, jak i Python 3,5. Bezpośrednie Instalowanie pakietów niestandardowych w tych domyślnych środowiskach wbudowanych może spowodować nieoczekiwane zmiany wersji biblioteki i zwiększyć klaster. Aby bezpiecznie instalować niestandardowe zewnętrzne pakiety języka Python dla aplikacji platformy Spark, wykonaj poniższe czynności.

1. Utwórz środowisko wirtualne języka Python za pomocą Conda. Środowisko wirtualne zapewnia izolowane miejsce dla projektów bez przerywania pracy. Podczas tworzenia środowiska wirtualnego języka Python można określić wersję języka Python, która ma być używana. Należy pamiętać, że nadal konieczne jest utworzenie środowiska wirtualnego, nawet jeśli chcesz użyć języka Python 2,7 i 3,5. Jest to konieczne, aby upewnić się, że środowisko domyślne klastra nie zostanie zerwane. Uruchom akcje skryptu w klastrze dla wszystkich węzłów z poniższym skryptem, aby utworzyć środowisko wirtualne języka Python. 

    -   `--prefix` określa ścieżkę, w której przebywa środowisko wirtualne Conda. Istnieje kilka konfiguracji, które należy zmienić na podstawie określonej tutaj ścieżki. W tym przykładzie używamy py35new, ponieważ klaster ma już istniejące środowisko wirtualne o nazwie py35.
    -   `python=` określa wersję języka Python dla środowiska wirtualnego. W tym przykładzie używamy wersji 3,5, która jest taka sama jak w przypadku klastra skompilowanego w jednym z nich. Do utworzenia środowiska wirtualnego można także użyć innych wersji języka Python.
    -   `anaconda` określa package_spec jako Anaconda do instalowania pakietów Anaconda w środowisku wirtualnym.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. W razie konieczności Zainstaluj zewnętrzne pakiety języka Python w utworzonym środowisku wirtualnym. Uruchom akcje skryptu w klastrze dla wszystkich węzłów z poniższym skryptem, aby zainstalować zewnętrzne pakiety języka Python. Musisz mieć tutaj uprawnienie sudo, aby zapisywać pliki w folderze środowiska wirtualnego.

    Aby uzyskać pełną listę dostępnych pakietów, można wyszukać w [indeksie pakietu](https://pypi.python.org/pypi) . Możesz również uzyskać listę dostępnych pakietów z innych źródeł. Można na przykład zainstalować pakiety udostępniane za poorednictwem [Conda-fałszerstwa](https://conda-forge.org/feedstocks/).

    -   `seaborn` to nazwa pakietu, który chcesz zainstalować.
    -   `-n py35new` określić nazwę środowiska wirtualnego, która właśnie zostanie utworzona. Należy koniecznie zmienić nazwę odpowiadającą tworzeniu środowiska wirtualnego.

    ```bash
    sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
    ```

    Jeśli nie znasz nazwy środowiska wirtualnego, możesz używać protokołu SSH do węzła głównego klastra i uruchamiać `/usr/bin/anaconda/bin/conda info -e`, aby wyświetlić wszystkie środowiska wirtualne.

3. Zmień konfigurację platformy Spark i usługi Livy, a następnie wskaż utworzone środowisko wirtualne.

    1. Otwórz interfejs użytkownika Ambari, przejdź do strony Spark2 na karcie konfiguracje.
    
        ![Zmień konfigurację platformy Spark i usługi Livy na Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)
 
    2. Rozwiń pozycję Advanced livy2-ENV, Dodaj poniższe instrukcje na dole. Jeśli środowisko wirtualne zostało zainstalowane z innym prefiksem, należy odpowiednio zmienić ścieżkę.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Zmień konfigurację usługi Livy w Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Rozwiń pozycję Advanced spark2-ENV, Zastąp istniejącą instrukcję Export PYSPARK_PYTHON w dolnej części. Jeśli środowisko wirtualne zostało zainstalowane z innym prefiksem, należy odpowiednio zmienić ścieżkę.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Zmień konfigurację platformy Spark za poorednictwem Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Zapisz zmiany i ponownie uruchom usługi, których to dotyczy. Te zmiany wymagają ponownego uruchomienia usługi Spark2. Interfejs użytkownika Ambari wyświetli monit o wymagane przypomnienie ponownego uruchomienia, kliknij przycisk Uruchom ponownie, aby ponownie uruchomić wszystkie usługi, których to dotyczy.

        ![Zmień konfigurację platformy Spark za poorednictwem Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)
 
4.  Jeśli chcesz użyć nowego utworzonego środowiska wirtualnego w Jupyter. Należy zmienić konfiguracje Jupyter i ponownie uruchomić Jupyter. Uruchom akcje skryptu na wszystkich węzłach nagłówka z instrukcją poniżej, aby wskazać Jupyter nowe środowisko wirtualne. Należy zmienić ścieżkę na prefiks określony dla środowiska wirtualnego. Po uruchomieniu tej akcji skryptu należy ponownie uruchomić usługę Jupyter za pomocą interfejsu użytkownika Ambari w celu udostępnienia tej zmiany.

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Możesz dwukrotnie potwierdzić środowisko Python w Jupyter Notebook, uruchamiając Poniższy kod:

    ![Sprawdź wersję języka Python w Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Znany problem

Istnieje znana usterka dla Anaconda w wersji 4.7.11 i 4.7.12. Jeśli zobaczysz akcje skryptu zawieszające się w `"Collecting package metadata (repodata.json): ...working..."` i kończy się niepowodzeniem z `"Python script has been killed due to timeout after waiting 3600 secs"`. Możesz pobrać [ten skrypt](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) i uruchomić go jako akcje skryptu na wszystkich węzłach, aby rozwiązać ten problem.

Aby sprawdzić wersję programu Anaconda, można używać protokołu SSH w węźle nagłówka klastra i uruchamiać `/usr/bin/anaconda/bin/conda --v`.

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
