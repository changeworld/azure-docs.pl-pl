---
title: Apache Hive za pomocą narzędzi Data Lake (Apache Hadoop) dla programu Visual Studio — Azure HDInsight
description: Dowiedz się, jak używać Data Lake tools for Visual Studio do uruchomienia zapytania usługi Apache Hive przy użyciu technologii Apache Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 7480dafe435e555bfba81ebd9242bb5724c0bf3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65861595"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Uruchamianie zapytania usługi Apache Hive przy użyciu narzędzi Data Lake tools for Visual Studio

Dowiedz się, jak za pomocą narzędzi Data Lake tools dla programu Visual Studio zapytań technologii Hive. Narzędzia Data Lake pozwala na łatwe tworzenie, przesyłania i monitorowania zapytania Hive w celu Apache Hadoop w usłudze Azure HDInsight.

## <a id="prereq"></a>Wymagania wstępne

* Klaster platformy Apache Hadoop w HDInsight. Zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (jedna z następujących wersji):

    * Visual Studio 2015, 2017 r. (w każdej wersji)
    * Visual Studio 2013 Community/Professional/Premium/Ultimate z aktualizacją Update 4

* HDInsight tools for Visual Studio lub Azure Data Lake tools for Visual Studio. Zobacz [rozpoczęcie korzystania z narzędzi Visual Studio Hadoop dla HDInsight](apache-hadoop-visual-studio-tools-get-started.md) instrukcje dotyczące instalowania i konfigurowania narzędzia.

## <a id="run"></a> Uruchamianie zapytania usługi Apache Hive przy użyciu programu Visual Studio

Masz dwie opcje umożliwiające utworzenie i uruchomienie zapytań Hive:

* Tworzenie zapytań ad hoc
* Tworzenie aplikacji Hive

### <a name="ad-hoc"></a>Ad hoc

Zapytania ad hoc, mogą być wykonywane w jednym **partii** lub **Interactive** trybu.

1. Otwórz **programu Visual Studio**.

2. Z **Eksploratora serwera**, przejdź do **Azure** > **HDInsight**.

3. Rozwiń **HDInsight**i kliknij prawym przyciskiem myszy klaster, w którym chcesz uruchomić zapytanie, a następnie wybierz **Napisz zapytanie Hive**.

4. Wprowadź następujące zapytanie programu hive:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Wybierz pozycję **Wykonaj**. Należy zauważyć, że tryb wykonywania jest ustawiana domyślnie do **Interactive**.

    ![Zrzut ekranu przedstawiający wykonywanie zapytań interaktywnych Hive](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Do uruchomienia tego samego zapytania **partii** tryb, Przełącz listę rozwijaną listę z **Interactive** do **partii**. Należy zauważyć, że przycisk wykonanie zmiany z **Execute** do **przesyłania**.

    ![Zrzut ekranu przedstawiający przesyłanie zapytania Hive](./media/apache-hadoop-use-hive-visual-studio/vs-batch-query.png)

    Edytor Hive obsługuje funkcję IntelliSense. Narzędzia Data Lake Tools for Visual Studio obsługują ładowanie zdalnych metadanych podczas edycji skryptu Hive. Na przykład, jeśli wpiszesz `SELECT * FROM`, funkcja IntelliSense wyświetla wszystkie sugerowane nazwy tabeli. Po określeniu nazwy tabeli funkcja IntelliSense wyświetla nazwy kolumn. Narzędzia obsługują większość instrukcji DML programu Hive, podzapytań i wbudowanych sterowników UDF. Funkcja IntelliSense zasugeruje tylko metadane klastra zaznaczonego na pasku narzędzi usługi HDInsight.

    ![Zrzut ekranu przykładu 1 funkcji IntelliSense narzędzi HDInsight Visual Studio Tools](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Zrzut ekranu przykładu 2 funkcji IntelliSense narzędzi HDInsight Visual Studio Tools](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. Wybierz pozycję **Prześlij** lub **Prześlij (zaawansowane)** .

   W przypadku wybrania zaawansowanych opcji przesyłania należy skonfigurować dla skryptu wartości takie jak **Nazwa zadania**, **Argumenty**, **Dodatkowe konfiguracje** i **Katalog stanu**:

    ![Zrzut ekranu zapytania programu Hive w usłudze HDInsight Hadoop](./media/apache-hadoop-use-hive-visual-studio/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Przesłanie zapytań")

### <a name="hive-application"></a>Aplikacja hive

1. Otwórz **programu Visual Studio**.

2. Na pasku menu, przejdź do **pliku** > **New** > **projektu**.

3. Z **nowy projekt** okna, przejdź do **szablony** > **usługi Azure Data Lake** > **HIVE (HDInsight)**  >  **Hive aplikacji**. 

4. Podaj nazwę dla tego projektu, a następnie wybierz pozycję **OK**.

5. Otwórz **Script.hql** pliku, który jest tworzony przy użyciu tego projektu i wklej poniższe instrukcje HiveQL:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Te instrukcje, wykonaj następujące czynności:

   * `DROP TABLE`: Jeśli tabela istnieje, ta instrukcja powoduje usunięcie go.

   * `CREATE EXTERNAL TABLE`: Tworzy nową tabelę "external" w gałęzi. Tabele zewnętrzne przechowywać wyłącznie w definicji tabeli programu Hive (danych jest pozostawiany w oryginalnej lokalizacji).

     > [!NOTE]  
     > Jeśli potrzebujesz danych bazowych do zaktualizowania za pomocą zewnętrznego źródła, należy używać tabel zewnętrznych. Na przykład zadanie MapReduce lub usługi platformy Azure.
     >
     > Usunięcie tabeli zewnętrznej jest **nie** usunąć dane w definicji tabeli.

   * `ROW FORMAT`: Informuje, Hive, w jaki sposób dane są sformatowane. W tym przypadku pola w każdym dzienniku są oddzielone spacją.

   * `STORED AS TEXTFILE LOCATION`: Gałąź informuje, że dane są przechowywane w katalogu przykład/dane i czy dane są przechowywane jako tekst.

   * `SELECT`: Wybierz liczbę wszystkich wierszy gdzie kolumna `t4` zawiera wartość `[ERROR]`. Ta instrukcja zwraca wartość `3` ponieważ istnieją trzy wiersze, które zawierają tę wartość.

   * `INPUT__FILE__NAME LIKE '%.log'` — Informuje Hive firma Microsoft z plików kończy się rozszerzeniem tylko powinna zwrócić dane. log. Ta klauzula ogranicza wyszukiwanie do pliku sample.log, który zawiera dane.

6. Na pasku narzędzi wybierz **klastra HDInsight** , którego chcesz użyć dla tego zapytania. Wybierz **przesyłania** do uruchomienia instrukcji jako zadania Hive.

   ![Przedstawia pasek](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

7. **Podsumowanie zadania Hive** pojawia się i wyświetla informacje o uruchomionego zadania. Użyj **Odśwież** łącze, aby odświeżyć informacje o zadaniu, aż do **stan zadania** zmieni się na **Ukończono**.

   ![Podsumowanie zadania wyświetlania ukończone zadanie](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

8. Użyj **dane wyjściowe zadania** link, aby wyświetlić danymi wyjściowymi tego zadania. Wyświetla `[ERROR] 3`, czyli wartość zwrócona przez tę kwerendę.

### <a name="additional-example"></a>Dodatkowe przykład

W tym przykładzie opiera się na `log4jLogs` tabeli utworzonej w poprzednim kroku.

1. Z **Eksploratora serwera**, kliknij prawym przyciskiem myszy klaster i wybierz **Napisz zapytanie Hive**.

2. Wprowadź następujące zapytanie programu hive:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Te instrukcje, wykonaj następujące czynności:

    * `CREATE TABLE IF NOT EXISTS`: Tworzy tabelę, jeśli jeszcze nie istnieje. Ponieważ `EXTERNAL` — słowo kluczowe nie jest używany, ta instrukcja tworzy wewnętrznej tabeli. Tabele wewnętrzne są przechowywane w magazynie danych programu Hive i są zarządzane przez program Hive.
    
    > [!NOTE]  
    > W odróżnieniu od `EXTERNAL` tabel, również porzucenie wewnętrznej tabeli powoduje usunięcie danych bazowych.

    * `STORED AS ORC`: Przechowuje dane w formacie (ORC) kolumnowym zoptymalizowane wiersza. ORC jest wysoce zoptymalizowane i wydajne formatu do przechowywania danych programu Hive.
    
    * `INSERT OVERWRITE ... SELECT`: Wybiera wiersze z `log4jLogs` tabeli, która zawiera `[ERROR]`, następnie wstawia dane do `errorLogs` tabeli.

3. Wykonaj zapytanie w **partii** trybu.

4. Aby sprawdzić, czy zadanie utworzone tabeli, użyj **Eksploratora serwera** i rozwiń **Azure** > **HDInsight** > klaster HDInsight >  **Bazy danych programu hive** > **domyślne**. **Przesłano** tabeli i **log4jLogs** tabeli są wymienione.

## <a id="nextsteps"></a>Następne kroki

Jak widać, narzędzi HDInsight dla programu Visual Studio zapewniają prosty sposób pracy z zapytań programu Hive na HDInsight.

Aby uzyskać ogólne informacje na temat programu Hive w HDInsight:

* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje o innych metodach można pracować z platformą Hadoop w HDInsight:

* [Use Apache Pig z platformą Apache Hadoop w HDInsight](hdinsight-use-pig.md)

* [Korzystanie z technologii MapReduce z platformą Apache Hadoop w HDInsight](hdinsight-use-mapreduce.md)

Aby uzyskać więcej informacji na temat narzędzi HDInsight dla programu Visual Studio:

* [Wprowadzenie do narzędzi HDInsight tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)