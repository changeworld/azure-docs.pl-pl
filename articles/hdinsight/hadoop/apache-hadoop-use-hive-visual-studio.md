---
title: Gałąź rejestru przy użyciu narzędzi Data Lake (Hadoop) dla programu Visual Studio - Azure HDInsight | Dokumentacja firmy Microsoft
description: Informacje o sposobie korzystania z narzędzi Data Lake dla programu Visual Studio do uruchamiania zapytań Apache Hive z platformą Apache Hadoop w usłudze Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 2b3e672a-1195-4fa5-afb7-b7b73937bfbe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 862a2aae2e9d417ccf9daf336177b23842dd3db7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="run-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Uruchamianie zapytań Hive przy użyciu narzędzi Data Lake tools dla programu Visual Studio

Informacje o sposobie korzystania z narzędzi Data Lake dla programu Visual Studio do zapytania Apache Hive. Narzędzia Data Lake umożliwiają łatwe tworzenie, przesyłania i monitorowania zapytań programu Hive do platformy Hadoop w usłudze Azure HDInsight.

## <a id="prereq"></a>Wymagania wstępne

* Klaster usługi Azure HDInsight (Hadoop w usłudze HDInsight)

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Visual Studio (jedna z następujących wersji):

    * Visual Studio 2013 Community/Professional/Premium/Ultimate z aktualizacją 4

    * Visual Studio 2015 (dowolna wersja)

    * Visual Studio 2017 (dowolna wersja)

* Narzędzia HDInsight tools for Visual Studio lub usługi Azure Data Lake tools dla programu Visual Studio. Zobacz [rozpocząć korzystanie z narzędzi Visual Studio Hadoop dla usługi HDInsight](apache-hadoop-visual-studio-tools-get-started.md) informacji o instalowaniu i konfigurowaniu narzędzi.

## <a id="run"></a> Uruchamianie zapytań Hive przy użyciu programu Visual Studio

1. Otwórz **programu Visual Studio** i wybierz **nowy** > **projektu** > **usługi Azure Data Lake** > **HIVE** > **aplikacji Hive**. Podaj nazwę dla tego projektu.

2. Otwórz **Script.hql** pliku, który jest tworzony z tego projektu i wklej poniższe instrukcje HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```

    Te instrukcje, wykonaj następujące czynności:

   * `DROP TABLE`: Jeśli tabela istnieje, ta instrukcja usunięcia go.

   * `CREATE EXTERNAL TABLE`: Tworzy nową tabelę "zewnętrzne" w gałęzi. Tabele zewnętrzne przechowywać tylko definicji tabeli w gałęzi (danych pozostaje w oryginalnej lokalizacji).

     > [!NOTE]
     > Jeśli oczekujesz zaktualizowania za pomocą zewnętrznego źródła danych, należy użyć tabel zewnętrznych. Na przykład zadania MapReduce lub usługi Azure.
     >
     > Usunięcie tabeli zewnętrznej jest **nie** Usuń dane, definicję tabeli.

   * `ROW FORMAT`: Nakazuje Hive sposób formatowania danych. W takim przypadku pól w każdym dzienniku są oddzielone spacją.

   * `STORED AS TEXTFILE LOCATION`: Nakazuje Hive, czy dane są przechowywane w katalogu przykładzie/danych oraz że są przechowywane jako tekst.

   * `SELECT`: Wybierz liczbę wszystkich wierszy gdzie kolumna `t4` zawiera wartość `[ERROR]`. Ta instrukcja zwraca wartość `3` ponieważ istnieją trzy wiersze, które zawierają tę wartość.

   * `INPUT__FILE__NAME LIKE '%.log'` -Informuje Hive czy firma Microsoft powinno zwrócić dane tylko z plików w. dziennika. Klauzulę ogranicza wyszukiwanie do pliku sample.log, który zawiera dane.

3. Na pasku narzędzi wybierz **klastra usługi HDInsight** , który ma być używany dla tego zapytania. Wybierz **przesyłania** do uruchamiania instrukcje jako zadania Hive.

   ![Przedstawia paska](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. **Podsumowanie zadania Hive** pojawia się i wyświetla informacje o wykonywanym zadaniem. Użyj **Odśwież** łącze, aby odświeżyć informacje o zadaniu, dopóki **stan zadania** zmienia się na **Ukończono**.

   ![Podsumowanie zadania wyświetlania zadanie zostało ukończone](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Użyj **dane wyjściowe zadania** łącze, aby wyświetlić dane wyjściowe tego zadania. Wyświetla `[ERROR] 3`, czyli wartość zwrócona przez tę kwerendę.

6. Można również uruchomić zapytań programu Hive, bez tworzenia projektu. Przy użyciu **Eksploratora serwera**, rozwiń węzeł **Azure** > **HDInsight**, kliknij prawym przyciskiem myszy serwera usługi HDInsight, a następnie wybierz **Napisz zapytanie Hive**.

7. W **temp.hql** dokumentu, która jest wyświetlana, Dodaj następujące instrukcje HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    Te instrukcje, wykonaj następujące czynności:

   * `CREATE TABLE IF NOT EXISTS`: Tworzy tabelę, jeśli jeszcze nie istnieje. Ponieważ `EXTERNAL` — słowo kluczowe nie jest używany, ta instrukcja tworzy wewnętrznej tabeli. Wewnętrzny tabele są przechowywane w magazynie danych programu Hive i są zarządzane przez Hive.

     > [!NOTE]
     > W odróżnieniu od `EXTERNAL` tabel, również porzucenie wewnętrznej tabeli powoduje usunięcie danych.

   * `STORED AS ORC`: Przechowuje dane w formacie (ORC) kolumnowym zoptymalizowane wiersza. ORC jest wysoce zoptymalizowane i wydajne formatu do przechowywania danych gałęzi.

   * `INSERT OVERWRITE ... SELECT`: Wybiera wiersze z `log4jLogs` tabeli, która zawiera `[ERROR]`, następnie wstawia dane do `errorLogs` tabeli.

8. Na pasku narzędzi wybierz **przesyłania** uruchomić zadanie. Użyj **stan zadania** ustalenie, czy zadanie zostało zakończone pomyślnie.

9. Aby sprawdzić, czy zadania utworzone tabeli, należy użyć **Eksploratora serwera** i rozwiń **Azure** > **HDInsight** > z klastrem usługi HDInsight > **bazy danych programu Hive** > **domyślne**. **ErrorLogs** tabeli i **log4jLogs** tabeli są wymienione.

## <a id="nextsteps"></a>Następne kroki

Jak widać, narzędzia HDInsight tools for Visual Studio umożliwiają łatwe do pracy z zapytań programu Hive w usłudze HDInsight.

Aby uzyskać ogólne informacje na temat programu Hive w usłudze HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje o innych metodach pracy z platformą Hadoop w usłudze HDInsight:

* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)

* [Używanie MapReduce z usługą Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)

Aby uzyskać więcej informacji na temat narzędzia HDInsight tools for Visual Studio:

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
