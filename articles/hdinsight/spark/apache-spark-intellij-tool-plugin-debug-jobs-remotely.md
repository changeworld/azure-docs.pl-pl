---
title: 'Azure Toolkit for IntelliJ: debugowanie aplikacji zdalnie w Spark w usłudze HDInsight | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak za pomocą narzędzi HDInsight w zestawie narzędzi Azure for IntelliJ zdalne debugowanie aplikacji Spark, działających w klastrach HDInsight za pośrednictwem sieci VPN.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 55fb454f-c7dc-46de-a978-e242e9a94f4c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 6ca69ccab6c9b526c86f6f7a8998089e52c6c939
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="use-azure-toolkit-for-intellij-to-debug-spark-applications-remotely-in-hdinsight-through-vpn"></a>Debugowanie aplikacji Spark zdalnie w usłudze HDInsight za pośrednictwem sieci VPN przy użyciu zestawu narzędzi Azure for IntelliJ

Firma Microsoft zaleca debugowanie aplikacji spark zdalnie za pośrednictwem protokołu SSH. Aby uzyskać instrukcje, zobacz [zdalne debugowanie aplikacji Spark w klastrze usługi HDInsight narzędzi Azure for IntelliJ za pośrednictwem protokołu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Ten artykuł zawiera wskazówki krok po kroku dotyczące sposobu używania narzędzi HDInsight Tools w zestawie narzędzi Azure for IntelliJ, aby przesłać zadanie Spark w klastrze Spark w usłudze HDInsight, a następnie Debuguj go zdalnie z komputera stacjonarnego. Aby wykonać te zadania, należy wykonać następujące ogólne kroki:

1. Tworzenie lokacji do lokacji lub punkt lokacja sieci wirtualnej platformy Azure. Kroki opisane w tym dokumencie Załóżmy, że sieć lokacja lokacja.
2. Tworzenie klastra Spark w usłudze HDInsight, który jest częścią sieci wirtualnej lokacja lokacja.
3. Sprawdź łączność między głównym węzłem klastra i pulpitu.
4. Tworzenie aplikacji Scala w IntelliJ IDEA, a następnie skonfigurować go do zdalnego debugowania.
5. Uruchom i debugowania aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Aby uzyskać więcej informacji, zobacz [Pobierz bezpłatną wersję próbną platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Klaster Apache Spark w usłudze HDInsight**. Aby uzyskać instrukcje, zobacz [klastrów utworzyć Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Oracle Java development kit**. Możesz zainstalować ją z [witryny sieci Web programu Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* **IntelliJ IDEA**. W tym artykule używa wersji 2017.1. Możesz zainstalować ją z [JetBrains witryny sieci Web](https://www.jetbrains.com/idea/download/).
* **Narzędzia HDInsight Tools w Azure Toolkit for IntelliJ**. Narzędzia HDInsight tools for IntelliJ są dostępne jako część zestawu narzędzi Azure for IntelliJ. Aby uzyskać instrukcje dotyczące sposobu instalowania zestawu narzędzi platformy Azure, zobacz [zainstalować zestaw narzędzi platformy Azure dla IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Zaloguj się do subskrypcji platformy Azure z rozwiązaniem IntelliJ IDEA**. Postępuj zgodnie z instrukcjami [użyj narzędzi Azure dla IntelliJ tworzenie aplikacji dla klastra usługi HDInsight Spark](apache-spark-intellij-tool-plugin.md).
* **Obejście wyjątku**. Podczas uruchamiania aplikacji Spark Scala do zdalnego debugowania na komputerze z systemem Windows, można uzyskać wyjątku. Ten wyjątek znajduje się w [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356) i występuje z powodu brakującego pliku WinUtils.exe w systemie Windows. Aby obejść ten błąd, należy najpierw [Pobierz plik wykonywalny](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do lokalizacji, takie jak **C:\WinUtils\bin**. Dodaj **HADOOP_HOME** zmiennej środowiskowej, a następnie ustaw wartość zmiennej **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Krok 1: Tworzenie sieci wirtualnej platformy Azure
Postępuj zgodnie z instrukcjami z poniższych linków, aby utworzyć sieć wirtualną platformy Azure, a następnie sprawdź łączność między komputerem stacjonarnym i siecią wirtualną:

* [Tworzenie sieci wirtualnej za pomocą połączenia sieci VPN lokacja lokacja przy użyciu portalu Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Tworzenie sieci wirtualnej za pomocą połączenia sieci VPN lokacja lokacja za pomocą programu PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Skonfiguruj połączenie punkt lokacja sieci wirtualnej przy użyciu programu PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Krok 2: Tworzenie klastra Spark w usłudze HDInsight
Zalecamy również utworzenie klastra Apache Spark w usłudze Azure HDInsight, który jest częścią sieci wirtualnej platformy Azure, który został utworzony. Skorzystaj z informacji dostępnych w [opartych na systemie Linux z tworzenia klastrów w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). W ramach konfiguracji opcjonalnej wybierz sieć wirtualna platformy Azure, który został utworzony w poprzednim kroku.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Krok 3: Sprawdź łączność między głównym węzłem klastra i pulpitu
1. Pobierz adres IP węzła głównego. Otwórz Ambari interfejsu użytkownika dla klastra. W bloku klastra, wybrać **pulpitu nawigacyjnego**.

    ![Wybierz pulpit nawigacyjny w Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
2. W interfejsie użytkownika narzędzia Ambari, wybierz **hostów**.

    ![Wybierz hosta w Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
3. Możesz wyświetlić listę węzłów głównych i węzłów procesu roboczego oraz węzły dozorcy. Mieć węzłów głównych **hn*** prefiks. Wybierz pierwszego węzła głównego.

    ![Znajdź węzła głównego w Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
4. Z **Podsumowanie** w okienku u dołu strony, który zostanie otwarty, kopiowania **adres IP** węzła głównego i **Hostname**.

    ![Znajdowanie adresu IP w Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
5. Dodaj adres IP i nazwy hosta węzła głównego do **hostów** pliku na komputerze, którym chcesz uruchomić i zdalne debugowanie zadań Spark. Dzięki temu można nawiązać połączenia z węzłem głównym przy użyciu adresu IP, a także nazwę hosta.

   a. Otwórz plik w programie Notatnik z podwyższonym poziomem uprawnień. Z **pliku** menu, wybierz opcję **Otwórz**, a następnie znajdź lokalizację pliku hostów. Na komputerze z systemem Windows jest lokalizacja **C:\Windows\System32\Drivers\etc\hosts**.

   b. Dodaj następujące informacje, aby **hostów** pliku:

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
6. Na komputerze, który podłączony do sieci wirtualnej platformy Azure, który jest używany przez klaster usługi HDInsight Sprawdź, czy można zbadać poleceniem ping węzłów głównych przy użyciu adresu IP, a także nazwę hosta.
7. Używanie protokołu SSH nawiązać połączenia z głównym węzłem klastra zgodnie z instrukcjami w [Połącz z klastrem usługi HDInsight przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Z głównym węzłem klastra Zbadaj adres IP komputera stacjonarnego. Testowanie łączności do oba adresy IP przypisane do komputera:

    - Jeden dla połączenia sieciowego
    - Jeden dla sieci wirtualnej platformy Azure

8. Powtórz kroki dla innych węzła głównego.

## <a name="step-4-create-a-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Krok 4: Tworzenie aplikacji Spark Scala przy użyciu narzędzi HDInsight w zestawie narzędzi Azure for IntelliJ i skonfiguruj ją do zdalnego debugowania
1. Otwórz środowisko IntelliJ IDEA i Utwórz nowy projekt. W **nowy projekt** okno dialogowe, wykonaj następujące czynności:

    ![Wybierz nowy szablon projektu w IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Wybierz **HDInsight** > **Spark w usłudze HDInsight (Scala)**.

    b. Wybierz opcję **Dalej**.
2. W następnej **nowy projekt** okno dialogowe, wykonaj następujące czynności, a następnie wybierz **Zakończ**:

    - Wprowadź nazwę projektu i lokalizację.

    - W **SDK projektu** listy rozwijanej wybierz **Java 1.8** dla klastra Spark 2.x lub wybierz **Java 1.7** 1.x klastra Spark.

    - W **wersji Spark** listy rozwijanej, Kreator tworzenia projektu Scala integruje poprawnej wersji zestawu SDK platformy Spark i Scala zestawu SDK. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie użyto **Spark pkt 2.0.2 (Scala 2.11.8)**.
  
   ![Wybierz wersję zestawu SDK i Spark projektu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
3. Projekt Spark automatycznie utworzy artefaktu. Aby wyświetlić artefakt, wykonaj następujące czynności:

    a. Z **pliku** menu, wybierz opcję **struktury projektu**.

    b. W **struktury projektu** okno dialogowe, wybierz opcję **artefakty** Aby wyświetlić artefaktu domyślny, który jest tworzony. Można również tworzyć własne artefaktu, wybierając znak plus (**+**).

   ![Utwórz JAR](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


4. Dodawanie biblioteki do projektu. Aby dodać biblioteki, wykonaj następujące czynności:

    a. Kliknij prawym przyciskiem myszy nazwę projektu w drzewie projektu, a następnie wybierz **Otwórz ustawienia modułu**. 

    b. W **struktury projektu** okno dialogowe, wybierz opcję **biblioteki**, wybierz pozycję (**+**) symboli, a następnie wybierz **z Maven** .

    ![Dodaj bibliotekę](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. W **pobrania biblioteki z repozytorium Maven** okna dialogowego, wyszukiwanie i dodać następujące biblioteki:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
5. Kopiuj `yarn-site.xml` i `core-site.xml` z klastra węzeł head i dodaj je do projektu. Użyj następujących poleceń, aby skopiować pliki. Można użyć [programów Cygwin](https://cygwin.com/install.html) do uruchamiania następujących `scp` węzły główne polecenia, aby skopiować pliki z klastra:

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Ponieważ firma Microsoft już dodany adres IP węzła głównego klastra i nazwy hostów dla pliku hosts na pulpicie, możemy użyć `scp` polecenia w następujący sposób:

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Aby dodać pliki do projektu, skopiuj je w obszarze **/src** folderu w drzewie z projektu, na przykład `<your project directory>\src`.
6. Aktualizacja `core-site.xml` plik, aby wprowadzić następujące zmiany:

   a. Zastąp zaszyfrowanego klucza. `core-site.xml` Plik zawiera zaszyfrowany klucz do konta magazynu skojarzone z klastrem. W `core-site.xml` pliku, który został dodany do projektu, Zamień zaszyfrowanego klucza magazynu rzeczywista klucz skojarzony z domyślnego konta magazynu. Aby uzyskać więcej informacji, zobacz [zarządzanie kluczami dostępu do magazynu](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

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
7. Dodawanie klasy głównym aplikacji. Z **Eksplorator projektów**, kliknij prawym przyciskiem myszy **src**, wskaż polecenie **nowy**, a następnie wybierz **klasy Scala**.

    ![Wybierz klasy głównym](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
8. W **Utwórz nową klasę Scala** okna dialogowego polu, podaj nazwę, wybierz **obiektu** w **rodzaj** , a następnie wybierz **OK**.

    ![Tworzenie nowej klasy Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
9. W `MyClusterAppMain.scala` pliku, wklej następujący kod. Ten kod tworzy Spark kontekstu i otwiera `executeJob` metody `SparkSample` obiektu.

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

10. Powtórz kroki od 8 do 9 do dodania nowego obiektu Scala o nazwie `*SparkSample`. Dodaj następujący kod do tej klasy. Ten kod odczytuje dane z HVAC.csv (dostępne we wszystkich klastrach Spark w usłudze HDInsight). Pobiera wiersze z tylko jedną cyfrę w kolumnie siódmego w pliku CSV, a następnie zapisuje dane wyjściowe do **/HVACOut** w obszarze domyślnego kontenera magazynu dla klastra.

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
11. Powtórz kroki od 8 i 9, aby dodać nową klasę o nazwie `RemoteClusterDebugging`. Ta klasa implementuje Spark struktury testowej, która jest używana do debugowania aplikacji. Dodaj następujący kod do `RemoteClusterDebugging` klasy:

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

     Istnieje kilka ważnych rzeczy do uwzględnienia:

      * Aby uzyskać `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, upewnij się, że zestaw Spark JAR jest dostępny w magazynie klastra w określonej ścieżce.
      * Aby uzyskać `setJars`, określ lokalizację, w której jest tworzony artefaktu JAR. Zazwyczaj jest `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
12. W`*RemoteClusterDebugging` klasy, kliknij prawym przyciskiem myszy `test` — słowo kluczowe, a następnie wybierz **Tworzenie konfiguracji RemoteClusterDebugging**.

    ![Tworzenie konfiguracji zdalnej](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

13. W **Tworzenie konfiguracji RemoteClusterDebugging** okno dialogowe, podaj nazwę dla konfiguracji, a następnie wybierz **Test rodzaj** jako **nazwa testu**. Pozostaw ustawienia domyślne wszystkich innych wartości. Wybierz **Zastosuj**, a następnie wybierz **OK**.

    ![Dodaj szczegóły konfiguracji](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
14. Powinien zostać wyświetlony **zdalnym przebiegiem** konfiguracji na liście rozwijanej na pasku menu.

    ![Zdalnego wykonywania listy rozwijanej](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Krok 5: Uruchom aplikację w trybie debugowania
1. W projekcie IntelliJ IDEA Otwórz `SparkSample.scala` i dalej, aby utworzyć punkt przerwania `val rdd1`. W **Utwórz punkt przerwania dla** menu podręczne, wybierz opcję **wiersz w funkcji executeJob**.

    ![Dodawanie punktu przerwania](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
2. Aby uruchomić aplikację, wybierz **debugowania Uruchom** znajdujący się obok **zdalnego uruchom** listy rozwijanej konfiguracji.

    ![Kliknij przycisk Uruchom debugowania](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
3. Podczas wykonywania programu osiągnie punkt przerwania, zobacz **debugera** kartę w dolnym okienku.

    ![Wyświetl kartę debugera](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
4. Aby dodać czujki, wybierz (**+**) ikona.

    ![Wybierz + ikony](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    W tym przykładzie aplikacja spowodowało przerwanie przed zmiennej `rdd1` został utworzony. Za pomocą tego czujki, zobaczysz pięć pierwszych wierszy w zmiennej `rdd`. Wybierz **wprowadź**.

    ![Uruchom program w trybie debugowania](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Zobacz na poprzedniej ilustracji jest, że w czasie wykonywania mogą zbadać terabajtów danych i debugowania sposób realizowany Twojej aplikacji. Na przykład w danych wyjściowych pokazano na poprzedniej ilustracji widać czy pierwszy wiersz danych wyjściowych jest nagłówkiem. W oparciu o te dane wyjściowe, można zmodyfikować kod aplikacji, aby pominąć wiersz nagłówka, jeśli to konieczne.
5. Można teraz wybrać **Program Wznów** ikonę, aby kontynuować uruchamianie aplikacji.

    ![Wybierz Program Wznów](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
6. Jeśli aplikacja zakończy działanie pomyślnie, powinny być widoczne dane wyjściowe podobne do następujących:

    ![Dane wyjściowe konsoli](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>Następne kroki
* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstracja
* Tworzenie projektu Scala (klip wideo): [tworzenie Spark Scala aplikacji](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Debugowanie zdalne (klip wideo): [użyj narzędzi Azure dla IntelliJ debugowanie aplikacji Spark zdalnie w klastrze usługi HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenariusze
* [Platforma Spark w usłudze BI: wykonaj interakcyjna analiza danych przy użyciu platformy Spark w usłudze HDInsight z narzędzi do analizy Biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Spark przy użyciu Machine Learning: Korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Zestaw narzędzi platformy Azure dla IntelliJ umożliwia tworzenie aplikacji Spark dla klastra usługi HDInsight](apache-spark-intellij-tool-plugin.md)
* [Debugowanie aplikacji Spark zdalnie za pośrednictwem usługi SSH przy użyciu zestawu narzędzi Azure for IntelliJ](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Użyj narzędzia HDInsight Tools for IntelliJ z Hortonworks piaskownicy](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Użyj narzędzia HDInsight Tools w zestawie narzędzi Azure dla programu Eclipse tworzenie aplikacji Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
