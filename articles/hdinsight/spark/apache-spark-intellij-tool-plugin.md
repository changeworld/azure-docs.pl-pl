---
title: 'Zestaw narzędzi platformy Azure dla aplikacji IntelliJ: Spark — HDInsight'
description: Użyj zestawu narzędzi Azure toolkit for IntelliJ do tworzenia aplikacji Platformy Spark napisanych w scali i przesyłania ich do klastra platformy SPARK usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d80f4cb12c79519818f6eccb2bb565bac472b471
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397694"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Tworzenie aplikacji Apache Spark dla klastra HDInsight za pomocą zestawu narzędzi Azure toolkit for IntelliJ

W tym artykule pokazano, jak tworzyć aplikacje Platformy Apache Spark na platformie Azure HDInsight przy użyciu wtyczki **Azure Toolkit** dla intellij IDE. [Usługa Azure HDInsight](../hdinsight-overview.md) to zarządzana usługa analityczna typu open source w chmurze, która umożliwia korzystanie z platform open source, takich jak Hadoop, Apache Spark, Apache Hive i Apache Kafka.

Wtyczkę **Azure Toolkit** można użyć na kilka sposobów:

* Tworzenie i przesyłanie aplikacji Scala Spark do klastra platformy HDInsight Spark.
* Uzyskaj dostęp do zasobów klastra platformy Azure HDInsight Spark.
* Lokalnie twórz i uruchamiaj aplikację Scala Spark.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Korzystanie z zestawu narzędzi platformy Azure dla wtyczki IntelliJ
> * Tworzenie aplikacji Apache Spark
> * Przesyłanie aplikacji do klastra usługi Azure HDInsight

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Zestaw Oracle Java Development](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  W tym artykule użyto wersji Java 8.0.202.

* IntelliJ IDEA. Ten artykuł używa [IntelliJ IDEA Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Zobacz [Installing the Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable) (Instalowanie zestawu Azure Toolkit for IntelliJ).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalowanie wtyczki Scala dla środowiska IntelliJ IDEA

Wykonaj następujące kroki, aby zainstalować wtyczkę Scala:

1. Otwórz środowisko IntelliJ IDEA.

2. Na ekranie powitalnym przejdź do **pozycji Konfiguruj** > **wtyczki,** aby otworzyć okno **Wtyczki.**

    ![IntelliJ IDEA włącz wtyczkę scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Wybierz pozycję **Install** (Instaluj) dla wtyczki Scala, która zostanie wyświetlona w nowym oknie.  

    ![IntelliJ IDEA zainstalować wtyczkę scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Po pomyślnym zainstalowaniu wtyczki musisz ponownie uruchomić środowisko IDE.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Tworzenie aplikacji Spark Scala dla klastra HDInsight Spark

1. Uruchom środowisko IntelliJ IDEA i wybierz pozycję **Create New Project** (Utwórz nowy projekt), aby otworzyć okno **New Project** (Nowy projekt).

2. Wybierz pozycję **Azure Spark/HDInsight** w okienku po lewej stronie.

3. Wybierz pozycję **Spark Project (Scala)** w głównym oknie.

4. Z listy rozwijanej **Build tool** (Narzędzie kompilacji), wybierz jedną z następujących pozycji:
   * **Maven** — w celu obsługi kreatora tworzenia projektu Scala.
   * **SBT** — na potrzeby zarządzania zależnościami i kompilacji projektu Scala.

     ![IntelliJ IDEA Nowy projekt dialogowy](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Wybierz **pozycję Dalej**.

6. W oknie **New Project** (Nowy projekt) podaj następujące informacje:  

    |  Właściwość   | Opis   |  
    | ----- | ----- |  
    |Project name (Nazwa projektu)| Wprowadź nazwę.  W tym `myApp`artykule użyto pliku .|  
    |Lokalizacja&nbsp;projektu| Wprowadź żądaną lokalizację do zapisania projektu.|
    |Zestaw SDK projektu| Może to być puste przy pierwszym użyciu IDEA.  Wybierz pozycję **New...** (Nowy...) i przejdź do swojego zestawu JDK.|
    |Wersja platformy Spark|Kreator tworzenia integruje poprawną wersję dla zestawów Spark SDK i Scala SDK. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie używana jest wersja **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Wybieranie sdk Apache Spark](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Wybierz **pozycję Zakończ**.  Może upłynąć kilka minut, zanim projekt stanie się dostępny.

8. Projekt Spark automatycznie tworzy artefakt dla Ciebie. Aby wyświetlić artefakt, wykonaj następujące czynności:

   a. Na pasku menu przejdź do pozycji Struktura projektu **pliku...** > **Project Structure...**.

   b. W oknie **Struktura projektu** wybierz pozycję **Artefakty**.  

   d. Wybierz **pozycję Anuluj** po wyświetleniu artefaktu.

      ![Informacje o artefaktach w oknie dialogowym](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Dodaj kod źródłowy aplikacji, wykonując następujące czynności:

    a. Z projektu przejdź do **myApp** > **src** > **main** > **scala**.  

    b. Kliknij prawym przyciskiem myszy **scala**, a następnie przejdź do **nowej** > **klasy Scala**.

   ![Polecenia do tworzenia klasy Scala z projektu](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   d. W oknie dialogowym **Tworzenie nowej klasy scali** podaj nazwę, wybierz pozycję **Obiekt** na liście rozwijanej **Rodzaj,** a następnie wybierz przycisk **OK**.

     ![Okno dialogowe Tworzenie nowej klasy Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Następnie w widoku głównym zostanie otwarty plik **myApp.scala.** Zastąp domyślny kod kodem znalezionym poniżej:  

        ```scala
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
        ```

    Kod odczytuje dane z pliku HVAC.csv (dostępne we wszystkich klastrach platformy SPARK usługi HDInsight), pobiera wiersze, które mają `/HVACOut` tylko jedną cyfrę w siódmej kolumnie w pliku CSV, i zapisuje dane wyjściowe w domyślnym kontenerze magazynu dla klastra.

## <a name="connect-to-your-hdinsight-cluster"></a>Łączenie się z klastrem HDInsight

Użytkownik może [zalogować się do subskrypcji platformy Azure](#sign-in-to-your-azure-subscription)lub [połączyć klaster HDInsight](#link-a-cluster) przy użyciu nazwy użytkownika/hasła Ambari lub poświadczeń przyłączonych do domeny, aby połączyć się z klastrem USŁUGI HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do subskrypcji platformy Azure

1. Na pasku menu przejdź do **pozycji Wyświetl** > narzędzie**Eksploratora****Tool Windows** > Windows Azure .

   ![IntelliJ IDEA pokaż azure explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. W Eksploratorze platformy Azure kliknij prawym przyciskiem myszy węzeł **platformy Azure,** a następnie wybierz polecenie **Zaloguj się**.

   ![IntelliJ IDEA Explorer kliknij prawym przyciskiem myszy pozycję Azure](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. W oknie dialogowym **Logowanie do platformy Azure** wybierz pozycję **Logowanie urządzenia,** a następnie wybierz pozycję **Zaloguj**się .

    ![IntelliJ IDEA azure zaloguj się login urządzenia](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. W oknie dialogowym **Logowanie urządzenia platformy Azure** kliknij pozycję **Kopiuj&Otwórz**.

   ![IntelliJ IDEA azure logowania urządzenia](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. W interfejsie przeglądarki wklej kod, a następnie kliknij przycisk **Dalej**.

   ![Microsoft wprowadzić okno dialogowe kodu dla HDI](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Wprowadź poświadczenia platformy Azure, a następnie zamknij przeglądarkę.

   ![Microsoft wprowadź okno dialogowe poczty e-mail dla hdi](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. Po zalogowaniu się okno dialogowe **Wybierz subskrypcje** zawiera listę wszystkich subskrypcji platformy Azure, które są skojarzone z poświadczeniami. Wybierz subskrypcję, a następnie wybierz przycisk **Wybierz.**

    ![Okno dialogowe Select Subscriptions (Wybieranie subskrypcji)](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. Z **usługi Azure Explorer**rozwiń usługę **HDInsight,** aby wyświetlić klastry platformy SPARK usługi HDInsight, które znajdują się w subskrypcjach.

    ![Widok główny IntelliJ IDEA Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Aby wyświetlić zasoby (na przykład konta magazynu), które są skojarzone z klastrem, można dalej rozwijać węzeł nazwy klastra.

    ![Konta magazynu usługi Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Łączenie klastra

Klaster HDInsight można połączyć za pomocą zarządzanej nazwy użytkownika Apache Ambari. Podobnie w przypadku klastra HDInsight przyłączony do domeny można połączyć `user1@contoso.com`za pomocą domeny i nazwy użytkownika, takich jak . Można również połączyć klaster usługi Livy.

1. Na pasku menu przejdź do **pozycji Wyświetl** > narzędzie**Eksploratora****Tool Windows** > Windows Azure .

1. W Eksploratorze platformy Azure kliknij prawym przyciskiem myszy węzeł **HDInsight,** a następnie wybierz polecenie **Połącz klaster**.

   ![Menu kontekstowe klastra łączy usługi Azure Explorer](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. Dostępne opcje w oknie **Klaster łącza** różnią się w zależności od wartości wybranej z listy rozwijanej **Połącz typ zasobu.**  Wprowadź wartości, a następnie wybierz **przycisk OK**.

    * **Klaster HDInsight**  
  
        |Właściwość |Wartość |
        |----|----|
        |Połącz typ zasobu|Z listy rozwijanej wybierz **klaster HDInsight.**|
        |Nazwa/adres URL klastra| Wprowadź nazwę klastra.|
        |Typ uwierzytelniania| Pozostaw jako **uwierzytelnianie podstawowe**|
        |Nazwa użytkownika| Wprowadź nazwę użytkownika klastra, domyślnie jest administratorem.|
        |Hasło| Wprowadź hasło do nazwy użytkownika.|

        ![IntelliJ IDEA połączyć okno dialogowe klastra](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Serwis Livy**  
  
        |Właściwość |Wartość |
        |----|----|
        |Połącz typ zasobu|Z listy rozwijanej wybierz **pozycję Usługa Livy.**|
        |Punkt końcowy Livy| Wprowadź punkt końcowy Livy|
        |Nazwa klastra| Wprowadź nazwę klastra.|
        |Punkt końcowy przędzy|Element opcjonalny.|
        |Typ uwierzytelniania| Pozostaw jako **uwierzytelnianie podstawowe**|
        |Nazwa użytkownika| Wprowadź nazwę użytkownika klastra, domyślnie jest administratorem.|
        |Hasło| Wprowadź hasło do nazwy użytkownika.|

        ![IntelliJ IDEA link Livy klastra dialogowego](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. Połączony klaster można zobaczyć z węzła **HDInsight.**

   ![Połączony klaster Usługi Azure Explorer1](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. Można również odłączyć klaster od **Usługi Azure Explorer**.

   ![Niezłączony klaster Usługi Azure Explorer](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Uruchamianie aplikacji Spark Scala w klastrze hdinsight Spark

Po utworzeniu aplikacji Scala można ją przesłać do klastra.

1. Z projektu przejdź do **myApp** > **src** > **main** > **scala** > **myApp**.  Kliknij prawym przyciskiem myszy **Aplikację**i wybierz pozycję **Prześlij aplikację Spark** (prawdopodobnie znajdzie się ona u dołu listy).

      ![Polecenie Prześlij aplikację iskrę do usługi HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. W oknie dialogowym **Przesyłanie aplikacji platformy Spark** wybierz pozycję **1. Iskra na HDInsight**.

3. W oknie **Edytuj konfigurację** podaj następujące wartości, a następnie wybierz przycisk **OK:**

    |Właściwość |Wartość |
    |----|----|
    |Klastry iskrowe (tylko Linux)|Wybierz klaster platformy SPARK usługi HDInsight, na którym chcesz uruchomić aplikację.|
    |Wybierz artefakt do przesłania|Pozostaw ustawienie domyślne.|
    |Nazwa głównej klasy|Wartością domyślną jest klasa główna z wybranego pliku. Klasę można zmienić, wybierając wielokropek(**...**)  i wybierając inną klasę.|
    |Konfiguracje zadań|Można zmienić domyślne klucze i/lub wartości. Aby uzyskać więcej informacji, zobacz [Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html).|
    |Argumenty wiersza polecenia|W razie potrzeby można wprowadzić argumenty oddzielone spacją dla klasy głównej.|
    |Słoiki odniesienia i pliki odniesienia|Można wprowadzić ścieżki dla smąków i plików, do których istnieją odwołania. Można również przeglądać pliki w wirtualnym systemie plików platformy Azure, który obecnie obsługuje tylko klaster ADLS Gen 2. Aby uzyskać więcej informacji: [Konfiguracja platformy Spark Apache](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Zobacz też [Jak przekazywać zasoby do klastra](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Miejsce do przekazywania zadań|Rozwiń, aby wyświetlić dodatkowe opcje.|
    |Typ magazynu|Wybierz **pozycję Użyj obiektu blob platformy Azure, aby przekazać** je z listy rozwijanej.|
    |Konto magazynu|Wprowadź swoje konto magazynu.|
    |Klucz magazynu|Wprowadź klucz pamięci masowej.|
    |Kontener do przechowywania|Wybierz kontener magazynu z listy rozwijanej po wprowadzeniu **konta magazynu** i **klucza magazynu.**|

    ![Okno dialogowe Przesyłanie platformy Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Wybierz **Opcję SparkJobRun,** aby przesłać projekt do wybranego klastra. Na karcie **Zdalne zadanie iskrzenie w klastrze** jest wyświetlany postęp wykonywania zadania u dołu. Aplikację można zatrzymać, klikając czerwony przycisk. Aby dowiedzieć się, jak uzyskać dostęp do danych wyjściowych zadania, zobacz sekcję "Dostęp i zarządzanie klastrami platformy Spark usługi HDInsight przy użyciu zestawu narzędzi Azure toolkit for IntelliJ" w dalszej części tego artykułu.  

    ![Apache Spark Okno składania](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Debugowanie aplikacji Platformy Spark apache lokalnie lub zdalnie w klastrze HDInsight

Firma Microsoft zaleca również inny sposób przesyłania aplikacji Platformy Spark do klastra. Można to zrobić, ustawiając parametry w **konfiguracji uruchamiania/debugowania** IDE. Aby uzyskać więcej informacji, zobacz [Debug Apache Spark aplikacji lokalnie lub zdalnie w klastrze HDInsight z azure toolkit dla IntelliJ przez SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Uzyskiwanie dostępu do klastrów platformy Spark usługi HDInsight i zarządzanie nimi przy użyciu zestawu narzędzi Azure dla technologii IntelliJ

Można wykonywać różne operacje przy użyciu narzędzia Azure Toolkit dla IntelliJ.  Większość operacji jest inicjowana z **usługi Azure Explorer**.  Na pasku menu przejdź do **pozycji Wyświetl** > narzędzie**Eksploratora****Tool Windows** > Windows Azure .

### <a name="access-the-job-view"></a>Dostęp do widoku zadania

1. Z Eksploratora Platformy Azure przejdź do funkcji **HDInsight** > \<Twój klaster> > **zadania.**

    ![Węzeł widoku zadania eksploratora Platformy IntelliJ](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. W prawym okienku karta **Widok zadania platformy Spark** wyświetla wszystkie aplikacje uruchomione w klastrze. Wybierz nazwę aplikacji, dla której chcesz wyświetlić więcej szczegółów.

    ![Szczegóły aplikacji spark Job View](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Aby wyświetlić podstawowe informacje o pracy, umieść wskaźnik myszy na wykresie zadań. Aby wyświetlić wykres etapów i informacje generowane przez każde zadanie, wybierz węzeł na wykresie zadania.

    ![Szczegóły etapu zadania spark Job View](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Aby wyświetlić często używane dzienniki, takie jak *Driver Stderr*, *Driver Stdout*i *Directory Info*, wybierz kartę **Dziennik.**

    ![Szczegóły dziennika widoku zadania platformy Spark](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. Można również wyświetlić interfejs użytkownika historii platformy Spark i interfejs użytkownika YARN (na poziomie aplikacji), wybierając łącze w górnej części okna.

### <a name="access-the-spark-history-server"></a>Dostęp do serwera historii platformy Spark

1. W eksploratorze platformy Azure rozwiń pozycję **HDInsight**, kliknij prawym przyciskiem myszy nazwę klastra platformy Spark, a następnie wybierz polecenie **Otwórz interfejs użytkownika historii iskier**.  
2. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra, które zostały określone podczas konfigurowania klastra.

3. Na pulpicie nawigacyjnym serwera historii platformy Spark można użyć nazwy aplikacji, aby wyszukać aplikację, która właśnie została ukończona. W poprzednim kodzie można ustawić nazwę `val conf = new SparkConf().setAppName("myApp")`aplikacji za pomocą programu . Dlatego nazwa aplikacji Spark to **myApp**.

### <a name="start-the-ambari-portal"></a>Uruchamianie portalu Ambari

1. W eksploratorze platformy Azure rozwiń pozycję **HDInsight**, kliknij prawym przyciskiem myszy nazwę klastra Platformy Spark, a następnie wybierz polecenie **Otwórz portal zarządzania klastrami (Ambari).**  

2. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra. Te poświadczenia zostały określone podczas procesu instalacji klastra.

### <a name="manage-azure-subscriptions"></a>Zarządzanie subskrypcjami platformy Azure

Domyślnie zestaw narzędzi platformy Azure dla technologii IntelliJ zawiera listę klastrów platformy Spark ze wszystkich subskrypcji platformy Azure. W razie potrzeby można określić subskrypcje, do których chcesz uzyskać dostęp.  

1. W Eksploratorze platformy Azure kliknij prawym przyciskiem myszy węzeł główny **platformy Azure,** a następnie wybierz pozycję **Wybierz subskrypcje**.  

2. W oknie **Wybierz subskrypcje wyczyść** pola wyboru obok subskrypcji, do których nie chcesz uzyskiwać dostępu, a następnie wybierz pozycję **Zamknij**.

## <a name="spark-console"></a>Konsola iskierki

Możesz uruchomić platformę Spark Local Console (Scala) lub uruchomić interaktywną konsolę sesji Spark Livy (Scala).

### <a name="spark-local-consolescala"></a>Lokalna konsola Spark (Scala)

Upewnij się, że jesteś zadowolony z WINUTILS. Warunek wstępny EXE.

1. Na pasku menu przejdź do **pozycji Uruchom** > **konfiguracje edycji...**.

2. W oknie **Konfiguracje uruchamiania/debugowania** w lewym okienku przejdź do **aplikacji Apache Spark on HDInsight** > **[Spark on HDInsight] myApp**.

3. W oknie głównym wybierz kartę **Uruchom lokalnie.**

4. Podaj następujące wartości, a następnie wybierz **przycisk OK:**

    |Właściwość |Wartość |
    |----|----|
    |Klasa główna zadania|Wartością domyślną jest klasa główna z wybranego pliku. Klasę można zmienić, wybierając wielokropek(**...**)  i wybierając inną klasę.|
    |Zmienne środowiskowe|Upewnij się, że wartość HADOOP_HOME jest poprawna.|
    |Lokalizacja winutils.exe|Upewnij się, że ścieżka jest poprawna.|

    ![Konfiguracja zestawu konsoli lokalnej](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. Z projektu przejdź do **myApp** > **src** > **main** > **scala** > **myApp**.  

6. Na pasku menu przejdź do pozycji **Narzędzia** > **Platformy Spark Console** > **Run Spark Local Console (Scala).**

7. Następnie mogą być wyświetlane dwa okna dialogowe z pytaniem, czy chcesz automatycznie naprawić zależności. Jeśli tak, wybierz **opcję Automatyczne naprawianie**.

    ![IntelliJ IDEA Spark Auto Fix dialog1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark Auto Fix dialog2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. Konsola powinna wyglądać podobnie do poniższego zdjęcia. W oknie konsoli `sc.appName`wpisz , a następnie naciśnij klawisze ctrl+Enter.  Wynik zostanie wyświetlony. Konsolę lokalną można zakończyć, klikając czerwony przycisk.

    ![Wynik konsoli lokalnej IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Interaktywna konsola sesji Spark Livy (Scala)

1. Na pasku menu przejdź do **pozycji Uruchom** > **konfiguracje edycji...**.

2. W oknie **Konfiguracje uruchamiania/debugowania** w lewym okienku przejdź do **aplikacji Apache Spark on HDInsight** > **[Spark on HDInsight] myApp**.

3. W oknie głównym wybierz pozycję **Remotely Run in Cluster** tab.From the main window, select the Remotely Run in Cluster tab.

4. Podaj następujące wartości, a następnie wybierz **przycisk OK:**

    |Właściwość |Wartość |
    |----|----|
    |Klastry iskrowe (tylko Linux)|Wybierz klaster platformy SPARK usługi HDInsight, na którym chcesz uruchomić aplikację.|
    |Nazwa głównej klasy|Wartością domyślną jest klasa główna z wybranego pliku. Klasę można zmienić, wybierając wielokropek(**...**)  i wybierając inną klasę.|

    ![Konfiguracja zestawu konsoli interaktywnej](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. Z projektu przejdź do **myApp** > **src** > **main** > **scala** > **myApp**.  

6. Na pasku menu przejdź do pozycji **Narzędzia** > **Spark Console** > **Run Spark Livy Interactive Session Console (Scala).**

7. Konsola powinna wyglądać podobnie do poniższego zdjęcia. W oknie konsoli `sc.appName`wpisz , a następnie naciśnij klawisze ctrl+Enter.  Wynik zostanie wyświetlony. Konsolę lokalną można zakończyć, klikając czerwony przycisk.

    ![IntelliJ IDEA Interaktywny wynik konsoli](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Wyślij zaznaczenie do konsoli platformy Spark

Jest to wygodne, aby przewidzieć wynik skryptu, wysyłając kod do lokalnej konsoli lub Livy Interactive Session Console (Scala). Możesz wyróżnić kod w pliku Scala, a następnie kliknąć prawym przyciskiem myszy **pozycję Wyślij zaznaczenie do konsoli platformy Spark**. Wybrany kod zostanie wysłany do konsoli i zostanie wykonany. Wynik zostanie wyświetlony po kodzie w konsoli. Konsola sprawdzi błędy, jeśli istnieją.  

   ![Wyślij zaznaczenie do konsoli platformy Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integracja z HDInsight Identity Broker (HIB) 

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Połącz się z klastrem USŁUGI HDInsight ESP za pomocą Id Broker (HIB)

Możesz wykonać normalne kroki, aby zalogować się do subskrypcji platformy Azure, aby połączyć się z klastrem USŁUGI HDInsight ESP za pomocą brokera identyfikatorów (HIB). Po zalogowaniu się zostanie wyświetlona lista klastrów w Eksploratorze platformy Azure. Aby uzyskać więcej instrukcji, zobacz [Łączenie się z klastrem HDInsight](#connect-to-your-hdinsight-cluster).

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Uruchamianie aplikacji Spark Scala w klastrze USŁUGI HDInsight ESP za pomocą brokera Id Broker (HIB)

Możesz wykonać normalne kroki, aby przesłać zadanie do klastra ESP usługi HDInsight za pomocą Id Broker (HIB). Aby uzyskać więcej instrukcji, zapoznaj się [z artykułem Uruchom aplikację Spark Scala w klastrze platformy HDInsight Spark.](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)

Przekazujemy niezbędne pliki do folderu o nazwie z kontem logowania i możesz zobaczyć ścieżkę przekazywania w pliku konfiguracyjnym.

   ![ścieżka przekazywania w konfiguracji](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Konsola Spark w klastrze ESP USŁUGI HDInsight z brokerem Id Broker (HIB)

Możesz uruchomić platformę Spark Local Console (Scala) lub uruchomić interaktywną konsolę sesji Spark Livy (Scala) w klastrze ESP USŁUGI HDInsight z brokerem identyfikatorów (HIB). Więcej instrukcji można znaleźć w [konsoli platformy Spark Console.](#spark-console)

   > [!NOTE]  
   > W przypadku klastra USŁUGI HDInsight ESP z brokerem identyfikatorów (HIB) [zdalne łączenie klastra](#link-a-cluster) i [debugowanie aplikacji Platformy Spark](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) nie jest obecnie obsługiwane.

## <a name="reader-only-role"></a>Rola tylko do czytnika

Gdy użytkownicy przesyłają zadanie do klastra z uprawnieniami do roli tylko do czytnika, wymagane są poświadczenia Ambari.

### <a name="link-cluster-from-context-menu"></a>Klaster łączy z menu kontekstowego

1. Zaloguj się przy za pomocą konta roli tylko do czytnika.

2. Z **Usługi Azure Explorer**rozwiń usługę **HDInsight,** aby wyświetlić klastry usługi HDInsight, które znajdują się w ramach subskrypcji. Klastry oznaczone **jako "Rola:Czytnik"** mają tylko uprawnienia roli tylko do czytnika.

    ![Intellij Azure Explorer Rola:Czytnik](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Kliknij prawym przyciskiem myszy klaster z uprawnieniem roli tylko do czytnika. Wybierz **pozycję Połącz ten klaster** z menu kontekstowego, aby połączyć klaster. Wprowadź nazwę użytkownika ambari i hasło.

    ![IntelliJ Azure Explorer połączyć ten klaster](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Jeśli klaster jest połączony pomyślnie, hdinsight zostanie odświeżony.
   Etap klastra zostanie połączony.
  
    ![Połączone okno dialogowe IntelliJ Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Łącze klastra przez rozwijanie węzła Zadania

1. Kliknij węzeł **Zadania,** zostanie wyświetlony okno **Odmowa dostępu do zadania klastra.**

2. Kliknij pozycję **Połącz ten klaster,** aby połączyć klaster.

    ![okno dialogowe dostępu do zadania klastra](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Klaster łączy z okna Konfiguracje uruchamiania/debugowania

1. Utwórz konfigurację hdinsight. Następnie wybierz **opcję Zdalne uruchamianie w klastrze**.

2. Wybierz klaster, który ma uprawnienia roli tylko do czytnika dla **klastrów Platformy Spark (tylko Linux).** Pojawia się komunikat ostrzegawczy. Możesz kliknąć pozycję **Połącz ten klaster,** aby połączyć klaster.

   ![IntelliJ IDEA uruchom/debugować konfigurację utworzyć](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Wyświetlanie kont magazynu

* W przypadku klastrów z uprawnieniem roli tylko do czytnika kliknij węzeł **Konta magazynu,** zostanie wyświetlony okno **Odmowa dostępu do magazynu.** Aby otworzyć Eksploratora magazynu, możesz kliknąć pozycję **Otwórz Eksploratora usługi Azure Storage.**

   ![Odmowa dostępu do pamięci masowej IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![Przycisk Odmowa dostępu do pamięci masowej IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* W przypadku połączonych klastrów kliknij węzeł **Konta magazynu,** zostanie wyświetlony okno **Odmowa dostępu do** magazynu. Możesz kliknąć **otwórz usługę Azure Storage,** aby otworzyć Eksploratora magazynu.

   ![Odmowa dostępu do pamięci masowej IntelliJ IDEA2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![Przycisk Odmowa dostępu do pamięci intellij IDEA2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Konwertowanie istniejących aplikacji IntelliJ IDEA na platformę Azure Toolkit dla technologii IntelliJ

Można przekonwertować istniejące aplikacje Spark Scala utworzone w intellij idea, aby były zgodne z zestawem narzędzi platformy Azure dla technologii IntelliJ. Następnie można użyć wtyczki do przesłania aplikacji do klastra platformy SPARK usługi HDInsight.

1. Dla istniejącej aplikacji Spark Scala, która została utworzona za pośrednictwem intellij idea, otwórz skojarzony plik .iml.

2. Na poziomie głównym jest **element** modułu, podobnie jak następujące:

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
        ```

   Edytuj element, `UniqueKey="HDInsightTool"` aby dodać tak, aby element **modułu** wyglądał następująco:

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
        ```

3. Zapisz zmiany. Aplikacja powinna być teraz zgodna z zestawem narzędzi platformy Azure dla intellij. Można go przetestować, klikając prawym przyciskiem myszy nazwę projektu w programie Project. W menu podręcznym jest teraz dostępna opcja **Prześlij aplikację Iskry do pliku HDInsight**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń utworzony klaster, wykonując następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. W polu **Wyszukaj** w górnej części wpisz **HDInsight**.

1. Wybierz pozycję **Klastry usługi HDInsight** w obszarze **Usługi**.

1. Na wyświetlona lista klastrów HDInsight wybierz **...** obok klastra utworzonego dla tego artykułu.

1. Wybierz pozycję **Usuń**. Wybierz **pozycję Tak**.

![Usługa Azure portal usuwa klaster usługi HDInsight](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono, jak używać azure toolkit for IntelliJ plug-in do tworzenia aplikacji Apache Spark napisanych w [Scali](https://www.scala-lang.org/), a następnie przesłane je do klastra platformy SPARK HDInsight bezpośrednio z intellij zintegrowanego środowiska programistycznego (IDE). Przejdź do następnego artykułu, aby dowiedzieć się, w jaki sposób można ściągnąć dane zarejestrowane na platformie Apache Spark do narzędzia analizy biznesowej, takiego jak usługa Power BI.

> [!div class="nextstepaction"]
> [Analizowanie danych platformy Spark apache przy użyciu usługi Power BI](apache-spark-use-bi-tools.md)
