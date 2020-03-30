---
title: Notesy zeppelin & klastra Apache Spark — Azure HDInsight
description: Instrukcje krok po kroku dotyczące używania notesów zeppelin z klastrami Apache Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: e313048986beca1991e38ce2e65ea12f954170d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598276"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze Azure HDInsight

Klastry platformy SPARK usługi HDInsight obejmują notesy [Apache Zeppelin,](https://zeppelin.apache.org/) których można używać do uruchamiania zadań [Platformy Apache Spark.](https://spark.apache.org/) W tym artykule dowiesz się, jak używać notesu Zeppelin w klastrze HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Schemat identyfikatorów URI dla magazynu podstawowego klastrów. Byłoby to `wasb://` dla usługi Azure `abfs://` Blob Storage, dla `adl://` usługi Azure Data Lake Storage Gen2 lub dla usługi Azure Data Lake Storage Gen1. Jeśli bezpieczny transfer jest włączony dla magazynu obiektów `wasbs://`blob, identyfikator URI będzie .  Aby uzyskać więcej informacji, zobacz [Wymaganie bezpiecznego transferu w usłudze Azure Storage](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Uruchamianie notebooka Apache Zeppelin

1. W **ybraniom Klastra**Platformy Spark wybierz pozycję **Notes zeppelin** z **pulpitów nawigacyjnych klastra**. Wprowadź poświadczenia administratora dla klastra.  

   > [!NOTE]  
   > Możesz również skontaktować się z Notesem Zeppelin dla swojego klastra, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Utwórz nowy notes. W okienku nagłówka przejdź do **pozycji Notes** > **Utwórz nową notatkę**.

    ![Tworzenie nowego notesu Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Tworzenie nowego notesu Zeppelin")

    Wprowadź nazwę notesu, a następnie wybierz pozycję **Utwórz notatkę**.

3. Upewnij się, że nagłówek notesu pokazuje stan połączenia. Jest oznaczona zieloną kropką w prawym górnym rogu.

    ![Stan notebooka Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Stan notebooka Zeppelin")

4. Załaduj przykładowe dane do tabeli tymczasowej. Podczas tworzenia klastra Platformy Spark w systemie HDInsight przykładowy plik `hvac.csv`danych jest `\HdiSamples\SensorSampleData\hvac`kopiowany na skojarzone konto magazynu w obszarze .

    W pustym akapicie, który jest tworzony domyślnie w nowym notesie, wklej następujący fragment kodu.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Naciśnij **klawisze SHIFT + ENTER** lub wybierz przycisk **Odtwórz** dla akapitu, aby uruchomić fragment kodu. Stan w prawym rogu akapitu powinien przejść z GOTOWE, OCZEKUJĄCE, URUCHOMIONE do ZAKOŃCZONE. Dane wyjściowe są wyświetlane w dolnej części tego samego akapitu. Zrzut ekranu wygląda następująco:

    ![Tworzenie tabeli tymczasowej na podstawie nieprzetworzonych danych](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Tworzenie tabeli tymczasowej na podstawie nieprzetworzonych danych")

    Można również podać tytuł do każdego akapitu. W prawym rogu akapitu wybierz ikonę **Ustawienia** (koła zębatego), a następnie wybierz pozycję **Pokaż tytuł**.  

    > [!NOTE]  
    > Interpreter %spark2 nie jest obsługiwany w notesach zeppelin we wszystkich wersjach programu HDInsight, a interpreter %sh nie będzie obsługiwany od programu HDInsight 4.0.

5. Teraz można uruchomić instrukcje `hvac` PROGRAMU SPARK SQL w tabeli. Wklej następującą kwerendę w nowym akapicie. Kwerenda pobiera identyfikator budynku i różnicę między wartością docelową a rzeczywistą temperaturą dla każdego budynku w danym dniu. Naciśnij **klawisz SHIFT + ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    Instrukcja **%sql** na początku informuje notes o użyciu interpretera Livy Scala.

6. Wybierz ikonę **Wykres słupkowy,** aby zmienić wyświetlanie.  **ustawienia**, które pojawiają się po **wybraniu wykresu słupkowego,** umożliwia **wybranie opcji Klawisze**i **Wartości**.  Poniższy zrzut ekranu przedstawia dane wyjściowe.

    ![Uruchamianie instrukcji SQL platformy Spark przy użyciu notesu1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Uruchamianie instrukcji SQL platformy Spark przy użyciu notesu1")

7. Można również uruchomić instrukcje SPARK SQL przy użyciu zmiennych w kwerendzie. Następny fragment kodu pokazuje, jak zdefiniować `Temp`zmienną, w kwerendzie z możliwych wartości, które mają być kwerendy. Po pierwszym uruchomieniu kwerendy listy rozwijanej są automatycznie wypełniane wartościami określonymi dla zmiennej.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Wklej ten fragment kodu w nowym akapicie i naciśnij **klawisze SHIFT + ENTER**. Następnie wybierz **65** z listy rozwijanej **Temp.**

8. Wybierz ikonę **Wykres słupkowy,** aby zmienić wyświetlanie.  Następnie wybierz **ustawienia** i wykonuj następujące zmiany:

   * **Grupy:**  Dodaj **plik docelowy**.  
   * **Wartości:** 1. Usuń **datę**.  2. Dodaj **temp_diff**.  3.  Zmień agregator z **SUM** NA **AVG**.  

     Poniższy zrzut ekranu przedstawia dane wyjściowe.

     ![Uruchamianie instrukcji SQL platformy Spark przy użyciu notesu2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Uruchamianie instrukcji SQL platformy Spark przy użyciu notesu2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Jak używać pakietów zewnętrznych z notebookiem?

Notes zeppelin w klastrze Apache Spark można skonfigurować w programie HDInsight, aby używać zewnętrznych pakietów współtworzonych przez społeczność, które nie są zawierane w klastrze. Pełną listę pakietów, które są dostępne, można przeszukać [repozytorium Maven.](https://search.maven.org/) Można również uzyskać listę dostępnych pakietów z innych źródeł. Na przykład pełna lista pakietów udostępnionych przez społeczność jest dostępna w [pakietach Spark Packages.](https://spark-packages.org/)

W tym artykule dowiesz się, jak używać pakietu [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) z notesem Jupyter.

1. Otwórz ustawienia tłumacza. W prawym górnym rogu wybierz nazwę zalogowanego użytkownika, a następnie wybierz pozycję **Interpreter**.

    ![Uruchom interpreter](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Wyjście gałęzi")

2. Przewiń do **livy2**, a następnie wybierz pozycję **edytuj**.

    ![Zmienianie ustawień interpretera1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Zmienianie ustawień interpretera1")

3. Przejdź do `livy.spark.jars.packages`klawisza i ustaw `group:id:version`jego wartość w formacie . Tak więc, jeśli chcesz użyć pakietu [spark-csv,](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) musisz ustawić `com.databricks:spark-csv_2.10:1.4.0`wartość klucza na .

    ![Zmienianie ustawień tłumacza2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Zmienianie ustawień tłumacza2")

    Wybierz **pozycję Zapisz,** a następnie **przycisk OK,** aby ponownie uruchomić interpreter Livy.

4. Jeśli chcesz zrozumieć, jak osiągnąć wartość klucza wprowadzonego powyżej, oto jak.

    a. Znajdź pakiet w repozytorium Maven. W tym artykule użyliśmy [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).

    b. Z repozytorium zebrać wartości dla **GroupId**, **ArtifactId**i **Version**.

    ![Używanie pakietów zewnętrznych z notebookiem Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Używanie pakietów zewnętrznych z notebookiem Jupyter")

    d. Połącz trzy wartości, oddzielone dwukropkiem (**:**).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Gdzie są zapisywane notebooki Zeppelin?

Notesy Zeppelin są zapisywane w węzłach głównych klastra. Tak więc, jeśli usuniesz klaster, notesy również zostaną usunięte. Jeśli chcesz zachować notesy do późniejszego użycia w innych klastrach, należy je wyeksportować po zakończeniu uruchamiania zadań. Aby wyeksportować notes, wybierz ikonę **Eksportuj,** jak pokazano na poniższej ilustracji.

![Pobierz notes](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Pobierz notes")

Spowoduje to zapisanie notesu jako pliku JSON w lokalizacji pobierania.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Użyj shiro, aby skonfigurować dostęp do interpreterów Zeppelin w klastrach pakietów zabezpieczeń przedsiębiorstwa (ESP)
Jak wspomniano powyżej, `%sh` tłumacz nie jest obsługiwany od hdinsight 4.0. Ponadto, ponieważ `%sh` interpreter wprowadza potencjalne problemy z zabezpieczeniami, takie jak klucze dostępu przy użyciu poleceń powłoki, został on usunięty z klastrów USŁUGI HDInsight 3.6 ESP. Oznacza `%sh` to, że interpreter nie jest dostępny po kliknięciu **przycisku Utwórz nową notatkę** lub domyślnie w interfejsie użytkownika interpretera. 

Użytkownicy domeny uprzywilejowanej `Shiro.ini` mogą korzystać z pliku, aby kontrolować dostęp do interfejsu użytkownika interpretera. W związku z tym `%sh` tylko ci użytkownicy mogą `%sh` tworzyć nowe interpretery i ustawiać uprawnienia dla każdego nowego interpretera. Aby kontrolować dostęp `shiro.ini` za pomocą pliku, należy wykonać następujące czynności:

1. Zdefiniuj nową rolę przy użyciu istniejącej nazwy grupy domen. W poniższym `adminGroupName` przykładzie jest grupa uprzywilejowanych użytkowników w UAD. Nie należy używać znaków specjalnych ani spacji w nazwie grupy. Znaki po `=` nadać uprawnienia dla tej roli. `*`oznacza, że grupa ma pełne uprawnienia.

    ```
    [roles]
    adminGroupName = *
    ```

2. Dodaj nową rolę dostępu do tłumaczy zeppelin. W poniższym przykładzie `adminGroupName` wszyscy użytkownicy mają dostęp do tłumaczy zeppelin i mogą tworzyć nowe tłumacze. Można umieścić wiele ról między `roles[]`nawiasami w , oddzielone przecinkami. Następnie użytkownicy, którzy mają niezbędne uprawnienia, mogą uzyskać dostęp do tłumaczy zeppelin.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Zarządzanie sesjami Livy

Po uruchomieniu pierwszego akapitu kodu w notesie Zeppelin w klastrze platformy HDInsight Spark zostanie utworzona nowa sesja Livy. Ta sesja jest współużytkowana we wszystkich notesach zeppelin, które następnie tworzysz. Jeśli z jakiegoś powodu sesja Livy zostanie zabita (ponowne uruchomienie klastra itd.), nie będzie można uruchamiać zadań z notesu Zeppelin.

W takim przypadku należy wykonać następujące kroki, aby można było rozpocząć uruchamianie zadań z notesu zeppelin.  

1. Uruchom ponownie tłumacza Livy z notesu Zeppelin. Aby to zrobić, otwórz ustawienia tłumacza, wybierając zalogowaną nazwę użytkownika w prawym górnym rogu, a następnie wybierz **interpreter**.

    ![Uruchom interpreter](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Wyjście gałęzi")

2. Przewiń do **livy2**, a następnie wybierz **polecenie uruchom ponownie**.

    ![Ponowne uruchamianie interpretera Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Ponowne uruchamianie tłumacza zeppelin")

3. Uruchom komórkę kodu z istniejącego notesu Zeppelin. Spowoduje to utworzenie nowej sesji Livy w klastrze HDInsight.

## <a name="general-information"></a>Informacje ogólne

### <a name="validate-service"></a>Sprawdzanie poprawności usługi

Aby sprawdzić poprawność usługi z Ambari, przejdź do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` miejsca, w którym CLUSTERNAME jest nazwą klastra.

Aby sprawdzić poprawność usługi z wiersza polecenia, SSH do węzła głównego. Przełącz użytkownika na zeppelin za pomocą polecenia `sudo su zeppelin`. Polecenia stanu:

|Polecenie |Opis |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Stan usługi.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Wersja usługi.|
|`ps -aux | grep zeppelin`|Zidentyfikuj identyfikator PID.|

### <a name="log-locations"></a>Lokalizacje dzienników

|Usługa |Ścieżka |
|---|---|
|zeppelin-serwer|/usr/hdp/current/zeppelin-server/|
|Dzienniki serwera|/var/log/zeppelin|
|Interpreter konfiguracji, Shiro, site.xml, log4j|/usr/hdp/current/zeppelin-server/conf or /etc/zeppelin/conf|
|Katalog PID|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Włączanie rejestrowania debugowania

1. Przejdź `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` do miejsca, w którym nazwa klastra jest nazwą klastra.

1. Przejdź do **log4j_properties_content** > **właściwości zeppelin-log4j-** > **log4j_properties_content.**

1. Zmodyfikuj `log4j.appender.dailyfile.Threshold = INFO` do `log4j.appender.dailyfile.Threshold = DEBUG`.

1. Dodaj `log4j.logger.org.apache.zeppelin.realm=DEBUG`.

1. Zapisz zmiany i uruchom ponownie usługę.

## <a name="next-steps"></a>Następne kroki

[Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z analizą biznesową: wykonywanie interaktywnej analizy danych przy użyciu funkcji Spark w funkcji HDInsight za pomocą narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z uczeniem maszynowym: użyj platformy Spark w hdinsight do analizowania temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika strony internetowej przy użyciu platformy Apache Spark w hdinsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Użyj wtyczki HDInsight Tools dla IntelliJ IDEA, aby utworzyć i przesłać aplikacje Apache Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki HDInsight Tools dla IntelliJ IDEA do zdalnego debugowania aplikacji Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Jądra dostępne dla notebooka Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
