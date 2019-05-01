---
title: Instalacja oprogramowania Jupyter lokalnie i łączenie z platformą Spark w usłudze Azure HDInsight
description: Dowiedz się, jak zainstalować notesu programu Jupyter lokalnie na komputerze i podłącz go do klastra Apache Spark.
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: hrasheed
ms.openlocfilehash: 5e9cd4c2a14f94c39c7058f45bf727df8198c053
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691292"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instalacja notesu programu Jupyter na komputerze i nawiązywanie z platformy Apache Spark w HDInsight

W tym artykule dowiesz się, jak zainstalować notesu programu Jupyter przy użyciu niestandardowych PySpark (dla języka Python) i jądra platformy Apache Spark (w przypadku Scala) przy użyciu platformy Spark magic i łączenie Notes z klastrem usługi HDInsight. Może istnieć wiele możliwych przyczyn, aby instalacja oprogramowania Jupyter na komputerze lokalnym, a może to być także niektóre wyzwania. Aby uzyskać więcej informacji, zobacz sekcję [Dlaczego należy zainstalować Jupyter na moim komputerze](#why-should-i-install-jupyter-on-my-computer) na końcu tego artykułu.

Istnieją cztery kroki klucza związane z instalacji programu Jupyter i nawiązywania połączenia z platformy Apache Spark w HDInsight.

* Skonfiguruj klaster Spark.
* Zainstaluj notesu programu Jupyter.
* Zainstaluj jądra PySpark i platformy Spark z magic platformy Spark.
* Skonfiguruj magic Spark do dostępu do klastra Spark w HDInsight.

Aby uzyskać więcej informacji o niestandardowych jądra i dostępne dla notesu Jupyter w klastrze HDInsight klastrze magic platformy Spark, zobacz [jądra, które są dostępne dla notesów programu Jupyter przy użyciu Apache Spark w systemie Linux klastrów HDInsight](apache-spark-jupyter-notebook-kernels.md).

> [!IMPORTANT]  
> Kroki opisane w artykule działają tylko do platformy Spark w wersji 2.1.0.

## <a name="prerequisites"></a>Wymagania wstępne
Wymagania wstępne wymienione w tym miejscu nie są dostępne do zainstalowania programu Jupyter. Są to do notesu programu Jupyter łączenia się z klastra usługi HDInsight po zainstalowaniu notesu.

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Klaster Apache Spark (wersja 2.1.0 lub małe) na HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).



## <a name="install-jupyter-notebook-on-your-computer"></a>Zainstaluj notesu programu Jupyter na komputerze

Przed zainstalowaniem notesów programu Jupyter, należy zainstalować języka Python. Środowiska Python i Jupyter są dostępne jako część [dystrybucji pakietu Anaconda](https://www.anaconda.com/download/). Podczas instalowania pakietu Anaconda, należy zainstalować dystrybucja języka Python. Po zainstalowaniu pakietu Anaconda, możesz dodać instalacji programu Jupyter, uruchamiając odpowiednich poleceń.

1. Pobierz [Instalatora pakietu Anaconda](https://www.anaconda.com/download/) dla platformy i uruchom Instalatora. Podczas uruchamiania Kreatora instalacji, upewnij się, że wybierasz opcję, aby dodać Anaconda do zmiennej PATH.

2. Uruchom następujące polecenie, aby instalacja oprogramowania Jupyter.

        conda install jupyter

    Aby uzyskać więcej informacji na temat instalowania programu Jupyter, zobacz [instalowanie Jupyter przy użyciu pakietu Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Zainstaluj jądra i magię platformy Spark

Aby uzyskać instrukcje dotyczące sposobu instalowania magic platformy Spark, jądra PySpark i platformy Spark, postępuj zgodnie z instrukcjami instalacji [dokumentacji sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) w witrynie GitHub. Pierwszym krokiem w dokumentacji magic Spark zostanie wyświetlony monit o zainstalowanie magic platformy Spark. Zastąp następujące polecenia, w zależności od wersji klastra HDInsight, z którą będzie łączyć się tym pierwszym krokiem link. Następnie wykonaj pozostałe kroki w dokumentacji magic platformy Spark. Jeśli chcesz zainstalować inny jądra, należy wykonać krok 3 w sekcji instrukcje instalacji magic platformy Spark.

* W przypadku klastrów 3.5 i v3.6 należy zainstalować sparkmagic 0.11.2, wykonując `pip install sparkmagic==0.11.2`

* W przypadku klastrów wersji 3.4 należy zainstalować sparkmagic 0.2.3, wykonując `pip install sparkmagic==0.2.3`

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurowanie magic platformy Spark, aby połączyć się z klastrem HDInsight Spark

W tej sekcji skonfigurujesz magic platformy Spark, zainstalowany wcześniej połączyć się z klastra Apache Spark, który musi już utworzonego w usłudze Azure HDInsight.

1. Uruchom powłokę języka Python, za pomocą następującego polecenia:

    ```
    python
    ```

2. Informacje o konfiguracji programu Jupyter, zwykle znajduje się w katalogu macierzystego użytkowników. Wprowadź następujące polecenie, aby zidentyfikować katalogu macierzystego i Utwórz folder istnieje o nazwie **.sparkmagic**.  Pełna ścieżka zostanie wyświetlona.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. W folderze `.sparkmagic`, Utwórz plik o nazwie **config.json** i Dodaj następujący fragment kodu JSON wewnątrz niego.  

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
4. Wprowadź następujące zmiany w pliku:

    |Wartość szablonu | Nowa wartość |
    |---|---|
    |UŻYTKOWNIK {USERNAME}|Logowania do klastra, domyślny jest administratorem.|
    |{CLUSTERDNSNAME}|Nazwa klastra|
    |{BASE64ENCODEDPASSWORD}|Hasło dla rzeczywistego hasła zakodowany w formacie base64.  Można wygenerować hasła base64 na [ https://www.url-encode-decode.com/base64-encode-decode/ ](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Jeśli przy użyciu `sparkmagic 0.11.23` (klastry 3.5 i v3.6).  Jeśli przy użyciu `sparkmagic 0.2.3` (klastry wersji 3.4), Zamień `"should_heartbeat": true`.|

    Widać w pliku, pełny przykład [config.json przykładowe](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Puls są wysyłane do upewnij się, że nie przedostają sesji. Gdy komputer przechodzi w stan uśpienia lub jest wyłączony, pulsu nie są wysyłane, wynikiem jest sesja wyczyszczone. W przypadku klastrów wersji 3.4, jeśli chcesz wyłączyć to zachowanie można ustawić konfiguracji usługi Livy `livy.server.interactive.heartbeat.timeout` do `0` z poziomu interfejsu użytkownika Ambari. Dla klastrów 3.5 Jeśli nie ustawisz 3,5 konfiguracji powyżej, sesja nie zostaną usunięte.

5. Rozpocznij Jupyter. Użyj następującego polecenia w wierszu polecenia.

        jupyter notebook

6. Sprawdź, czy za pomocą magic Spark dostępne jądra. Wykonaj poniższe kroki.

    a. Utwórz nowy notes. W prawym rogu wybierz opcję **New**. Powinien zostać wyświetlony domyślnego jądra **języka Python 2** lub **Python 3** i jądra został zainstalowany. Rzeczywiste wartości mogą się różnić w zależności od opcji instalacji.  Wybierz **PySpark**.

    ![Jądra w notesie Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "jądra w notesie Jupyter")

    > [!IMPORTANT]  
    > Po wybraniu **New** Przejrzyj powłoki pod kątem błędów.  Jeśli zostanie wyświetlony błąd `TypeError: __init__() got an unexpected keyword argument 'io_loop'` może wystąpić znany problem w niektórych wersjach Tornado.  Jeśli tak, Zatrzymaj jądro i następnie obniżenia poziomu Tornado instalację za pomocą następującego polecenia: `pip install tornado==4.5.3`.

    b. Uruchom poniższy fragment kodu.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Jeśli można pomyślnie pobrać dane wyjściowe, bada się połączenie z klastrem HDInsight.

    Można zaktualizować konfiguracji Notes, aby nawiązać połączenie z innym klastrem, należy zaktualizować config.json za pomocą nowego zestawu wartości, w jak pokazano w kroku 3 powyżej.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Dlaczego Instalacja oprogramowania Jupyter na moim komputerze?

Może to być z kilku powodów dlaczego warto Instalacja oprogramowania Jupyter na komputerze, a następnie nawiązać połączenie z klastrem platformy Apache Spark w HDInsight.

* Mimo że notesów programu Jupyter są już dostępne w klastrze Spark w usłudze Azure HDInsight, instalowanie Jupyter na komputerze zapewnia możliwość tworzenia notesy lokalnie, przetestować aplikację działającego klastra, a następnie przekaż notesy w klastrze. Na przekazywanie notesów w klastrze, możesz przekazać je za pomocą notesu programu Jupyter, z którym jest uruchomiona lub klastra lub zapisać je w folderze /HdiNotebooks na koncie magazynu skojarzonym z klastrem. Aby uzyskać więcej informacji na temat sposobu przechowywania notesów w klastrze, zobacz [notesów programu Jupyter przechowywania](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Za pomocą notesów, które są dostępne lokalnie, możesz połączyć różnych klastrach platformy Spark, zgodnie z wymaganiami aplikacji.
* GitHub można użyć do wdrożenia systemu kontroli źródła i mają kontrolę wersji dla komputerów przenośnych. Mogą też istnieć środowisko współpracy, w których wielu użytkowników może korzystać z tego samego notesu.
* Możesz pracować z notesami lokalnie nawet bez klastra. Dzięki temu wystarczy klastra, aby przetestować notesów programu przed nimi nie do ręcznego zarządzania Notesy użytkownika lub środowiska deweloperskiego.
* Może być łatwiejsze do skonfigurowania środowiska deweloperskiego lokalnego nie można skonfigurować instalację programu Jupyter w klastrze.  Możesz korzystać z zalet całe oprogramowanie zainstalowane lokalnie bez konfigurowania zdalnego klastrami.

> [!WARNING]  
> Jupyter zainstalowane na komputerze lokalnym wielu użytkowników umożliwia uruchamianie tego samego notesu na tym samym klastrze Spark w tym samym czasie. W takiej sytuacji wiele sesji usługi Livy są tworzone. Jeśli napotkania problemu, a chcesz debugować, który będzie że złożonym zadaniem w celu śledzenia sesji usługi Livy, które należy do użytkownika, który.  

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
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji platformy Apache Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
