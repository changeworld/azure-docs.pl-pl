---
title: Hive za pomocą narzędzi Data Lake (Hadoop) dla programu Visual Studio — Azure HDInsight
description: Dowiedz się, jak używać Data Lake tools for Visual Studio do uruchomienia zapytania usługi Apache Hive przy użyciu technologii Apache Hadoop w usłudze Azure HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: fd57e713a24fb83e42d46b4a1978530b706bf583
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049900"
---
# <a name="run-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Uruchamianie zapytań Hive przy użyciu narzędzi Data Lake tools for Visual Studio

Dowiedz się, jak za pomocą narzędzi Data Lake tools dla programu Visual Studio zapytań technologii Hive. Narzędzia Data Lake pozwala na łatwe tworzenie, przesyłania i monitorowania zapytań programu Hive z usługą Hadoop w usłudze Azure HDInsight.

## <a id="prereq"></a>Wymagania wstępne

* Klaster usługi Azure HDInsight (Hadoop w HDInsight)

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Visual Studio (jedna z następujących wersji):

    * Visual Studio 2013 Community/Professional/Premium/Ultimate z aktualizacją Update 4

    * Program Visual Studio 2015 (w każdej wersji)

    * Program Visual Studio 2017 (w każdej wersji)

* HDInsight tools for Visual Studio lub Azure Data Lake tools for Visual Studio. Zobacz [rozpoczęcie korzystania z narzędzi Visual Studio Hadoop dla HDInsight](apache-hadoop-visual-studio-tools-get-started.md) instrukcje dotyczące instalowania i konfigurowania narzędzia.

## <a id="run"></a> Uruchamianie zapytań Hive przy użyciu programu Visual Studio

1. Otwórz **programu Visual Studio** i wybierz **New** > **projektu** > **usługi Azure Data Lake**  >   **HIVE** > **Hive aplikacji**. Podaj nazwę dla tego projektu.

2. Otwórz **Script.hql** pliku, który jest tworzony przy użyciu tego projektu i wklej poniższe instrukcje HiveQL:

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

   * `ROW FORMAT`: Nakazuje Hive, w jaki sposób dane są sformatowane. W tym przypadku pola w każdym dzienniku są oddzielone spacją.

   * `STORED AS TEXTFILE LOCATION`: Nakazuje gałąź, czy dane są przechowywane w katalogu przykład/dane i że są przechowywane jako tekst.

   * `SELECT`: Wybierz liczbę wszystkich wierszy gdzie kolumna `t4` zawiera wartość `[ERROR]`. Ta instrukcja zwraca wartość `3` ponieważ istnieją trzy wiersze, które zawierają tę wartość.

   * `INPUT__FILE__NAME LIKE '%.log'` — Informuje Hive firma Microsoft z plików kończy się rozszerzeniem tylko powinna zwrócić dane. log. Ta klauzula ogranicza wyszukiwanie do pliku sample.log, który zawiera dane.

3. Na pasku narzędzi wybierz **klastra HDInsight** , którego chcesz użyć dla tego zapytania. Wybierz **przesyłania** do uruchomienia instrukcji jako zadania Hive.

   ![Przedstawia pasek](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. **Podsumowanie zadania Hive** pojawia się i wyświetla informacje o uruchomionego zadania. Użyj **Odśwież** łącze, aby odświeżyć informacje o zadaniu, aż do **stan zadania** zmieni się na **Ukończono**.

   ![Podsumowanie zadania wyświetlania ukończone zadanie](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Użyj **dane wyjściowe zadania** link, aby wyświetlić danymi wyjściowymi tego zadania. Wyświetla `[ERROR] 3`, czyli wartość zwrócona przez tę kwerendę.

6. Można również uruchomić zapytania programu Hive, bez tworzenia projektu. Za pomocą **Eksploratora serwera**, rozwiń węzeł **Azure** > **HDInsight**, kliknij prawym przyciskiem myszy serwer usługi HDInsight, a następnie wybierz **Napisz zapytanie Hive** .

7. W **temp.hql** dokument, który jest wyświetlany, Dodaj poniższe instrukcje HiveQL:

   ```hiveql
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

8. Na pasku narzędzi wybierz **przesyłania** do uruchomienia zadania. Użyj **stan zadania** Aby określić, że pomyślnie Ukończono zadanie.

9. Aby sprawdzić, czy zadanie utworzone tabeli, użyj **Eksploratora serwera** i rozwiń **Azure** > **HDInsight** > klaster HDInsight >  **Bazy danych programu hive** > **domyślne**. **Przesłano** tabeli i **log4jLogs** tabeli są wymienione.

## <a id="nextsteps"></a>Następne kroki

Jak widać, narzędzi HDInsight dla programu Visual Studio zapewniają prosty sposób pracy z zapytań programu Hive na HDInsight.

Aby uzyskać ogólne informacje na temat programu Hive w HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje o innych metodach można pracować z platformą Hadoop w HDInsight:

* [Korzystanie z języka Pig z platformą Hadoop w HDInsight](hdinsight-use-pig.md)

* [Korzystanie z technologii MapReduce z platformą Hadoop w HDInsight](hdinsight-use-mapreduce.md)

Aby uzyskać więcej informacji na temat narzędzi HDInsight dla programu Visual Studio:

* [Wprowadzenie do narzędzi HDInsight tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
