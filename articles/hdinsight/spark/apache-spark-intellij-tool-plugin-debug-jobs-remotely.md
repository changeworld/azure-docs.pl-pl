---
title: 'Zestaw narzędzi platformy Azure: zdalnie debugowanie aplikacji Platformy Spark — usługa Azure HDInsight'
description: Dowiedz się, jak używać narzędzi HDInsight w usłudze Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Platformy Spark uruchamianych w klastrach HDInsight za pośrednictwem sieci VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 393356bd8604f6e7622acd778817681aad31f1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935017"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Użyj zestawu narzędzi Azure toolkit dla intellij do zdalnego debugowania aplikacji Apache Spark w usłudze HDInsight przez sieć VPN

Zalecamy debugowanie aplikacji [Apache Spark](https://spark.apache.org/) zdalnie za pośrednictwem SSH. Aby uzyskać [instrukcje, zobacz Zdalne debugowanie aplikacji Platformy Spark w klastrze USŁUGI HDInsight za pomocą zestawu narzędzi Azure toolkit dla technologii IntelliJ za pośrednictwem programu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Ten artykuł zawiera wskazówki krok po kroku dotyczące używania narzędzi HDInsight Tools w usłudze Azure Toolkit for IntelliJ do przesyłania zadania platformy Spark w klastrze platformy SPARK usługi HDInsight, a następnie zdalnego debugowania go z komputera stacjonarnego. Aby wykonać te zadania, należy wykonać następujące kroki wysokiego poziomu:

1. Utwórz sieć wirtualną platformy Azure typu lokacja-lokacja lub punkt-lokacja. Kroki opisane w tym dokumencie zakładają, że używasz sieci lokacja-lokacja.
1. Utwórz klaster platformy Spark w układzie HDInsight, który jest częścią sieci wirtualnej lokacja lokacja.
1. Sprawdź łączność między węzłem głównym klastra a pulpitem.
1. Utwórz aplikację Scala w IntelliJ IDEA, a następnie skonfiguruj ją do zdalnego debugowania.
1. Uruchamianie i debugowanie aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Aby uzyskać więcej informacji, zobacz [Uzyskaj bezpłatną wersję próbną platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Klaster Platformy Spark Apache w pliku HDInsight**. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Zestaw deweloperski Oracle Java**. Można go zainstalować na [stronie internetowej Oracle](https://aka.ms/azure-jdks).
* **Intellij IDEA**. W tym artykule użyto wersji 2017.1. Można go zainstalować ze [strony internetowej JetBrains](https://www.jetbrains.com/idea/download/).
* **Narzędzia HDInsight w usłudze Azure Toolkit dla intellij**. Narzędzia HDInsight dla intellij są dostępne jako część zestawu narzędzi Azure toolkit dla intellij. Aby uzyskać instrukcje dotyczące instalowania zestawu narzędzi platformy Azure, zobacz [Instalowanie zestawu narzędzi platformy Azure dla technologii IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Zaloguj się do subskrypcji platformy Azure z IntelliJ IDEA**. Postępuj zgodnie z instrukcjami zawartymi w [usłudze Użyj zestawu narzędzi platformy Azure dla intellij, aby utworzyć aplikacje Apache Spark dla klastra HDInsight](apache-spark-intellij-tool-plugin.md).
* **Obejście wyjątku**. Podczas uruchamiania aplikacji Spark Scala do zdalnego debugowania na komputerze z systemem Windows może pojawić się wyjątek. Ten wyjątek jest wyjaśniony w [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) i występuje z powodu braku pliku WinUtils.exe w systemie Windows. Aby obejść ten błąd, należy pobrać [plik Winutils.exe](https://github.com/steveloughran/winutils) do lokalizacji takiej jak **C:\WinUtils\bin**. Dodaj **HADOOP_HOME** zmienną środowiskową, a następnie ustaw wartość zmiennej na **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Krok 1: Tworzenie sieci wirtualnej platformy Azure

Postępuj zgodnie z instrukcjami z następujących łączy, aby utworzyć sieć wirtualną platformy Azure, a następnie sprawdź łączność między komputerem stacjonarnym a siecią wirtualną:

* [Tworzenie sieci wirtualnej z połączeniem sieci VPN między lokacjami za pomocą portalu Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Tworzenie sieci wirtualnej z połączeniem sieci VPN typu lokacja lokacja przy użyciu programu PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Konfigurowanie połączenia typu "punkt-lokacja" z siecią wirtualną przy użyciu programu PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Krok 2: Tworzenie klastra platformy Spark usługi HDInsight

Zaleca się również utworzenie klastra Platformy Spark Apache w usłudze Azure HDInsight, który jest częścią utworzonej sieci wirtualnej platformy Azure. Użyj informacji dostępnych w [programach Tworzenie klastrów opartych na systemie Linux w umiańskim pliku HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). W ramach konfiguracji opcjonalnej wybierz sieć wirtualną platformy Azure utworzoną w poprzednim kroku.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Krok 3: Sprawdź łączność między węzłem głównym klastra a pulpitem

1. Pobierz adres IP węzła głównego. Otwórz interfejs użytkownika Ambari dla klastra. W bloku klastra wybierz pozycję **Pulpit nawigacyjny**.

    ![Wybierz pulpit nawigacyjny w Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png)

1. W interfejsie użytkownika Ambari wybierz pozycję **Hosty**.

    ![Wybierz hosty w Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png)

1. Widoczna jest lista węzłów głównego, węzłów procesu roboczego i węzłów zookeeper. Węzły głowy mają prefiks **hn***. Wybierz pierwszy węzeł główny.

    ![Znajdź węzeł główny w Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png)

1. W okienku **Podsumowanie** u dołu otwieranej strony skopiuj **adres IP** węzła głównego i **nazwy hosta**.

    ![Znajdź adres IP w Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png)

1. Dodaj adres IP i nazwę hosta węzła głównego do pliku **hosts** na komputerze, na którym chcesz uruchomić i zdalnie debugować zadanie Platformy Spark. Dzięki temu można komunikować się z węzłem głównym przy użyciu adresu IP, a także nazwy hosta.

   a. Otwórz plik Notatnika z podwyższonymi uprawnieniami. Z menu **Plik** wybierz polecenie **Otwórz**, a następnie znajdź lokalizację pliku hostów. Na komputerze z systemem Windows lokalizacją jest **C:\Windows\System32\Drivers\etc\hosts**.

   b. Dodaj następujące informacje do pliku **hosts:**

    ```
    # For headnode0
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. Z komputera podłączonego do sieci wirtualnej platformy Azure, która jest używana przez klaster HDInsight, sprawdź, czy można ping węzłów głównego przy użyciu adresu IP, a także nazwy hosta.

1. Użyj SSH, aby połączyć się z węzłem głównym klastra, postępując zgodnie z instrukcjami w [połącz do klastra HDInsight przy użyciu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). W węźle głównym klastra należy wysyłć adres IP komputera stacjonarnego. Przetestuj łączność z obydwoma adresami IP przypisaną do komputera:

    - Jeden dla połączenia sieciowego
    - Jeden dla sieci wirtualnej platformy Azure

1. Powtórz kroki dla drugiego węzła głównego.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Krok 4: Utwórz aplikację Apache Spark Scala przy użyciu narzędzi HDInsight w usłudze Azure Toolkit dla technologii IntelliJ i skonfiguruj ją do zdalnego debugowania

1. Otwórz IntelliJ IDEA i utwórz nowy projekt. W oknie dialogowym **Nowy projekt** wykonaj następujące czynności:

    ![Wybierz nowy szablon projektu w intellij idea](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Wybierz **HDInsight** > **Spark na HDInsight (Scala)**.

    b. Wybierz **pozycję Dalej**.
1. W następnym oknie dialogowym **Nowy projekt** wykonaj następujące czynności, a następnie wybierz pozycję **Zakończ:**

    - Wprowadź nazwę i lokalizację projektu.

    - Z listy rozwijanej **Zestaw SDK projektu** wybierz pozycję **Java 1.8** dla klastra Spark 2.x lub pozycję **Java 1.7** dla klastra Spark 1.x.

    - Na liście rozwijanej **wersji platformy Spark** kreator tworzenia projektów Scala integruje właściwą wersję dla zestawie Spark SDK i scala SDK. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie używana jest wersja **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Wybieranie wersji SDK i Spark projektu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. Projekt Spark automatycznie tworzy artefakt dla Ciebie. Aby wyświetlić artefakt, wykonaj następujące czynności:

    a. Z menu **Plik** wybierz polecenie **Struktura projektu**.

    b. W oknie dialogowym **Struktura projektu** wybierz **pozycję Artefakty,** aby wyświetlić utworzony domyślny artefakt. Można również utworzyć własny artefakt, wybierając znak**+** plus ( ).

   ![Artefakty IntelliJ IDEA tworzą słoik](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png)

1. Dodawanie bibliotek do projektu. Aby dodać bibliotekę, wykonaj następujące czynności:

    a. Kliknij prawym przyciskiem myszy nazwę projektu w drzewie projektu, a następnie wybierz polecenie **Otwórz ustawienia modułu**.

    b. W oknie dialogowym **Struktura projektu** wybierz pozycję**+** **Biblioteki**, wybierz symbol ( ), a następnie wybierz pozycję Z **maven**.

    ![Biblioteka pobierania IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png)

    d. W oknie dialogowym **Pobieranie biblioteki z repozytorium Maven** wyszukaj i dodaj następujące biblioteki:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. Skopiuj `yarn-site.xml` i `core-site.xml` z węzła głównego klastra i dodaj je do projektu. Użyj następujących poleceń, aby skopiować pliki. Za pomocą [programu Cygwin](https://cygwin.com/install.html) `scp` można uruchamiać następujące polecenia w celu skopiowania plików z węzłów głównego klastra:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Ponieważ dodaliśmy już adres IP węzła głównego klastra i nazwy hostów dla `scp` pliku hosts na pulpicie, możemy użyć poleceń w następujący sposób:

    ```bash
    scp sshuser@nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Aby dodać te pliki do projektu, skopiuj je w `<your project directory>\src`folderze **/src** w drzewie projektu, na przykład .

1. Zaktualizuj plik, `core-site.xml` aby wprowadzić następujące zmiany:

   a. Wymień zaszyfrowany klucz. Plik `core-site.xml` zawiera zaszyfrowany klucz do konta magazynu skojarzonego z klastrem. W `core-site.xml` pliku dodanym do projektu zastąp zaszyfrowany klucz rzeczywistym kluczem magazynu skojarzonym z domyślnym kontem magazynu. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Usuń następujące wpisy `core-site.xml`z :

    ```xml
    <property>
            <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
            <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
    </property>

    <property>
            <name>fs.azure.shellkeyprovider.script</name>
            <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
    </property>

    <property>
            <name>net.topology.script.file.name</name>
            <value>/etc/hadoop/conf/topology_script.py</value>
    </property>
    ```

   d. Zapisz plik.

1. Dodaj klasę główną dla aplikacji. W **Eksploratorze projektu**kliknij prawym przyciskiem **myszy polecenie SRC**, wskaż polecenie **Nowy**, a następnie wybierz **klasę Scala**.

    ![IntelliJ IDEA Wybierz klasę główną](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

1. W oknie dialogowym **Tworzenie nowej klasy scali** podaj nazwę, wybierz **pozycję Obiekt** w polu **Rodzaj,** a następnie wybierz przycisk **OK**.

    ![IntelliJ IDEA Tworzenie nowej klasy Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

1. W `MyClusterAppMain.scala` pliku wklej następujący kod. Ten kod tworzy kontekst Platformy `executeJob` Spark `SparkSample` i otwiera metodę z obiektu.

    ```scala
    import org.apache.spark.{SparkConf, SparkContext}

    object SparkSampleMain {
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("SparkSample")
                                    .set("spark.hadoop.validateOutputSpecs", "false")
        val sc = new SparkContext(conf)

        SparkSample.executeJob(sc,
                            "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                            "wasb:///HVACOut")
        }
    }
    ```

1. Powtórz kroki 8 i 9, aby `*SparkSample`dodać nowy obiekt Scala o nazwie . Dodaj następujący kod do tej klasy. Ten kod odczytuje dane z pliku HVAC.csv (dostępne we wszystkich klastrach platformy SPARK usługi HDInsight). Pobiera wiersze, które mają tylko jedną cyfrę w siódmej kolumnie w pliku CSV, a następnie zapisuje dane wyjściowe do **/HVACOut** w domyślnym kontenerze magazynu dla klastra.

    ```scala
    import org.apache.spark.SparkContext

    object SparkSample {
        def executeJob (sc: SparkContext, input: String, output: String): Unit = {
        val rdd = sc.textFile(input)

        //find the rows which have only one digit in the 7th column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
        println(s)

        rdd1.saveAsTextFile(output)
        //rdd1.collect().foreach(println)
         }
    }
    ```

1. Powtórz kroki 8 i 9, `RemoteClusterDebugging`aby dodać nową klasę o nazwie . Ta klasa implementuje platformę testów platformy Spark, która jest używana do debugowania aplikacji. Dodaj następujący kod `RemoteClusterDebugging` do klasy:

    ```scala
        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }
    ```

     Istnieje kilka ważnych rzeczy, na które należy zwrócić uwagę:

      * Dla `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, upewnij się, że zestaw Spark JAR jest dostępny w magazynie klastra w określonej ścieżce.
      * Dla `setJars`, określ lokalizację, w której tworzony jest artefakt JAR. Zazwyczaj jest `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`to .

1. W`*RemoteClusterDebugging` klasie kliknij prawym `test` przyciskiem myszy słowo kluczowe, a następnie wybierz polecenie **Utwórz konfigurację zdalnego blokowania**.

    ![IntelliJ IDEA Tworzenie konfiguracji zdalnej](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. W oknie dialogowym **Tworzenie konfiguracji zdalnego blokowania** należy podać nazwę konfiguracji, a następnie wybrać opcję **Testuj jako** nazwę **testu**. Pozostaw wszystkie inne wartości jako ustawienia domyślne. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.

    ![Tworzenie konfiguracji zdalnego sterowaniaClusterDebugging](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)

1. Na pasku menu powinna zostać wyświetlona lista rozwijana konfiguracji **uruchamiania zdalnego.**

    ![IntelliJ Lista rozwijana zdalnego uruchamiania](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Krok 5: Uruchamianie aplikacji w trybie debugowania

1. W projekcie IntelliJ IDEA `SparkSample.scala` otwórz i utwórz `val rdd1`punkt przerwania obok pozycji . W menu podręcznym **Utwórz punkt przerwania** wybierz **linię w funkcji executeJob**.

    ![IntelliJ IDEA Dodaj punkt przerwania](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png)

1. Aby uruchomić aplikację, wybierz przycisk **Uruchom debugowanie** obok listy rozwijanej Konfiguracji **uruchamiania zdalnego.**

    ![IntelliJ IDEA Wybierz przycisk Uruchom debugowanie](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png)

1. Gdy wykonanie programu osiągnie punkt przerwania, w dolnym okienku pojawi się karta **Debuger.**

    ![IntelliJ IDEA Wyświetl kartę Debuger](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png)

1. Aby dodać zegarek, wybierz**+** ikonę ( ).

    ![IntelliJ debug-add-watch-zmienna](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    W tym przykładzie aplikacja została `rdd1` utworzona przed utworzeniem zmiennej. Za pomocą tego zegarka, możemy zobaczyć pierwsze `rdd`pięć wierszy w zmiennej . Wybierz **pozycję Wprowadź**.

    ![IntelliJ Uruchom program w trybie debugowania](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Co widać w poprzednim obrazie jest to, że w czasie wykonywania, można zbadać terabajty danych i debugować, jak aplikacja postępuje. Na przykład na wyjściu pokazanym na poprzednim obrazie widać, że pierwszy wiersz danych wyjściowych jest nagłówkiem. Na podstawie tego danych wyjściowych można zmodyfikować kod aplikacji, aby pominąć wiersz nagłówka, jeśli to konieczne.

1. Teraz można wybrać ikonę **Wznów program,** aby kontynuować uruchamianie aplikacji.

    ![IntelliJ IDEA Wybierz program Wznów](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png)

1. Jeśli aplikacja zakończy się pomyślnie, powinny być widoczne dane wyjściowe następujące:

    ![Wyjście konsoli debugera IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png)

## <a name="next-steps"></a><a name="seealso"></a>Następne kroki

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstracja

* Utwórz projekt Scala (wideo): [Tworzenie aplikacji Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Zdalne debugowanie (wideo): [używanie zestawu narzędzi Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark w klastrze USŁUGI HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z analizą biznesową: wykonywanie interaktywnej analizy danych przy użyciu funkcji Spark in HDInsight za pomocą narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight do analizy temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika strony internetowej przy użyciu platformy Apache Spark w hdinsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie aplikacji Apache Spark dla klastra USŁUGI HDInsight za pomocą zestawu narzędzi Azure toolkit for IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Użyj zestawu narzędzi Azure toolkit dla intellij do zdalnego debugowania aplikacji Apache Spark za pośrednictwem SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Tworzenie aplikacji Apache Spark za pomocą narzędzi HDInsight Tools w usłudze Azure Toolkit for Eclipse](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Używanie notebooków Apache Zeppelin z klastrem Apache Spark w udziale HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notebooka Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamiane w klastrze Platformy Spark apache w programie HDInsight](apache-spark-job-debugging.md)
