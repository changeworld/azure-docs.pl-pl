---
title: 'Zestaw narzędzi platformy Azure dla środowiska Eclipse: Tworzenie aplikacji Scala dla usługi HDInsight Spark '
description: Narzędzia HDInsight w zestaw narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji Spark napisanych w języku Scala i przesyłanie ich do klastra usługi HDInsight Spark bezpośrednio w środowisku IDE programu Eclipse.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: hrasheed
ms.openlocfilehash: cd5839520a5b85f31cbe677ad6691a3d6bacd0b0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066354"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Używanie zestawu narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji platformy Apache Spark dla klastra usługi HDInsight

Przy użyciu narzędzi HDInsight w Azure Toolkit for [Eclipse](https://www.eclipse.org/) do opracowywania [platformy Apache Spark](https://spark.apache.org/) aplikacje napisane w [Scala](https://www.scala-lang.org/) i przesyłanie ich do klastra usługi Azure HDInsight Spark bezpośrednio z poziomu środowiska Eclipse IDE. Można użyć narzędzia HDInsight wtyczki na kilka różnych sposobów:

* Twórz i przesyłaj aplikację Scala Spark w klastrze usługi HDInsight Spark.
* Aby uzyskać dostęp do zasobów klastra usługi HDInsight Spark.
* Twórz i uruchamiaj lokalnie aplikację Scala Spark.

> [!IMPORTANT]  
> To narzędzie umożliwia tworzenie i przesyłanie aplikacji tylko w przypadku klastra HDInsight Spark, w systemie Linux.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Wersja oprogramowania Oracle Java Development Kit 8 używanego dla środowiska Eclipse IDE środowiska uruchomieniowego. Możesz ją pobrać z [witryny internetowej Oracle](https://aka.ms/azure-jdks).
* Środowisko Eclipse IDE. W tym artykule używa środowiska Eclipse Neon. Można zainstalować go z [witryny sieci Web w środowisku Eclipse](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Instalowanie narzędzi HDInsight w zestaw narzędzi platformy Azure dla środowisk Eclipse i Scala wtyczki

### <a name="install-azure-toolkit-for-eclipse"></a>Zainstaluj zestaw narzędzi platformy Azure dla programu Eclipse
Narzędzia HDInsight Tools for Eclipse jest dostępna w ramach zestawu narzędzi platformy Azure dla środowiska Eclipse. Aby uzyskać instrukcje dotyczące instalacji, zobacz [Instalowanie zestawu narzędzi platformy Azure dla środowiska Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>Instalowanie wtyczki Scala
Po otwarciu programu Eclipse, HDInsight, narzędzie automatycznie wykrywa, czy zainstalowany Scala wtyczki. Wybierz **OK** aby kontynuować, a następnie postępuj zgodnie z instrukcjami, aby zainstalować wtyczkę z witryny Marketplace programu Eclipse.

![Instalacja automatyczna Scala wtyczki](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

Użytkownik może być [Zaloguj się do subskrypcji platformy Azure](#sign-in-to-your-azure-subscription), lub [połączenia klastra HDInsight](#link-a-cluster) przy użyciu narzędzia Ambari nazwy użytkownika/hasła lub domeny przyłączone poświadczeń do uruchomienia. 

## <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do Twojej subskrypcji platformy Azure.
1. Uruchom środowisko Eclipse IDE i Otwórz Eksploratora usługi Azure. Na **okna** menu, wybierz opcję **Pokaż widok**, a następnie wybierz pozycję **innych**. W wyświetlonym oknie dialogowym Rozwiń **Azure**, wybierz opcję **Eksploratora usługi Azure**, a następnie wybierz pozycję **OK**.

   ![Okno dialogowe pokazywania widoków](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
1. Kliknij prawym przyciskiem myszy **Azure** węzeł, a następnie wybierz **Zaloguj**.
1. W **Azure Sign In** okna dialogowego pole, wybierz metodę uwierzytelniania, wybierz **Zaloguj**, a następnie wprowadź swoje poświadczenia platformy Azure.
   
   ![Usługa Azure Sign In okno dialogowe](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
1. Po użytkownik jest zalogowany, **subskrypcje wybierz** okno dialogowe wyświetla listę wszystkich subskrypcji platformy Azure skojarzone z poświadczeniami. Kliknij przycisk **wybierz** aby zamknąć okno dialogowe.

   ![Wybierz subskrypcje, okno dialogowe](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
1. Na **Eksploratora usługi Azure** kartę, a następnie rozwiń **HDInsight** wyświetlić platformy HDInsight Spark klastrów w ramach Twojej subskrypcji.
   
   ![Klastry HDInsight Spark, w Eksploratorze usługi Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
1. Można rozwinąć węzła nazwę klastra, zapoznaj się z zasobami (na przykład kont magazynu) skojarzonego z klastrem.
   
   ![Rozwijanie nazwę klastra, aby wyświetlić zasoby](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Połącz klaster
Możesz połączyć normalny klaster przy użyciu nazwy użytkownika systemu Ambari zarządzane. Podobnie, dla klastra HDInsight przyłączone do domeny, możesz połączyć przy użyciu domeny i nazwę użytkownika, takich jak user1@contoso.com.

1. Wybierz **połączenia klastra** z **Eksploratora usługi Azure**.

   ![menu kontekstowe klaster w linku](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. Wprowadź **nazwy klastra**, **nazwa_użytkownika** i **hasło**, następnie kliknij przycisk OK w celu połączenia klastra. Opcjonalnie wprowadź klucz magazynu konta magazynu, a następnie wybierz kontener magazynu Eksplorator usługi storage do pracy w widoku drzewa po lewej stronie
   
   ![okno dialogowe klastra łącza](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]  
   > Używamy klucz połączonego magazynu, nazwę użytkownika i hasło, jeśli klaster w subskrypcji platformy Azure i połączone w klaster.
   > ![Eksplorator usługi Storage w środowisku Eclipse](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. Możesz zobaczyć połączone w klaster **HDInsight** węzła po kliknięciu przycisku OK, jeśli dane wejściowe są odpowiednie. Możesz teraz przesłać aplikację do tego klastra połączonych.

   ![połączone klastra](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. Możesz również można odłączyć elementy od klastra z **Eksploratora usługi Azure**.
   
   ![Odłączono klastra](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Konfigurowanie projektu Spark Scala dla klastra usługi HDInsight Spark

1. W obszarze roboczym środowiska IDE programu Eclipse wybierz **pliku**, wybierz opcję **nowy**, a następnie wybierz pozycję **projektu**. 
1. W Kreatorze nowego projektu, rozwiń **HDInsight**, wybierz opcję **Spark on HDInsight (Scala)** , a następnie wybierz pozycję **dalej**.

   ![Wybieranie platformy Spark w projekcie HDInsight (Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
1. Kreator tworzenia projektu w języku Scala automatycznie wykrywa, czy zainstalowany Scala wtyczki. Wybierz **OK** można kontynuować pobieranie Scala wtyczki, a następnie postępuj zgodnie z instrukcjami, aby ponownie uruchomić środowiska Eclipse.

   ![Scala wyboru](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
1. W **nowy projekt w języku Scala HDInsight** okno dialogowe, podaj następujące wartości, a następnie wybierz pozycję **dalej**:
   * Wprowadź nazwę dla projektu.
   * W **środowiska JRE** obszaru, upewnij się, że **Użyj środowisko wykonawcze środowiska JRE** jest ustawiona na **JavaSE 1.7** lub nowszej.
   * W **biblioteki Spark** obszaru, możesz wybrać **Użyj narzędzia Maven, aby skonfigurować zestaw SDK platformy Spark** opcji.  Nasze narzędzie integruje się odpowiednią wersję zestawu SDK platformy Spark i Scala zestawu SDK. Można również wybrać **ręcznie dodać zestaw SDK platformy Spark** opcji, Pobierz i ręcznie Dodaj zestaw SDK platformy Spark usługi przez.

   ![Okno dialogowe Nowy projekt Scala HDInsight](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
1. W następnym oknie dialogowym wybierz **Zakończ**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Utwórz aplikację Scala dla klastra usługi HDInsight Spark

1. W środowisku IDE programu Eclipse w narzędziu Package Explorer, rozwiń węzeł projektu, który został utworzony wcześniej, kliknij prawym przyciskiem myszy **src**, wskaż polecenie **New**, a następnie wybierz pozycję **innych**.
1. W **Wybierz kreatora** okna dialogowego rozwiń **kreatorów Scala**, wybierz opcję **obiektu Scala**, a następnie wybierz **dalej**.
   
   ![Wybierz okno dialogowe Kreator](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
1. W **Utwórz nowy plik** okno dialogowe, wprowadź nazwę dla obiektu, a następnie wybierz pozycję **Zakończ**.
   
   ![Utwórz nowy plik — okno dialogowe](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
1. Wklej następujący kod w edytorze tekstu:
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
1. Uruchom aplikację w klastrze usługi HDInsight Spark:
   
   a. W narzędziu Package Explorer kliknij prawym przyciskiem myszy nazwę projektu, a następnie wybierz **przesyłanie aplikacji Spark HDInsight**.        
   b. W **przesyłania Spark** okno dialogowe, podaj następujące wartości, a następnie wybierz pozycję **przesyłania**:
      
   * Aby uzyskać **nazwy klastra**, wybierz klaster HDInsight Spark, na którym chcesz uruchomić aplikację.
   * Wybierz artefakt z projekt środowiska Eclipse lub wybierz jedną z dysku twardego. Wartość domyślna zależy od elementu, który można kliknąć prawym przyciskiem myszy w narzędziu Package Explorer.
   * W **Nazwa klasy Main** listy rozwijanej, Kreator przesyłania Wyświetla wszystkie nazwy obiektów z projektu. Wybierz lub wprowadź klucz, który chcesz uruchomić. Jeśli wybrano artefaktu z dysku twardego, nazwa główna klasa musi wprowadzić ręcznie. 
   * Ponieważ kod aplikacji, w tym przykładzie nie wymagają żadnych argumentów wiersza polecenia lub odwoływać się do plików lub plikach JAR, pozostałych pól tekstowych można pozostawić puste.
        
     ![Okno dialogowe przesyłania platformy Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
1. **Przesyłania Spark** kartę powinien rozpocząć wyświetlanie postępu. Można zatrzymać aplikację, wybierając czerwony przycisk w **przesyłania Spark** okna. Można również wyświetlić dzienniki dla tej konkretnej aplikacji, uruchomić, wybierając ikonę świecie (wskazywane przez niebieskie pole na obrazie).
      
   ![Okno przesyłania platformy Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Dostęp i zarządzać klastry HDInsight Spark przy użyciu narzędzi HDInsight zestawu narzędzi platformy Azure dla środowiska Eclipse
Za pomocą narzędzi HDInsight, takich jak uzyskiwanie dostępu do danych wyjściowych zadania można wykonywać różne operacje.

### <a name="access-the-job-view"></a>Dostęp do widoku zadania
1. W Eksploratorze usługi Azure, rozwiń węzeł **HDInsight**, rozwiń nazwę klastra platformy Spark, a następnie wybierz **zadań**. 

   ![Zadania widoku węzła](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

1. Wybierz **zadań** węzła. Jeśli wersja języka Java jest niższa niż **1.8**, narzędzia HDInsight automatycznie przypomnienie o zainstalowaniu **clipse E (fx)** wtyczki. Wybierz **OK** aby kontynuować, a następnie użyj Kreatora aby go zainstalować z witryny Marketplace programu Eclipse i uruchom ponownie program Eclipse. 

   ![Zainstaluj clipse E (fx)](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Powoduje ono otwarcie widoku zadania z **zadań** węzła. W okienku po prawej stronie **widok zadania Spark** karcie są wyświetlane wszystkie aplikacje, które zostały uruchomione w klastrze. Wybierz nazwę aplikacji, dla którego chcesz wyświetlić więcej szczegółowych informacji.

   ![Szczegóły aplikacji](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Następnie można wykonać dowolną z tych akcji:

   * Zatrzymaj wskaźnik myszy na wykresie zadania. Wyświetla podstawowe informacje dotyczące uruchomionego zadania. Wybierz wykres zadania, a zobaczysz etapów i informacji, które generuje każde zadanie.

     ![Szczegóły etap zadania](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Wybierz **dziennika** kartę, aby wyświetlić często używane dzienników, w tym **Stderr sterownika**, **Stdout sterownika**, i **informacji o katalogu**.

     ![Szczegóły dziennika](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Otwórz historii platformy Spark interfejsu użytkownika i Apache Hadoop Interfejsie użytkownika YARN (na poziomie aplikacji), wybierając hiperłącza w górnej części okna.

### <a name="access-the-storage-container-for-the-cluster"></a>Dostęp do kontenera magazynu dla klastra
1. W Eksploratorze usługi Azure, rozwiń węzeł **HDInsight** węzła głównego, aby wyświetlić listę klastrów HDInsight Spark, które są dostępne.
1. Rozwiń nazwę klastra, aby wyświetlić konta magazynu i domyślnego kontenera magazynu dla klastra.
   
   ![Magazyn konta i domyślnego kontenera magazynu](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
1. Wybierz nazwę kontenera magazynu skojarzonego z klastrem. W okienku po prawej stronie, kliknij dwukrotnie **HVACOut** folderu. Otwórz jeden z **część -** plików, aby zobaczyć dane wyjściowe aplikacji.

### <a name="access-the-spark-history-server"></a>Dostęp serwer historii platformy Spark
1. W Eksploratorze usługi Azure, kliknij prawym przyciskiem myszy nazwę klastra platformy Spark, a następnie wybierz **Otwórz użytkownika usługi Historia Spark**. Po wyświetleniu monitu wprowadź poświadczenia administratora dla klastra. Określono je podczas inicjowania obsługi klastra.
1. Na pulpicie nawigacyjnym serwer historii platformy Spark Nazwa aplikacji jest służy do wyszukiwania dla aplikacji, w właśnie został uruchomiony. W poprzednim kodzie, należy określić nazwę aplikacji przy użyciu `val conf = new SparkConf().setAppName("MyClusterApp")`. Dlatego został nazwę swojej aplikacji Spark **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Uruchom portal Apache Ambari
1. W Eksploratorze usługi Azure, kliknij prawym przyciskiem myszy nazwę klastra platformy Spark, a następnie wybierz **Otwórz Portal zarządzania klastrem (Ambari)** . 
1. Po wyświetleniu monitu wprowadź poświadczenia administratora dla klastra. Określono je podczas inicjowania obsługi klastra.

### <a name="manage-azure-subscriptions"></a>Zarządzanie subskrypcjami platformy Azure
Domyślnie narzędzia HDInsight w zestaw narzędzi platformy Azure dla środowiska Eclipse, wyświetla listę klastrów Spark z Twoich subskrypcji platformy Azure. Jeśli to konieczne, można określić subskrypcje, dla których chcesz uzyskać dostęp do klastra. 

1. W Eksploratorze usługi Azure, kliknij prawym przyciskiem myszy **Azure** węzła głównego, a następnie wybierz **Zarządzaj subskrypcjami**. 
1. W oknie dialogowym, wyczyść pola wyboru dla subskrypcji, który nie ma dostępu do, a następnie wybierz pozycję **Zamknij**. Możesz również wybrać **Wyloguj** aby Wyloguj się ze swoją subskrypcją platformy Azure.

## <a name="run-a-spark-scala-application-locally"></a>Uruchamiaj lokalnie aplikację Spark Scala
Do uruchomienia aplikacji Spark scala przy lokalnie na swojej stacji roboczej, można użyć narzędzia HDInsight w zestaw narzędzi platformy Azure dla środowiska Eclipse. Zazwyczaj te aplikacje nie wymagają dostępu do zasobów klastra, takich jak kontener magazynu i można uruchomić i przetestować je lokalnie.

### <a name="prerequisite"></a>Wymagania wstępne
Gdy korzystasz z aplikacji Spark Scala lokalnej na komputerze Windows, możesz otrzymać wyjątek zgodnie z objaśnieniem w [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). Ten wyjątek występuje, ponieważ **WinUtils.exe** brakuje w Windows. 

Aby rozwiązać ten problem, musisz mieć [Pobierz plik wykonywalny](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do lokalizacji, takiej jak **C:\WinUtils\bin**, a następnie dodaj zmienną środowiskową **HADOOP_HOME** i ustaw wartość Zmienna **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Uruchamianie lokalnych aplikacji Spark Scala
1. Uruchom środowisko Eclipse i Utwórz projekt. W **nowy projekt** okno dialogowe, wybierz następujące opcje, a następnie wybierz pozycję **dalej**.
   
   * W lewym okienku wybierz opcję **HDInsight**.
   * W okienku po prawej stronie wybierz **Spark on HDInsight lokalnego Uruchom próbkę (Scala)** .

   ![Okno dialogowe Nowy projekt](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
1. Aby podać szczegóły projektu, wykonaj kroki od 3 do 6 z wcześniejszej sekcji [skonfigurować projekt Spark Scala dla klastra usługi HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Do szablonu dodawany jest kod przykładowy (**LogQuery**) w obszarze **src** folder, który można uruchomić lokalnie na komputerze.
   
   ![Lokalizacja LogQuery](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
1. Kliknij prawym przyciskiem myszy **LogQuery** aplikacji, wskaż **Uruchom jako**, a następnie wybierz pozycję **1 aplikacja Scala**. Dane wyjściowe, takie jak pojawia się na **konsoli** karty:
   
   ![Aplikacja Spark lokalnego uruchomienia wyniku](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Rola tylko do czytnika
Gdy użytkownicy przesyłania zadania do klastra z uprawnieniami tylko do odczytywania roli, Ambari poświadczenia jest wymagana.

### <a name="link-cluster-from-context-menu"></a>Połącz klaster z menu kontekstowego

1. Zaloguj się przy użyciu konta roli Czytelnik — tylko.
       
2. Z **Eksploratora usługi Azure**, rozwiń węzeł **HDInsight** do wyświetlania klastrów HDInsight, które znajdują się w Twojej subskrypcji. Oznaczone jako klastry **"Rola: Czytelnik"** tylko mają uprawnienia tylko do odczytywania roli.

    ![Klastry HDInsight Spark, w Eksploratorze usługi Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-6.png)

3. Kliknij prawym przyciskiem myszy klaster z uprawnieniami tylko do odczytywania roli. Wybierz **połączyć ten klaster** z menu kontekstowego w celu połączenia klastra. Wprowadź nazwę użytkownika systemu Ambari i hasło.

    ![Klastry HDInsight Spark, w Eksploratorze usługi Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-7.png)

4. Jeśli klaster został połączony pomyślnie, zostanie odświeżona HDInsight.
   Etap klastra będzie stają się połączony.
  
    ![Klastry HDInsight Spark, w Eksploratorze usługi Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-8.png)



### <a name="link-cluster-by-expanding-jobs-node"></a>Link klastra, rozwijając węzeł zadań

1. Kliknij przycisk **zadania** węzła **klastra zadania dostępu** pojawi się okno.
   
2. Kliknij przycisk **połączyć ten klaster** celu połączenia klastra.
   
    ![Klastry HDInsight Spark, w Eksploratorze usługi Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Link klastra z poziomu okna przesyłania platformy Spark

1. Utwórz projekt HDInsight.

2. Kliknij prawym przyciskiem myszy pakiet. Następnie wybierz pozycję **przesyłanie aplikacji Spark HDInsight**.
   
   ![Klastry HDInsight Spark, w Eksploratorze usługi Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-11.png)

3. Wybierz klaster, który ma uprawnienia tylko do odczytywania roli dla **nazwy klastra**. Pokazuje komunikat ostrzegawczy. Możesz kliknąć pozycję **połączyć ten klaster** celu połączenia klastra.
   
   ![Klastry HDInsight Spark, w Eksploratorze usługi Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-15.png)
   
### <a name="view-storage-accounts"></a>Widok kont magazynu

* W przypadku klastrów z uprawnieniami tylko do odczytywania roli kliknij **kont magazynu** węzła **odmowa dostępu do magazynu** pojawi się okno. 
     
   ![Klastry HDInsight Spark, w Eksploratorze usługi Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-13.png)

   ![Klastry HDInsight Spark, w Eksploratorze usługi Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-12.png)

* Połączone klastrów, kliknij przycisk **kont magazynu** węzła **odmowa dostępu do magazynu** pojawi się okno. 
     
   ![Klastry HDInsight Spark, w Eksploratorze usługi Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-14.png)

## <a name="known-problems"></a>Znane problemy
Gdy połączenie klastra, I sugerować, musisz podać poświadczenia magazynu.

![Logowanie interaktywne](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Istnieją dwa tryby można przesłać zadania. Jeśli nie podano poświadczeń magazynu, tryb wsadowy będzie służyć do przesyłania zadania. W przeciwnym razie będzie używany tryb interakcyjny. Jeśli klaster jest zajęty, możesz otrzymać poniższego błędu.

![Błąd Eclipse po klastra zajęty](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png)

![Błąd Eclipse po klastra zajęty](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png)

## <a name="seealso"></a>Zobacz też
* [Omówienie: Platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Apache Spark przy użyciu Power BI: Interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika witryny sieci Web przy użyciu platformy Apache Spark w HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Debugowanie aplikacji platformy Apache Spark, zdalnie za pośrednictwem sieci VPN przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Debugowanie aplikacji platformy Apache Spark, zdalnie za pośrednictwem protokołu SSH przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

