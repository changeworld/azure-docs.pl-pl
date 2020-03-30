---
title: Debug Spark zadanie z IntelliJ Azure Toolkit (wersja zapoznawcza) - HDInsight
description: Wskazówki dotyczące używania narzędzi HDInsight w usłudze Azure Toolkit for IntelliJ do debugowania aplikacji
keywords: debug zdalnie intellij, zdalne debugowanie intellij, ssh, intellij, hdinsight, debug intellij, debugowanie
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1a0a6cf5a26854539dc4bbb0ae0254bbf08dad1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494593"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Debugowanie zadania iskry awarii za pomocą zestawu narzędzi Azure dla intellij (wersja zapoznawcza)

Ten artykuł zawiera wskazówki krok po kroku dotyczące używania narzędzi HDInsight w [usłudze Azure Toolkit dla intellij](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) do uruchamiania aplikacji **debugowania awarii platformy Spark.**

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw Oracle Java Development](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). W tym samouczku jest używany język Java w wersji 8.0.202.
  
* IntelliJ IDEA. W tym artykule użyto [technologii IntelliJ IDEA Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Zobacz [Installing the Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable) (Instalowanie zestawu Azure Toolkit for IntelliJ).

* Połącz się z klastrem HDInsight. Zobacz [Łączenie się z klastrem HDInsight](apache-spark-intellij-tool-plugin.md).

* Eksplorator magazynu platformy Microsoft Azure. Zobacz [Pobieranie Eksploratora magazynu platformy Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Tworzenie projektu z szablonem debugowania

Utwórz projekt spark2.3.2, aby kontynuować debugowanie błędów, weź przykładowy plik debugowania zadania niepowodzenie w tym dokumencie.

1. Otwórz środowisko IntelliJ IDEA. Otwórz okno **Nowy projekt.**

   a. Wybierz pozycję **Azure Spark/HDInsight** w okienku po lewej stronie.

   b. Z okna głównego wybierz **pozycję Spark Project with Failure Task Debugging Sample(Preview)(Scala).**

     ![Intellij Tworzenie projektu debugowania](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   d. Wybierz **pozycję Dalej**.

2. W oknie **Nowy projekt** wykonaj następujące czynności:

   ![Intellij Nowy projekt wybierz wersję Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Wprowadź nazwę projektu i lokalizację projektu.

   b. Z listy rozwijanej **Zestawu SDK projektu** wybierz pozycję **Java 1.8** dla klastra **platformy Spark 2.3.2.**

   d. Z listy rozwijanej **Wersja platformy Spark** wybierz opcję Spark **2.3.2(Scala 2.11.8).**

   d. Wybierz **pozycję Zakończ**.

3. Wybierz **src** > **main** > **scala,** aby otworzyć kod w projekcie. W tym przykładzie użyto skryptu **AgeMean_Div().**

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Uruchamianie aplikacji Spark Scala/Java w klastrze HDInsight

Utwórz aplikację Spark Scala/Java, a następnie uruchom aplikację w klastrze Platformy Spark, wykonując następujące kroki:

1. Kliknij **pozycję Dodaj konfigurację,** aby otworzyć okno **Konfiguracje uruchamiania/debugowania.**

   ![HDI Intellij Dodaj konfigurację](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. W oknie dialogowym **Uruchamianie/Debugowanie konfiguracji** **+** wybierz znak plus ( ). Następnie wybierz opcję **Apache Spark on HDInsight.**

   ![Intellij Dodaj nową konfigurację](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Przełączanie do **uruchamiania zdalnego na karcie Klaster.** Wprowadź informacje o **nazwie,** **klastrze platformy Spark**i nazwie klasy **głównej**. Nasze narzędzia obsługują debugowanie z **Executors**. **NumExectors**, wartość domyślna to 5, a lepiej nie ustawić wyższe niż 3. Aby skrócić czas wykonywania, można dodać **spark.yarn.maxAppAttempts** do **konfiguracji zadania** i ustawić wartość na 1. Kliknij przycisk **OK,** aby zapisać konfigurację.

   ![Intellij Uruchom konfiguracje debugowania nowe](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Konfiguracja jest teraz zapisywana z podaną nazwą. Aby wyświetlić szczegóły konfiguracji, wybierz nazwę konfiguracji. Aby wprowadzić zmiany, wybierz pozycję **Edytuj konfiguracje**.

5. Po zakończeniu ustawień konfiguracji można uruchomić projekt względem klastra zdalnego.

   ![Intellij Debug Remote Spark Job Remote Run przycisk uruchamiania zdalnego uruchomienia](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Identyfikator aplikacji można sprawdzić w oknie danych wyjściowych.

   ![Wynik zdalnego uruchomienia zadania zdalnego uruchomienia platformy Iskra Intellij](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Pobieranie nieudanego profilu zadania

Jeśli przesłanie zadania nie powiedzie się, można pobrać profil zadania nie powiodło się do komputera lokalnego do dalszego debugowania.

1. Otwórz **Eksploratora magazynu platformy Microsoft Azure**, zlokalizuj konto HDInsight klastra dla zadania, które nie powiodło się, pobierz nieudane zasoby zadania z odpowiedniej lokalizacji: **\\\hdp\spark2-events .spark-failures\\\<application ID>** do folderu lokalnego. Okno **działań** wyświetli postęp pobierania.

   ![Niepowodzenie pobierania przez Eksploratora usługi Azure Storage](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Sukces pobierania w Eksploratorze usługi Azure Storage](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Konfigurowanie lokalnego środowiska debugowania i debugowania w przypadku awarii

1. Otwórz oryginalny projekt lub utwórz nowy projekt i skojarz go z oryginalnym kodem źródłowym. Tylko wersja spark2.3.2 jest obsługiwana dla debugowania błędów obecnie.

1. W intellij idea, utwórz plik konfiguracyjny **debugowania awarii platformy Spark,** wybierz plik FTD z wcześniej pobranych zasobów zadania, które nie powiodło się dla pola **lokalizacja kontekstu awarii zadania platformy Spark.**

   ![konfiguracja awarii krety](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Kliknij przycisk uruchom lokalnie na pasku narzędzi, błąd zostanie wyświetlony w oknie Uruchom.

   ![konfiguracja run-failure1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![konfiguracja run-failure2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Ustaw punkt przerwania, jak wskazuje dziennik, a następnie kliknij przycisk lokalnego debugowania, aby wykonać lokalne debugowanie, tak jak normalne projekty Scala / Java w IntelliJ.

1. Po debugowaniu, jeśli projekt zakończy się pomyślnie, można ponownie przesłać zadanie nie powiodło się do iskry w klastrze HDInsight.

## <a name="next-steps"></a><a name="seealso"></a>Następne kroki

* [Omówienie: Debug Apache Spark applications](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demonstracja

* Utwórz projekt Scala (wideo): [Tworzenie aplikacji Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Zdalne debugowanie (wideo): [używanie zestawu narzędzi Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark w klastrze USŁUGI HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z analizą biznesową: do analizy danych interaktywnych przy użyciu platformy Spark w funkcji HDInsight za pomocą narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight do analizy temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika strony internetowej przy użyciu platformy Apache Spark w hdinsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie aplikacji Apache Spark dla klastra USŁUGI HDInsight za pomocą zestawu narzędzi Azure toolkit for IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Użyj zestawu narzędzi Azure toolkit dla intellij do zdalnego debugowania aplikacji Apache Spark za pośrednictwem sieci VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z narzędzi HDInsight tools dla intellij z piaskownicą Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Tworzenie aplikacji Apache Spark za pomocą narzędzi HDInsight Tools w usłudze Azure Toolkit for Eclipse](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Używanie notebooków Apache Zeppelin z klastrem Apache Spark w programie HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notebooka Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
