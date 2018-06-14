---
title: Instalacja oprogramowania Jupyter lokalnie i połączenia z klastrem usługi Azure HDInsight Spark | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować notesu Jupyter lokalnie na komputerze i podłącz go do klastra Apache Spark w usłudze Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48593bdf-4122-4f2e-a8ec-fdc009e47c16
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: eea61586054f34142d77f16333fe70a66d95d529
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528362"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Zainstaluj notesu Jupyter na komputerze i nawiązywanie Apache Spark w usłudze HDInsight

W tym artykule należy dowiedzieć się, jak zainstalować notesu Jupyter z niestandardowych PySpark (dla języka Python) i jądra Spark (na języku Scala) z Spark magic i notesu nawiązać połączenia z klastrem usługi HDInsight. Może istnieć wiele przyczyn, aby instalacja oprogramowania Jupyter na komputerze lokalnym i mogą być również pewne wyzwania. Aby uzyskać więcej informacji, zobacz sekcję [Dlaczego należy zainstalować oprogramowania Jupyter na tym komputerze](#why-should-i-install-jupyter-on-my-computer) na końcu tego artykułu.

Istnieją trzy kluczowe kroki związane z instalowanie Jupyter i Spark magic na komputerze.

* Zainstaluj notesu Jupyter
* Instalowanie jądra PySpark i Spark przy użyciu Spark magic
* Skonfiguruj magic Spark uzyskanie dostępu do klastra Spark w usłudze HDInsight

Aby uzyskać więcej informacji o niestandardowych jądra i magic Spark dostępne dla notesu Jupyter w klastrze z klastrem usługi HDInsight, zobacz [jądra dostępne dla notesu Jupyter klastrze Apache Spark w systemie Linux klastrów HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Wymagania wstępne
Wymagania wstępne wymienione w tym miejscu nie dotyczą instalowania Jupyter. Są to podłączania notesu Jupyter w klastrze usługi HDInsight, po zainstalowaniu notesu.

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [klastrów utworzyć Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Zainstaluj na tym komputerze notesu Jupyter

Python należy zainstalować przed zainstalowaniem notesów Jupyter. Zarówno Python i Jupyter są dostępne jako część [Anaconda dystrybucji](https://www.continuum.io/downloads). Po zainstalowaniu Anaconda, należy zainstalować dystrybucji języka Python. Po zainstalowaniu Anaconda, możesz dodać za pomocą odpowiednich poleceń instalacji Jupyter.

1. Pobierz [Instalator Anaconda](https://www.continuum.io/downloads) dla platformy i uruchom Instalatora. Podczas uruchamiania Kreatora instalacji, upewnij się, że zostanie wybrana opcja, aby dodać Anaconda do zmiennej PATH.
2. Uruchom następujące polecenie, aby instalacja oprogramowania Jupyter.

        conda install jupyter

    Aby uzyskać więcej informacji na temat instalowania Jupyter, zobacz [Jupyter instalowanie przy użyciu Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Zainstaluj jądra i Spark magic

Aby uzyskać instrukcje dotyczące sposobu instalowania Spark magic, jądra PySpark i Spark, postępuj zgodnie z instrukcjami instalacji [dokumentacji sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) w witrynie GitHub. Pierwszym krokiem w dokumentacji magic Spark jest wyświetlany monit o zainstalowanie Spark magic. Zastąp ten pierwszy krok w łączu za pomocą następujących poleceń, w zależności od wersji z klastrem usługi HDInsight, z którą będzie łączyć się. Następnie wykonaj pozostałe kroki w dokumentacji magic Spark. Jeśli chcesz zainstalować różne jądra, należy wykonać krok 3 w sekcji instrukcje instalacji magic Spark.

* W przypadku klastrów v3.4 należy zainstalować sparkmagic 0.2.3, wykonując `pip install sparkmagic==0.2.3`

* W przypadku klastrów w wersji 3.5 i v3.6 należy zainstalować sparkmagic 0.11.2, wykonując `pip install sparkmagic==0.11.2`

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Skonfiguruj magic Spark do nawiązania połączenia klastra Spark w usłudze HDInsight

W tej sekcji skonfigurujesz magic Spark, która zainstalowane wcześniej do nawiązania połączenia klastra Apache Spark, który musi mieć już utworzone w usłudze Azure HDInsight.

1. Informacje o konfiguracji Jupyter zazwyczaj znajduje się w katalogu macierzystego użytkowników. Aby zlokalizować katalog macierzysty na dowolnej platformie systemu operacyjnego, wpisz następujące polecenia.

    Uruchom powłokę Python. W oknie polecenia wpisz następujące polecenie:

        python

    Na powłoki Python wprowadź następujące polecenie, aby dowiedzieć się, katalogu macierzystego.

        import os
        print(os.path.expanduser('~'))

2. Przejdź do katalogu macierzystego i Utwórz folder o nazwie **.sparkmagic** Jeśli jeszcze nie istnieje.
3. W folderze, Utwórz plik o nazwie **config.json** i Dodaj następujący fragment kodu JSON wewnątrz niej.

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
          }
        }

4. SUBSTITUTE **{USERNAME}**, **{CLUSTERDNSNAME}**, i **{BASE64ENCODEDPASSWORD}** z odpowiednie wartości. Szereg narzędzi w ulubionych języka programowania lub w trybie online służy do generowania haseł kodowany w standardzie base64 rzeczywistego hasła.

5. Konfigurowanie ustawień pulsu praw w `config.json`. Należy dodać te ustawienia na tym samym poziomie jako `kernel_python_credentials` i `kernel_scala_credentials` wstawki programu dodane wcześniej. Na przykład o tym, jak i kiedy należy dodać ustawienia pulsu, zobacz [config.json próbki](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

    * Aby uzyskać `sparkmagic 0.2.3` (klastrów v3.4), obejmują:

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * Aby uzyskać `sparkmagic 0.11.2` (klastrów w wersji 3.5 i v3.6), obejmują:

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >Aby upewnić się, że nie przedostają sesji są wysyłane impulsy. Gdy komputer przechodzi w stan uśpienia lub jest wyłączony, pulsu nie są wysyłane, co Trwa sesja wyczyszczone. V3.4 klastrów, jeśli chcesz wyłączyć to zachowanie można ustawić konfiguracji Livy `livy.server.interactive.heartbeat.timeout` do `0` w interfejsie użytkownika narzędzia Ambari. Dla klastrów w wersji 3.5 Jeśli konfiguracja 3.5 powyżej, nie należy ustawiać sesji nie zostaną usunięte.

6. Uruchom Jupyter. Użyj następującego polecenia w wierszu polecenia.

        jupyter notebook

7. Sprawdź, czy możesz nawiązać klastra za pomocą notesu Jupyter i że za pomocą dostępnych magic Spark jądra. Wykonaj poniższe kroki.

    a. Utwórz nowy notes. W prawym górnym rogu, kliknij przycisk **nowy**. Powinna zostać wyświetlona domyślna jądra **Python2** i dwa nowe jądra, które można zainstalować **PySpark** i **Spark**. Kliknij przycisk **PySpark**.

    ![Jądra w notesu Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "jądra w notesu Jupyter")

    b. Uruchom poniższy fragment kodu.

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    Czy można pomyślnie pobrać dane wyjściowe, jest przetestować połączenie z klastrem usługi HDInsight.

    >[!TIP]
    >Jeśli chcesz zaktualizować konfigurację notesu nawiązać do innego klastra, należy zaktualizować config.json o nowy zestaw wartości, jak pokazano w kroku 3 powyżej.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Dlaczego Instalacja oprogramowania Jupyter na komputerze?
Może istnieć wiele przyczyn, dlaczego warto Instalacja oprogramowania Jupyter na komputerze, a następnie podłącz go do klastra Spark w usłudze HDInsight.

* Mimo że notesów Jupyter są już dostępne w klastrze Spark w usłudze Azure HDInsight, instalowanie oprogramowania Jupyter na komputerze zapewnia można równocześnie utworzyć notesy lokalnie, przetestować aplikację na klastrze uruchomione, a następnie przekaż notebooki do klastra. Aby przekazać notebooki do klastra, można przekazać je za pomocą notesu Jupyter, na którym jest uruchomiona lub klastra lub zapisać je w folderze /HdiNotebooks w ramach konta magazynu skojarzone z klastrem. Aby uzyskać więcej informacji dotyczących sposobu przechowywania notesów w klastrze, zobacz [notesów Jupyter przechowywania](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Dzięki notesy dostępne lokalnie, możesz mogą łączyć się różne klastry Spark oparty na wymagań aplikacji.
* GitHub można użyć do wdrożenia systemu kontroli źródła i kontroli wersji dla komputerów przenośnych. Można również mieć środowisku współpracy, gdy wielu użytkowników może korzystać z tego samego notesu.
* Możesz pracować z notesów lokalnie nawet bez klastra. Wystarczy tylko klastra do testowania notesy względem, aby nie ręcznie zarządzać notesy lub środowisko deweloperskie.
* Może być łatwiejsze do konfigurowania środowiska deweloperskiego lokalnego nie można skonfigurować instalację Jupyter w klastrze.  Można korzystać z oprogramowania zainstalowane lokalnie bez konfigurowania co najmniej jeden klaster zdalnego.

> [!WARNING]
> Jupyter zainstalowany na komputerze lokalnym wielu użytkowników umożliwia uruchamianie tego samego notesu na tym samym klastrze Spark w tym samym czasie. W takiej sytuacji wiele sesji Livy są tworzone. Jeśli wystąpiły problemu i chcesz debugować, będzie się, że złożonym zadaniem w celu śledzenia sesji Livy, które należy do użytkownika, który.
>
>

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
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
