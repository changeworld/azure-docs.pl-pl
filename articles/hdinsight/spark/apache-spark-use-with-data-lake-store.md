---
title: Analizowanie usługi Azure Data Lake Storage Gen1 za pomocą platformy HDInsight Apache Spark
description: Uruchamianie zadań Apache Spark w celu analizowania danych przechowywanych w usłudze Azure Data Lake Storage Gen1
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f7a6ab954aff1bcc2e3dae3fc035db4b136ccbbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818164"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Używanie klastra platformy SPARK usługi HDInsight do analizowania danych w umiań magazynu usługi Data Lake1

W tym artykule używasz [Notebooka Jupyter](https://jupyter.org/) dostępnego z klastrami PLATFORMY SPARK usługi HDInsight do uruchamiania zadania odczytywalnego z konta usługi Data Lake Storage.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto usługi Azure Data Lake Storage Gen1. Postępuj zgodnie z instrukcjami w [usłudze Wprowadzenie do usługi Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal.](../../data-lake-store/data-lake-store-get-started-portal.md)

* Klaster platformy Azure HDInsight Spark z magazynem Data Lake Storage Gen1 jako magazynem. Postępuj zgodnie z instrukcjami dotyczącymi [przewodnika Szybki start: Konfigurowanie klastrów w ujrzyszy.](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)

## <a name="prepare-the-data"></a>Przygotowywanie danych

> [!NOTE]  
> Ten krok nie jest potrzebny, jeśli został utworzony klaster HDInsight z magazynem usługi Data Lake jako magazyn domyślny. Proces tworzenia klastra dodaje niektóre przykładowe dane na koncie magazynu usługi Data Lake, które określisz podczas tworzenia klastra. Przejdź do sekcji Użyj klastra platformy Spark usługi HDInsight z magazynem usługi Data Lake.

Jeśli utworzono klaster HDInsight z magazynem usługi Data Lake jako dodatkowym magazynem i obiektem Blob usługi Azure Storage jako magazyn domyślny, należy najpierw skopiować niektóre przykładowe dane do konta magazynu usługi Data Lake. Można użyć przykładowych danych z obiektu blob usługi Azure Storage skojarzonego z klastrem HDInsight. W tym celu można użyć [narzędzia ADLCopy.](https://www.microsoft.com/download/details.aspx?id=50358) Pobierz i zainstaluj narzędzie z linku.

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym zazwyczaj `%HOMEPATH%\Documents\adlcopy`jest zainstalowany program AdlCopy.

2. Uruchom następujące polecenie, aby skopiować określony obiekt blob z kontenera źródłowego do magazynu usługi Data Lake:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Skopiuj przykładowy plik danych **HVAC.csv** na koncie usługi Azure Data Lake Storage na koncie usługi Azure Data Lake Storage.Copy the HVAC.csv sample data file at **/HdiSamples/HdiSamples/SensorSampleData/hvac/** to the Azure Data Lake Storage account. Fragment kodu powinien wyglądać następująco:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Upewnij się, że nazwy plików i ścieżek używają odpowiedniej litery.

3. Zostanie wyświetlony monit o wprowadzenie poświadczeń dla subskrypcji platformy Azure, w ramach której masz konto usługi Data Lake Storage. Zostaną wyświetlone dane wyjściowe podobne do następującego fragmentu kodu:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Plik danych **(HVAC.csv)** zostanie skopiowany pod **folderem /hvac** na koncie Data Lake Storage.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Używanie klastra platformy SPARK usługi HDInsight z pamięcią data lake storage gen1

1. W [witrynie Azure portal](https://portal.azure.com/)— z tablicy startowej kliknij kafelek klastra Platformy Spark Apache (jeśli przypiąłeś go do tablicy startowej). Można również przejść do klastra w obszarze **Przeglądaj wszystkie** > **klastry HDInsight**.

2. W bloku klastra Spark kliknij pozycję **Szybkie linki**, a następnie w bloku **Pulpit nawigacyjny klastra** kliknij pozycję **Jupyter Notebook**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

   > [!NOTE]  
   > Można również przejść do aplikacji Jupyter Notebook dla klastra, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Utwórz nowy notes. Kliknij opcję **New** (Nowy), a następnie kliknij pozycję **PySpark**.

    ![Tworzenie nowego notesu Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Tworzenie nowego notesu Jupyter")

4. Ponieważ notes został utworzony z użyciem jądra PySpark, nie ma konieczności jawnego tworzenia kontekstów. Konteksty Spark i Hive zostaną automatycznie utworzone po uruchomieniu pierwszej komórki kodu. Możesz zacząć od importowania typów wymaganych w tym scenariuszu. W tym celu wklej poniższy fragment kodu w komórce i naciśnij klawisze **SHIFT + ENTER**.

        from pyspark.sql.types import *

    Przy każdym uruchomieniu zadania w oprogramowaniu Jupyter w tytule okna przeglądarki sieci Web będzie wyświetlony stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne będzie także pełne kółko obok tekstu **PySpark** w prawym górnym rogu. Po zakończeniu zadania zmieni się ono w pusty okrąg.

     ![Stan zadania notesu Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Stan zadania notesu Jupyter")

5. Załaduj przykładowe dane do tabeli tymczasowej przy użyciu pliku **HVAC.csv** skopiowanego do konta Data Lake Storage Gen1. Dostęp do danych można uzyskać na koncie Usługi Data Lake Storage przy użyciu następującego wzorca adresu URL.

   * Jeśli jako domyślny magazyn danych Lake Storage Gen1 jest domyślny, plik HVAC.csv będzie miał ścieżkę podobną do następującego adresu URL:

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       Można też użyć skróconego formatu, takiego jak:

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Jeśli masz magazyn danych Lake jako dodatkowy magazyn, plik HVAC.csv będzie znajdować się w miejscu, w którym został skopiowany, na przykład:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     W pustej komórce wklej poniższy przykład kodu, zastąp **MYDATALAKESTORE** nazwą konta Data Lake Storage i naciśnij **klawisze SHIFT + ENTER**. Ten przykład kodu rejestruje dane w tabeli tymczasowej o nazwie **hvac**.

           # Load the data. The path below assumes Data Lake Storage is default storage for the Spark cluster
           hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

           # Create the schema
           hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

           # Parse the data in hvacText
           hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

           # Create a data frame
           hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

           # Register the data fram as a table to run queries against
           hvacdf.registerTempTable("hvac")

6. Ponieważ używane jest jądro PySpark, można teraz bezpośrednio uruchomić zapytanie SQL w tabeli tymczasowej **hvac** utworzonej przed chwilą za pomocą polecenia magicznego `%%sql`. Aby uzyskać więcej `%%sql` informacji na temat magii, a także innych magii dostępnych w jądrze PySpark, zobacz [Jądra dostępne w notebookach Jupyter z klastrami Apache Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Po pomyślnym ukończeniu zadania domyślnie wyświetlane są następujące tabelaryczne dane wyjściowe.

      ![Wyniki zapytania w tabeli danych wyjściowych](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Wyniki zapytania w tabeli danych wyjściowych")

     Wyniki można również przeglądać w postaci innych wizualizacji. Na przykład wykres warstwowy tych samych danych wyjściowych będzie wyglądać w następujący sposób.

     ![Wykres warstwowy wyników zapytania](./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png "Wykres warstwowy wyników zapytania")

8. Po zakończeniu działania aplikacji należy ją zamknąć, aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie kliknij polecenie **Close and Halt** (Zamknij i zatrzymaj). Spowoduje to zakończenie pracy i zamknięcie notesu.


## <a name="next-steps"></a>Następne kroki

* [Tworzenie autonomicznej aplikacji Scala do uruchamiania w klastrze Apache Spark](apache-spark-create-standalone-application.md)
* [Użyj narzędzia HDInsight Tools w usłudze Azure Toolkit for IntelliJ do tworzenia aplikacji Apache Spark dla klastra HDInsight Spark Linux](apache-spark-intellij-tool-plugin.md)
* [Użyj narzędzi HDInsight w usłudze Azure Toolkit for Eclipse, aby utworzyć aplikacje Apache Spark dla klastra systemu HDInsight Spark Linux](apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
