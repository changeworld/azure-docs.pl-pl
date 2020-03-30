---
title: Akcja skryptu dla pakietów języka Python z programem Jupyter w usłudze Azure HDInsight
description: Instrukcje krok po kroku dotyczące używania akcji skryptu do konfigurowania notesów Jupyter dostępnych w klastrach platformy HDInsight Spark do używania zewnętrznych pakietów języka Python.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 659af8b85cb3736d663e79676b04af8041aeabfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129575"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Bezpieczne zarządzanie środowiskiem Python w usłudze Azure HDInsight za pomocą akcji skryptu

> [!div class="op_single_selector"]
> * [Korzystanie z magii komórek](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Korzystanie z akcji skryptu](apache-spark-python-package-installation.md)

HDInsight ma dwie wbudowane instalacje Języka Python w klastrze Spark, Anaconda Python 2.7 i Python 3.5. W niektórych przypadkach klienci muszą dostosować środowisko Języka Python, na przykład instalowanie zewnętrznych pakietów Pythona lub innej wersji Języka Python. W tym artykule pokazujemy najlepsze rozwiązania bezpiecznego zarządzania środowiskami języka Python dla klastra [Platformy Spark Apache](./apache-spark-overview.md) w programie HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md). Jeśli nie masz jeszcze klastra Platformy Spark w programie HDInsight, można uruchomić akcje skryptu podczas tworzenia klastra. Zapoznaj się z dokumentacją [dotyczącą używania akcji skryptu niestandardowego](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Obsługa oprogramowania typu open source używanego w klastrach HDInsight

Usługa Microsoft Azure HDInsight korzysta z ekosystemu technologii open source utworzonych wokół Apache Hadoop. Platforma Microsoft Azure zapewnia ogólny poziom obsługi technologii typu open source. Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq/). Usługa HDInsight zapewnia dodatkowy poziom obsługi wbudowanych składników.

Istnieją dwa typy składników typu open source, które są dostępne w usłudze HDInsight:

|Składnik |Opis |
|---|---|
|Wbudowane|Te składniki są wstępnie zainstalowane w klastrach HDInsight i zapewniają podstawowe funkcje klastra. Na przykład Apache Hadoop YARN Resource Manager, Apache Hive query language (HiveQL) i Biblioteka Mahout należą do tej kategorii. Pełna lista składników klastra jest dostępna w [co nowego w wersjach klastra Apache Hadoop dostarczonych przez HDInsight](../hdinsight-component-versioning.md).|
|Niestandardowy|Użytkownik, jako użytkownik klastra, można zainstalować lub używać w obciążeniu dowolnego składnika dostępnego w społeczności lub utworzonego przez użytkownika.|

> [!IMPORTANT]
> Komponenty dostarczane z klastrem HDInsight są w pełni obsługiwane. Pomoc techniczna firmy Microsoft pomaga wyizolować i rozwiązać problemy związane z tymi składnikami.
>
> Składniki niestandardowe otrzymują komercyjnie uzasadnione wsparcie, które pomoże Ci w dalszym rozwiązywaniu problemu. Pomoc techniczna firmy Microsoft może być w stanie rozwiązać problem LUB mogą poprosić o zaangażowanie dostępnych kanałów dla technologii open source, w których znajduje się głęboka wiedza specjalistyczna w zakresie tej technologii. Na przykład istnieje wiele witryn społecznościowych, z których można korzystać, [https://stackoverflow.com](https://stackoverflow.com)takich jak: [forum MSDN dla HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), . Również projekty Apache mają [https://apache.org](https://apache.org)miejsca projektu na , na przykład: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Opis domyślnej instalacji języka Python

Klaster HDInsight Spark jest tworzony z instalacją Anaconda. Istnieją dwie instalacje Pythona w klastrze, Anaconda Python 2.7 i Python 3.5. W poniższej tabeli przedstawiono domyślne ustawienia języka Python dla spark, Livy i Jupyter.

| |Python 2.7|Python 3.5|
|----|----|----|
|Ścieżka|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|platforma Spark|Domyślnie ustawiona na 2.7|Nie dotyczy|
|Livy|Domyślnie ustawiona na 2.7|Nie dotyczy|
|Jupyter|Jądro PySpark|Jądro PySpark3|

## <a name="safely-install-external-python-packages"></a>Bezpieczne instalowanie zewnętrznych pakietów Pythona

Klaster HDInsight zależy od wbudowanego środowiska Python, zarówno Python 2.7 i Python 3.5. Bezpośrednie instalowanie pakietów niestandardowych w tych domyślnych środowiskach wbudowanych może spowodować nieoczekiwane zmiany wersji biblioteki i spowodować dalsze przerwanie klastra. Aby bezpiecznie zainstalować niestandardowe zewnętrzne pakiety Python dla aplikacji Spark, wykonaj poniższe kroki.

1. Tworzenie środowiska wirtualnego języka Python przy użyciu conda. Środowisko wirtualne zapewnia izolowane miejsce dla projektów bez przerywania innych. Podczas tworzenia środowiska wirtualnego języka Python można określić wersję języka Python, której chcesz użyć. Należy pamiętać, że nadal trzeba utworzyć środowisko wirtualne, nawet jeśli chcesz używać Python 2.7 i 3.5. Ma to na celu upewnienie się, że domyślne środowisko klastra nie zostanie złamane. Uruchom akcje skryptu w klastrze dla wszystkich węzłów z poniższym skryptem, aby utworzyć środowisko wirtualne języka Python.

    -   `--prefix`określa ścieżkę, w której żyje środowisko wirtualne conda. Istnieje kilka configs, które muszą być zmienione dalej w oparciu o ścieżkę określoną w tym miejscu. W tym przykładzie używamy py35new, ponieważ klaster ma już istniejące środowisko wirtualne o nazwie py35.
    -   `python=`określa wersję języka Python dla środowiska wirtualnego. W tym przykładzie używamy wersji 3.5, tej samej wersji co klaster zbudowany w jednym. Można również użyć innych wersji języka Python do tworzenia środowiska wirtualnego.
    -   `anaconda`określa package_spec jako anakondę do instalowania pakietów Anaconda w środowisku wirtualnym.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. W razie potrzeby zainstaluj zewnętrzne pakiety języka Python w utworzonym środowisku wirtualnym. Uruchom akcje skryptu w klastrze dla wszystkich węzłów z poniższym skryptem, aby zainstalować zewnętrzne pakiety Języka Python. Musisz mieć sudo przywilej tutaj, aby zapisać pliki do folderu środowiska wirtualnego.

    Można przeszukać [indeks pakietu](https://pypi.python.org/pypi) dla pełnej listy pakietów, które są dostępne. Można również uzyskać listę dostępnych pakietów z innych źródeł. Na przykład można zainstalować pakiety udostępnione za pośrednictwem [conda-forge](https://conda-forge.org/feedstocks/).

    Użyj polecenia poniżej, jeśli chcesz zainstalować bibliotekę z najnowszą wersją:

    - Użyj kanału conda:

        -   `seaborn`to nazwa pakietu, którą chcesz zainstalować.
        -   `-n py35new`określić nazwę środowiska wirtualnego, który właśnie zostanie utworzony. Upewnij się, aby odpowiednio zmienić nazwę na podstawie tworzenia środowiska wirtualnego.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Lub użyj repozytorium `seaborn` PyPi, zmień i `py35new` odpowiednio:
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    Użyj polecenia poniżej, jeśli chcesz zainstalować bibliotekę z określoną wersją:

    - Użyj kanału conda:

        -   `numpy=1.16.1`to nazwa i wersja pakietu, którą chcesz zainstalować.
        -   `-n py35new`określić nazwę środowiska wirtualnego, który właśnie zostanie utworzony. Upewnij się, aby odpowiednio zmienić nazwę na podstawie tworzenia środowiska wirtualnego.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Lub użyj repozytorium `numpy==1.16.1` PyPi, zmień i `py35new` odpowiednio:

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    Jeśli nie znasz nazwy środowiska wirtualnego, możesz SSH do węzła `/usr/bin/anaconda/bin/conda info -e` głównego klastra i uruchomić, aby wyświetlić wszystkie środowiska wirtualne.

3. Zmień configs Spark i Livy i wskaż utworzone środowisko wirtualne.

    1. Otwórz interfejs użytkownika Ambari, przejdź do strony Spark2, karta Konfiguracje.

        ![Zmień spark i livy config przez Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Rozwiń Zaawansowane livy2-env, dodaj poniższe instrukcje na dole. Jeśli zainstalowano środowisko wirtualne z innym prefiksem, zmień odpowiednio ścieżkę.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Zmień livy config przez Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Rozwiń Zaawansowane spark2-env, zastąp istniejącą instrukcję PYSPARK_PYTHON eksportu na dole. Jeśli zainstalowano środowisko wirtualne z innym prefiksem, zmień odpowiednio ścieżkę.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Zmień config Spark przez Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Zapisz zmiany i uruchom ponownie usługi, których dotyczy problem. Te zmiany wymagają ponownego uruchomienia usługi Spark2. Interfejs użytkownika Ambari wyświetli monit o wymagane przypomnienie o ponownym uruchomieniu, kliknij przycisk Uruchom ponownie, aby ponownie uruchomić wszystkie usługi, których dotyczy problem.

        ![Zmień config Spark przez Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. Jeśli chcesz użyć nowego utworzonego środowiska wirtualnego na Jupyter. Musisz zmienić configs Jupyter i ponownie uruchomić Jupyter. Uruchom akcje skryptu na wszystkich węzłach nagłówka z poniższą instrukcją, aby wskazać Jupyterowi nowo utworzone środowisko wirtualne. Upewnij się, aby zmodyfikować ścieżkę do prefiksu określonego dla środowiska wirtualnego. Po uruchomieniu tej akcji skryptu uruchom ponownie usługę Jupyter za pośrednictwem interfejsu użytkownika Ambari, aby udostępnić tę zmianę.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Możesz dwukrotnie potwierdzić środowisko Pythona w notesie Jupytera, uruchamiając poniższy kod:

    ![Sprawdź wersję Pythona w notebooku Jupyter](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Znany problem

Znany jest błąd anakondy w wersji 4.7.11, 4.7.12 i 4.8.0. Jeśli widzisz, że akcje `"Collecting package metadata (repodata.json): ...working..."` skryptu `"Python script has been killed due to timeout after waiting 3600 secs"`wiszą na i nie z . Możesz pobrać [ten skrypt](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) i uruchomić go jako akcje skryptu we wszystkich węzłach, aby rozwiązać problem.

Aby sprawdzić wersję anakondy, można ssh do `/usr/bin/anaconda/bin/conda --v`węzła nagłówka klastra i uruchomić .

## <a name="next-steps"></a>Następne kroki

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)
* [Apache Spark z analizą biznesową: wykonywanie interaktywnej analizy danych przy użyciu funkcji Spark w funkcji HDInsight za pomocą narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
