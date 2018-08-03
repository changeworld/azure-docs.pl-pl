---
title: 'Zestaw narzędzi platformy Azure dla środowiska IntelliJ: tworzenie aplikacji Spark dla klastra usługi HDInsight | Dokumentacja firmy Microsoft'
description: Azure Toolkit for IntelliJ umożliwia tworzenie aplikacji Spark napisanych w języku Scala i przesyłanie ich do klastra usługi HDInsight Spark.
services: hdinsight
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/25/2017
ms.author: maxluk
ms.openlocfilehash: 891a568af7892048eb84646acbf495f32ddd00b2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427424"
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Azure Toolkit for IntelliJ umożliwia tworzenie aplikacji Spark dla klastra usługi HDInsight

Użyj zestawu narzędzi platformy Azure dla środowiska IntelliJ wtyczki do tworzenia aplikacji Spark napisanych w języku Scala, a następnie prześlij je do klastra usługi HDInsight Spark, bezpośrednio z poziomu zintegrowanego środowiska projektowego (IDE). Wtyczki można użyć, na kilka sposobów:

* Programuj i przesyłaj aplikację Scala Spark w klastrze usługi HDInsight Spark.
* Dostęp do zasobów klastra usługi HDInsight Spark.
* Twórz i uruchamiaj lokalnie aplikację Scala Spark.

Aby utworzyć projekt, należy wyświetlić [tworzenie aplikacji Spark przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) wideo.

> [!IMPORTANT]
> Ta wtyczka można użyć, aby utworzyć i przesłać aplikacji tylko w przypadku klastra HDInsight Spark, w systemie Linux.
> 

## <a name="prerequisites"></a>Wymagania wstępne

- Klaster Apache Spark HDInsight w systemie Linux. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
- Oracle Java Development Kit. Można zainstalować go z [witryny internetowej Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- IntelliJ IDEA. W tym artykule używa wersji 2017.1. Można zainstalować go z [JetBrains witryny sieci Web](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Zainstaluj zestaw narzędzi platformy Azure dla środowiska IntelliJ
Aby uzyskać instrukcje dotyczące instalacji, zobacz [zainstalować Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="get-started"></a>Rozpoczęcie pracy
Użytkownik może być [Zaloguj się do subskrypcji platformy Azure](#sign-in-to-your-azure-subscription), lub [połączenia klastra HDInsight](#link-a-cluster) przy użyciu narzędzia Ambari nazwy użytkownika/hasła lub domeny przyłączone poświadczeń do uruchomienia.


## <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do Twojej subskrypcji platformy Azure.

1. Uruchom środowisko IDE IntelliJ, a następnie Otwórz Eksplorator usługi Azure. Na **widoku** menu, wybierz opcję **narzędzie Windows**, a następnie wybierz pozycję **Eksploratora usługi Azure**.
       
   ![Łącze Eksploratora usługi Azure](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

1. Kliknij prawym przyciskiem myszy **Azure** węzeł, a następnie wybierz **Sign In**.

1. W **Azure Sign In** okno dialogowe, wybierz opcję **Zaloguj**, a następnie wprowadź swoje poświadczenia platformy Azure.

    ![Okno dialogowe Azure Sign In](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

1. Po użytkownik jest zalogowany, **subskrypcje wybierz** okno dialogowe wyświetla listę wszystkich subskrypcji platformy Azure, które są skojarzone z poświadczeniami. Wybierz **wybierz** przycisku.

    ![Okno dialogowe Wybieranie subskrypcji](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

1. Na **Eksploratora usługi Azure** kartę, a następnie rozwiń **HDInsight** do wyświetlania klastrów HDInsight Spark, które znajdują się w Twojej subskrypcji.
   
    ![Klastry HDInsight Spark, w Eksploratorze usługi Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

1. Aby wyświetlić zasoby (na przykład kont magazynu), które są skojarzone z klastrem, można rozwinąć węzła nazwy klastra.
   
    ![Rozwinięty węzeł nazwy klastra](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Połącz klaster
Możesz połączyć normalny klaster HDInsight przy użyciu nazwy użytkownika systemu Ambari zarządzane. Podobnie, dla klastra HDInsight przyłączone do domeny, możesz połączyć przy użyciu domeny i nazwę użytkownika, takich jak user1@contoso.com.

1. Wybierz **połączenia klastra** z **Eksploratora usługi Azure**.

   ![menu kontekstowe klaster w linku](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)


1. Wprowadź **nazwa klastra**, **nazwa_użytkownika** i **hasło**. Należy sprawdzić nazwę użytkownika i hasło, jeśli masz niepowodzenie uwierzytelniania. Opcjonalnie dodaj konto magazynu, klucz magazynu, a następnie wybierz kontener z kontenera magazynu. Informacje dotyczące magazynu jest Eksplorator usługi storage w drzewie po lewej stronie
   
   ![okno dialogowe klastra łącza](./media/apache-spark-intellij-tool-plugin/link-a-cluster-dialog.png)

   > [!NOTE]
   > Używamy klucz połączonego magazynu, nazwę użytkownika i hasło, jeśli klaster w subskrypcji platformy Azure i połączone w klaster.
   > ![Eksplorator usługi Storage w programie IntelliJ](./media/apache-spark-intellij-tool-plugin/storage-explorer-in-IntelliJ.png)

   
1. Możesz zobaczyć połączone w klaster **HDInsight** węzła, jeśli dane wejściowe jest odpowiednia. Możesz teraz przesłać aplikację do tego klastra połączonych.

   ![połączone klastra](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. Możesz również można odłączyć elementy od klastra z **Eksploratora usługi Azure**.
   
   ![Odłączono klastra](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Uruchom aplikację Spark Scala w klastrze usługi HDInsight Spark

1. Uruchom środowisko IntelliJ IDEA, a następnie utwórz projekt. W oknie dialogowym **Nowy projekt** wykonaj następujące czynności: 

   a. Wybierz pozycję **HDInsight** > **Spark w usłudze HDInsight (Scala)**.

   b. Z listy **Narzędzie kompilacji** wybierz jedną z następujących pozycji odpowiednio do potrzeb:

      * **Maven** — w celu obsługi kreatora tworzenia projektu Scala
      * **SBT** — na potrzeby zarządzania zależnościami i kompilacji projektu Scala

    ![Okno dialogowe Nowy projekt](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

1. Wybierz opcję **Dalej**.

1. Kreator tworzenia projektu Scala automatycznie wykrywa, czy wtyczka Scala została zainstalowana. Wybierz pozycję **Zainstaluj**.

   ![Sprawdzanie wtyczki Scala](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

1. Aby pobrać wtyczkę Scala, wybierz przycisk **OK**. Postępuj zgodnie z instrukcjami, aby ponownie uruchomić środowisko IntelliJ. 

   ![Okno dialogowe instalacji wtyczki Scala](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

1. W oknie **Nowy projekt** wykonaj następujące czynności:  

    ![Wybieranie zestawu SDK platformy Spark](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Wprowadź nazwę i lokalizację projektu.

   b. Z listy rozwijanej **Zestaw SDK projektu** wybierz pozycję **Java 1.8** dla klastra Spark 2.x lub pozycję **Java 1.7** dla klastra Spark 1.x.

   c. Na liście rozwijanej **Wersja Spark** kreator tworzenia projektu Scala integruje poprawną wersję dla zestawu SDK platformy Spark i zestawu SDK Scala. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie używana jest wersja **Spark 2.0.2 (Scala 2.11.8)**.

1. Wybierz pozycję **Finish** (Zakończ).

1. Spark project automatycznie tworzy artefaktu. Aby wyświetlić artefakt, wykonaj następujące czynności:

   a. Na **pliku** menu, wybierz opcję **struktury projektu**.

   b. W **struktury projektu** okno dialogowe, wybierz opcję **artefaktów** Aby wyświetlić artefakt domyślny, który zostanie utworzony. Można również utworzyć własne artefaktu, wybierając znak plus (**+**).

      ![W oknie dialogowym, informacje o artefaktu](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
1. Dodaj kod źródłowy aplikacji, wykonując następujące czynności:

   a. W obszarze Eksplorator projektów kliknij prawym przyciskiem myszy **src**, wskaż polecenie **nowy**, a następnie wybierz pozycję **klasy Scala**.
      
      ![Polecenia do tworzenia klasy Scala w Eksploratorze projektu](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. W **Utwórz nową klasę Scala** okna dialogowego pole, wprowadź nazwę, wybierz **obiektu** w **rodzaj** , a następnie wybierz **OK**.
      
      ![Utwórz nową klasę Scala — okno dialogowe](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. W **MyClusterApp.scala** plików, wklej następujący kod. Kod odczytuje dane z HVAC.csv (dostępne we wszystkich klastrach HDInsight Spark), pobiera wiersze, które mają tylko jedna cyfra w siódmej kolumny w pliku CSV i zapisuje dane wyjściowe do **/HVACOut** w ramach domyślnego kontenera magazynu dla klastra.

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

1. Uruchom aplikację w klastrze usługi HDInsight Spark, wykonując następujące czynności:

   a. W obszarze Eksplorator projektów kliknij prawym przyciskiem myszy nazwę projektu, a następnie wybierz **przesyłanie aplikacji Spark HDInsight**.
      
      ![Aplikacja Spark przesłać do polecenia HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   b. Monit o podanie poświadczeń subskrypcji platformy Azure. W **przesyłania Spark** okno dialogowe, podaj następujące wartości, a następnie wybierz pozycję **przesyłania**.
      
      * Aby uzyskać **(tylko system Linux) klastry usługi Spark**, wybierz klaster HDInsight Spark, na którym chcesz uruchomić aplikację.

      * Wybierz artefakt z projektu IntelliJ lub wybierz jedną z dysku twardego.

      * W **Nazwa klasy Main** wybierz wielokropek (**...** ) wybierz głównej klasy w kodzie źródłowym aplikacji, a następnie wybierz pozycję **OK**.

        ![W oknie dialogowym Wybierz klasę Main](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * Ponieważ kod aplikacji, w tym przykładzie nie wymagają argumentów wiersza polecenia lub odwoływać się do plików lub plikach JAR, pozostałe pola można pozostawić puste. Po podaniu wszystkich informacji w oknie dialogowym powinny zgodnie z poniższym obrazem.
        
        ![Okno dialogowe przesyłania platformy Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. **Przesyłania Spark** karta w dolnej części okna należy rozpocząć wyświetlanie postępu. Można je również zatrzymać aplikację, wybierając czerwony przycisk w **przesyłania Spark** okna.
      
     ![W oknie przesyłania platformy Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Aby dowiedzieć się, jak dostęp do danych wyjściowych zadania, zobacz "dostępu i zarządzać klastry HDInsight Spark przy użyciu platformy Azure Toolkit for IntelliJ" sekcję w dalszej części tego artykułu.

## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Lokalne lub zdalne debugowanie aplikacji Spark w klastrze usługi HDInsight 
Zalecamy również innym sposobem przesyłanie aplikacji Spark w klastrze. Możesz to zrobić przez ustawienie parametrów w **konfiguracji uruchomienia/debugowania** IDE. Aby uzyskać więcej informacji, zobacz [debugowanie aplikacji Spark lokalnie lub zdalnie w klastrze usługi HDInsight przy użyciu Azure Toolkit for IntelliJ przez protokół SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Dostęp i zarządzać klastry HDInsight Spark przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ
Za pomocą zestawu narzędzi platformy Azure dla środowiska IntelliJ, można wykonywać różne operacje.

### <a name="access-the-job-view"></a>Dostęp do widoku zadania
1. W Eksploratorze usługi Azure, rozwiń węzeł **HDInsight**, rozwiń nazwę klastra platformy Spark, a następnie wybierz **zadań**.  

    ![Zadania widoku węzła](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

1. W okienku po prawej stronie **widok zadania Spark** karcie są wyświetlane wszystkie aplikacje, które zostały uruchomione w klastrze. Wybierz nazwę aplikacji, dla którego chcesz wyświetlić więcej szczegółowych informacji.

    ![Szczegóły aplikacji](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)
    >Uwaga
    >

1. Aby wyświetlić podstawowe informacje o zadaniu uruchomione, umieść kursor Graf zadania. Aby wyświetlić wykres etapów i informacje, które generuje każde zadanie, wybierz węzeł na wykresie zadania.

    ![Szczegóły etap zadania](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

1. Aby wyświetlić często używane dzienników, taką jak *Stderr sterownika*, *Stdout sterownika*, i *informacji o katalogu*, wybierz opcję **dziennika** kartę.

    ![Szczegóły dziennika](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

1. Można również wyświetlić historię Spark interfejsu użytkownika i Interfejsie użytkownika YARN (na poziomie aplikacji), wybierając link u góry okna.

### <a name="access-the-spark-history-server"></a>Dostęp serwer historii platformy Spark
1. W Eksploratorze usługi Azure, rozwiń węzeł **HDInsight**, kliknij prawym przyciskiem myszy nazwę klastra platformy Spark, a następnie wybierz **Otwórz użytkownika usługi Historia Spark**. 

1. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra, które zostały określone podczas konfigurowania klastra.

1. Na pulpicie nawigacyjnym serwer historii platformy Spark nazwę aplikacji można użyć do wyszukania aplikacja właśnie został uruchomiony. W poprzednim kodzie, należy określić nazwę aplikacji przy użyciu `val conf = new SparkConf().setAppName("MyClusterApp")`. W związku z tym, Twoja nazwa aplikacji Spark jest **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Uruchom portal systemu Ambari
1. W Eksploratorze usługi Azure, rozwiń węzeł **HDInsight**, kliknij prawym przyciskiem myszy nazwę klastra platformy Spark, a następnie wybierz **Otwórz Portal zarządzania klastrem (Ambari)**. 

1. Po wyświetleniu monitu wprowadź poświadczenia administratora dla klastra. Te poświadczenia są określane podczas procesu konfigurowania klastra.

### <a name="manage-azure-subscriptions"></a>Zarządzanie subskrypcjami platformy Azure
Domyślnie program Azure Toolkit for IntelliJ Wyświetla listę klastrów Spark z Twoich subskrypcji platformy Azure. Jeśli to konieczne, można określić subskrypcji, dla których chcesz uzyskać dostęp. 

1. W Eksploratorze usługi Azure, kliknij prawym przyciskiem myszy **Azure** węzła głównego, a następnie wybierz **Zarządzaj subskrypcjami**. 

1. W oknie dialogowym, usuń zaznaczenie pola wyboru obok subskrypcji, dla których nie chcesz uzyskać dostęp, a następnie wybierz **Zamknij**. Możesz również wybrać **Wyloguj** aby Wyloguj się ze swoją subskrypcją platformy Azure.

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Konwertowanie istniejących aplikacji IntelliJ IDEA, aby użyć zestawu narzędzi platformy Azure dla środowiska IntelliJ
Można przekonwertować istniejących Spark Scala aplikacje utworzone w ramach IntelliJ IDEA, aby był zgodny z Azure Toolkit for IntelliJ. Następnie można wtyczki przesyłanie aplikacji do klastra usługi HDInsight Spark.

1. Dla istniejących aplikacji Spark Scala, która została utworzona przy użyciu rozwiązania IntelliJ IDEA Otwórz plik .iml skojarzone.

1. W katalogu głównym poziom jest **modułu** element podobnie do poniższego:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Edytuj element, aby dodać `UniqueKey="HDInsightTool"` tak, aby **modułu** element wygląda podobnie do następującej:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Zapisz zmiany. Aplikacja powinno być teraz zgodne z Azure Toolkit for IntelliJ. Można ją przetestować, klikając prawym przyciskiem myszy nazwę projektu w Eksploratorze projektów. Dodano opcję menu podręcznym **przesyłanie aplikacji Spark HDInsight**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Błąd podczas uruchamiania lokalnego: *Użyj większy rozmiar sterty*
W Spark 1.6 Jeśli korzystasz z zestawu SDK Java 32-bitowego podczas uruchamiania lokalnego, mogą wystąpić następujące błędy:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Te błędy się tak zdarzyć, ponieważ rozmiar stosu nie jest wystarczająco duży dla platformy Spark do uruchomienia. Platforma Spark wymaga co najmniej 471 MB. (Aby uzyskać więcej informacji, zobacz [SPARK 12081](https://issues.apache.org/jira/browse/SPARK-12081).) Jedno rozwiązanie proste jest korzystanie z zestawu SDK Java 64-bitowych. Możesz również zmienić ustawienia maszyny wirtualnej Java w programie IntelliJ przez dodanie następujących opcji:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Dodanie opcji do pola "Opcje maszyny Wirtualnej" w programie IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>Często zadawane pytania
Gdy połączenie klastra, I sugerować, musisz podać poświadczenia magazynu.

![Połączenia klastra, podaj poświadczenia magazynu](./media/apache-spark-intellij-tool-plugin/link-cluster-with-storage-credential-intellij.png)

Istnieją dwa tryby można przesłać zadania. Jeśli nie podano poświadczeń magazynu, tryb wsadowy będzie służyć do przesyłania zadania. W przeciwnym razie będzie używany tryb interakcyjny. Jeśli klaster jest zajęty, możesz otrzymać poniższego błędu.

![Intellij komunikat o błędzie podczas klastra zajęty](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Intellij komunikat o błędzie podczas klastra zajęty](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Opinie i znane problemy
Wyświetlanie danych wyjściowych platformy Spark bezpośrednio nie jest obecnie obsługiwana.

Jeśli masz jakiekolwiek sugestie lub opinię lub wystąpią problemy, korzystając z tego dodatku, Wyślij wiadomość e-mail hdivstool@microsoft.com.

## <a name="seealso"></a>Następne kroki
* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstracja
* Tworzenie projektu Scala (wideo): [tworzenia aplikacji Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Zdalne debugowanie (wideo): [użycia Azure Toolkit for IntelliJ debugowanie aplikacji Spark zdalnie w klastrze HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenariusze
* [Platforma Spark i usługa Power BI: wykonaj interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Debugowanie aplikacji Spark zdalnie za pośrednictwem sieci VPN przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Debugowanie aplikacji Spark zdalnie za pośrednictwem protokołu SSH przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Korzystaj z narzędzi HDInsight dla programu IntelliJ z Piaskownicą Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Narzędzia HDInsight znajdujące się w zestaw narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji platformy Spark](apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
