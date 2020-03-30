---
title: Debugowanie zadań platformy Apache Spark uruchomionych w usłudze Azure HDInsight
description: Używanie interfejsu użytkownika YARN, interfejsu użytkownika platformy Spark i serwera historii platformy Spark do śledzenia i debugowania zadań uruchomionych w klastrze platformy Spark w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: bcf2f97e855126c86dbb1d74cd430704e2af3af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932132"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Debugowanie zadań platformy Apache Spark uruchomionych w usłudze Azure HDInsight

W tym artykule dowiesz się, jak śledzić i debugować zadania [platformy Apache Spark](https://spark.apache.org/) uruchomione w klastrach USŁUGI HDInsight przy użyciu interfejsu użytkownika [usługi Apache Hadoop YARN,](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) interfejsu użytkownika platformy Spark i serwera historii platformy Spark. Zadanie platformy Spark można rozpocząć przy użyciu notesu dostępnego w klastrze Spark, **Uczenie maszynowe: Analiza predykcyjna danych inspekcji żywności przy użyciu pliku MLLib**. Poniższe kroki można wykonać, aby śledzić aplikację przesłaną przy użyciu innego podejścia, na przykład **spark-submit**.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Należy rozpocząć uruchamianie notebooka, **[Uczenie maszynowe: Analiza predykcyjna danych inspekcji żywności przy użyciu MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Aby uzyskać instrukcje dotyczące uruchamiania tego notesu, kliknij łącze.  

## <a name="track-an-application-in-the-yarn-ui"></a>Śledzenie aplikacji w interfejsie użytkownika YARN

1. Uruchom interfejs użytkownika YARN. Wybierz **pozycję Przędza** w obszarze **Pulpity nawigacyjne klastra**.

    ![Uruchomienie interfejsu użytkownika portalu Azure](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Alternatywnie, można również uruchomić YARN UI z interfejsu użytkownika Ambari. Aby uruchomić interfejs użytkownika Ambari, wybierz pozycję **Ambari home** w obszarze **Pulpity nawigacyjne klastra**. W interfejsie użytkownika Ambari przejdź do szybkiego**łącza** **YARN** > > aktywnym interfejsie menedżera zasobów > **Menedżera zasobów**.

2. Ponieważ uruchomiono zadanie Spark przy użyciu notesów Jupyter, aplikacja ma nazwę **remotesparkmagics** (jest to nazwa dla wszystkich aplikacji, które są uruchamiane z notesów). Wybierz identyfikator aplikacji względem nazwy aplikacji, aby uzyskać więcej informacji na temat zadania. Spowoduje to uruchomienie widoku aplikacji.

    ![Identyfikator aplikacji Znajdź aplikację serwera historii platformy Spark](./media/apache-spark-job-debugging/find-application-id1.png)

    W przypadku takich aplikacji, które są uruchamiane z notesów Jupyter, stan jest zawsze **uruchomiony,** dopóki nie wyjdziesz z notesu.

3. Z widoku aplikacji można przejść do szczegółów, aby dowiedzieć się kontenery skojarzone z aplikacją i dzienniki (stdout/stderr). Możesz również uruchomić interfejs spark, klikając łącze odpowiadające **adresowi URL śledzenia**, jak pokazano poniżej.

    ![Dzienniki kontenerów pobierania serwera historii iskier](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Śledzenie aplikacji w interfejsie użytkownika platformy Spark

W interfejsie użytkownika platformy Spark można przejść do szczegółów zadań platformy Spark, które są zduplikowane przez aplikację uruchomiono wcześniej.

1. Aby uruchomić interfejs spark, z widoku aplikacji wybierz łącze względem **adresu URL śledzenia,** jak pokazano na ekranie przechwytywania powyżej. Możesz zobaczyć wszystkie zadania platformy Spark, które są uruchamiane przez aplikację działającą w notesie Jupyter.

    ![Karta Zadania serwera historii iskierki](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Wybierz kartę **Executors,** aby wyświetlić informacje o przetwarzaniu i przechowywaniu dla każdego wykonawcy. Można również pobrać stos wywołań, wybierając łącze **Zrzut wątku.**

    ![Karta executorów serwera historii istry](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Wybierz kartę **Etapy,** aby wyświetlić etapy skojarzone z aplikacją.

    ![Karta etapów serwera historii iskierki](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Wyświetl etapy Iskry")

    Każdy etap może mieć wiele zadań, dla których można wyświetlić statystyki wykonania, jak pokazano poniżej.

    ![Szczegóły karty etapów serwera historii iskry](./media/apache-spark-job-debugging/view-spark-stages-details.png "Wyświetl szczegóły etapów iskry")

4. Na stronie szczegółów etapu możesz uruchomić wizualizację DAG. Rozwiń łącze **wizualizacji DAG** u góry strony, jak pokazano poniżej.

    ![Zobacz etapy Iskry wizualizacji DAG](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG lub Direct Aclyic Graph reprezentuje różne etapy w aplikacji. Każde niebieskie pole na wykresie reprezentuje spark operacji wywoływanych z aplikacji.

5. Na stronie szczegółów stołu montażowego można również uruchomić widok osi czasu aplikacji. Rozwiń łącze **Oś czasu zdarzenia** u góry strony, jak pokazano poniżej.

    ![Wyświetlanie osi czasu zdarzeń etapów iskry](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Spowoduje to wyświetlenia spark zdarzenia w postaci osi czasu. Widok osi czasu jest dostępny na trzech poziomach, między zadaniami, w ramach zadania i na etapie. Powyższy obraz przechwytuje widok osi czasu dla danego etapu.

   > [!TIP]  
   > Jeśli zaznaczysz pole wyboru **Włącz powiększanie,** możesz przewijać w lewo i w prawo w widoku osi czasu.

6. Inne karty w interfejsie użytkownika platformy Spark zawierają przydatne informacje na temat spark wystąpienia, jak również.

   * Karta Magazyn — jeśli aplikacja tworzy rdd, można znaleźć informacje o tych na karcie Magazyn.
   * Karta Środowisko — ta karta zawiera przydatne informacje o wystąpieniu platformy Spark, takie jak:
     * Wersja Scala
     * Katalog dziennika zdarzeń skojarzony z klastrem
     * Liczba rdzeni wykonawcy dla aplikacji
     * Itp.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Znajdowanie informacji o ukończonych zadaniach przy użyciu serwera spark history server

Po zakończeniu zadania informacje o zadaniu są zachowywane na serwerze historii platformy Spark.

1. Aby uruchomić serwer historii platformy Spark, na stronie **Przegląd** wybierz pozycję **Serwer historii platformy Spark** w obszarze **Pulpity nawigacyjne klastra**.

    ![Uruchomienie serwera historii usługi Azure portal](./media/apache-spark-job-debugging/launch-spark-history-server.png "Uruchamianie serwera historii platformy Spark1")

   > [!TIP]  
   > Alternatywnie można również uruchomić interfejs użytkownika serwera historii platformy Spark z interfejsu użytkownika Ambari. Aby uruchomić interfejs użytkownika Ambari, z bloku Przegląd wybierz pozycję **Ambari home** w obszarze **Pulpity nawigacyjne klastra**. W interfejsie użytkownika Ambari przejdź do interfejsu **spark2** > **Quick Links** > **History Server .**

2. Na liście zostaną wyświetlone wszystkie ukończone aplikacje. Wybierz identyfikator aplikacji, aby przejść do szczegółów w aplikacji, aby uzyskać więcej informacji.

    ![Aplikacje ukończone przez serwer historii platformy Spark](./media/apache-spark-job-debugging/view-completed-applications.png "Uruchamianie serwera historii platformy Spark2")

## <a name="see-also"></a>Zobacz też

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Debug Apache Spark Zadania przy użyciu rozszerzonego serwera historii platformy Spark](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Dla analityków danych

* [Apache Spark z uczeniem maszynowym: użyj platformy Spark w hdinsight do analizowania temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika strony internetowej przy użyciu platformy Apache Spark w hdinsight](apache-spark-custom-library-website-log-analysis.md)
* [Analiza danych telemetrycznych usługi Application Insight przy użyciu platformy Spark apache w udziale hdinsight](apache-spark-analyze-application-insight-logs.md)


### <a name="for-spark-developers"></a>Dla deweloperów Spark

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki HDInsight Tools dla IntelliJ IDEA do zdalnego debugowania aplikacji Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Używanie notebooków Apache Zeppelin z klastrem Apache Spark w programie HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notebooka Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)
