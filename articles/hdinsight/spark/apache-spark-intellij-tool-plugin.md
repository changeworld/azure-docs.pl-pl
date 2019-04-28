---
title: 'Zestaw narzędzi platformy Azure dla środowiska IntelliJ: Tworzenie aplikacji Spark dla klastra usługi HDInsight '
description: Azure Toolkit for IntelliJ umożliwia tworzenie aplikacji Spark napisanych w języku Scala i przesyłanie ich do klastra usługi HDInsight Spark.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: maxluk
ms.openlocfilehash: 682b2266442f3aba6696385862844e1b4b93aa3d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124258"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Azure Toolkit for IntelliJ umożliwia tworzenie aplikacji platformy Apache Spark dla klastra usługi HDInsight

Użyj zestawu narzędzi platformy Azure dla środowiska IntelliJ wtyczki do tworzenia [platformy Apache Spark](https://spark.apache.org/) aplikacje napisane w [Scala](https://www.scala-lang.org/)oraz przesyłanie ich do klastra usługi HDInsight Spark bezpośrednio w rozwoju IntelliJ zintegrowane środowiska (IDE). Wtyczki można użyć, na kilka sposobów:

* Programuj i przesyłaj aplikację Scala Spark w klastrze usługi HDInsight Spark.
* Dostęp do zasobów klastra usługi HDInsight Spark.
* Twórz i uruchamiaj lokalnie aplikację Scala Spark.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* [Zestaw Oracle Java Development](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  W tym samouczku jest używany język Java w wersji 8.0.202.
* IntelliJ IDEA. W tym artykule jest używane środowisko [IntelliJ IDEA Community w wersji  2018.3.4](https://www.jetbrains.com/idea/download/).
* Azure Toolkit for IntelliJ.  Zobacz [Installing the Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable) (Instalowanie zestawu Azure Toolkit for IntelliJ).
* WINUTILS.EXE.  Zobacz [problemy z uruchamianiem usługi Hadoop na Windows](https://wiki.apache.org/hadoop/WindowsProblems).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalowanie wtyczki Scala dla środowiska IntelliJ IDEA
Wykonaj następujące kroki, aby zainstalować wtyczkę Scala:

1. Otwórz środowisko IntelliJ IDEA.

2. Na ekranie powitalnym przejdź do pozycji **Configure (Konfiguruj)** > **Plugins (Wtyczki)**, aby otworzyć okno **Plugins (Wtyczki)**.
   
    ![Włączanie wtyczki Scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. Wybierz pozycję **Install** (Instaluj) dla wtyczki Scala, która zostanie wyświetlona w nowym oknie.  

    ![Instalowanie wtyczki Scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Po pomyślnym zainstalowaniu wtyczki musisz ponownie uruchomić środowisko IDE.


## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Tworzenie aplikacji Spark Scala dla klastra usługi HDInsight Spark

1. Uruchom środowisko IntelliJ IDEA i wybierz pozycję **Create New Project** (Utwórz nowy projekt), aby otworzyć okno **New Project** (Nowy projekt).

2. Wybierz pozycję **Azure Spark/HDInsight** w okienku po lewej stronie.

3. Wybierz pozycję **Spark Project (Scala)** w głównym oknie.

4. Z listy rozwijanej **Build tool** (Narzędzie kompilacji), wybierz jedną z następujących pozycji:
   * **Maven** — w celu obsługi kreatora tworzenia projektu Scala.
   * **SBT** — na potrzeby zarządzania zależnościami i kompilacji projektu Scala.

     ![Okno dialogowe Nowy projekt](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Wybierz opcję **Dalej**.

6. W oknie **New Project** (Nowy projekt) podaj następujące informacje:  

    |  Właściwość   | Opis   |  
    | ----- | ----- |  
    |Project name (Nazwa projektu)| Wprowadź nazwę.  W tym samouczku użyto regionu `myApp`.|  
    |Lokalizacja&nbsp;projektu| Wprowadź żądaną lokalizację do zapisania projektu.|
    |Zestaw SDK projektu| Może to być puste na pierwszym korzystanie z ROZWIĄZANIEM.  Wybierz pozycję **New...** (Nowy...) i przejdź do swojego zestawu JDK.|
    |Wersja platformy Spark|Kreator tworzenia integruje poprawną wersję dla zestawów Spark SDK i Scala SDK. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie używana jest wersja **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Wybieranie zestawu SDK platformy Spark](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. Wybierz pozycję **Finish** (Zakończ).  Może upłynąć kilka minut, zanim projekt staje się dostępna.

8. Spark project automatycznie tworzy artefaktu. Aby wyświetlić artefakt, wykonaj następujące czynności:

   a. Na pasku menu, przejdź do **pliku** > **struktury projektu...** .

   b. Z **struktury projektu** wybierz **artefaktów**.  

   c. Wybierz **anulować** po wyświetlający artefakt.

      ![W oknie dialogowym, informacje o artefaktu](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. Dodaj kod źródłowy aplikacji, wykonując następujące czynności:

    a. W projekcie, przejdź do **myApp** > **src** > **głównego** > **scala**.  

    b. Kliknij prawym przyciskiem myszy **scala**, a następnie przejdź do **New** > **klasy Scala**.

   ![Polecenia do tworzenia zawartości języków Scala klasy z projektu](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. W **Utwórz nową klasę Scala** okna dialogowego pole, wprowadź nazwę, wybierz **obiektu** w **rodzaj** listy rozwijanej, a następnie wybierz **OK**.

     ![Utwórz nową klasę Scala — okno dialogowe](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. **MyApp.scala** pliku następnie zostanie otwarty w widoku głównego. Zastąp kod domyślnego kodu znajdują się poniżej:  

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object myApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("myApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasbs:///HVACOut")
            }
    
        }

    Kod odczytuje dane z HVAC.csv (dostępne we wszystkich klastrach HDInsight Spark), pobiera wiersze, które mają tylko jedna cyfra w siódmej kolumny w pliku CSV i zapisuje dane wyjściowe do `/HVACOut` w ramach domyślnego kontenera magazynu dla klastra.

## <a name="connect-to-your-hdinsight-cluster"></a>Nawiąż połączenie z klastrem usługi HDInsight
Użytkownik może być [Zaloguj się do subskrypcji platformy Azure](#sign-in-to-your-azure-subscription), lub [połączenia klastra HDInsight](#link-a-cluster) przy użyciu narzędzia Ambari nazwy użytkownika/hasła lub domeny przyłączone poświadczenia do połączenia z klastrem HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do Twojej subskrypcji platformy Azure.

1. Na pasku menu, przejdź do **widoku** > **narzędzie Windows** > **Eksploratora usługi Azure**.
       
   ![Łącze Eksploratora usługi Azure](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. W Eksploratorze usługi Azure, kliknij prawym przyciskiem myszy **Azure** węzeł, a następnie wybierz **Sign In**.

3. W **Azure Sign In** okno dialogowe, wybierz opcję **Zaloguj**, a następnie wprowadź swoje poświadczenia platformy Azure.

    ![Okno dialogowe Azure Sign In](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Po użytkownik jest zalogowany, **subskrypcje wybierz** okno dialogowe wyświetla listę wszystkich subskrypcji platformy Azure, które są skojarzone z poświadczeniami. Wybierz swoją subskrypcję, a następnie wybierz pozycję **wybierz** przycisku.

    ![Okno dialogowe Wybieranie subskrypcji](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. Z **Eksploratora usługi Azure**, rozwiń węzeł **HDInsight** do wyświetlania klastrów HDInsight Spark, które znajdują się w Twojej subskrypcji.

    ![Klastry HDInsight Spark, w Eksploratorze usługi Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Aby wyświetlić zasoby (na przykład kont magazynu), które są skojarzone z klastrem, można rozwinąć węzła nazwy klastra.

    ![Rozwinięty węzeł nazwy klastra](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>Połącz klaster
Możesz połączyć klaster usługi HDInsight przy użyciu nazwy użytkownika systemu Apache Ambari zarządzane. Podobnie, dla klastra HDInsight przyłączone do domeny, możesz połączyć przy użyciu domeny i nazwę użytkownika, takich jak user1@contoso.com. Możesz również połączyć klaster usługi Livy.

1. Na pasku menu, przejdź do **widoku** > **narzędzie Windows** > **Eksploratora usługi Azure**.

2. W Eksploratorze usługi Azure, kliknij prawym przyciskiem myszy **HDInsight** węzeł, a następnie wybierz **klastra łącze**.

   ![menu kontekstowe klaster w linku](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. Opcje dostępne w **klastra łącze** okna będą się różnić w zależności od tego, w którym wartość, możesz wybrać spośród **typ zasobu Linku** listy rozwijanej.  Wprowadź wartości, a następnie wybierz pozycję **OK**.

    * **Klaster HDInsight**  
  
        |Właściwość |Wartość |
        |----|----|
        |Typ zasobu linku|Wybierz **klastra HDInsight** z listy rozwijanej.|
        |Nazwa/adres URL klastra| Wprowadź nazwę klastra.|
        |Typ uwierzytelnienia| Pozostaw **uwierzytelnianie podstawowe**|
        |Nazwa użytkownika| Wprowadź nazwę użytkownika klastra, wartość domyślna to administratora.|
        |Hasło| Wprowadź hasło dla nazwy użytkownika.|
    
        ![okno dialogowe klastra usługi hdinsight łącza](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy Service**  
  
        |Właściwość |Wartość |
        |----|----|
        |Typ zasobu linku|Wybierz **usługi Livy** z listy rozwijanej.|
        |Punkt końcowy usługi Livy| Wprowadź punkt końcowy usługi Livy|
        |Nazwa klastra| Wprowadź nazwę klastra.|
        |Punkt końcowy usługi yarn|Opcjonalny.|
        |Typ uwierzytelnienia| Pozostaw **uwierzytelnianie podstawowe**|
        |Nazwa użytkownika| Wprowadź nazwę użytkownika klastra, wartość domyślna to administratora.|
        |Hasło| Wprowadź hasło dla nazwy użytkownika.|

        ![Łączenie usługi livy klastra w oknie dialogowym](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

4. Możesz zobaczyć klastra połączonych z **HDInsight** węzła.

   ![połączone klastra](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

5. Możesz również można odłączyć elementy od klastra z **Eksploratora usługi Azure**.

   ![Odłączono klastra](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Uruchom aplikację Spark Scala w klastrze usługi HDInsight Spark
Po utworzeniu aplikacji w języku Scala, możesz go przesłać do klastra.

1. W projekcie, przejdź do **myApp** > **src** > **głównego** > **scala**  >  **myApp**.  Kliknij prawym przyciskiem myszy **myApp**i wybierz **przesyłanie aplikacji Spark** (prawdopodobnie będzie znajduje się w dolnej części listy).
    
      ![Aplikacja Spark przesłać do polecenia HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. W **przesyłanie aplikacji Spark** okno dialogowe, wybierz opcję **1. Spark on HDInsight**.

3. W **Upravit konfiguraci** okna, podaj następujące wartości, a następnie wybierz pozycję **OK**:

    |Właściwość |Wartość |
    |----|----|
    |Klastry Spark (tylko system Linux)|Wybierz klaster HDInsight Spark, na którym chcesz uruchomić aplikację.|
    |Wybierz artefakt do przesyłania|Pozostaw ustawienie domyślne.|
    |Nazwa główna klasa|Wartość domyślna to główna klasa z wybranego pliku. Klasę można zmienić, wybierając wielokropek (**...** ) i wybierając inną klasę.|
    |Zadania konfiguracji|Można zmienić domyślne klucze i/lub wartości. Aby uzyskać więcej informacji, zobacz [interfejsu API REST usługi Livy Apache](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Argumenty wiersza polecenia|Można wprowadzić argumentów rozdzielonych miejsca dla klasy głównej, jeśli to konieczne.|
    |Przywoływany w plikach JAR i pliki z odwołaniami|Ewentualne, możesz wprowadzić ścieżki odwołania plikach JAR i plików. Więcej informacji: [Konfiguracja platformy Apache Spark](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Zobacz też [sposób przekazywania zasobów do klastra](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Zadanie przekazywania magazynu|Rozwiń, aby wyświetlić dodatkowe opcje.|
    |Typ magazynu|Wybierz **używanie usługi Azure Blob do przekazania** z listy rozwijanej.|
    |Konto magazynu|Wprowadź nazwę konta magazynu.|
    |Klucz magazynu|Wprowadź klucz magazynu.|
    |Kontener magazynu|Wybierz kontener magazynu z listy rozwijanej raz **konta magazynu** i **klucza magazynu** został wprowadzony.|

    ![Okno dialogowe przesyłania platformy Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

4. Wybierz **SparkJobRun** przesłać projekt do wybranego klastra. **Zdalnego zadania Spark w klastrze** karty wyświetla postęp wykonywania zadania u dołu. Aby zatrzymać aplikację, należy kliknąć czerwony przycisk. Aby dowiedzieć się, jak dostęp do danych wyjściowych zadania, zobacz "dostępu i zarządzać klastry HDInsight Spark przy użyciu platformy Azure Toolkit for IntelliJ" sekcję w dalszej części tego artykułu.  
      
    ![W oknie przesyłania platformy Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Lokalne lub zdalne debugowanie aplikacji platformy Apache Spark w klastrze usługi HDInsight 
Zalecamy również innym sposobem przesyłanie aplikacji Spark w klastrze. Możesz to zrobić przez ustawienie parametrów w **konfiguracji uruchomienia/debugowania** IDE. Aby uzyskać więcej informacji, zobacz [debugowanie aplikacji platformy Apache Spark lokalnie lub zdalnie w klastrze usługi HDInsight przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ za pośrednictwem protokołu SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Dostęp i zarządzać klastry HDInsight Spark przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ
Za pomocą zestawu narzędzi platformy Azure dla środowiska IntelliJ, można wykonywać różne operacje.  Większość operacji są inicjowane z **Eksploratora usługi Azure**.  Na pasku menu, przejdź do **widoku** > **narzędzie Windows** > **Eksploratora usługi Azure**.

### <a name="access-the-job-view"></a>Dostęp do widoku zadania

1. W Eksploratorze usługi Azure, przejdź do **HDInsight** > \<klastra Your >> **zadań**.

    ![Zadania widoku węzła](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. W okienku po prawej stronie **widok zadania Spark** karcie są wyświetlane wszystkie aplikacje, które zostały uruchomione w klastrze. Wybierz nazwę aplikacji, dla którego chcesz wyświetlić więcej szczegółowych informacji.

    ![Szczegóły aplikacji](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Aby wyświetlić podstawowe informacje o zadaniu uruchomione, umieść kursor Graf zadania. Aby wyświetlić wykres etapów i informacje, które generuje każde zadanie, wybierz węzeł na wykresie zadania.

    ![Szczegóły etap zadania](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Aby wyświetlić często używane dzienników, taką jak *Stderr sterownika*, *Stdout sterownika*, i *informacji o katalogu*, wybierz opcję **dziennika** kartę.

    ![Szczegóły dziennika](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Można również wyświetlić historię Spark interfejsu użytkownika i Interfejsie użytkownika YARN (na poziomie aplikacji), wybierając link u góry okna.

### <a name="access-the-spark-history-server"></a>Dostęp serwer historii platformy Spark

1. Z poziomu Eksploratora usługi Azure, rozwiń węzeł **HDInsight**, kliknij prawym przyciskiem myszy nazwę klastra platformy Spark, a następnie wybierz **Otwórz użytkownika usługi Historia Spark**.  
2. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra, które zostały określone podczas konfigurowania klastra.

3. Na pulpicie nawigacyjnym serwer historii platformy Spark nazwę aplikacji można użyć do wyszukania aplikacja właśnie został uruchomiony. W poprzednim kodzie, należy określić nazwę aplikacji przy użyciu `val conf = new SparkConf().setAppName("myApp")`. W związku z tym, Twoja nazwa aplikacji Spark jest **myApp**.

### <a name="start-the-ambari-portal"></a>Uruchom portal systemu Ambari

1. Z poziomu Eksploratora usługi Azure, rozwiń węzeł **HDInsight**, kliknij prawym przyciskiem myszy nazwę klastra platformy Spark, a następnie wybierz **Otwórz klaster zarządzania Portal(Ambari)**.  

2. Po wyświetleniu monitu wprowadź poświadczenia administratora dla klastra. Te poświadczenia są określane podczas procesu konfigurowania klastra.

### <a name="manage-azure-subscriptions"></a>Zarządzanie subskrypcjami platformy Azure
Domyślnie program Azure Toolkit for IntelliJ Wyświetla listę klastrów Spark z Twoich subskrypcji platformy Azure. Jeśli to konieczne, można określić subskrypcji, dla których chcesz uzyskać dostęp.  

1. W Eksploratorze usługi Azure, kliknij prawym przyciskiem myszy **Azure** węzła głównego, a następnie wybierz **subskrypcje wybierz**.  

2. Z **subskrypcje wybierz** okna, wyczyść pola wyboru obok subskrypcji, nie mają dostępu do, a następnie wybierz pozycję **Zamknij**.

## <a name="spark-console"></a>Spark Console
Można uruchomić lokalnego Console(Scala) Spark lub uruchomić interakcyjne Console(Scala) sesja platformy Spark usługi Livy.

### <a name="spark-local-consolescala"></a>Console(Scala) lokalnej platformy Spark
Upewnij się, że zostały spełnione WINUTILS. Wymaganie wstępne EXE.

1. Na pasku menu, przejdź do **Uruchom** > **Edycja konfiguracji...** .

2. Z **konfiguracji uruchomienia/debugowania** okna, w okienku po lewej stronie przejdź do **platformy Apache Spark w HDInsight** > **myApp [Spark on HDInsight]**.

3. W oknie głównym wybierz **lokalnie uruchomić** kartę.

4. Podaj następujące wartości, a następnie wybierz **OK**:

    |Właściwość |Wartość |
    |----|----|
    |Główna klasa zadania|Wartość domyślna to główna klasa z wybranego pliku. Klasę można zmienić, wybierając wielokropek (**...** ) i wybierając inną klasę.|
    |Zmienne środowiskowe|Upewnij się, że wartość HADOOP_HOME jest poprawna.|
    |Lokalizacja WINUTILS.exe|Upewnij się, że ścieżka jest poprawna.|

    ![Konfiguracja zestawu w lokalnej konsoli](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. W projekcie, przejdź do **myApp** > **src** > **głównego** > **scala**  >  **myApp**.  

6. Na pasku menu, przejdź do **narzędzia** > **konsoli Spark** > **uruchamiania lokalnego Console(Scala) Spark**.

7. Następnie dwóch okien dialogowych mogą być wyświetlane było zadawać, czy chcesz automatycznie rozwiązać zależności. Jeśli tak, zaznacz **automatycznie naprawić**.

    ![Fix1 automatycznie platformy Spark](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Fix2 automatycznie platformy Spark](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. Konsoli powinien wyglądać podobnie do poniższej ilustracji. W typie okna konsoli `sc.appName`, a następnie naciśnij klawisze ctrl + Enter.  Wyświetlany jest wynik. Czerwony przycisk, możesz zakończyć działanie konsoli lokalnej.

    ![Wynik lokalnej konsoli](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Platforma Spark jest Console(Scala) interaktywnej sesji usługi Livy
Jest ona obsługiwana tylko na IntelliJ 2018.2 i 2018.3.

1. Na pasku menu, przejdź do **Uruchom** > **Edycja konfiguracji...** .

2. Z **konfiguracji uruchomienia/debugowania** okna, w okienku po lewej stronie przejdź do **platformy Apache Spark w HDInsight** > **myApp [Spark on HDInsight]**.

3. W oknie głównym wybierz **zdalnie uruchomić w klastrze** kartę.

4. Podaj następujące wartości, a następnie wybierz **OK**:

    |Właściwość |Wartość |
    |----|----|
    |Klastry Spark (tylko system Linux)|Wybierz klaster HDInsight Spark, na którym chcesz uruchomić aplikację.|
    |Nazwa główna klasa|Wartość domyślna to główna klasa z wybranego pliku. Klasę można zmienić, wybierając wielokropek (**...** ) i wybierając inną klasę.|

    ![Konfiguracja zestawu interakcyjnej konsoli](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. W projekcie, przejdź do **myApp** > **src** > **głównego** > **scala**  >  **myApp**.  

6. Na pasku menu, przejdź do **narzędzia** > **konsoli Spark** > **Console(Scala) sesji: uruchamianie usługi Livy Spark interaktywne**.

7. Konsoli powinien wyglądać podobnie do poniższej ilustracji. W typie okna konsoli `sc.appName`, a następnie naciśnij klawisze ctrl + Enter.  Wyświetlany jest wynik. Czerwony przycisk, możesz zakończyć działanie konsoli lokalnej.

    ![Wynik interakcyjnej konsoli](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Wyślij zaznaczenie do konsoli platformy Spark

Jest to wygodne, wysyłając jakiś kod w lokalnej konsoli lub usługi Livy interaktywnych sesji Console(Scala) przewiduje się wynik skryptu. Można wyróżnić kodu w pliku języka Scala, a następnie kliknij prawym przyciskiem myszy **Wyślij zaznaczenie do konsoli Spark**. Zaznaczony kod zostanie wysłane do konsoli i być wykonywane. Wynik pojawi się po kodzie w konsoli. Konsolę będzie sprawdzać błędy, jeśli istniejące.  

   ![Wyślij zaznaczenie do konsoli platformy Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Konwertowanie istniejących aplikacji IntelliJ IDEA, aby użyć zestawu narzędzi platformy Azure dla środowiska IntelliJ

Można przekonwertować istniejących Spark Scala aplikacje utworzone w ramach IntelliJ IDEA, aby był zgodny z Azure Toolkit for IntelliJ. Następnie można wtyczki przesyłanie aplikacji do klastra usługi HDInsight Spark.

1. Dla istniejących aplikacji Spark Scala, która została utworzona przy użyciu rozwiązania IntelliJ IDEA Otwórz plik .iml skojarzone.

1. W katalogu głównym poziom jest **modułu** element podobnie do poniższego:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Edytuj element, aby dodać `UniqueKey="HDInsightTool"` tak, aby **modułu** element wygląda podobnie do następującej:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Zapisz zmiany. Aplikacja powinno być teraz zgodne z Azure Toolkit for IntelliJ. Można ją przetestować, klikając prawym przyciskiem myszy nazwę projektu w projekcie. Dodano opcję menu podręcznym **przesyłanie aplikacji Spark HDInsight**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="error-in-local-run-use-a-larger-heap-size"></a>Błąd podczas uruchamiania lokalnego: *Użyj większego rozmiaru sterty*
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
Jeśli klaster jest zajęty, możesz otrzymać poniższego błędu.

![Intellij komunikat o błędzie podczas klastra zajęty](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Intellij komunikat o błędzie podczas klastra zajęty](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Opinie i znane problemy
Wyświetlanie danych wyjściowych platformy Spark bezpośrednio nie jest obecnie obsługiwana.

Jeśli masz jakiekolwiek sugestie lub opinię lub wystąpią problemy, korzystając z tego dodatku, Wyślij wiadomość e-mail hdivstool@microsoft.com.

## <a name="seealso"></a>Następne kroki
* [Omówienie: Platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstracja
* Utwórz projekt Scala (wideo): [Tworzenie aplikacji programu Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Debugowanie zdalne (wideo): [Debugowanie aplikacji platformy Apache Spark, zdalnie w klastrze HDInsight przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenariusze
* [Platforma Apache Spark przy użyciu Power BI: Wykonaj interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika witryny sieci Web przy użyciu platformy Apache Spark w HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Debugowanie aplikacji platformy Apache Spark, zdalnie za pośrednictwem sieci VPN przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Debugowanie aplikacji platformy Apache Spark, zdalnie za pośrednictwem protokołu SSH przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Korzystaj z narzędzi HDInsight dla programu IntelliJ z Piaskownicą Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Używanie narzędzia HDInsight w zestaw narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji platformy Apache Spark](apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
