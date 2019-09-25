---
title: 'Azure Toolkit for IntelliJ: Zdalnie Debuguj aplikacje Spark za pośrednictwem protokołu SSH '
description: Wskazówki krok po kroku dotyczące korzystania z narzędzi usługi HDInsight w Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji w klastrach usługi HDInsight za pośrednictwem protokołu SSH
keywords: debugowanie zdalne IntelliJ, debugowanie zdalne IntelliJ, SSH, IntelliJ, HDInsight, debugowanie IntelliJ, debugowanie
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 1f3f08385ac4f7044f4e6c4e4110be701e8f576d
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266224"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Debugowanie Apache Spark aplikacji w klastrze usługi HDInsight z Azure Toolkit for IntelliJ za pośrednictwem protokołu SSH

Ten artykuł zawiera wskazówki krok po kroku dotyczące korzystania z narzędzi usługi HDInsight w [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) do zdalnego debugowania aplikacji w klastrze usługi HDInsight. Aby debugować projekt, możesz również wyświetlić [aplikacje do debugowania usługi HDInsight Spark za pomocą Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) wideo.

**Wymagania wstępne**
* **Narzędzia usługi HDInsight w Azure Toolkit for IntelliJ**. To narzędzie jest częścią Azure Toolkit for IntelliJ. Aby uzyskać więcej informacji, zobacz [Install Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). I **Azure Toolkit for IntelliJ**. Użyj tego zestawu narzędzi do tworzenia aplikacji Apache Spark dla klastra usługi HDInsight. Aby uzyskać więcej informacji, postępuj zgodnie z instrukcjami w temacie [Use Azure Toolkit for IntelliJ, aby utworzyć aplikacje Apache Spark dla klastra usługi HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **Usługa protokołu SSH w usłudze HDInsight z zarządzaniem nazwami użytkowników i hasłami**. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) i [Używanie tunelowania SSH do uzyskiwania dostępu do Ambari internetowego interfejsu użytkownika, JobHistory, NameNode, Apache Oozie i innych witryn sieci Web interfejsów użytkownika](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Dowiedz się, jak przeprowadzić lokalne uruchamianie i debugowanie
### <a name="scenario-1-create-a-spark-scala-application"></a>Scenariusz 1. Tworzenie aplikacji platformy Spark Scala 

1. Uruchom środowisko IntelliJ IDEA, a następnie utwórz projekt. W oknie dialogowym **Nowy projekt** wykonaj następujące czynności:

   a. Wybierz pozycję **Azure Spark/HDInsight**. 

   b. Wybierz szablon Java lub Scala w oparciu o Twoje preferencje. Wybierz jedną z następujących opcji:

   - **Projekt platformy Spark (Java)**

   - **Projekt platformy Spark (Scala)**

   - **Projekt Spark z przykładami (Scala)**

   - **Projekt platformy Spark z przykładami debugowania zadania błędu (wersja zapoznawcza) (Scala)**

     W tym przykładzie zastosowano **projekt Spark z szablonem Samples (Scala)** .

   c. Z listy **Narzędzie kompilacji** wybierz jedną z następujących pozycji odpowiednio do potrzeb:

   - **Maven**do obsługi Kreatora tworzenia projektów Scala.

   - **SBT**, do zarządzania zależnościami i kompilowania dla projektu Scala.

     ![IntelliJ Utwórz nowy projekt Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Wybierz opcję **Dalej**.

1. W następnym oknie **Nowy projekt** wykonaj następujące czynności:

   ![IntelliJ nowy projekt wybierz wersję platformy Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Wprowadź nazwę projektu i lokalizację projektu.

   b. Z listy rozwijanej **zestaw SDK projektu** wybierz pozycję **Java 1,8** dla klastra **Spark 2. x** lub wybierz pozycję **Java 1,7** dla klastra **Spark 1. x** .

   c. Z listy rozwijanej **wersja platformy Spark** Kreator tworzenia projektu Scala integruje poprawną wersję zestawu Spark SDK i scala SDK. Jeśli wersja klastra Spark jest wcześniejsza niż 2,0, wybierz pozycję **Spark 1. x**. W przeciwnym razie wybierz pozycję **Spark 2. x.** W tym przykładzie używana jest wersja **Spark 2.0.2 (Scala 2.11.8)** .

   d. Wybierz pozycję **Finish** (Zakończ).

1. Wybierz pozycję **src** > **Main** > **Scala** , aby otworzyć swój kod w projekcie. Ten przykład używa skryptu **SparkCore_wasbloTest** .

### <a name="prerequisite-for-windows"></a>Wymagania wstępne dla systemu Windows
Podczas uruchamiania lokalnej aplikacji platformy Spark Scala na komputerze z systemem Windows może wystąpić wyjątek, zgodnie z opisem w platformie [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Wyjątek występuje, ponieważ w systemie Windows brakuje pliku WinUtils. exe.

Aby rozwiązać ten problem, [Pobierz plik wykonywalny](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do lokalizacji takiej jak **C:\WinUtils\bin**. Następnie Dodaj zmienną środowiskową **HADOOP_HOME**i ustaw wartość zmiennej na **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Scenariusz 2. Wykonaj przebieg lokalny

1. Otwórz skrypt **SparkCore_wasbloTest** , kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz opcję **Uruchom "[zadanie Spark] XXX"** , aby wykonać przebieg lokalny.

1. Po zakończeniu lokalnego uruchomienia można zobaczyć plik wyjściowy Zapisz w bieżącym **__domyślnym__** **danych** > Eksploratora projektu.

    ![Wynik lokalnego uruchomienia projektu IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Nasze narzędzia ustawili domyślną konfigurację uruchamiania lokalnego podczas przeprowadzania lokalnego uruchamiania i debugowania lokalnego. Otwórz konfigurację **[Spark w usłudze HDInsight] XXX** w prawym górnym rogu, zobaczysz, że **[Spark w usłudze HDInsight] XXX** już utworzone w obszarze **Apache Spark w usłudze HDInsight**. Przejdź do karty **uruchamiania lokalnego** .

    ![IntelliJ uruchamianie konfiguracji debugowania — uruchomienie lokalne](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Zmienne środowiskowe](#prerequisite-for-windows): Jeśli ustawisz już zmienną środowiskową system **HADOOP_HOME** na **C:\WinUtils**, może ona automatycznie wykryć, że nie trzeba ręcznie dodawać.
    - [WinUtils. exe — lokalizacja](#prerequisite-for-windows): Jeśli nie ustawisz zmiennej środowiskowej systemowej, możesz znaleźć tę lokalizację, klikając jej przycisk.
    - Po prostu wybierz jedną z dwóch opcji i nie są one potrzebne w systemach MacOS i Linux.

1. Konfigurację można również ustawić ręcznie przed wykonaniem operacji lokalnego uruchamiania i debugowania lokalnego. Na poprzednim zrzucie ekranu wybierz znak plus ( **+** ). Następnie wybierz opcję **Apache Spark w usłudze HDInsight** . Wprowadź informacje w polu **Nazwa**, **Nazwa klasy głównej** do zapisania, a następnie kliknij przycisk Uruchom lokalnie.

### <a name="scenario-3-perform-local-debugging"></a>Scenariusz 3. Wykonaj debugowanie lokalne
1. Otwórz skrypt **SparkCore_wasbloTest** , ustaw punkty przerwania.
1. Kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz opcję **Debuguj "[Spark w usłudze HDInsight] XXX"** , aby przeprowadzić debugowanie lokalne.

## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Dowiedz się, jak wykonać zdalne uruchamianie i debugowanie
### <a name="scenario-1-perform-remote-run"></a>Scenariusz 1. Wykonaj przebieg zdalny

1. Aby uzyskać dostęp do menu **Edytuj konfiguracje** , wybierz ikonę w prawym górnym rogu. Z tego menu można utworzyć lub edytować konfiguracje zdalnego debugowania.

   ![HDI IntelliJ Edytuj konfiguracje](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png)

1. W oknie dialogowym **konfiguracje uruchamiania/debugowania** wybierz znak plus ( **+** ). Następnie wybierz opcję **Apache Spark w usłudze HDInsight** .

   ![IntelliJ Dodaj nową konfigurację](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Przełącz się do **zdalnego uruchamiania na karcie klaster** . Wprowadź informacje o **nazwie**, **klastrze Spark**i **nazwie klasy głównej**. Następnie kliknij pozycję **Konfiguracja zaawansowana (debugowanie zdalne)** . Nasze narzędzia obsługują debugowanie za pomocą **modułów wykonujących**. **NumExectors**wartość domyślna to 5. Lepiej nie ustawiono wartości większej niż 3.

   ![IntelliJ uruchamiaj konfiguracje debugowania](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. W części **Konfiguracja zaawansowana (debugowanie zdalne)** wybierz pozycję **Włącz debugowanie zdalne Spark**. Wprowadź nazwę użytkownika SSH, a następnie wprowadź hasło lub użyj pliku klucza prywatnego. Jeśli chcesz przeprowadzić debugowanie zdalne, musisz je ustawić. Nie trzeba ustawiać go, jeśli chcesz tylko korzystać z zdalnego uruchomienia.

   ![IntelliJ zaawansowaną konfigurację Włącz debugowanie zdalne Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Konfiguracja jest teraz zapisywana o podanej nazwie. Aby wyświetlić szczegóły konfiguracji, wybierz nazwę konfiguracji. Aby wprowadzić zmiany, wybierz pozycję **Edytuj konfiguracje**.

1. Po zakończeniu ustawień konfiguracji można uruchomić projekt względem klastra zdalnego lub wykonać zdalne debugowanie.

   ![Zdalny przycisk uruchamiania zdalnego zadania Spark IntelliJ debugowania](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Kliknij przycisk **Rozłącz** , aby dzienniki przesłane nie były wyświetlane w lewym panelu. Jednak nadal działa w zapleczu.

   ![Wynik zdalnego uruchomienia zdalnego zadania Spark debugowania IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>Scenariusz 2. Wykonaj debugowanie zdalne
1. Skonfiguruj punkty przerwania, a następnie kliknij ikonę **debugowania zdalnego** . Różnica polega na tym, że należy skonfigurować nazwę użytkownika/hasło SSH.

   ![Ikona debugowania zdalnego zadania platformy Spark debugowania IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Gdy wykonanie programu dociera do punktu przerwania, zobaczysz kartę **sterownika** i dwie karty **wykonawcze** w okienku **debuger** . Wybierz ikonę **Wznów program** , aby kontynuować uruchamianie kodu, który następnie osiągnie następny punkt przerwania. Aby znaleźć docelowy moduł wykonujący debugowanie , należy przełączyć się na poprawną kartę programu wykonującego. Dzienniki wykonywania można wyświetlić na odpowiedniej karcie **konsoli** .

   ![Karta debugowania zdalnego zadania platformy Spark debugowania IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scenariusz 3. Wykonaj debugowanie zdalne i naprawianie błędów

1. Skonfiguruj dwa punkty przerwania, a następnie wybierz ikonę **debugowania** , aby rozpocząć proces zdalnego debugowania.

1. Kod zostaje zatrzymany w pierwszym punkcie przerwania, a informacje o parametrze i zmiennej są wyświetlane w okienku **zmienne** .

1. Wybierz ikonę **Wznów program** , aby kontynuować. Kod zostaje zatrzymany w drugim punkcie. Wyjątek jest przechwytywany zgodnie z oczekiwaniami.

   ![Błąd rzutowania zdalnego zadania Spark debugowania IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Wybierz ponownie ikonę **programu Resume** . W oknie **przesyłanie usługi HDInsight Spark** zostanie wyświetlony komunikat o błędzie "uruchomienie zadania nie powiodło się".

   ![Błąd podczas wysyłania zdalnego zadania Spark debugowania IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Aby dynamicznie zaktualizować wartość zmiennej przy użyciu funkcji debugowania IntelliJ, należy ponownie wybrać **Debuguj** . Zostanie wyświetlone okienko **zmienne** .

1. Kliknij prawym przyciskiem myszy obiekt docelowy na karcie **debugowanie** , a następnie wybierz pozycję **Ustaw wartość**. Następnie wprowadź nową wartość dla zmiennej. Następnie wybierz klawisz **Enter** , aby zapisać wartość.

   ![Wartość zestawu zadań zdalnej Spark dla debugowania IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Wybierz ikonę **Wznów program** , aby kontynuować uruchamianie programu. Tym razem nie jest przechwytywany żaden wyjątek. Można zobaczyć, że projekt zostanie uruchomiony pomyślnie bez żadnych wyjątków.

   ![Debuguj zdalne zadanie Spark IntelliJ debugowania bez wyjątku](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Następne kroki

* [Podsumowanie Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Pokaz
* Utwórz projekt Scala (wideo): [Tworzenie Apache Spark aplikacji Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Debugowanie zdalne (wideo): [Użyj Azure Toolkit for IntelliJ, aby zdalnie debugować aplikacje Apache Spark w klastrze usługi HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenariusze
* [Apache Spark z usługą BI: Przeprowadzanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: Korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury budowania przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: Korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Tworzenie aplikacji Apache Spark dla klastra usługi HDInsight za pomocą Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Używanie Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark za pośrednictwem sieci VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Tworzenie aplikacji Apache Spark przy użyciu narzędzi usługi HDInsight w Azure Toolkit for Eclipse](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzaj zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
