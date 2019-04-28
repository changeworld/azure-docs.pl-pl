---
title: Debugowanie zadania platformy Apache Spark działające w usłudze Azure HDInsight
description: Użyj interfejsie użytkownika YARN, interfejs użytkownika platformy Spark i serwer historii platformy Spark, śledzenie i debugowanie zadań uruchamianych w klastrze Spark w usłudze Azure HDInsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: hrasheed
ms.openlocfilehash: 5e384520c1b8d6cf5e3b182bbddf41a5f4f7f8f6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124292"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Debugowanie zadania platformy Apache Spark działające w usłudze Azure HDInsight

W tym artykule dowiesz się, jak śledzenie i debugowanie [platformy Apache Spark](https://spark.apache.org/) przy użyciu klastrów HDInsight zadań uruchomionych na [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) interfejsu użytkownika, interfejs użytkownika platformy Spark i serwer historii platformy Spark. Uruchamianie zadania Spark za pomocą notesu dostępnych z klastrem Spark **usługi Machine learning: Analizy predykcyjnej na danych inspekcji żywności przy użyciu MLLib**. Następujące kroki służą do śledzenia aplikacji, która zostanie przesłane za pomocą jakiejkolwiek innej metody, na przykład **skryptu spark-submit**.

## <a name="prerequisites"></a>Wymagania wstępne
Należy dysponować następującymi elementami:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Należy rozpocząłeś uruchamiania notesu  **[usługi Machine learning: Analizy predykcyjnej na danych inspekcji żywności przy użyciu MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Aby uzyskać instrukcje na temat uruchamiania ten notes Użyj linku.  

## <a name="track-an-application-in-the-yarn-ui"></a>Śledzenie aplikacji w interfejsie użytkownika YARN
1. Uruchom interfejs użytkownika usługi YARN. Kliknij przycisk **Yarn** w obszarze **pulpity nawigacyjne klastra**.
   
    ![Uruchom interfejs użytkownika usługi YARN](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]  
   > Alternatywnie można również uruchomić Interfejsie użytkownika YARN z poziomu interfejsu użytkownika Ambari. Aby uruchomić interfejs użytkownika systemu Ambari, kliknij przycisk **Ambari macierzystego** w obszarze **pulpity nawigacyjne klastra**. W Interfejsie użytkownika Ambari, kliknij **YARN**, kliknij przycisk **szybkich łączy**, kliknij active usługi Resource Manager, a następnie kliknij przycisk **interfejsu użytkownika Menedżera zasobów**. 

2. Ponieważ rozpoczęto zadanie Spark za pomocą notesów programu Jupyter, aplikacji o nazwie **remotesparkmagics** (jest to nazwa dla wszystkich aplikacji, które są uruchamiane z notesów). Kliknij przycisk Identyfikatora aplikacji i nazwa aplikacji, aby uzyskać więcej informacji o zadaniu. Spowoduje to uruchomienie widoku aplikacji.
   
    ![Znajdź identyfikator aplikacji Spark](./media/apache-spark-job-debugging/find-application-id.png)
   
    Dla takich aplikacji, które będą uruchamiane z notesów programu Jupyter, stan jest zawsze **systemem** aż do zakończenia działania notesu.
3. W widoku aplikacji można przejść do szczegółów dostosowaną znajdziesz kontenery skojarzone z aplikacją i dzienniki (stdout/stderr). Interfejs użytkownika platformy Spark można również uruchomić, klikając łączenia odpowiadający **śledzenia adresu URL**, jak pokazano poniżej. 
   
    ![Pobierz dzienniki kontenerów](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Śledzenie aplikacji w Interfejsie użytkownika platformy Spark
W Interfejsie użytkownika platformy Spark można przejść do szczegółów do zadań platformy Spark, które są zduplikowany przez aplikację, którą uruchomiono wcześniej.

1. Aby uruchomić interfejs użytkownika platformy Spark z widoku aplikacji, kliknij łącze względem **śledzenia adresu URL**, jak pokazano na powyższym zrzucie ekranu. Możesz zobaczyć wszystkich zadań platformy Spark, które będą uruchamiane przez aplikację uruchomioną w notesie Jupyter.
   
    ![Wyświetlanie zadań platformy Spark](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. Kliknij przycisk **Executors** kartę do przetwarzania i przechowywania informacji dotyczących poszczególnych funkcji wykonawczej. Możesz również pobrać stos wywołań, klikając **wątku zrzutu** łącza.
   
    ![Wyświetl executors platformy Spark](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Kliknij przycisk **etapów** kartę, aby zobaczyć etapów powiązane z daną aplikacją.
   
    ![Przeglądanie etapów platformy Spark](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    Każdy z etapów może mieć wiele zadań, które można wyświetlić statystyk wykonywania, jak pokazano poniżej.
   
    ![Przeglądanie etapów platformy Spark](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. Na stronie szczegółów etapu można uruchomić wizualizację grafu DAG. Rozwiń **wizualizacji DAG** link u góry strony, jak pokazano poniżej.
   
    ![Wyświetlanie wizualizacji etapów DAG platformy Spark](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG lub bezpośredniego Aclyic wykresu reprezentuje różnych etapów w ramach aplikacji. Każdy niebieskie pole na wykresie reprezentuje operację Spark wywoływane z aplikacji.
5. Na stronie szczegółów etapu można również uruchomić Widok osi czasu aplikacji. Rozwiń **osi czasu zdarzeń** link u góry strony, jak pokazano poniżej.
   
    ![Wyświetl oś czasu dla platformy Spark etapy zdarzeń](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Spowoduje to wyświetlenie zdarzenia platformy Spark w postaci osi czasu. Widok osi czasu jest dostępna na trzech poziomach różnych zadań, w ramach danego zadania, a także wewnątrz etapu. Na powyższej ilustracji przechwytuje widok osi czasu dla danego etapu.
   
   > [!TIP]  
   > Jeśli wybierzesz **włączone powiększanie** pole wyboru można przewijać lewej i prawej w widoku osi czasu.

6. Inne karty w Interfejsie użytkownika Spark zawierają przydatne informacje o tym wystąpieniu platformy Spark.
   
   * Karta magazynu — Jeśli aplikacja tworzy danych można znaleźć informacje o znajdującymi się na karcie pamięci masowej.
   * Karta środowiska — ta karta zawiera wiele przydatnych informacji o wystąpienie platformy Spark takich jak 
     * Wersja języka scala
     * Katalog dziennika zdarzeń, skojarzonego z klastrem
     * Liczba rdzeni wykonywania dla aplikacji
     * Itp.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Informacje o zakończonych zadań przy użyciu serwer historii platformy Spark
Po zakończeniu zadania, informacje o zadaniu są utrwalane w serwer historii platformy Spark.

1. Aby uruchomić serwer historii platformy Spark z poziomu bloku Przegląd kliknij **serwer historii platformy Spark** w obszarze **pulpity nawigacyjne klastra**.
   
    ![Uruchom serwer historii platformy Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]  
   > Alternatywnie można również uruchomić interfejs użytkownika serwer historii platformy Spark z poziomu interfejsu użytkownika Ambari. Aby uruchomić interfejs użytkownika systemu Ambari, z poziomu bloku Przegląd kliknij **Ambari macierzystego** w obszarze **pulpity nawigacyjne klastra**. W Interfejsie użytkownika Ambari, kliknij **Spark**, kliknij przycisk **szybkich łączy**, a następnie kliknij przycisk **interfejsu użytkownika aplikacji serwer historii platformy Spark**.

2. Zobaczysz wszystkie ukończone aplikacje na liście. Kliknij przycisk Identyfikator aplikacji, aby przejść do aplikacji, aby uzyskać więcej informacji.
   
    ![Uruchom serwer historii platformy Spark](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>Zobacz także
*  [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
*  [Debugowanie zadań usługi Apache Spark przy użyciu rozszerzonych serwer historii platformy Spark](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Dla analityków danych

* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika witryny sieci Web przy użyciu platformy Apache Spark w HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Dane telemetryczne analiza danych Application Insight przy użyciu platformy Apache Spark w HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Używanie platformy Caffe na platformie Azure HDInsight Spark na potrzeby rozproszonej uczenia głębokiego](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Dla deweloperów platformy Spark

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji platformy Apache Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)
