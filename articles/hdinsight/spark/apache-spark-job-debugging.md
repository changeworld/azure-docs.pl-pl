---
title: Debugowanie Apache Spark zadań uruchomionych w usłudze Azure HDInsight
description: Śledzenie i debugowanie zadań uruchomionych w klastrze Spark w usłudze Azure HDInsight za pomocą interfejsu użytkownika, interfejsu użytkownika platformy Spark i serwera historii platformy Spark
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: hrasheed
ms.openlocfilehash: c71bf9be94799c2b109d57393e70e99a0710e252
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995501"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Debugowanie Apache Spark zadań uruchomionych w usłudze Azure HDInsight

W tym artykule dowiesz się, jak śledzić i debugować zadania [Apache Spark](https://spark.apache.org/) uruchomione w klastrach usługi HDInsight przy użyciu interfejsu użytkownika ( [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ), interfejsu użytkownika platformy Spark i serwera historii platformy Spark. Rozpoczęcie zadania platformy Spark przy użyciu notesu dostępnego w klastrze Spark, **Uczenie maszynowe: Analiza predykcyjna danych inspekcji żywności przy użyciu MLLib**. Poniższe kroki służą do śledzenia aplikacji przesłanej przy użyciu innego podejścia, na przykład do przesyłania danych przez program **Spark**.

## <a name="prerequisites"></a>Wymagania wstępne
Wymagane są następujące elementy:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Należy rozpocząć pracę z notesem,  **[Uczenie maszynowe: Analiza predykcyjna danych inspekcji żywności przy użyciu MLLib](apache-spark-machine-learning-mllib-ipython.md).** Aby uzyskać instrukcje dotyczące sposobu uruchamiania tego notesu, Użyj linku.  

## <a name="track-an-application-in-the-yarn-ui"></a>Śledzenie aplikacji w interfejsie użytkownika PRZĘDZy
1. Uruchom interfejs użytkownika PRZĘDZy. Kliknij pozycję **przędza** w obszarze **pulpity nawigacyjne klastra**.
   
    ![Uruchom interfejs użytkownika PRZĘDZy](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)
   
   > [!TIP]  
   > Alternatywnie można również uruchomić interfejs użytkownika PRZĘDZy z interfejsu użytkownika Ambari. Aby uruchomić interfejs użytkownika Ambari, kliknij pozycję **Strona główna** w obszarze **pulpity nawigacyjne klastra**. W interfejsie użytkownika Ambari kliknij pozycję **przędza**, kliknij pozycję **szybkie linki**, kliknij pozycję aktywne Menedżer zasobów, a następnie kliknij pozycję **interfejs użytkownika Menedżer zasobów**. 

2. Ponieważ uruchomiono zadanie Spark przy użyciu notesów Jupyter, aplikacja ma nazwę **remotesparkmagics** (jest to nazwa wszystkich aplikacji uruchamianych z notesów). Kliknij identyfikator aplikacji dla nazwy aplikacji, aby uzyskać więcej informacji o zadaniu. Spowoduje to uruchomienie widoku aplikacji.
   
    ![Znajdź identyfikator aplikacji platformy Spark](./media/apache-spark-job-debugging/find-application-id1.png)
   
    W przypadku takich aplikacji, które są uruchamiane z notesów Jupyter, stan jest zawsze **uruchamiany** , dopóki nie zamkniesz notesu.
3. W widoku aplikacji możesz przejść do szczegółów, aby dowiedzieć się więcej o kontenerach skojarzonych z aplikacją i dziennikach (stdout/stderr). Interfejs użytkownika Spark można również uruchomić, klikając link odpowiadający **adresowi URL śledzenia**, jak pokazano poniżej. 
   
    ![Pobierz dzienniki kontenerów](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Śledzenie aplikacji w interfejsie użytkownika Spark
W interfejsie użytkownika platformy Spark można przechodzić do szczegółów zadań platformy Spark, które są duplikowane przez uruchomioną wcześniej aplikację.

1. Aby uruchomić interfejs użytkownika Spark, w widoku aplikacji kliknij link z **adresem URL śledzenia**, jak pokazano na poniższym zrzucie ekranu. Można wyświetlić wszystkie zadania platformy Spark uruchamiane przez aplikację uruchomioną w notesie Jupyter.
   
    ![Wyświetlanie zadań platformy Spark](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)
2. Kliknij kartę **wykonawcy** , aby wyświetlić informacje dotyczące przetwarzania i magazynowania dla każdego wykonawcy. Możesz również pobrać stos wywołań, klikając łącze **zrzut wątku** .
   
    ![Wyświetlanie modułów wykonujących Spark](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Kliknij kartę **etapy** , aby wyświetlić etapy skojarzone z aplikacją.
   
    ![Wyświetl etapy platformy Spark](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Wyświetl etapy platformy Spark")
   
    Każdy etap może mieć wiele zadań, dla których można wyświetlić statystyki wykonywania, jak pokazano poniżej.
   
    ![Wyświetl szczegóły etapów platformy Spark](./media/apache-spark-job-debugging/view-spark-stages-details.png "Wyświetl szczegóły etapów platformy Spark") 
4. Na stronie szczegóły etapu można uruchomić wizualizację DAG. Rozwiń link **wizualizacji DAG** w górnej części strony, jak pokazano poniżej.
   
    ![Wyświetl wizualizację DAG etapów platformy Spark](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG lub Direct Aclyic Graph reprezentuje różne etapy aplikacji. Każde niebieskie pole na grafie reprezentuje operację Spark wywoływaną z aplikacji.
5. Na stronie szczegóły etapu można również uruchomić widok oś czasu aplikacji. Rozwiń link **oś czasu zdarzeń** w górnej części strony, jak pokazano poniżej.
   
    ![Wyświetl oś czasu zdarzeń etapów platformy Spark](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Spowoduje to wyświetlenie zdarzeń platformy Spark w formie osi czasu. Widok oś czasu jest dostępny na trzech poziomach, między zadaniami, w ramach zadania i w obrębie etapu. Powyższy obraz przechwytuje Widok osi czasu dla danego etapu.
   
   > [!TIP]  
   > W przypadku zaznaczenia pola wyboru **Włącz powiększanie** , można przewijać w lewo i w prawo w widoku oś czasu.

6. Inne karty w interfejsie użytkownika Spark zawierają również użyteczne informacje o wystąpieniu platformy Spark.
   
   * Karta magazyn — Jeśli aplikacja tworzy odporne, można znaleźć informacje o nich na karcie Magazyn.
   * Karta środowisko — ta karta zawiera wiele przydatnych informacji o wystąpieniu platformy Spark, takich jak 
     * Wersja Scala
     * Katalog dziennika zdarzeń skojarzony z klastrem
     * Liczba rdzeni wykonawców dla aplikacji
     * Etc.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Znajdowanie informacji o ukończonych zadaniach przy użyciu serwera historii platformy Spark
Po zakończeniu zadania informacje o zadaniu są utrwalane na serwerze historii platformy Spark.

1. Aby uruchomić serwer historii platformy Spark, w bloku przegląd kliknij pozycję **serwer historii platformy Spark** w obszarze **pulpity nawigacyjne klastra**.
   
    ![Uruchom historię platformy Spark serwer1](./media/apache-spark-job-debugging/launch-spark-history-server.png "Uruchom historię platformy Spark serwer1")
   
   > [!TIP]  
   > Alternatywnie można również uruchomić interfejs użytkownika serwera historii platformy Spark z poziomu interfejsu użytkownika Ambari. Aby uruchomić interfejs użytkownika Ambari, w bloku przegląd kliknij pozycję **Strona główna Ambari** w obszarze **pulpity nawigacyjne klastra**. W interfejsie użytkownika Ambari kliknij pozycję **Spark**, kliknij pozycję **szybkie linki**, a następnie kliknij pozycję **interfejs użytkownika serwera historii platformy Spark**.

2. Zobaczysz wszystkie ukończone aplikacje wymienione na liście. Kliknij identyfikator aplikacji, aby przejść do aplikacji, aby uzyskać więcej informacji.
   
    ![Uruchom historię platformy Spark Serwer2](./media/apache-spark-job-debugging/view-completed-applications.png "Uruchom historię platformy Spark Serwer2")

## <a name="see-also"></a>Zobacz także
*  [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
*  [Debugowanie Apache Spark zadań przy użyciu rozszerzonego serwera historii platformy Spark](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Dla analityków danych

* [Apache Spark z Machine Learning: Korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: Korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analiza danych telemetrycznych usługi Application Insight przy użyciu Apache Spark w usłudze HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Użyj Caffe na Azure HDInsight Spark na potrzeby rozproszonej uczenia głębokiego](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Dla deweloperów platformy Spark

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki narzędzi HDInsight do IntelliJ pomysł, aby debugować aplikacje Apache Spark zdalnie](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)
