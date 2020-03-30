---
title: Apache Hive & narzędzia usługi Data Lake dla programu Visual Studio — Azure HDInsight
description: Dowiedz się, jak używać narzędzi usługi Data Lake dla programu Visual Studio do uruchamiania zapytań gałęzi Apache za pomocą apache Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 27ab13481525819eb1435f4c9ac256a21acd21fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687801"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Uruchamianie zapytań Apache Hive przy użyciu wtyczki Data Lake Tools for Visual Studio

Dowiedz się, jak używać narzędzi usługi Data Lake dla programu Visual Studio do wykonywania zapytań o gałąź apache. Narzędzia usługi Data Lake umożliwiają łatwe tworzenie, przesyłanie i monitorowanie zapytań hive do apache Hadoop na platformie Azure HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w programie HDInsight. Aby uzyskać informacje dotyczące tworzenia tego elementu, zobacz [Tworzenie klastra Apache Hadoop w usłudze Azure HDInsight przy użyciu szablonu Usługi Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/). Kroki opisane w tym artykule używają programu Visual Studio 2019.

* Narzędzia HDInsight dla programu Visual Studio lub narzędzia usługi Azure Data Lake dla programu Visual Studio. Aby uzyskać informacje dotyczące instalowania i konfigurowania narzędzi, zobacz [Instalowanie narzędzi usługi Data Lake Tools dla programu Visual Studio](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Uruchamianie zapytań gałęzi Apache przy użyciu programu Visual Studio

Masz dwie opcje umożliwiające utworzenie i uruchomienie zapytań Hive:

* Tworzenie zapytań ad hoc.
* Tworzenie aplikacji hive.

### <a name="create-an-ad-hoc-hive-query"></a>Tworzenie kwerendy doraźnej gałęzi

Zapytania ad hoc mogą być wykonywane w trybie **usługi Batch** lub **Interactive.**

1. Uruchom **program Visual Studio** i wybierz pozycję Kontynuuj bez **kodu**.

2. Z **Eksploratora serwera**, prawym przyciskiem myszy **Azure**, wybierz **połącz z subskrypcją Microsoft Azure...** i zakończyć proces logowania.

3. Rozwiń pozycję **HDInsight**, kliknij prawym przyciskiem myszy klaster, w którym chcesz uruchomić **kwerendę,** a następnie wybierz pozycję Napisz kwerendę gałęzi .

4. Wprowadź następującą kwerendę gałęzi:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Wybierz pozycję **Wykonaj**. Domyślnie tryb wykonywania ma wartość **Interaktywna**.

    ![Wykonywanie interaktywnej kwerendy gałęzi, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Aby uruchomić tę samą kwerendę w trybie **wsadowym,** przełącz listę rozwijaną z **interaktywnej** na **osobową .** Przycisk wykonywania zmienia się z **Wykonaj** na **Prześlij**.

    ![Prześlij kwerendę gałęzi wsadowych, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Edytor Hive obsługuje funkcję IntelliSense. Narzędzia Data Lake Tools for Visual Studio obsługują ładowanie zdalnych metadanych podczas edycji skryptu Hive. Na przykład, jeśli `SELECT * FROM`wpiszesz , IntelliSense wyświetla listę wszystkich sugerowanych nazw tabel. Po określeniu nazwy tabeli funkcja IntelliSense wyświetla nazwy kolumn. Narzędzia obsługują większość instrukcji DML programu Hive, podzapytań i wbudowanych sterowników UDF. Funkcja IntelliSense zasugeruje tylko metadane klastra zaznaczonego na pasku narzędzi usługi HDInsight.

7. Na pasku narzędzi kwerendy (obszar poniżej karty kwerendy i nad tekstem kwerendy) wybierz pozycję **Prześlij**lub zaznacz strzałkę rozwijaną obok **pozycji Prześlij** i wybierz pozycję **Zaawansowane** z listy rozwijanej. Jeśli wybierzesz tę drugą opcję,

8. Jeśli w oknie dialogowym **Prześlij skryptuj skonfigurowano** opcję Zaawansowane przesyłanie, skonfiguruj **nazwę zadania,** **argumenty,** **dodatkowe konfiguracje**i **katalog stanu.** Następnie wybierz pozycję **Prześlij**.

    ![Okno dialogowe Prześlij skrypt, zapytanie gałęzi programu HDInsight Hadoop](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Tworzenie aplikacji Hive

Aby uruchomić kwerendę hive, tworząc aplikację hive, wykonaj następujące kroki:

1. Otwórz **program Visual Studio**.

2. W oknie **Start** wybierz pozycję **Utwórz nowy projekt**.

3. W oknie **Utwórz nowy projekt** w polu **Wyszukaj szablony** wprowadź pozycję *Gałąź*. Następnie wybierz pozycję **Aplikacja gałęzi** i wybierz pozycję **Dalej**.

4. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu**, wybierz lub utwórz **lokalizację** dla nowego projektu, a następnie wybierz pozycję **Utwórz**.

5. Otwórz plik **Script.hql** utworzony za pomocą tego projektu i wklej następujące instrukcje HiveQL:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Te instrukcje wykonują następujące akcje:

    * `DROP TABLE`: Usuwa tabelę, jeśli istnieje.

    * `CREATE EXTERNAL TABLE`: Tworzy nową tabelę "zewnętrzną" w uli. Tabele zewnętrzne przechowują tylko definicję tabeli w obszarze Gałęzi. (Dane pozostają w oryginalnej lokalizacji).

        > [!NOTE]  
        > Tabele zewnętrzne powinny być używane, gdy oczekujesz, że dane źródłowe mają być aktualizowane przez źródło zewnętrzne, takie jak zadanie MapReduce lub usługa platformy Azure.
        >
        > Upuszczenie tabeli zewnętrznej **nie** powoduje usunięcia danych, a jedynie definicji tabeli.

    * `ROW FORMAT`: Informuje hive, jak dane są sformatowane. W takim przypadku pola w każdym dzienniku są oddzielone spacją.

    * `STORED AS TEXTFILE LOCATION`: Informuje Hive, że dane są przechowywane w *katalogu przykład/dane* i że są przechowywane jako tekst.

    * `SELECT`: Wybiera liczbę wszystkich wierszy, `t4` w `[ERROR]`których kolumna zawiera wartość . Ta instrukcja zwraca `3`wartość , ponieważ trzy wiersze zawierają tę wartość.

    * `INPUT__FILE__NAME LIKE '%.log'`: Nakazuje gałęzi zwracać tylko dane z plików kończących się na dzienniku.: Tells Hive to only return data from files ending in .log. Ta klauzula ogranicza wyszukiwanie do pliku *sample.log,* który zawiera dane.

6. Na pasku narzędzi pliku kwerendy (który ma podobny wygląd do paska narzędzi zapytania ad hoc) wybierz klaster HDInsight, którego chcesz użyć w tej kwerendzie. Następnie zmień **interaktywne** na **partia** (jeśli to konieczne) i wybierz **przycisk Prześlij,** aby uruchomić instrukcje jako zadanie gałęzi.

   **Zostanie wyświetlone podsumowanie zadania gałęzi** i wyświetlane są informacje o uruchomionym zadaniu. Użyj łącza **Odśwież,** aby odświeżyć informacje o zadaniu, dopóki **stan zadania** nie zmieni się na **Zakończone**.

   ![Ukończone podsumowanie zadania gałęzi, aplikacja Gałąź, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Wybierz **wyjście zadania,** aby wyświetlić dane wyjściowe tego zadania. Wyświetla `[ERROR] 3`, który jest wartością zwracaną przez tę kwerendę.

### <a name="additional-example"></a>Dodatkowy przykład

Poniższy przykład opiera `log4jLogs` się na tabeli utworzonej w poprzedniej procedurze [Tworzenie aplikacji Hive](#create-a-hive-application).

1. W **Eksploratorze serwera**kliknij prawym przyciskiem myszy klaster i wybierz polecenie **Napisz kwerendę gałęzi**.

2. Wprowadź następującą kwerendę gałęzi:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Te instrukcje wykonują następujące akcje:

    * `CREATE TABLE IF NOT EXISTS`: Tworzy tabelę, jeśli jeszcze nie istnieje. Ponieważ `EXTERNAL` słowo kluczowe nie jest używane, ta instrukcja tworzy tabelę wewnętrzną. Wewnętrzne tabele są przechowywane w magazynie danych hive i są zarządzane przez hive.

        > [!NOTE]  
        > W `EXTERNAL` przeciwieństwie do tabel, upuszczanie tabeli wewnętrznej również usuwa dane źródłowe.

    * `STORED AS ORC`: Przechowuje dane w zoptymalizowanym formacie *kolumnowym wiersza* (ORC). ORC to wysoce zoptymalizowany i wydajny format do przechowywania danych hive.

    * `INSERT OVERWRITE ... SELECT`: Wybiera wiersze `log4jLogs` z tabeli `[ERROR]`zawierającej , a `errorLogs` następnie wstawia dane do tabeli.

3. W razie potrzeby zmień **interaktywną** na **aktywną,** a następnie wybierz pozycję **Prześlij**.

4. Aby sprawdzić, czy zadanie utworzyło tabelę, przejdź do **Eksploratora serwera** i rozwiń pozycję **Azure** > **HDInsight**. Rozwiń klaster HDInsight, a następnie rozwiń**domyślne** **bazy danych hive** > . Tabela **errorLogs** i **log4jLogs** tabela są wymienione.

## <a name="next-steps"></a>Następne kroki

Jak widać, narzędzia HDInsight dla programu Visual Studio zapewniają łatwy sposób pracy z zapytaniami hive w programie HDInsight.

* Aby uzyskać ogólne informacje na temat gałęzi w programie HDInsight, zobacz [Co to jest Gałąź apache i hiveQl w usłudze Azure HDInsight?](hdinsight-use-hive.md)

* Aby uzyskać informacje o innych sposobach pracy z hadoop na HDInsight, zobacz [Korzystanie z MapReduce w Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md)

* Aby uzyskać więcej informacji na temat narzędzi HDInsight dla programu Visual Studio, zobacz Łączenie się z usługą[Azure HDInsight za pomocą narzędzi usługi Data Lake Tools dla programu Visual Studio i uruchamianie kwerend gałęzi apache](apache-hadoop-visual-studio-tools-get-started.md)
