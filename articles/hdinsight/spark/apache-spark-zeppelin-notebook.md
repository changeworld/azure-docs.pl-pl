---
title: Zeppelin notesy & Apache Spark klastra — Azure HDInsight
description: Instrukcje krok po kroku dotyczące korzystania z notesów Zeppelin z klastrami Apache Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: e313048986beca1991e38ce2e65ea12f954170d2
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598276"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze Azure HDInsight

Klastry usługi HDInsight Spark obejmują notesy [Apache Zeppelin](https://zeppelin.apache.org/) , których można używać do uruchamiania zadań [Apache Spark](https://spark.apache.org/) . W tym artykule dowiesz się, jak używać notesu Zeppelin w klastrze usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Schemat identyfikatora URI magazynu podstawowego klastrów. Będzie to `wasb://` dla Blob Storage platformy Azure, `abfs://` dla Azure Data Lake Storage Gen2 lub `adl://` dla Azure Data Lake Storage Gen1. W przypadku włączenia bezpiecznego transferu dla Blob Storage można `wasbs://`identyfikator URI.  Aby uzyskać więcej informacji, zobacz [Wymagaj bezpiecznego transferu w usłudze Azure Storage](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Uruchamianie notesu Apache Zeppelin

1. Na stronie **Omówienie**klastra Spark wybierz pozycję **Notes Zeppelin** z **pulpitów nawigacyjnych klastra**. Wprowadź poświadczenia administratora dla klastra.  

   > [!NOTE]  
   > Możesz również uzyskać dostęp do notesu Zeppelin dla klastra, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Utwórz nowy notes. W okienku nagłówka przejdź do **notesu** > **Utwórz nową notatkę**.

    ![Tworzenie nowego notesu Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Tworzenie nowego notesu Zeppelin")

    Wprowadź nazwę notesu, a następnie wybierz pozycję **Utwórz notatkę**.

3. Upewnij się, że nagłówek notesu pokazuje połączony stan. Jest ona oznaczona zieloną kropką w prawym górnym rogu.

    ![Stan notesu Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Stan notesu Zeppelin")

4. Załaduj przykładowe dane do tabeli tymczasowej. Podczas tworzenia klastra Spark w usłudze HDInsight plik danych przykładowych `hvac.csv`jest kopiowany do skojarzonego konta magazynu w obszarze `\HdiSamples\SensorSampleData\hvac`.

    W pustym akapicie, który jest tworzony domyślnie w nowym notesie, wklej poniższy fragment kodu.

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

    Naciśnij klawisze **SHIFT + ENTER** lub wybierz przycisk **odtwarzania** dla akapitu, aby uruchomić fragment kodu. Stan w prawym górnym rogu akapitu powinien postępować od gotowy, OCZEKUJĄCy, uruchomiony na zakończony. Dane wyjściowe są wyświetlane w dolnej części tego samego akapitu. Zrzut ekranu wygląda następująco:

    ![Tworzenie tabeli tymczasowej na podstawie danych pierwotnych](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Tworzenie tabeli tymczasowej na podstawie danych pierwotnych")

    Możesz również podać tytuł w każdym akapicie. W prawym górnym rogu akapitu wybierz ikonę **ustawień** (Sprocket), a następnie wybierz pozycję **Pokaż tytuł**.  

    > [!NOTE]  
    > % spark2 interpretera nie jest obsługiwana w notesach Zeppelin we wszystkich wersjach usługi HDInsight, a interpreter% SH nie będzie obsługiwany przez usługi HDInsight 4,0.

5. W tabeli `hvac` można teraz uruchamiać instrukcje platformy Spark SQL. Wklej następujące zapytanie w nowym akapicie. Zapytanie pobiera identyfikator budynku i różnicę między docelową i rzeczywistą temperaturą dla każdego budynku w danym dniu. Naciśnij klawisze **SHIFT + ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    Instrukcja **% SQL** na początku informuje Notes o konieczności użycia interpretera Scala usługi Livy.

6. Wybierz ikonę **wykresu słupkowego** , aby zmienić wyświetlanie.  **Ustawienia**, które pojawiają się po wybraniu **wykresu słupkowego**, umożliwiają wybranie **kluczy**i **wartości**.  Poniższy zrzut ekranu przedstawia dane wyjściowe.

    ![Uruchamianie instrukcji platformy Spark języka SQL przy użyciu notebook1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Uruchamianie instrukcji platformy Spark języka SQL przy użyciu notebook1")

7. Możesz również uruchomić instrukcje Spark SQL przy użyciu zmiennych w zapytaniu. W następnym fragmencie kodu pokazano, jak zdefiniować zmienną, `Temp`w zapytaniu z możliwymi wartościami, które chcesz zbadać. Po pierwszym uruchomieniu zapytania lista rozwijana jest automatycznie wypełniana wartościami określonymi dla zmiennej.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Wklej ten fragment kodu w nowym akapicie i naciśnij klawisze **SHIFT + ENTER**. Następnie wybierz pozycję **65** z listy rozwijanej **temp** .

8. Wybierz ikonę **wykresu słupkowego** , aby zmienić wyświetlanie.  Następnie wybierz pozycję **Ustawienia** i wprowadź następujące zmiany:

   * **Grupy:**  Dodaj **targettemp**.  
   * **Wartości:** jedno. Usuń **datę**.  2. Dodaj **temp_diff**.  3.  Zmień agregator z **sum** na **śr**.  

     Poniższy zrzut ekranu przedstawia dane wyjściowe.

     ![Uruchamianie instrukcji platformy Spark języka SQL przy użyciu notebook2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Uruchamianie instrukcji platformy Spark języka SQL przy użyciu notebook2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Jak mogę używać zewnętrznych pakietów z notesem?

Notes Zeppelin można skonfigurować w klastrze Apache Spark w usłudze HDInsight, aby korzystał z zewnętrznych pakietów współtworzonych przez społeczność, które nie zostały dołączone do usługi w klastrze. Możesz wyszukać w [repozytorium Maven](https://search.maven.org/) pełną listę dostępnych pakietów. Możesz również uzyskać listę dostępnych pakietów z innych źródeł. Na przykład kompletna lista pakietów z wkładem do społeczności jest dostępna w [pakietach Spark](https://spark-packages.org/).

W tym artykule przedstawiono sposób korzystania z pakietu [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) z notesem Jupyter.

1. Otwórz ustawienia interpretera. W prawym górnym rogu wybierz nazwę zalogowanego użytkownika, a następnie wybierz **interpreter**.

    ![Uruchom interpreter](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Dane wyjściowe Hive")

2. Przewiń do **livy2**, a następnie wybierz pozycję **Edytuj**.

    ![Zmień interpreter settings1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Zmień interpreter settings1")

3. Przejdź do klucza `livy.spark.jars.packages`i ustaw jego wartość w formacie `group:id:version`. Tak więc, jeśli chcesz użyć pakietu [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) , musisz ustawić wartość klucza na `com.databricks:spark-csv_2.10:1.4.0`.

    ![Zmień interpreter settings2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Zmień interpreter settings2")

    Wybierz pozycję **Zapisz** , a następnie kliknij przycisk **OK** , aby ponownie uruchomić interpreter usługi Livy.

4. Jeśli chcesz zrozumieć, jak dotrzeć do wartości klucza wprowadzonego powyżej, Oto jak to zrobić.

    a. Znajdź pakiet w repozytorium Maven. W tym artykule użyto [platformy Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).

    b. W repozytorium Zbierz wartości dla **identyfikatora GroupID**, **ArtifactId**i **wersji**.

    ![Korzystanie z zewnętrznych pakietów z notesem Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Korzystanie z zewnętrznych pakietów z notesem Jupyter")

    c. Połącz trzy wartości rozdzielone dwukropkiem ( **:** ).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Gdzie są zapisane notesy Zeppelin?

Notesy Zeppelin są zapisywane w klastrze węzłów głównych. W związku z tym, jeśli usuniesz klaster, notesy również zostaną usunięte. Jeśli chcesz zachować swoje notesy do późniejszego użycia w innych klastrach, musisz je wyeksportować po zakończeniu wykonywania zadań. Aby wyeksportować Notes, wybierz ikonę **eksportowania** , jak pokazano na poniższej ilustracji.

![Pobierz Notes](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Pobierz Notes")

Spowoduje to zapisanie notesu jako pliku JSON w lokalizacji pobierania.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Użyj Shiro, aby skonfigurować dostęp do interpreterów Zeppelin w klastrach pakiet Enterprise Security (ESP)
Jak wspomniano powyżej, interpreter `%sh` nie jest obsługiwany przez usługi HDInsight 4,0. Ponadto, ponieważ interpreter `%sh` wprowadza potencjalne problemy z zabezpieczeniami, takie jak dostęp do kart kluczowych przy użyciu poleceń powłoki, został również usunięty z klastrów HDInsight 3,6 ESP. Oznacza to, że interpreter `%sh` nie jest dostępny podczas klikania opcji **Utwórz nową notatkę** lub w interfejsie użytkownika interpretera domyślnie. 

Użytkownicy domeny uprzywilejowanych mogą korzystać z pliku `Shiro.ini` w celu kontrolowania dostępu do interfejsu użytkownika interpretera. W ten sposób tylko Ci użytkownicy mogą tworzyć nowe interpretery `%sh` i ustawiać uprawnienia dla każdego nowego interpretera `%sh`. Aby kontrolować dostęp przy użyciu pliku `shiro.ini`, wykonaj następujące czynności:

1. Zdefiniuj nową rolę przy użyciu istniejącej nazwy grupy domen. W poniższym przykładzie `adminGroupName` jest grupą uprzywilejowanych użytkowników w usłudze AAD. Nie używaj znaków specjalnych ani białych spacji w nazwie grupy. Znaki po `=` przyznają uprawnienia dla tej roli. `*` oznacza, że grupa ma pełne uprawnienia.

    ```
    [roles]
    adminGroupName = *
    ```

2. Dodaj nową rolę, aby uzyskać dostęp do interpreterów Zeppelin. W poniższym przykładzie wszyscy użytkownicy w `adminGroupName` uzyskują dostęp do interpreterów Zeppelin i mogą tworzyć nowe interpretery. Można umieścić wiele ról między nawiasami w `roles[]`, rozdzielając je przecinkami. Następnie użytkownicy, którzy mają niezbędne uprawnienia, mogą uzyskiwać dostęp do interpreterów Zeppelin.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Zarządzanie sesją usługi Livy

Po uruchomieniu pierwszego akapitu kodu w notesie Zeppelin zostanie utworzona nowa sesja usługi Livy w klastrze usługi HDInsight Spark. Ta sesja jest udostępniana dla wszystkich utworzonych przez siebie notesów Zeppelin. Jeśli z jakiegoś powodu sesja usługi Livy zostanie przerwana (ponowne uruchomienie klastra itd.), nie będzie można uruchamiać zadań z notesu Zeppelin.

W takim przypadku przed rozpoczęciem uruchamiania zadań z notesu Zeppelin należy wykonać następujące czynności.  

1. Uruchom ponownie interpreter usługi Livy z notesu Zeppelin. Aby to zrobić, Otwórz ustawienia interpretera, wybierając nazwę zalogowanego użytkownika w prawym górnym rogu, a następnie wybierz **interpreter**.

    ![Uruchom interpreter](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Dane wyjściowe Hive")

2. Przewiń do **livy2**, a następnie wybierz pozycję **Uruchom ponownie**.

    ![Uruchom ponownie interpreter usługi Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Uruchom ponownie interpreter Zeppelin")

3. Uruchom komórkę kodu z istniejącego notesu Zeppelin. Spowoduje to utworzenie nowej sesji usługi Livy w klastrze usługi HDInsight.

## <a name="general-information"></a>Informacje ogólne

### <a name="validate-service"></a>Sprawdź poprawność usługi

Aby sprawdzić poprawność usługi z Ambari, przejdź do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` gdzie CLUSTERname jest nazwą klastra.

Aby sprawdzić poprawność usługi z wiersza polecenia, Użyj protokołu SSH do węzła głównego. Przełącz użytkownika na Zeppelin za pomocą polecenia `sudo su zeppelin`. Polecenia stanu:

|Polecenie |Opis |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Stan usługi.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Wersja usługi.|
|`ps -aux | grep zeppelin`|Identyfikowanie identyfikatora PID.|

### <a name="log-locations"></a>Lokalizacje dzienników

|NDES |Ścieżka |
|---|---|
|Zeppelin — serwer|/usr/hdp/current/zeppelin-server/|
|Dzienniki serwera|/var/log/zeppelin|
|Interpreter konfiguracji, Shiro, site. XML, Log4J|/usr/HDP/Current/Zeppelin-Server/conf lub/etc/Zeppelin/conf|
|Katalog PID|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Włączenie rejestrowania debugowania

1. Przejdź do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` gdzie CLUSTERname jest nazwą klastra.

1. Przejdź do **pliku configs** > **Advanced Zeppelin-log4j-Properties** > **log4j_properties_content**.

1. Zmodyfikuj `log4j.appender.dailyfile.Threshold = INFO`, aby `log4j.appender.dailyfile.Threshold = DEBUG`.

1. Dodaj `log4j.logger.org.apache.zeppelin.realm=DEBUG`.

1. Zapisz zmiany i ponownie uruchom usługę.

## <a name="next-steps"></a>Następne kroki

[Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z usługą BI: wykonywanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Użyj wtyczki Tools plugin for IntelliJ pomysł, aby utworzyć i przesłać Apache Spark aplikacje Scala](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki narzędzi HDInsight do IntelliJ pomysł, aby debugować aplikacje Apache Spark zdalnie](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
