---
title: Analizowanie danych w usłudze Azure Data Lake Store przy użyciu platformy Apache Spark
description: Uruchamianie zadań Spark do analizy danych przechowywanych w usłudze Azure Data Lake Store
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.openlocfilehash: 9fe4b3e49f8e3270f58929a5708a83ab02e2486c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255254"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-store"></a>Analizowanie danych w Data Lake Store przy użyciu klastra platformy HDInsight Spark

W tym samouczku używasz notesu programu Jupyter dostępne przy użyciu klastrów HDInsight Spark do uruchomienia zadania, która odczytuje dane z konta Data Lake Store.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto usługi Azure Data Lake Store. Postępuj zgodnie z instrukcjami w temacie [Rozpoczynanie pracy z usługą Azure Data Lake Store za pomocą witryny Azure Portal](../../data-lake-store/data-lake-store-get-started-portal.md).

* Usługi Azure HDInsight Spark klastra za pomocą programu Data Lake Store jako magazyn. Postępuj zgodnie z instrukcjami w artykule [Szybki Start: konfigurowanie klastrów w HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    
## <a name="prepare-the-data"></a>Przygotowywanie danych

> [!NOTE]
> Nie trzeba wykonać ten krok, jeśli utworzono klaster HDInsight Data Lake Store jako magazynem domyślnym. Proces tworzenia klastra dodaje przykładowych danych w ramach konta Data Lake Store, który należy określić podczas tworzenia klastra. Przejdź do sekcji [klastra HDInsight korzystanie z platformy Spark za pomocą programu Data Lake Store](#use-an-hdinsight-spark-cluster-with-data-lake-store).
>
>

Jeśli utworzono klaster usługi HDInsight za pomocą programu Data Lake Store jako dodatkowego miejsca do magazynowania i Azure Storage Blob jako magazynem domyślnym, należy najpierw skopiować na przykładowych danych do konta Data Lake Store. Można użyć przykładowych danych z usługi Azure Blob Storage skojarzonego z klastrem HDInsight. Możesz użyć [narzędzia ADLCopy](https://aka.ms/downloadadlcopy) Aby to zrobić. Pobierz i zainstaluj narzędzie przy użyciu linku.

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym zainstalowane jest AdlCopy, zwykle `%HOMEPATH%\Documents\adlcopy`.

2. Uruchom następujące polecenie, aby skopiować konkretny obiekt blob z kontenera źródłowego do Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Kopiuj **HVAC.csv** pliku przykładowych danych **/HdiSamples/HdiSamples/SensorSampleData/hvac/** do konta usługi Azure Data Lake Store. Fragment kodu powinien wyglądać podobnie jak:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]
   > Upewnij się, że nazwy pliku i ścieżki są w przypadku prawidłowego.
   >
   >
3. Monit o podanie poświadczeń subskrypcji platformy Azure, w którym masz konto usługi Data Lake Store. Zostaną wyświetlone dane wyjściowe podobne do następującego fragmentu kodu:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Plik danych (**HVAC.csv**) zostaną skopiowane w folderze **/hvac** w ramach konta Data Lake Store.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-store"></a>Klaster usługi HDInsight Spark za pomocą programu Data Lake Store

1. W [Portalu Azure](https://portal.azure.com/) na tablicy startowej kliknij kafelek klastra Spark (jeśli został przypięty do tablicy startowej). Możesz także przejść do klastra, wybierając polecenia **Przeglądaj wszystko** > **Klastry usługi HDInsight**.

2. W bloku klastra Spark kliknij pozycję **Szybkie linki**, a następnie w bloku **Pulpit nawigacyjny klastra** kliknij pozycję **Jupyter Notebook**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

   > [!NOTE]
   > Można również przejść do aplikacji Jupyter Notebook dla klastra, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Utwórz nowy notes. Kliknij opcję **New** (Nowy), a następnie kliknij pozycję **PySpark**.

    ![Tworzenie nowego notesu Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Tworzenie nowego notesu Jupyter")

4. Ponieważ notes został utworzony z użyciem jądra PySpark, nie ma konieczności jawnego tworzenia kontekstów. Konteksty Spark i Hive zostaną automatycznie utworzone po uruchomieniu pierwszej komórki kodu. Możesz zacząć od importowania typów wymaganych w tym scenariuszu. W tym celu wklej poniższy fragment kodu w komórce i naciśnij klawisze **SHIFT + ENTER**.

        from pyspark.sql.types import *

    Przy każdym uruchomieniu zadania w oprogramowaniu Jupyter w tytule okna przeglądarki sieci Web będzie wyświetlony stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne będzie także pełne kółko obok tekstu **PySpark** w prawym górnym rogu. Po zakończeniu zadania zmieni się ono w pusty okrąg.

     ![Stan zadania notesu Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Stan zadania notesu Jupyter")

5. Ładowanie przykładowych danych do tabeli tymczasowej, w którym używana jest **HVAC.csv** plik został skopiowany do konta Data Lake Store. Możesz uzyskać dostęp do danych w ramach konta Data Lake Store przy użyciu następującego wzorca adresu URL.

    * W przypadku Data Lake Store jako magazynem domyślnym HVAC.csv będą się w ścieżce podobny do następującego adresu URL:

            adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

        Alternatywnie można także użyć skróconą format, takie jak następujące:

            adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

    * Jeśli masz Data Lake Store jako magazynu dodatkowego, HVAC.csv będzie w lokalizacji, w której został skopiowany, takich jak:

            adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     W pustej komórce Wklej poniższy kod, Zastąp **MYDATALAKESTORE** za pomocą usługi Data Lake Store, nazwa konta, a następnie naciśnij klawisz **SHIFT + ENTER**. Ten przykład kodu rejestruje dane w tabeli tymczasowej o nazwie **hvac**.

            # Load the data. The path below assumes Data Lake Store is default storage for the Spark cluster
            hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            # Create the schema
            hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

            # Parse the data in hvacText
            hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

            # Create a data frame
            hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

            # Register the data fram as a table to run queries against
            hvacdf.registerTempTable("hvac")

6. Ponieważ używane jest jądro PySpark, można teraz bezpośrednio uruchomić zapytanie SQL w tabeli tymczasowej **hvac** utworzonej przed chwilą za pomocą polecenia magicznego `%%sql`. Aby uzyskać więcej informacji na temat polecenia magicznego `%%sql` oraz innych poleceń magicznych dostępnych za pośrednictwem jądra PySpark, zobacz [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Po pomyślnym ukończeniu zadania domyślnie wyświetlane są następujące tabelaryczne dane wyjściowe.

      ![Wyniki zapytania w tabeli danych wyjściowych](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Wyniki zapytania w tabeli danych wyjściowych")

     Wyniki można również przeglądać w postaci innych wizualizacji. Na przykład wykres warstwowy tych samych danych wyjściowych będzie wyglądać w następujący sposób.

     ![Wykres warstwowy wyników zapytania](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "Wykres warstwowy wyników zapytania")

8. Po zakończeniu działania aplikacji należy ją zamknąć, aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie kliknij polecenie **Close and Halt** (Zamknij i zatrzymaj). Spowoduje to zakończenie pracy i zamknięcie notesu.


## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie autonomicznego Scala aplikacji do uruchamiania w klastrze Apache Spark](apache-spark-create-standalone-application.md)
* [Narzędzia HDInsight w usłudze Azure Toolkit for IntelliJ do tworzenie aplikacji Spark dla klastra HDInsight Spark systemu Linux](apache-spark-intellij-tool-plugin.md)
* [Narzędzia HDInsight w zestaw narzędzi platformy Azure dla środowiska Eclipse do tworzenie aplikacji Spark dla klastra HDInsight Spark systemu Linux](apache-spark-eclipse-tool-plugin.md)
