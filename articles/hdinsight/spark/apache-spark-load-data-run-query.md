---
title: 'Samouczek: ładowanie danych i uruchamianie zapytań w klastrze platformy Apache Spark w usłudze Azure HDInsight '
description: Dowiedz się, jak ładować dane i uruchamiać interakcyjne zapytania w klastrach Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.author: hrasheed
ms.date: 05/16/2019
ms.openlocfilehash: 09509b32320fb10b8ab3d563442b6d0fb44ad34e
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65909201"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Samouczek: ładowanie danych i uruchamianie zapytań w klastrze platformy Apache Spark w usłudze Azure HDInsight

W tym samouczku dowiesz się, jak utworzyć ramkę danych na podstawie pliku CSV i uruchamiać interakcyjne zapytania Spark SQL względem klastra [Apache Spark](https://spark.apache.org/) w usłudze Azure HDInsight. Na platformie Spark ramka danych jest rozproszoną kolekcją danych zorganizowanych w nazwanych kolumnach. Jest równoważna tabeli w relacyjnej bazie danych lub ramce danych w języku R/Python.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie ramki danych z pliku csv
> * Uruchamianie zapytań na ramce danych

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Spark w usłudze HDInsight. Zobacz [utworzyć klaster platformy Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

Jupyter Notebook to interakcyjne środowisko notesu, które obsługuje różne języki programowania. Notes pozwala na interakcję z danymi, łączenie kodu z tekstem markdown i wykonywanie prostych wizualizacji. 

1. Edytuj adres URL `https://SPARKCLUSTER.azurehdinsight.net/jupyter` , zastępując `SPARKCLUSTER` nazwą klastra Spark. Następnie wprowadź edytowanych adresu URL w przeglądarce sieci web. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla klastra.

2. Na stronie sieci web programu Jupyter, wybierz **New** > **PySpark** do utworzenia notesu. 

   ![Tworzenie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Tworzenie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL")

   Nowy notes zostanie utworzony i otwarty o nazwie Untitled (`Untitled.ipynb`).

    > [!NOTE]  
    > Dzięki temu, że tworzenie notesu odbywa się przy użyciu jądra PySpark, po uruchomieniu pierwszej komórki kodu sesja `spark` jest tworzona automatycznie. Nie jest konieczne jawne tworzenie sesji.

## <a name="create-a-dataframe-from-a-csv-file"></a>Tworzenie ramki danych z pliku csv

Aplikacje mogą tworzyć ramki danych bezpośrednio na podstawie plików lub folderów znajdujących się w magazynie zdalnym, takich jak magazyn Azure Storage lub Azure Data Lake Storage, na podstawie tabeli programu Hive lub na podstawie innych źródeł danych obsługiwanych przez platformę Spark, takich jak bazy danych Cosmos DB i Azure SQL, magazyn DW itp. Poniższy zrzut ekranu przedstawia migawkę pliku HVAC.csv używanego w tym samouczku. Plik csv jest zainstalowany na wszystkich klastrach HDInsight Spark. Dane dotyczą zmian temperatury w niektórych budynkach.
    
![Migawka danych używanych w interakcyjnym zapytaniu Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Migawka danych używanych w interakcyjnym zapytaniu Spark SQL")

1. Wklej następujący kod do pustej komórki notesu programu Jupyter, a następnie naciśnij klawisz **SHIFT + ENTER** do uruchomienia kodu. Kod importuje typy wymagane w tym scenariuszu:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Podczas uruchamiania interakcyjnego zapytania w programie Jupyter w oknie przeglądarki internetowej lub w tytule karty wyświetlany jest stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne jest także pełne kółko obok tekstu **PySpark** w prawym górnym rogu. Po zakończeniu zadania zmienia się ono w pusty okrąg.

    ![Stan interakcyjnego zapytania Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Stan interakcyjnego zapytania Spark SQL")

2. Uruchom następujący kod, aby utworzyć ramkę danych i tabelę tymczasową (**hvac**). 

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>Uruchamianie zapytań na ramce danych

Po utworzeniu tabeli możesz uruchomić interakcyjne zapytanie na danych.

1. W pustej komórce notesu uruchom następujący kod:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Zostanie wyświetlona następująca tabela danych wyjściowych.

     ![Tabela wyjściowa wyników interakcyjnego zapytania Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabela wyjściowa wyników interakcyjnego zapytania Spark")

2. Wyniki można również przeglądać w postaci innych wizualizacji. Aby wyświetlić wykres warstwowy dla tych samych danych wyjściowych, wybierz pozycję **Area** (Obszar), a następnie ustaw inne wartości, jak pokazano poniżej.

    ![Wykres warstwowy wyników interakcyjnego zapytania Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Wykres warstwowy wyników interakcyjnego zapytania Spark")

3. Na pasku menu Notes, przejdź do **pliku** > **zapisywanie i punktu kontrolnego**.

4. Jeśli zamierzasz teraz otworzyć [następny samouczek](apache-spark-use-bi-tools.md), pozostaw notes otwarty. Jeśli nie, zamknij notes w celu zwolnienia zasobów klastra: na pasku menu Notes, przejdź do **pliku** >  **Zamknij i zatrzymanie**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Dzięki użyciu usługi HDInsight Twoje dane oraz notesy Jupyter są przechowywane w magazynie Azure Storage lub Azure Data Lake Storage, więc możesz bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane. Jeśli planujesz natychmiastowe rozpoczęcie pracy z następnym samouczkiem, warto zachować klaster.

Otwórz klaster w witrynie Azure Portal, a następnie wybierz pozycję **Usuń**.

![Usuwanie klastra usługi HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usunięcie grupy zasobów powoduje usunięcie zarówno klastra Spark w usłudze HDInsight, jak i domyślnego konta magazynu.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób Utwórz ramkę danych z pliku csv oraz do uruchamiania interakcyjnych zapytań Spark SQL klastra Apache Spark w usłudze Azure HDInsight. Przejdź do następnego artykułu, aby dowiedzieć się, w jaki sposób można ściągnąć dane zarejestrowane na platformie Apache Spark do narzędzia analizy biznesowej, takiego jak usługa Power BI.

> [!div class="nextstepaction"]
> [Analyze data using BI tools (Analizowanie danych przy użyciu narzędzi do analizy biznesowej)](apache-spark-use-bi-tools.md)