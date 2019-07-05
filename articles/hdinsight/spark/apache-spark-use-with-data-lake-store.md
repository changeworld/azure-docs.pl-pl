---
title: Analizowanie danych w usłudze Azure Data Lake magazynu Gen1 przy użyciu platformy Apache Spark
description: Uruchamianie zadań Spark do analizy danych przechowywanych w usłudze Azure Data Lake magazynu Gen1
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 5a98cb2356c25329e091514dd1d6181dfc2690be
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448653"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Umożliwia analizowanie danych w Data Lake Storage Gen1 klastra platformy HDInsight Spark

W tym artykule używasz [notesu programu Jupyter](https://jupyter.org/) dostępne przy użyciu klastrów HDInsight Spark do uruchomienia zadania, która odczytuje dane z konta usługi Data Lake Storage.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto usługi Azure Data Lake Storage Gen1. Postępuj zgodnie z instrukcjami w artykule [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal](../../data-lake-store/data-lake-store-get-started-portal.md).

* Usługi Azure HDInsight Spark klastra za pomocą programu Data Lake Storage Gen1 jako magazyn. Postępuj zgodnie z instrukcjami w artykule [Szybki start: Konfigurowanie klastrów w usłudze HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    
## <a name="prepare-the-data"></a>Przygotowywanie danych

> [!NOTE]  
> Nie trzeba wykonać ten krok, jeśli utworzono klaster HDInsight przy użyciu usługi Data Lake Storage jako magazynu domyślnego. Proces tworzenia klastra dodaje przykładowe dane na koncie usługi Data Lake Storage, które należy określić podczas tworzenia klastra. Przejdź do sekcji klastra korzystanie z platformy HDInsight Spark przy użyciu usługi Data Lake Storage.

Jeśli utworzono klaster usługi HDInsight przy użyciu usługi Data Lake Storage jako dodatkowego miejsca do magazynowania i Azure Storage Blob jako magazynem domyślnym, należy najpierw skopiować na przykładowych danych do konta usługi Data Lake Storage. Można użyć przykładowych danych z usługi Azure Blob Storage skojarzonego z klastrem HDInsight. Możesz użyć [narzędzia ADLCopy](https://aka.ms/downloadadlcopy) Aby to zrobić. Pobierz i zainstaluj narzędzie przy użyciu linku.

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym zainstalowane jest AdlCopy, zwykle `%HOMEPATH%\Documents\adlcopy`.

2. Uruchom następujące polecenie, aby skopiować konkretny obiekt blob z kontenera źródłowego do usługi Data Lake Storage:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Kopiuj **HVAC.csv** pliku przykładowych danych **/HdiSamples/HdiSamples/SensorSampleData/hvac/** do konta usługi Azure Data Lake Storage. Fragment kodu powinien wyglądać podobnie jak:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Upewnij się, że nazwy pliku i ścieżki stosowanie odpowiednich liter.

3. Monit o podanie poświadczeń subskrypcji platformy Azure, w którym masz konta usługi Data Lake Storage. Zostaną wyświetlone dane wyjściowe podobne do następującego fragmentu kodu:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Plik danych (**HVAC.csv**) zostaną skopiowane w folderze **/hvac** na koncie usługi Data Lake Storage.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Klaster usługi HDInsight Spark za pomocą programu Data Lake Storage Gen1

1. Z [witryny Azure Portal](https://portal.azure.com/), na tablicy startowej kliknij Kafelek klastra Apache Spark (jeśli został przypięty do tablicy startowej). Możesz także przejść do klastra, wybierając polecenia **Przeglądaj wszystko** > **Klastry usługi HDInsight**.

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

5. Ładowanie przykładowych danych do tabeli tymczasowej, w którym używana jest **HVAC.csv** plik został skopiowany do konta Data Lake Storage Gen1. Możesz uzyskać dostęp do danych w ramach konta usługi Data Lake Storage przy użyciu następującego wzorca adresu URL.

   * W przypadku Data Lake Storage Gen1 jako magazynem domyślnym HVAC.csv będą się w ścieżce podobny do następującego adresu URL:

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       Alternatywnie można także użyć skróconą format, takie jak następujące:

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * W przypadku usługi Data Lake Storage jako magazynu dodatkowego HVAC.csv będzie w lokalizacji, w której został skopiowany, takich jak:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     W pustej komórce Wklej poniższy kod, Zastąp **MYDATALAKESTORE** przy użyciu swojej nazwy konta usługi Data Lake Storage, a następnie naciśnij klawisz **SHIFT + ENTER**. Ten przykład kodu rejestruje dane w tabeli tymczasowej o nazwie **hvac**.

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

6. Ponieważ używane jest jądro PySpark, można teraz bezpośrednio uruchomić zapytanie SQL w tabeli tymczasowej **hvac** utworzonej przed chwilą za pomocą polecenia magicznego `%%sql`. Aby uzyskać więcej informacji na temat `%%sql` magic, a także innych poleceń magicznych dostępnych za pośrednictwem jądra PySpark, zobacz [jądra dostępne dla notesu Jupyter w klastrze z klastrami Apache Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Po pomyślnym ukończeniu zadania domyślnie wyświetlane są następujące tabelaryczne dane wyjściowe.

      ![Wyniki zapytania w tabeli danych wyjściowych](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Wyniki zapytania w tabeli danych wyjściowych")

     Wyniki można również przeglądać w postaci innych wizualizacji. Na przykład wykres warstwowy tych samych danych wyjściowych będzie wyglądać w następujący sposób.

     ![Wykres warstwowy wyników zapytania](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "Wykres warstwowy wyników zapytania")

8. Po zakończeniu działania aplikacji należy ją zamknąć, aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie kliknij polecenie **Close and Halt** (Zamknij i zatrzymaj). Spowoduje to zakończenie pracy i zamknięcie notesu.


## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie autonomicznego Scala aplikacji do uruchamiania w klastrze Apache Spark](apache-spark-create-standalone-application.md)
* [Narzędzia HDInsight w usłudze Azure Toolkit for IntelliJ do tworzenia aplikacji platformy Apache Spark dla klastra HDInsight Spark systemu Linux](apache-spark-intellij-tool-plugin.md)
* [Korzystaj z narzędzi HDInsight w zestaw narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji platformy Apache Spark dla klastra HDInsight Spark systemu Linux](apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
