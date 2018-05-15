---
title: 'Samouczek: ładowanie danych i uruchamianie zapytań w klastrze platformy Apache Spark w usłudze Azure HDInsight| Microsoft Docs'
description: Dowiedz się, jak ładować dane i uruchamiać interakcyjne zapytania w klastrach Spark w usłudze Azure HDInsight.
services: azure-hdinsight
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.author: jgao
ms.date: 05/07/2018
ms.openlocfilehash: 63a876dc148129cd2a3eb93ed7ab6baf06a07c62
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Samouczek: ładowanie danych i uruchamianie zapytań w klastrze platformy Apache Spark w usłudze Azure HDInsight

W tym samouczku dowiesz się, jak utworzyć ramkę danych na podstawie pliku csv i uruchamiać interakcyjne zapytania Spark SQL względem klastra Apache Spark w usłudze Azure HDInsight. Na platformie Spark ramka danych jest rozproszoną kolekcją danych zorganizowanych w nazwanych kolumnach. Jest równoważna tabeli w relacyjnej bazie danych lub ramce danych w języku R/Python.
 
Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie ramki danych z pliku csv
> * Uruchamianie zapytań na ramce danych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* Zapoznanie się z przewodnikiem [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-dataframe-from-a-csv-file"></a>Tworzenie ramki danych z pliku csv

Aplikacje mogą tworzyć ramki danych za pomocą obiektu SQLContext na podstawie źródeł danych lub tabeli programu Hive albo istniejącego, odpornego na błędy, dystrybuowanego zestawu danych (RDD, Resilient Distributed Dataset). Poniższy zrzut ekranu przedstawia migawkę pliku HVAC.csv używanego w tym samouczku. Plik csv jest zainstalowany na wszystkich klastrach HDInsight Spark. Dane dotyczą zmian temperatury w niektórych budynkach.
    
![Migawka danych używanych w interakcyjnym zapytaniu Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Migawka danych używanych w interakcyjnym zapytaniu Spark SQL")


1. Otwórz notes Jupyter, utworzony w sekcji wymagań wstępnych.
2. W notesie wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT+ENTER**, aby go uruchomić. Kod importuje typy wymagane w tym scenariuszu:

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Podczas uruchamiania interakcyjnego zapytania w programie Jupyter w oknie przeglądarki internetowej lub w tytule karty wyświetlany jest stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne jest także pełne kółko obok tekstu **PySpark** w prawym górnym rogu. Po zakończeniu zadania zmienia się ono w pusty okrąg.

    ![Stan interakcyjnego zapytania Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Stan interakcyjnego zapytania Spark SQL")

3. Uruchom następujący kod, aby utworzyć ramkę danych i tabelę tymczasową (**hvac**). Ten kod nie powoduje wyodrębnienia wszystkich kolumn dostępnych w pliku CSV. 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for the data
    Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
    
    # Parse the data and create a schema
    hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
    hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
    
    # Infer the schema and create a table       
    hvacTable = sqlContext.createDataFrame(hvac)
    hvacTable.registerTempTable('hvactemptable')
    dfw = DataFrameWriter(hvacTable)
    dfw.saveAsTable('hvac')
    ```

    > [!NOTE]
    > Dzięki temu, że tworzenie notesu odbywa się przy użyciu jądra PySpark, po uruchomieniu pierwszej komórki kodu konteksty SQL są tworzone automatycznie. Nie musisz jawnie tworzyć kontekstów.


## <a name="run-queries-on-the-dataframe"></a>Uruchamianie zapytań na ramce danych

Po utworzeniu tabeli możesz uruchomić interakcyjne zapytanie na danych.

1. W pustej komórce notesu uruchom następujący kod:

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Ponieważ w notesie używane jest jądro PySpark, można teraz bezpośrednio uruchomić interakcyjne zapytanie SQL w tabeli tymczasowej **hvac**.

   Zostanie wyświetlona następująca tabela danych wyjściowych.

     ![Tabela wyjściowa wyników interakcyjnego zapytania Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabela wyjściowa wyników interakcyjnego zapytania Spark")

3. Wyniki można również przeglądać w postaci innych wizualizacji. Aby wyświetlić wykres warstwowy dla tych samych danych wyjściowych, wybierz pozycję **Area** (Obszar), a następnie ustaw inne wartości, jak pokazano poniżej.

    ![Wykres warstwowy wyników interakcyjnego zapytania Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Wykres warstwowy wyników interakcyjnego zapytania Spark")

10. W menu **File** (Plik) w notesie wybierz pozycję **Save and Checkpoint** (Zapisz i ustaw punkt kontrolny). 

11. Jeśli zamierzasz teraz otworzyć [następny samouczek](apache-spark-use-bi-tools.md), pozostaw notes otwarty. W przeciwnym razie zamknij notes, aby zwolnić zasoby klastra: w menu **File** (Plik) wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usługa HDInsight zapisuje Twoje dane w usłudze Azure Storage lub Azure Data Lake Store, więc możesz bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane. Jeśli planujesz natychmiastowe rozpoczęcie pracy z następnym samouczkiem, warto zachować klaster.

Otwórz klaster w witrynie Azure Portal, a następnie wybierz pozycję **Usuń**.

![Usuwanie klastra usługi HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usunięcie grupy zasobów powoduje usunięcie zarówno klastra Spark w usłudze HDInsight, jak i domyślnego konta magazynu.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie ramki danych Spark.
* Uruchamianie zapytania Spark SQL względem ramki danych.

Przejdź do następnego artykułu, aby dowiedzieć się, w jaki sposób można ściągnąć dane zarejestrowane na platformie Spark do narzędzia analizy biznesowej, takiego jak usługa Power BI. 
> [!div class="nextstepaction"]
> [Analyze data using BI tools (Analizowanie danych przy użyciu narzędzi do analizy biznesowej)](apache-spark-use-bi-tools.md)

