---
title: 'Azure Toolkit for IntelliJ: aplikacji Spark debugować zdalnie za pośrednictwem SSH | Dokumentacja firmy Microsoft'
description: Wskazówki krok po kroku dotyczące sposobu używania narzędzi HDInsight Tools w zestawie narzędzi Azure for IntelliJ debugowanie aplikacji zdalnie w usłudze HDInsight clusters za pośrednictwem SSH
keywords: zdalne debugowanie intellij, zdalnego debugowania intellij, ssh, intellij, hdinsight, debugowania intellij, debugowania
services: hdinsight
documentationcenter: ''
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: ''
ms.topic: article
ms.date: 11/25/2017
ms.author: jejiang
ms.openlocfilehash: ad0b1bbfc74f992a646ac375583f3399243873f5
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Debugowanie aplikacji Spark lokalnie lub zdalnie w klastrze usługi HDInsight narzędzi Azure for IntelliJ za pośrednictwem SSH

Ten artykuł zawiera wskazówki krok po kroku dotyczące sposobu używania narzędzi HDInsight Tools w zestawie narzędzi Azure for IntelliJ debugowanie aplikacji zdalnie w klastrze usługi HDInsight. Aby debugować projektu, można również wyświetlić [aplikacji Spark w usłudze HDInsight debugowania narzędzi Azure for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) wideo.

**Wymagania wstępne**
* **Narzędzia HDInsight Tools w Azure Toolkit for IntelliJ**. To narzędzie jest częścią zestawu narzędzi Azure for IntelliJ. Aby uzyskać więcej informacji, zobacz [zainstalować zestaw narzędzi platformy Azure dla IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). I **Azure Toolkit for IntelliJ**. Ten zestaw narzędzi umożliwia tworzenie aplikacji Spark dla klastra usługi HDInsight. Aby uzyskać więcej informacji, postępuj zgodnie z instrukcjami [użyj narzędzi Azure dla IntelliJ tworzenie aplikacji dla klastra usługi HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **Usługa HDInsight SSH z nazwy użytkownika i hasła zarządzania**. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługą HDInsight (Hadoop) przy użyciu protokołu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) i [używanie protokołu SSH tunneling Ambari dostępu do sieci web UI, JobHistory, NameNode, Oozie i innych sieci web UI](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Dowiedz się, jak wykonać lokalnego uruchamiania i debugowanie
### <a name="scenario-1-create-a-spark-scala-application"></a>Scenariusz 1: Tworzenie aplikacji Spark Scala 

1. Uruchom IntelliJ IDEA, a następnie utworzyć projekt. W **nowy projekt** okno dialogowe, wykonaj następujące czynności:

   a. Wybierz **HDInsight**. 

   b. Wybierz język Java lub Scala szablon na podstawie preferencji użytkownika. Wybierz jedną z następujących opcji:

      - **Platforma Spark w usłudze HDInsight (Scala)**

      - **Platforma Spark w usłudze HDInsight (Java)**

      - **Platforma Spark w HDInsight przykładowy (Scala)**

      W tym przykładzie użyto **Spark, na przykład HDInsight (Scala)** szablonu.

   c. W **narzędzia kompilacji** listy, wybierz jedną z następujących czynności, w zależności od potrzeb:

      - **Maven**, obsługę języka Scala Kreator tworzenia projektu

      -  **SBT**, do zarządzania zależności i budowania projektu języka Scala 

      ![Tworzenie projektu debugowania](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Wybierz opcję **Dalej**.     
 
2. W następnej **nowy projekt** okna, wykonaj następujące czynności:

   ![Wybierz Spark zestawu SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Wprowadź nazwę projektu i lokalizacja projektu.

   b. W **SDK projektu** listy rozwijanej wybierz **Java 1.8** dla **Spark 2.x** klastra lub wybierz **Java 1.7** dla **Spark 1.x** klastra.

   c. W **wersji Spark** listy rozwijanej, Kreator tworzenia projektu Scala integruje poprawnej wersji dla platformy Spark zestawy SDK i Scala. Jeśli wersja klastra spark jest starsza niż 2.0, wybierz **Spark 1.x**. W przeciwnym razie wybierz **Spark 2.x.** W tym przykładzie użyto **Spark pkt 2.0.2 (Scala 2.11.8)**.

   d. Wybierz pozycję **Finish** (Zakończ).

3. Wybierz **src** > **głównego** > **scala** otworzyć kodu w projekcie. W tym przykładzie użyto **SparkCore_wasbloTest** skryptu.

### <a name="prerequisite-for-windows"></a>Wymagania wstępne dotyczące systemu windows
Gdy używasz lokalnych aplikacji Spark Scala na komputerze z systemem Windows, można uzyskać wyjątku, zgodnie z objaśnieniem w [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). Wyjątek występuje, ponieważ brakuje WinUtils.exe w systemie Windows. 

Aby rozwiązać ten problem, [Pobierz plik wykonywalny](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do lokalizacji, takie jak **C:\WinUtils\bin**. Następnie należy dodać zmienną środowiskową **HADOOP_HOME**, a następnie ustaw wartość zmiennej **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Scenariusz 2: Wykonaj przebiegu lokalnego
1. Otwórz **SparkCore_wasbloTest** skryptów, kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz opcję **Uruchom 'XXX [Spark zadania]'** do wykonywania przebiegu lokalnego.
2. Uruchom raz lokalnego ukończone, widać pliku wyjściowego, zapisać w Twojej bieżącej Eksplorator projektów **danych** > **__domyślne__**.

    ![Wynik uruchomienia lokalnego](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
3. Nasze narzędzia ustawiono domyślne debugowania lokalnego konfigurację uruchomieniową automatycznie, podczas wykonywania lokalnego uruchamiania i lokalnych. Otwórz konfigurację **XXX [Spark zadania]** w prawym górnym rogu, zobacz **XXX [Spark zadania]** już utworzone w obszarze **Azure HDInsight Spark zadania**. Przełącz się do **lokalnie uruchomić** kartę.

    ![Lokalnej konfiguracji uruchamiania](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Zmienne środowiskowe](#prerequisite-for-windows): Jeśli ustawisz zmienna środowiskowa **HADOOP_HOME** do **C:\WinUtils**, można ją automatycznie wykrywa, że nie trzeba ręcznie dodać.
    - [Lokalizacja WinUtils.exe](#prerequisite-for-windows): Jeśli zmienna środowiskowa nie zostały ustawione, można znaleźć lokalizacji, klikając przycisk.
    - Wystarczy wybrać jedną z dwóch opcji, a nie są one wymagane w MacOS i Linux.
4. Można również ustawić konfiguracji ręcznie, przed wykonaniem lokalnego uruchamiania i lokalnego debugowania. Na poprzednim zrzucie ekranu wybierz znak plus (**+**). Następnie wybierz **Azure HDInsight Spark zadania** opcji. Wprowadź informacje dotyczące **nazwa**, **Nazwa klasy głównym** można zapisać, a następnie kliknij przycisk uruchamiania lokalnego.

### <a name="scenario-3-perform-local-debugging"></a>Scenariusz 3: Wykonaj debugowanie lokalne
1. Otwórz **SparkCore_wasbloTest** skryptu, ustaw punkty przerwania.
2. Kliknij prawym przyciskiem myszy edytora skryptów, a następnie wybierz opcję **debugowania 'XXX [Spark zadania]'** przeprowadzić debugowania lokalnego.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Dowiedz się, jak wykonać zdalnego uruchom i debugowanie
### <a name="scenario-1-perform-remote-run"></a>Scenariusz 1: Wykonania zdalnego wykonywania

1. Aby uzyskać dostęp do **Edytuj konfiguracje** menu, wybierz ikonę w prawym górnym rogu. Z tego menu można utworzyć lub edytować konfiguracji do zdalnego debugowania.

   ![Edytuj konfiguracje](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

2. W **konfiguracji uruchomienia/debugowania** oknie dialogowym wybierz znak plus (**+**). Następnie wybierz **Azure HDInsight Spark zadania** opcji.

   ![Dodawanie nowej konfiguracji](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
3. Przełącz się do **zdalnie uruchamiać w klastrze** kartę. Wprowadź informacje dotyczące **nazwa**, **klastra Spark**, i **Nazwa klasy głównym**. Następnie wybierz **konfiguracji zaawansowanej**. Nasze narzędzia obsługi debugowania z **modułów**. **NumExectors**, wartość domyślna to 5. Czy lepiej nie ustawisz wyższej niż 3.

   ![Uruchom konfiguracje debugowania](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

4. W **Spark przesyłanie Advanced Configuration** okno dialogowe, wybierz opcję **Spark włączenia debugowania zdalnego**. Wprowadź nazwę użytkownika SSH, a następnie wprowadź hasło lub użyć pliku klucza prywatnego. Aby zapisać konfigurację, zaznacz **OK**. Jeśli chcesz przeprowadzić debugowania zdalnego, należy ustawić go. Nie istnieje potrzeba aby ustawić ją, jeśli chcesz użyć zdalnego Uruchom.

   ![Włączanie debugowania zdalnego Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

5. Konfiguracja jest teraz zapisywane o nazwie podane. Aby wyświetlić szczegóły konfiguracji, wybierz nazwę konfiguracji. Aby wprowadzić zmiany, wybierz **Edytuj konfiguracje**. 

6. Po ukończeniu ustawienia konfiguracji, można uruchomić projektu na zdalny klaster lub przeprowadzić debugowania zdalnego.
   
   ![Przycisk uruchamiania zdalnego](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

7. Kliknij przycisk **rozłączenia** przycisku, który pojawia się w lewym panelu w dziennikach przesyłania. Jednak nadal działa w wewnętrznej bazie danych.

   ![Przycisk uruchamiania zdalnego](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Scenariusz 2: Wykonaj debugowanie zdalne
1. Ustawianie punktów podziału, a następnie kliknij przycisk **zdalnego debugowania** ikony. Różnica w stosunku do zdalnego przesyłania jest wymagających nazwę użytkownika i hasło SSH, można skonfigurować.

   ![Wybierz ikonę debugowania](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

2. Podczas wykonywania programu osiągnie punkt podziału, zobacz **sterownika** kartę i dwa **Moduł wykonujący** tabulatorów w **debugera** okienka. Wybierz **Program Wznów** ikonę, aby kontynuować uruchamianie kodu, który następnie osiągnie następnego punktu przerwania. Musisz przełączyć się do prawidłowego **Moduł wykonujący** kartę, aby znaleźć wykonujący docelowego debugowania. Dzienniki wykonywania można wyświetlać w odpowiedniej **konsoli** kartę.

   ![Karta debugowanie](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scenariusz 3: Wykonywania zdalnego debugowania i naprawienie usterki
1. Skonfiguruj dwa punkty podziału, a następnie wybierz **debugowania** ikonę, aby rozpocząć proces zdalnego debugowania.

2. Kod zatrzymuje się w pierwszym punkcie podziału, a parametr, a informacje o zmiennych są wyświetlane w **zmienne** okienka. 

3. Wybierz **Program Wznów** ikonę, aby kontynuować. Kod zatrzymuje się na drugi punkt. Wyjątek jest zgodnie z oczekiwaniami.

   ![Zgłoś błąd](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

4. Wybierz **Program Wznów** ikonę ponownie. **HDInsight Spark przesyłanie** okno wyświetla błąd "zadanie uruchamianie nie powiodło się".

   ![Przesyłanie błędów](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

5. Aby dynamicznie aktualizować wartość zmiennej przy użyciu IntelliJ debugowania możliwości, wybierz **debugowania** ponownie. **Zmienne** okienko zostanie wyświetlony ponownie. 

6. Kliknij prawym przyciskiem myszy docelowy **debugowania** , a następnie wybierz **ustaw wartość**. Następnie wprowadź nową wartość dla zmiennej. Następnie wybierz **Enter** można zapisać wartości. 

   ![Ustawiono wartość](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

7. Wybierz **Program Wznów** ikonę, aby kontynuować do uruchomienia programu. Tym razem nie jest wyjątek. Widać, że projekt zostanie pomyślnie uruchomiony bez żadnych wyjątków.

   ![Debugowanie bez wyjątku](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Następne kroki
* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstracja
* Tworzenie projektu Scala (klip wideo): [tworzenie Spark Scala aplikacji](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Debugowanie zdalne (klip wideo): [użyj narzędzi Azure dla IntelliJ debugowanie aplikacji Spark zdalnie w klastrze usługi HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenariusze
* [Platforma Spark w usłudze BI: wykonaj interakcyjna analiza danych przy użyciu platformy Spark w usłudze HDInsight z narzędzi do analizy Biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Spark przy użyciu Machine Learning: Korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Zestaw narzędzi platformy Azure dla IntelliJ umożliwia tworzenie aplikacji Spark dla klastra usługi HDInsight](apache-spark-intellij-tool-plugin.md)
* [Debugowanie aplikacji Spark zdalnie za pośrednictwem sieci VPN przy użyciu zestawu narzędzi Azure for IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Użyj narzędzia HDInsight Tools for IntelliJ z Hortonworks piaskownicy](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Użyj narzędzia HDInsight Tools w zestawie narzędzi Azure dla programu Eclipse tworzenie aplikacji Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
