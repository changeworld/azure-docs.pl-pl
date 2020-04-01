---
title: 'Samouczek: Ładowanie danych & uruchamianie zapytań za pomocą platformy Apache Spark — usługa Azure HDInsight'
description: Samouczek — dowiedz się, jak załadować dane i uruchomić interaktywne zapytania dotyczące klastrów platformy Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: 5eb6788a558e4429296731f1693edd18bf92f98f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77198892"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Samouczek: ładowanie danych i uruchamianie zapytań w klastrze platformy Apache Spark w usłudze Azure HDInsight

W tym samouczku dowiesz się, jak utworzyć ramkę danych z pliku csv i jak uruchamiać interaktywne zapytania programu Spark SQL względem klastra [Platformy Spark apache](https://spark.apache.org/) w usłudze Azure HDInsight. Na platformie Spark ramka danych jest rozproszoną kolekcją danych zorganizowanych w nazwanych kolumnach. Jest równoważna tabeli w relacyjnej bazie danych lub ramce danych w języku R/Python.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie ramki danych z pliku csv
> * Uruchamianie zapytań na ramce danych

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Spark w usłudze HDInsight. Zobacz [Tworzenie klastra Platformy Spark apache](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

Jupyter Notebook to interakcyjne środowisko notesu, które obsługuje różne języki programowania. Notes pozwala na interakcję z danymi, łączenie kodu z tekstem markdown i wykonywanie prostych wizualizacji.

1. Edytuj adres `https://SPARKCLUSTER.azurehdinsight.net/jupyter` URL, zastępując `SPARKCLUSTER` nazwą klastra platformy Spark. Następnie wprowadź edytowany adres URL w przeglądarce internetowej. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla klastra.

2. Na stronie internetowej Jupyter wybierz **nowy** > **pyspark,** aby utworzyć notes.

   ![Tworzenie notesu Jupyter w celu uruchomienia interaktywnej kwerendy SQL platformy Spark](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Tworzenie notesu Jupyter w celu uruchomienia interaktywnej kwerendy SQL platformy Spark")

   Nowy notes jest tworzony i otwierany`Untitled.ipynb`pod nazwą Untitled( ).

    > [!NOTE]  
    > Dzięki temu, że tworzenie notesu odbywa się przy użyciu jądra PySpark, po uruchomieniu pierwszej komórki kodu sesja `spark` jest tworzona automatycznie. Nie jest konieczne jawne tworzenie sesji.

## <a name="create-a-dataframe-from-a-csv-file"></a>Tworzenie ramki danych z pliku csv

Aplikacje mogą tworzyć pulpity danych bezpośrednio z plików lub folderów w magazynie zdalnym, takich jak Usługa Azure Storage lub Usługi Azure Data Lake Storage; z tabeli Hive; lub z innych źródeł danych obsługiwanych przez platformę Spark, takich jak Cosmos DB, Azure SQL DB, DW i tak dalej. Poniższy zrzut ekranu przedstawia migawkę pliku HVAC.csv używanego w tym samouczku. Plik csv jest zainstalowany na wszystkich klastrach HDInsight Spark. Dane dotyczą zmian temperatury w niektórych budynkach.

![Migawka danych dla interaktywnej kwerendy SQL platformy Spark](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Migawka danych dla interaktywnej kwerendy SQL platformy Spark")

1. Wklej następujący kod w pustej komórce notesu Jupyter, a następnie naciśnij **klawisze SHIFT + ENTER,** aby uruchomić kod. Kod importuje typy wymagane w tym scenariuszu:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Podczas uruchamiania interakcyjnego zapytania w programie Jupyter w oknie przeglądarki internetowej lub w tytule karty wyświetlany jest stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne jest także pełne kółko obok tekstu **PySpark** w prawym górnym rogu. Po zakończeniu zadania zmienia się ono w pusty okrąg.

    ![Stan interaktywnej kwerendy SQL platformy Spark](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Stan interaktywnej kwerendy SQL platformy Spark")

1. Zwróć uwagę na zwrócony identyfikator sesji. Na zdjęciu powyżej identyfikator sesji wynosi 0. W razie potrzeby można pobrać szczegóły sesji, `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements` przechodząc do miejsca, w którym CLUSTERNAME jest nazwą klastra Platformy Spark, a identyfikator jest numerem identyfikatora sesji.

1. Uruchom następujący kod, aby utworzyć ramkę danych i tabelę tymczasową (**hvac**).

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

     ![Dane wyjściowe tabeli interakcyjnego wyniku kwerendy platformy Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Dane wyjściowe tabeli interakcyjnego wyniku kwerendy platformy Spark")

2. Wyniki można również przeglądać w postaci innych wizualizacji. Aby wyświetlić wykres warstwowy dla tych samych danych wyjściowych, wybierz pozycję **Area** (Obszar), a następnie ustaw inne wartości, jak pokazano poniżej.

    ![Wykres warstwowy interaktywnego wyniku zapytania Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Wykres warstwowy interaktywnego wyniku zapytania Spark")

3. Na pasku menu notesu przejdź do pozycji **Zapisywanie plików** > **i Punkt kontrolny**.

4. Jeśli zamierzasz teraz otworzyć [następny samouczek](apache-spark-use-bi-tools.md), pozostaw notes otwarty. Jeśli nie, zamknij notes, aby zwolnić zasoby klastra: z paska menu notesu przejdź do pozycji Zamknięcie **pliku** >  **i wstrzymanie**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Dzięki funkcji HDInsight twoje dane i notesy jupyter są przechowywane w usłudze Azure Storage lub usłudze Azure Data Lake Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Naliczana jest również opłata za klaster HDInsight, nawet jeśli nie jest używana. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ma ekonomiczny sens usuwanie klastrów, gdy nie są używane. Jeśli planujesz natychmiastowe rozpoczęcie pracy z następnym samouczkiem, warto zachować klaster.

Otwórz klaster w witrynie Azure Portal, a następnie wybierz pozycję **Usuń**.

![Usuwanie klastra usługi HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usunięcie grupy zasobów powoduje usunięcie zarówno klastra Spark w usłudze HDInsight, jak i domyślnego konta magazynu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak utworzyć ramkę danych z pliku csv i jak uruchamiać interaktywne zapytania programu Spark SQL względem klastra Platformy Spark apache w usłudze Azure HDInsight. Przejdź do następnego artykułu, aby dowiedzieć się, w jaki sposób można ściągnąć dane zarejestrowane na platformie Apache Spark do narzędzia analizy biznesowej, takiego jak usługa Power BI.

> [!div class="nextstepaction"]
> [Analizowanie danych przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
