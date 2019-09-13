---
title: Apache Hive z narzędziami Data Lake Tools for Visual Studio — Azure HDInsight
description: Dowiedz się, jak za pomocą narzędzi Data Lake Tools for Visual Studio uruchamiać zapytania Apache Hive za pomocą Apache Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 129f200bc9f61d70f4403b1154978d57e09fee26
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917471"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Uruchamianie zapytań Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio

Dowiedz się, jak Apache Hive wykonywać zapytania za pomocą narzędzi Data Lake Tools for Visual Studio. Narzędzia Data Lake umożliwiają łatwe tworzenie, przesyłanie i monitorowanie zapytań Hive w celu Apache Hadoop w usłudze Azure HDInsight.

## <a id="prereq"></a>Wymagania wstępne

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (jedna z następujących wersji):

    * Visual Studio 2015, 2017 (dowolna wersja)
    * Visual Studio 2013 Community/Professional/Premium/Ultimate z aktualizacją Update 4

* Narzędzia HDInsight Tools for Visual Studio lub Azure Data Lake Tools for Visual Studio. Aby uzyskać informacje na temat instalowania i konfigurowania narzędzi, zobacz Wprowadzenie do [narzędzia Visual Studio Hadoop Tools for HDInsight](apache-hadoop-visual-studio-tools-get-started.md) .

## <a id="run"></a>Uruchamianie zapytań Apache Hive przy użyciu programu Visual Studio

Masz dwie opcje umożliwiające utworzenie i uruchomienie zapytań Hive:

* Tworzenie zapytań ad hoc
* Tworzenie aplikacji Hive

### <a name="ad-hoc"></a>Ad hoc

Zapytania ad hoc mogą być wykonywane w trybie **wsadowym** lub **interaktywnym** .

1. Otwórz **program Visual Studio**.

2. W obszarze **Eksplorator serwera**przejdź do **usługi Azure** > **HDInsight**.

3. Rozwiń węzeł **HDInsight**i kliknij prawym przyciskiem myszy klaster, w którym chcesz uruchomić zapytanie, a następnie wybierz polecenie **Napisz zapytanie Hive**.

4. Wprowadź następujące zapytanie programu Hive:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Wybierz pozycję **Wykonaj**. Należy pamiętać, że tryb wykonywania jest domyślnie **interaktywny**.

    ![Zrzut ekranu przedstawiający wykonywanie zapytań interaktywnych Hive](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Aby uruchomić to samo zapytanie w trybie **wsadowym** , przełącz listę rozwijaną **interaktywnie** do **Batch**. Należy zauważyć, że przycisk wykonywania zmienia się z **Execute** na **Submit**.

    ![Zrzut ekranu przedstawiający przesyłanie zapytania Hive](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Edytor Hive obsługuje funkcję IntelliSense. Narzędzia Data Lake Tools for Visual Studio obsługują ładowanie zdalnych metadanych podczas edycji skryptu Hive. Na przykład, jeśli wpiszesz `SELECT * FROM`, IntelliSense wyświetla wszystkie sugerowane nazwy tabel. Po określeniu nazwy tabeli funkcja IntelliSense wyświetla nazwy kolumn. Narzędzia obsługują większość instrukcji DML programu Hive, podzapytań i wbudowanych sterowników UDF. Funkcja IntelliSense zasugeruje tylko metadane klastra zaznaczonego na pasku narzędzi usługi HDInsight.

    ![Zrzut ekranu przykładu 1 funkcji IntelliSense narzędzi HDInsight Visual Studio Tools](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Zrzut ekranu przykładu 2 funkcji IntelliSense narzędzi HDInsight Visual Studio Tools](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. Wybierz pozycję **Prześlij** lub **Prześlij (zaawansowane)** .

   W przypadku wybrania zaawansowanych opcji przesyłania należy skonfigurować dla skryptu wartości takie jak **Nazwa zadania**, **Argumenty**, **Dodatkowe konfiguracje** i **Katalog stanu**:

    ![Zrzut ekranu zapytania programu Hive w usłudze HDInsight Hadoop](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png "Przesłanie zapytań")

### <a name="hive-application"></a>Aplikacja Hive

1. Otwórz **program Visual Studio**.

2. Na pasku menu Przejdź do menu **plik** > **Nowy** > **projekt**.

3. W oknie **Nowy projekt** przejdź do **szablonów** > **aplikacji Hive** **Azure Data Lake** > **Hive (HDInsight)**  > . 

4. Podaj nazwę dla tego projektu, a następnie wybierz przycisk **OK**.

5. Otwórz plik **Script. HQL** , który jest tworzony przy użyciu tego projektu, i wklej go w następujących instrukcjach HiveQL:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Te instrukcje wykonują następujące czynności:

   * `DROP TABLE`: Jeśli tabela istnieje, ta instrukcja usunie ją.

   * `CREATE EXTERNAL TABLE`: Tworzy nową tabelę "External" w gałęzi. Tabele zewnętrzne przechowują definicję tabeli w gałęzi Hive (dane są pozostawione w oryginalnej lokalizacji).

     > [!NOTE]  
     > Tabele zewnętrzne powinny być używane, gdy oczekuje się, że dane podstawowe mają być aktualizowane przez zewnętrzne źródło. Na przykład zadanie MapReduce lub usługa platformy Azure.
     >
     > Porzucenie tabeli **zewnętrznej nie powoduje usunięcia danych** , tylko definicji tabeli.

   * `ROW FORMAT`: Informuje Hive, jak dane są sformatowane. W takim przypadku pola w każdym dzienniku są oddzielone spacją.

   * `STORED AS TEXTFILE LOCATION`: Instruuje gałąź, że dane są przechowywane w przykładowym katalogu danych i są przechowywane jako tekst.

   * `SELECT`: Wybierz liczbę wszystkich wierszy, w których kolumna `t4` zawiera wartość. `[ERROR]` Ta instrukcja zwraca wartość `3` , ponieważ istnieją trzy wiersze, które zawierają tę wartość.

   * `INPUT__FILE__NAME LIKE '%.log'`-Informuje gałąź, że będziemy zwracać tylko dane z plików kończących się na. log. Ta klauzula ogranicza wyszukiwanie do pliku Sample. log, który zawiera dane.

6. Na pasku narzędzi wybierz klaster usługi **HDInsight** , który ma być używany dla tego zapytania. Wybierz pozycję **Prześlij** , aby uruchomić instrukcje jako zadanie Hive.

   ![Pasek przesyłania](./media/apache-hadoop-use-hive-visual-studio/hdinsight-toolbar-submit.png)

7. Zostanie wyświetlone **Podsumowanie zadania programu Hive** zawierające informacje o uruchomionym zadaniu. Użyj linku **Odśwież** , aby odświeżyć informacje o zadaniu do momentu, aż **stan zadania** zmieni się na **zakończone**.

   ![Podsumowanie zadania zawierające ukończone zadanie](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

8. Użyj linku **dane wyjściowe zadania** , aby wyświetlić dane wyjściowe tego zadania. Zostanie wyświetlona `[ERROR] 3`, która jest wartością zwracaną przez to zapytanie.

### <a name="additional-example"></a>Dodatkowy przykład

Ten przykład opiera się `log4jLogs` na tabeli utworzonej w poprzednim kroku.

1. W **Eksplorator serwera**kliknij prawym przyciskiem myszy klaster i wybierz polecenie **Napisz zapytanie Hive**.

2. Wprowadź następujące zapytanie programu Hive:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Te instrukcje wykonują następujące czynności:

    * `CREATE TABLE IF NOT EXISTS`: Tworzy tabelę, jeśli jeszcze nie istnieje. `EXTERNAL` Ponieważ słowo kluczowe nie jest używane, ta instrukcja tworzy tabelę wewnętrzną. Tabele wewnętrzne są przechowywane w magazynie danych programu Hive i są zarządzane przez program Hive.
    
    > [!NOTE]  
    > W przeciwieństwie do `EXTERNAL` tabel, upuszczenie wewnętrznej tabeli spowoduje również usunięcie danych źródłowych.

    * `STORED AS ORC`: Dane są przechowywane w formacie zoptymalizowanego wiersza kolumnowy (ORC). ORC to wysoce zoptymalizowany i wydajny format służący do przechowywania danych programu Hive.
    
    * `INSERT OVERWRITE ... SELECT`: Wybiera wiersze z `log4jLogs` tabeli, która zawiera `[ERROR]`, a następnie `errorLogs` wstawia dane do tabeli.

3. Wykonaj zapytanie w trybie **wsadowym** .

4. Aby sprawdzić, czy zadanie utworzyło tabelę, użyj **Eksplorator serwera** i rozwiń węzeł Azure**HDInsight** > klastrze **usługi** > HDInsight > **baz danych** > programu Hive jako**domyślnych**. Zostanie wyświetlona tabela **errorLogs** i tabela **log4jLogs** .

## <a id="nextsteps"></a>Następne kroki

Jak widać, narzędzia HDInsight Tools for Visual Studio zapewniają łatwy sposób pracy z zapytaniami programu Hive w usłudze HDInsight.

Aby uzyskać ogólne informacje na temat usługi Hive w usłudze HDInsight:

* [Używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje o innych sposobach pracy z usługą Hadoop w usłudze HDInsight:

* [Korzystanie z usługi Apache świni z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-pig.md)

* [Używanie MapReduce z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)

Aby uzyskać więcej informacji na temat narzędzi HDInsight Tools for Visual Studio:

* [Wprowadzenie do narzędzi HDInsight Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)