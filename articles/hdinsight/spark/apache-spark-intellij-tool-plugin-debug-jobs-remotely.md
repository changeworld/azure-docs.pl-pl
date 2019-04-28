---
title: 'Zestaw narzędzi platformy Azure dla środowiska IntelliJ: Debugowanie aplikacji zdalnie w HDInsight Spark '
description: Dowiedz się, jak za pomocą narzędzi HDInsight w usłudze Azure Toolkit for IntelliJ zdalne debugowanie aplikacji Spark, które działały w klastrach HDInsight za pośrednictwem sieci VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 30d52f1ac6a68a3202de59a0b4cab8edfb7ed042
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124340"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Debugowanie aplikacji platformy Apache Spark, zdalnie w HDInsight za pośrednictwem sieci VPN przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ

Zaleca się profilowanie [platformy Apache Spark](https://spark.apache.org/) aplikacje zdalne za pośrednictwem protokołu SSH. Aby uzyskać instrukcje, zobacz [zdalne debugowanie aplikacji platformy Apache Spark w klastrze usługi HDInsight przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ za pośrednictwem protokołu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Ten artykuł zawiera wskazówki krok po kroku dotyczące sposobu korzystania z narzędzi HDInsight w usłudze Azure Toolkit for IntelliJ do przesyłania zadania Spark w klastrze usługi HDInsight Spark, a następnie ją debugować zdalnie z komputera stacjonarnego. Aby wykonać te zadania, należy wykonać następujące kroki ogólne:

1. Tworzenie lokacja lokacja lub punkt lokacja sieci wirtualnej platformy Azure. Kroki opisane w tym dokumencie przyjęto założenie, użyć sieci lokacja lokacja.
1. Tworzenie klastra Spark w HDInsight, który jest częścią sieci wirtualnej lokacja lokacja.
1. Sprawdź łączność między głównym węzłem klastra i pulpitu.
1. Tworzenie aplikacji w języku Scala w IntelliJ IDEA, a następnie skonfigurować go dla zdalnego debugowania.
1. Uruchamianie i debugowanie aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Aby uzyskać więcej informacji, zobacz [bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Klaster Apache Spark w HDInsight**. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Zestawu Oracle Java development kit**. Można zainstalować go z [witryny internetowej Oracle](https://aka.ms/azure-jdks).
* **IntelliJ IDEA**. W tym artykule używa wersji 2017.1. Można zainstalować go z [JetBrains witryny sieci Web](https://www.jetbrains.com/idea/download/).
* **Narzędzia HDInsight w usłudze Azure Toolkit for IntelliJ**. Narzędzia HDInsight tools for IntelliJ są dostępne w ramach zestawu narzędzi platformy Azure dla środowiska IntelliJ. Aby uzyskać instrukcje dotyczące sposobu instalowania zestawu narzędzi platformy Azure, zobacz [zainstalować Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Zaloguj się do subskrypcji platformy Azure z rozwiązaniem IntelliJ IDEA**. Postępuj zgodnie z instrukcjami w [użycia Azure Toolkit for IntelliJ tworzyć aplikacje platformy Apache Spark dla klastra usługi HDInsight](apache-spark-intellij-tool-plugin.md).
* **Wyjątek obejścia**. Podczas uruchamiania aplikacji Spark Scala dla zdalnego debugowania na komputerze Windows, możesz otrzymać wyjątek. Ten wyjątek została wyjaśniona w [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356) i występuje ze względu na Brak pliku WinUtils.exe w Windows. Aby obejść ten błąd, należy najpierw [Pobierz plik wykonywalny](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do lokalizacji, takich jak **C:\WinUtils\bin**. Dodaj **HADOOP_HOME** zmiennej środowiskowej, a następnie ustaw wartość zmiennej do **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Krok 1: Tworzenie sieci wirtualnej platformy Azure
Postępuj zgodnie z instrukcjami z następujących linków, aby utworzyć sieć wirtualną platformy Azure, a następnie sprawdź łączność między komputerem stacjonarnym, a siecią wirtualną:

* [Tworzenie sieci wirtualnej za pomocą połączenia sieci VPN lokacja lokacja przy użyciu witryny Azure portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Tworzenie sieci wirtualnej za pomocą połączenia sieci VPN lokacja lokacja przy użyciu programu PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Konfigurowanie połączenia punkt lokacja z siecią wirtualną przy użyciu programu PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Krok 2: Tworzenie klastra Spark w usłudze HDInsight
Firma Microsoft zaleca również utworzyć klaster Apache Spark w usłudze Azure HDInsight, który jest częścią sieci wirtualnej platformy Azure, który został utworzony. Skorzystaj z informacji dostępnych w [opartych na systemie Linux z Tworzenie klastrów w HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). W ramach konfiguracji opcjonalnej Wybieranie sieci wirtualnej platformy Azure, który został utworzony w poprzednim kroku.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Krok 3: Sprawdź łączność między głównym węzłem klastra i pulpitu
1. Uzyskaj adres IP węzła głównego. Otwórz interfejs użytkownika systemu Ambari klastra. W bloku klastra wybierz **pulpit nawigacyjny**.

    ![Wybierz pulpit nawigacyjny systemu Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
1. Wybierz z interfejsu użytkownika Ambari **hosty**.

    ![Wybierz hosty systemu Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
1. Możesz wyświetlić listę węzłów głównych, węzłów procesu roboczego i węzły dozorcy. Węzły główne mają **hn*** prefiks. Wybierz pierwszy węzeł główny.

    ![Znajdź węzeł główny w Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
1. Z **Podsumowanie** okienku u dołu strony, która zostanie otwarta kopia **adresu IP** węzła głównego i **Hostname**.

    ![Adres IP znaleźć systemu Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
1. Dodaj adres IP i nazwa hosta węzła głównego do **hosty** pliku na komputerze, w którym chcesz uruchomić i debugować zdalnie zadanie platformy Apache Spark. Dzięki temu można nawiązać połączenia z węzłem za pomocą adresu IP, a także nazwę hosta.

   a. Otwórz plik w programie Notatnik z podwyższonym poziomem uprawnień. Z **pliku** menu, wybierz opcję **Otwórz**, a następnie znajdź lokalizację pliku hosts. Na komputerze Windows lokalizacja jest **C:\Windows\System32\Drivers\etc\hosts**.

   b. Dodaj następujące informacje, aby **hosty** pliku:

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
1. Na komputerze, który podłączony do sieci wirtualnej platformy Azure, który jest używany przez klaster usługi HDInsight upewnij się, że możesz wysłać polecenie ping do węzłów głównych przy użyciu adresu IP, a także nazwę hosta.
1. Używanie protokołu SSH, aby nawiązać połączenie z głównym węzłem klastra, postępując zgodnie z instrukcjami w [łączenie z klastrem usługi HDInsight przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Wyślij komunikat ping adres IP komputera stacjonarnego z węzła głównego klastra. Sprawdź łączność z obu adresów IP przypisanych do komputera:

    - Jeden dla połączenia sieciowego
    - Jeden dla sieci wirtualnej platformy Azure

1. Powtórz kroki dla innych węzła głównego.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Krok 4: Tworzenie aplikacji Apache Spark Scala przy użyciu narzędzi HDInsight zestawu narzędzi platformy Azure dla środowiska IntelliJ i skonfigurować go na potrzeby debugowania zdalnego
1. Otwórz IntelliJ IDEA i Utwórz nowy projekt. W oknie dialogowym **Nowy projekt** wykonaj następujące czynności:

    ![Wybierz nowy szablon projektu w IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Wybierz pozycję **HDInsight** > **Spark w usłudze HDInsight (Scala)**.

    b. Wybierz opcję **Dalej**.
1. W ciągu następnych **nowy projekt** okno dialogowe, wykonaj następujące czynności, a następnie wybierz **Zakończ**:

    - Wprowadź nazwę i lokalizację projektu.

    - Z listy rozwijanej **Zestaw SDK projektu** wybierz pozycję **Java 1.8** dla klastra Spark 2.x lub pozycję **Java 1.7** dla klastra Spark 1.x.

    - W **wersji platformy Spark** listy rozwijanej, Kreator tworzenia projektu w języku Scala integruje się odpowiednia wersja zestawu SDK platformy Spark i Scala zestawu SDK. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie używana jest wersja **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Wybierz wersję zestawu SDK i platformy Spark projektu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. Spark project automatycznie tworzy artefaktu. Aby wyświetlić artefakt, wykonaj następujące czynności:

    a. W menu **File** (Plik) wybierz pozycję **Project Structure** (Struktura projektu).

    b. W **struktury projektu** okno dialogowe, wybierz opcję **artefaktów** Aby wyświetlić artefakt domyślny, który zostanie utworzony. Można również utworzyć własne artefaktu, wybierając znak plus (**+**).

   ![Tworzenie pliku JAR](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


1. Dodawanie biblioteki do projektu. Aby dodać bibliotekę, wykonaj następujące czynności:

    a. Kliknij prawym przyciskiem myszy nazwę projektu, w drzewie projektu, a następnie wybierz **Otwórz ustawienia modułu**. 

    b. W **struktury projektu** okno dialogowe, wybierz opcję **bibliotek**, wybierz opcję (**+**) symboli, a następnie wybierz **z narzędzia Maven** .

    ![Dodaj bibliotekę](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. W **pobrania biblioteki z repozytorium Maven** okna dialogowego pole, wyszukaj i dodaj następujące biblioteki:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
1. Kopiuj `yarn-site.xml` i `core-site.xml` z klastra węzeł w elemencie head i dodać je do projektu. Użyj następujących poleceń, aby skopiować pliki. Możesz użyć [Cygwin](https://cygwin.com/install.html) uruchom następujące `scp` węzłami głównymi polecenia, aby skopiować pliki z klastra:

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Ponieważ firma Microsoft już dodany adres IP węzła głównego klastra i nazwy hostów dla pliku hosts na pulpicie, możemy użyć `scp` poleceń w następujący sposób:

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Aby dodać te pliki do projektu, skopiuj je w obszarze **/src** folderów w drzewie Twojego projektu, na przykład `<your project directory>\src`.
1. Aktualizacja `core-site.xml` plik, aby wprowadzić następujące zmiany:

   a. Zastąp zaszyfrowany klucz. `core-site.xml` Plik zawiera zaszyfrowany klucz konta magazynu skojarzonego z klastrem. W `core-site.xml` pliku, który został dodany do projektu, Zamień na klucz rzeczywisty magazyn skojarzony z domyślnego konta magazynu zaszyfrowanego klucza. Aby uzyskać więcej informacji, zobacz [zarządzanie kluczami dostępu do magazynu](../../storage/common/storage-account-manage.md#access-keys).

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   b. Usuń następujące wpisy z `core-site.xml`:

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
   c. Zapisz plik.
1. Dodawanie klasy głównej aplikacji. Z **Eksplorator projektów**, kliknij prawym przyciskiem myszy **src**, wskaż polecenie **nowy**, a następnie wybierz pozycję **klasy Scala**.

    ![Wybierz klasę głównego](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
1. W **Utwórz nową klasę Scala** okna dialogowego pole, wprowadź nazwę, wybierz **obiektu** w **rodzaj** , a następnie wybierz **OK**.

    ![Utwórz nową klasę Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
1. W `MyClusterAppMain.scala` plików, wklej następujący kod. Ten kod tworzy platformy Spark w kontekście i zostanie otwarta `executeJob` metody z `SparkSample` obiektu.

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

1. Powtórz kroki 8 i 9, aby dodać nowy obiekt Scala o nazwie `*SparkSample`. Dodaj następujący kod do klasy. Ten kod odczytuje dane z HVAC.csv (dostępne we wszystkich klastrach HDInsight Spark). Pobiera wiersze z tylko jedną cyfrę w siódmej kolumnie w pliku CSV, a następnie zapisuje dane wyjściowe do **/HVACOut** w ramach domyślnego kontenera magazynu dla klastra.

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
1. Powtórz kroki 8 i 9, aby dodać nową klasę o nazwie `RemoteClusterDebugging`. Ta klasa implementuje struktury testowej platformy Spark, która jest używana do debugowania aplikacji. Dodaj następujący kod do `RemoteClusterDebugging` klasy:

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

     Istnieje kilka ważnych kwestii, które należy zwrócić uwagę:

      * Aby uzyskać `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, upewnij się, zestaw Spark JAR jest dostępny w magazynie klastra, w określonej ścieżce.
      * Aby uzyskać `setJars`, określ lokalizację, w której został utworzony plik JAR artefaktu. Zazwyczaj jest `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
1. W`*RemoteClusterDebugging` klas kliknij prawym przyciskiem myszy `test` — słowo kluczowe, a następnie wybierz **Tworzenie konfiguracji RemoteClusterDebugging**.

    ![Utwórz konfigurację zdalnego](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. W **Tworzenie konfiguracji RemoteClusterDebugging** okno dialogowe, podaj nazwę dla konfiguracji, a następnie wybierz **Test rodzaj** jako **nazwa testu**. Wszystkie pozostałe wartości pozostaw ustawienia domyślne. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.

    ![Dodaj szczegóły konfiguracji](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
1. Powinien zostać wyświetlony **zdalnym przebiegiem** konfiguracji na liście rozwijanej na pasku menu.

    ![Zdalnego wykonywania listy rozwijanej](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Krok 5. Uruchom aplikację w trybie debugowania
1. Otwórz w projekcie rozwiązania IntelliJ IDEA `SparkSample.scala` i utworzyć punkt przerwania obok `val rdd1`. W **utworzyć punkt przerwania dla** wyskakujących menu, wybierz opcję **wiersz w funkcji executeJob**.

    ![Dodawanie punktu przerwania](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
1. Aby uruchomić aplikację, wybierz pozycję **debugowanie Uruchom** znajdujący się obok **zdalnego uruchamiania** listy rozwijanej konfiguracji.

    ![Kliknij przycisk Uruchom debugowanie](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
1. Podczas wykonywania programu osiąga punkt przerwania, zostanie wyświetlony **debugera** kartę w dolnym okienku.

    ![Wyświetl kartę debugera](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
1. Aby dodać wyrażenie kontrolne, wybierz (**+**) ikona.

    ![Wybierz ikonę +](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    W tym przykładzie aplikacja Przerwano przed zmiennej `rdd1` został utworzony. Za pomocą tego wyrażenie kontrolne, możemy zobaczyć pięciu pierwszych wierszy w zmiennej `rdd`. Kliknij przycisk **Wprowadź**.

    ![Uruchom program w trybie debugowania](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Jest to, co widać na poprzedniej ilustracji, czy w czasie wykonywania, użytkownik może wysyłać zapytania o terabajtów danych i debugowania jak postępów Twojej aplikacji. Na przykład w danych wyjściowych pokazano na poprzedniej ilustracji możesz zobaczyć, że pierwszy wiersz danych wyjściowych jest nagłówka. Oparte na tych danych wyjściowych, można modyfikować kodu aplikacji, aby pominąć wiersz nagłówka, jeśli to konieczne.
1. Teraz możesz wybrać **Program Wznów** ikonę, aby kontynuować uruchamianie aplikacji.

    ![Wybierz Program Wznów](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
1. Jeśli aplikację pomyślnym zakończeniu powinny pojawić się dane wyjściowe podobne do następujących:

    ![Dane wyjściowe konsoli](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>Następne kroki
* [Omówienie: Platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstracja
* Utwórz projekt Scala (wideo): [Tworzenie aplikacji programu Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Debugowanie zdalne (wideo): [Debugowanie aplikacji platformy Apache Spark, zdalnie w klastrze usługi HDInsight przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenariusze
* [Platforma Apache Spark przy użyciu Power BI: Wykonaj interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika witryny sieci Web przy użyciu platformy Apache Spark w HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Azure Toolkit for IntelliJ umożliwia tworzenie aplikacji platformy Apache Spark dla klastra usługi HDInsight](apache-spark-intellij-tool-plugin.md)
* [Debugowanie aplikacji platformy Apache Spark, zdalnie za pośrednictwem protokołu SSH przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Korzystaj z narzędzi HDInsight dla programu IntelliJ z Piaskownicą Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Używanie narzędzia HDInsight w zestaw narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji platformy Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Apache Zeppelin przy użyciu klastra Apache Spark w HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze usługi Apache Spark w HDInsight](apache-spark-job-debugging.md)
