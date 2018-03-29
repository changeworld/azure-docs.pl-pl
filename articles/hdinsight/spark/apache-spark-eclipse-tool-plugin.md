---
title: 'Azure zestawu narzędzi dla programu Eclipse: Scala tworzenie aplikacji Spark w usłudze HDInsight | Dokumentacja firmy Microsoft'
description: Narzędzia HDInsight w zestawie narzędzi Azure dla programu Eclipse do tworzenia aplikacji Spark napisanych w języku Scala i przesyłanie ich do klastra Spark w usłudze HDInsight, bezpośrednio w programie Eclipse IDE.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: nitinme
ms.openlocfilehash: 4ff67ebcbac5b1b85345421b16036c96f8e807cc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Tworzenie aplikacji Spark dla klastra usługi HDInsight za pomocą narzędzi Azure dla programu Eclipse

Narzędzia HDInsight w zestawie narzędzi Azure dla programu Eclipse do tworzenia aplikacji Spark napisanych w języku Scala i przesyłanie ich do klastra Spark w usłudze HDInsight Azure bezpośrednio w programie Eclipse IDE. Można użyć narzędzia HDInsight Tools wtyczki na kilka różnych sposobów:

* Tworzenie i przesyłanie aplikacji Scala Spark w klastrze Spark w usłudze HDInsight
* Aby dostęp do zasobów klastra Spark w usłudze HDInsight Azure
* Tworzenie i uruchamianie aplikacji Scala Spark lokalnie

> [!IMPORTANT]
> To narzędzie umożliwia tworzenie i przesyłanie aplikacji tylko w przypadku klastra Spark w usłudze HDInsight w systemie Linux.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne

* Klastra Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [klastrów utworzyć Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit w wersji 8 używanego środowiska uruchomieniowego Eclipse IDE. Możesz pobrać go z [witryny sieci Web programu Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Środowisko Eclipse IDE. W tym artykule wykorzystano Eclipse Neon. Możesz zainstalować ją z [witryny sieci Web w środowisku Eclipse](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Instalowanie narzędzi HDInsight Tools w zestawie narzędzi Azure dla programu Eclipse i Scala wtyczki
### <a name="install-azure-toolkit-for-eclipse"></a>Zainstaluj zestaw narzędzi platformy Azure dla programu Eclipse
Narzędzia HDInsight Tools dla programu Eclipse jest dostępny jako część zestawu narzędzi platformy Azure dla programu Eclipse. Aby uzyskać instrukcje instalacji, zobacz [Instalowanie zestawu narzędzi platformy Azure dla programu Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).
### <a name="install-the-scala-plug-in"></a>Instalowanie wtyczki Scala
Po otwarciu Eclipse, narzędzia HDInsight automatycznie wykrywa, czy zainstalowano Scala wtyczki. Wybierz **OK** aby kontynuować, a następnie postępuj zgodnie z instrukcjami, aby zainstalować wtyczkę z witryny Marketplace Eclipse.

![Automatyczne instalowanie wtyczki Scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

Użytkownik może albo [Zaloguj się do subskrypcji platformy Azure](#Sign-in-to-your-Azure-subscription), lub [Połącz z klastrem usługi HDInsight](#Link-a-cluster) przy użyciu Ambari nazwy użytkownika i hasła lub domeny przyłączone poświadczenie, aby rozpocząć. 

## <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do Twojej subskrypcji platformy Azure.
1. Uruchom środowisko Eclipse IDE i Otwórz Eksploratora Azure. Na **okna** menu, wybierz opcję **Pokaż widok**, a następnie wybierz **innych**. W otwartym oknie dialogowym Rozwiń **Azure**, wybierz pozycję **Eksploratora Azure**, a następnie wybierz **OK**.

   ![Pokaż okno dialogowe widoku](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. Kliknij prawym przyciskiem myszy **Azure** węzeł, a następnie wybierz **Zaloguj**.
3. W **Azure logowania** okno dialogowe pola, wybierz metodę uwierzytelniania, wybierz **Zaloguj**i wprowadź swoje poświadczenia usługi Azure.
   
   ![Azure logowania — okno dialogowe](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. Po zalogowaniu, **Wybierz subskrypcje** okno dialogowe wyświetla wszystkie subskrypcje platformy Azure skojarzone z poświadczeniami. Kliknij przycisk **wybierz** aby zamknąć okno dialogowe.

   ![Wybierz subskrypcje — okno dialogowe](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. Na **Eksploratora Azure** karcie, rozwiń **HDInsight** wyświetlić klastry Spark w usłudze HDInsight w ramach Twojej subskrypcji.
   
   ![Klastry HDInsight Spark w Eksploratorze Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. Można rozwinąć węzeł nazwę klastra, aby zobaczyć zasoby (na przykład konta magazynu) skojarzony z klastrem.
   
   ![Rozszerzanie nazwę klastra, aby wyświetlić zasoby](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Połącz klaster
Można połączyć normalne klastra przy użyciu Ambari zarządzane username, także połączyć zabezpieczeń klastra usługi hadoop przy użyciu nazwy użytkownika domeny (takich jak: user1@contoso.com).
1. Kliknij przycisk **Link klastra** z **Eksploratora Azure**.

   ![menu kontekstowe klastra łącza](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Wprowadź **nazwy klastra**, **nazwy użytkownika** i **hasło**, następnie kliknij przycisk OK, aby połączyć klastra. Opcjonalnie wprowadź konto magazynu, klucz magazynu, a następnie wybierz kontener magazynu do pracy w widoku drzewa po lewej stronie Eksploratora usługi storage
   
   ![okno dialogowe klastra łącza](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]
   > Używamy klucza magazynu połączone, nazwę użytkownika i hasło, jeśli klaster rejestrowane w subskrypcji platformy Azure i połączone klastra.
   > ![Eksplorator usługi Storage w środowisku Eclipse](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

3. Widać klastra połączone w **HDInsight** węzeł po kliknięciu przycisku OK, jeśli dane wejściowe są prawidłowe. Teraz można przesłać do tego klastra połączonych aplikacji.

   ![połączone klastra](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. Możesz również odłączyć klastra z **Eksploratora Azure**.
   
   ![odłączyć klastra](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Konfigurowanie projektu Spark Scala dla klastra Spark w usłudze HDInsight

1. W obszarze roboczym Eclipse IDE wybierz **pliku**, wybierz pozycję **nowy**, a następnie wybierz **projektu**. 
2. W Kreatorze nowego projektu, rozwiń węzeł **HDInsight**, wybierz pozycję **Spark w usłudze HDInsight (Scala)**, a następnie wybierz **dalej**.

   ![Wybieranie platformy Spark w usłudze HDInsight (Scala) projektu](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. Kreator tworzenia projektu Scala automatycznie wykrywa, czy zainstalowano Scala wtyczki. Wybierz **OK** aby kontynuować pobieranie Scala wtyczki, a następnie postępuj zgodnie z instrukcjami w celu ponownego uruchomienia aplikacji Eclipse.

   ![Scala wyboru](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
4. W **nowy projekt języka Scala HDInsight** okno dialogowe, podaj następujące wartości, a następnie wybierz **dalej**:
   * Wprowadź nazwę dla projektu.
   * W **środowiska JRE** obszaru, upewnij się, że **Użyj środowiska wykonawczego środowiska JRE** ustawiono **JavaSE 1.7** lub nowszym.
   * W **biblioteki Spark** obszaru, możesz wybrać **Maven używany do konfigurowania zestawu SDK platformy Spark** opcji.  Nasze narzędzie integruje się odpowiedniej wersji dla platformy Spark zestawy SDK i Scala. Można również wybrać **ręcznie dodać zestawu SDK platformy Spark** opcji, Pobierz i ręcznie dodać zestawu SDK platformy Spark przez.

   ![Okno dialogowe Nowy projekt języka Scala HDInsight](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
5. W następnym oknie dialogowym wybierz **Zakończ**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Tworzenie aplikacji dla klastra usługi HDInsight Spark Scala

1. W środowisku Eclipse IDE, w Eksploratorze pakietu, rozwiń węzeł projektu, który został utworzony wcześniej, kliknij prawym przyciskiem myszy **src**, wskaż polecenie **nowy**, a następnie wybierz **innych**.
2. W **kreatora wybierz** okna dialogowego rozwiń **kreatorów języka Scala**, wybierz pozycję **obiektu Scala**, a następnie wybierz **dalej**.
   
   ![Wybierz okno dialogowe Kreator](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. W **Utwórz nowy plik** okno dialogowe, wprowadź nazwę dla obiektu, a następnie wybierz **Zakończ**.
   
   ![Utwórz nowy plik — okno dialogowe](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. Wklej następujący kod w edytorze tekstu:
   
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
5. Uruchom aplikację w klastrze Spark w usłudze HDInsight:
   
   a. W Eksploratorze pakietu, kliknij prawym przyciskiem myszy nazwę projektu, a następnie wybierz **przesyłanie aplikacji Spark HDInsight**.        
   b. W **przesyłanie Spark** okno dialogowe, podaj następujące wartości, a następnie wybierz **przesyłania**:
      
      * Aby uzyskać **nazwy klastra**, wybierz klaster Spark w usłudze HDInsight, na którym chcesz uruchomić aplikację.
      * Wybierz artefakt z projektu Eclipse, lub wybierz jedną z dysku twardego. Wartość domyślna zależy element prawym przyciskiem myszy w Eksploratorze pakietu.
      * W **Nazwa klasy głównym** listy rozwijanej, Kreator przesyłanie Wyświetla wszystkie nazwy obiektów z projektu. Wybierz lub wprowadź klucz, który chcesz uruchomić. Jeśli wybrano artefaktu z dysku twardego, wprowadź nazwę klasy głównym ręcznie. 
      * Kod aplikacji, w tym przykładzie wymagają żadnych argumentów wiersza polecenia lub odwołać słoików lub pliki, pozostałe pola tekstowe można pozostawić pusty.
        
      ![Przesłanie Spark — okno dialogowe](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
6. **Przesyłanie Spark** kartę powinna zaczynać się wyświetlanie postępu. Można zatrzymać aplikację, wybierając czerwony przycisk w **przesyłanie Spark** okna. Można również wyświetlić dzienników dla tej aplikacji określonych Uruchom wybierając ikona globu (wskazywane przez niebieskie pole w obrazie).
      
   ![Przesłanie Spark okna](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Dostęp i zarządzania klastrami Spark w usłudze HDInsight przy użyciu narzędzia HDInsight Tools w zestawie narzędzi Azure dla programu Eclipse
Za pomocą narzędzi HDInsight, takich jak uzyskiwanie dostępu do danych wyjściowych zadania można wykonywać różne operacje.

### <a name="access-the-job-view"></a>Dostęp do widoku zadania
1. W Eksploratorze Azure rozwiń **HDInsight**, rozwiń nazwę klastra Spark, a następnie wybierz **zadania**. 

   ![Zadania widoku węzła](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

2. Wybierz **zadania** węzła. Jeśli jest niższa niż wersja Java **1.8**, narzędzia HDInsight Tools automatycznie przypomnienia o zainstalowaniu **clipse E (fx)** wtyczki. Wybierz **OK** aby kontynuować, a następnie użyj kreatora, aby zainstalować go z witryny Marketplace Eclipse i ponownego uruchomienia aplikacji Eclipse. 

   ![Zainstaluj clipse E (fx)](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

3. Otwórz widok zadania z **zadania** węzła. W okienku po prawej stronie **widoku zadania Spark** karcie są wyświetlane wszystkie aplikacje, które zostały uruchomione w klastrze. Wybierz nazwę aplikacji, dla którego chcesz zobaczyć więcej szczegółów.

   ![Szczegóły aplikacji](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Następnie można wykonać następujące czynności:

   * Umieść kursor na wykresie zadania. Zawiera podstawowe informacje dotyczące wykonywanym zadaniem. Wybierz wykres zadania, a widać etapów i informacje, które generuje każde zadanie.

     ![Szczegóły etap zadania](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Wybierz **dziennika** kartę, aby wyświetlić często używane dzienniki, w tym **Stderr sterownika**, **Stdout sterownika**, i **informacji o katalogu**.

     ![Szczegóły dziennika](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Otwórz historii Spark interfejsu użytkownika i interfejsie użytkownika YARN (na poziomie aplikacji), wybierając hiperłącza znajdującego się w górnej części okna.

### <a name="access-the-storage-container-for-the-cluster"></a>Dostęp do kontenera magazynu dla klastra
1. W Eksploratorze Azure rozwiń **HDInsight** węzła głównego, aby wyświetlić listę klastry HDInsight Spark, które są dostępne.
2. Rozwiń nazwę klastra, aby wyświetlić konta magazynu i domyślnego kontenera magazynu dla klastra.
   
   ![Magazyn konta i domyślnego kontenera magazynu](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. Wybierz nazwę kontenera magazynu skojarzone z klastrem. W prawym okienku kliknij dwukrotnie **HVACOut** folderu. Otwórz jeden z **części -** pliki, aby wyświetlić dane wyjściowe aplikacji.

### <a name="access-the-spark-history-server"></a>Dostęp do serwera historii Spark
1. W Eksploratorze Azure, kliknij prawym przyciskiem myszy nazwę klastra Spark, a następnie wybierz **Otwórz użytkownika usługi Historia Spark**. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra. Możesz określić te podczas inicjowania obsługi klastra.
2. Na pulpicie nawigacyjnym serwera Spark historii możesz Użyj nazwy aplikacji, aby wyszukać aplikacji właśnie zakończono uruchomiona. W powyższym kodzie, należy określić nazwę aplikacji przy użyciu `val conf = new SparkConf().setAppName("MyClusterApp")`. Tak, nazwa aplikacji Spark został **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Uruchom portalu Ambari
1. W Eksploratorze Azure, kliknij prawym przyciskiem myszy nazwę klastra Spark, a następnie wybierz **Otwórz Portal zarządzania klastra (Ambari)**. 
2. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra. Możesz określić te podczas inicjowania obsługi klastra.

### <a name="manage-azure-subscriptions"></a>Zarządzać subskrypcjami platformy Azure
Domyślnie narzędzie HDInsight w zestawie narzędzi Azure dla programu Eclipse wymieniono klastry Spark wszystkie subskrypcje platformy Azure. Jeśli to konieczne, można określić subskrypcji, dla których ma dostęp do klastra. 

1. W Eksploratorze Azure, kliknij prawym przyciskiem myszy **Azure** węzła głównego, a następnie wybierz **Zarządzaj subskrypcjami**. 
2. W oknie dialogowym, wyczyść pola wyboru dla subskrypcji, które nie mają dostępu, a następnie wybierz **Zamknij**. Możesz też wybrać **Wyloguj** Aby wylogować się z subskrypcją platformy Azure.

## <a name="run-a-spark-scala-application-locally"></a>Uruchamianie aplikacji Spark Scala lokalnie
Narzędzia HDInsight Tools w zestawie narzędzi Azure dla programu Eclipse służy do uruchamiania aplikacji Spark Scala lokalnie na stacji roboczej. Zazwyczaj te aplikacje nie wymagają dostępu do zasobów klastra, takie jak kontenera magazynu, a program można uruchomić i przetestować je lokalnie.

### <a name="prerequisite"></a>Wymagania wstępne
Gdy używasz lokalnych aplikacji Spark Scala na komputerze z systemem Windows, można uzyskać wyjątku zgodnie z objaśnieniem w [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). Ten wyjątek spowodowany **WinUtils.exe** nie ma w systemie Windows. 

Aby rozwiązać ten problem, należy [Pobierz plik wykonywalny](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do lokalizacji, takiej jak **C:\WinUtils\bin**, a następnie dodaj zmienną środowiskową **HADOOP_HOME** i ustaw wartość Zmienna **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Uruchamianie aplikacji Spark Scala lokalnej
1. Uruchom środowisko Eclipse i tworzenia projektu. W **nowy projekt** okno dialogowe, wybierz następujące opcje, a następnie wybierz **dalej**.
   
   * W lewym okienku wybierz opcję **HDInsight**.
   * W okienku po prawej stronie zaznacz **Spark dla usługi HDInsight lokalnego uruchamiania próbki (Scala)**.

   ![Okno dialogowe Nowy projekt](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
2. Aby podać szczegóły projektu, wykonaj kroki od 3 do 6 z we wcześniejszej sekcji [ustawienia projektu dla klastra Spark w usłudze HDInsight Spark Scala](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

3. Przykładowy kod dodaje szablonu (**LogQuery**) w obszarze **src** folderu, który można uruchomić lokalnie na komputerze.
   
   ![Lokalizacja LogQuery](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
4. Kliknij prawym przyciskiem myszy **LogQuery** aplikacji, punkt **Uruchom jako**, a następnie wybierz **1 aplikacja Scala**. Dane wyjściowe, takie jak pojawia się na **konsoli** karty:
   
   ![Aplikacji Spark lokalnego wynik uruchomienia](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>Znane problemy
Aby przesłać aplikacji do usługi Azure Data Lake Store, wybierz **Interactive** tryb podczas Azure procesu logowania. W przypadku wybrania **automatycznego** tryb, może być wyświetlany komunikat o błędzie.

![Interakcyjne logowanie](./media/apache-spark-eclipse-tool-plugin/interactive-authentication.png)

Można wybrać do klastra usługi Azure Data Lake do przesyłania aplikacji przy użyciu dowolnej metody logowania.

Przeglądanie wyjść Spark bezpośrednio nie jest obecnie obsługiwana.

## <a name="feedback"></a>Opinia
Jeśli masz opinię, lub jeśli występują inne problemy podczas korzystania z tego narzędzia, Wyślij do nas wiadomość e-mail na hdivstool@microsoft.com.

## <a name="seealso"></a>Zobacz też
* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Spark i analiza biznesowa: interakcyjna analiza danych na platformie Spark w usłudze HDInsight z użyciem narzędzi do analizy biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Zestaw narzędzi platformy Azure dla IntelliJ umożliwia tworzenie i przesyłanie aplikacji Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Debugowanie aplikacji Spark zdalnie za pośrednictwem sieci VPN przy użyciu zestawu narzędzi Azure for IntelliJ](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Debugowanie aplikacji Spark zdalnie za pośrednictwem usługi SSH przy użyciu zestawu narzędzi Azure for IntelliJ](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Użyj narzędzia HDInsight Tools for IntelliJ z Hortonworks piaskownicy](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

