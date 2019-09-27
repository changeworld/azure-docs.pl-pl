---
title: Instalowanie Jupyter lokalnie i nawiązywanie połączenia z platformą Spark w usłudze Azure HDInsight
description: Dowiedz się, jak zainstalować Notes Jupyter lokalnie na komputerze i połączyć go z klastrem Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.openlocfilehash: 46164cfc0c2baff919808a831a67180b65a23ff7
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337656"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instalowanie notesu Jupyter na komputerze i nawiązywanie połączenia z usługą Apache Spark w usłudze HDInsight

W tym artykule dowiesz się, jak zainstalować Notes Jupyter z użyciem niestandardowych PySpark (dla języka Python) i Apache Spark (dla Scala) za pomocą usługi Spark Magic i połączyć Notes z klastrem usługi HDInsight. Istnieje kilka powodów, dla których można instalować Jupyter na komputerze lokalnym, a także niektóre wyzwania. Aby uzyskać więcej informacji na ten temat, zobacz sekcję [Dlaczego należy zainstalować program Jupyter na komputerze](#why-should-i-install-jupyter-on-my-computer) na końcu tego artykułu.

Istnieją cztery kluczowe kroki związane z instalowaniem Jupyter i nawiązywanie połączenia z usługą Apache Spark w usłudze HDInsight.

* Skonfiguruj klaster Spark.
* Zainstaluj Notes Jupyter.
* Zainstaluj jądra PySpark i Spark przy użyciu Magic Spark.
* Skonfiguruj magiczną platformę Spark, aby uzyskać dostęp do klastra Spark w usłudze HDInsight.

Aby uzyskać więcej informacji o niestandardowych jądrach i Magic Spark dostępnych dla notesów Jupyter z klastrem usługi HDInsight, zobacz [jądra dostępne dla notesów Jupyter z klastrami Apache Spark Linux w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne wymienione w tym miejscu nie dotyczą instalowania Jupyter. Są one przeznaczone do łączenia notesu Jupyter z klastrem usługi HDInsight po zainstalowaniu notesu.

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalowanie notesu Jupyter na komputerze

Przed zainstalowaniem notesów Jupyter należy zainstalować środowisko Python. W ramach [dystrybucji Anaconda](https://www.anaconda.com/download/) zostanie zainstalowany zarówno Język Python, jak i Jupyter Notebook.

Pobierz [Instalatora Anaconda](https://www.anaconda.com/download/) na platformę i uruchom Instalatora. Podczas uruchamiania Kreatora instalacji upewnij się, że wybrano opcję dodania Anaconda do zmiennej PATH.  Zobacz również [Instalowanie Jupyter przy użyciu Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Zainstaluj Magic Spark

1. Wprowadź jedno z poniższych poleceń, aby zainstalować program Spark Magic. Zobacz również [dokumentację sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Wersja klastra | Polecenie instalacji |
    |---|---|
    |v 3.6 i v 3.5 |`pip install sparkmagic==0.12.7`|
    |v 3.4|`pip install sparkmagic==0.2.3`|

1. Upewnij `ipywidgets` się, że program jest prawidłowo zainstalowany, uruchamiając następujące polecenie:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Instalowanie PySpark i jądra platformy Spark

1. Określ, `sparkmagic` gdzie jest zainstalowana, wprowadzając następujące polecenie:

    ```cmd
    pip show sparkmagic
    ```

    Następnie zmień katalog roboczy na lokalizację identyfikowaną za pomocą powyższego polecenia.

1. W nowym katalogu roboczym wprowadź co najmniej jedno z poniższych poleceń, aby zainstalować odpowiednie jądra:

    |Jądra | Polecenie |
    |---|---|
    |platforma Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Opcjonalny. Wprowadź poniższe polecenie, aby włączyć rozszerzenie serwera:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurowanie programu Spark Magic do nawiązywania połączenia z klastrem usługi HDInsight Spark

W tej sekcji skonfigurujesz program Spark, który został wcześniej zainstalowany do łączenia się z klastrem Apache Spark.

1. Uruchom powłokę języka Python za pomocą następującego polecenia:

    ```cmd
    python
    ```

2. Informacje o konfiguracji Jupyter są zwykle przechowywane w katalogu macierzystym użytkowników. Wprowadź następujące polecenie, aby zidentyfikować katalog macierzysty, i Utwórz folder o nazwie **. sparkmagic**.  Pełna ścieżka zostanie wypełniona.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. W folderze `.sparkmagic`Utwórz plik o nazwie **config. JSON** i Dodaj do niego Poniższy fragment kodu JSON.  

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

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Wprowadź następujące zmiany do pliku:

    |Wartość szablonu | Nowa wartość |
    |---|---|
    |UŻ|Logowanie klastra, wartość domyślna `admin`to.|
    |CLUSTERDNSNAME|Nazwa klastra|
    |{BASE64ENCODEDPASSWORD}|Zakodowane w formacie base64 hasło do rzeczywistego hasła.  Hasło Base64 można wygenerować pod adresem [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Kontynuuj, jeśli `sparkmagic 0.12.7` jest używany (klastry v 3.5 i 3.6).  W przypadku `sparkmagic 0.2.3` używania (klastrów v 3.4) Zastąp `"should_heartbeat": true`ciąg opcją.|

    W przykładowym pliku [config. JSON](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)można zobaczyć pełny przykładowy plik.

   > [!TIP]  
   > Pulsy są wysyłane w celu zapewnienia braku przecieku sesji. Gdy komputer przejdzie w tryb uśpienia lub jest wyłączony, puls nie zostanie wysłany, co spowodowało wyczyszczenie sesji. W przypadku klastrów w wersji 3.4, jeśli chcesz wyłączyć to zachowanie, można ustawić konfigurację `livy.server.interactive.heartbeat.timeout` `0` usługi Livy z poziomu interfejsu użytkownika Ambari. Jeśli w przypadku klastrów 3.5 nie zostanie ustawiona Powyższa konfiguracja 3,5, sesja nie zostanie usunięta.

5. Uruchom Jupyter. Użyj następującego polecenia w wierszu polecenia.

    ```cmd
    jupyter notebook
    ```

6. Sprawdź, czy możesz użyć Magic Spark dostępnego z jądrami. Wykonaj poniższe kroki.

    a. Utwórz nowy notes. W prawym górnym rogu wybierz pozycję **Nowy**. Powinien zostać wyświetlony domyślny jądro **Python 2** lub **Python 3** i zainstalowane jądra. Rzeczywiste wartości mogą się różnić w zależności od opcji instalacji.  Wybierz pozycję **PySpark**.

    ![Dostępne jądra w](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "jądrze Jupyter w") notesie Jupyter

    > [!IMPORTANT]  
    > Po wybraniu pozycji **Nowy** Przejrzyj powłokę pod kątem błędów.  Jeśli zobaczysz błąd `TypeError: __init__() got an unexpected keyword argument 'io_loop'` , może wystąpić znany problem z niektórymi wersjami programu Tornado.  Jeśli tak, Zatrzymaj jądro, a następnie Obniż poziom instalacji Tornado przy użyciu następującego polecenia `pip install tornado==4.5.3`:.

    b. Uruchom Poniższy fragment kodu.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Jeśli można pomyślnie pobrać dane wyjściowe, zostanie przetestowane połączenie z klastrem usługi HDInsight.

    Jeśli chcesz zaktualizować konfigurację notesu w celu nawiązania połączenia z innym klastrem, zaktualizuj plik config. JSON przy użyciu nowego zestawu wartości, jak pokazano w kroku 3 powyżej.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Dlaczego należy zainstalować program Jupyter na komputerze?

Może być kilka powodów, dla których warto zainstalować program Jupyter na komputerze, a następnie połączyć go z klastrem Apache Spark w usłudze HDInsight.

* Mimo że notesy Jupyter są już dostępne w klastrze Spark w usłudze Azure HDInsight, zainstalowanie Jupyter na komputerze zapewnia możliwość lokalnego tworzenia notesów, testowania aplikacji w uruchomionym klastrze, a następnie przekazywania Notesy do klastra. Aby przekazać Notes do klastra, można przekazać je za pomocą notesu Jupyter działającego w systemie lub w klastrze lub zapisać w folderze/HdiNotebooks na koncie magazynu skojarzonym z klastrem. Aby uzyskać więcej informacji o tym, jak notesy są przechowywane w klastrze, zobacz [gdzie są przechowywane notesy Jupyter](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Dostępne lokalnie notesy umożliwiają łączenie się z różnymi klastrami platformy Spark na podstawie wymagań aplikacji.
* Za pomocą usługi GitHub można zaimplementować system kontroli źródła i mieć kontrolę wersji dla notesów. Możesz również mieć środowisko współpracy, w którym wielu użytkowników może pracować z tym samym notesem.
* Można korzystać z notesów lokalnie bez potrzeby klastra. Do testowania notesów jest potrzebny tylko klaster, a nie ręczne zarządzanie notesami lub środowiskiem programistycznym.
* Skonfigurowanie lokalnego środowiska deweloperskiego może być łatwiejsze niż skonfigurowanie instalacji Jupyter w klastrze.  Można korzystać ze wszystkich zainstalowanych lokalnie programów bez konfigurowania co najmniej jednego klastra zdalnego.

> [!WARNING]  
> Po zainstalowaniu Jupyter na komputerze lokalnym, wielu użytkowników może uruchamiać ten sam Notes w tym samym klastrze Spark w tym samym czasie. W takiej sytuacji tworzone są wiele sesji usługi Livy. Jeśli napotkasz problem i chcesz debugować ten program, będzie to złożone zadanie umożliwiające śledzenie, która sesja usługi Livy należy do którego użytkownika.  

## <a name="next-steps"></a>Następne kroki

* [Podsumowanie Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)
* [Apache Spark z usługą BI: Przeprowadzanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: Korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
