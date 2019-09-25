---
title: 'Azure Toolkit for Eclipse: Tworzenie aplikacji Scala dla usługi HDInsight Spark '
description: Użyj narzędzi usługi HDInsight w Azure Toolkit for Eclipse, aby opracowywać aplikacje platformy Spark zapisane w Scala i przesyłać je do klastra usługi HDInsight Spark bezpośrednio z poziomu środowiska IDE.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 5336153a776f3324e5f73564ba2804389cd96938
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257571"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Tworzenie aplikacji Apache Spark dla klastra usługi HDInsight za pomocą Azure Toolkit for Eclipse

Użyj narzędzi HDInsight dostępnych w zestawie narzędzi platformy Azure [, aby przełożyć na](https://www.eclipse.org/) [Apache Spark](https://spark.apache.org/) aplikacje zapisane w [Scala](https://www.scala-lang.org/) i przesłać je do klastra Azure HDInsight Spark bezpośrednio z poziomu środowiska IDE. Możesz użyć wtyczki narzędzi HDInsight na kilka różnych sposobów:

* Aby opracować i przesłać aplikację Scala Spark w klastrze usługi HDInsight Spark.
* Aby uzyskać dostęp do zasobów klastra Azure HDInsight Spark.
* Aby lokalnie opracowywać i uruchamiać aplikację Scala Spark.

> [!IMPORTANT]  
> Za pomocą tego narzędzia można tworzyć i przesyłać aplikacje tylko dla klastra Spark usługi HDInsight w systemie Linux.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Pakiet Oracle Java Development Kit w wersji 8, który jest używany w środowisku uruchomieniowym środowiska IDE. Można go pobrać z [witryny sieci Web Oracle](https://aka.ms/azure-jdks).
* Zaćmienie IDE. W tym artykule jest stosowane przezaćmienie — Neon. Można go zainstalować z [witryny sieci Web w witrynie zaćmienie](https://www.eclipse.org/downloads/).

## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Instalowanie narzędzi usługi HDInsight w Azure Toolkit for Eclipse i wtyczka Scala

### <a name="install-azure-toolkit-for-eclipse"></a>Zainstaluj Azure Toolkit for Eclipse

Narzędzia HDInsight Tools for zaćmienie są dostępne w ramach Azure Toolkit for Eclipse. Instrukcje instalacji znajdują się w temacie [installing Azure Toolkit for Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>Instalowanie wtyczki Scala

Po otwarciu okna zaćmienie narzędzie HDInsight automatycznie wykrywa, czy zainstalowano wtyczkę Scala. Wybierz **przycisk OK** , aby kontynuować, a następnie postępuj zgodnie z instrukcjami, aby zainstalować wtyczkę z portalu Marketplace.

![Automatyczna instalacja wtyczki Scala](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

Użytkownik może [zalogować się do subskrypcji platformy Azure](#sign-in-to-your-azure-subscription)lub [połączyć klaster usługi HDInsight](#link-a-cluster) przy użyciu poświadczeń Ambari username/Password lub Domain przyłączonych do systemu.

## <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do Twojej subskrypcji platformy Azure.

1. Uruchom środowisko IDE zaćmienie i Otwórz Eksploratora platformy Azure. W menu **okno** wybierz pozycję **Pokaż widok**, a następnie wybierz pozycję **inne**. W otwartym oknie dialogowym Rozwiń węzeł **Azure**, wybierz pozycję **Eksplorator platformy Azure**, a następnie wybierz przycisk **OK**.

   ![Apache Spark Pokaż widok Eclispse](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)

1. Kliknij prawym przyciskiem myszy węzeł **Azure** , a następnie wybierz pozycję **Zaloguj się**.
1. W oknie dialogowym **Logowanie do platformy Azure** wybierz metodę uwierzytelniania, wybierz pozycję **Zaloguj**, a następnie wprowadź swoje poświadczenia platformy Azure.

   ![Apache Spark Eclispse na platformie Azure](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. Po zalogowaniu się okno dialogowe **wybierz subskrypcje** zawiera listę wszystkich subskrypcji platformy Azure skojarzonych z poświadczeniami. Kliknij przycisk **Wybierz** , aby zamknąć okno dialogowe.

   ![Okno dialogowe Wybieranie subskrypcji](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. Na karcie **Azure Explorer** rozwiń węzeł **HDInsight** , aby zobaczyć klastry usługi HDInsight Spark w ramach subskrypcji.

   ![Klastry HDInsight Spark na platformie Azure Explorer3](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. Można dodatkowo rozwinąć węzeł Nazwa klastra, aby wyświetlić zasoby (na przykład konta magazynu) skojarzone z klastrem.

   ![Rozszerzanie nazwy klastra w celu wyświetlenia zasobów](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>Łączenie klastra

Normalny klaster można połączyć za pomocą Ambari zarządzanej nazwy użytkownika. Podobnie w przypadku klastra usługi HDInsight przyłączonego do domeny można łączyć się za pomocą domeny i nazwy użytkownika, takich jak `user1@contoso.com`.

1. Wybierz pozycję **Połącz klaster** z poziomu **Eksploratora platformy Azure**.

   ![Menu klastra łącza programu Azure Explorer](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. Wprowadź **nazwę klastra**, **nazwę użytkownika** i **hasło**, a następnie kliknij przycisk OK, aby połączyć klaster. Opcjonalnie wprowadź konto magazynu, klucz magazynu, a następnie wybierz pozycję kontener magazynu dla Eksploratora usługi Storage, aby rozpocząć działanie w widoku drzewa po lewej stronie

   ![Okno dialogowe łączenie nowego klastra usługi HDInsight](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > Jeśli klaster jest zarejestrowany w ramach subskrypcji platformy Azure i połączony z klastrem, użyjemy podanego klucza magazynu, nazwy użytkownika i hasła.
   > ![Konta usługi Azure Explorer Storage](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. Możesz zobaczyć połączony klaster w węźle usługi **HDInsight** po kliknięciu przycisku OK, jeśli dane wejściowe są prawidłowe. Teraz można przesłać aplikację do tego połączonego klastra.

   ![Połączony klaster programu Azure Explorer HDI](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. Możesz również odłączyć klaster od programu **Azure Explorer**.

   ![Niepołączony klaster programu Azure Explorer](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Konfigurowanie projektu Spark Scala dla klastra usługi HDInsight Spark

1. W obszarze roboczym zaćmienie IDE wybierz pozycję **plik**, wybierz pozycję **Nowy**, a następnie wybierz pozycję **projekt**.

1. W Kreatorze nowego projektu rozwiń węzeł **HDInsight**, wybierz pozycję **Spark w usłudze HDInsight (Scala)** , a następnie wybierz pozycję **dalej**.

   ![Wybieranie projektu platformy Spark w usłudze HDInsight (Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. Kreator tworzenia projektu Scala automatycznie wykrywa, czy zainstalowano wtyczkę Scala. Wybierz **przycisk OK** , aby kontynuować pobieranie wtyczki Scala, a następnie postępuj zgodnie z instrukcjami, aby ponownie uruchomić program zaćmienie.

   ![Zainstaluj brak sprawdzenia Scala wtyczki](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala2.png)

1. W oknie dialogowym **Nowy projekt usługi HDInsight Scala** podaj następujące wartości, a następnie wybierz pozycję **dalej**:
   * Wprowadź nazwę dla projektu.
   * W obszarze **środowiska JRE** upewnij się, że w obszarze **środowisko wykonawcze** jest ustawiona wartość **Java-1,7** lub nowsza.
   * W obszarze **Biblioteka platformy Spark** możesz wybrać opcję **Użyj Maven do skonfigurowania opcji zestawu SDK platformy Spark** .  Nasze narzędzie integruje odpowiednią wersję zestawu Spark SDK i scala SDK. Można również wybrać opcję **Dodaj ręcznie zestaw SDK platformy Spark** , pobrać i dodać zestaw Spark SDK ręcznie.

   ![Okno dialogowe Nowy projekt usługi HDInsight Scala](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. W następnym oknie dialogowym wybierz pozycję **Zakończ**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Tworzenie aplikacji Scala dla klastra usługi HDInsight Spark

1. W środowisku IDE w wersji zaćmienie, w Eksploratorze pakietów rozwiń utworzony wcześniej projekt, kliknij prawym przyciskiem myszy pozycję **src**, wskaż polecenie **Nowy**, a następnie wybierz pozycję **inne**.

1. W oknie dialogowym **Wybierz kreatora** rozwiń węzeł **kreatory Scala**, wybierz pozycję **Scala obiekt**, a następnie wybierz przycisk **dalej**.

   ![Wybieranie Kreatora tworzenia obiektu Scala](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)
1. W oknie dialogowym **Utwórz nowy plik** wprowadź nazwę obiektu, a następnie wybierz pozycję **Zakończ**.

   ![Kreator nowego pliku — Utwórz nowy plik](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)
1. Wklej następujący kod w edytorze tekstu:

    ```scala
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
    ```

1. Uruchom aplikację w klastrze usługi HDInsight Spark:

   a. W Eksploratorze pakietów kliknij prawym przyciskiem myszy nazwę projektu, a następnie wybierz polecenie **Prześlij aplikację Spark do usługi HDInsight**.

   b. W oknie dialogowym Przesyłanie w usłudze **Spark** podaj następujące wartości, a następnie wybierz pozycję **Prześlij**:

   * W polu **Nazwa klastra**wybierz klaster usługi HDInsight Spark, na którym chcesz uruchomić aplikację.
   * Wybierz artefakt z projektu zaćmienie lub wybierz go z dysku twardego. Wartość domyślna zależy od elementu, który można kliknąć prawym przyciskiem myszy w Eksploratorze pakietów.
   * Na liście rozwijanej **Nazwa klasy głównej** Kreator wysyłania wyświetla wszystkie nazwy obiektów z projektu. Wybierz lub wprowadź jeden, który chcesz uruchomić. W przypadku wybrania artefaktu z dysku twardego należy ręcznie wprowadzić nazwę klasy głównej. 
   * Ponieważ kod aplikacji w tym przykładzie nie wymaga żadnych argumentów wiersza polecenia lub JARs odniesienia lub plików, można pozostawić pozostałe pola tekstowe puste.

     ![Okno dialogowe przesyłanie Apache Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. Karta **przesyłanie** na platformie Spark powinna rozpocząć wyświetlanie postępu. Możesz zatrzymać aplikację, wybierając czerwony przycisk w oknie **przesyłanie platformy Spark** . Możesz również wyświetlić dzienniki dla tego konkretnej aplikacji, wybierając ikonę globusa (oznaczaną niebieską ramką na obrazie).

   ![Okno przesłania Apache Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Dostęp do klastrów usługi HDInsight Spark i zarządzanie nimi przy użyciu narzędzi usługi HDInsight w Azure Toolkit for Eclipse

Możesz wykonywać różne operacje przy użyciu narzędzi usługi HDInsight, w tym do uzyskiwania dostępu do danych wyjściowych zadania.

### <a name="access-the-job-view"></a>Dostęp do widoku zadania

1. W Eksploratorze Azure rozwiń węzeł **HDInsight**, rozwiń węzeł Nazwa klastra Spark, a następnie wybierz pozycję **zadania**.

   ![Węzeł widoku zadania w programie Azure Explorer zaćmienie](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. Wybierz węzeł **zadania** . Jeśli wersja języka Java jest niższa niż **1,8**, narzędzia usługi HDInsight automatycznie przypomnią, że zainstalowano wtyczkę **klipu E (FX)** . Wybierz **przycisk OK** , aby kontynuować, a następnie postępuj zgodnie z instrukcjami kreatora, aby zainstalować go w witrynie zaćmienie Marketplace i ponownie uruchom program zaćmienie.

   ![Zainstaluj brakujące klipy wtyczki E (FX)](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Otwórz widok zadania w węźle **zadania** . W okienku po prawej stronie karta **widok zadania platformy Spark** wyświetla wszystkie aplikacje, które zostały uruchomione w klastrze. Wybierz nazwę aplikacji, dla której chcesz zobaczyć więcej szczegółów.

   ![Szczegóły dzienników zadań w widoku Apache zaćmienie](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   Następnie można wykonać jedną z następujących czynności:

   * Umieść kursor na grafie zadania. Są w nim wyświetlane podstawowe informacje o uruchomionym zadaniu. Wybierz wykres zadania i możesz zobaczyć etapy i informacje, które generuje każde zadanie.

     ![Informacje o etapie grafu zadania Apache Spark](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Wybierz kartę **Dziennik** , aby wyświetlić często używane dzienniki, w tym **sterowniki stderr**, **Sterownik stdout**i **Informacje o katalogu**.

     ![Apache Spark zaćmienie — informacje o dzienniku zadań](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * Otwórz interfejs użytkownika historii platformy Spark i interfejs użytkownika Apache Hadoop PRZĘDZy (na poziomie aplikacji), zaznaczając hiperlinki w górnej części okna.

### <a name="access-the-storage-container-for-the-cluster"></a>Dostęp do kontenera magazynu dla klastra

1. W Eksploratorze Azure rozwiń węzeł główny usługi **HDInsight** , aby wyświetlić listę dostępnych klastrów usługi HDInsight Spark.

1. Rozwiń nazwę klastra, aby wyświetlić konto magazynu i domyślny kontener magazynu dla klastra.

   ![Konto magazynu i domyślny kontener magazynu](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. Wybierz nazwę kontenera magazynu skojarzonego z klastrem. W prawym okienku kliknij dwukrotnie folder **HVACOut** . Otwórz jeden z plików **części** , aby wyświetlić dane wyjściowe aplikacji.

### <a name="access-the-spark-history-server"></a>Dostęp do serwera historii platformy Spark

1. W Eksploratorze Azure kliknij prawym przyciskiem myszy nazwę klastra Spark, a następnie wybierz pozycję **Otwórz interfejs użytkownika historii platformy Spark**. Po wyświetleniu monitu wprowadź poświadczenia administratora dla klastra. Określono je podczas aprowizacji klastra.

1. Na pulpicie nawigacyjnym serwera historii platformy Spark Użyj nazwy aplikacji, aby wyszukać aplikację, która właśnie została uruchomiona. W powyższym kodzie, należy ustawić nazwę aplikacji przy użyciu `val conf = new SparkConf().setAppName("MyClusterApp")`. Dlatego nazwa aplikacji Spark została **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Uruchamianie portalu Apache Ambari

1. W Eksploratorze Azure kliknij prawym przyciskiem myszy nazwę klastra Spark, a następnie wybierz pozycję **Otwórz klaster Portal zarządzania (Ambari)** .

1. Po wyświetleniu monitu wprowadź poświadczenia administratora dla klastra. Określono je podczas aprowizacji klastra.

### <a name="manage-azure-subscriptions"></a>Zarządzaj subskrypcjami platformy Azure

Domyślnie narzędzie HDInsight w Azure Toolkit for Eclipse wyświetla listę klastrów Spark ze wszystkich subskrypcji platformy Azure. W razie potrzeby możesz określić subskrypcje, dla których chcesz uzyskać dostęp do klastra.

1. W Eksploratorze Azure kliknij prawym przyciskiem myszy węzeł główny **platformy Azure** , a następnie wybierz pozycję **Zarządzaj subskrypcjami**.

1. W oknie dialogowym Usuń zaznaczenie pól wyboru dla subskrypcji, do której nie chcesz uzyskiwać dostępu, a następnie wybierz pozycję **Zamknij**. Możesz również wybrać opcję **Wyloguj się** , jeśli chcesz wylogować się z subskrypcji platformy Azure.

## <a name="run-a-spark-scala-application-locally"></a>Uruchamianie aplikacji platformy Spark Scala lokalnie

Za pomocą narzędzi usługi HDInsight w Azure Toolkit for Eclipse można uruchamiać aplikacje platformy Spark Scala lokalnie na stacji roboczej. Zazwyczaj te aplikacje nie potrzebują dostępu do zasobów klastra, takich jak kontener magazynu, i można je uruchamiać i testować lokalnie.

### <a name="prerequisite"></a>Wymagania wstępne

Podczas uruchamiania lokalnej aplikacji platformy Spark Scala na komputerze z systemem Windows może wystąpić wyjątek opisany w temacie [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Ten wyjątek występuje, ponieważ w systemie Windows brakuje pliku **WinUtils. exe** .

Aby rozwiązać ten problem, należy [pobrać plik wykonywalny](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do lokalizacji takiej jak **C:\WinUtils\bin**, a następnie dodać zmienną środowiskową **HADOOP_HOME** i ustawić wartość zmiennej na **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Uruchamianie lokalnej aplikacji platformy Spark Scala

1. Zacznij przezaćmienie i Utwórz projekt. W oknie dialogowym **Nowy projekt** wprowadź następujące opcje, a następnie wybierz przycisk **dalej**.

   * W lewym okienku wybierz opcję **HDInsight**.
   * W okienku po prawej stronie wybierz pozycję **Spark w usłudze HDInsight Local Run Sample (Scala)** .

   ![Nowy projekt — okno dialogowe Wybierz Kreatora](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. Aby podać szczegóły projektu, wykonaj kroki od 3 do 6 z wcześniejszej sekcji [Konfigurowanie projektu Spark Scala dla klastra usługi HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Szablon dodaje przykładowy kod (**LogQuery**) w folderze **src** , który można uruchomić lokalnie na komputerze.

   ![Lokalizacja aplikacji LogQuery Local Scala](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. Kliknij prawym przyciskiem myszy aplikację **LogQuery** , wskaż polecenie **Uruchom jako**, a następnie wybierz pozycję **1 Scala aplikację**. Dane wyjściowe podobne do tego pojawiają się na karcie **konsoli** :

   ![Wynik uruchomienia lokalnego aplikacji platformy Spark](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Rola tylko do odczytu

Gdy użytkownicy przesyłają zadanie do klastra z uprawnieniami roli tylko do odczytu, wymagane są poświadczenia Ambari.

### <a name="link-cluster-from-context-menu"></a>Połącz klaster z menu kontekstowego

1. Zaloguj się przy użyciu konta roli tylko czytelnik.

2. W **Eksploratorze Azure**rozwiń węzeł **HDInsight** , aby wyświetlić klastry usługi HDInsight, które znajdują się w Twojej subskrypcji. Klastry oznaczone jako **"role: Reader"** mają uprawnienia roli tylko do odczytu.

    ![Klastry HDInsight Spark w czytniku roli programu Azure Explorer](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. Kliknij prawym przyciskiem myszy klaster z uprawnieniami roli tylko czytelnik. Wybierz opcję **Połącz ten klaster** z menu kontekstowego, aby połączyć klaster. Wprowadź nazwę użytkownika i hasło Ambari.

    ![Linki do usługi HDInsight Spark w programie Azure Explorer](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. Jeśli klaster zostanie połączony pomyślnie, Usługa HDInsight zostanie odświeżona.
   Etap klastra zostanie połączony.
  
    ![Klastry HDInsight Spark w programie Azure Explorer połączone](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Łączenie klastra przez rozwijanie węzła zadania

1. Kliknij pozycję **zadania** węzeł, okna **odmowa dostępu do zadania klastra** .

2. Kliknij przycisk **Połącz ten klaster** , aby dołączyć klaster.

    ![Klastry HDInsight Spark na platformie Azure Explorer9](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Połącz klaster z okna przekazywania platformy Spark

1. Utwórz projekt usługi HDInsight.

2. Kliknij prawym przyciskiem myszy pakiet. Następnie wybierz pozycję **Prześlij aplikację platformy Spark do usługi HDInsight**.

   ![Przesyłanie klastrów usługi HDInsight Spark w Eksploratorze Azure](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. Wybierz klaster, który ma uprawnienia roli tylko do odczytu dla **nazwy klastra**. Komunikat ostrzegawczy jest wyświetlany. Możesz kliknąć przycisk **Połącz ten klaster** , aby połączyć klaster.

   ![Klastry HDInsight Spark w programie Azure Explorer Połącz to](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>Wyświetlanie kont magazynu

* W przypadku klastrów z uprawnieniem tylko do odczytu, kliknij węzeł **konta magazynu** , zostanie wystawione okno **odmowa dostępu do magazynu** .

   ![Klastry usługi HDInsight Spark w usłudze Azure Explorer Storage](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![Odmówiono realizacji klastrów usługi HDInsight Spark w Eksploratorze Azure](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* W przypadku połączonych klastrów kliknij węzeł **konta magazynu** , w którym znajduje się okno **odmowa dostępu do magazynu** .

   ![Klastry HDInsight Spark w Eksploratorze Azure denied2](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>Znane problemy

W przypadku połączenia z klastrem sugerujemy podanie poświadczeń magazynu.

![Połącz klaster z zastąpeniem poświadczenia magazynu](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Istnieją dwa tryby przesyłania zadań. Jeśli podano poświadczenie magazynu, do przesłania zadania zostanie użyty tryb wsadowy. W przeciwnym razie zostanie użyty tryb interaktywny. Jeśli klaster jest zajęty, może wystąpić błąd poniżej.

Zapoznaj się z ![błędem pobierania, gdy klaster jest zajęty] Zapoznaj się z (./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "błędem pobierania, gdy klaster jest zajęty")

Zapoznaj się z ![błędem pobierania w przypadku, gdy klaster jest zajęty] Zapoznaj się z (./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "błędem pobierania w przypadku, gdy klaster jest zajęty")

## <a name="seealso"></a>Zobacz też

* [Podsumowanie Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z usługą BI: Przeprowadzanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: Korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: Korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie i przesyłanie aplikacji platformy Spark Scala za pomocą Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Używanie Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark za pośrednictwem sieci VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Używanie Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark za pośrednictwem protokołu SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
