---
title: Instalowanie jupytera lokalnie i łączenie się z programem Spark w usłudze Azure HDInsight
description: Dowiedz się, jak zainstalować notes Jupyter lokalnie na komputerze i podłączyć go do klastra Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/02/2020
ms.openlocfilehash: 1d044ddaea0a2c7a1d489523cc9aa4515df0728a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632658"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Zainstaluj notebook Jupyter na komputerze i połącz się z Apache Spark na hdinsight

W tym artykule dowiesz się, jak zainstalować notes Jupyter z niestandardowymi jądrami PySpark (dla Pythona) i Apache Spark (dla Scali) za pomocą magii Spark. Następnie należy podłączyć notes do klastra HDInsight.

Istnieją cztery kluczowe kroki związane z instalacją Jupyter i łączeniem się z Apache Spark na HDInsight.

* Konfigurowanie klastra platformy Spark.
* Zainstaluj notebooka Jupyter.
* Zainstaluj jądra PySpark i Spark za pomocą magii Iskry.
* Skonfiguruj magię platformy Spark, aby uzyskać dostęp do klastra Platformy Spark w programie HDInsight.

Aby uzyskać więcej informacji na temat niestandardowych jąder i magii Spark, zobacz [Jądra dostępne dla notebooków Jupyter z klastrami Apache Spark Linux w programie HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md). Notes lokalny łączy się z klastrem HDInsight.

* Znajomość zagadnień dotyczących używania notesów Jupyter za pomocą platformy Spark w usłudze HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalowanie notebooka Jupyter na komputerze

Zainstaluj Pythona przed zainstalowaniem notebooków Jupyter. Dystrybucja [Anaconda](https://www.anaconda.com/download/) zainstaluje zarówno Python, jak i Notebook Jupyter.

Pobierz [instalator Anaconda](https://www.anaconda.com/download/) dla swojej platformy i uruchom instalatora. Podczas uruchamiania kreatora konfiguracji upewnij się, że wybrano opcję dodawania anakondy do zmiennej PATH.  Zobacz też: [Instalowanie programu Jupyter przy użyciu programu Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Zainstaluj magię Spark

1. Wprowadź jedno z poniższych poleceń, aby zainstalować magię Iskry. Zobacz też, [sparkmagic dokumentacji](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Wersja klastra | Polecenie Zainstaluj |
    |---|---|
    |wersja 3.6 i v3.5 |`pip install sparkmagic==0.13.1`|
    |wersja 3.4|`pip install sparkmagic==0.2.3`|

1. Upewnij `ipywidgets` się, że jest poprawnie zainstalowany, uruchamiając następujące polecenie:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Instalowanie jąder PySpark i Spark

1. Określ, gdzie `sparkmagic` jest zainstalowany, wprowadzając następujące polecenie:

    ```cmd
    pip show sparkmagic
    ```

    Następnie zmień katalog roboczy na **lokalizację** zidentyfikowaną za pomocą powyższego polecenia.

1. W nowym katalogu roboczym wprowadź jedno lub więcej poniższych poleceń, aby zainstalować poszukiwane jądro(

    |Jądro | Polecenie |
    |---|---|
    |platforma Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |Iskra|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark (pyspark)|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Element opcjonalny. Wprowadź poniższe polecenie, aby włączyć rozszerzenie serwera:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurowanie magii platformy Spark w celu połączenia z klastrem HDInsight Spark

W tej sekcji można skonfigurować magii Platformy Spark, który został zainstalowany wcześniej, aby połączyć się z klastrem Platformy Spark Apache.

1. Uruchom powłokę Języka Python za pomocą następującego polecenia:

    ```cmd
    python
    ```

2. Informacje o konfiguracji Jupyter są zazwyczaj przechowywane w katalogu domowym użytkowników. Wprowadź następujące polecenie, aby zidentyfikować katalog macierzysty, i utwórz folder o nazwie ** \.sparkmagic**.  Zostanie wysuń pełną ścieżkę.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. W folderze `.sparkmagic`utwórz plik o nazwie **config.json** i dodaj w nim następujący fragment kodu JSON.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Wykonuj następujące zmiany w pliku:

    |Wartość szablonu | Nowa wartość |
    |---|---|
    |{NAZWA UŻYTKOWNIKA}|Logowanie do `admin`klastra, domyślnie jest .|
    |{NAZWA_KLASTRA}|Nazwa klastra|
    |{BASE64ENCODEDPASSWORD}|Hasło zakodowane w base64 dla rzeczywistego hasła.  Hasło base64 można wygenerować w punkcie [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Zachowaj, `sparkmagic 0.12.7` jeśli używasz (klastry v3.5 i v3.6).  Jeśli `sparkmagic 0.2.3` używasz (klastry w wersji `"should_heartbeat": true`3.4), zamień na .|

    Pełny przykładowy plik można zobaczyć na [przykładowym pliku config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Pulsy są wysyłane w celu zapewnienia, że sesje nie są przeciekły. Gdy komputer przechodzi w tryb uśpienia lub jest zamknięty, bicie serca nie jest wysyłane, co powoduje, że sesja jest czyszczona. W przypadku klastrów w wersji 3.4, jeśli chcesz wyłączyć to `livy.server.interactive.heartbeat.timeout` `0` zachowanie, możesz ustawić konfigurację Livy z interfejsu użytkownika Ambari. W przypadku klastrów w wersji 3.5, jeśli nie ustawisz powyższej konfiguracji 3.5, sesja nie zostanie usunięta.

5. Start Jupyter. Użyj następującego polecenia z wiersza polecenia.

    ```cmd
    jupyter notebook
    ```

6. Sprawdź, czy możesz użyć magii Iskry dostępnej z jądrami. Wykonaj poniższe czynności.

    a. Utwórz nowy notes. W prawym rogu wybierz pozycję **Nowy**. Powinieneś zobaczyć domyślne jądro **Python 2** lub **Python 3** i zainstalowane jądra. Rzeczywiste wartości mogą się różnić w zależności od opcji instalacji.  Wybierz **PySpark**.

    ![Dostępne jądra w notebooku Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Jądra w notebooku Jupyter")

    > [!IMPORTANT]  
    > Po **wybraniu opcji Nowa** recenzja powłoki pod kątem błędów.  Jeśli widzisz błąd, `TypeError: __init__() got an unexpected keyword argument 'io_loop'` może wystąpić znany problem z niektórymi wersjami tornado.  Jeśli tak, zatrzymaj jądro, a następnie obniż poziom `pip install tornado==4.5.3`instalacji Tornado za pomocą następującego polecenia: .

    b. Uruchom poniższy fragment kodu.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Jeśli można pomyślnie pobrać dane wyjściowe, połączenie z klastrem HDInsight jest testowany.

    Jeśli chcesz zaktualizować konfigurację notesu, aby połączyć się z innym klastrem, zaktualizuj plik config.json o nowy zestaw wartości, jak pokazano w kroku 3 powyżej.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Dlaczego warto zainstalować jupyter na komputerze?

Powody, aby zainstalować Jupyter na komputerze, a następnie podłączyć go do klastra Apache Spark w programie HDInsight:

* Umożliwia lokalne tworzenie notesów, testowanie aplikacji względem uruchomionego klastra, a następnie przekazywanie notesów do klastra. Aby przekazać notesy do klastra, można przekazać je przy użyciu uruchomionego notesu Jupyter `/HdiNotebooks` lub klastra lub zapisać je w folderze na koncie magazynu skojarzonym z klastrem. Aby uzyskać więcej informacji na temat przechowywania notesów w klastrze, zobacz [Gdzie są przechowywane notesy jupyter?](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)
* Dzięki notesom dostępnym lokalnie można łączyć się z różnymi klastrami platformy Spark na podstawie wymagań aplikacji.
* Za pomocą gitHub można zaimplementować system kontroli źródła i mieć kontrolę wersji dla notesów. Można również mieć środowisko współpracy, w którym wielu użytkowników może pracować z tym samym notesem.
* Można pracować z notesami lokalnie, nawet bez konieczności klastra. Wystarczy klaster do testowania notesów, a nie do ręcznego zarządzania notesami lub środowiskiem programistycznym.
* Konfigurowanie lokalnego środowiska programistycznego może być łatwiejsze niż skonfigurowanie instalacji Jupytera w klastrze.  Możesz korzystać ze wszystkich zainstalowanych programów lokalnie bez konfigurowania jednego lub większej liczby klastrów zdalnych.

> [!WARNING]  
> Po zainstalowaniu jupytera na komputerze lokalnym wielu użytkowników może jednocześnie uruchamiać ten sam notes w tym samym klastrze platformy Spark. W takiej sytuacji tworzonych jest wiele sesji Livy. Jeśli napotkasz problem i chcesz to debugować, będzie to złożone zadanie do śledzenia, która sesja Livy należy do którego użytkownika.  

## <a name="next-steps"></a>Następne kroki

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)
* [Apache Spark z analizą analizy biznesowej: analizowanie danych platformy Spark apache przy użyciu usługi Power BI w programie HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark z uczeniem maszynowym: użyj platformy Spark w hdinsight do analizowania temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
