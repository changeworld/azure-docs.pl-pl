---
title: Korzystanie z notesów Zeppelin z klastra Apache Spark w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Instrukcje krok po kroku dotyczące sposobu używania notesów Zeppelin z klastrami Apache Spark w usłudze Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: df489d70-7788-4efa-a089-e5e5006421e2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: b2f47dce058af7a39366c06d0b33117a66ed116a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31522008"
---
# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Korzystanie z notesów Zeppelin z klastra Apache Spark w usłudze Azure HDInsight

Klastry HDInsight Spark obejmują notesów Zeppelin, których można użyć do uruchomienia zadań Spark. W tym artykule Dowiedz się jak używać notesu Zeppelin w klastrze usługi HDInsight.

> [!NOTE]
> Notesów Zeppelin są dostępne tylko w przypadku 1.6.3 platformy Spark w usłudze HDInsight 3.5 i Spark 2.1.0 na 3,6 HDInsight.
>

**Wymagania wstępne:**

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [klastrów utworzyć Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Uruchamianie notesu Zeppelin
1. W bloku klastra Spark kliknij **pulpit nawigacyjny klastra**, a następnie kliknij przycisk **notesu Zeppelin**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.
   
   > [!NOTE]
   > Można również przejść Zeppelin Notebook dla klastra, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`
   > 
   > 
2. Utwórz nowy notes. Okienko nagłówka kliknij **notesu**, a następnie kliknij przycisk **Tworzenie nowej notatki**.
   
    ![Tworzenie nowego notesu Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Tworzenie nowego notesu Zeppelin")
   
    Wprowadź nazwę notesu, a następnie kliknij przycisk **utworzyć Uwaga**.
3. Ponadto upewnij się, że nagłówek notesu wyświetlany jest stan połączenia. Jest oznaczany zielonym kropką w prawym górnym rogu.
   
    ![Stan notesu Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin notesu stanu")
4. Załaduj przykładowe dane do tabeli tymczasowej. Podczas tworzenia klastra Spark w usłudze HDInsight przykładowy plik danych **hvac.csv**, jest kopiowany do skojarzonego konta magazynu w obszarze **\HdiSamples\SensorSampleData\hvac**.
   
    W pusty akapit, który jest tworzony nowy notes domyślnie Wklej poniższy fragment kodu.
   
        %livy.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter
   
        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
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
   
    Naciśnij klawisz **SHIFT + ENTER** lub kliknij przycisk **odtwarzanie** przycisk akapitu uruchomić fragment kodu. Stan w prawym dolnym rogu akapitu powinien postępu z gotowe do czasu działania na ZAKOŃCZONE. Dane wyjściowe zostaną wyświetlone u dołu tej samej akapitu. Zrzut ekranu wygląda następująco:
   
    ![Tworzenie tabeli tymczasowej od danych pierwotnych](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Tworzenie tabeli tymczasowej od danych pierwotnych")
   
    Można też podać tytuł do każdego akapitu. W prawym górnym rogu, kliknij przycisk **ustawienia** ikonę, a następnie kliknij przycisk **Pokaż tytuł**.
5. Teraz możesz uruchomić instrukcje Spark SQL **hvac** tabeli. Wklej poniższe zapytanie nowy akapit. Zapytanie pobiera identyfikator budynku i różnica między docelowy i rzeczywistego temperatury dla każdego opierając się na dany dzień. Naciśnij klawisz **SHIFT + ENTER**.
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    **% Sql** instrukcji na początku informuje, aby użyć interpreter języka Scala Livy.
   
    Poniższy zrzut ekranu przedstawia dane wyjściowe.
   
    ![Uruchom instrukcję Spark SQL za pomocą notesu](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "zestawienia Spark SQL za pomocą notesu")
   
     Kliknij opcje wyświetlania (wyróżnione prostokąt), aby przełączyć między różne reprezentacje tych samych danych wyjściowych. Kliknij przycisk **ustawienia** wybierz jakie consitutes klucza i wartości w danych wyjściowych. Przechwytywanie ekranu powyżej używa **buildingID** jako klucz i średnią **temp_diff** jako wartość.
6. Można również uruchomić Spark SQL instrukcje używania zmiennych w zapytaniu. Dalej fragment kodu przedstawia sposób zdefiniowania zmiennej, **Temp**, kwerendy za pomocą możliwe wartości chcesz zbadać za pomocą. Przy pierwszym uruchomieniu zapytania, menu rozwijane jest automatycznie wypełniane przy użyciu wartości określone dla zmiennej.
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    Wklej następujący fragment kodu w nowy akapit i naciśnij klawisz **SHIFT + ENTER**. Poniższy zrzut ekranu przedstawia dane wyjściowe.
   
    ![Uruchom instrukcję Spark SQL za pomocą notesu](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "zestawienia Spark SQL za pomocą notesu")
   
    Następnych kwerend można wybrać nową wartość z listy rozwijanej i ponownie uruchom zapytanie. Kliknij przycisk **ustawienia** wybierz jakie consitutes klucza i wartości w danych wyjściowych. Przechwytywanie ekranu powyżej używa **buildingID** jako klucz średnią **temp_diff** jako wartość, i **targettemp** jako grupa.
7. Uruchom ponownie interpreter Livy, aby zakończyć działanie aplikacji. Aby to zrobić, Otwórz okno Ustawienia interpreter klikając zalogowanego w nazwie użytkownika w prawym górnym rogu, a następnie kliknij przycisk **Interpreter**.
   
    ![Uruchamianie interpreter](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive danych wyjściowych")
8. Przewiń do Livy interpreter ustawienia, a następnie kliknij przycisk **ponownego uruchomienia**.
   
    ![Uruchom ponownie Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Uruchom ponownie Zeppelin intepreter")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Jak korzystanie z zewnętrznych pakietów z notesu?
Notesu Zeppelin w klastrze Apache Spark w usłudze HDInsight (Linux) można skonfigurować na korzystanie z pakietów zewnętrznych, przyczyniły się społeczności, które nie są uwzględniane out-of--box w klastrze. Możesz przeszukać [repozytorium Maven](http://search.maven.org/) Aby uzyskać pełną listę pakietów, które są dostępne. Można również uzyskać listę dostępnych pakietów z innych źródeł. Na przykład pełną listę społeczności przyczyniły się do pakietów znajduje się w temacie [pakietów Spark](http://spark-packages.org/).

W tym artykule, wyświetlona zostanie sposób użycia [udostępnionego woluminu klastra spark](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pakietu z notesem Jupyter.

1. Otwórz ustawienia interpreter. W prawym górnym rogu kliknij zalogowanego w nazwie użytkownika, a następnie kliknij **Interpreter**.
   
    ![Uruchamianie interpreter](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive danych wyjściowych")
2. Przewiń do Livy interpreter ustawienia, a następnie kliknij przycisk **Edytuj**.
   
    ![Zmień ustawienia interpreter](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "zmiany ustawień interpretera")
3. Dodaj nowy klucz o nazwie **livy.spark.jars.packages** i ustaw jej wartość w formacie `group:id:version`. Tak więc, jeśli chcesz użyć [udostępnionego woluminu klastra spark](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pakietu, należy ustawić wartość klucza `com.databricks:spark-csv_2.10:1.4.0`.
   
    ![Zmień ustawienia interpreter](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "zmiany ustawień interpretera")
   
    Kliknij przycisk **zapisać** i uruchom ponownie Livy interpreter.
4. **Porada**: Jeśli chcesz zrozumieć sposób został odebrany w wartość klucza wprowadzony powyżej, poniżej przedstawiono sposób.
   
    a. Zlokalizuj pakiet w repozytorium Maven. W tym samouczku użyliśmy [udostępnionego woluminu klastra spark](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Z repozytorium, Zbierz wartości **GroupId**, **ArtifactId**, i **wersji**.
   
    ![Pakiety zewnętrzne za pomocą notesu Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "pakiety zewnętrzne za pomocą notesu Jupyter")
   
    c. Łączenie trzech oddzieloną dwukropkiem (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Gdzie są zapisywane notesów Zeppelin?
Notesów Zeppelin są zapisywane headnodes klastra. Aby usunąć klaster, jak również zostaną usunięte notebooki. Jeśli chcesz zachować notesy do późniejszego użytku w innych klastrach, należy je wyeksportować po zakończeniu wykonywania zadań. Aby wyeksportować Notes, kliknij przycisk **wyeksportować** ikony, jak pokazano na poniższej ilustracji.

![Pobierz notesu](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Pobierz notesu")

Spowoduje to zapisanie notesu jako plik JSON w lokalizacji pobierania.

## <a name="livy-session-management"></a>Zarządzanie sesjami programu Livy
Po uruchomieniu pierwszym akapicie kodu w notesie Zeppelin w nowej sesji programu Livy jest tworzony w klastrze Spark w usłudze HDInsight. Ta sesja jest współużytkowana przez wszystkie notesów Zeppelin, które następnie utworzysz. Jeśli z jakiegoś powodu Livy sesja jest skasowane (ponowne uruchomienie klastra, itp.), nie będzie można uruchamiać zadania z Zeppelin notesu.

Przed rozpoczęciem wykonywania zadań z notesu Zeppelin w takim przypadku należy wykonać następujące kroki. 

1. Uruchom ponownie interpreter Livy z Zeppelin notesu. Aby to zrobić, Otwórz okno Ustawienia interpreter klikając zalogowanego w nazwie użytkownika w prawym górnym rogu, a następnie kliknij przycisk **Interpreter**.
   
    ![Uruchamianie interpreter](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive danych wyjściowych")
2. Przewiń do Livy interpreter ustawienia, a następnie kliknij przycisk **ponownego uruchomienia**.
   
    ![Uruchom ponownie Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Uruchom ponownie Zeppelin intepreter")
3. Uruchom komórki kodu z istniejącego notesu Zeppelin. Spowoduje to utworzenie nowej sesji programu Livy klastra usługi HDInsight.

## <a name="seealso"></a>Zobacz też
* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Spark i analiza biznesowa: interakcyjna analiza danych na platformie Spark w usłudze HDInsight z użyciem narzędzi do analizy biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 







